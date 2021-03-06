---
title: Integração de controle de origem na Automação do Azure
description: Este artigo descreve a integração de controle de origem com o GitHub na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: eef67ca8111983adb4d9994894ba215240daee6f
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253742"
---
# <a name="source-control-integration-in-azure-automation"></a>Integração de controle de origem na Automação do Azure

 A integração de controle do código-fonte na automação do Azure dá suporte à sincronização de direção única do repositório do controle do código-fonte O controle do código-fonte permite que você mantenha seus runbooks em sua conta de automação atualizados com scripts em seu GitHub ou Azure Repos repositório de controle do código-fonte. Esse recurso facilita a promoção de código que foi testado em seu ambiente de desenvolvimento para sua conta de automação de produção.
 
 Usando a integração de controle do código-fonte, você pode colaborar facilmente com sua equipe, controlar alterações e reverter para versões anteriores de seus runbooks. Por exemplo, o controle do código-fonte permite que você sincronize diferentes branches no controle do código-fonte com suas contas de desenvolvimento, teste e automação de produção. 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Tipos de controle do código-fonte

A automação do Azure dá suporte a três tipos de controle do código-fonte:

* GitHub
* Azure Repos (git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Prerequisites

* Um repositório de controle do código-fonte (GitHub ou Azure Repos)
* Uma [conta Executar como](manage-runas-account.md)
* Os [módulos do Azure mais recentes](automation-update-azure-modules.md) em sua conta de automação, incluindo o módulo **AZ. Accounts** (módulo AZ equivalente de AzureRM. Profile)

> [!NOTE]
> Os trabalhos de sincronização de controle do código-fonte são executados sob a conta de automação do usuário e são cobrados com a mesma taxa de outros trabalhos de automação.

## <a name="configuring-source-control"></a>Configurando controle do código-fonte

Esta seção informa como configurar o controle do código-fonte para sua conta de automação. Você pode usar o portal do Azure ou o PowerShell.

### <a name="configure-source-control----azure-portal"></a>Configurar controle do código-fonte-portal do Azure

Use este procedimento para configurar o controle do código-fonte usando o portal do Azure.

1. Em sua conta de automação, selecione **controle do código-fonte** e clique em **+ Adicionar**.

    ![Selecionar controle do código-fonte](./media/source-control-integration/select-source-control.png)

2. Escolha **tipo de controle do código-fonte**e clique em **autenticar**. 

3. Uma janela do navegador é aberta e solicita que você entre. Siga os prompts para concluir a autenticação.

4. Na página **Resumo do controle do código-fonte** , use os campos para preencher as propriedades de controle do código-fonte definidas abaixo. Clique em **Salvar** ao terminar. 

    |Propriedade  |DESCRIÇÃO  |
    |---------|---------|
    |Nome do controle do código-fonte     | Um nome amigável para o controle do código-fonte. Esse nome deve conter apenas letras e números.        |
    |Tipo de controle do código-fonte     | Tipo de mecanismo de controle do código-fonte. As opções disponíveis são:</br> GitHub</br>Azure Repos (git)</br> Azure Repos (TFVC)        |
    |Repositório     | Nome do repositório ou do projeto. Os primeiros 200 repositórios são recuperados. Para pesquisar um repositório, digite o nome no campo e clique em **Pesquisar no GitHub**.|
    |Branch     | Ramificação da qual os arquivos de origem são extraídos. O direcionamento de Branch não está disponível para o tipo de controle do código-fonte TFVC.          |
    |Caminho da pasta     | Pasta que contém os runbooks a serem sincronizados, por exemplo,/Runbooks. Somente runbooks na pasta especificada são sincronizados. Não há suporte para recursão.        |
    |Sincronização automática<sup>1</sup>     | Configuração que ativa ou desativa a sincronização automática quando uma confirmação é feita no repositório do controle do código-fonte.        |
    |Publicar runbook     | Configuração de on If os runbooks são publicados automaticamente após a sincronização do controle do código-fonte e fora do contrário.           |
    |DESCRIÇÃO     | Texto que especifica detalhes adicionais sobre o controle do código-fonte.        |

    <sup>1</sup> para habilitar a sincronização automática ao configurar a integração do controle do código-fonte com o Azure Repos, você deve ser um administrador do projeto.

   ![Resumo do controle do código-fonte](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Seu logon para o repositório do controle do código-fonte pode ser diferente do seu logon para o portal do Azure. Verifique se você está conectado com a conta correta para seu repositório de controle do código-fonte ao configurar o controle do código-fonte. Se houver dúvida, abra uma nova guia no seu navegador, faça logoff de visualstudio.com ou github.com e tente se conectar ao controle do código-fonte novamente.

### <a name="configure-source-control----powershell"></a>Configurar controle do código-fonte-PowerShell

Você também pode usar o PowerShell para configurar o controle do código-fonte na automação do Azure. Para usar os cmdlets do PowerShell para esta operação, você precisa de um PAT (token de acesso pessoal). Use o cmdlet [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) para criar a conexão de controle do código-fonte. Este cmdlet requer uma cadeia de caracteres segura para o PAT. Para saber como criar uma cadeia de caracteres segura, consulte [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

As subseções a seguir ilustram a criação do PowerShell de conexão de controle do código-fonte para GitHub, Azure Repos (git) e Azure Repos (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Criar conexão de controle do código-fonte para o GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Criar conexão de controle do código-fonte para Azure Repos (git)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Criar conexão de controle do código-fonte para Azure Repos (TFVC)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Permissões de token de acesso pessoal (PAT)

O controle do código-fonte requer algumas permissões mínimas para PATs. As subseções a seguir contêm as permissões mínimas necessárias para o GitHub e o Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Mínimo de permissões PAT para o GitHub

A tabela a seguir define as permissões mínimas do PAT necessárias para o GitHub. Para obter mais informações sobre como criar uma PAT no GitHub, consulte [criando um token de acesso pessoal para a linha de comando](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Escopo  |DESCRIÇÃO  |
|---------|---------|
|**repositório**     |         |
|repo:status     | Acessar status de confirmação         |
|repo_deployment      | Acessar status de implantação         |
|public_repo     | Repositórios públicos de acesso         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Escrever ganchos de repositório         |
|read:repo_hook|Ler ganchos de repositório|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Mínimo de permissões PAT para Azure Repos

A lista a seguir define as permissões mínimas do PAT necessárias para Azure Repos. Para obter mais informações sobre como criar uma PAT no Azure Repos, consulte [autenticar o acesso com tokens de acesso pessoal](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Escopo  |  Tipo de acesso  |
|---------| ----------|
| Código      | Ler  |
| Projeto e equipe | Ler |
| Identidade | Ler     |
| Perfil de Usuário | Ler     |
| Itens de trabalho | Ler    |
| Conexões de serviço | Ler, consultar, gerenciar<sup>1</sup>    |

<sup>1</sup> a permissão de conexões de serviço só será necessária se você tiver habilitado o AutoSync.

## <a name="synchronizing"></a>Sincronizando

Faça o seguinte para sincronizar com o controle do código-fonte. 

1. Selecione a origem da tabela na página **controle do código-fonte** . 

2. Clique em **Iniciar sincronização** para iniciar o processo de sincronização. 

3. Exiba o status do trabalho de sincronização atual ou os anteriores clicando na guia **trabalhos de sincronização** . 

4. No menu suspenso **controle do código-fonte** , selecione um mecanismo de controle do código-fonte.

    ![Status da sincronização](./media/source-control-integration/sync-status.png)

5. Clicar em um trabalho permite exibir a saída do trabalho. O exemplo a seguir é a saída de um trabalho de sincronização do controle do código-fonte.

    ```output
    ============================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzureRmEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

     =========================================================================

    ```

6. O registro em log adicional está disponível selecionando **todos os logs** na página Resumo do trabalho de sincronização de **controle do código-fonte** . Essas entradas de log adicionais podem ajudá-lo a solucionar problemas que podem surgir ao usar o controle do código-fonte.

## <a name="disconnecting-source-control"></a>Desconectando o controle de origem

Para desconectar-se de um repositório de controle do código-fonte:

1. **Controle do código-fonte** aberto em **configurações de conta** em sua conta de automação.

2. Selecione o mecanismo de controle do código-fonte a ser removido. 

3. Na página **Resumo do controle do código-fonte**, clique em **Excluir**.

## <a name="handling-encoding-issues"></a>Lidando com problemas de codificação

Se várias pessoas estiverem editando runbooks em seu repositório de controle do código-fonte usando diferentes editores, poderão ocorrer problemas de codificação. Para saber mais sobre essa situação, confira [causas comuns de problemas de codificação](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-pat"></a>Atualizando o PAT

Atualmente, não há como usar o portal do Azure para atualizar o PAT no controle do código-fonte. Depois que sua PAT expirou ou foi revogada, você pode atualizar o controle do código-fonte com um novo token de acesso de uma das seguintes maneiras:

* Use a [API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Use o cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre tipos de runbook e suas vantagens e limitações, confira [tipos de runbook de automação do Azure](automation-runbook-types.md).
