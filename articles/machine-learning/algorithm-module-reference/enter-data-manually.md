---
title: 'Inserir dados manualmente: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo inserir dados manualmente em Azure Machine Learning para criar um pequeno conjunto digitando valores. O conjunto de linhas pode ter várias colunas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 949cdb4c53531a548380a6f0116fb45f91d7ba66
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268770"
---
# <a name="enter-data-manually-module"></a>Inserir dados manualmente no módulo

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para criar um pequeno conjunto de pequena digitando valores. O conjunto de linhas pode ter várias colunas.
  
Esse módulo pode ser útil em cenários como estes:  
  
- Gerando um pequeno conjunto de valores para teste  
  
- Criando uma lista curta de rótulos
  
- Digitar uma lista de nomes de coluna para inserir em um conjunto de dados

## <a name="enter-data-manually"></a>Inserir Dados Manualmente 
  
1.  Adicione o módulo [inserir dados manualmente](./enter-data-manually.md) ao seu pipeline. Você pode encontrar esse módulo na categoria **entrada e saída de dados** em Azure Machine Learning. 
  
2.  Em **formato de DataFormat**, selecione uma das opções a seguir. Essas opções determinam como os dados que você fornece devem ser analisados. Os requisitos para cada formato diferem muito, portanto, não deixe de ler os tópicos relacionados.  
  
    -   **ARFF**: o formato de arquivo de relação de atributo, usado pelo weka.   
  
    -   **CSV**: formato de valores separados por vírgula. Para obter mais informações, consulte [converter em CSV](./convert-to-csv.md).  
  
    -   **SVMLight**: um formato usado por Vowpal wabbit e outras estruturas de aprendizado de máquina.  
  
    -   **TSV**: formato de valores separados por tabulação.

     Se você escolher um formato e não fornecer dados que atendam às especificações de formato, ocorrerá um erro em tempo de execução.
  
3.  Clique dentro da caixa de texto **dados** para começar a inserir dados. Os formatos a seguir exigem atenção especial:  
  
    - **CSV**: para criar várias colunas, Cole o texto separado por vírgulas ou digite várias colunas usando vírgulas entre os campos.
  
        Se você selecionar a opção **HasHeader** , poderá usar a primeira linha de valores como o título de coluna.  
  
        Se você desmarcar essa opção, as colunas Names, Col1, Col2 e assim por diante serão usadas. Você pode adicionar ou alterar nomes de colunas posteriormente usando [Editar metadados](./edit-metadata.md).  
  
    - **TSV**: para criar várias colunas, cole em texto separado por tabulações ou digite várias colunas usando guias entre campos.  
  
        Se você selecionar a opção **HasHeader** , poderá usar a primeira linha de valores como o título de coluna.  
  
        Se você desmarcar essa opção, as colunas Names, Col1, Col2 e assim por diante serão usadas. Você pode adicionar ou alterar nomes de colunas posteriormente usando [Editar metadados](./edit-metadata.md).  
  
    -   **ARFF**: colar um arquivo de formato ARFF existente. Se você estiver digitando valores diretamente, certifique-se de adicionar o cabeçalho opcional e os campos de atributo necessários no início dos dados. 
    
        Por exemplo, as linhas de cabeçalho e de atributo a seguir podem ser adicionadas a uma lista simples. O título da coluna seria `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: digite ou cole valores usando o formato SVMLight.  
  
        Por exemplo, o exemplo a seguir representa as primeiras linhas do conjunto de linha de doação de sangue, no formato SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Quando você executa o módulo [inserir dados manualmente](./enter-data-manually.md) , essas linhas são convertidas em um conjunto de valores de colunas e de índice da seguinte maneira:  
  
        |Col1|Col2|Col3|Col4|Rótulos|  
        |-|-|-|-|-|  
        |0, 16|0, 4|0,999961|0, 784|1|  
        |0|0, 4|0,999955|0, 8615|1|  
  
4.  Pressione ENTER após cada linha para iniciar uma nova linha.      
     
     Se você pressionar ENTER várias vezes para adicionar várias linhas à direita vazias, as linhas vazias serão removidas.  
  
     Se você criar linhas com valores ausentes, você sempre poderá filtrá-los mais tarde.  
  
5.  Conecte a porta de saída a outros módulos e execute o pipeline.  
  
     Para exibir o conjunto de um, clique com o botão direito do mouse no módulo e selecione **Visualizar**.  
## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 