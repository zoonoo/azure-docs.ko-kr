---
title: "Azure Active Directory v2.0 인증 라이브러리 | Microsoft Docs"
description: "Azure Active Directory v2.0 끝점에 대한 호환 가능한 클라이언트 라이브러리 및 서버 미들웨어 라이브러리 및 관련된 라이브러리, 원본 및 샘플 링크."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2016
ms.author: skwan;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 9710381787cc1fe570443467b6980616879c503f
ms.openlocfilehash: ab5576d09e5a51cd7ae602d57693558715e12d66
ms.lasthandoff: 02/27/2017


---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0 인증 라이브러리
Azure AD(Azure Active Directory) v2.0 끝점은 산업 표준 OAuth 2.0 및 OpenID Connect 1.0 프로토콜을 지원합니다. V2.0 끝점과 Microsoft 및 기타 조직에서 다양한 라이브러리를 사용할 수 있습니다.

v2.0 끝점을 사용하는 응용 프로그램을 빌드하는 경우, [Microsoft에서 수행한 방법론과 같이][Microsoft-SDL] SDL(보안 개발 수명 주기) 방법론을 수행하는 프로토콜 도메인 전문가에 의해 작성된 라이브러리를 사용하는 것이 좋습니다. 프로토콜에 대한 직접 코딩 지원을 하려는 경우 SDL 방법론을 따르고 각 프로토콜에 대한 표준 사양의 보안 고려 사항에 유의하는 것이 좋습니다.

## <a name="types-of-libraries"></a>라이브러리 유형
Azure AD v2.0은 두 가지 유형의 라이브러리를 사용할 수 있습니다.

* **클라이언트 라이브러리** 네이티브 클라이언트 및 서버는 클라이언트 라이브러리를 사용하여 Microsoft Graph와 같은 리소스를 호출하기 위한 액세스 토큰을 가져옵니다.
* **서버 미들웨어 라이브러리** 웹앱은 사용자 로그인에 서버 미들웨어 라이브러리를 사용합니다. 웹 API는 서버 미들웨어 라이브러리를 사용하여 네이티브 클라이언트 또는 다른 서버에서 전송되는 토큰의 유효성을 검사합니다.

## <a name="library-support"></a>라이브러리 지원
V 2.0 끝점을 사용하는 경우 모든 표준 호환 라이브러리를 선택할 수 있기 때문에 지원하려는 위치를 알아야 합니다. 라이브러리 코드에서 문제 및 기능 요청은 라이브러리 소유자에게 문의합니다. 서비스 측 프로토콜 구현에서 문제 및 기능 요청은 Microsoft로 문의합니다.

라이브러리는 두 지원 범주로 제공됩니다.

* **Microsoft 지원** Microsoft는 이러한 라이브러리에 대한 수정 사항을 제공하고 이러한 라이브러리에 대한 보안 개발 수명 주기 실사를 마쳤습니다.
* **호환 가능** Microsoft는 기본 시나리오에서 이러한 라이브러리를 테스트하고 v2.0 끝점에서 작동하는지를 확인했습니다. Microsoft는 이러한 라이브러리에 대한 수정 사항을 제공하지 않으며 이러한 라이브러리의 검토를 완료하지 않았습니다. 문제 및 기능 요청은 라이브러리의 오픈 소스 프로젝트로 리디렉션되어야 합니다.

v2.0 끝점을 사용하는 라이브러리 목록은 이 문서의 다음 섹션을 참조하세요.

## <a name="microsoft-supported-client-libraries"></a>Microsoft 지원 클라이언트 라이브러리
| 플랫폼 | 라이브러리 이름 | 다운로드 | 소스 코드 | 샘플 |
|:---:|:---:|:---:|:---:|:---:|
| .NET, Windows Store, Xamarin |.NET용 MSAL(Microsoft 인증 라이브러리) |[Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] |[.NET용 MSAL(GitHub)][ClientLib-NET-Repo] |[Windows 데스크톱 네이티브 클라이언트 샘플][ClientLib-NET-Sample] |
| Node.js |Microsoft Azure Active Directory Passport.js 플러그 인 |[Passport-Azure-AD (npm)][ClientLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] |서비스 예정 |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 지원 서버 미들웨어 라이브러리
| 플랫폼 | 라이브러리 이름 | 다운로드 | 소스 코드 | 샘플 |
|:---:|:---:|:---:|:---:|:---:|
| .NET 4.x |ASP.NET용 OWIN OpenID Connect 미들웨어 |[Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] |[Katana Project (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] |[웹앱 샘플][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x |ASP.NET용 OWIN OAuth Bearer 미들웨어 |[Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] |[Katana Project (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] |[웹 API 샘플][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET Core |.NET Core용 OWIN OpenID Connect 미들웨어 |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.Net 보안(GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[웹앱 샘플][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET Core |.NET Core용 OWIN OAuth Bearer 미들웨어 |[Microsoft.AspNetCore.Authentication.OAuth(NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.Net 보안(GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |서비스 예정 |
| Node.js |Microsoft Azure Active Directory Passport.js 플러그 인 |[Passport-Azure-AD (npm)][ServerLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] |[웹앱 샘플][ServerLib-Node-Sample] |

<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>호환 가능한 클라이언트 라이브러리
| 플랫폼 | 라이브러리 이름 | 테스트 버전 | 소스 코드 | 샘플 |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[네이티브 앱 샘플](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[네이티브 앱 샘플](active-directory-v2-devquickstarts-ios.md) |
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [버전 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | 서비스 예정 |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [버전 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | 서비스 예정 |
| Python-Flask |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |[웹앱](https://github.com/Azure-Samples/active-directory-python-flask-graphapi-web-v2) |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |서비스 예정 |

<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>호환 가능한 서버 미들웨어 라이브러리
서비스 예정

## <a name="related-content"></a>관련 콘텐츠
Azure AD v2.0 끝점에 대한 자세한 내용은 [Azure AD 앱 모델 v2.0 개요][AAD-App-Model-V2-Overview]를 참조하세요.

콘텐츠를 구체화하고 모양을 갖추는데 도움을 주려면 이 문서 끝의 Disqus 설명 기능을 사용하여 피드백을 제공하세요.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/

