---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Hosted MyCirqa SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Hosted MyCirqa SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4337e343-180d-475d-83ac-6f5b5f133dfe
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc6cd36190ca3847fdc9e3f6935aed5c78e0942
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75662436"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hosted-mycirqa-sso"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Hosted MyCirqa SSO

Neste tutorial, você aprenderá a integrar o Hosted MyCirqa SSO ao Azure AD (Azure Active Directory). Ao integrar o Hosted MyCirqa SSO ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao Hosted MyCirqa SSO.
* Permitir que os usuários, usando as respectivas contas do Azure AD, sejam conectados automaticamente ao Hosted MyCirqa SSO.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisites

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Hosted MyCirqa SSO habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Hosted MyCirqa SSO é compatível com SSO iniciado por **SP**

## <a name="adding-hosted-mycirqa-sso-from-the-gallery"></a>Adição do Hosted MyCirqa SSO por meio da Galeria

Para configurar a integração do Hosted MyCirqa SSO ao Azure AD, você precisa adicionar o Hosted MyCirqa SSO da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Hosted MyCirqa SSO** na caixa de pesquisa.
1. Selecione **Hosted MyCirqa SSO** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hosted-mycirqa-sso"></a>Configurar e testar o logon único do Azure AD para o Hosted MyCirqa SSO

Configure e teste o SSO do Azure AD com o Hosted MyCirqa SSO usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Hosted MyCirqa SSO.

Para configurar e testar o SSO do Azure AD com o Hosted MyCirqa SSO, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o Hosted MyCirqa SSO](#configure-hosted-mycirqa-sso)** –para configurar o logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Hosted MyCirqa SSO](#create-hosted-mycirqa-sso-test-user)** – para ter um equivalente de B. Fernandes no Hosted MyCirqa SSO que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Hosted MyCirqa SSO**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.cirqahosting.com/CirqaIdentity/external?`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://isoxford.com/<CUSTOMID>/cirqaidentity/saml2`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Hosted MyCirqa SSO](not sure) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo a ela acesso ao Hosted MyCirqa SSO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Hosted MyCirqa SSO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-hosted-mycirqa-sso"></a>Configurar o Hosted MyCirqa SSO

Para configurar o logon único no lado do **Hosted MyCirqa SSO**, é necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do Hosted MyCirqa SSO](mailto:support@isoxford.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

## <a name="create-hosted-mycirqa-sso-test-user"></a>Criar um usuário de teste do Hosted MyCirqa SSO

Nesta seção, você criará uma usuária chamado Brenda Fernandes no Hosted MyCirqa SSO. Trabalhe com [equipe de suporte do Hosted MyCirqa SSO](mailto:support@isoxford.com) para adicionar os usuários na plataforma do Hosted MyCirqa SSO. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Hosted MyCirqa SSO no Painel de Acesso, você deverá ser conectado automaticamente ao Hosted MyCirqa SSO no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Hosted MyCirqa SSO com o Azure AD](https://aad.portal.azure.com/)