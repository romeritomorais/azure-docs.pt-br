---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/02/2020
ms.author: dacoulte
ms.openlocfilehash: 85c7cb292c5131b161bd488d635ae5a051cd2bfe
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262165"
---
|Nome |Descrição |Efeito(s) |Versão |GitHub |
|---|---|---|---|---|
|[Locais permitidos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe56962a6-4747-49cd-b67b-bf8b01975c4c) |Esta política permite que você restrinja os locais que sua organização pode especificar ao implantar recursos. Use para impor seus requisitos de conformidade geográfica. Exclui grupos de recursos, Microsoft.AzureActiveDirectory/b2cDirectories e recursos que usam a região "global". |deny |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json)
|[Localizações permitidas para grupos de recursos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe765b5de-1225-4ba3-bd56-1ac6695af988) |Esta política permite que você restrinja os locais em que sua organização pode criar grupos de recursos. Use para impor seus requisitos de conformidade geográfica. |deny |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json)
|[Tipos de recursos permitidos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa08ec900-254a-4555-9bf5-e42af04b5c5c) |Essa política permite especificar os tipos de recursos que sua organização pode implantar. Somente os tipos de recursos que dão suporte a "marcas" e "local" serão afetados por essa política. Para restringir todos os recursos, duplique essa política e altere o "modo" para "Todos". |deny |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json)
|[O local do recurso de auditoria corresponde ao local do grupo de recursos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a914e76-4921-4c19-b460-a2d36003525a) |Auditar se o local do recurso corresponde ao local do seu grupo de recursos |auditoria |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json)
|[Auditar o uso de regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Auditar funções internas, como 'Proprietário, Contribuidor, Leitor', em vez de funções RBAC personalizadas, que são propensas a erros. O uso de funções personalizadas é tratado como uma exceção e requer uma revisão rigorosa e uma modelagem de ameaças |Audit, desabilitado |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json)
|[Não devem existir funções personalizadas de proprietário de assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Essa política garante que não exista nenhuma função personalizada de proprietário de assinatura. |Audit, desabilitado |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json)
|[Tipos de recursos não permitidos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c112d4e-5bc7-47ae-a041-ea2d9dccd749) |Essa política permite especificar os tipos de recursos que sua organização não pode implantar. |Negar |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json)
