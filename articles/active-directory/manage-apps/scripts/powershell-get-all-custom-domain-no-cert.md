---
title: Exemplo do PowerShell – aplicativos de proxy de aplicativo sem certificado
description: Exemplo do PowerShell que lista todos os aplicativos de proxy de aplicativo Azure Active Directory (Azure AD) que estão usando domínios personalizados, mas não têm um certificado SSL válido carregado.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0732a96c33062fdfdc552810880dedd79ce821ab
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482030"
---
# <a name="get-all-azure-ad-proxy-application-apps-published-with-no-certificate-uploaded"></a>Obter todos os aplicativos de aplicativo proxy do Azure AD publicados sem nenhum certificado carregado

Este exemplo de script do PowerShell lista todos os aplicativos de proxy de aplicativo Azure Active Directory (Azure AD) que estão usando domínios personalizados, mas não têm um certificado SSL válido carregado.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Este exemplo requer o [módulo AzureAD v2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou o [PowerShell do AzureAD v2 para a versão de visualização do módulo do Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domain-no-cert.ps1 "Get all Azure AD Proxy application apps published with no certificate uploaded")]

## <a name="script-explanation"></a>Explicação sobre o script

| Comando | Observações |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Obtém uma entidade de serviço. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Obtém um aplicativo do Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Recupera um aplicativo configurado para o proxy de aplicativo no Azure AD. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o módulo do PowerShell do Azure AD, consulte [visão geral do módulo do PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para obter outros exemplos do PowerShell para o proxy de aplicativo, consulte [exemplos do PowerShell do Azure ad para azure proxy de aplicativo do AD](../application-proxy-powershell-samples.md).