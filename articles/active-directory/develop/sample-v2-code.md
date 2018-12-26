---
title: Azure Active Directory 코드 샘플 | Microsoft Docs
description: 시나리오별로 구성된 사용 가능한 Azure Active Directory(V2 엔드포인트) 코드 샘플의 인덱스를 제공합니다.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6b6dd80f649b0c92aacdb5711e88d7710db5f51b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424445"
---
# <a name="azure-active-directory-code-samples-v20-endpoint"></a>Azure Active Directory 코드 샘플(v2.0 엔드포인트)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Microsoft ID 플랫폼을 사용하여 다음을 수행할 수 있습니다.

- 웹 응용 프로그램 및 web API에 인증 및 권한 부여 추가.
- 보호되는 web API에 액세스하려면 액세스 토큰 요구.

이 문서에서는 Azure AD v2.0 엔드포인트에 대해 간략하게 설명하고 샘플 링크를 제공합니다. 이 샘플은 응용 프로그램에 사용할 수 있는 코드 조각과 함께 작업 수행 방법을 보여줍니다. 코드 샘플 페이지에서 요구 사항, 설치 및 설정에 도움이 되는 자세한 추가 정보 항목을 찾을 수 있습니다. 코드 안에 있는 주석은 중요한 섹션을 이해하는 데 도움을 주기 위한 용도입니다.

> [!NOTE]
> v1.0 샘플에 관심이 있는 경우 [Azure AD 코드 샘플(v1.0 엔드포인트)](sample-v1-code.md)을 참조하세요.

각 샘플 형식에 대한 기본 시나리오를 이해하려면 [Azure Active Directory v2.0 엔드포인트에 대한 앱 형식](v2-app-types.md)을 참조하세요.

GitHub의 샘플에 참여할 수도 있습니다. 자세한 방법은 [Microsoft Azure Active Directory 샘플 및 설명서](https://github.com/Azure-Samples?page=3&query=active-directory)를 참조하세요.

## <a name="single-page-applications-spa"></a>SPA(단일 페이지 응용 프로그램)

이 샘플에서는 Azure AD로 보안이 설정된 단일 페이지 응용 프로그램을 작성하는 방법을 보여 줍니다. 이 샘플은 MSAL.js의 특징 중 하나를 사용합니다.

* [JavaScript용 Microsoft 인증 라이브러리](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)
* [Angular용 Microsoft 인증 라이브러리](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)
* [AngularJS용 Microsoft 인증 라이브러리](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

 플랫폼 |  Microsoft Graph 호출
 -------- |  ---------------------
![JavaScript](media/sample-v2-code/logo_js.png) JavaScript(msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript(MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
![Angular](media/sample-v2-code/logo_angular.png) JavaScript(MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>웹 응용 프로그램

다음 샘플은 사용자를 로그인하는 웹 응용 프로그램을 보여 줍니다. 일부 샘플은 사용자 ID를 사용하여 Microsoft Graph 또는 사용자 고유의 웹 API를 호출하는 응용 프로그램도 보여 줍니다.

 플랫폼 | 사용자만 로그인 | 사용자를 로그인하고 Microsoft Graph를 호출
 -------- | ------------------- | ---------------------------------
![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.1 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) | [aspnetcore2-2-signInAndCallGraph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/aspnetcore2-2-signInAndCallGraph) 분기의 동일한 샘플
![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET | [ASP.NET 빠른 시작](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [dotnet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) <p/>[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Node.js 빠른 시작](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)

## <a name="desktop-and-mobile-public-client-apps"></a>데스크톱 및 모바일 공용 클라이언트 앱

다음 샘플은 사용자 이름으로 Microsoft Graph API 또는 사용자 고유의 Web API에 액세스하는 공용 클라이언트 응용 프로그램(데스크톱/모바일 응용 프로그램)을 보여 줍니다. 이러한 클라이언트 응용 프로그램은 모두 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 | ASP.NET Core 2.0 Web API 호출
------------------ | -------- |  ----------| ---------- | -------------------------
데스크톱(WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | 대화형 | [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
데스크톱(콘솔)   | ![.NET/C#(데스크톱)](media/sample-v2-code/logo_NET.png) | Windows 통합 인증 |[dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
데스크톱(콘솔)   | ![.NET/C#(데스크톱)](media/sample-v2-code/logo_NETcore.png) | 사용자 이름/암호 |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)
모바일(UWP)   | ![.NET/C#(UWP)](media/sample-v2-code/logo_windows.png) | 대화형 |[dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
모바일(Android, iOS, UWP)   | ![.NET/C#(Xamarin)](media/sample-v2-code/logo_xamarin.png) | 대화형 |[xamarin-native-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
모바일(iOS)       | ![iOS/Objective C 또는 swift](media/sample-v2-code/logo_iOS.png) | 대화형 |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
모바일(Android)   | ![Android/Java](media/sample-v2-code/logo_Android.png) | 대화형 |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>디먼 응용 프로그램

다음 샘플은 고유 ID(사용자 없음)로 Microsoft Graph API에 액세스하는 응용 프로그램을 보여 줍니다.

클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출
------------------ | -------- | ---------- | --------------------
콘솔 | ![.NET Core](media/sample-v2-code/logo_NETcore.png)<p/> ASP.NET  | 클라이언트 자격 증명 | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
웹앱 | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET  | 클라이언트 자격 증명 | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>헤드리스 응용 프로그램

다음 샘플은 웹 브라우저가 없는 디바이스에서 실행되는 공용 클라이언트 애플리케이션을 보여 줍니다. 앱은 명령줄 도구이거나 Linux/Mac 또는 IoT 응용 프로그램에서 실행될 수 있습니다. 샘플에는 다른 디바이스(예: 휴대폰)에서 대화형으로 로그인하는 사용자 이름으로 Microsoft Graph API에 액세스하는 앱이 있습니다. 이 클라이언트 응용 프로그램은 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출
------------------ | -------- |  ----------| ----------
데스크톱(콘솔)   | ![.NET/C#(데스크톱)](media/sample-v2-code/logo_NETcore.png) | 디바이스 코드 흐름 |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>Web API

다음 샘플은 Azure AD v2.0 엔드포인트로 Web API를 보호하는 방법을 보여 줍니다. 이 API는 WPF 응용 프로그램이 실행하지만, 모든 응용 프로그램에서 호출할 수 있습니다.

플랫폼 | 샘플
 -------- | -------------------
![.NET/C#](media/sample-v2-code/logo_NET.png) | [dotnet-native-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)의 WebAPI(서비스)

## <a name="other-microsoft-graph-samples"></a>다른 Microsoft Graph 샘플

Azure AD 인증을 포함하여 Microsoft Graph API에 대한 여러 사용 패턴을 보여주는 [샘플](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) 및 자습서에 대한 내용은 [Microsoft Graph 커뮤니티 샘플 및 자습서](https://github.com/microsoftgraph/msgraph-community-samples)를 참조하세요.

## <a name="see-also"></a>참고 항목

[Azure Active Directory 개발자 가이드](v1-overview.md)

[Azure AD Graph API 개념 및 참조](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API 도우미 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
