---
title: Azure Active Directory padrões de segurança
description: Políticas padrão de segurança que ajudam a proteger as organizações contra ataques comuns
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3139d39797c3bc576bb39f1438b7e6d3f37e3c5c
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78248855"
---
# <a name="what-are-security-defaults"></a>O que são os padrões de segurança?

O gerenciamento de segurança pode ser difícil quando ataques comuns relacionados à identidade estão se tornando cada vez mais populares. Esses ataques incluem pulverização, reprodução e phishing de senha.

Os padrões de segurança no Azure Active Directory (AD do Azure) facilitam a segurança e ajudam a proteger sua organização. Os padrões de segurança contêm configurações de segurança pré-configuradas para ataques comuns. 

A Microsoft está disponibilizando os padrões de segurança para todos. O objetivo é garantir que todas as organizações tenham um nível básico de segurança habilitado sem nenhum custo adicional. Você ativa os padrões de segurança no portal do Azure.

![Captura de tela da portal do Azure com a alternância para habilitar os padrões de segurança](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Se seu locatário foi criado em ou após 22 de outubro de 2019, é possível que você esteja experimentando o novo comportamento seguro por padrão e já tenha os padrões de segurança habilitados em seu locatário. Em um esforço para proteger todos os nossos usuários, os padrões de segurança estão sendo distribuídos para todos os novos locatários criados.

Mais detalhes sobre por que os padrões de segurança estão sendo disponibilizados podem ser encontrados na postagem do blog de Alex Weinert, [apresentando os padrões de segurança](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="unified-multi-factor-authentication-registration"></a>Registro de autenticação multifator unificado

Todos os usuários em seu locatário devem se registrar para a autenticação multifator (MFA) na forma do serviço de autenticação multifator do Azure. Os usuários têm 14 dias para se registrarem na autenticação multifator usando o aplicativo Microsoft Authenticator. Depois que os 14 dias tiverem passado, o usuário não poderá entrar até que o registro da autenticação multifator seja concluído.

Entendemos que alguns usuários podem estar fora do escritório ou não entrarão durante os 14 dias imediatamente após habilitar os padrões de segurança. Para garantir que cada usuário tenha bastante tempo para se registrar na autenticação multifator, o período de 14 dias é exclusivo para cada usuário. O período de 14 dias de um usuário começa após sua primeira entrada interativa bem-sucedida depois de habilitar os padrões de segurança.

## <a name="multi-factor-authentication-enforcement"></a>Imposição da autenticação multifator

### <a name="protecting-administrators"></a>Protegendo administradores

Os usuários com acesso a contas com privilégios aumentaram o acesso ao seu ambiente. Devido à capacidade que essas contas têm, devem ser tratadas com cuidado especial. Um método comum para melhorar a proteção de contas com privilégios é exigir uma forma mais forte de verificação de conta para entrada. No Azure AD, você pode obter uma verificação de conta mais forte exigindo a autenticação multifator.

Após a conclusão do registro com a autenticação multifator, serão necessárias as nove funções de administrador do Azure AD a seguir para executar autenticação adicional sempre que entrarem:

- Administrador global
- Administrador do SharePoint
- Administrador do Exchange
- Administrador de acesso condicional
- Administrador de segurança
- Administrador de assistência técnica ou administrador de senha
- Administrador de cobrança
- Administrador de usuários
- Administrador de autenticação

### <a name="protecting-all-users"></a>Protegendo todos os usuários

Tendemos a imaginar que as contas de administrador são as únicas contas que precisam de camadas extras de autenticação. Os administradores têm acesso amplo a informações confidenciais e podem fazer alterações nas configurações de toda a assinatura. Mas os invasores tendem a direcionar os usuários finais. 

Depois que esses invasores obtiverem acesso, eles poderão solicitar acesso a informações privilegiadas em nome do titular da conta original. Eles podem até mesmo baixar o diretório inteiro para executar um ataque de phishing em toda a sua organização. 

Um método comum para melhorar a proteção para todos os usuários é exigir uma forma mais forte de verificação de conta, como a autenticação multifator, para todos. Depois que os usuários concluírem o registro da autenticação multifator, eles serão solicitados a fornecer autenticação adicional sempre que necessário.

### <a name="blocking-legacy-authentication"></a>Bloqueando a autenticação herdada

Para dar aos usuários acesso fácil aos seus aplicativos de nuvem, o Azure AD dá suporte a uma variedade de protocolos de autenticação, incluindo a autenticação herdada. A *autenticação herdada* é um termo que se refere a uma solicitação de autenticação feita por:

- Clientes que não usam autenticação moderna (por exemplo, um cliente do Office 2010).
- Qualquer cliente que usa protocolos de email mais antigos, como IMAP, SMTP ou POP3.

Hoje, a maior parte do comprometimento de tentativas de entrada é proveniente da autenticação herdada. A autenticação herdada não dá suporte à autenticação multifator. Mesmo que você tenha uma política de autenticação multifator habilitada em seu diretório, um invasor pode se autenticar usando um protocolo mais antigo e ignorar a autenticação multifator. 

Depois que os padrões de segurança são habilitados em seu locatário, todas as solicitações de autenticação feitas por um protocolo mais antigo serão bloqueadas. Os padrões de segurança bloqueia Exchange Active Sync autenticação básica.

> [!WARNING]
> Antes de habilitar os padrões de segurança, verifique se os administradores não estão usando protocolos de autenticação mais antigos. Para obter mais informações, consulte [como sair da autenticação herdada](concept-fundamentals-block-legacy-authentication.md).

### <a name="protecting-privileged-actions"></a>Protegendo ações privilegiadas

As organizações usam uma variedade de serviços do Azure gerenciados por meio da API Azure Resource Manager, incluindo:

- Portal do Azure 
- Azure PowerShell 
- CLI do Azure

O uso de Azure Resource Manager para gerenciar seus serviços é uma ação altamente privilegiada. Azure Resource Manager pode alterar configurações de todo o locatário, como configurações de serviço e cobrança de assinatura. A autenticação de fator único é vulnerável a uma variedade de ataques, como phishing e spray de senha. 

É importante verificar a identidade dos usuários que desejam acessar Azure Resource Manager e atualizar as configurações. Você verifica sua identidade exigindo autenticação adicional antes de permitir o acesso.

Depois de habilitar os padrões de segurança em seu locatário, qualquer usuário que estiver acessando o portal do Azure, Azure PowerShell ou o CLI do Azure precisará concluir a autenticação adicional. Essa política se aplica a todos os usuários que estão acessando Azure Resource Manager, seja um administrador ou um usuário. 

Se o usuário não estiver registrado para autenticação multifator, o usuário será solicitado a se registrar usando o aplicativo Microsoft Authenticator para continuar. Nenhum período de registro de autenticação multifator de 14 dias será fornecido.

> [!NOTE]
> Os locatários anteriores à 2017 do Exchange Online têm autenticação moderna desabilitada por padrão. Para evitar a possibilidade de um loop de logon durante a autenticação por meio desses locatários, você deve [habilitar a autenticação moderna](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> A conta de sincronização de Azure AD Connect é excluída dos padrões de segurança e não será solicitado a se registrar ou executar a autenticação multifator. As organizações não devem usar essa conta para outros fins.

## <a name="deployment-considerations"></a>Considerações de implantação

As considerações adicionais a seguir estão relacionadas à implantação de padrões de segurança para seu locatário.

### <a name="authentication-methods"></a>Métodos de autenticação

Os padrões de segurança permitem o registro e o uso da autenticação multifator do Azure **usando apenas o aplicativo Microsoft Authenticator usando notificações**. O acesso condicional permite o uso de qualquer método de autenticação que o administrador escolha para habilitar.

|   | Padrões de segurança | Acesso Condicional |
| --- | --- | --- |
| Notificação pelo aplicativo móvel | X | X |
| O código de verificação do aplicativo móvel ou token de hardware |   | X |
| Mensagem de texto para telefone |   | X |
| Ligue para o telefone |   | X |
| Senhas de aplicativo |   | X * * |

\* * As senhas de aplicativo só estarão disponíveis no MFA por usuário com cenários de autenticação herdados somente se habilitadas pelos administradores.

### <a name="conditional-access"></a>Acesso Condicional

Você pode usar o acesso condicional para configurar políticas semelhantes a padrões de segurança, mas com mais granularidade, incluindo exclusões de usuário, que não estão disponíveis em padrões de segurança. Se você estiver usando o acesso condicional e tiver políticas de acesso condicional habilitadas em seu ambiente, os padrões de segurança não estarão disponíveis para você. Se você tiver uma licença que forneça acesso condicional, mas não tiver políticas de acesso condicional habilitadas em seu ambiente, você poderá usar os padrões de segurança até habilitar as políticas de acesso condicional. Mais informações sobre o licenciamento do Azure AD podem ser encontradas na [página de preços do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

![Mensagem de aviso de que você pode ter padrões de segurança ou acesso condicional não ambos](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Aqui estão os guias passo a passo sobre como você pode usar o acesso condicional para configurar políticas equivalentes:

- [Exigir MFA para administradores](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Exigir MFA para gerenciamento do Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Bloquear autenticação herdada](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Exigir MFA para todos os usuários](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Exigir registro do Azure MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) -requer Azure ad Identity Protection

## <a name="enabling-security-defaults"></a>Habilitando padrões de segurança

Para habilitar os padrões de segurança em seu diretório:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador de segurança, administrador de acesso condicional ou administrador global.
1. Navegue até **Azure Active Directory**  **Propriedades**de >de .
1. Selecione **gerenciar padrões de segurança**.
1. Defina a alternância **habilitar padrões de segurança** para **Sim**.
1. Clique em **Salvar**.

## <a name="disabling-security-defaults"></a>Desabilitando padrões de segurança

As organizações que optam por implementar políticas de acesso condicional que substituem os padrões de segurança devem desabilitar os padrões de segurança. 

![Mensagem de aviso desabilite os padrões de segurança para habilitar o acesso condicional](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Para desabilitar os padrões de segurança em seu diretório:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador de segurança, administrador de acesso condicional ou administrador global.
1. Navegue até **Azure Active Directory**  **Propriedades**de >de .
1. Selecione **gerenciar padrões de segurança**.
1. Defina a alternância **habilitar padrões de segurança** para **não**.
1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Políticas de acesso condicional comum](../conditional-access/concept-conditional-access-policy-common.md)
