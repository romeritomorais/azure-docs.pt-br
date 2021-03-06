---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67080914"
---
| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Contas do lote do Azure por região por assinatura | 1-3 |50 |
| Núcleos dedicados por conta do Lote | 90-900 | Contatar o suporte |
| Núcleos de baixa prioridade por conta do Lote | 10-100 | Contatar o suporte |
| Trabalhos **[ativos](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e agendas de trabalho por conta do lote (trabalhos**concluídos** não têm limite) | 100-300 | 1\.000<sup>1</sup> |
| Pools por conta do Lote | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Os limites padrão variam dependendo do tipo de assinatura que você usa para criar uma conta do Lote. As cotas de núcleos mostradas são para as contas do Lote no modo do Serviço de lote. [Exibir as cotas na conta do Lote](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup> Para solicitar um aumento além desse limite, entre em contato com o suporte do Azure.
