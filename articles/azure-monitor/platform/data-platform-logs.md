---
title: Logs em Azure Monitor | Microsoft Docs
description: Descreve os logs no Azure Monitor que são usados para análise avançada de dados de monitoramento.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 06b35fdcd9da333c3c9d94d41c84a2f25ede59ad
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361757"
---
# <a name="logs-in-azure-monitor"></a>Logs no Azure Monitor

> [!NOTE]
> Todos os dados coletados pelo Azure Monitor se adaptam a um dos dois tipos fundamentais, métricas e logs. Este artigo descreve os logs do. Consulte [métricas em Azure monitor](data-platform-metrics.md) para obter uma descrição detalhada das métricas e o [monitoramento de dados coletados pelo Azure monitor](data-platform.md) para uma comparação dos dois.

Os logs em Azure Monitor são especialmente úteis para executar análises complexas em dados de uma variedade de fontes. Este artigo descreve como os logs são estruturados em Azure Monitor, o que você pode fazer com os dados e identifica diferentes fontes de dados que armazenam dados em logs.

> [!NOTE]
> É importante distinguir entre Azure Monitor logs e fontes de dados de log no Azure. Por exemplo, os eventos de nível de assinatura no Azure são gravados em um [log de atividades](platform-logs-overview.md) que você pode exibir no menu Azure monitor. A maioria dos recursos irá gravar informações operacionais em um [log de recursos](platform-logs-overview.md) que você pode encaminhar para locais diferentes. Os logs de Azure Monitor são uma plataforma de dados de log que coleta logs de atividade e logs de recursos junto com outros dados de monitoramento para fornecer análise profunda em todo o seu conjunto de recursos.

## <a name="what-are-azure-monitor-logs"></a>O que são Azure Monitor logs?

Os logs em Azure Monitor contêm diferentes tipos de dados organizados em registros com diferentes conjuntos de propriedades para cada tipo. Os logs podem conter valores numéricos como Azure Monitor métricas, mas normalmente contêm dados de texto com descrições detalhadas. Eles diferem ainda mais dos dados de métricas, pois variam em sua estrutura e, muitas vezes, não são coletados em intervalos regulares. A telemetria, como eventos e rastreamentos, é armazenada Azure Monitor logs além dos dados de desempenho para que todos possam ser combinados para análise.

Um tipo comum de entrada de log é um evento, que é coletado esporadicamente. Os eventos são criados por um aplicativo ou serviço e normalmente incluem informações suficientes para fornecer o contexto completo por conta própria. Por exemplo, um evento pode indicar que um recurso específico foi criado ou modificado, um novo host é iniciado em resposta ao maior tráfego ou um erro foi detectado em um aplicativo.

 Como o formato dos dados pode variar, os aplicativos podem criar logs personalizados usando a estrutura que eles exigem. Os dados de métrica podem até mesmo ser armazenados em logs para combiná-los com outros dados de monitoramento para análise de tendências e outros dados.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>O que você pode fazer com os logs de Azure Monitor?
A tabela a seguir lista as diferentes maneiras que você pode usar logs em Azure Monitor.


|  |  |
|:---|:---|
| Analisar | Use [log Analytics](../log-query/get-started-portal.md) no portal do Azure para gravar [consultas de log](../log-query/log-query-overview.md) e analisar interativamente os dados de log usando o poderoso mecanismo de análise de data Explorer.<br>Use o [console do Application insights Analytics](../app/analytics.md) no portal do Azure para gravar consultas de log e analisar interativamente os dados de log de Application insights. |
| Visualizar | Fixe os resultados da consulta renderizados como tabelas ou gráficos em um [painel do Azure](../../azure-portal/azure-portal-dashboards.md).<br>Crie uma [pasta de trabalho](../app/usage-workbooks.md) para combinar com vários conjuntos de dados em um relatório interativo. <br>Exportar os resultados de uma consulta para o [Power BI](powerbi.md) a fim de usar visualizações diferentes e compartilhar com usuários fora do Azure.<br>Exporte os resultados de uma consulta para [Grafana](grafana-plugin.md) para aproveitar seu painel e combinar com outras fontes de dados.|
| Alert | Configurar uma [regra de alerta de log](alerts-log.md) que envia uma notificação ou realiza [ação automatizada](action-groups.md) quando os resultados da consulta correspondem a um resultado específico.<br>Configure uma [regra de alerta de métrica](alerts-metric-logs.md) em determinados logs de dados de log extraídos como métricas. |
| Recuperar | Acessar resultados da consulta de log de uma linha de comando usando [CLI do Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Acessar resultados da consulta de log de uma linha de comando usando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Acessar resultados da consulta de log de um aplicativo personalizado usando a [API REST](https://dev.loganalytics.io/). |
| Exportação | Crie um fluxo de trabalho para recuperar dados de log e copie-os para um local externo usando [aplicativos lógicos](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Como os dados nos logs de Azure Monitor são estruturados?
Os dados coletados por logs de Azure Monitor são armazenados em um [espaço de trabalho do log Analytics](../platform/design-logs-deployment.md). Cada espaço de trabalho contém várias tabelas que armazenam dados de uma fonte específica. Embora todas as tabelas compartilhem [algumas propriedades comuns](log-standard-properties.md), cada uma tem um conjunto exclusivo de propriedades, dependendo do tipo de dados que armazena. Um novo espaço de trabalho terá um conjunto padrão de tabelas e mais tabelas serão adicionadas por diferentes soluções de monitoramento e outros serviços que gravam no espaço de trabalho.

Os dados de log de Application Insights usam o mesmo mecanismo de Log Analytics que espaços de trabalho, mas são armazenados separadamente para cada aplicativo monitorado. Cada aplicativo tem um conjunto padrão de tabelas para manter dados, como solicitações de aplicativos, exceções e exibições de página.

As consultas de log usarão dados de um espaço de trabalho Log Analytics ou de um aplicativo Application Insights. Você pode usar uma [consulta entre recursos](../log-query/cross-workspace-query.md) para analisar dados de aplicativos com outros dados de log ou para criar consultas, incluindo vários espaços de trabalho ou aplicativos.

![Workspaces](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Consultas de logs
Os dados em logs de Azure Monitor são recuperados usando uma [consulta de log](../log-query/log-query-overview.md) escrita com a [linguagem de consulta Kusto](../log-query/get-started-queries.md), que permite recuperar, consolidar e analisar rapidamente os dados coletados. Use [log Analytics](../log-query/portals.md) para gravar e testar consultas de Log no portal do Azure. Ele permite trabalhar com resultados interativamente ou fixá-los em um painel para exibi-los com outras visualizações.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Abra [log Analytics de Application insights](../app/analytics.md) para analisar Application insights dados.

![Análise do Application Insights](media/data-platform-logs/app-insights-analytics.png)

Você também pode recuperar dados de log usando a [API de log Analytics](https://dev.loganalytics.io/documentation/overview) e a [API REST Application insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Fontes de logs de Azure Monitor
O Azure Monitor pode coletar dados de log de várias origens no Azure e de recursos locais. As tabelas a seguir listam as diferentes fontes de dados disponíveis de diferentes recursos que gravam dados em logs de Azure Monitor. Cada um tem um link para obter detalhes sobre qualquer configuração necessária.

### <a name="azure-tenant-and-subscription"></a>Locatário e assinatura do Azure

| Dados | Descrição |
|:---|:---|
| Azure Active Directory logs de auditoria | Definido por meio de configurações de diagnóstico para cada diretório. Consulte [integrar logs do Azure AD com logs de Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Logs de atividade | Armazenados separadamente por padrão e podem ser usados para alertas quase em tempo real. Instale a solução do log Analytics da atividade para gravar no espaço de trabalho Log Analytics. Consulte [coletar e analisar logs de atividades do Azure em log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Recursos do Azure

| Dados | Descrição |
|:---|:---|
| Diagnóstico de recursos | Defina as configurações de diagnóstico para gravar em dados de diagnóstico, incluindo métricas para um espaço de trabalho Log Analytics. Consulte [transmitir logs de recursos do Azure para log Analytics](resource-logs-collect-storage.md). |
| Soluções de monitoramento | As soluções de monitoramento gravam dados que coletam para seu espaço de trabalho Log Analytics. Consulte [detalhes da coleta de dados para soluções de gerenciamento no Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Consulte [Monitoring Solutions in Azure monitor](../insights/solutions.md) para obter detalhes sobre como instalar e usar soluções. |
| Métricas | Envie métricas de plataforma para Azure Monitor recursos a um espaço de trabalho Log Analytics para manter os dados de log por períodos mais longos e para executar uma análise complexa com outros tipos de dados usando a [linguagem de consulta Kusto](/azure/kusto/query/). Consulte [transmitir logs de recursos do Azure para log Analytics](resource-logs-collect-storage.md). |
| Armazenamento de tabelas do Azure | Coletar dados do armazenamento do Azure onde alguns recursos do Azure gravam dados de monitoramento. Consulte [usar o armazenamento de BLOBs do Azure para o IIS e o armazenamento de tabelas do Azure para eventos com log Analytics](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Máquinas Virtuais

| Dados | Descrição |
|:---|:---|
|  Fontes de dados de agente | As fontes de dados coletadas de agentes do [Windows](agent-windows.md) e do [Linux](../learn/quick-collect-linux-computer.md) incluem eventos, dados de desempenho e logs personalizados. Consulte [fontes de dados do agente no Azure monitor](data-sources.md) para obter uma lista de fontes de dados e detalhes sobre a configuração. |
| Soluções de monitoramento | Soluções de monitoramento gravam dados que coletam de agentes para seu espaço de trabalho Log Analytics. Consulte [detalhes da coleta de dados para soluções de gerenciamento no Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Consulte [Monitoring Solutions in Azure monitor](../insights/solutions.md) para obter detalhes sobre como instalar e usar soluções. |
| System Center Operations Manager | Conecte Operations Manager grupo de gerenciamento ao Azure Monitor para coletar dados de eventos e de desempenho de agentes locais para logs. Consulte [conectar Operations Manager ao log Analytics](om-agents.md) para obter detalhes sobre essa configuração. |


### <a name="applications"></a>Aplicativos

| Dados | Descrição |
|:---|:---|
| Solicitações e exceções | Os dados detalhados sobre as solicitações e exceções do aplicativo estão nas tabelas _requests_, _pageViews_e _Exceptions_ . As chamadas para [componentes externos](../app/asp-net-dependencies.md) estão na tabela de _dependências_ . |
| Uso e desempenho | O desempenho do aplicativo está disponível nas tabelas _requests_, _browserTimings_ e _PerformanceCounters_ . Os dados para [métricas personalizadas](../app/api-custom-events-metrics.md#trackevent) estão na tabela _customMetrics_ .|
| Dados de rastreamento | Os resultados do [rastreamento distribuído](../app/distributed-tracing.md) são armazenados na tabela de _rastreamentos_ . |
| Testes de disponibilidade | Os dados de resumo dos [testes de disponibilidade](../app/monitor-web-app-availability.md) são armazenados na tabela _availabilityResults_ . Os dados detalhados desses testes estão em armazenamento separado e acessados de Application Insights no portal do Azure. |

### <a name="insights"></a>Insights

| Dados | Descrição |
|:---|:---|
| Azure Monitor para contêineres | Inventário e dados de desempenho coletados por [Azure monitor para contêineres](../insights/container-insights-overview.md). Consulte [detalhes de coleta de dados de contêiner](../insights/container-insights-log-search.md#container-records) para obter uma lista das tabelas. |
| Azure Monitor para VMs | Dados de mapa e desempenho coletados pelo [Azure monitor para VMs](../insights/vminsights-overview.md). Consulte [como consultar logs de Azure monitor para VMs](../insights/vminsights-log-search.md) para obter detalhes sobre como consultar esses dados. |

### <a name="custom"></a>Personalizado 

| Dados | Descrição |
|:---|:---|
| API REST | Gravar dados em um espaço de trabalho Log Analytics de qualquer cliente REST. Consulte [enviar dados de log para Azure monitor com a API do coletor de dados http](data-collector-api.md) para obter detalhes.
| Aplicativo Lógico | Grave todos os dados em um espaço de trabalho Log Analytics de um Workflow de aplicativo lógico com a ação do **coletor de dados do Azure log Analytics** . |

### <a name="security"></a>Segurança

| Dados | Descrição |
|:---|:---|
| Central de Segurança do Azure | A [central de segurança do Azure](/azure/security-center/) armazena os dados coletados em um espaço de trabalho log Analytics onde podem ser analisados com outros dados de log. Consulte [coleta de dados na central de segurança do Azure](../../security-center/security-center-enable-data-collection.md) para obter detalhes sobre a configuração do espaço de trabalho. |
| Azure Sentinel | O [Azure Sentinel](/azure/sentinel/) armazena dados de fontes de dados em um espaço de trabalho log Analytics. Consulte [conectar fontes de dados](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- Saiba mais sobre a [plataforma de dados Azure monitor](data-platform.md).
- Saiba mais sobre as [métricas em Azure monitor](data-platform-metrics.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](data-sources.md) para diferentes recursos no Azure.
