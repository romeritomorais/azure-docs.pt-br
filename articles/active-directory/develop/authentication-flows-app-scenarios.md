---
title: Cenários de aplicativo e fluxos de autenticação da plataforma de identidade da Microsoft | Azure
description: Saiba mais sobre cenários de aplicativos para a plataforma de identidade da Microsoft, incluindo autenticação de identidades, aquisição de tokens e chamada de APIs protegidas.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 89bafeb077fc83f4f3165d591006831bf8287875
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190476"
---
# <a name="authentication-flows-and-application-scenarios"></a>Fluxos de autenticação e cenários de aplicativos

O ponto de extremidade da plataforma de identidade da Microsoft (v2.0) dá suporte à autenticação para diferentes tipos de arquiteturas de aplicativo modernas. Todas elas são baseadas nos protocolos padrão da indústria [OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md).  Usando as [bibliotecas de autenticação da plataforma de identidade da Microsoft](reference-v2-libraries.md), os aplicativos autenticam identidades e adquirem tokens para acessar APIs protegidas.

Este artigo descreve os fluxos de autenticação e os cenários de aplicativo em que eles são usados:

- [Fluxos de autenticação e cenários de aplicativos compatíveis](#scenarios-and-supported-authentication-flows).
- [Cenários de aplicativos e plataformas e linguagens compatíveis](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Categorias de aplicativo

Os tokens podem ser adquiridos de vários tipos de aplicativos, incluindo:

- Aplicativos Web
- Aplicativos móveis
- Aplicativos da área de trabalho
- APIs da Web

Os tokens também podem ser adquiridos de aplicativos em execução em dispositivos que não têm um navegador ou que estão em execução no IoT.

Os aplicativos podem ser categorizados como na lista a seguir:

- [Recursos protegidos vs. aplicativos cliente](#protected-resources-vs-client-applications): alguns cenários são sobre a proteção de recursos, como aplicativos Web ou APIs Web. Outros cenários são sobre adquirir um token de segurança para chamar uma API Web protegida.
- [Com usuários ou sem usuários](#with-users-or-without-users): alguns cenários envolvem um usuário conectado, mas outros, como cenários de daemon, não envolvem um usuário.
- [Aplicativos cliente de página única, cliente público e confidencial](#single-page-public-client-and-confidential-client-applications): esses tipos são três grandes categorias de aplicativos. Cada uma é usada com diferentes bibliotecas e objetos.
- [Público-alvo](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): os fluxos de autenticação disponíveis diferem de acordo com o público-alvo. Alguns fluxos estão disponíveis apenas para contas corporativas ou de estudante. Alguns estão disponíveis para contas corporativas ou de estudante e também para contas Microsoft pessoais. O público permitido depende dos fluxos de autenticação.
- [Fluxos OAuth 2,0 com suporte](#scenarios-and-supported-authentication-flows): os fluxos de autenticação são usados para implementar os cenários de aplicativo que estão solicitando tokens. Não há um mapeamento individualizado entre cenários de aplicativos e fluxos de autenticação.
- [Plataformas com suporte](#scenarios-and-supported-platforms-and-languages): nem todos os cenários de aplicativo estão disponíveis para cada plataforma.

### <a name="protected-resources-vs-client-applications"></a>Recursos protegidos versus aplicativos cliente

Os cenários de autenticação envolvem duas atividades:

- **Adquirindo tokens de segurança para uma API Web protegida**: Recomendamos que você use [bibliotecas de cliente com suporte da Microsoft](reference-v2-libraries.md#microsoft-supported-client-libraries) para adquirir tokens, especialmente a família de MSAL (biblioteca de autenticação da Microsoft).
- **Proteger uma API da Web ou um aplicativo Web**: um desafio de proteger um recurso de aplicativo Web ou API da Web é validar o token de segurança. Em algumas plataformas, a Microsoft oferece [bibliotecas de middleware](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Com usuários ou sem usuários

A maioria dos cenários de autenticação adquire tokens em nome de usuários conectados.

![Cenários com usuários](media/scenarios/scenarios-with-users.svg)

No entanto, também há cenários de aplicativos daemon, em que os aplicativos adquirem tokens em nome deles próprios, sem usuário.

![Cenários com aplicativos daemon](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Aplicativos de página única, aplicativos cliente públicos e aplicativos cliente confidenciais

Os tokens de segurança podem ser adquiridos de vários tipos de aplicativos. Esses aplicativos tendem a ser divididos em três categorias:

- **Aplicativos de página única**: também conhecidos como spas, esses aplicativos são aplicativos Web nos quais os tokens são adquiridos de um aplicativo JavaScript ou TypeScript em execução no navegador. Muitos aplicativos modernos têm um aplicativo de página única front-end que é escrito principalmente em JavaScript. O aplicativo geralmente usa uma estrutura como Angular, React ou Vue. MSAL.js é a única biblioteca de autenticação da Microsoft que dá suporte a aplicativos de página única.

- **Aplicativos cliente públicos**: estes aplicativos sempre entram em usuários:
  - Aplicativos da área de trabalho que chamam APIs Web em nome do usuário conectado
  - Aplicativos móveis
  - Aplicativos em execução em dispositivos que não têm um navegador, assim como aqueles em execução em IoT

  Esses aplicativos são representados pela classe [PublicClientApplication](/dotnet/api/microsoft.identity.client.publicclientapplication) da MSAL. Para obter mais informações, consulte [cliente público e aplicativos cliente confidenciais](msal-client-applications.md).

- **Aplicativos cliente confidenciais**:
  - Aplicativos Web que chamam uma API Web
  - APIs Web que chamam uma API Web
  - Aplicativos daemon, mesmo quando implementados como um serviço de console, como um daemon no Linux ou um serviço Windows

  Esses tipos de aplicativos usam a classe [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplication). Para obter mais informações, consulte [cliente público e aplicativos cliente confidenciais](msal-client-applications.md).

## <a name="application-scenarios"></a>Cenários de aplicativos

O ponto de extremidade da plataforma de identidade da Microsoft dá suporte à autenticação para diferentes arquiteturas de aplicativo:

- Aplicativos de página única
- Aplicativos Web
- APIs da Web
- Aplicativos móveis
- Aplicativos nativos
- Aplicativos Daemon
- Aplicativos do lado do servidor

Os aplicativos usam fluxos de autenticação diferentes para conectar usuários e obter tokens para chamar APIs protegidas.

### <a name="a-single-page-application"></a>Um aplicativo de página única

Muitos aplicativos Web modernos são criados como aplicativos de página única do lado do cliente. Esses aplicativos usam JavaScript ou uma estrutura de aplicativo de página única como angular, Vue. js e reaja. js. Esses aplicativos são executados em um navegador da Web.

Suas características de autenticação diferem dos aplicativos Web tradicionais do lado do servidor. Usando a plataforma de identidade da Microsoft, aplicativos de página única podem conectar usuários e obter tokens para acessar serviços de back-end ou APIs Web.

![Um aplicativo de página única](media/scenarios/spa-app.svg)

Para obter mais informações, confira [Aplicativos de página única](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>Um aplicativo Web que está conectando um usuário

![Um aplicativo Web que conecta um usuário](media/scenarios/scenario-webapp-signs-in-users.svg)

Para proteger um aplicativo Web que está conectando um usuário:

- Se desenvolver no .NET, você usará o ASP.NET ou o ASP.NET Core com o middleware ASP.NET Open ID Connect. A proteção de um recurso envolve validar o token de segurança, o que é feito pela biblioteca [Extensões IdentityModel para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki), não por bibliotecas MSAL.

- Se desenvolve em Node.js, você usa o Passport.js.

Para obter mais informações, confira [Aplicativo Web que conecta os usuários](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Um aplicativo Web que conecta um usuário e chama uma API Web em nome desse usuário

![Um aplicativo Web chamando APIs Web](media/scenarios/web-app.svg)

Para chamar uma API Web de um aplicativo Web em nome de um usuário, use a classe **ConfidentialClientApplication** da MSAL. Você usará o fluxo de código de autorização e armazenará os tokens adquiridos no cache de token. Quando necessário, o MSAL atualiza os tokens e o controlador adquire silenciosamente os tokens do cache.

Para obter mais informações, confira [Um aplicativo Web que chama APIs Web](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Um aplicativo da área de trabalho que chama uma API Web em nome de um usuário conectado

Para que um aplicativo da área de trabalho chame uma API Web que conecta usuários, você usará os métodos de aquisição de tokens interativos da classe **PublicClientApplication** da MSAL. Com esses métodos interativos, é possível controlar a experiência de interface do usuário de entrada. A MSAL usa um navegador da Web para essa interação.

![Um aplicativo da área de trabalho que chama uma API Web](media/scenarios/desktop-app.svg)

Há outra possibilidade para aplicativos hospedados no Windows em execução em computadores ingressados em um domínio do Windows ou ingressados no Azure AD (Azure Active Directory). Esses aplicativos podem adquirir um token silenciosamente usando a [Autenticação Integrada do Windows](https://aka.ms/msal-net-iwa).

Os aplicativos em execução em um dispositivo sem um navegador continuam podendo chamar uma API em nome de um usuário. Para autenticar, o usuário precisa entrar em outro dispositivo que tem um navegador da Web. Este cenário requer que você use o [fluxo de código do dispositivo](https://aka.ms/msal-net-device-code-flow).

![Fluxo de código do dispositivo](media/scenarios/device-code-flow-app.svg)

Embora não recomendemos que você o use, o [fluxo de nome de usuário/senha](https://aka.ms/msal-net-up) está disponível em aplicativos cliente públicos. Esse fluxo ainda é necessário em alguns cenários, tais como o DevOps.

Mas o uso desse fluxo restringe seus aplicativos. Por exemplo, os aplicativos não podem entrar em um usuário que precise usar a autenticação multifator ou o acesso condicional. Seus aplicativos também não se beneficiam do logon único.

A autenticação com nome de usuário/senha vai contra os princípios da autenticação moderna e só é oferecido por ser herdado.

Em aplicativos de área de trabalho, se você quiser que o cache de token persista, personalize a [serialização de cache de token](https://aka.ms/msal-net-token-cache-serialization). Ao implementar a [serialização de cache de token duplo](https://aka.ms/msal-net-dual-cache-serialization), você pode usar caches de token compatíveis com versões anteriores e de encaminhamento. Esses tokens dão suporte a gerações anteriores de bibliotecas de autenticação. Bibliotecas específicas incluem a ADAL.NET (Biblioteca de Autenticação do Azure AD para .NET) versão 3 e versão 4.

Para obter mais informações, confira [Aplicativo da área de trabalho que chama APIs Web](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Um aplicativo móvel que chama uma API Web em nome de um usuário interativo

Semelhante a um aplicativo da área de trabalho, um aplicativo móvel chama os métodos de aquisição de token interativo da classe **PublicClientApplication** da MSAL para adquirir um token para chamar uma API Web.

![Um aplicativo móvel que chama uma API Web](media/scenarios/mobile-app.svg)

A MSAL iOS e a MSAL Android usam, por padrão, o navegador da Web do sistema. No entanto, você pode instruí-lo a usar em vez disso a exibição da Web inserida. Há especificidades que dependem da plataforma móvel: Plataforma Universal do Windows (UWP), iOS ou Android.

Alguns cenários, como aqueles que envolvem o acesso condicional relacionado a uma ID de dispositivo ou um registro de dispositivo, exigem que um agente seja instalado no dispositivo. Alguns exemplos de agentes incluem o Portal da Empresa Microsoft no Android e o Microsoft Authenticator no Android e no iOS. MSAL agora pode interagir com agentes. Para obter mais informações, consulte [aproveitando os agentes no Android e no Ios](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Para obter mais informações, confira [Aplicativo móvel que chama APIs Web](scenario-mobile-overview.md).

> [!NOTE]
> Seu aplicativo móvel que usa MSAL.iOS, MSAL.Android ou MSAL.NET no Xamarin pode ter políticas de proteção de aplicativo aplicadas a ele. Por exemplo, as políticas podem impedir que um usuário copie texto protegido. O aplicativo móvel é gerenciado pelo Intune e reconhecido pelo Intune como um aplicativo gerenciado. Para obter mais informações, consulte [visão geral do SDK do aplicativo Microsoft Intune](https://docs.microsoft.com/intune/app-sdk).
>
> O [SDK do aplicativo do Intune](https://docs.microsoft.com/intune/app-sdk-get-started) é separado das bibliotecas MSAL e interage com o Azure AD por conta própria.

### <a name="a-protected-web-api"></a>Uma API Web protegida

Você pode usar o ponto de extremidade da plataforma de identidade da Microsoft para proteger serviços Web como a API Web RESTful do seu aplicativo. Uma API Web protegida é chamada usando um token de acesso. O token protege os dados da API e autentica as solicitações de entrada. O chamador de uma API Web acrescenta um token de acesso ao cabeçalho de autorização de uma solicitação HTTP.

Se você quiser proteger sua API Web do ASP.NET Core ou ASP.NET, será necessário validar o token de acesso. Para essa validação, você usará o middleware JWT ASP.NET. A validação é feita pela biblioteca [Extensões IdentityModel para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki), não por MSAL.NET.

Para obter mais informações, confira [API Web protegida](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Uma API Web que chama outra API Web em nome de um usuário

Para que a API Web ASP.NET ou ASP.NET Core protegida chame outra API Web em nome de um usuário, o aplicativo precisa adquirir um token para a API Web downstream. Para adquirir um token, seu aplicativo chama o método [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) da classe **ConfidentialClientApplication** . Essas chamadas também são denominadas chamadas *serviço a serviços* . As APIs Web que chamam outras APIs Web também precisam fornecer serialização de cache personalizada.

  ![Uma API Web que chama outra API Web](media/scenarios/web-api.svg)

Para obter mais informações, confira [API Web que chama APIs Web](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Um aplicativo daemon que chama uma API Web no nome do daemon

Os aplicativos com processos de longa duração ou que operam sem interação com um usuário também precisam de uma maneira de acessar APIs Web protegidas. Esse aplicativo pode autenticar e obter tokens usando a identidade do aplicativo. O aplicativo provar a própria identidade usando um certificado ou o segredo do cliente.

É possível codificar tais aplicativos daemon que adquirem um token para o aplicativo chamador pelo uso dos métodos de aquisição de **credenciais de cliente** da classe [ConfidentialClientApplication](https://aka.ms/msal-net-client-credentials) da MSAL. Esses métodos exigem que o aplicativo chamador tenha registrado um segredo com o Azure AD. Em seguida, o aplicativo compartilha o segredo com o daemon chamado. Exemplos desses segredos incluem senhas de aplicativo, declaração de certificado ou asserção de cliente.

![Um aplicativo daemon chamado por outros aplicativos e APIs](media/scenarios/daemon-app.svg)

Para obter mais informações, confira [Aplicativo daemon que chama APIs Web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Cenários e fluxos de autenticação com suporte

Os cenários que envolvem a aquisição de tokens também são mapeados para os fluxos de autenticação do OAuth 2,0. Para obter mais informações, consulte [protocolos OAuth 2,0 e OpenID Connect na plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Cenário</th> <th>Passo a passo detalhado do cenário</th> <th>Fluxo e concessão do OAuth 2.0</th> <th>Público</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Aplicativo de página única</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implícito</a></td>
   <td>Contas corporativas ou de estudante, contas pessoais e Microsoft Azure AD B2C (Azure Active Directory B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Aplicativo Web que conecta os usuários</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de autorização</a></td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Um aplicativo Web que chama as APIs Web</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de autorização</a></td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Um aplicativo da área de trabalho que chama as APIs Web</a></td>
   <td>Interativo usando o <a href="v2-oauth2-auth-code-flow.md">Código de Autorização</a> com PKCE</td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td>Autenticação integrada do Windows</td>
   <td>Contas corporativas ou de estudante</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Senha de proprietário do recurso</a></td>
   <td>Contas corporativas ou de estudante e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Código do dispositivo</a></td>
   <td>Contas corporativas ou de estudante</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Um aplicativo móvel que chama as APIs Web</a></td>
   <td>Interativo usando o <a href="v2-oauth2-auth-code-flow.md">Código de Autorização</a> com PKCE</td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Senha de proprietário do recurso</a></td>
   <td>Contas corporativas ou de estudante e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Um aplicativo daemon que chama as APIs Web</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Credenciais do cliente</a></td>
   <td>Permissões somente de aplicativo sem usuário e usadas somente em organizações do Azure AD</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Uma API Web que chama as APIs Web</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Em nome de</a></td>
   <td>Contas corporativas ou de estudante e contas pessoais</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Cenários e plataformas e idiomas com suporte

As bibliotecas de autenticação da Microsoft dão suporte a várias plataformas:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- iOS nativo
- macOS
- Android nativo
- Java
- Python

Você também pode usar várias linguagens para compilar seus aplicativos.

> [!NOTE]
> Alguns tipos de aplicativos não estão disponíveis em todas as plataformas.

Na coluna Windows da tabela a seguir, cada vez que o .NET Core é mencionado, o .NET Framework também é possível. Esse último é omitido para evitar aglomeração na tabela.

|Cenário  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Aplicativo de página única](scenario-spa-overview.md) <br/>[![Aplicativo de página única](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Aplicativo Web que conecta os usuários](scenario-web-app-sign-user-overview.md) <br/>[![Aplicativo Web que conecta os usuários](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Aplicativo Web que chama as APIs Web](scenario-web-app-call-api-overview.md) <br/> <br/>[![Aplicativo Web que chama as APIs Web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [Aplicativo da área de trabalho que chama APIs Web](scenario-desktop-overview.md) <br/> <br/>[![Aplicativo da área de trabalho que chama APIs Web](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![Fluxo de código de trabalho](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS / Objective C ou swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Aplicativo móvel que chama as APIs Web](scenario-mobile-overview.md) <br/> [![Aplicativo móvel que chama as APIs Web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Objective C ou swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [Aplicativo daemon](scenario-daemon-overview.md) <br/> [![Aplicativo daemon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [API Web que chama as APIs Web](scenario-web-api-call-api-overview.md) <br/><br/> [![API Web que chama as APIs Web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Para obter mais informações, consulte [bibliotecas com suporte da Microsoft por sistema operacional/idioma](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [noções básicas de autenticação](authentication-scenarios.md) e [tokens de acesso da plataforma de identidade da Microsoft](access-tokens.md).
