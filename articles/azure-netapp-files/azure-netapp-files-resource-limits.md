---
title: Limites de recursos no Azure NetApp Files | Microsoft Docs
description: Descreve os limites para Azure NetApp Files recursos e como solicitar o aumento do limite de recursos.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: b-juche
ms.openlocfilehash: 7637d18017f5bdc76c8a271198a88f21a59a6aac
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604986"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites de recursos no Azure NetApp Files

O reconhecimento dos limites de recursos no Azure NetApp Files ajuda você a gerenciar seus volumes.

## <a name="resource-limits"></a>Limites de recursos

A tabela a seguir descreve os limites de recursos para Azure NetApp Files:

|  Recurso  |  Limite padrão  |  Ajustável por meio de solicitação de suporte  |
|----------------|---------------------|--------------------------------------|
|  Número de contas do NetApp por região do Azure   |  10    |  Sim   |
|  Número de pools de capacidade por conta do NetApp   |    25     |   Sim   |
|  Número de volumes por pool de capacidade     |    500   |    Sim     |
|  Número de instantâneos por volume       |    255     |    Não        |
|  Número de sub-redes delegadas a Azure NetApp Files (Microsoft. NetApp/volumes) por rede virtual do Azure    |   1   |    Não    |
|  Número de IPs usados em uma VNet (incluindo VNets imediatamente emparelhados) com Azure NetApp Files   |    1000   |    Sim   |
|  Tamanho mínimo de um pool de capacidade única   |  4 TiB     |    Não  |
|  Tamanho máximo de um pool de capacidade única    |  500 TiB   |   Não   |
|  Tamanho mínimo de um único volume    |    100 GiB    |    Não    |
|  Tamanho máximo de um único volume     |    100 TiB    |    Não    |
|  Número máximo de arquivos ([maxfiles](#maxfiles)) por volume     |    100 milhões    |    Sim    |    
|  Tamanho máximo de um único arquivo     |    16 TiB    |    Não    |    

## Limites de maxfiles<a name="maxfiles"></a> 

Azure NetApp Files volumes têm um limite chamado *maxfiles*. O limite de maxfiles é o número de arquivos que um volume pode conter. O limite de maxfiles para um volume de Azure NetApp Files é indexado com base no tamanho (cota) do volume. O limite de maxfiles para um volume aumenta ou diminui na taxa de 20 milhões arquivos por TiB de tamanho de volume provisionado. 

O serviço ajusta dinamicamente o limite de maxfiles para um volume com base em seu tamanho provisionado. Por exemplo, um volume configurado inicialmente com um tamanho de 1 TiB teria um limite de maxfiles de 20 milhões. Alterações subsequentes no tamanho do volume resultarão em um reajuste automático do limite de maxfiles com base nas seguintes regras: 

|    Tamanho do volume (cota)     |  Reajuste automático do limite de maxfiles    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 milhões     |
|    > = 1 TiB, mas < 2 TiB    |    40 milhões     |
|    > = 2 TiB, mas < 3 TiB    |    60 milhões     |
|    > = 3 TiB, mas < 4 TiB    |    80 milhões     |
|    > = 4 TiB                |    100 milhões    |

Para qualquer tamanho de volume, você pode iniciar uma [solicitação de suporte](#limit_increase) para aumentar o limite de maxfiles para além de 100 milhões.

## Aumento do limite de solicitação<a name="limit_increase"></a> 

Você pode criar uma solicitação de suporte do Azure para aumentar os limites ajustáveis da tabela acima. 

Do plano de navegação portal do Azure: 

1. Clique em **ajuda + suporte**.
2. Clique em **+ nova solicitação de suporte**.
3. Na guia noções básicas, forneça as seguintes informações: 
    1. Tipo de problema: selecione **limites de serviço e de assinatura (cotas)** .
    2. Assinaturas: selecione a assinatura para o recurso que você precisa que a cota aumentou.
    3. Tipo de cota: selecione **armazenamento: limites de Azure NetApp files**.
    4. Clique em **Avançar: soluções**.
4. Na guia detalhes:
    1. Na caixa Descrição, forneça as seguintes informações para o tipo de recurso correspondente:

        |  Recurso  |    Recursos pai      |    Novos limites solicitados     |    Motivo para aumento de cota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Conta |  *ID da assinatura*   |  *Número de Nova **conta** máxima solicitado*    |  *Qual cenário ou caso de uso solicitou a solicitação?*  |
        |  pool    |  *ID da assinatura, URI da conta*  |  *Novo número máximo de **pool** solicitado*   |  *Qual cenário ou caso de uso solicitou a solicitação?*  |
        |  Volume  |  *ID da assinatura, URI da conta, URI do pool*   |  *Novo número de **volume** máximo solicitado*     |  *Qual cenário ou caso de uso solicitou a solicitação?*  |
        |  Maxfiles  |  *ID da assinatura, URI da conta, URI do pool, URI do volume*   |  *Novo número máximo de **maxfiles** solicitado*     |  *Qual cenário ou caso de uso solicitou a solicitação?*  |    

    2. Especifique o método de suporte apropriado e forneça suas informações de contrato.

    3. Clique em **Avançar: examinar + criar** para criar a solicitação. 


## <a name="next-steps"></a>Próximas etapas  

- [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modelo de custo para Azure NetApp Files](azure-netapp-files-cost-model.md)
