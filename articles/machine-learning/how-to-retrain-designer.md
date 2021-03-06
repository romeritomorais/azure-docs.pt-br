---
title: Treinar novamente os modelos usando o designer de Azure Machine Learning (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como treinar novamente modelos com pipelines publicados no designer de Azure Machine Learning (versão prévia).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 02/24/2020
ms.openlocfilehash: 8e7874ec2a0ea160d29f8755ca8680c4dfbeec1d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268495"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Treinar novamente modelos com o designer de Azure Machine Learning (versão prévia)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste "como", você aprenderá a usar Azure Machine Learning designer para treinar novamente um modelo de aprendizado de máquina. Descubra como usar pipelines publicados para automatizar fluxos de trabalho de aprendizado de máquina para readaptação.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Treinar um modelo de machine learning.
> * Crie um parâmetro de pipeline.
> * Publique seu pipeline de treinamento.
> * Readaptação de seu modelo.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Um espaço de trabalho Azure Machine Learning com o SKU corporativo.

Este "como" pressupõe que você tenha conhecimento básico da criação de pipelines no designer. Para obter uma introdução guiada ao designer, conclua o [tutorial](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Amostra do pipeline

O pipeline usado neste artigo é uma versão alterada do encontrada no [exemplo de previsão de 3 rendas](how-to-designer-sample-classification-predict-income.md). Ele usa o módulo [importar dados](algorithm-module-reference/import-data.md) em vez do conjunto de exemplo para mostrar como treinar um modelo usando seus próprios dados.

![Captura de tela mostrando o pipeline de exemplo modificado com uma caixa realçando o módulo importar dados](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Treinar um modelo de machine learning

Para treinar novamente um modelo, você precisa de um modelo inicial. Nesta seção, você aprenderá a treinar um modelo e acessar o modelo salvo usando o designer.

1. Selecione o módulo **importar dados** .
1. No painel Propriedades, especifique uma fonte de dados.

    ![Captura de tela mostrando um exemplo de configuração do módulo importar dados](./media/how-to-retrain-designer/import-data-settings.png)

    Para este exemplo, os dados são armazenados em um [Azure datastore](how-to-access-data.md). Se você ainda não tiver um repositório de armazenamento, poderá criar um agora selecionando **novo repositório de armazenamento**.

1. Especifique o caminho para seus dados. Você também pode selecionar **procurar caminho** para navegar visualmente no seu repositório de armazenamento. 

1. Selecione **executar** na parte superior da tela.
    
    > [!NOTE]
    > Se você já tiver definido uma computação padrão para esse rascunho de pipeline, o pipeline será executado automaticamente. Caso contrário, você pode seguir os prompts no painel configurações que aparece para definir um agora.

### <a name="locate-your-trained-model"></a>Localize seu modelo treinado

O designer salva todas as saídas de pipeline, incluindo modelos treinados, para a conta de armazenamento padrão. Você também pode acessar o modelo treinado diretamente no designer:

1. Aguarde até que o pipeline termine a execução.

1. Selecione o módulo **Treinar Modelo**.

1. No painel configurações, selecione **saídas + logs**.

1. Clique no ícone **Exibir saída** e siga as instruções na janela pop-up para localizar o modelo treinado.

![Captura de tela mostrando como baixar o modelo treinado](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Criar um parâmetro de pipeline

Adicione parâmetros de pipeline para definir variáveis dinamicamente em tempo de execução. Para este pipeline, adicione um parâmetro para o caminho de dados de treinamento para que você possa treinar novamente seu modelo em um novo conjunto.

1. Selecione o módulo **importar dados** .
1. No painel configurações, selecione as reticências acima do campo **caminho** .
1. Selecione **Adicionar ao parâmetro de pipeline**.
1. Forneça um nome de parâmetro e um valor padrão.

    > [!NOTE]
    > Você pode inspecionar e editar os parâmetros de pipeline selecionando o **ícone de engrenagem configurações** ao lado do título do rascunho do seu pipeline. 

![Captura de tela mostrando como criar um parâmetro de pipeline](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publicar um pipeline de treinamento

Quando você publica um pipeline, ele cria um ponto de extremidade de pipeline. Os pontos de extremidade do pipeline permitem que você reutilize e gerencie seus pipelines para repetição e automação. Neste exemplo, você configurou seu pipeline para treinar novamente.

1. Selecione **publicar** acima da tela do designer.
1. Selecione, ou crie, um novo ponto de extremidade de pipeline.

    > [!NOTE]
    > Você pode publicar vários pipelines em um único ponto de extremidade. Cada pipeline no ponto de extremidade recebe um número de versão, que você pode especificar ao chamar o ponto de extremidade do pipeline.

1. Selecione **Publicar**.

## <a name="retrain-your-model"></a>Treinar novamente seu modelo

Agora que você tem um pipeline de treinamento publicado, você pode usá-lo para treinar novamente seu modelo usando novos dados. Você pode enviar execuções de um ponto de extremidade de pipeline de um portal ou de forma programática.

### <a name="submit-runs-with-the-designer"></a>Enviar execuções com o designer

Use as etapas a seguir para enviar uma execução de ponto de extremidade de pipeline do designer:

1. Vá para a página **pontos de extremidade** .

1. Selecione a guia **pontos de extremidade do pipeline** .

1. Selecione o ponto de extremidade do pipeline.

1. Selecione a guia **pipelines publicados** .

1. Selecione o pipeline que você deseja executar.

1. Selecione **Enviar**.

1. Na caixa de diálogo de instalação, você pode especificar um novo valor de caminho de dados de entrada, que aponta para o novo conjunto.

![Captura de tela mostrando como configurar uma execução de pipeline com parâmetros no designer](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-with-code"></a>Enviar execuções com código

Você pode encontrar o ponto de extremidade REST de um pipeline publicado no painel Visão geral. Ao chamar o ponto de extremidade, você pode treinar novamente o pipeline publicado.

Para fazer uma chamada REST, você precisará de um cabeçalho de autenticação do tipo portador OAuth 2,0. Confira a [seção do tutorial](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) a seguir para obter mais detalhes sobre como configurar a autenticação para seu workspace e realizar uma chamada REST.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Siga o [tutorial](tutorial-designer-automobile-price-train-score.md) do designer para treinar e implantar um modelo de regressão.
