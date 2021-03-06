---
title: 'Tutorial: Integração do Azure Active Directory ao Lucidchart | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Lucidchart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3eace60445dc9d52f9690da74360282efbb4cbe5
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291204"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Lucidchart

Neste tutorial, você aprenderá como integrar o Lucidchart ao Azure AD (Azure Active Directory). Ao integrar o Lucidchart ao Azure AD, você poderá:

* Controlar quem tem acesso ao Lucidchart no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Lucidchart com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisites

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Lucidchart.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Lucidchart dá suporte ao SSO iniciado por **SP**
* O Lucidchart dá suporte ao provisionamento de usuário **just-in-time**
* Após configurar o Lucidchart, você pode impor controles de sessão, que protegem o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-lucidchart-from-the-gallery"></a>Adicionar o Lucidchart da galeria

Para configurar a integração do Lucidchart ao Azure AD, você precisará adicionar o Lucidchart da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Lucidchart** na caixa de pesquisa.
1. Selecione **Lucidchart** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-lucidchart"></a>Configurar e testar o logon único do Azure AD para o Lucidchart

Configure e teste o SSO do Azure AD com o Lucidchart usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Lucidchart.

Para configurar e testar o SSO do Azure AD com o Lucidchart, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Lucidchart](#configure-lucidchart-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Lucidchart](#create-lucidchart-test-user)** – para ter um equivalente de B.Fernandes no Lucidchart que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Lucidchart**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

   No **URL de logon** caixa de texto, digite uma URL como: `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Lucidchart**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Lucidchart.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Lucidchart**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-lucidchart-sso"></a>Configurar o SSO do Lucidchart

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Lucidchart como administrador.

2. No menu na parte superior, clique em **Equipe**.

    ![Equipe](./media/lucidchart-tutorial/ic791190.png "Equipe")

3. Clique em **Aplicativos \> Gerenciar SAML**.

    ![Gerenciar SAML](./media/lucidchart-tutorial/ic791191.png "Gerenciar SAML")

4. Na página do diálogo **Configurações de Autenticação SAML** , realize as seguintes etapas:

    a. Selecione **Habilitar Autenticação SAML** e clique em **Opcional**.

    ![Configurações de Autenticação SAML](./media/lucidchart-tutorial/ic791192.png "Configurações de Autenticação SAML")

    b. Na caixa de texto **Domínio**, digite seu domínio e clique em **Alterar Certificado**.

    ![Alterar Certificado](./media/lucidchart-tutorial/ic791193.png "Alterar Certificado")

    c. Abra o arquivo de metadados baixado, copie o conteúdo e cole-o na caixa de texto **Carregar Metadados** .

    ![Carregar Metadados](./media/lucidchart-tutorial/ic791194.png "Carregar Metadados")

    d. Selecione **Adicionar Automaticamente Novos Usuários à Equipe** e clique em **Salvar Alterações**.

    ![Salvar alterações](./media/lucidchart-tutorial/ic791195.png "Salvar alterações")

### <a name="create-lucidchart-test-user"></a>Criar um usuário de teste do Lucidchart

Não há nenhum item de ação para a configuração de provisionamento de usuário para o Lucidchart.  Quando um usuário atribuído tenta fazer logon no Lucidchart usando o painel de acesso, o Lucidchart verifica se o usuário existe.  

Se não houver conta de usuário ainda, ela é criada automaticamente pelo Lucidchart.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Lucidchart no Painel de Acesso, você deverá ser conectado automaticamente ao Lucidchart, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Lucidchart com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Lucidchart com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
