---
title: Coletar dados de log
titleSuffix: Azure Cognitive Search
description: Colete e analise dados de log habilitando uma configuração de diagnóstico e, em seguida, use a linguagem de consulta Kusto para explorar os resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462337"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Coletar e analisar dados de log para o Azure Pesquisa Cognitiva

Os logs de diagnóstico ou operacionais fornecem informações sobre as operações detalhadas do Azure Pesquisa Cognitiva e são úteis para monitorar processos de serviço e carga de trabalho. Internamente, os logs existem no back-end por um curto período de tempo, o suficiente para investigação e análise, caso você registre um tíquete de suporte. No entanto, se você quiser a autodireção sobre os dados operacionais, deverá configurar uma configuração de diagnóstico para especificar onde as informações de log serão coletadas.

A configuração de logs é útil para diagnóstico e preservação do histórico operacional. Depois de habilitar o registro em log, você pode executar consultas ou criar relatórios para análise estruturada.

A tabela a seguir enumera as opções para coletar e manter dados.

| Recurso | Usado para |
|----------|----------|
| [Enviar para Log Analytics espaço de trabalho](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | Os eventos e as métricas são enviados para um espaço de trabalho Log Analytics, que pode ser consultado no portal para retornar informações detalhadas. Para obter uma introdução, consulte Introdução [aos logs de Azure monitor](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Arquivar com armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Os eventos e as métricas são arquivados em um contêiner de BLOB e armazenados em arquivos JSON. Os logs podem ser bastante granulares (por hora/minuto), úteis para pesquisar um incidente específico, mas não para investigação aberta. Use um editor de JSON para exibir um arquivo de log bruto ou Power BI para agregar e Visualizar dados de log.|
| [Transmitir para o Hub de eventos](https://docs.microsoft.com/azure/event-hubs/) | Os eventos e as métricas são transmitidos para um serviço de hubs de eventos do Azure. Escolha esta opção como um serviço de coleta de dados alternativo para logs muito grandes. |

Os logs de Azure Monitor e o armazenamento de BLOBs estão disponíveis como um serviço gratuito para que você possa experimentá-lo gratuitamente pelo tempo de vida da sua assinatura do Azure. O Application Insights é gratuito para se inscrever e usar, desde que o tamanho de dados do aplicativo esteja abaixo de certos limites (confira a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para saber mais).

## <a name="prerequisites"></a>Prerequisites

Se você estiver usando o Log Analytics ou o armazenamento do Azure, poderá criar recursos com antecedência.

+ [Criar um espaço de trabalho do log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>Habilitar coleta de dados

As configurações de diagnóstico especificam como os eventos registrados e as métricas são coletados.

1. Em **Monitoramento**, selecione **Configurações de diagnóstico**.

   ![Configurações de diagnóstico](./media/search-monitor-usage/diagnostic-settings.png "Configurações de Diagnóstico")

1. Selecione **+ Adicionar configuração de diagnóstico**

1. Marque **log Analytics**, selecione seu espaço de trabalho e selecione **OperationLogs** e **biométricas**.

   ![Configurar coleta de dados](./media/search-monitor-usage/configure-storage.png "Configurar a coleta de dados")

1. Salve a configuração.

1. Depois que o registro em log tiver sido habilitado, use o serviço de pesquisa para começar a gerar logs e métricas. Levará tempo antes que os eventos e as métricas registrados fiquem disponíveis.

Por Log Analytics, serão vários minutos antes que os dados estejam disponíveis, após o qual você pode executar consultas Kusto para retornar dados. Para obter mais informações, consulte [monitorar solicitações de consulta](search-monitor-logs.md).

Para o armazenamento de BLOBs, ele leva uma hora antes que os contêineres apareçam no armazenamento de BLOBs. Haverá um blob por hora, por contêiner. Os contêineres são criados apenas quando há uma atividade para registrar ou medir. Quando os dados são copiados para uma conta de armazenamento, eles são formatados como JSON e são colocados em dois contêineres:

+ insights-logs-operationlogs: para logs de tráfego de pesquisa
+ insights-metrics-pt1m: para métrica

## <a name="query-log-information"></a>Informações do log de consulta

Nos logs de diagnóstico, duas tabelas contêm logs e métricas para o Azure Pesquisa Cognitiva: **AzureDiagnostics** e **AzureMetrics**.

1. Em **monitoramento**, selecione **logs**.

1. Insira **AzureMetrics** na janela de consulta. Execute esta consulta simples para familiarizar-se com os dados coletados nesta tabela. Role pela tabela para exibir as métricas e os valores. Observe a contagem de registros na parte superior e, se o serviço estiver coletando métricas por algum tempo, talvez você queira ajustar o intervalo de tempo para obter um conjunto de dados gerenciável.

   ![Tabela AzureMetrics](./media/search-monitor-usage/azuremetrics-table.png "Tabela AzureMetrics")

1. Insira a consulta a seguir para retornar um conjunto de resultados tabulares.

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Repita as etapas anteriores, começando com **AzureDiagnostics** para retornar todas as colunas para fins informativos, seguidos por uma consulta mais seletiva que extrai informações mais interessantes.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Tabela AzureDiagnostics](./media/search-monitor-usage/azurediagnostics-table.png "Tabela AzureDiagnostics")

## <a name="log-schema"></a>Esquema do log

Estruturas de dados que contêm dados de log de Pesquisa Cognitiva do Azure estão em conformidade com o esquema abaixo. 

Para o armazenamento de BLOBs, cada blob tem um objeto raiz chamado **registros** que contém uma matriz de objetos de log. Cada blob contém registros de todas as operações que ocorreram durante a mesma hora.

A tabela a seguir é uma lista parcial de campos comuns ao log de diagnóstico.

| Nome | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| timeGenerated |DATETIME |"2018-12-07T00:00:43.6872559Z" |Carimbo de data/hora da operação |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Seu ResourceId |
| operationName |string |"Query.Search" |O nome da operação |
| operationVersion |string |"2019-05-06" |A api-version usada |
| category |string |"OperationLogs" |constante |
| resultType |string |"Success" |Valores possíveis: Success ou Failure |
| resultSignature |INT |200 |Código do resultado HTTP |
| durationMS |INT |50 |Duração da operação em milissegundos |
| properties |objeto |confira a seguinte tabela |Objeto que contém os dados específicos da operação |

### <a name="properties-schema"></a>Esquema de propriedades

As propriedades abaixo são específicas para Pesquisa Cognitiva do Azure.

| Nome | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| Description_s |string |"GET /indexes('content')/docs" |Ponto de extremidade da operação |
| Documents_d |INT |42 |Número de documentos processados |
| IndexName_s |string |"Test-index" |Nome do índice associado à operação |
| Query_s |string |"?search=AzureSearch&$count=true&api-version=2019-05-06" |Parâmetros da consulta |

## <a name="metrics-schema"></a>Esquema de métricas

As métricas são capturadas para solicitações de consulta e medidas em intervalos de um minuto. Cada métrica expõe valores mínimo, máximo e médios por minuto. Para obter mais informações, consulte [monitorar solicitações de consulta](search-monitor-queries.md).

| Nome | Type | Exemplo | Observações |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |sua ID de recurso |
| metricName |string |"Latency" |o nome da métrica |
| time |DATETIME |"2018-12-07T00:00:43.6872559Z" |carimbo de data/hora da operação |
| média |INT |64 |O valor médio das amostras brutas no intervalo de tempo de métrica, unidades em segundos ou percentual, dependendo da métrica. |
| mínimo |INT |37 |O valor mínimo das amostras brutas no intervalo de tempo de métrica, unidades em segundos. |
| máximo |INT |78 |O valor máximo das amostras brutas no intervalo de tempo de métrica, unidades em segundos.  |
| total |INT |258 |O valor total das amostras brutas no intervalo de tempo de métrica, unidades em segundos.  |
| count |INT |4 |O número de métricas emitidas de um nó para o log dentro do intervalo de um minuto.  |
| intervalo de tempo |string |"PT1M" |O intervalo de tempo da métrica no ISO 8601. |

É comum que as consultas sejam executadas em milissegundos, portanto, somente as consultas que medem como segundos aparecerão em métrica como QPS.

Para a métrica de **consultas de pesquisa por segundo** , o mínimo é o valor mais baixo para consultas de pesquisa por segundo que foi registrado durante esse minuto. O mesmo se aplica ao valor máximo. O valor médio é a agregação durante todo o minuto. Por exemplo, em um minuto, você pode ter um padrão como este: um segundo de alta carga que é o máximo para SearchQueriesPerSecond, seguido de 58 segundos de carga média e, finalmente, um segundo com apenas uma consulta, que é o mínimo.

Para **consultas de pesquisa limitadas percentual**, mínimo, máximo, média e total, todos têm o mesmo valor: a porcentagem de consultas de pesquisa que foram limitadas, do número total de consultas de pesquisa durante um minuto.

## <a name="view-raw-log-files"></a>Exibir arquivos de log brutos

O armazenamento de BLOBs é usado para arquivar arquivos de log. Você pode usar qualquer editor de JSON para exibir o arquivo de log. Se você não tem um, recomendamos usar o [Visual Studio Code](https://code.visualstudio.com/download).

1. No portal do Azure, abra sua conta de armazenamento. 

2. No painel de navegação à esquerda, clique em **Blobs**. Você deve ver **insights-logs-operationlogs** e **insights-metrics-pt1m**. Esses contêineres são criados pelo Azure Pesquisa Cognitiva quando os dados de log são exportados para o armazenamento de BLOBs.

3. Clique para baixo na hierarquia de pastas até alcançar o arquivo .json.  Use o menu de contexto para baixar o arquivo.

Depois de baixar o arquivo, abra-o em um editor de JSON para exibir o conteúdo.

## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, examine os conceitos básicos do monitoramento do serviço de pesquisa para saber mais sobre a gama completa de recursos de supervisão.

> [!div class="nextstepaction"]
> [Monitorar operações e atividades no Azure Pesquisa Cognitiva](search-monitor-usage.md)