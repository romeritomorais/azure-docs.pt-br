---
title: 'Início Rápido: Sintetizar uma fala em um arquivo de áudio, Python – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 4af31b281f4b2e7cdd7ed217753df55ce8009fa9
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445628"
---
## <a name="prerequisites"></a>Prerequisites

* Uma chave de assinatura do Azure para o serviço de Fala. [Obtenha uma gratuitamente](~/articles/cognitive-services/Speech-Service/get-started.md).
* [Python 3.5 ou posterior](https://www.python.org/downloads/).
* O pacote do SDK de Fala do Python está disponível para estes sistemas operacionais:
    * Windows: x64 e x86.
    * Mac: macOS X versão 10.12 ou mais recente.
    * Linux: Ubuntu 16.04, Ubuntu 18.04, Debian 9 em x64.
* No Linux, execute estes comandos para instalar os pacotes necessários:

  * No Ubuntu:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * No Debian 9:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* No Windows, é necessário ter os [Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Este comando instala o pacote Python do [PyPI](https://pypi.org/) para o SDK de Fala:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote Python do SDK de Fala são distribuídas por meio do PyPI e anunciadas no [Notas sobre a versão](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Se uma nova versão estiver disponível, você poderá atualizar para ela com o comando `pip install --upgrade azure-cognitiveservices-speech`.
Verifique qual versão está instalada no momento inspecionando a variável `azure.cognitiveservices.speech.__version__`.

Se você tiver um problema ou se estiver faltando um recurso, confira [Support and help options](~/articles/cognitive-services/Speech-Service/support.md) (Opções de Ajuda e Suporte).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Criar um aplicativo Python que usa o SDK de Fala

### <a name="run-the-sample"></a>Execute o exemplo

É possível copiar o [código de exemplo](#sample-code) neste início rápido para um arquivo de origem `quickstart.py` e executá-lo no IDE ou no console:

```sh
python quickstart.py
```

ou é possível baixar este tutorial de início rápido como um [Jupyter](https://jupyter.org) Notebook do [repositório de exemplos do SDK de Fala](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) e executá-lo como um notebook.

### <a name="sample-code"></a>Código de exemplo

````Python

import azure.cognitiveservices.speech as speechsdk

# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalar e usar o SDK de Fala com o Visual Studio Code

1. Baixe e instale uma versão de 64 bits do [Python](https://www.python.org/downloads/), 3.5 ou posterior, em seu computador.
1. Baixe e instale o [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra o Visual Studio Code e instale a extensão do Python. Selecione **Arquivo** > **Preferências** > **Extensões** no menu. Pesquise **Python**.

   ![Instalar a extensão do Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Crie uma pasta na qual armazenar o projeto. Um exemplo é fazer isso usando o Windows Explorer.
1. No Visual Studio Code, selecione o ícone **Arquivo**. Em seguida, abra a pasta criada.

   ![Abrir uma pasta](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Crie um novo arquivo de origem Python, `speechsdk.py`, clicando no ícone de novo arquivo.

   ![Criar um arquivo](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copie, cole e salve o [código do Python](#sample-code) para o arquivo recém-criado.
1. Insira as informações de assinatura do Serviço de Fala.
1. Se já tiver sido selecionado, um interpretador do Python será exibido no lado esquerdo da barra de status na parte inferior da janela.
   Caso contrário, exiba uma lista de interpretadores do Python disponíveis. Abra a paleta de comandos (Ctrl+Shift+P) e insira **Python: selecionar interpretador**. Escolha um adequado.
1. É possível instalar o pacote Python do SDK de Fala de dentro do Visual Studio Code. Faça isso se ele ainda não foi instalado para o interpretador do Python selecionado.
   Para instalar o pacote do SDK de Fala, abra um terminal. Exiba a paleta de comandos novamente (Ctrl+Shift+P) e insira **Terminal: Criar um Novo Terminal Integrado**.
   No terminal aberto, insira o comando `python -m pip install azure-cognitiveservices-speech` ou o comando apropriado para o seu sistema.
1. Para executar o código de exemplo, clique com o botão direito do mouse em algum lugar dentro do editor. Selecione **Executar arquivo Python no Terminal**.
   O texto é convertido em fala e salvo nos dados de áudio especificados.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

Se houver problemas em seguir estas instruções, consulte o [tutorial do Python do Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial) mais amplo.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Confira também

- [Criar uma Voz Personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizada](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
