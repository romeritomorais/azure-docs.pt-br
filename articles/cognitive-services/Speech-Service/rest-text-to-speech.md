---
title: REST (referência de API de conversão de texto em fala) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API REST de conversão de texto em fala. Neste artigo, você aprenderá sobre opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: ab0891653f449b13f50dc43b196cf16a2f71370e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975815"
---
# <a name="text-to-speech-rest-api"></a>API REST conversão de texto em fala

O serviço de fala permite que você [converta o texto em fala sintetizada](#convert-text-to-speech) e [obtenha uma lista de vozes com suporte](#get-a-list-of-voices) para uma região usando um conjunto de APIs REST. Cada ponto de extremidade disponível é associado a uma região. É necessária uma chave de assinatura para o ponto de extremidade/região que você planeja usar.

A API REST de conversão de texto em fala é compatível com vozes neurais e padrão de conversão de texto em fala, cada uma delas compatível com um idioma e dialeto específicos, identificados pela localidade.

* Para obter uma lista completa de vozes, consulte [suporte para idioma](language-support.md#text-to-speech).
* Para obter informações sobre a disponibilidade regional, consulte [regiões](regions.md#text-to-speech).

> [!IMPORTANT]
> Os custos variam para vozes padrão, personalizadas e neurais. Para saber mais, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Antes de usar essa API, entenda:

* A API REST de conversão de texto em voz requer um cabeçalho de autorização. Isso significa que você precisa concluir uma troca de tokens para acessar o serviço. Para obter mais informações, consulte [Autenticação](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Obter uma lista de vozes

O ponto de extremidade `voices/list` permite obter uma lista completa de vozes para uma região/um ponto de extremidade específico.

### <a name="regions-and-endpoints"></a>Regiões e endpoints

| Região | Ponto de extremidade |
|--------|----------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sul do Brasil | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| EUA Central | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Leste da Ásia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Leste dos EUA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Leste dos EUA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| França Central | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Centro da Índia | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Leste do Japão | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Coreia Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Centro-Norte dos EUA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa Setentrional | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Centro-Sul dos EUA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Oeste da Europa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Oeste dos EUA | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Oeste dos EUA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Cabeçalhos da solicitação

Esta tabela lista os cabeçalhos obrigatórios e opcionais para solicitações de conversão de texto em fala.

| Cabeçalho | Descrição | Obrigatório/Opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, consulte [Autenticação](#authentication). | obrigatórios |

### <a name="request-body"></a>Corpo da solicitação

Um corpo não é necessário para solicitações de `GET` para esse ponto de extremidade.

### <a name="sample-request"></a>Solicitação de exemplo

Essa solicitação requer apenas um cabeçalho de autorização.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Resposta de exemplo

Essa resposta foi truncada para ilustrar a estrutura de uma resposta.

> [!NOTE]
> A disponibilidade de voz varia por região/ponto de extremidade.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica sucesso ou erros comuns.

| Código de status HTTP | Descrição | Possível motivo |
|------------------|-------------|-----------------|
| 200 | OK | A solicitação foi bem-sucedida. |
| 400 | Solicitação incorreta | Um parâmetro obrigatório está ausente, vazio ou nulo. Ou então, o valor passado como um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho que é muito longo. |
| 401 | Não autorizado | A solicitação não foi autorizada. Verifique se a chave de assinatura ou o token são válidos e se estão na região correta. |
| 429 | Número Excessivo de Solicitações | Você excedeu a cota ou a taxa de solicitações permitidas para a sua assinatura. |
| 502 | Gateway incorreto | Problema de rede ou do servidor. Também pode indicar cabeçalhos inválidos. |


## <a name="convert-text-to-speech"></a>Converter texto em fala

O ponto de extremidade `v1` permite que você converta conversão de texto em fala usando a [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regiões e endpoints

Essas regiões são suportadas para text-to-speech usando a API REST. Certifique-se de selecionar o terminal que corresponde à sua região de assinatura.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Cabeçalhos da solicitação

Esta tabela lista os cabeçalhos obrigatórios e opcionais para solicitações de conversão de texto em fala.

| Cabeçalho | Descrição | Obrigatório/Opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, consulte [Autenticação](#authentication). | obrigatórios |
| `Content-Type` | Especifica o tipo de conteúdo para o texto fornecido. Aceita o valor: `application/ssml+xml`. | obrigatórios |
| `X-Microsoft-OutputFormat` | Especifica o formato de saída de áudio. Para obter uma lista completa dos valores aceitos, consulte [saídas de áudio](#audio-outputs). | obrigatórios |
| `User-Agent` | O nome do aplicativo. O valor fornecido deve ter menos de 255 caracteres. | obrigatórios |

### <a name="audio-outputs"></a>Saídas de áudio

Esta é uma lista de formatos de áudio suportados que são enviados em cada solicitação como o cabeçalho `X-Microsoft-OutputFormat`. Cada um incorpora um tipo de taxa de bits e codificação. O serviço de fala dá suporte a saídas de áudio de 24 kHz, 16 kHz e 8 kHz.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Se sua voz selecionada e o formato de saída tiverem diferentes taxas de bits, o áudio é aumentado conforme necessário. No entanto, as vozes de 24 kHz não dão suporte a `audio-16khz-16kbps-mono-siren` e `riff-16khz-16kbps-mono-siren` formatos de saída.

### <a name="request-body"></a>Corpo da solicitação

O corpo de cada solicitação `POST` é enviada como [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md). A SSML permite que você escolha o idioma e a voz da fala sintetizada retornada pelo serviço de conversão de texto em fala. Para obter uma lista completa de vozes compatíveis, confira [suporte para idioma](language-support.md#text-to-speech).

> [!NOTE]
> Se usar uma voz personalizada, o corpo de uma solicitação poderá ser enviado como texto sem formatação (ASCII ou UTF-8).

### <a name="sample-request"></a>Solicitação de exemplo

Esta solicitação HTTP usa SSML para especificar a voz e o idioma. O corpo não pode exceder 1.000 caracteres.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-JessaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Consulte nossos guias de início rápido para obter exemplos específicos de idioma:

* [.NET Core, C#](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica sucesso ou erros comuns.

| Código de status HTTP | Descrição | Possível motivo |
|------------------|-------------|-----------------|
| 200 | OK | A solicitação foi bem-sucedida. O corpo da resposta é um arquivo de áudio. |
| 400 | Solicitação incorreta | Um parâmetro obrigatório está ausente, vazio ou nulo. Ou então, o valor passado como um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho que é muito longo. |
| 401 | Não autorizado | A solicitação não foi autorizada. Verifique se a chave de assinatura ou o token são válidos e se estão na região correta. |
| 413 | Entidade de solicitação muito grande | A entrada de SSML tem mais de 1024 caracteres. |
| 415 | Tipo de Mídia Sem Suporte | É possível que o `Content-Type` errado seja fornecido. `Content-Type` deve ser definido como `application/ssml+xml`. |
| 429 | Número Excessivo de Solicitações | Você excedeu a cota ou a taxa de solicitações permitidas para a sua assinatura. |
| 502 | Gateway incorreto | Problema de rede ou do servidor. Também pode indicar cabeçalhos inválidos. |

Se o status HTTP for `200 OK`, o corpo da resposta conterá um arquivo de áudio no formato solicitado. Este arquivo pode ser reproduzido enquanto é transferido, salvo em um buffer ou salvo em um arquivo.

## <a name="next-steps"></a>Próximos passos

- [Obter a assinatura de avaliação do Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
