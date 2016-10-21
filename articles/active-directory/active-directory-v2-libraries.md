<properties
   pageTitle="Azure Active Directory v2.0 라이브러리 | Microsoft Azure"
   description="Azure Active Directory v2.0 끝점에 대해 관련된 라이브러리/원본/샘플 링크와 함께 모든 호환 가능한 클라이언트 라이브러리 및 서버 미들웨어 라이브러리의 목록을 제공합니다."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/26/2016"
   ms.author="skwan;bryanla"/>


# Azure AD(Active Directory) v2.0 및 인증 라이브러리
Azure AD v2.0 끝점은 산업 표준 OAuth 2.0 및 OpenID Connect 1.0 프로토콜을 지원합니다. Microsoft 및 타사에서 다양한 라이브러리를 v2.0 끝점과 함께 사용할 수 있습니다.

v2.0 끝점을 사용하는 응용 프로그램을 빌드하는 경우 [Microsoft에서 수행한 방법론과 같이](Microsoft-SDL) SDL(보안 개발 수명 주기) 방법론을 수행하는 프로토콜 도메인 전문가에 의해 작성된 라이브러리를 사용하는 것이 좋습니다. 프로토콜에 대한 직접 코딩 지원을 하려는 경우 SDL을 따르고 각 프로토콜에 대한 표준 사양에서 찾은 보안 고려 사항을 신중하게 관찰하는 것이 좋습니다.

## 라이브러리 유형
V 2.0을 사용하는 라이브러리는 다음과 같이 두 종류가 있습니다.

- **클라이언트 라이브러리**: 클라이언트 라이브러리는 Microsoft Graph와 같이 리소스를 호출하는 액세스 토큰을 가져오기 위해 네이티브 클라이언트 및 서버에 사용됩니다.
- **서버 미들웨어 라이브러리**: 서버 미들웨어 라이브러리는 사용자가 로그인하기 위해 웹 응용 프로그램에서 사용되고 네이티브 클라이언트 또는 다른 서버에서 보낸 토큰의 유효성을 검사하기 위해 Web API에서 사용됩니다.

## 라이브러리 지원
V 2.0 끝점을 사용 하는 경우 모든 표준 호환 라이브러리를 선택할 수 있기 때문에 지원하려는 위치를 알아야 합니다. 라이브러리 코드에서 문제 및 기능 요청은 라이브러리 소유자에게 전송됩니다. 서비스 측 프로토콜 구현에서 문제 및 기능 요청은 Microsoft로 전송됩니다.

라이브러리는 두 지원 범주로 제공됩니다.

- **Microsoft 지원**: Microsoft는 이러한 라이브러리에 대한 수정 사항을 제공합니다. Microsoft는 이러한 라이브러리에 대한 보안 개발 수명 주기 실사를 마쳤습니다.
- **호환 가능**: Microsoft는 기본 시나리오에서 라이브러리 집합을 테스트하고 v2.0 끝점에서 작동하는지를 확인했습니다. Microsoft는 이러한 라이브러리에 대한 수정 사항을 제공하지 않으며 이러한 라이브러리의 검토를 완료하지 않았습니다. 문제 및 기능 요청은 라이브러리의 오픈 소스 프로젝트로 리디렉션되어야 합니다.

v2.0 끝점을 사용하는 라이브러리 목록은 다음 섹션을 참조하세요.

## Microsoft 지원되는 클라이언트 라이브러리
| 플랫폼| 라이브러리 이름| 다운로드 | 소스 코드 | 샘플 |
| :-: | :-: | :-: | :-: | :-: |
| .NET, Windows Store, Xamarin | .NET용 MSAL(Microsoft 인증 라이브러리) | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [.NET용 MSAL(GitHub)][ClientLib-NET-Repo] | [Windows 데스크톱 네이티브 클라이언트 샘플][ClientLib-NET-Sample] |
| Node.js | Microsoft Azure Active Directory Passport.js 플러그 인 | [Passport-Azure-AD(npm)][ClientLib-Node-Lib] | [Passport-Azure-AD(GitHub)][ClientLib-Node-Repo] | 서비스 예정 |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## Microsoft 지원되는 서버 미들웨어 라이브러리
| 플랫폼| 라이브러리 이름| 다운로드 | 소스 코드 | 샘플 |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4.x | ASP.NET용 OWIN OpenID Connect 미들웨어 | [Microsoft.Owin.Security.OpenIdConnect(NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [Katana Project(CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [웹앱 샘플][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x | ASP.Net용 OWIN OAuth Bearer 미들웨어 | [Microsoft.Owin.Security.OAuth(NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [Katana Project(CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [웹 API 샘플][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET Core | .Net Core용 OWIN OpenID Connect 미들웨어 | [Microsoft.AspNetCore.Authentication.OpenIdConnect(NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [ASP.Net 보안(GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [웹앱 샘플][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET Core | .Net Core용 OWIN OAuth Bearer 미들웨어 | [Microsoft.AspNetCore.Authentication.OAuth(NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [ASP.Net 보안(GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | 서비스 예정 |
| Node.js | Microsoft Azure Active Directory Passport.js 플러그 인 | [Passport-Azure-AD(npm)][ServerLib-Node-Lib] | [Passport-Azure-AD(GitHub)][ServerLib-Node-Repo] | [웹앱 샘플][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .Net | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .Net (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## 호환 가능한 클라이언트 라이브러리
| 플랫폼| 이름 | 테스트 버전 | 소스 코드 | 샘플 |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0\.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [네이티브 앱 샘플](active-directory-v2-devquickstarts-android.md) |
| iOS | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1\.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [네이티브 앱 샘플](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Hello.js](https://adodson.com/hello.js/) | 1\.13.5 | [Hello.js](https://github.com/MrSwitch/hello.js) | 서비스 예정 |
| Python - Flask | [Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) | 0\.9.3 | [Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) | 서비스 예정 |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) | 서비스 예정 |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## 호환 가능한 서버 미들웨어 라이브러리 
서비스 예정


## 관련 콘텐츠
v2.0 Azure AD 끝점에 대한 자세한 내용은 [Azure AD 앱 모델 v2 개요][AAD-App-Model-V2-Overview]를 참조하세요.

다음 DISQUS 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]: [ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]: [ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]: [ClientLib-Android-Repo]:
[ClientLib-Android-Sample]: [ClientLib-Js-Lib]:
[ClientLib-Js-Repo]: [ClientLib-Js-Sample]:
[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]: /
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]: /
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/

<!---HONumber=AcomDC_0928_2016-->