---
title: Configurar um serviço de QnA Maker-QnA Maker
description: Antes de criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro você deve configurar um serviço de QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos em uma assinatura pode configurar o serviço QnA Maker.
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 663cbce0e096c6189d97cf7872d466383d272f06
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650411"
---
# <a name="manage-qna-maker-resources"></a>Gerenciar QnA Maker recursos

Antes de criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro você deve configurar um serviço de QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos em uma assinatura pode configurar o serviço QnA Maker.

Uma compreensão sólida dos conceitos a seguir é útil antes de criar o recurso:

* [Recursos do QnA Maker](../Concepts/azure-resources.md)
* [Chaves de criação e publicação](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Criar um novo serviço do QnA Maker

Este procedimento cria os recursos do Azure necessários para gerenciar o conteúdo da base de dados de conhecimento. Depois de concluir essas etapas, você encontrará as chaves de _assinatura_ na página **chaves** do recurso no portal do Azure.

1. Entre no portal do Azure e [crie um recurso de QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Selecione **criar** depois de ler os termos e condições:

    ![Criar um novo serviço do QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Em **QnA Maker**, selecione as camadas e regiões apropriadas:

    ![Criar um serviço do QnA Maker – tipo de preço e regiões](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * No campo **nome** , insira um nome exclusivo para identificar este QnA Maker serviço. Esse nome também identifica o ponto de extremidade QnA Maker ao qual suas bases de dados de conhecimento serão associadas.
    * Escolha a **assinatura** sob a qual o recurso de QnA Maker será implantado.
    * Selecione o **tipo de preço** para os serviços de gerenciamento de QnA Maker (portal e APIs de gerenciamento). Veja [mais detalhes sobre os preços de SKU](https://aka.ms/qnamaker-pricing).
    * Crie um novo **grupo de recursos** (recomendado) ou use um existente para implantar esse QnA Maker recurso. QnA Maker cria vários recursos do Azure. Ao criar um grupo de recursos para manter esses recursos, você pode facilmente localizar, gerenciar e excluir esses recursos pelo nome do grupo de recursos.
    * Selecione um **local do grupo de recursos**.
    * Escolha o **tipo de preço de pesquisa** do serviço de pesquisa cognitiva do Azure. Se a opção camada gratuita não estiver disponível (aparece esmaecida), isso significa que você já tem um serviço gratuito implantado por meio de sua assinatura. Nesse caso, você precisará começar com a camada básica. Consulte [detalhes de preços do Azure pesquisa cognitiva](https://azure.microsoft.com/pricing/details/search/).
    * Escolha o **local de pesquisa** onde você deseja que os índices de pesquisa cognitiva do Azure sejam implantados. As restrições sobre onde os dados do cliente devem ser armazenados ajudarão a determinar o local escolhido para o Azure Pesquisa Cognitiva.
    * No campo **nome do aplicativo** , insira um nome para sua instância de serviço de Azure app.
    * Por padrão, o serviço de aplicativo assume como padrão a camada padrão (S1). Você pode alterar o plano após a criação. Saiba mais sobre os [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).
    * Escolha o **local do site** em que o serviço de aplicativo será implantado.

        > [!NOTE]
        > O **local de pesquisa** pode ser diferente do **local do site**.

    * Escolha se deseja ou não habilitar **Application insights**. Se o **Application Insights** estiver habilitado, o QnA Maker coletará a telemetria em tráfego, logs de chat e erros.
    * Escolha o **local do Application insights** no qual o recurso de Application insights será implantado.
    * Como medida de economia de custo, você pode [compartilhar](#configure-qna-maker-to-use-different-cognitive-search-resource) alguns, mas não todos os recursos do Azure criados para o QnA Maker.

1. Depois que todos os campos forem validados, selecione **criar**. O processo pode levar alguns minutos para ser concluído.

1. Após a conclusão da implantação, você verá os seguintes recursos criados em sua assinatura:

   ![O recurso criou um serviço do QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    O recurso com o tipo de _Serviços cognitivas_ tem suas chaves de _assinatura_ .

## <a name="find-subscription-keys-in-the-azure-portal"></a>Localizar chaves de assinatura no portal do Azure

Você pode exibir e redefinir suas chaves de assinatura do portal do Azure, em que você criou o recurso de QnA Maker.

1. Vá para o recurso de QnA Maker na portal do Azure e selecione o recurso que tem o tipo de _Serviços cognitivas_ :

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Vá para **as chaves**:

    ![Chave de assinatura](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Localizar chaves de ponto de extremidade no portal de QnA Maker

O ponto de extremidade está na mesma região que o recurso porque as chaves de ponto de extremidade são usadas para fazer uma chamada para a base de dados de conhecimento.

As chaves de ponto de extremidade podem ser gerenciadas a partir do [portal do QnA Maker](https://qnamaker.ai).

1. Entre no portal de [QnA Maker](https://qnamaker.ai), acesse seu perfil e, em seguida, selecione **configurações de serviço**:

    ![Chave do ponto de extremidade](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Exibir ou redefinir suas chaves:

    > [!div class="mx-imgBorder"]
    > ![o Gerenciador de chaves do ponto de extremidade](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Atualize suas chaves se você acreditar que elas foram comprometidas. Isso pode exigir que sejam feitas as alterações correspondentes no seu aplicativo cliente ou código bot.

### <a name="upgrade-qna-maker-sku"></a>Atualizar QnA Maker SKU

Quando você quiser ter mais perguntas e respostas em sua base de dados de conhecimento, além da sua camada atual, atualize seu tipo de preço do QnA Maker Service.

Para fazer upgrade da SKU de gerenciamento do QnA Maker:

1. Vá para o recurso QnA Maker no portal do Azure e selecione **Camada de preços**.

    ![Recurso do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Escolha o SKU apropriado e pressione **Selecionar**.

    ![Preços do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Atualizar serviço de aplicativo

 Quando sua base de dados de conhecimento precisar atender a mais solicitações de seu aplicativo cliente, atualize seu tipo de preço do serviço de aplicativo.

Você pode [escalar verticalmente](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou escalar horizontalmente o serviço de aplicativo.

Vá para o recurso serviço de aplicativo no portal do Azure e selecione a opção **escalar verticalmente** ou **escalar** horizontalmente, conforme necessário.

![Escala do serviço de aplicativo QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Atualizar o serviço de Pesquisa Cognitiva do Azure

Se você planeja ter muitas bases de dados de conhecimento, atualize seu tipo de preço do serviço Pesquisa Cognitiva do Azure.

No momento, não é possível realizar uma atualização in-loco da SKU do Azure Search. No entanto, pode criar um novo recurso de pesquisa do Azure com o SKU desejado, restaurar os dados para o novo recurso e vinculá-lo com a pilha do QnA Maker. Para fazer isso, siga estas etapas:

1. Crie um novo recurso de Azure Search no portal do Azure e selecione o SKU desejado.

    ![Recurso de pesquisa do Azure do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaure os índices de seu recurso original da pesquisa do Azure para o novo. Consulte o [código de exemplo de restauração de backup](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Depois que os dados forem restaurados, vá para o novo recurso do Azure Search, selecione **chaves**e anote o **nome** e a **chave de administração**:

    ![Chaves de pesquisa do QnA Maker Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Para vincular o novo recurso de Azure Search à pilha de QnA Maker, vá para a instância do serviço de aplicativo QnA Maker.

    ![Instância do serviço de aplicativo QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecione **configurações do aplicativo** e modifique as configurações nos campos **AzureSearchName** e **AzureSearchAdminKey** da etapa 3.

    ![QnA Maker configuração do serviço de aplicativo](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Reinicie a instância do serviço de aplicativo.

    ![Reinicialização da instância do serviço de aplicativo QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Obter as atualizações de tempo de execução mais recentes

O tempo de execução do QnAMaker faz parte da instância do serviço de Azure App que é implantada quando você [cria um serviço QnAMaker](./set-up-qnamaker-service-azure.md) no portal do Azure. Atualizações são feitas periodicamente para o runtime. A instância do serviço de aplicativo QnA Maker está no modo de atualização automática após a versão da extensão do site de abril de 2019 (versão 5 +). Essa atualização foi projetada para cuidar do tempo de inatividade ZERO durante as atualizações.

Você pode verificar sua versão atual em https://www.qnamaker.ai/UserSettings. Se sua versão for anterior à versão 5. x, você deverá reiniciar o serviço de aplicativo para aplicar as atualizações mais recentes:

1. Vá para o serviço QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![o grupo de recursos QnAMaker do Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione a instância do serviço de aplicativo e abra a seção **visão geral** .

    > [!div class="mx-imgBorder"]
    > ![instância do serviço de aplicativo do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Reinicie o serviço de aplicativo. O processo de atualização deve ser concluído em alguns segundos. Quaisquer aplicativos dependentes ou bots que usam esse serviço QnAMaker não estarão disponíveis para os usuários finais durante esse período de reinicialização.

    ![Reinicialização da instância do serviço de aplicativo QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Configurar QnA Maker para usar diferentes recursos de Pesquisa Cognitiva

Se você criar um serviço QnA e suas dependências (como pesquisa) por meio do portal, um serviço de pesquisa será criado para você e vinculado ao serviço de QnA Maker. Depois que esses recursos forem criados, você poderá atualizar a configuração do serviço de aplicativo para usar um serviço de pesquisa existente anteriormente e remover o que você acabou de criar.

O recurso de **serviço de aplicativo** do QnA Maker usa o recurso pesquisa cognitiva. Para alterar o recurso de Pesquisa Cognitiva usado pelo QnA Maker, você precisa alterar a configuração no portal do Azure.

1. Obtenha a **chave de administração** e o **nome** do pesquisa cognitiva recurso que você deseja que QnA Maker use.

1. Entre no [portal do Azure](https://portal.azure.com) e localize o serviço de **aplicativo** associado ao recurso de QnA Maker. Ambos têm o mesmo nome.

1. Selecione **configurações**e **configuração**. Isso exibirá todas as configurações existentes para o serviço de aplicativo do QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![captura de tela do portal do Azure mostrando as definições de configuração do serviço de aplicativo](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Altere os valores para as seguintes chaves:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Para usar as novas configurações, você precisa reiniciar o serviço de aplicativo. Selecione **visão geral**e, em seguida, selecione **reiniciar**.

    > [!div class="mx-imgBorder"]
    > ![captura de tela de portal do Azure reiniciando o serviço de aplicativo após a alteração das definições de configuração](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Se você criar um serviço QnA por meio de modelos de Azure Resource Manager, poderá criar todos os recursos e controlar a criação do serviço de aplicativo para usar um serviço de pesquisa existente.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o serviço de [aplicativo](../../../app-service/index.yml) e o [serviço de pesquisa](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Criar e publicar uma base de conhecimento](../Quickstarts/create-publish-knowledge-base.md)