---
title: ClaimsProviders  - Azure Active Directory B2C | Microsoft Docs
description: Especifica o elemento ClaimsProvider de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dfb34085181e0b759d1d77485ff21b5bc59e0de3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189762"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um provedor de declarações contém um conjunto de [perfis técnicos](technicalprofiles.md). Cada provedor de declarações precisa ter um ou mais perfis técnicos que determinam os pontos de extremidade e os protocolos necessários para a comunicação com o provedor de declarações. Um provedor de declarações pode ter vários perfis técnicos. Por exemplo, vários perfis técnicos podem ser definidos porque o provedor de declarações dá suporte a vários protocolos, vários pontos de extremidade com diferentes recursos ou libera diferentes declarações em diferentes níveis de garantia. Pode ser aceitável liberar solicitações confidenciais em um percurso do usuário, mas não em outro.

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

O elemento **ClaimsProviders** contém o seguinte elemento:

| Elemento | Ocorrências | DESCRIÇÃO |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | Um provedor de declarações autorizado que pode ser aproveitado em vários percursos do usuário. |

## <a name="claimsprovider"></a>ClaimsProvider

O elemento **ClaimsProvider** contém os elementos filho a seguir:

| Elemento | Ocorrências | DESCRIÇÃO |
| ------- | ---------- | ----------- |
| Domínio | 0:1 | Uma cadeia de caracteres que contém o nome de domínio do provedor de declarações. Por exemplo, se o provedor de declarações incluir o perfil técnico do Facebook, o nome de domínio será Facebook.com. Esse nome de domínio é usado para todos os perfis técnicos definidos no provedor de declarações, a menos que seja substituído pelo perfil técnico. O nome do domínio também pode ser referenciado em **domain_hint**. Para obter mais informações, consulte a seção **Redirecionar a entrada para um provedor social** de [Configurar entrada direta usando o Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | Uma cadeia de caracteres que contém o nome do provedor de declarações. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Um conjunto de perfis técnicos com suporte pelo provedor de declarações |

**Claimprovider** organiza como seus perfis técnicos se relacionam com o provedor de declarações. O exemplo a seguir mostra o provedor de declarações do Azure Active Directory com os perfis técnicos do Azure Active Directory:

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

O exemplo a seguir mostra o provedor de declarações do Facebook com o perfil técnico **Facebook-OAUTH**.

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
