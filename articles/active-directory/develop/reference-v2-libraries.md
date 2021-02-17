---
title: Microsoft id 플랫폼 인증 라이브러리 | Microsoft
description: Microsoft id 플랫폼과 호환 되는 클라이언트 라이브러리 및 미들웨어 목록입니다. 이러한 라이브러리를 사용 하 여 사용자 로그인 (인증) 및 보호 된 웹 API 액세스 (권한 부여)에 대 한 지원을 응용 프로그램에 추가할 수 있습니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 228a15e9e9e27cbcfd71d4762db2f4ab9f6dfffe
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560155"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft id 플랫폼 인증 라이브러리

다음 표에서는 여러 응용 프로그램 유형에 대 한 Microsoft 인증 라이브러리 지원을 보여 줍니다. 여기에는 라이브러리 소스 코드에 대 한 링크, 앱 프로젝트에 대 한 패키지를 가져올 위치, 라이브러리가 사용자 로그인 (인증), 보호 된 웹 Api (인증)에 대 한 액세스 또는 둘 모두를 지원 하는지 여부가 포함 됩니다.

Microsoft id 플랫폼은 [인증 된 openid connect 공급자로](https://openid.net/certification/)openid connect Foundation에서 인증 되었습니다. MSAL (Microsoft 인증 라이브러리) 이외의 라이브러리나 Microsoft에서 지 원하는 다른 라이브러리를 사용 하려는 경우 [인증 된 Openid connect Connect 구현](https://openid.net/developers/certified/)으로 하나를 선택 합니다.

[OAuth 2.0 또는 Openid connect Connect 1.0](active-directory-v2-protocols.md)의 고유한 프로토콜 수준 구현을 직접 코딩 하도록 선택 하는 경우 각 표준 사양의 보안 고려 사항에 주의 하 고 [Microsoft SDL][Microsoft-SDL]과 같은 sdl (소프트웨어 개발 수명 주기) 방법론을 따릅니다.

## <a name="single-page-application-spa"></a>SPA (단일 페이지 응용 프로그램)

단일 페이지 응용 프로그램은 브라우저 화면에서 전적으로 실행 되며 페이지 데이터 (HTML, CSS 및 JavaScript)를 동적으로 또는 응용 프로그램 로드 시 인출 합니다. 웹 Api를 호출 하 여 백 엔드 데이터 원본과 상호 작용할 수 있습니다.

SPA의 코드가 브라우저에서 완전히 실행 되기 때문에 암호를 안전 하 게 저장할 수 없는 *공용 클라이언트로* 간주 됩니다.

| 언어/프레임 워크 | 프로젝트 설정<br/>GitHub                                                                                                    | 패키지                                                                      | 도달<br/>시작됨                             | 사용자 로그인                                         | 웹 Api 액세스                                                 | 일반적으로 사용 가능 (GA) *또는*<br/>공개 미리 보기<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL 각도 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | 퍼블릭 미리 보기                                               |
| Angular              | [MSAL 각도](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [자습서](tutorial-v2-angular.md)              | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | 퍼블릭 미리 보기                                               |
| JavaScript           | [MSAL.js 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [자습서](tutorial-v2-javascript-auth-code.md) | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| JavaScript           | [MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)              | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)     | [자습서](tutorial-v2-javascript-spa.md) | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| React                | [MSAL 반응](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | 퍼블릭 미리 보기                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 미리 보기의 추가 사용 약관][preview-tos] 은 *공개 미리 보기로* 제공 되는 라이브러리에 적용 됩니다.

## <a name="web-application"></a>웹 애플리케이션

웹 응용 프로그램은 렌더링 되는 사용자의 웹 브라우저에 HTML, CSS 및 JavaScript를 생성 하 고 보내는 서버에서 코드를 실행 합니다. 사용자의 id는 사용자의 브라우저 (프런트 엔드)와 웹 서버 (백 엔드) 사이에서 세션으로 유지 됩니다.

웹 응용 프로그램의 코드는 웹 서버에서 실행 되므로 비밀을 안전 하 게 저장할 수 있는 *기밀 클라이언트* 라고 합니다.

| 언어/프레임 워크 | 프로젝트 설정<br/>GitHub                                                                                     | 패키지                                                                                                    | 도달<br/>시작됨                               | 사용자 로그인                                            | 웹 Api 액세스                                                    | 일반적으로 사용 가능 (GA) *또는*<br/>공개 미리 보기<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![라이브러리가 사용자 로그인에 대 한 ID 토큰을 요청할 수 없습니다.][n] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y]    | GA                                                           |
| ASP.NET Core         | [ASP.NET 보안](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리가 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 없습니다.][n] | GA                                                           |
| ASP.NET Core         | [Microsoft. Identity. 웹](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. Identity. 웹](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [빠른 시작](quickstart-v2-java-webapp.md)        | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y]    | GA                                                           |
| Node.js              | [MSAL 노드](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-노드](https://www.npmjs.com/package/@azure/msal-node)                                                | [빠른 시작](quickstart-v2-nodejs-webapp-msal.md) | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y]    | GA                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [passport-azure-ad](https://www.npmjs.com/package/passport-azure-ad)                                       | [빠른 시작](quickstart-v2-nodejs-webapp.md)      | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [빠른 시작](quickstart-v2-python-webapp.md)      | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y]    | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 미리 보기의 추가 사용 약관][preview-tos] 은 *공개 미리 보기로* 제공 되는 라이브러리에 적용 됩니다.

## <a name="desktop-application"></a>데스크톱 애플리케이션

데스크톱 응용 프로그램은 일반적으로 사용자 인터페이스를 표시 하 고 사용자의 데스크톱에서 실행 하기 위한 이진 (컴파일) 코드입니다.

데스크톱 응용 프로그램은 사용자의 데스크톱에서 실행 되므로 비밀을 안전 하 게 저장할 수 없는 *공용 클라이언트로* 간주 됩니다.

| 언어/프레임 워크 | 프로젝트 설정<br/>GitHub                                                                                     | 패키지                                                                               | 도달<br/>시작됨                        | 사용자 로그인                                         | 웹 Api 액세스                                                 | 일반적으로 사용 가능 (GA) *또는*<br/>공개 미리 보기<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL 노드](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | [자습서](tutorial-v2-nodejs-desktop.md)   | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| macOS (Swift/Obj-C)  | [iOS 및 macOS용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [자습서](tutorial-v2-ios.md)             | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [자습서](tutorial-v2-windows-uwp.md)     | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [자습서](tutorial-v2-windows-desktop.md) | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 미리 보기의 추가 사용 약관][preview-tos] 은 *공개 미리 보기로* 제공 되는 라이브러리에 적용 됩니다.

## <a name="mobile-application"></a>모바일 애플리케이션

모바일 응용 프로그램은 일반적으로 사용자 인터페이스를 표시 하 고 사용자의 모바일 장치에서 실행 되는 이진 (컴파일) 코드입니다.

모바일 응용 프로그램은 사용자의 모바일 장치에서 실행 되기 때문에 암호를 안전 하 게 저장할 수 없는 *공용 클라이언트로* 간주 됩니다.

| 플랫폼          | 프로젝트 설정<br/>GitHub                                                                          | 패키지                                                                               | 도달<br/>시작됨                    | 사용자 로그인                                         | 웹 Api 액세스                                                 | 일반적으로 사용 가능 (GA) *또는*<br/>공개 미리 보기<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android(Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [빠른 시작](quickstart-v2-android.md) | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| iOS (Swift/Obj-C) | [iOS 및 macOS용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [자습서](tutorial-v2-ios.md)         | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| Xamarin(.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![라이브러리에서 사용자 로그인에 대 한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 미리 보기의 추가 사용 약관][preview-tos] 은 *공개 미리 보기로* 제공 되는 라이브러리에 적용 됩니다.

## <a name="service--daemon"></a>서비스/디먼

서비스 및 디먼는 일반적으로 서버 간 및 기타 무인 ( *헤드리스*) 통신에 사용 됩니다. 키보드에 자격 증명을 입력 하거나 리소스 액세스에 동의할 수 있는 사용자가 없기 때문에, 이러한 응용 프로그램은 웹 API 리소스에 대 한 권한 있는 액세스를 요청할 때 사용자가 아닌 자신으로 인증 합니다.

서버에서 실행 되는 서비스 또는 디먼은 암호를 안전 하 게 저장할 수 있는 *기밀 클라이언트로* 간주 됩니다.

| 언어/프레임 워크 | 프로젝트 설정<br/>GitHub                                                                 | 패키지                                                                                | 도달<br/>시작됨                           | 사용자 로그인                                            | 웹 Api 액세스                                                 | 일반적으로 사용 가능 (GA) *또는*<br/>공개 미리 보기<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [빠른 시작](quickstart-v2-netcore-daemon.md) | ![라이브러리가 사용자 로그인에 대 한 ID 토큰을 요청할 수 없습니다.][n] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![라이브러리가 사용자 로그인에 대 한 ID 토큰을 요청할 수 없습니다.][n] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| 노드               | [MSAL 노드](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-노드](https://www.npmjs.com/package/@azure/msal-node)  | [빠른 시작](quickstart-v2-nodejs-console.md)  | ![라이브러리가 사용자 로그인에 대 한 ID 토큰을 요청할 수 없습니다.][n] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | —  | ![라이브러리가 사용자 로그인에 대 한 ID 토큰을 요청할 수 없습니다.][n] | ![라이브러리는 보호 된 웹 Api에 대 한 액세스 토큰을 요청할 수 있습니다.][y] | GA |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 미리 보기의 추가 사용 약관][preview-tos] 은 *공개 미리 보기로* 제공 되는 라이브러리에 적용 됩니다.

## <a name="next-steps"></a>다음 단계

Microsoft 인증 라이브러리에 대 한 자세한 내용은 [MSAL (Microsoft 인증 라이브러리) 개요](msal-overview.md)를 참조 하세요.

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
