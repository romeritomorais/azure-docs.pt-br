---
title: Visão geral das VMs do Linux no Azure
description: Visão geral das máquinas virtuais do Linux no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bfda5fe7592d4c3f3f9550f406cf7635c43168ed
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896205"
---
# <a name="linux-virtual-machines-in-azure"></a>Máquinas virtuais do Linux no Azure

VM (Máquinas Virtuais) do Azure é um dos vários tipos de [recursos de computação sob demanda escalonáveis](/azure/architecture/guide/technology-choices/compute-decision-tree) oferecidos pelo Azure. Normalmente, você escolhe uma VM quando precisar de mais controle sobre o ambiente de computação do que as outras opções oferecem. Este artigo fornece informações sobre o que você deve considerar antes de criar uma VM, como criá-la e como gerenciá-la.

Uma VM do Azure oferece a flexibilidade da virtualização sem a necessidade de comprar e manter o hardware físico que a executa. No entanto, você ainda precisa manter a VM executando tarefas, como configurar, corrigir e instalar o software que será executado nela.

Máquinas virtuais do Azure podem ser usadas de várias maneiras. Alguns exemplos são:

* **Desenvolvimento e teste** – as VMs do Azure oferecem uma rápida e maneira fácil de criar um computador com configurações específicas, necessárias para codificar e testar um aplicativo.
* **Aplicativos na nuvem** – como a demanda por seu aplicativo pode flutuar, pode fazer sentido, em termos econômicos, executá-lo em uma VM no Azure. Você paga por VMs extras quando precisa delas e as desliga quando não são necessárias.
* **Datacenter estendido** – máquinas virtuais em uma rede virtual do Azure podem ser facilmente conectadas à rede de sua organização.

O número de VMs que o aplicativo usa pode ser escalado verticalmente e horizontalmente para atender às suas necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é necessário pensar antes de criar uma VM?
Sempre há uma infinidade de [considerações de design](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm) quando você cria uma infraestrutura de aplicativo no Azure. Estes aspectos de uma VM são importantes a considerar antes de começar:

* Os nomes dos recursos do aplicativo
* O local onde os recursos são armazenados
* O tamanho da VM
* O número máximo de VMs que podem ser criadas
* O sistema operacional que a VM executa
* A configuração da VM após ela ser iniciada
* Os recursos relacionados dos quais a VM precisa

### <a name="locations"></a>Locais
Todos os recursos criados no Azure são distribuídos entre várias [regiões geográficas](https://azure.microsoft.com/regions/) em todo o mundo. Normalmente, a região é chamada **local** quando você cria uma VM. Para uma VM, a localização especifica onde os discos rígidos virtuais são armazenados.

Esta tabela mostra algumas das maneiras de obter uma lista dos locais disponíveis.

| Método | Descrição |
| --- | --- |
| Portal do Azure |Selecione um local na lista quando você criar uma VM. |
| Azure PowerShell |Use o comando [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation). |
| API REST |Use a operação [Listar locais](https://docs.microsoft.com/rest/api/resources/subscriptions). |
| CLI do Azure |Use a operação [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest). |

## <a name="availability"></a>Disponibilidade
O Azure anunciou um Contrato de Nível de Serviço de máquina virtual de única instância de 99,9%, o melhor que há no mercado, desde que você implante a VM com armazenamento premium para todos os discos.  Para sua implantação se qualificar para o Contrato de Nível de Serviço de 99,95% padrão de VM, você ainda precisará implantar duas ou mais VMs que executem sua carga de trabalho dentro de um conjunto de disponibilidade. Um conjunto de disponibilidade garante que suas VMs sejam distribuídas entre vários domínios de falha nos datacenters do Azure, além de serem implantadas em hosts com janelas de manutenção diferentes. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

## <a name="vm-size"></a>Tamanho da VM
O [tamanho](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) da VM que você usa é determinado pela carga de trabalho que deseja executar. O tamanho que você escolhe, em seguida, determina fatores como capacidade de processamento, memória e armazenamento. O Azure oferece uma grande variedade de tamanhos para oferecer suporte a muitos tipos de usos.

O Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) com base no tamanho da VM e do sistema operacional. Para horas parciais, o Azure cobrará somente os minutos usados. O armazenamento terá o preço e será cobrado separadamente.

## <a name="vm-limits"></a>Limites de VM
Sua assinatura do Azure tem [limites de cota](../../azure-resource-manager/management/azure-subscription-service-limits.md) padrão que podem afetar a implantação de muitas VMs para seu projeto. O limite atual por assinatura é de 20 VMs por região. Os limites podem ser aumentados [pelo preenchimento de um tíquete de suporte para solicitar um aumento](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="managed-disks"></a>Managed Disks

Os Managed Disks trata da criação da conta de Armazenamento do Azure e do gerenciamento em segundo plano para você, além de garantir que você não tenha que se preocupar com os limites de escalabilidade da conta de armazenamento. Especifique o tamanho do disco e o nível de desempenho (Standard ou Premium) e o Azure cria e gerencia o disco. À medida que você adiciona discos ou dimensiona a VM para cima e para baixo, não é preciso se preocupar com o armazenamento que está sendo usado. Se você estiver criando novas VMs, [use o CLI do Azure](quick-create-cli.md) ou o portal do Azure para criar VMs com SO gerenciado e discos de dados. Caso tenha VMs com discos não gerenciados, você poderá [convertê-las para que tenham suporte do Managed Disks](convert-unmanaged-to-managed-disks.md).

Você também pode gerenciar suas imagens personalizadas em uma conta de armazenamento por região do Azure e usá-las para criar centenas de VMs na mesma assinatura. Para saber mais sobre os Managed Disks, confira a [Visão geral dos Managed Disks](../linux/managed-disks-overview.md).

## <a name="distributions"></a>Distribuições 
O Microsoft Azure dá suporte à execução de várias distribuições populares do Linux fornecidas e mantidas por diversos parceiros.  Você pode encontrar distribuições como Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD e muito mais no Azure Marketplace. A Microsoft trabalha ativamente com várias comunidades do Linux para adicionar ainda mais opções à lista de [Distribuições do Linux endossadas pelo Azure](endorsed-distros.md).

Se sua distribuição preferencial do Linux não estiver presente na galeria no momento, você poderá "trazer sua própria VM do Linux" [criando e carregando um VHD do Linux no Azure](create-upload-generic.md).

A Microsoft trabalha junto com parceiros para garantir que as imagens disponíveis sejam atualizadas e otimizadas para um runtime do Azure.  Para obter mais informações sobre os parceiros do Azure, confira os links a seguir:

* Linux no Azure – [Distribuições endossadas](endorsed-distros.md)
* SUSE – [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat – [Azure Marketplace – Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami Library para Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS no Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Marketplace – Serviço de Contêiner do Azure com Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)

## <a name="vm-sizes"></a>Tamanhos de VM
O [tamanho](sizes.md) da VM que você usa é determinado pela carga de trabalho que deseja executar. O tamanho que você escolhe, em seguida, determina fatores como capacidade de processamento, memória e armazenamento. O Azure oferece uma grande variedade de tamanhos para oferecer suporte a muitos tipos de usos.

O Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) com base no tamanho da VM e do sistema operacional. Para horas parciais, o Azure cobrará somente os minutos usados. O armazenamento terá o preço e será cobrado separadamente.

## <a name="cloud-init"></a>Cloud-init 

Para obter uma cultura apropriada do DevOps, toda a infraestrutura deve ser codificada.  Quando toda a infraestrutura reside no código, ela pode ser recriada com facilidade.  O Azure funciona com as principais ferramentas de automação, como a Ansible, Chef, SaltStack e Puppet.  O Azure também tem suas próprias ferramentas de automação:

* [Modelos do Azure](create-ssh-secured-vm-from-template.md)
* [VMAccess do Azure](using-vmaccess-extension.md)

O Azure dá suporte a [cloud-init](https://cloud-init.io/) na maioria das distribuições Linux que dão suporte a ele.  Trabalhamos ativamente com nossos parceiros endossados de distribuição de Linux para termos imagens de cloud-init habilitadas disponíveis no marketplace do Azure. Essas imagens farão com que as implantações e as configurações de cloud-init funcionem perfeitamente com VMs e conjuntos de dimensionamento de máquinas virtuais.

* [Como usar o cloud-init em VMs Linux do Azure](using-cloud-init.md)

## <a name="quotas"></a>Cotas
Cada assinatura do Azure tem limites de cota padrão que podem afetar a implantação de um grande número de VMs para seu projeto. O limite atual por assinatura é de 20 VMs por região.  Os limites de cota podem ser aumentados de forma rápida e fácil com a emissão de um tíquete de suporte para solicitar um aumento de limite.  Para obter mais detalhes sobre os limites de cota:

* [Limites de Serviço da assinatura do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md)


## <a name="storage"></a>Armazenamento
* [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
* [Adicionar um disco a uma VM do Linux usando a CLI do Azure](add-disk.md)
* [Como anexar um disco de dados a uma VM Linux no Portal do Azure](attach-disk-portal.md)

## <a name="networking"></a>Rede
* [Visão geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Abertura de portas para uma VM Linux no Azure](nsg-quickstart.md)
* [Criar um nome de domínio totalmente qualificado no portal do Azure](portal-create-fqdn.md)


## <a name="next-steps"></a>Próximas etapas

Crie sua primeira VM!

- [Portal](quick-create-portal.md)
- [CLI do Azure](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)

