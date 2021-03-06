---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 7246a072c1bf2253b822fca53b0b69700c66221d
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565224"
---
## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade

Para acessar os recursos de arquivos do Azure com autenticação baseada em identidade, uma identidade (um usuário, grupo ou entidade de serviço) deve ter as permissões necessárias no nível de compartilhamento. Esse processo é semelhante à especificação de permissões de compartilhamento do Windows, em que você especifica o tipo de acesso que um usuário específico tem a um compartilhamento de arquivos. As orientações nesta seção demonstram como atribuir ler, gravar ou excluir as permissões para um compartilhamento de arquivos para uma identidade.

Introduzimos três funções internas do Azure para conceder permissões de nível de compartilhamento aos usuários:

- O **leitor de compartilhamento SMB de dados de arquivo de armazenamento** permite acesso de leitura em compartilhamentos de arquivos de armazenamento do Azure via SMB
- O **colaborador de compartilhamento SMB de dados de arquivo de armazenamento** permite acesso de leitura, gravação e exclusão em compartilhamentos de arquivos de armazenamento do Azure via SMB.
- O **colaborador elevado de compartilhamento SMB de dados de arquivo de armazenamento** permite a leitura, gravação, exclusão e modificação de permissões NTFS em compartilhamentos de arquivos de armazenamento do Azure via SMB.

> [!IMPORTANT]
> O controle administrativo total de um compartilhamento de arquivos, incluindo a capacidade de atribuir uma função a uma identidade, requer o uso da chave da conta de armazenamento. O controle administrativo não é compatível com credenciais do Azure AD.

Você pode usar o portal do Azure, o PowerShell ou o CLI do Azure para atribuir as funções internas à identidade do Azure AD de um usuário para conceder permissões de nível de compartilhamento.

> [!NOTE]
> Lembre-se de sincronizar suas credenciais do AD com o Azure AD se você planeja usar seu AD para autenticação. A sincronização de hash de senha do AD para o Azure AD é opcional. A permissão de nível de compartilhamento será concedida à identidade do Azure AD que é sincronizada do AD.

#### <a name="azure-portal"></a>Portal do Azure
Para atribuir uma função de RBAC a uma identidade do Azure AD, usando o [portal do Azure](https://portal.azure.com), siga estas etapas:

1. No portal do Azure, vá para o compartilhamento de arquivos ou [crie um compartilhamento de arquivos](../articles/storage/files/storage-how-to-create-file-share.md).
2. Selecione **Controle de Acesso (IAM)** .
3. Selecione **Adicionar uma atribuição de função**
4. Na folha **Adicionar atribuição de função** , selecione a função interna apropriada (leitor de compartilhamento SMB de dados de arquivo de armazenamento, colaborador de compartilhamento SMB de dados de arquivo de armazenamento) na lista de **funções** . Deixe **atribuir acesso à** na configuração padrão: **usuário, grupo ou entidade de serviço do Azure ad**. Selecione a identidade do Azure AD de destino por nome ou endereço de email.
5. Selecione **salvar** para concluir a operação de atribuição de função.

#### <a name="powershell"></a>PowerShell

O exemplo do PowerShell a seguir mostra como atribuir uma função de RBAC a uma identidade do Azure AD, com base no nome de entrada. Para obter mais informações sobre como atribuir funções do RBAC ao PowerShell, consulte [Gerenciar acesso usando o RBAC e o Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Antes de executar o script de exemplo a seguir, lembre-se de substituir os valores de espaço reservado, incluindo colchetes, pelos seus próprios valores.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
O comando da CLI 2,0 a seguir mostra como atribuir uma função de RBAC a uma identidade do Azure AD, com base no nome de entrada. Para obter mais informações sobre como atribuir funções RBAC com CLI do Azure, consulte [gerenciar o acesso usando RBAC e CLI do Azure](../articles/role-based-access-control/role-assignments-cli.md). 

Antes de executar o script de exemplo a seguir, lembre-se de substituir os valores de espaço reservado, incluindo colchetes, pelos seus próprios valores.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Configurar permissões NTFS em SMB 
Depois de atribuir permissões no nível de compartilhamento com o RBAC, você deve atribuir permissões de NTFS corretas no nível raiz, de diretório ou de arquivo. Considere as permissões de nível de compartilhamento como o gatekeeper de alto nível que determina se um usuário pode acessar o compartilhamento. Enquanto as permissões de NTFS atuam em um nível mais granular para determinar quais operações o usuário pode fazer no nível do diretório ou do arquivo.

Os arquivos do Azure dá suporte a todo o conjunto de permissões de NTFS básicos e avançados. Você pode exibir e configurar as permissões NTFS em diretórios e arquivos em um compartilhamento de arquivos do Azure montando o compartilhamento e usando o explorador de arquivos do Windows ou executando o comando [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) do Windows ou [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

Para configurar o NTFS com permissões de superusuário, você deve montar o compartilhamento usando sua chave de conta de armazenamento de sua VM ingressada no domínio. Siga as instruções na próxima seção para montar um compartilhamento de arquivos do Azure no prompt de comando e configurar as permissões NTFS adequadamente.

Conjuntos de permissões a seguir têm suporte para o diretório raiz de um compartilhamento de arquivos:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(Rx)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurar permissões NTFS com icacls
Use o seguinte comando do Windows para conceder permissões completas para todos os diretórios e arquivos no compartilhamento de arquivos, incluindo o diretório raiz. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para obter mais informações sobre como usar o icacls para definir permissões NTFS e sobre os diferentes tipos de permissões com suporte, consulte [a referência de linha de comando para icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montar um compartilhamento de arquivos do Azure no prompt de comando

Usar o Windows **net use** comando para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores de espaço reservado no exemplo a seguir pelos seus próprios valores. Para obter mais informações sobre como montar compartilhamentos de arquivos, consulte [usar um compartilhamento de arquivos do Azure com o Windows](../articles/storage/files/storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configurar permissões NTFS com o explorador de arquivos do Windows
Use o explorador de arquivos do Windows para conceder permissão total a todos os diretórios e arquivos no compartilhamento de arquivos, incluindo o diretório raiz.

1. Abra o explorador de arquivos do Windows e clique com o botão direito do mouse no arquivo/diretório e selecione **Propriedades**
2. Clique na guia **segurança**
3. Clique em **Editar..** . botão para alterar permissões
4. Você pode alterar a permissão de usuários existentes ou clicar em **Adicionar...** para conceder permissões a novos usuários
5. Na janela de prompt para adicionar novos usuários, insira o nome de usuário de destino ao qual você deseja conceder permissão na caixa **digite os nomes de objeto a serem selecionados** e clique em **verificar nomes** para localizar o nome UPN completo do usuário de destino.
7.  Clique em **OK**
8.  Na guia Segurança, selecione todas as permissões que você deseja conceder ao usuário recém-adicionado
9.  Clique em **Aplicar**

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montar um compartilhamento de arquivos de uma VM ingressada no domínio

O processo a seguir verifica se as permissões de acesso e compartilhamento de arquivos foram configuradas corretamente e se você pode acessar um compartilhamento de arquivos do Azure de uma VM ingressada no domínio:

Entre na VM usando a identidade do Azure AD à qual você concedeu permissões, conforme mostrado na imagem a seguir. Se você tiver habilitado a autenticação do AD para arquivos do Azure, use a credencial do AD. Para a autenticação de AD DS do Azure, faça logon com a credencial do Azure AD.

![Captura de tela mostrando a tela de entrada do Azure AD para autenticação do usuário](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Use o comando a seguir para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores de espaço reservado por seus próprios valores. Como você foi autenticado, não precisa fornecer a chave da conta de armazenamento, as credenciais do AD ou as credenciais do AD do Azure. Há suporte para a experiência de logon único para autenticação com o AD ou o Azure AD DS.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```