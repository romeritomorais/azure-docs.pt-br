---
title: Política de aprendizagem-personalizador
description: As configurações de aprendizado determinam os *hiperparâmetros* do treinamento do modelo. Dois modelos dos mesmos dados que são treinados em diferentes configurações de aprendizado acabarão sendo diferentes.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623791"
---
# <a name="learning-policy-and-settings"></a>Política de aprendizado e configurações

As configurações de aprendizado determinam os *hiperparâmetros* do treinamento do modelo. Dois modelos dos mesmos dados que são treinados em diferentes configurações de aprendizado acabarão sendo diferentes.

[As configurações e a política de aprendizado](how-to-settings.md#configure-rewards-for-the-feedback-loop) são definidas no recurso personalizado no portal do Azure.

## <a name="import-and-export-learning-policies"></a>Importar e exportar políticas de aprendizado

Você pode importar e exportar arquivos de política de aprendizagem do portal do Azure. Use esse método para salvar as políticas existentes, testá-las, substituí-las e arquivá-las no controle do código-fonte como artefatos para referência e auditoria futuras.

Saiba [como](how-to-manage-model.md#import-a-new-learning-policy) importar e exportar uma política de aprendizado no portal do Azure para o recurso personalizador.

## <a name="understand-learning-policy-settings"></a>Entender as configurações da política de aprendizagem

As configurações na política de aprendizado não devem ser alteradas. Altere as configurações somente se você entender como elas afetam o personalizador. Sem esse conhecimento, você pode causar problemas, incluindo a invalidação de modelos de personalização.

O personalizador usa [vowpalwabbit](https://github.com/VowpalWabbit) para treinar e pontuar os eventos. Consulte a [documentação do vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) sobre como editar as configurações de aprendizado usando o vowpalwabbit. Depois de ter os argumentos de linha de comando corretos, salve o comando em um arquivo com o seguinte formato (substitua o valor da propriedade arguments pelo comando desejado) e carregue o arquivo para importar as configurações de aprendizado no painel de **configurações de aprendizado e de modelo** no portal do Azure para o recurso personalizador.

O `.json` a seguir é um exemplo de uma política de aprendizado.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Comparar políticas de aprendizado

Você pode comparar o desempenho das diferentes políticas de aprendizagem em relação aos dados anteriores nos logs do personalizado, fazendo [avaliações offline](concepts-offline-evaluation.md).

[Carregue suas próprias políticas de aprendizado](how-to-manage-model.md) para compará-las com a política de aprendizado atual.

## <a name="optimize-learning-policies"></a>Otimizar políticas de aprendizado

O personalizador pode criar uma política de aprendizado otimizada em uma [avaliação offline](how-to-offline-evaluation.md). Uma política de aprendizado otimizada que tem recompensas melhores em uma avaliação offline produzirá resultados melhores quando for usada online no personalizador.

Depois de otimizar uma política de aprendizado, você pode aplicá-la diretamente ao personalizador para que ela substitua imediatamente a política atual. Ou você pode salvar a política otimizada para avaliação adicional e, posteriormente, decidir se deseja descartá-la, salvá-la ou aplicá-la.

## <a name="next-steps"></a>Próximas etapas

* Aprenda [eventos ativos e inativos](concept-active-inactive-events.md).
