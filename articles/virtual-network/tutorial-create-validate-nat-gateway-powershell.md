---
title: 'Tutorial: Criar e testar um gateway da NAT – Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Este tutorial mostra como criar um gateway da NAT usando o Azure PowerShell e testar o serviço NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: faaf32b08fdf2415b1c60192f917fc2aedc14704
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660981"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Tutorial: Criar um gateway da NAT usando o Azure PowerShell e testar o serviço NAT

Neste tutorial, você criará um gateway da NAT para fornecer conectividade de saída para máquinas virtuais no Azure. Para testar o gateway da NAT, implante uma máquina virtual de origem e de destino. Você testará o gateway da NAT realizando conexões de saída com um endereço IP público. Essas conexões serão provenientes da máquina virtual de origem para a de destino. Este tutorial implanta a origem e o destino em duas redes virtuais diferentes no mesmo grupo de recursos para simplificar.

>[!NOTE] 
>A NAT de Rede Virtual do Azure está disponível como versão prévia pública neste momento e em um conjunto limitado de [regiões](./nat-overview.md#region-availability). Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms) para obter detalhes.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode concluir este tutorial usando o Azure Cloud Shell ou executar os respectivos comandos localmente.  Caso nunca tenha usado o Azure Cloud Shell, [entre agora](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O seguinte exemplo cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2**:


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Criar o gateway da NAT

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Para acessar a Internet, você precisa de um ou mais endereços IP públicos para o gateway da NAT. Use [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) para criar um recurso de endereço IP público chamado **myPublicIPsource** em **myResourceGroupNAT**. O resultado desse comando será armazenado em uma variável chamada **$publicIPsource** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

 Use [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) para criar um recurso de prefixo IP público chamado **myPublicIPprefixsource** em **myResourceGroupNAT**.  O resultado desse comando será armazenado em uma variável chamada **$publicIPPrefixsource** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway da NAT

Esta seção fornece detalhes sobre como criar e configurar os seguintes componentes do serviço NAT usando o recurso de gateway da NAT:
  - Um pool de IPs públicos e o prefixo IP público a serem usados para fluxos de saída convertidos pelo recurso de Gateway da NAT.
  - Altere o tempo limite de ociosidade do padrão de 4 minutos para 10 minutos.

Crie um gateway da NAT global do Azure com [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). O resultado desse comando criará um recurso de gateway chamado **myNATgateway** que usa o endereço IP público **myPublicIPsource** e o prefixo IP público **myPublicIPprefixsource**. O tempo limite de ociosidade está definido como 10 minutos.  O resultado desse comando será armazenado em uma variável chamada **$natGateway** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

Neste momento, o gateway da NAT está funcional e tudo o que está faltando é configurar quais sub-redes de uma rede virtual devem usá-lo.

## <a name="prepare-the-source-for-outbound-traffic"></a>Preparar a origem para o tráfego de saída

Orientaremos você pela instalação de um ambiente de teste completo. Você configurará um teste usando ferramentas de software livre para verificar o gateway da NAT. Começaremos com a origem, que usará o gateway da NAT criado anteriormente.

### <a name="configure-virtual-network-for-source"></a>Configurar a rede virtual para a origem

Crie a rede virtual e associe a sub-rede ao gateway.

Crie uma rede virtual chamada **myVnetsource** com uma sub-rede chamada **mySubnetsource** usando [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) no **myResourceGroupNAT** usando [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). O espaço de endereços IP da rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.  O resultado dos comandos serão armazenados em variáveis chamadas **$subnetsource** e **$vnetsource** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

Todo o tráfego de saída para os destinos da Internet agora usa o serviço NAT.  Não é necessário configurar uma UDR.

Antes de podermos testar o gateway da NAT, precisamos criar uma VM de origem.  Atribuiremos um recurso de endereço IP público como um IP Público em nível de instância para acessar essa VM de fora. Esse endereço é usado somente para acessá-la para o teste.  Demonstraremos como o serviço NAT tem precedência sobre outras opções de saída.

Você também pode criar essa VM sem um IP público e criar outra VM para usá-la como um jumpbox sem um IP público como um exercício.

### <a name="create-public-ip-for-source-vm"></a>Criar IP público para a VM de origem

Criamos um IP público para ser usado para acessar a VM.  Use [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) para criar um recurso de endereço IP público chamado **myPublicIPVM** em **myResourceGroupNAT**.  O resultado desse comando será armazenado em uma variável chamada **$publicIpsourceVM** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Criar um NSG e expor um ponto de extremidade SSH para a VM

Como os endereços IP Públicos Standard são "seguros por padrão", criamos um NSG para permitir o acesso de entrada para SSH. O serviço NAT reconhece a direção do fluxo. Este NSG não será usado para saída depois que o gateway da NAT estiver configurado na mesma sub-rede. Use [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) para criar um recurso NSG chamado **myNSGsource**. Use [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) para criar uma regra NSG para acesso SSH chamada **ssh** em **myResourceGroupNAT**. O resultado desse comando será armazenado na variável chamada **$nsgsource** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>Criar NIC para a VM de origem

Crie um adaptador de rede com [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) chamado **myNicsource**. Esse comando associará o endereço IP Público e o grupo de segurança de rede. O resultado desse comando será armazenado em uma variável chamada **$nicsource** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>Criar uma VM de origem

#### <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Você precisa de um par de chaves SSH para concluir este início rápido. Se você já tiver um par de chaves SSH, você pode ignorar esta etapa.

Use o ssh-keygen para criar um par de chaves SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Para obter mais informações sobre como criar pares de chave SSH, incluindo o uso de PuTTy, consulte [Como usar chaves SSH com o Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Se você criar o par de chaves SSH usando o Cloud Shell, esse par será armazenado em uma imagem de contêiner. Essa [conta de armazenamento é criada automaticamente](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Não exclua a conta de armazenamento ou o compartilhamento de arquivo dentro dela até que você tenha recuperado suas chaves.

#### <a name="create-vm-configuration"></a>Criar a Configuração da VM

Para criar uma VM no PowerShell, você deve criar uma configuração que tem configurações como a imagem para opções de autenticação, tamanho e uso. Em seguida, a configuração é usada para compilar a VM.

Defina as credenciais do SSH, as informações do sistema operacional e o tamanho de VM. Neste exemplo, a chave SSH é armazenada em ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combine as definições de configuração para criar uma VM chamada **myVMsource** com [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) em **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Embora o comando retorne imediatamente, pode levar alguns minutos para a VM ser implantada.

## <a name="prepare-destination-for-outbound-traffic"></a>Preparar destino para o tráfego de saída

Agora criaremos um destino para o tráfego de saída convertido pelo serviço NAT para permitir que você o teste.

### <a name="configure-virtual-network-for-destination"></a>Configurar a rede virtual para o destino

Precisamos criar uma rede virtual na qual a máquina virtual de destino estará.  Esses comandos são as mesmas etapas para a VM de origem. Pequenas alterações foram adicionadas para expor o ponto de extremidade de destino.

Crie uma rede virtual chamada **myVnetdestination** com uma sub-rede chamada **mySubnetdestination** usando [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) no **myResourceGroupNAT** usando [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). O espaço de endereços IP da rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.  O resultado dos comandos serão armazenados em variáveis chamadas **$subnetdestination** e **$vnetdestination** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>Criar IP público para a VM de destino

Criamos um IP público para ser usado para acessar a VM de origem.  Use [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) para criar um recurso de endereço IP público chamado **myPublicIPdestinationVM** em **myResourceGroupNAT**.  O resultado desse comando será armazenado em uma variável chamada **$publicIpdestinationVM** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Criar um NSG e expor um ponto de extremidade SSH e HTTP para a VM

Os endereços IP Públicos Standard são "seguros por padrão". Criamos um NSG para permitir o acesso de entrada para SSH. Use [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) para criar um recurso NSG chamado **myNSGdestination**. Use [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) para criar uma regra NSG para acesso SSH chamada **ssh**.  Use [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) para criar uma regra NSG para acesso HTTP chamada **http**. Ambas as regras serão criadas em **myResourceGroupNAT**. O resultado desse comando será armazenado em uma variável chamada **$nsgdestination** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>Criar NIC para a VM de destino

Crie um adaptador de rede com [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) chamado **myNicdestination**. Esse comando será associado ao endereço IP Público e o grupo de segurança de rede. O resultado desse comando será armazenado em uma variável chamada **$nicdestination** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>Criar uma VM de destino

#### <a name="create-vm-configuration"></a>Criar a Configuração da VM

Para criar uma VM no PowerShell, você deve criar uma configuração que tem configurações como a imagem para opções de autenticação, tamanho e uso. Em seguida, a configuração é usada para compilar a VM.

Defina as credenciais do SSH, as informações do sistema operacional e o tamanho de VM. Neste exemplo, a chave SSH é armazenada em ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combine as definições de configuração para criar uma VM chamada **myVMdestination** com [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) em **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Embora o comando retorne imediatamente, pode levar alguns minutos para a VM ser implantada.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Preparar um servidor Web e testar o conteúdo na VM de destino

Primeiro precisamos descobrir o endereço IP da VM de destino.  Para obter o endereço IP público da VM, use o [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para usá-lo nas etapas seguintes. Indique isso como a máquina virtual de destino.

### <a name="sign-in-to-destination-vm"></a>Entrar na VM de destino

As credenciais do SSH devem ser armazenadas no Cloud Shell da operação anterior.  Abra um [Azure Cloud Shell](https://shell.azure.com) no navegador. Use o endereço IP recuperado na etapa anterior para se conectar por SSH à máquina virtual. 

```bash
ssh azureuser@<ip-address-destination>
```

Copie e cole os comandos a seguir depois de fazer logon.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Esses comandos atualizarão sua máquina virtual, instalarão o nginx e criarão um arquivo de 100 KB. Esse arquivo será recuperado da VM de origem usando o serviço NAT.

Feche a sessão SSH com a VM de destino.

## <a name="prepare-test-on-source-vm"></a>Preparar o teste na VM de origem

Primeiro precisamos descobrir o endereço IP da VM de origem.  Para obter o endereço IP público da VM, use o [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para usá-lo nas etapas seguintes. Indique isso como a máquina virtual de origem.

### <a name="log-into-source-vm"></a>Fazer logon na VM de origem

Novamente, as credenciais SSH são armazenadas no Cloud Shell. Abra uma nova guia para o [Azure Cloud Shell](https://shell.azure.com) no navegador.  Use o endereço IP recuperado na etapa anterior para se conectar por SSH à máquina virtual. 

```bash
ssh azureuser@<ip-address-source>
```

Copie e cole os comandos a seguir para se preparar para testar o serviço NAT.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Esses comandos atualizarão sua máquina virtual, instalarão o go, instalarão o [hey](https://github.com/rakyll/hey) do GitHub e atualizarão seu ambiente shell.

Agora você está pronto para testar o serviço NAT.

## <a name="validate-nat-service"></a>Validar o serviço NAT

Enquanto estiver conectado à VM de origem, você poderá usar o **curl** e o **hey** para gerar solicitações para o endereço IP de destino.

Use curl para recuperar o arquivo de 100 KB.  Substitua **\<destino-de-endereço-IP>** no exemplo abaixo pelo endereço IP de destino copiado anteriormente.  O parâmetro **--output** indica que o arquivo recuperado será descartado.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Você também pode gerar uma série de solicitações usando o **hey**. Novamente, substitua **\<destino-de-endereço-IP>** pelo endereço IP de destino copiado anteriormente.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Esse comando gerará 100 solicitações, 10 simultaneamente, com um tempo limite de 30 segundos. A conexão TCP não será reutilizada.  Cada solicitação recuperará 100 KB.  No fim da execução, o **hey** relatará algumas estatísticas sobre o desempenho do serviço NAT.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) para remover o grupo de recursos e todos os recursos contidos nele.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um gateway da NAT, criou uma VM de origem e destino e, em seguida, testou o gateway da NAT.

Examine as métricas no Azure Monitor para verificar a operação do serviço NAT. Diagnostique problemas como o esgotamento de recursos das portas SNAT disponíveis.  O esgotamento de recursos das portas SNAT é resolvido com facilidade adicionando mais recursos de endereço IP público, recursos de prefixo IP público ou ambos.

- Saiba mais sobre a [NAT de Rede Virtual](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway da NAT](./nat-gateway-resource.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando a CLI do Azure](./quickstart-create-nat-gateway-cli.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o portal do Azure](./quickstart-create-nat-gateway-portal.md).
- [Forneça comentários sobre a versão prévia pública](https://aka.ms/natfeedback).

> [!div class="nextstepaction"]

