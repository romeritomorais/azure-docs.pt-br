---
title: Preparar aplicativos móveis – chamando APIs da Web para produção | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo móvel que chama APIs da Web. (Preparar aplicativos para produção.)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1ea19b8b76f4eb4a2c984f0e39eb0fd373c8b83c
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132384"
---
# <a name="prepare-mobile-apps-for-production"></a>Preparar aplicativos móveis para produção

Este artigo fornece detalhes sobre como melhorar a qualidade e a confiabilidade do seu aplicativo móvel antes de movê-lo para produção.

## <a name="handle-errors"></a>Tratar erros

Ao preparar um aplicativo móvel para produção, várias condições de erro podem ocorrer. Os principais casos que você tratará são falhas silenciosas e fallbacks para a interação. Outras condições que você deve considerar incluem situações sem rede, interrupções de serviço, requisitos para o consentimento do administrador e outros casos específicos do cenário.

Para cada tipo de MSAL (biblioteca de autenticação da Microsoft), você pode encontrar o código de exemplo e o conteúdo do wiki que descrevem como lidar com condições de erro:

- [Wiki do MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki do MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Atenuar e investigar problemas

Para diagnosticar melhor os problemas em seu aplicativo, colete dados. Para obter informações sobre os tipos de dados que você pode coletar, consulte [Logging in MSAL Applications](https://docs.microsoft.com/azure/active-directory/develop/msal-logging).

Aqui estão algumas sugestões para a coleta de dados:

- Os usuários podem pedir ajuda quando tiverem problemas. Uma prática recomendada é capturar e armazenar logs temporariamente. Forneça um local onde os usuários possam carregar os logs. O MSAL fornece extensões de log para capturar informações detalhadas sobre a autenticação.

- Se a telemetria estiver disponível, habilite-a por meio do MSAL para coletar dados sobre como os usuários entram no seu aplicativo.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Para experimentar exemplos adicionais, consulte [aplicativos cliente públicos móveis e de desktop](sample-v2-code.md#desktop-and-mobile-public-client-apps).
