---
title: Fornecer declarações opcionais para aplicativos do Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Como adicionar declarações personalizadas ou adicionais aos tokens SAML 2,0 e JSON Web tokens (JWT) emitidos pelo Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/08/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 9ea3388cb65b18c093ffff3ec8b8c9f2764ef189
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300061"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Como: fornecer declarações opcionais para seu aplicativo do Azure AD

Os desenvolvedores de aplicativos podem usar declarações opcionais em seus aplicativos do Azure AD para especificar quais declarações eles desejam em tokens enviados para seu aplicativo. 

Você pode usar declarações opcionais para:

- Selecione declarações adicionais para incluir nos tokens para o aplicativo.
- Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
- Adicione e acesse as declarações personalizadas para o aplicativo.

Para obter as listas de declarações padrão, consulte o [token de acesso](access-tokens.md) e a documentação de declarações de [id_token](id-tokens.md) . 

Embora as declarações opcionais tenham suporte nos tokens de formato v 1.0 e v 2.0, bem como nos tokens SAML, elas fornecem a maior parte de seu valor ao mudar de v 1.0 para v 2.0. Um dos objetivos do ponto de [extremidade da plataforma de identidade da Microsoft v 2.0](active-directory-appmodel-v2-overview.md) é um tamanho de token menor para garantir o desempenho ideal dos clientes. Como resultado, várias declarações anteriormente incluídas em tokens de acesso e ID não estão mais presentes nos tokens v2.0 e devem ser solicitadas especificamente, por aplicativo.

**Tabela 1: Aplicabilidade**

| Tipo de Conta | Tokens v1.0 | Tokens v2.0  |
|--------------|---------------|----------------|
| Conta pessoal da Microsoft  | {1&gt;N/A&lt;1}  | Suportado |
| Conta do AD do Azure      | Suportado | Suportado |

## <a name="v10-and-v20-optional-claims-set"></a>conjunto de declarações opcionais v 1.0 e v 2.0

O conjunto de declarações opcionais disponíveis por padrão para uso pelos aplicativos é listado abaixo. Para adicionar declarações opcionais personalizadas para o aplicativo, confira [Extensões de Diretório](#configuring-directory-extension-optional-claims), abaixo. Ao adicionar declarações ao **token de acesso**, as declarações se aplicam aos tokens de acesso solicitados *para* o aplicativo (uma API da Web), não as declarações solicitadas *pelo* aplicativo. Não importa como o cliente acessa sua API, os dados corretos estão presentes no token de acesso que é usado para autenticar em sua API.

> [!NOTE]
> A maioria dessas declarações pode ser incluída em JWTs para tokens v1.0 e v2.0, mas não para tokens SAML, exceto quando indicado na coluna Tipo de Token. As contas de consumidor dão suporte a um subconjunto dessas declarações, marcadas na coluna "tipo de usuário".  Muitas das declarações listadas não se aplicam aos usuários do consumidor (não têm locatários, portanto `tenant_ctry` não tem valor).  

**Tabela 2: conjunto de declarações opcionais v 1.0 e v 2.0**

| {1&gt;Nome&lt;1}                       |  Descrição   | Tipo de token | Tipo de Usuário | {1&gt;Observações&lt;1}  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Hora em que o usuário foi autenticado pela última vez. Confira especificações de OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Região do locatário do recurso | JWT        |           | |
| `home_oid`                 | Para usuários convidados, a ID de objeto do usuário no locatário inicial do usuário.| JWT        |           | |
| `sid`                      | ID da sessão, usada para saída do usuário por sessão. | JWT        |  Contas pessoais e do Azure AD.   |         |
| `platf`                    | Plataforma de dispositivos    | JWT        |           | Restrito aos dispositivos gerenciados que podem verificar o tipo de dispositivo.|
| `verified_primary_email`   | Originado de PrimaryAuthoritativeEmail do usuário      | JWT        |           |         |
| `verified_secondary_email` | Originado de SecondaryAuthoritativeEmail do usuário   | JWT        |           |        |
| `enfpolids`                | IDs de política aplicada. Uma lista de IDs de política que foram avaliadas para o usuário atual. | JWT |  |  |
| `vnet`                     | Informações de especificador de VNET. | JWT        |           |      |
| `fwd`                      | Endereço IP.| JWT    |   | Adiciona o endereço IPv4 original do cliente solicitante (quando dentro de uma VNET) |
| `ctry`                     | O país do usuário | JWT |  | O Azure AD retorna a declaração opcional `ctry` se ela estiver presente e o valor da declaração é um código de país padrão de duas letras, como FR, JP, SZ e assim por diante. |
| `tenant_ctry`              | País do locatário de recursos | JWT | | |
| `xms_pdl`          | Local dos dados preferido   | JWT | | Para locatários de várias regiões geográficas, o local de dados preferencial é o código de três letras mostrando a região geográfica em que o usuário está. Para obter mais informações, consulte a [documentação de Azure ad Connect sobre o local de dados preferencial](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Por exemplo: `APC` para Pacífico Asiático. |
| `xms_pl`                   | Idioma preferido do usuário  | JWT ||O idioma preferido do usuário, se definido. Originado de seu locatário inicial, em cenários de acesso de convidado. Tem o formato II-PP ("en-us"). |
| `xms_tpl`                  | Idioma preferido do locatário| JWT | | O idioma preferido do locatário do recurso, se definido. Com o formato II (“en”). |
| `ztdid`                    | ID de implantação de zero toque | JWT | | A identidade do dispositivo usada para o [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | O email endereçável para este usuário, se o usuário tiver um.  | JWT, SAML | MSA, Azure AD | Esse valor é incluído por padrão, se o usuário é um convidado no locatário.  Para usuários gerenciados (os usuários dentro do locatário), ele deve ser solicitado por essa declaração opcional ou, somente no v 2.0, com o escopo de OpenID.  Para usuários gerenciados, o endereço de email deve ser definido [portal de administração do Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Formatação opcional para declarações de grupo |JWT, SAML| |Usado em conjunto com a configuração GroupMembershipClaims no [manifesto do aplicativo](reference-app-manifest.md), que também deve ser definido. Para obter detalhes, consulte [declarações de grupo](#configuring-groups-optional-claims) abaixo. Para obter mais informações sobre declarações de grupo, consulte [como configurar declarações de grupo](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Status da conta de usuários no locatário. | JWT, SAML | | Se o usuário for um membro do locatário, o valor será `0`. Se eles forem convidado, o valor é `1`. |
| `upn`                      | Declaração UserPrincipalName. | JWT, SAML  |           | Embora essa declaração seja incluída automaticamente, você pode especificá-la como uma declaração opcional para anexar propriedades adicionais a fim de modificar seu comportamento, no caso do usuário convidado.  |

## <a name="v20-specific-optional-claims-set"></a>v 2.0-conjunto de declarações opcionais específicas

Essas declarações são sempre incluídas em tokens do Azure AD v 1.0, mas não são incluídas em tokens v 2.0, a menos que solicitado. Essas declarações são aplicáveis somente para JWTs (tokens de ID e tokens de acesso). 

**Tabela 3: v 2.0-apenas declarações opcionais**

| Declaração JWT     | {1&gt;Nome&lt;1}                            | Descrição                                | {1&gt;Observações&lt;1} |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP com o qual o cliente se conectou.   |       |
| `onprem_sid`  | Identificador de Segurança Local |                                             |       |
| `pwd_exp`     | Hora da Expiração da Senha        | A data e a hora em que a senha expira. |       |
| `pwd_url`     | Alterar URL da Senha             | Uma URL que o usuário pode acessar para alterar sua senha.   |   |
| `in_corp`     | Dentro da Rede Corporativa        | Indica se o cliente está se conectando da rede corporativa. Se não forem, a declaração não será incluída.   |  Baseado nas configurações de [IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips) na Autenticação Multifator.    |
| `nickname`    | Apelido                        | Um nome adicional para o usuário. O apelido é separado de First ou Last Name. | 
| `family_name` | Sobrenome                       | Fornece o sobrenome, sobrenome ou nome de família do usuário, conforme definido no objeto de usuário. <br>"family_name":"Barros" | Com suporte no MSA e no Azure AD   |
| `given_name`  | Nome                      | Fornece o primeiro ou "dado" nome do usuário, conforme definido no objeto de usuário.<br>"given_name": "Davi"                   | Com suporte no MSA e no Azure AD  |
| `upn`         | Nome UPN | Um identificador para o usuário que pode ser usado com o parâmetro username_hint.  Não é um identificador durável para o usuário e não deve ser usado para dados de chave. | Ver [propriedades adicionais](#additional-properties-of-optional-claims) abaixo para a configuração da declaração. |

### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de declarações opcionais

Algumas declarações opcionais podem ser configuradas para alterar o modo como a declaração é retornada. Essas propriedades adicionais são usadas principalmente para ajudar a migração de aplicativos locais com expectativas de dados diferentes (por exemplo, `include_externally_authenticated_upn_without_hash` ajuda com clientes que não podem manipular marcas (`#`) no UPN)

**Tabela 4: valores para configurar declarações opcionais**

| Nome da propriedade  | Nome de Propriedade Adicional | Descrição |
|----------------|--------------------------|-------------|
| `upn`          |                          | Pode ser usada para respostas SAML e JWT e para tokens v1.0 e v2.0. |
|                | `include_externally_authenticated_upn`  | Inclui o UPN de convidado conforme armazenado no locatário do recurso. Por exemplo, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | O mesmo que acima, exceto que as marcas de hash (`#`) são substituídas por sublinhados (`_`), por exemplo `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Exemplo de propriedades adicionais

    ```json
        "optionalClaims": 
         {
             "idToken": [ 
            { 
                      "name": "upn", 
                      "essential": false,
                  "additionalProperties": [ "include_externally_authenticated_upn"]  
                    }
                 ]
        }
    ```

Esse objeto OptionalClaims faz com que o token de ID retornado ao cliente inclua outro UPN com o locatário de início adicional e as informações do locatário do recurso. A declaração de `upn` só será alterada no token se o usuário for um convidado no locatário (que usa um IDP diferente para autenticação). 

## <a name="configuring-optional-claims"></a>Como configurar as declarações opcionais

> [!IMPORTANT]
> Os tokens de acesso **sempre** são gerados usando o manifesto do recurso, não o cliente.  Portanto, na solicitação `...scope=https://graph.microsoft.com/user.read...` o recurso é a API Microsoft Graph.  Assim, o token de acesso é criado usando o manifesto da API Microsoft Graph, não o manifesto do cliente.  Alterar o manifesto para seu aplicativo nunca fará com que os tokens da API de Microsoft Graph pareçam diferentes.  Para validar que as alterações de `accessToken` estão em vigor, solicite um token para seu aplicativo, não para outro aplicativo.  

Você pode configurar declarações opcionais para seu aplicativo por meio da interface do usuário ou do manifesto do aplicativo.

1. Vá para o [Portal do Azure](https://portal.azure.com). Pesquise **Azure Active Directory** e selecione-o.
1. Na seção **gerenciar** , selecione **registros de aplicativo**.
1. Selecione o aplicativo para o qual você deseja configurar declarações opcionais na lista.

**Configurando declarações opcionais por meio da interface do usuário:**

[![mostra como configurar declarações opcionais usando a interface do usuário](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Na seção **gerenciar** , selecione **configuração de token (versão prévia)** .
2. Selecione **Adicionar declaração opcional**.
3. Selecione o tipo de token que você deseja configurar.
4. Selecione as declarações opcionais a serem adicionadas.
5. Clique em **Adicionar**.

**Configurando declarações opcionais por meio do manifesto do aplicativo:**

[![mostra como configurar declarações opcionais usando o manifesto do aplicativo](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Na seção **gerenciar** , selecione **manifesto**. Um editor de manifesto baseado na Web é aberto, permitindo que você edite o manifesto. Opcionalmente, você pode selecionar **Baixar** e editar o manifesto localmente e, em seguida, usar **Carregar** para reaplicá-lo ao seu aplicativo. Para obter mais informações sobre o manifesto do aplicativo, consulte o [artigo noções básicas sobre o manifesto do aplicativo do Azure ad](reference-app-manifest.md).

    A seguinte entrada de manifesto de aplicativo adiciona as declarações opcionais auth_time, IPADDR e UPN aos tokens de ID, acesso e SAML.

    ```json
        "optionalClaims":  
           {
              "idToken": [
                    {
                          "name": "auth_time", 
                          "essential": false
                     }
              ],
              "accessToken": [
                     {
                            "name": "ipaddr", 
                            "essential": false
                      }
              ],
              "saml2Token": [
                      {
                            "name": "upn", 
                            "essential": false
                       },
                       {
                            "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                            "source": "user", 
                            "essential": false
                       }
               ]
           }
    ```

2. Ao terminar, clique em **Salvar**. Agora, as declarações opcionais especificadas serão incluídas nos tokens para seu aplicativo.    


### <a name="optionalclaims-type"></a>Tipo de OptionalClaims

Declara as declarações opcionais solicitadas por um aplicativo. Um aplicativo pode configurar as declarações opcionais a serem retornadas em cada um dos três tipos de tokens (token de ID, token de acesso, token SAML 2) que pode receber do serviço de token de segurança. O aplicativo pode configurar um conjunto de declarações opcionais a serem retornadas em cada tipo de token diferente. A propriedade OptionalClaims da entidade do aplicativo é um objeto OptionalClaims.

**Tabela 5: propriedades do tipo OptionalClaims**

| {1&gt;Nome&lt;1}        | Tipo                       | Descrição                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de ID JWT. |
| `accessToken` | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de acesso JWT. |
| `saml2Token`  | Coleção (OptionalClaim) | As declarações opcionais retornadas no token SAML.   |

### <a name="optionalclaim-type"></a>Tipo de OptionalClaim

Contém uma declaração opcional associada a um aplicativo ou uma entidade de segurança. As propriedades idToken, accessToken e saml2Token do tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) são uma coleção de OptionalClaim.
Caso haja suporte por uma declaração específica, você também poderá modificar o comportamento de OptionalClaim usando o campo AdditionalProperties.

**Tabela 6: propriedades do tipo OptionalClaim**

| {1&gt;Nome&lt;1}                 | Tipo                    | Descrição                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | O nome da declaração opcional.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | A origem (objeto de diretório) da declaração. Há declarações predefinidas e definidas pelo usuário de propriedades de extensão. Se o valor de origem for nulo, a declaração será uma declaração opcional predefinida. Se o valor de origem for um usuário, o valor na propriedade name será a propriedade de extensão do objeto de usuário. |
| `essential`            | Edm.Boolean             | Se o valor for true, a declaração especificada pelo cliente será necessária para garantir uma experiência de autorização sem problemas para a tarefa específica solicitada pelo usuário final. O valor padrão é false.                                                                                                             |
| `additionalProperties` | Coleção (Edm.String) | Propriedades adicionais da declaração. Se uma propriedade existir na coleção, ela modificará o comportamento da declaração opcional especificado na propriedade name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Configurando declarações opcionais de extensão de diretório

Além do conjunto de declarações opcionais padrão, você também pode configurar tokens para incluir extensões. Esse recurso é útil para anexar informações adicionais do usuário que o aplicativo pode usar; por exemplo, um identificador adicional ou uma opção de configuração importante que o usuário configurou. Consulte a parte inferior desta página para obter um exemplo.

> [!NOTE]
> - As extensões de esquema de diretório são um recurso somente do Azure AD, portanto, se o manifesto do aplicativo solicitar uma extensão personalizada e um usuário da MSA fizer logon em seu aplicativo, essas extensões não serão retornadas.

### <a name="directory-extension-formatting"></a>Formatação de extensão de diretório

Ao configurar declarações opcionais de extensão de diretório usando o manifesto do aplicativo, use o nome completo da extensão (no formato: `extension_<appid>_<attributename>`). O `<appid>` deve corresponder à ID do aplicativo que está solicitando a declaração. 

No JWT, essas declarações serão emitidas com o seguinte formato de nome: `extn.<attributename>`.

Em tokens SAML, essas declarações serão emitidas com o seguinte formato de URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Configurando declarações opcionais de grupos

   > [!NOTE]
   > A capacidade de emitir nomes de grupo para usuários e grupos sincronizados a partir do local é visualização pública.

Esta seção aborda as opções de configuração em declarações opcionais para alterar os atributos de grupo usados em declarações de grupo do objectID do grupo padrão para atributos sincronizados do Windows Active Directory local. Você pode configurar declarações opcionais de grupos para seu aplicativo por meio da interface do usuário ou do manifesto do aplicativo.

> [!IMPORTANT]
> Para obter mais detalhes, incluindo advertências importantes para a visualização pública de declarações de grupo de atributos locais, consulte [Configurar declarações de grupo para aplicativos com o Azure ad](../hybrid/how-to-connect-fed-group-claims.md).

**Configurar declarações opcionais de grupos por meio da interface do usuário:**
1. Entre no [Portal do Azure](https://portal.azure.com)
1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página
1. Selecione **Azure Active Directory** no menu à esquerda
1. Na seção **gerenciar** , selecione **registros de aplicativo**
1. Selecione o aplicativo para o qual você deseja configurar declarações opcionais na lista
1. Na seção **gerenciar** , selecione **configuração de token (versão prévia)**
2. Selecione **Adicionar declaração de grupos**
3. Selecione os tipos de grupo a serem retornados (**todos os grupos, grupo**de **segurança**ou **DirectoryRole**). A opção **todos os grupos** inclui o grupo de **segurança**, **DirectoryRole**e **DistributionList**
4. Opcional: clique nas propriedades do tipo de token específico para modificar o valor da declaração grupos para conter os atributos do grupo local ou para alterar o tipo de declaração para uma função
5. Clique em **Salvar**

**Configurar declarações opcionais de grupos por meio do manifesto do aplicativo:**
1. Entre no [Portal do Azure](https://portal.azure.com)
1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página
1. Selecione **Azure Active Directory** no menu à esquerda
1. Selecione o aplicativo para o qual você deseja configurar declarações opcionais na lista
1. Na seção **gerenciar** , selecione **manifesto**
3. Adicione a seguinte entrada usando o editor de manifesto:

   Os valores válidos são:

   - "All" (essa opção inclui o The Securitylist, DirectoryRole e DistributionList)
   - SecurityGroup
   - DirectoryRole

   Por exemplo:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Por padrão, ObjectIDs de grupo serão emitidas no valor de declaração de grupo.  Para modificar o valor da declaração para conter os atributos do grupo local ou para alterar o tipo de declaração para função, use a configuração OptionalClaims da seguinte maneira:

3. Definir declarações opcionais de configuração de nome de grupo.

   Se você quiser que os grupos no token contenham os atributos do grupo do AD local na seção declarações opcionais, especifique a qual tipo de token a declaração opcional deve ser aplicada, o nome da declaração opcional solicitada e todas as propriedades adicionais desejadas.  Vários tipos de token podem ser listados:

   - Token para o token de ID de OIDC
   - accessToken para o token de acesso do OAuth/OIDC
   - Saml2Token para tokens SAML.

   > [!NOTE]
   > O tipo Saml2Token aplica-se aos tokens de formato SAML 1.1 e SAML 2.0

   Para cada tipo de token relevante, modifique a declaração de grupos para usar a seção OptionalClaims no manifesto. O esquema OptionalClaims é o seguinte:

    ```json
       {
       "name": "groups",
       "source": null,
       "essential": false,
       "additionalProperties": []
       }
    ```

   | Esquema de declarações opcional | {1&gt;Valor&lt;1} |
   |----------|-------------|
   | **nomes** | Deve ser "grupos" |
   | **original** | Não usado. Omitir ou especificar nulo |
   | **importante** | Não usado. Omitir ou especificar false |
   | **AdditionalProperties** | Lista de propriedades adicionais.  As opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Somente um de "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" é necessário em um adicional.  Se mais de um estiver presente, o primeiro será usado e todos os outros serão ignorados.

   Alguns aplicativos exigem informações de grupo sobre o usuário na declaração de função.  Para alterar o tipo de declaração para de uma declaração de grupo para uma declaração de função, adicione "emit_as_roles" a propriedades adicionais.  Os valores de grupo serão emitidos na declaração de função.

   > [!NOTE]
   > Se "emit_as_roles" for usado, as funções de aplicativo configuradas que o usuário está atribuído não aparecerão na declaração de função

**Exemplos:**

1) Emitir grupos como nomes de grupo nos tokens de acesso OAuth no formato dnsDomainName\sAMAccountName

    
    **Configuração da interface do usuário:**

    [![mostra como configurar declarações opcionais usando a interface do usuário](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Entrada do manifesto do aplicativo:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Emitir nomes de grupos a serem retornados no formato netbiosDomain\sAMAccountName como a declaração de funções em tokens de ID SAML e OIDC

    **Configuração da interface do usuário:**

    [![mostra como configurar declarações opcionais usando a interface do usuário](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Entrada do manifesto do aplicativo:**
    
    ```json
        "optionalClaims": {
        "saml2Token": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }],
        "idToken": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }]
    ``` 
     

## <a name="optional-claims-example"></a>Exemplo de declarações opcional

Nesta seção, você pode examinar um cenário para ver como usar o recurso opcional de declarações para o aplicativo.
Há várias opções disponíveis para atualizar as propriedades na configuração de identidade do aplicativo para habilitar e configurar declarações opcionais:
-    Você pode usar a interface do usuário de **configuração de token (versão prévia)** (Veja o exemplo abaixo)
-    Você pode usar o **manifesto** (Veja o exemplo abaixo). Primeiro leia o [documento Noções básicas sobre o manifesto do aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) para obter uma introdução ao manifesto.
-   Também é possível escrever um aplicativo que usa a [API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) para atualizar seu aplicativo. O tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) no guia de referência da API Microsoft Graph pode ajudá-lo a configurar as declarações opcionais.

**Exemplo:** No exemplo a seguir, você usará a interface do usuário de **configuração do token (versão prévia)** e o **manifesto** para adicionar declarações opcionais aos tokens de acesso, ID e SAML destinados ao seu aplicativo. Diferentes declarações opcionais serão adicionadas a cada tipo de token que o aplicativo pode receber:
-    Os tokens de ID agora contêm o UPN para usuários federados no formato completo (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    Os tokens de acesso que outros clientes solicitam para esse aplicativo agora incluirão a declaração de auth_time
-    Agora os tokens SAML contêm a extensão do esquema de diretório skypeId (neste exemplo, a ID de aplicativo para esse aplicativo é ab603c56068041afb2f6832e2a17e237). Os tokens SAML vão expor a ID do Skype como `extension_skypeId`.

**Configuração da interface do usuário:**

1. Entre no [Portal do Azure](https://portal.azure.com)

1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página.

1. Selecione **Azure Active Directory** no menu à esquerda.

1. Na seção **gerenciar** , selecione **registros de aplicativo**.

1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e clique nele.

1. Na seção **gerenciar** , clique em **configuração de token (versão prévia)** .

1. Selecione **Adicionar declaração opcional**, selecione o tipo de token de **ID** , selecione **UPN** na lista de declarações e clique em **Adicionar**.

1. Selecione **Adicionar declaração opcional**, selecione o tipo de token de **acesso** , selecione **auth_time** na lista de declarações e clique em **Adicionar**.

1. Na tela Visão geral da configuração do token, clique no ícone de lápis ao lado de **UPN**, clique na alternância **autenticada externamente** e, em seguida, clique em **salvar**.

1. Selecione **Adicionar declaração opcional**, selecione o tipo de token **SAML** , selecione **Extn. SkypeID** na lista de declarações (aplicável somente se você tiver criado um objeto de usuário do Azure AD chamado SkypeID) e, em seguida, clique em **Adicionar**.

    [![mostra como configurar declarações opcionais usando a interface do usuário](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configuração do manifesto:**
1. Entre no [portal do Azure](https://portal.azure.com).
1. Depois de autenticado, escolha seu locatário do Azure AD selecionando-o no canto superior direito da página.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e clique nele.
1. Na seção **gerenciar** , clique em **manifesto** para abrir o editor de manifesto embutido.
1. Você pode editar diretamente o manifesto usando esse editor. O manifesto segue o esquema para a [entidade de aplicativo](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)e formata automaticamente o manifesto depois de salvo. Novos elementos serão adicionados para o `OptionalClaims` propriedade.

    ```json
            "optionalClaims": {
                "idToken": [ 
                     { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                     }
                     ],
                "accessToken": [ 
                      {
                        "name": "auth_time", 
                        "essential": false
                      }
                     ],
            "saml2Token": [ 
                  { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
                  }
                 ]
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
