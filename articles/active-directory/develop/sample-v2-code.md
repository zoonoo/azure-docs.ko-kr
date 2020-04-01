---
title: Microsoft ID 플랫폼에 대한 코드 샘플 | 마이크로 소프트 문서
description: 시나리오별로 구성된 사용 가능한 Microsoft ID 플랫폼(v2.0 끝점) 코드 샘플의 인덱스를 제공합니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d8f69096544b0c3b51513d0cb5c4330fcbb1c18c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419822"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft ID 플랫폼 코드 샘플(v2.0 끝점)

Microsoft ID 플랫폼을 사용하여 다음을 수행할 수 있습니다.

- 웹 애플리케이션 및 web API에 인증 및 권한 부여 추가.
- 보호되는 web API에 액세스하려면 액세스 토큰 요구.

이 문서에서는 Microsoft ID 플랫폼 끝점에 대한 샘플에 대한 링크를 간략하게 설명하고 제공합니다. 이러한 샘플에서는 이 작업이 어떻게 수행되는지 보여 주며 응용 프로그램에서 사용할 수 있는 코드 조각도 제공합니다. 코드 샘플 페이지에서 요구 사항, 설치 및 설정에 도움이 되는 자세한 readme 항목을 찾을 수 있습니다. 코드 내의 주석은 중요한 섹션을 이해하는 데 도움이 됩니다.

> [!NOTE]
> v1.0 샘플에 관심이 있는 경우 [Azure AD 코드 샘플(v1.0 끝점)을](../azuread-dev/sample-v1-code.md)참조하십시오.

각 샘플 유형에 대한 기본 시나리오를 이해하려면 [Microsoft ID 플랫폼 끝점에 대한 앱 유형을](v2-app-types.md)참조하십시오.

GitHub의 샘플에 참여할 수도 있습니다. 자세한 방법은 [Microsoft Azure Active Directory 샘플 및 설명서](https://github.com/Azure-Samples?page=3&query=active-directory)를 참조하세요.

## <a name="single-page-applications"></a>단일 페이지 애플리케이션

이 샘플에서는 Microsoft ID 플랫폼으로 보호되는 단일 페이지 응용 프로그램을 작성하는 방법을 보여 주며 이 샘플을 보여 준다. 이 샘플은 MSAL.js의 맛 중 하나를 사용합니다.

| 플랫폼 | Description | 링크 |
| -------- | --------------------- | -------- |
| ![이 이미지는 자바 스크립트](media/sample-v2-code/logo_js.png) 로고 [자바 스크립트 (msal.js)를](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) 보여줍니다 | Microsoft Graph 호출 |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![이 이미지는 자바 스크립트](media/sample-v2-code/logo_js.png) 로고 [자바 스크립트 (msal.js)를](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) 보여줍니다 | B2C 통화 |[b2c 자바 스크립트 - msal - 단일 페이지 앱](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![이 이미지는 자바 스크립트](media/sample-v2-code/logo_js.png) 로고 [자바 스크립트 (msal.js)를](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) 보여줍니다 | 자체 웹 API 호출 |[자바 스크립트 - 단일 페이지 앱 - 도트넷 - 웹 피피 - v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![이 이미지는 각도 로고](media/sample-v2-code/logo_angular.png) 자바 스크립트 [(MSAL 각도)를](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) 보여줍니다| Microsoft Graph 호출  | [액티브 디렉토리 자바 스크립트 - 단일 페이지 앱 각도](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![이 이미지는 각도 로고](media/sample-v2-code/logo_angular.png) 자바 스크립트 [(MSAL 각도)를](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) 보여줍니다 | B2C 통화 |[액티브 디렉토리 b2c 자바 스크립트 각도 스파](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>웹 애플리케이션

다음 샘플은 사용자를 로그인하는 웹 애플리케이션을 보여 줍니다. 일부 샘플은 사용자 ID를 사용하여 Microsoft Graph 또는 사용자 고유의 웹 API를 호출하는 애플리케이션도 보여 줍니다.

| 플랫폼 | 사용자만 로그인 | 사용자를 로그인하고 Microsoft Graph를 호출 |
| -------- | ------------------- | --------------------------------- |
| ![이 이미지는 ASP.NET 코어 로고를 보여줍니다.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET 코어 2.2 | [ASP.NET 코어 WebApp 에 사용자 자습서](https://aka.ms/aspnetcore-webapp-sign-in) | ASP.NET 코어 [웹 앱에서](https://aka.ms/aspnetcore-webapp-call-msgraph) 동일한 샘플은 Microsoft 그래프 단계를 호출합니다. |
| ![이 이미지는 ASP.NET 로고를 보여줍니다.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET 빠른 시작](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![이 이미지는 Java 로고를 보여줍니다.](media/sample-v2-code/logo_java.png)  |                   | [MS-아이덴티티-자바 웹 앱](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![이 이미지는 파이썬 로고를 보여줍니다.](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![이 이미지는 루비 로고를 보여줍니다.](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>데스크톱 및 모바일 공용 클라이언트 앱

다음 샘플에서는 Microsoft Graph API에 액세스하는 공용 클라이언트 응용 프로그램(데스크톱 또는 모바일 응용 프로그램) 또는 사용자 이름으로 사용자 고유의 웹 API를 보여 줍니다. 이러한 모든 클라이언트 응용 프로그램은 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 | ASP.NET 코어 2.0 웹 API 호출 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 데스크톱(WPF)      | ![이 이미지는 .NET/C# 로고를 보여 주며](media/sample-v2-code/logo_NET.png) | [대화형](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| 데스크톱(콘솔)   | ![이 이미지는 .NET/C# (데스크톱) 로고를 보여 주며](media/sample-v2-code/logo_NET.png) | [Windows 통합 인증](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 보여줍니다.](media/sample-v2-code/logo_java.png) | [Windows 통합 인증](msal-authentication-flows.md#integrated-windows-authentication) |[MS-ID-자바 데스크톱](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 데스크톱(콘솔)   | ![이 이미지는 .NET/C# (데스크톱) 로고를 보여 주며](media/sample-v2-code/logo_NETcore.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| WAM이 있는 데스크톱(콘솔)  | ![이 이미지는 .NET/C# (데스크톱) 로고를 보여 주며](media/sample-v2-code/logo_NETcore.png) | [WAM과 대화 형](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 보여줍니다.](media/sample-v2-code/logo_java.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[MS-ID-자바 데스크톱](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 데스크톱(콘솔)   | ![이 이미지는 파이썬 로고를 보여줍니다.](media/sample-v2-code/logo_python.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[MS-아이덴티티 파이썬 데스크톱](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| 모바일(Android, iOS, UWP)   | ![이 이미지는 .NET/C# (자마린) 로고를 보여 주며](media/sample-v2-code/logo_xamarin.png) | [대화형](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| 모바일(iOS)       | ![이 이미지는 iOS/Objective-C 또는 Swift를 보여 주며](media/sample-v2-code/logo_iOS.png) | [대화형](msal-authentication-flows.md#interactive) |[ios-스위프트-오jc-네이티브-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| 데스크톱(macOS)       | macOS | [대화형](msal-authentication-flows.md#interactive) |[맥OS 스위프트 오jc-네이티브-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| 모바일 (안드로이드 자바)   | ![이 이미지는 안드로이드 로고를 보여줍니다](media/sample-v2-code/logo_Android.png) | [대화형](msal-authentication-flows.md#interactive) |  [안드로이드 자바](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| 모바일 (안드로이드 코틀린)   | ![이 이미지는 안드로이드 로고를 보여줍니다](media/sample-v2-code/logo_Android.png) | [대화형](msal-authentication-flows.md#interactive) |  [안드로이드 코틀린](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>디먼 애플리케이션

다음 샘플은 고유 ID(사용자 없음)로 Microsoft Graph API에 액세스하는 애플리케이션을 보여 줍니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 |
| ------------------ | -------- | ---------- | -------------------- |
| 콘솔 | ![이 이미지는 .NET 코어 로고를 보여줍니다.](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| 웹앱 | ![이 이미지는 ASP.NET 로고를 보여줍니다.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| 콘솔 | ![이 이미지는 Java 로고를 보여줍니다.](media/sample-v2-code/logo_java.png) | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [MS-아이덴티티-자바데몬](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| 콘솔 | ![이 이미지는 파이썬 로고를 보여줍니다.](media/sample-v2-code/logo_python.png) | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [MS-아이덴티티 파이썬 데몬](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>헤드리스 애플리케이션

다음 샘플은 웹 브라우저가 없는 디바이스에서 실행되는 공용 클라이언트 애플리케이션을 보여 줍니다. 이 앱은 명령줄 도구, Linux 또는 Mac에서 실행되는 앱 또는 IoT 응용 프로그램일 수 있습니다. 이 샘플에는 다른 장치(예: 휴대폰)에서 대화식으로 로인하는 사용자의 이름으로 Microsoft Graph API에 액세스하는 앱이 있습니다. 이 클라이언트 응용 프로그램은 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 |
| ------------------ | -------- |  ----------| ---------- |
| 데스크톱(콘솔)   | ![이 이미지는 .NET/C# (데스크톱) 로고를 보여 주며](media/sample-v2-code/logo_NETcore.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 보여줍니다.](media/sample-v2-code/logo_java.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[ms-ID-자바-디바이스코드플로우](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| 데스크톱(콘솔)   | ![이 이미지는 파이썬 로고를 보여줍니다.](media/sample-v2-code/logo_python.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[ms-ID-파이썬-디바이스코드플로우](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Web API

다음 샘플에서는 Microsoft ID 플랫폼 끝점을 사용하여 웹 API를 보호하는 방법과 웹 API에서 다운스트림 API를 호출하는 방법을 보여 주며, 이 샘플에서는 웹 API를 호출하는 방법을 보여 준다.

| 플랫폼 | 예제 |
| -------- | ------------------- |
| ![이 이미지는 ASP.NET 코어 로고를 보여줍니다.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET 코어 2.2 | ASP.NET 코어 웹 API(서비스) [-네이티브-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![이 이미지는 ASP.NET 로고를 보여줍니다.](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [MS-ID-aspnet-webapi-onbeof의](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) 웹 API(서비스) |
| ![이 이미지는 Java 로고를 보여줍니다.](media/sample-v2-code/logo_java.png) | [MS-ID-자바-웹피의](https://github.com/Azure-Samples/ms-identity-java-webapi) 웹 API(서비스) |
| ![이 이미지는 Node.js 로고를 보여 주며](media/sample-v2-code/logo_nodejs.png) | [액티브 디렉토리-자바 스크립트-노드-webapi-v2의](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) 웹 API(서비스) |
| ![이 이미지는 Node.js 로고를 보여 주며](media/sample-v2-code/logo_nodejs.png) | [활성 디렉토리-b2c-자바 스크립트-nodejs-webapi의 B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 웹 API (서비스) |

## <a name="azure-functions-as-web-apis"></a>웹 API로 Azure 함수

다음 샘플에서는 HttpTrigger를 사용하여 Azure Function을 보호하고 Microsoft ID 플랫폼 끝점을 사용하여 웹 API를 노출하는 방법과 웹 API에서 다운스트림 API를 호출하는 방법을 보여 주며,

| 플랫폼 | 예제 |
| -------- | ------------------- |
| ![이 이미지는 ASP.NET 코어 로고를 보여줍니다.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET 코어 2.2 | ASP.NET 코어 웹 API (서비스) [도트넷 네이티브 aspnetcore-v2의](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) Azure 기능  |
| ![이 이미지는 Node.js 로고를 보여 주며](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [NodeJS 및 여권 azure 광고의](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) 웹 API(서비스) |
| ![이 이미지는 파이썬 로고를 보여줍니다.](media/sample-v2-code/logo_python.png)</p>Python | [파이썬의](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) 웹 API(서비스) |
| ![이 이미지는 Node.js 로고를 보여 주며](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [노드JS의](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) 웹 API(서비스)와 |

## <a name="other-microsoft-graph-samples"></a>다른 Microsoft Graph 샘플

Azure AD 인증을 포함하여 Microsoft Graph API에 대한 여러 사용 패턴을 보여주는 [샘플](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) 및 자습서에 대한 내용은 [Microsoft Graph 커뮤니티 샘플 및 자습서](https://github.com/microsoftgraph/msgraph-community-samples)를 참조하세요.

## <a name="see-also"></a>참조

- [Azure Active Directory(v1.0) 개발자 가이드](../azuread-dev/v1-overview.md)
- [마이크로소프트 그래프 API 개념 및 참조](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
