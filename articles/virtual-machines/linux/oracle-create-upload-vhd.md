---
title: Criar e carregar um VHD de Oracle Linux
description: Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contenha um sistema operacional Oracle Linux.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/10/2019
ms.author: mimckitt
ms.openlocfilehash: 240333e55f23f2536d3cf14d2bb817e5776c8139
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251590"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Preparar uma máquina virtual Oracle Linux para o Azure

Este artigo pressupõe que você já instalou um sistema operacional Oracle Linux em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="oracle-linux-installation-notes"></a>Notas de instalação do Oracle Linux
* Veja também as [Notas de instalação gerais do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* O Hyper-V e o suporte do Azure Oracle Linux com o UEK (inbreakable Enterprise kernel) ou o kernel compatível com Red Hat.
* O UEK2 da Oracle não é compatível com o Hyper-V nem com o Azure, pois não contém os drivers necessários.
* O formato VHDX não tem suporte no Azure, somente o **VHD fixo**.  Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) em discos de dados.
* As versões do kernel do Linux anteriores à 2.6.37 não são suporte para NUMA no Hyper-V com tamanhos de VM maiores. Esse problema afeta principalmente as distribuições mais antigas usando o kernel upstream Red Hat 2.6.32 e foi corrigido no Oracle Linux 6,6 e posterior
* Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários.  Verifique as etapas a seguir para obter mais informações a esse respeito.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco não processado para VHD, certifique-se de que o tamanho do disco não processado seja um múltiplo de 1 MB antes da conversão. Consulte [Notas de Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.
* Certifique-se de que o repositório `Addons` está habilitado. Edite o arquivo `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7) e altere a `enabled=0` de linha para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** neste arquivo.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6,4 e posterior
Você deve concluir as etapas de configuração específicas do sistema operacional para que a máquina virtual seja executada no Azure.

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela da máquina virtual.
3. Desinstale o NetworkManager executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Observação:** se o pacote ainda não estiver instalado, esse comando irá falhar com uma mensagem de erro. Isso é esperado.
4. Crie um arquivo chamado **network** in the `/etc/sysconfig/` que contém o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Crie um arquivo chamado **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` que contém o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Modifique as regras de udev para evitar a geração de regras estáticas das interfaces Ethernet. Essas regras podem provocar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Certifique-se de que o serviço de rede será iniciado na inicialização executando o seguinte comando:
   
        # chkconfig network on
8. Instale o python-pyasn1 executando o seguinte comando:
   
        # sudo yum install python-pyasn1
9. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel inclui os seguintes parâmetros:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração.
   
   Além disso, recomendamos que você *remova* os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
   As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.
   
   Você pode deixar configurada a opção `crashkernel` , mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.
10. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.
11. Instale o Agente Linux do Azure executando o comando a seguir. A versão mais recente é 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Observe que a instalação do pacote WALinuxAgent removerá o NetworkManager e os pacotes NetworkManager-gnome se eles já não tiverem sido removidos conforme descrito na etapa 2.
12. Não crie espaço de permuta no disco do SO.
    
    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7,0 e posterior
**Alterações no Oracle Linux 7**

A preparação de uma máquina virtual Oracle Linux 7 para o Azure é muito parecida com a preparação das máquinas virtuais Oracle Linux 6, mas há diversas diferenças que merecem atenção:

* O Azure dá suporte a Oracle Linux com o kernel do UEK (inquebrable Enterprise) ou kernel compatível com Red Hat. É recomendável Oracle Linux com UEK.
* O pacote do NetworkManager não entra mais em conflito com o agente Linux do Azure. Esse pacote é instalado por padrão e recomendamos que você não o remova.
* O GRUB2 agora é usado como carregador de inicialização padrão. Com isso, o procedimento de edição de parâmetros do kernel mudou (confira abaixo).
* O XFS agora é o sistema de arquivos padrão. Ainda é possível usar o sistema de arquivos ext4 se você preferir.

**Etapas de configuração**

1. No Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.
3. Crie um arquivo chamado **network** in the `/etc/sysconfig/` que contém o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Crie um arquivo chamado **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` que contém o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Modifique as regras de udev para evitar a geração de regras estáticas das interfaces Ethernet. Essas regras podem provocar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Certifique-se de que o serviço de rede será iniciado na inicialização executando o seguinte comando:
   
        # sudo chkconfig network on
7. Execute o comando a seguir para instalar o pacote python-pyasn1:
   
        # sudo yum install python-pyasn1
8. Execute o comando a seguir para limpar os metadados atuais do yum e instalar atualizações:
   
        # sudo yum clean all
        # sudo yum -y update
9. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/etc/default/grub" em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Ele também desativa as convenções de nomenclatura para NICs no Oracle Linux 7 com o kernel corporativo inquebrável. Além disso, recomendamos que você *remova* os seguintes parâmetros:
   
       rhgb quiet crashkernel=auto
   
   As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.
   
   Você pode deixar configurada a opção `crashkernel` , mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.
10. Depois de editar "/etc/default/grub" como mostramos acimo, execute o comando a seguir para recompilar a configuração do grub:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.
12. Instale o Agente Linux do Azure executando o seguinte comando:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Não crie espaço de permuta no disco do SO.
    
    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (veja a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Próximas etapas
Agora você está pronto para usar o .vhd Oracle Linux para criar novas máquinas virtuais no Azure. Se esta é a primeira vez que você está carregando o arquivo .vhd para o Azure, consulte [Criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

