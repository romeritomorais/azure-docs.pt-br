---
title: Girar o protetor TDE – PowerShell
description: Saiba como girar o protetor de TDE (Transparent Data Encryption) para um servidor SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 553d8535d2fdbd7daa5c93535c7c4bd51f2da1a1
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995798"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Girar o protetor de TDE (Transparent Data Encryption) usando PowerShell

Este artigo descreve a rotação de chave para um servidor SQL do Azure usando um protetor de TDE do Azure Key Vault. Girar um protetor de TDE do servidor SQL do Azure significa alternar para uma nova chave assimétrica que protege os bancos de dados no servidor. A rotação de chave é uma operação online e deve demorar apenas alguns segundos para ser concluída, porque isso somente descriptografa e criptografa novamente a chave de criptografia de dados do banco de dados, não o banco de dados inteiro.

Este guia aborda duas opções para girar o protetor de TDE no servidor.

> [!NOTE]
> Um SQL Data Warehouse pausado deve ser retomado antes das rotações de chave.

> [!IMPORTANT]
> Não exclua as versões anteriores da chave após uma substituição. Quando as teclas são substituídas, alguns dados ainda são criptografados com as chaves anteriores, como backups de banco de dados mais antigos.

## <a name="prerequisites"></a>Pré-requisitos

- Este guia de instruções assume que você já esteja usando uma chave do Azure Key Vault como protetor de TDE para um Data Warehouse ou Banco de Dados SQL do Azure. Consulte [Transparent Data Encryption com suporte de BYOK](transparent-data-encryption-byok-azure-sql.md).
- Você deve ter Azure PowerShell instalado e em execução.
- [Recomendado, mas opcional] Crie o material da chave para o protetor de TDE em um HSM (Módulo de Segurança de Hardware) ou o armazenamento de chaves local primeiro e importe o material da chave para o Azure Key Vault. Siga as [instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) para saber mais.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter instruções de instalação do módulo Az, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [apresentando o novo módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para instalação, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Rotação manual de chaves

A rotação de teclas manual usa os seguintes comandos para adicionar uma chave totalmente nova, que pode estar sob um novo nome de chave ou até mesmo em outro cofre de chaves. Usar essa abordagem dá suporte à adição da mesma chave a diferentes cofres de chaves para dar suporte a cenários de alta disponibilidade e recuperação de desastres geográficos.

> [!NOTE]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Use os cmdlets [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)e [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use os comandos [AZ keyvault Key Create](/cli/azure/keyvault/key#az-keyvault-key-create), [AZ SQL Server Key Create](/cli/azure/sql/server/key#az-sql-server-key-create)e [AZ SQL Server TDE-Key Set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) .

```azure-cli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis do PowerShell

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- Para alternar o protetor de TDE do modo gerenciado pela Microsoft para o BYOK, use o cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Para alternar o protetor de TDE de modo BYOK para gerenciado pela Microsoft, use o cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Os exemplos a seguir usam [AZ SQL Server TDE-Key Set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Para alternar o protetor de TDE do modo gerenciado pela Microsoft para o BYOK,

   ```azure-cli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Para alternar o protetor de TDE do modo BYOK para o gerenciado pela Microsoft,

   ```azure-cli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Próximos passos

- Em caso de risco de segurança, saiba como remover um protetor de TDE potencialmente comprometido: [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Introdução à integração de Azure Key Vault e Bring Your Own Key suporte para TDE: [ative o TDE usando sua própria chave de Key Vault usando o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
