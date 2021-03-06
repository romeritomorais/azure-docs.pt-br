---
title: 'Tutorial: Integração do Azure Active Directory com o Origami | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Origami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: fd347f4eb5f77dacc3c9fd61d0e885e9b3ee7959
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095646"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Tutorial: Integração do Azure Active Directory com o Origami

Neste tutorial, você aprenderá a integrar o Origami ao Azure AD (Azure Active Directory).
A integração do Origami ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Origami.
* Você pode permitir que os usuários sejam conectados automaticamente ao Origami (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao Origami, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Origami

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Origami é compatível com o SSO iniciado por **SP**

## <a name="adding-origami-from-the-gallery"></a>Adicionando o Origami por meio da galeria

Para configurar a integração do Origami ao Azure AD, você precisará adicionar o Origami por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Origami por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Origami**, selecione **Origami** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Origami na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Origami, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Origami.

Para configurar e testar o logon único do Azure AD com o Origami, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Origami](#configure-origami-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Origami](#create-origami-test-user)** – para ter um equivalente de Brenda Fernandes no Origami que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Origami, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Origami**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Origami](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do Origami](https://wordpress.org/support/theme/origami) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Origami**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-origami-single-sign-on"></a>Configurar o logon único do Origami

1. Faça logon na conta do Origami com direitos de Administrador.

2. No menu na parte superior, clique em **Administrador**.
   
    ![Configurar o logon único](./media/origami-tutorial/tutorial_origami_51.png)

3. Na página do diálogo Configuração de Logon Único, realize as seguintes etapas:
   
    ![Configurar o logon único](./media/origami-tutorial/tutorial_origami_531.png)

    a. Selecione **Habilitar Logon Único**.

    b. Na caixa de texto **URL da Página de Entrada do Provedor de Identidade**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    c. Na caixa de texto **URL da Página de Saída do Provedor de Identidade**, cole o valor de **URL de Logoff** que você copiou do portal do Azure.

    d. Clique em **Procurar** para carregar o certificado baixado do Portal do Azure.

    e. Clique em **Salvar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Origami.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Origami**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Origami**.

    ![Link do Origami na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-origami-test-user"></a>Criar um usuário de teste do Origami

Nesta seção, você criará um usuário chamado Brenda Fernandes no Origami. 

1. Faça logon na conta do Origami com direitos de Administrador.

2. No menu na parte superior, clique em **Administrador**.
   
    ![Configurar o logon único](./media/origami-tutorial/tutorial_origami_51.png)

3. No diálogo **Usuários e Segurança**, clique em **Usuários**.
   
    ![Configurar o logon único](./media/origami-tutorial/tutorial_origami_54.png)

4. Clique em **Adicionar Novo Usuário**.
   
    ![Configurar o logon único](./media/origami-tutorial/tutorial_origami_55.png)

5. Na caixa de diálogo Adicionar Novo Usuário, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/origami-tutorial/tutorial_origami_56.png)

    a. Na caixa de texto **Nome de Usuário**, insira o email do usuário como **brendafernandes\@contoso.com**.

    b. Na caixa de texto **Senha** , digite uma senha.

    c. Na caixa de texto **Confirmar Senha** , digite a senha novamente.

    d. Na caixa de texto **Nome**, digite o nome do usuário como **Brenda**.

    e. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.

    f. Clique em **Save** (Salvar).
   
    ![Configurar o logon único](./media/origami-tutorial/tutorial_origami_57.png)

6. Atribua **Funções de Usuário** e **Acesso para Cliente** ao usuário. 
   
    ![Configurar o logon único](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Origami no Painel de Acesso, você deverá ser conectado automaticamente ao Origami, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

