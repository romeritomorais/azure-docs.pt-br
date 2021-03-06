---
title: Executar runbooks no Hybrid Runbook Worker da Automação do Azure
description: Este artigo fornece informações sobre a execução de runbooks em computadores em seu datacenter local ou provedor de nuvem com a função de Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: c67fff32770446cac3adef8af50c9e5733077bc7
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226284"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Executar runbooks em um Hybrid Runbook Worker

Os Runbooks que visam um Hybrid Runbook Worker geralmente gerenciam recursos no computador local ou em recursos no ambiente local onde o trabalho é implantado. Os runbooks na Automação do Azure normalmente gerenciam recursos na nuvem do Azure. Embora sejam usadas de forma diferente, os runbooks executados na automação do Azure e os runbooks executados em um Hybrid Runbook Worker são idênticos na estrutura.

Ao criar um runbook para ser executado em um Hybrid Runbook Worker, você deve editar e testar o runbook no computador que hospeda o trabalho. O computador host tem todos os módulos do PowerShell e o acesso à rede necessários para gerenciar e acessar os recursos locais. Depois de testar o runbook na máquina Hybrid Runbook Worker, você pode carregá-lo no ambiente de automação do Azure, onde ele pode ser executado no trabalho. 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Permissões de runbook para um Hybrid Runbook Worker

Como eles estão acessando recursos não Azure, os runbooks em execução em um Hybrid Runbook Worker não podem usar o mecanismo de autenticação normalmente usado por runbooks Autenticando para recursos do Azure. Um runbook fornece sua própria autenticação para recursos locais ou configura a autenticação usando [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Você também pode especificar uma conta Executar como para fornecer um contexto de usuário para todos os runbooks.

### <a name="runbook-authentication"></a>Autenticação de runbook

Por padrão, os runbooks são executados no computador local. Para o Windows, eles são executados no contexto da conta do sistema local. Para o Linux, eles são executados no contexto da conta de usuário especial **nxautomation**. Em qualquer cenário, os runbooks devem fornecer sua própria autenticação aos recursos que eles acessam.

Você pode usar os ativos de [credencial](automation-credentials.md) e [certificado](automation-certificates.md) em seu runbook com cmdlets que permitem especificar credenciais para que o runbook possa se autenticar em diferentes recursos. O exemplo a seguir mostra uma parte de um runbook que reinicia um computador. Ele recupera as credenciais de um ativo de credencial e o nome do computador de um ativo variável e, em seguida, usa esses valores com o cmdlet **Restart-Computer** .

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Você também pode usar uma atividade [InlineScript](automation-powershell-workflow.md#inlinescript) . O InlineScript permite executar blocos de código em outro computador com as credenciais especificadas pelo [parâmetro comum PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>Conta Executar como

Em vez de fazer com que seu runbook forneça sua própria autenticação para recursos locais, você pode especificar uma conta Executar como para um grupo de Hybrid Runbook Worker. Para fazer isso, você deve definir um [ativo de credencial](automation-credentials.md) que tenha acesso aos recursos locais. Esses recursos incluem repositórios de certificados e todos os runbooks executados sob essas credenciais em um Hybrid Runbook Worker no grupo.

O nome de usuário da credencial deve estar em um dos seguintes formatos:

* domain\username
* username@domain
* nome de usuário (para contas locais do computador local)

Use o procedimento a seguir para especificar uma conta Executar como para um grupo de Hybrid Runbook Worker.

1. Crie um [ativo de credencial](automation-credentials.md) com acesso a recursos locais.
2. Abra a conta de Automação no Portal do Azure.
3. Escolha o bloco **Grupos do Hybrid Worker** e selecione o grupo.
4. Selecione **todas as configurações**, seguidas pelas **configurações do grupo do Hybrid Worker**.
5. Altere o valor de **Executar como** de **padrão** para **personalizado**.
6. Escolha a credencial e clique em **Salvar**.

### <a name="managed-identities-for-azure-resources"></a>Identidades gerenciadas para recursos do Azure

Hybrid runbook Workers em máquinas virtuais do Azure podem usar identidades gerenciadas para recursos do Azure para autenticar os recursos do Azure. O uso de identidades gerenciadas para recursos do Azure em vez de contas Executar como fornece benefícios porque você não precisa:

* Exportar o certificado executar como e, em seguida, importá-lo para o Hybrid Runbook Worker
* Renovar o certificado usado pela conta Executar como
* Manipular o objeto de conexão executar como em seu código de runbook

Siga as próximas etapas para usar uma identidade gerenciada para recursos do Azure em um Hybrid Runbook Worker.

1. Crie uma VM do Azure.
2. Configure identidades gerenciadas para recursos do Azure na VM. Consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Conceda acesso à VM a um grupo de recursos no Gerenciador de recursos. Consulte [usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Instale o Hybrid runbook Worker na VM. Consulte [implantar um Hybrid runbook Worker do Windows](automation-windows-hrw-install.md).
5. Atualize o runbook para usar o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) com o parâmetro *Identity* para autenticar os recursos do Azure. Essa configuração reduz a necessidade de usar uma conta Executar como e executar o gerenciamento de conta associado.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity` funciona para um Hybrid Runbook Worker usando uma identidade atribuída pelo sistema e uma única identidade atribuída pelo usuário. Se você usar várias identidades atribuídas pelo usuário na Hybrid Runbook Worker, seu runbook deverá especificar o parâmetro *AccountId* para **Connect-AzAccount** para selecionar uma identidade específica atribuída pelo usuário.

### <a name="runas-script"></a>Conta de automação Executar como

Como parte do processo de compilação automatizado para a implantação de recursos no Azure, você pode exigir acesso a sistemas locais para dar suporte a uma tarefa ou a um conjunto de etapas em sua sequência de implantação. Para fornecer autenticação no Azure usando a conta Executar como, você deve instalar o certificado da conta Executar como.

O runbook do PowerShell a seguir, chamado **Export-RunAsCertificateToHybridWorker**, exporta o certificado executar como da sua conta de automação do Azure. O runbook baixa e importa o certificado para o repositório de certificados do computador local em um Hybrid Runbook Worker que está conectado à mesma conta. Depois de concluir essa etapa, o runbook verifica se o trabalho pode se autenticar com êxito no Azure usando a conta Executar como.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Para runbooks do PowerShell, **Add-AzAccount** e **Add-AzureRMAccount** são aliases para **Connect-AzAccount**. Ao pesquisar os itens de biblioteca, se você não vir **Connect-AzAccount**, poderá usar **Add-AzAccount**ou poderá atualizar seus módulos na sua conta de automação.

Para concluir a preparação da conta Executar como:

1. Salve o runbook **Export-RunAsCertificateToHybridWorker** em seu computador com uma extensão **. ps1** .
2. Importe-o para sua conta de automação.
3. Edite o runbook, alterando o valor da variável de *senha* de sua própria senha. 
4. Publique o runbook.
5. Execute o runbook, direcionando o grupo de Hybrid Runbook Worker que executa e autentica runbooks usando a conta Executar como. 
6. Examine o fluxo de trabalho para ver se ele relata a tentativa de importar o certificado para o repositório do computador local e segue várias linhas. Esse comportamento depende de quantas contas de automação você define em sua assinatura e o grau de sucesso da autenticação.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Comportamento do trabalho em Hybrid runbook Workers

A automação do Azure manipula trabalhos em Hybrid runbook Workers de forma ligeiramente diferente dos trabalhos executados em áreas restritas do Azure. Uma diferença importante é que não há limite para a duração do trabalho nos runbook Workers. Os Runbooks executados em áreas restritas do Azure são limitados a três horas devido à [participação justa](automation-runbook-execution.md#fair-share).

Para um runbook de execução longa, você deseja garantir que ele seja resiliente à possível reinicialização, por exemplo, se o computador que hospeda o trabalho for reinicializado. Se a máquina host Hybrid Runbook Worker for reinicializada, qualquer trabalho de runbook em execução será reiniciado desde o início ou do último ponto de verificação para runbooks de fluxo de trabalho do PowerShell. Depois que um trabalho de runbook é reiniciado mais de três vezes, ele é suspenso.

Lembre-se de que os trabalhos para Hybrid runbook Workers são executados na conta sistema local no Windows ou na conta **nxautomation** no Linux. Para o Linux, você deve garantir que a conta **nxautomation** tenha acesso ao local onde os módulos runbook estão armazenados. Ao usar o cmdlet [install-Module](/powershell/module/powershellget/install-module) , certifique-se de especificar **AllUsers** para o parâmetro de *escopo* para garantir que a conta **nxautomation** tenha acesso. Para obter mais informações sobre o PowerShell no Linux, consulte [problemas conhecidos do PowerShell em plataformas que não são do Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Iniciando um runbook em um Hybrid Runbook Worker

[Iniciar um runbook na automação do Azure](automation-starting-a-runbook.md) descreve métodos diferentes para iniciar um runbook. A inicialização de um runbook em um Hybrid Runbook Worker usa uma opção **executar em** que permite especificar o nome de um grupo de Hybrid runbook Worker. Quando um grupo é especificado, um dos trabalhadores desse grupo recupera e executa o runbook. Se o runbook não especificar essa opção, a automação do Azure executará o runbook como de costume.

Ao iniciar um runbook no portal do Azure, você verá a opção **executar em** para a qual você pode selecionar **Azure** ou **Hybrid Worker**. Se você selecionar **Hybrid Worker**, poderá escolher o grupo de Hybrid runbook Worker de uma lista suspensa.

Use o parâmetro *RunOn* com o cmdlet **Start-AzureAutomationRunbook** . O exemplo a seguir usa o Windows PowerShell para iniciar um runbook chamado **Test-runbook** em um grupo de Hybrid runbook Worker chamado myhíbridoy.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> O parâmetro *RunOn* foi adicionado ao **Start-AzureAutomationRunbook** na versão 0.9.1 do Microsoft Azure PowerShell. Você deve [baixar a versão mais recente](https://azure.microsoft.com/downloads/) se tiver uma anterior instalada. Instale essa versão somente na estação de trabalho em que você está iniciando o runbook do PowerShell. Você não precisa instalá-lo no computador Hybrid Runbook Worker, a menos que pretenda iniciar runbooks deste computador.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Trabalhando com runbooks assinados em um Hybrid Runbook Worker do Windows

Você pode configurar um Hybrid Runbook Worker do Windows para executar apenas runbooks assinados.

> [!IMPORTANT]
> Depois de configurar um Hybrid Runbook Worker para executar apenas runbooks assinados, os runbooks que não foram assinados não serão executados no trabalho.

### <a name="create-signing-certificate"></a>Criar certificado de assinatura

O exemplo a seguir cria um certificado autoassinado que pode ser usado para assinar runbooks. Esse código cria o certificado e o exporta para que o Hybrid Runbook Worker possa importá-lo mais tarde. A impressão digital também é retornada para uso posterior na referência ao certificado.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importar certificado e configurar trabalhadores para validação de assinatura

Copie o certificado que você criou para cada Hybrid Runbook Worker em um grupo. Execute o script a seguir para importar o certificado e configurar os trabalhos para usar a validação de assinatura em runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Assinar seus runbooks usando o certificado

Com os Hybrid runbook Workers configurados para usar apenas runbooks assinados, você deve assinar runbooks que serão usados no Hybrid Runbook Worker. Use o código do PowerShell de exemplo a seguir para assinar esses runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando um runbook tiver sido assinado, você deverá importá-lo para sua conta de automação e publicá-lo com o bloco de assinatura. Para saber como importar os runbooks, consulte [Importando um runbook de um arquivo para a Automação do Azure](manage-runbooks.md#import-a-runbook).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Trabalhando com runbooks assinados em um Hybrid Runbook Worker Linux

Para poder trabalhar com runbooks assinados, uma Hybrid Runbook Worker do Linux deve ter o executável [GPG](https://gnupg.org/index.html) no computador local.

> [!IMPORTANT]
> Depois de configurar um Hybrid Runbook Worker para executar apenas runbooks assinados, os runbooks que não foram assinados não serão executados no trabalho.

### <a name="create-a-gpg-keyring-and-keypair"></a>Criar um token de autenticação e um par de chaves do GPG

Para criar o keyring GPG e o par de chaves, use a conta Hybrid Runbook Worker **nxautomation** .

1. Use o aplicativo sudo para entrar como a conta **nxautomation** .

    ```bash
    sudo su – nxautomation
    ```

2. Quando você estiver usando o **nxautomation**, gere o par de chaves GPG. O GPG orienta você pelas etapas. Você deve fornecer nome, endereço de email, hora de expiração e frase secreta. Em seguida, aguarde até que haja uma entropia suficiente no computador para que a chave seja gerada.

    ```bash
    sudo gpg --generate-key
    ```

3. Como o diretório GPG foi gerado com sudo, você deve alterar seu proprietário para **nxautomation** usando o comando a seguir.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Disponibilizar o keyring para o Hybrid Runbook Worker

Depois que o keyring tiver sido criado, disponibilize-o para o Hybrid Runbook Worker. Modifique o arquivo de configurações `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` para incluir o seguinte código de exemplo na seção arquivo **[Worker-opcional]** .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Verifique se a validação da assinatura está ativada

Se a validação de assinatura tiver sido desabilitada no computador, você deverá ativá-la executando o comando sudo a seguir. Substitua `<LogAnalyticsworkspaceId>` pela sua ID do espaço de trabalho.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Assinar um runbook

Depois de configurar a validação de assinatura, use o comando GPG a seguir para assinar um runbook.

```bash
gpg –-clear-sign <runbook name>
```

O runbook assinado é chamado de `<runbook name>.asc`.

Agora você pode carregar o runbook assinado para a automação do Azure e executá-lo como um runbook regular.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Para saber mais sobre os métodos para iniciar um runbook, consulte [iniciando um runbook na automação do Azure](automation-starting-a-runbook.md).
* Para entender como usar o editor de texto para trabalhar com runbooks do PowerShell na automação do Azure, consulte [editando um runbook na automação do Azure](automation-edit-textual-runbook.md).
* Se seus runbooks não estiverem concluídos com êxito, examine o guia de solução de problemas de [falhas de execução do runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
