---
title: Aumento anormal no volume de exceção – insights de Aplicativo Azure
description: Monitore as exceções de aplicativo com detecção inteligente no Aplicativo Azure insights de padrões incomuns no volume de exceção.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: a08fae4774a8afb9959f55ea3196cd1a45c33439
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671759"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Aumento anormal no volume de exceção (visualização)

O Application Insights analisa automaticamente as exceções lançadas no aplicativo e pode avisar sobre padrões incomuns em sua telemetria de exceção.

Este recurso não exige nenhuma configuração especial, diferente de [configurar relatório de exceção](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) para seu aplicativo. Ele permanece ativo quando o aplicativo gera telemetria de exceção suficiente.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando eu receberia este tipo de notificação de detecção inteligente?
Você poderá receber esse tipo de notificação se o aplicativo estiver apresentando um aumento anormal no número de exceções de um tipo específico durante um dia, em comparação com uma linha de base calculada nos sete dias anteriores.
Algoritmos de aprendizado de máquina estão sendo usados para detectar o aumento na contagem de exceção ao levar em conta um crescimento natural em seu uso do aplicativo.

## <a name="does-my-app-definitely-have-a-problem"></a>Meu aplicativo definitivamente tem um problema?
Não, uma notificação não significa que seu aplicativo definitivamente tem um problema. Embora um número excessivo de exceções geralmente indique um problema de aplicativo, essas exceções podem ser benignas e tratadas corretamente pelo seu aplicativo.

## <a name="how-do-i-fix-it"></a>Como corrigi-la?
As notificações incluem informações de diagnóstico para oferecer suporte no processo de diagnóstico:
1. **Triagem.** A notificação mostra quantos usuários ou quantas solicitações foram afetadas. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Escopo.** O problema está afetando todo o tráfego ou apenas algumas operações? Essas informações podem ser obtidas na notificação.
3. **Diagnosticar.** A detecção contém informações sobre o método do qual a exceção foi lançada, bem como o tipo de exceção. Você também pode usar o relatórios de vinculação para dar suporte a informações e itens relacionados para ajudá-lo a diagnosticar o problema.
