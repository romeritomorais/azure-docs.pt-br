---
title: Tamanhos de VM do Azure – HPC | Microsoft Docs
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: b900a95df00ccdd0ad9b5bee3887364195c7d1c2
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226727"
---
# <a name="high-performance-compute-vm-sizes"></a>Tamanhos de VM de computação de alto desempenho

As máquinas virtuais (VMs) da série H do Azure foram projetadas para fornecer desempenho de nível de liderança, escalabilidade MPI e eficiência de custo para uma variedade de cargas de trabalho do HPC do mundo real.

[Série HBv2](hbv2-series.md) O recurso VMs tem 200 GB/s Mellanox HDR InfiniBand, enquanto as VMs da série HB e HC apresentam 100 GB/s Mellanox EDR InfiniBand. Cada um desses tipos de VM é conectado em uma árvore Fat sem bloqueio para desempenho de RDMA otimizado e consistente. As VMs HBv2 dão suporte ao roteamento adaptável e ao transporte conectado dinâmico (DCT, em outros transportes Standard RC e UD). Esses recursos aprimoram o desempenho, a escalabilidade e a consistência do aplicativo e o uso deles é altamente recomendável.

[Série HB](hb-series.md) As VMs são otimizadas para aplicativos orientados por largura de banda de memória, como dinâmica de fluidos, análise de elemento finito explícito e modelagem de clima. As VMs HB apresentam 60 AMD EPYC 7551 núcleos de processador, 4 GB de RAM por núcleo de CPU e nenhum hyperthreading. A plataforma AMD EPYC fornece mais de 260 GB/s de largura de banda de memória.

[Série HC](hc-series.md) As VMs são otimizadas para aplicativos orientados por computação densa, como análise implícita de elemento finito, Molecular Dynamics e química computacional. O HC VMs Feature 44 Intel Xeon Platinum 8168 núcleos de processador, 8 GB de RAM por núcleo de CPU e nenhum hyperthreading. A plataforma Intel Xeon Platinum dá suporte ao ecossistema avançado de ferramentas de software da Intel, como a biblioteca de kernels matemáticos da Intel.

[Série H](h-series.md) As VMs são otimizadas para aplicativos orientados por altas frequências de CPU ou grandes requisitos de memória por núcleo. As VMs da série H apresentam 8 ou 16 núcleos de processador Intel Xeon E5 2667 v3, 7 ou 14 GB de RAM por núcleo de CPU e nenhum hyperthreading. A série H apresenta 56 GB/s Mellanox FDR InfiniBand em uma configuração de árvore de Fat sem bloqueio para desempenho consistente de RDMA. As VMs da série H dão suporte ao Intel MPI 5. x e ao MS-MPI.

## <a name="deployment-considerations"></a>Considerações de implantação

- **Assinatura do Azure** – Para implantar um número maior de instâncias de computação intensiva, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

- **Preço e disponibilidade** – esses tamanhos de VM são oferecidos apenas no tipo de preço Standard. Confira [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade nas regiões do Azure.
- **Cota de núcleos** – Talvez seja preciso aumentar a cota de núcleos em sua assinatura do Azure, saindo do valor padrão. Sua assinatura também pode limitar o número de núcleos que você pode implantar em determinadas famílias de tamanho de VM, incluindo a série de H. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](../azure-supportability/how-to-create-azure-support-request.md) gratuitamente. (Os limites padrão podem variar dependendo de sua categoria de assinatura.)

  > [!NOTE]
  > Entre em contato com o Suporte do Azure se precisar de capacidade em larga escala. Cotas do Azure são limites de crédito, não garantias de capacidade. Independentemente de sua cota, você é cobrado apenas pelo núcleos utilizados.
  
- **Rede virtual** – Não é necessário ter uma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) do Azure para usar instâncias de computação intensiva. No entanto, para muitas implantações, é necessária pelo menos uma rede virtual do Azure baseada em nuvem ou uma conexão site a site se você precisar acessar recursos locais. Quando necessário, você precisará criar uma nova rede virtual para implantar as instâncias. Não há suporte para a adição de VMs de computação intensiva a uma rede virtual em um grupo de afinidades.
- **Redimensionamento** – devido ao hardware especializado, você só pode redimensionar instâncias de computação intensiva dentro da mesma família de tamanho (série H ou série A de computação intensiva). Por exemplo, somente é possível redimensionar uma VM da série H de um tamanho da série H para outro. Além disso, não há suporte para o redimensionamento de um tamanho sem computação intensiva para um tamanho de computação intensiva.  

> [!NOTE]
> As VMs A8 – a11 estão planejadas para aposentadoria em 3/2021. Para obter mais informações, consulte [Guia de migração do HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Instâncias compatíveis com RDMA

Um subconjunto das instâncias de computação intensiva (A8, A9, H16r, H16mr, HB e HC) apresentam um adaptador de rede para conectividade RDMA (acesso remoto direto à memória). Os tamanhos de série N selecionados designados com ' r ', como as configurações de NC24rs (NC24rs_v2 e NC24rs_v3) também são compatíveis com RDMA. Essa interface é além do adaptador de rede do Azure padrão disponível para outros tamanhos de VM.

Essa interface permite que as instâncias compatíveis com RDMA se comuniquem por meio de uma rede InfiniBand (IB), operando em taxas de EDR para HB, HC, FDR taxas para máquinas virtuais da série N com capacidade para H16r, H16mr e RDMA e taxas de QDR para máquinas virtuais A8 e A9. Esses recursos RDMA podem melhorar a escalabilidade e o desempenho de determinados aplicativos MPI (Interface de Transmissão de Mensagens). Para obter mais informações sobre velocidade, consulte os detalhes nas tabelas desta página.

> [!NOTE]
> No Azure, o IP sobre IB tem suporte apenas nas VMs habilitadas para SR-IOV (SR-IOV para InfiniBand, atualmente HB e HC). O RDMA sobre IB tem suporte para todas as instâncias compatíveis com RDMA.
- **Sistema operacional** -Windows Server 2016 em todas as VMs da série HPC acima. O Windows Server 2012 R2, o Windows Server 2012 também tem suporte em VMs não habilitadas para SR-IOV (portanto, excluindo HB e HC).

- **MPI** -os tamanhos de VM habilitados para Sr-IOV no Azure (HB, HC) permitem que quase qualquer tipo de MPI seja usado com o Mellanox ofed.
Em VMs não habilitadas para SR-IOV, as implementações de MPI com suporte usam a interface do Microsoft Network Direct (ND) para se comunicar entre instâncias. Portanto, somente as versões do Microsoft MPI (MS-MPI) 2012 R2 ou posterior e do Intel MPI 5. x têm suporte. Versões posteriores (2017, 2018) da biblioteca de tempo de execução do Intel MPI podem ou não ser compatíveis com os drivers RDMA do Azure.

- **Extensão de VM InfiniBandDriverWindows** -em VMs compatíveis com RDMA, adicione a extensão InfiniBandDriverWindows para habilitar a InfiniBand. Essa extensão de VM do Windows instala drivers diretos de rede do Windows (em VMs que não são de SR-IOV) ou drivers Mellanox OFED (em VMs SR-IOV) para conectividade RDMA.
Em determinadas implantações de instâncias A8 e A9, a extensão HpcVmDrivers é adicionada automaticamente. Observe que a extensão de VM HpcVmDrivers está sendo preterida; Ele não será atualizado. Para adicionar a extensão de VM a uma VM, use cmdlets do [Azure PowerShell](/powershell/azure/overview). 

  O comando a seguir instala a extensão mais recente da versão 1,0 do InfiniBandDriverWindows em uma VM compatível com RDMA existente chamada *myVM* implantada no grupo de recursos chamado *MyResource* Group na região *oeste dos EUA* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Como alternativa, as extensões de VM podem ser incluídas em modelos de Azure Resource Manager para fácil implantação, com o seguinte elemento JSON:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  O comando a seguir instala a extensão mais recente da versão 1,0 do InfiniBandDriverWindows em todas as VMs compatíveis com RDMA em um conjunto de dimensionamento de máquinas virtuais existente chamado *myVMSS* implantado no grupo de recursos chamado *MyResource*Group:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Para obter mais informações, consulte [Recursos e extensões da máquina virtual](./extensions/overview.md). Também é possível trabalhar com extensões de VMs implantadas no [modelo de implantação clássico](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **Espaço de endereço de rede RDMA** - A rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/16. Para executar aplicativos MPI em instâncias implantadas em uma rede virtual do Azure, verifique se o espaço do endereço de rede virtual não se sobrepõe à rede RDMA.

## <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criar clusters de VMs do Windows HPC que podem se comunicar usando a rede RDMA, incluindo: 

- **Máquinas virtuais** - implante as VMs HPC compatíveis com RDMA no mesmo conjunto de disponibilidade (ao usar o modelo de implantação do Azure Resource Manager). Se você usar o modelo de implantação clássico, implante as VMs no mesmo serviço de nuvem. 

- **Conjuntos de dimensionamento de máquinas virtuais** – em um conjunto de dimensionamento de máquinas virtuais, certifique-se de limitar a implantação a um único grupo de posicionamento. Por exemplo, em um modelo do Resource Manager, defina a `singlePlacementGroup`propriedade como`true`. 

- **MPI entre máquinas virtuais** – se a comunicação MPI for necessária entre VMs (máquinas virtuais), verifique se as VMs estão no mesmo conjunto de disponibilidade ou na mesma configuração de conjunto de dimensionamento da máquina virtual.

- **Azure CycleCloud** - Crie um cluster HPC em [Azure CycleCloud](/azure/cyclecloud/) para executar tarefas MPI em nós do Windows.

- **Azure Batch** - Crie um pool do [Azure Batch](/azure/batch/) para executar cargas de trabalho MPI em nós de computação do Windows Server. Para obter mais informações, consulte [Usar instâncias habilitadas para RDMA ou habilitadas para GPU em Conjuntos de lotes](../batch/batch-pool-compute-intensive-sizes.md). Consulte também o [Batch Shipyard](https://github.com/Azure/batch-shipyard) projeto, para executar cargas de trabalho baseados em contêiner no lote.

- O **Microsoft HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) inclui um ambiente de tempo de execução para MS-MPI que usa a rede RDMA do Azure quando implantado em VMs Linux compatíveis com RDMA. Por exemplo, para implantações, consulte [configurar um cluster de RDMA do Linux com o HPC Pack para executar aplicativos MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- Para que as listas de verificação usem as instâncias de computação intensiva com o HPC Pack no Windows Server, consulte [configurar um cluster de RDMA do Linux com o HPC Pack para executar aplicativos MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

- Para usar instâncias de computação intensiva para executar aplicativos MPI com o Lote do Azure, consulte [Usar tarefas de várias instâncias para executar aplicativos MPI (Interface de Transmissão de Mensagens) no Lote do Azure](../batch/batch-mpi.md).

- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.