---
title: Solucionar erros com recursos compartilhados do Automação do Azure
description: Saiba como solucionar problemas e resolver questões com recursos compartilhados de automação do Azure que dão suporte a runbooks.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769856"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Solucionar erros com recursos compartilhados

Este artigo discute soluções para resolver problemas que podem ser encontrados ao usar os recursos compartilhados na Automação do Azure.

## <a name="modules"></a>Módulos

### <a name="module-stuck-importing"></a>Cenário: Um módulo está paralisado importando

#### <a name="issue"></a>Problema

Um módulo está paralisado no estado **Importando** quando você importa ou atualiza os módulos na automação do Azure.

#### <a name="cause"></a>Causa

A importação de módulos do PowerShell é um processo complexo de várias etapas. Este processo introduz a possibilidade de um módulo não importar corretamente. Se esse problema ocorrer, o módulo que você está importando pode estar paralisado em um estado transitório. Para saber mais sobre esse processo, consulte [importação de um módulo do PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Resolução

Para resolver esse problema, você deve remover o módulo que está emperrado no estado **Importando** usando o cmdlet [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule). Você pode, em seguida, tente importar novamente o módulo.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Cenário: os módulos AzureRM estão presos na importação depois de tentar atualizá-los

#### <a name="issue"></a>Problema

Uma faixa com a seguinte mensagem permanece em sua conta depois de tentar atualizar os módulos do AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Causa

Há um problema conhecido com a atualização dos módulos AzureRM em uma conta de automação que está em um grupo de recursos com um nome numérico que começa com 0.

#### <a name="resolution"></a>Resolução

Para atualizar seus módulos do Azure em sua conta de automação, ele deve estar em um grupo de recursos que tenha um nome alfanumérico. Os grupos de recursos com nomes numéricos que começam com 0 não podem atualizar os módulos AzureRM no momento.

### <a name="module-fails-to-import"></a>Cenário: Falha de módulo importar ou cmdlets não pode ser executados após a importação

#### <a name="issue"></a>Problema

Falha ao importar de um módulo ou importa com êxito, mas nenhum cmdlets são extraídos.

#### <a name="cause"></a>Causa

Algumas razões comuns para que um módulo não pode importar com êxito à automação do Azure são:

* A estrutura não corresponde à estrutura em que a automação precisa estar.
* O módulo depende de outro módulo que não tenha sido implantado em sua conta de Automação.
* O módulo não tem suas dependências na pasta.
* O cmdlet `New-AzureRmAutomationModule` está sendo usado para carregar o módulo e você não forneceu o caminho de armazenamento completo ou não carregou o módulo usando uma URL publicamente acessível.

#### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Verifique se o módulo segue o seguinte formato: ModuleName.Zip **->** ModuleName ou Número de versão **->** (ModuleName.psm1, ModuleName.psd1)
* Abra o arquivo .psd1 e veja se o módulo tem dependências. Se tiver, carregue esses módulos para a conta de Automação.
* Verifique se quaisquer .dlls referenciadas estão presentes na pasta do módulo.

### <a name="all-modules-suspended"></a>Cenário: Update-AzureModule. ps1 suspende ao atualizar módulos

#### <a name="issue"></a>Problema

Ao usar o runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) para atualizar os módulos do Azure, o processo de atualização de módulo é suspenso.

#### <a name="cause"></a>Causa

A configuração padrão para determinar quantos módulos são atualizados simultaneamente é 10 ao usar o script `Update-AzureModule.ps1`. O processo de atualização é propenso a erros quando muitos módulos estão sendo atualizados ao mesmo tempo.

#### <a name="resolution"></a>Resolução

Não é comum todos os módulos AzureRM serem necessários na mesma conta de automação. É recomendável importar apenas os módulos AzureRM que você precisa.

> [!NOTE]
> Evite importar o módulo **AzureRM**. Importar os módulos **AzureRM** faz com que todos os módulos **AzureRM.\*** sejam importados, e isso não é recomendado.

Se o processo de atualização ficar suspenso, você precisará adicionar o parâmetro `SimultaneousModuleImportJobCount`ao script `Update-AzureModules.ps1` e fornecer um valor inferior ao padrão, que é 10. Caso implemente essa lógica, é recomendável iniciar com um valor de 3 ou 5. O `SimultaneousModuleImportJobCount` é um parâmetro do runbook do sistema `Update-AutomationAzureModulesForAccount` que é usado para atualizar módulos do Azure. Essa alteração torna a execução do processo mais demorada, mas tem mais chances de ser concluído. O exemplo a seguir mostra o parâmetro e onde colocá-lo no runbook:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Contas Executar como

### <a name="unable-create-update"></a>Cenário: não é possível criar ou atualizar uma conta Executar como

#### <a name="issue"></a>Problema

Ao tentar criar ou atualizar uma conta Executar como, um erro semelhante à mensagem de erro a seguir é exibido:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Você não tem as permissões necessárias para criar ou atualizar a conta Executar como ou o recurso está bloqueado em um nível de grupo de recursos.

#### <a name="resolution"></a>Resolução

Para criar ou atualizar uma conta Executar como, você deve ter permissões apropriadas para os diversos recursos usados pela conta Executar como. Para saber mais sobre as permissões necessárias para criar ou atualizar uma conta Executar como, consulte [Permissões de conta Executar como](../manage-runas-account.md#permissions).

Se o problema for por causa de um bloqueio, verifique se é adequado removê-lo. Em seguida, navegue até o recurso que está bloqueado, clique com o botão direito do mouse no bloqueio e escolha **Excluir** para remover o bloqueio.

### <a name="iphelper"></a>Cenário: você recebe o erro "não foi possível encontrar um ponto de entrada chamado ' GetPerAdapterInfo ' na DLL ' iplpapi. dll '" ao executar um runbook.

#### <a name="issue"></a>Problema

Ao executar um runbook, você receberá a seguinte exceção:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Esse erro é provavelmente causado por uma [conta Executar como](../manage-runas-account.md)configurada incorretamente.

#### <a name="resolution"></a>Resolução

Verifique se a [conta Executar como](../manage-runas-account.md) está configurada corretamente. Depois de configurado corretamente, verifique se você tem o código adequado em seu runbook para autenticar com o Azure. O exemplo a seguir mostra um trecho de código para autenticar no Azure em um runbook usando uma conta Executar como.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Próximos passos

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
