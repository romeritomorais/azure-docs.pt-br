---
title: 'Início Rápido: Biblioteca de clientes da Análise de Texto v3 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use este início rápido para conectar seus aplicativos à API de Análise de Texto por meio dos Serviços Cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/24/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: c6883b9b79adfc4f91af354147d45ea27d806d78
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614489"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Início Rápido: Usar a biblioteca de clientes da Análise de Texto

Introdução à biblioteca de clientes da Análise de Texto. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas.

Use a biblioteca de clientes da Análise de Texto para executar:

* Análise de sentimento
* Detecção de idioma
* Reconhecimento de entidade
* Extração de frases-chave

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * A última versão prévia da API de Análise de Texto é `3.0-preview`, que inclui uma versão prévia pública para a [Análise de Sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e o [NER (reconhecimento de entidade nomeada)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) aprimorados. A última versão estável é `2.1`.
>    * Lembre-se de seguir as instruções para a versão que você está usando.
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Para cenários de produção, recomendamos usar os métodos assíncronos em lote para desempenho e escalabilidade. Confira a documentação de referência abaixo.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Este início rápido destina-se somente à versão `3.0-preview` da biblioteca de clientes da Análise de Texto, que inclui uma versão prévia pública para a [Análise de Sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e o [NER (reconhecimento de entidade nomeada)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) aprimorados.
>    * Lembre-se de seguir as instruções para a versão que você está usando.
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Para cenários de produção, recomendamos usar os métodos assíncronos em lote para desempenho e escalabilidade. Confira a documentação de referência abaixo.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * A última versão prévia da API de Análise de Texto é `3.0-preview`, que inclui uma versão prévia pública para a [Análise de Sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e o [NER (reconhecimento de entidade nomeada)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) aprimorados. A última versão estável é `2.1`.
>    * Lembre-se de seguir as instruções para a versão que você está usando.
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Para cenários de produção, recomendamos usar os métodos assíncronos em lote para desempenho e escalabilidade. Confira a documentação de referência abaixo.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * A última versão prévia da API de Análise de Texto é `3.0-preview`, que inclui uma versão prévia pública para a [Análise de Sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e o [NER (reconhecimento de entidade nomeada)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) aprimorados. A última versão estável é `2.1`.
>    * Lembre-se de seguir as instruções para a versão que você está usando.
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Para cenários de produção, recomendamos usar os métodos assíncronos em lote para desempenho e escalabilidade. Confira a documentação de referência abaixo. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Suporte a idiomas adicionais

Se você clicou nessa guia, provavelmente não viu um início rápido em sua linguagem de programação favorita. Não se preocupe. Temos inícios rápidos adicionais disponíveis. Use a tabela para encontrar a amostra correta para a sua linguagem de programação.

| Linguagem | Versão disponível | 
|----------|------------------------|
| Ruby     | [Versão 2.1](ruby-sdk.md) | 
| Go       | [Versão 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar uma solução](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Visão geral da Análise de Texto](../overview.md)
* [Análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidade](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detectar o idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento de idioma](../how-tos/text-analytics-how-to-language-detection.md)
