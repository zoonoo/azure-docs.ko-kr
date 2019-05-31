---
title: Azure Active Directory v1.0 코드 샘플 | Microsoft Docs
description: 시나리오별로 구성된 Azure Active Directory(v1.0 엔드포인트) 코드 샘플의 인덱스를 제공합니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b14dcb5f5af971b10c83ea7fd9064a08cde3ab35
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545441"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory 코드 샘플(v1.0 엔드포인트)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Microsoft Azure AD(Azure Active Directory)를 사용하여 웹 애플리케이션 및 Web API에 인증 및 권한 부여를 추가할 수 있습니다.

이 섹션에서는 Azure AD v1.0 엔드포인트에 대해 자세히 알아보는 데 사용할 수 있는 샘플 링크를 제공합니다. 이 샘플은 애플리케이션에 사용할 수 있는 코드 조각과 함께 작업 수행 방법을 보여줍니다. 코드 샘플 페이지에서 요구 사항, 설치 및 설정에 도움이 되는 자세한 추가 정보 항목을 찾을 수 있습니다. 또한 코드는 중요한 섹션을 이해하는 데 도움이 되도록 주석으로 처리되어 있습니다.

> [!NOTE]
> Azure AD V2 코드 샘플에 관심이 있는 경우 [시나리오별 v2.0 코드 샘플](sample-v2-code.md)을 참조하세요.

각 샘플 유형식에 대한 기본 시나리오를 이해하려면 [Azure AD의 인증 시나리오](authentication-scenarios.md)를 참조하세요.

GitHub에서 샘플에 참여할 수도 있습니다. 자세한 방법은 [Microsoft Azure Active Directory 샘플 및 설명서](https://github.com/Azure-Samples?page=3&query=active-directory)를 참조하세요.

## <a name="single-page-applications"></a>단일 페이지 응용 프로그램

이 샘플에서는 Azure AD로 보안이 설정된 단일 페이지 애플리케이션을 작성하는 방법을 보여 줍니다.

 플랫폼 | 자체 API 호출 | 다른 Web API 호출
 -------- |  --------------------- | ------------------ 
![JavaScript](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Angular JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>웹 응용 프로그램

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>웹 애플리케이션은 사용자 ID로 사용자를 로그인하고 Microsoft Graph 또는 Web API를 호출

다음 샘플은 사용자를 로그인하는 웹 애플리케이션을 보여 줍니다. 일부 애플리케이션은 로그인한 사용자의 이름으로 Microsoft Graph 또는 사용자 고유의 Web API도 호출합니다.

 플랫폼 | 사용자만 로그인 | Microsoft Graph 또는 AAD Graph 호출| 또 다른 ASP.NET 또는 ASP.NET Core 2.0 Web API 호출
 -------- | ------------------- | --------------------- | -------------------------
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) <p/>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) <p/> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) <p/> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)<p/> (AAD Graph) |
![Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![PHP](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>역할 기반 액세스 제어(권한 부여)를 보여주는 웹 애플리케이션

다음 샘플은 RBAC(역할 기반 액세스 제어)를 구현하는 방법을 보여 줍니다. RBAC는 웹 애플리케이션의 특정 기능에 대한 사용 권한을 특정 사용자로 제한하는 데 사용됩니다. 사용자가 **Azure AD 그룹**에 속해 있는지 또는 지정된 애플리케이션 **역할**이 있는지에 따라 권한이 부여됩니다.

플랫폼 | 샘플 |
 -------- | ------------------- |
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) <p/>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Azure AD **역할**을 권한 부여에 사용하는 .NET 4.5 MVC 웹앱

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Microsoft Graph 또는 Web API를 호출하는 데스크톱 및 모바일 공용 클라이언트의 애플리케이션

다음 샘플은 사용자 이름으로 Microsoft Graph 또는 Web API에 액세스하는 공용 클라이언트 애플리케이션(데스크톱/모바일 애플리케이션)을 보여 줍니다. 장치 및 플랫폼에 따라 애플리케이션에서 다른 방식(흐름/권한 부여)으로 사용자를 로그인할 수 있습니다. 

- 대화형으로
- 자동으로(Windows의 Windows 통합 인증 또는 사용자 이름/암호 사용) 
- 또는 다른 디바이스에 대화형 로그인을 위임하여(웹 컨트롤을 제공하지 않는 디바이스에서 사용되는 디바이스 코드 흐름)

클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 | ASP.NET 또는 ASP.NET Core 2.x Web API 호출
------------------ | -------- | ---------- | -------------------- | -------------------------
데스크톱(WPF)           | ![.NET/C#](media/sample-v2-code/logo_NET.png)  | 대화형 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget)의 일부 | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
모바일(UWP)            | .![.NET/C#/UWP](media/sample-v2-code/logo_Windows.png)   | 대화형 | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> 이 샘플은 [ADAL.NET](https://aka.ms/adalnet)이 아니라 [WAM](https://docs.microsoft.com/windows/uwp/security/web-account-manager)을 사용합니다.|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store)(ADAL.NET을 사용하여 단일 테넌트 Web API를 호출하는 UWP 애플리케이션) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store)(ADAL.NET을 사용하여 다중 테넌트 Web API를 호출하는 UWP 애플리케이션)|
모바일(Android, iOS, UWP)   | ![.NET/C#(Xamarin)](media/sample-v2-code/logo_xamarin.png) | 대화형 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
모바일(Android)           | ![Android/Java](media/sample-v2-code/logo_Android.png) | 대화형 |   [android](https://github.com/Azure-Samples/active-directory-android) |
모바일(iOS)           | ![iOS/Objective C 또는 swift](media/sample-v2-code/logo_iOS.png) | 대화형 |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
데스크톱(콘솔)          | ![.NET/C#](media/sample-v2-code/logo_NET.png) | 사용자 이름/암호 </p>  통합 Windows 인증 | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
데스크톱(콘솔)          | ![Java 콘솔](media/sample-v2-code/logo_Java.png) | 사용자 이름/암호 | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
데스크톱(콘솔)           | ![.NET Core/C#](media/sample-v2-code/logo_NETcore.png) | 디바이스 코드 흐름 | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>디먼 애플리케이션(애플리케이션 ID를 사용하여 Web API에 액세스)

다음 샘플은 사용자 없이(애플리케이션 ID로) Microsoft Graph 또는 web API에 액세스하는 데스크톱 또는 웹 애플리케이션을 보여줍니다.

클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | ASP.NET 또는 ASP.NET Core 2.0 Web API 호출
------------------ | -------- | ---------- | -------------------- 
디먼 앱(콘솔)          | ![.NET](media/sample-v2-code/logo_NETframework.png) | 앱 비밀 또는 인증서를 사용하는 클라이언트 자격 증명 | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
디먼 앱(콘솔)         | ![.NET](media/sample-v2-code/logo_NETcore.png) | 인증서를 사용하는 클라이언트 자격 증명| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET 웹앱  | ![.NET](media/sample-v2-code/logo_NETframework.png) | 클라이언트 자격 증명 | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web API

### <a name="web-api-protected-by-azure-active-directory"></a>Azure Active Directory로 보호되는 Web API

다음 샘플은 Azure AD로 node.js web API를 보호하는 방법을 보여줍니다.

이 문서의 이전 섹션에서 ASP.NET 또는 ASP.NET Core **Web API**를 **호출**하는 클라이언트 애플리케이션을 보여 주는 다른 샘플을 확인할 수도 있습니다. 해당 샘플은 이 섹션에서 다시 언급되지 않지만 위 또는 아래 표의 마지막 열에 나와 있습니다.

플랫폼 | 샘플
 -------- | -------------------
![PHP](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi)

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Microsoft Graph 또는 다른 Web API를 호출하는 Web API

다음 샘플은 또 다른 web API를 호출하는 web API를 보여줍니다. 두 번째 샘플은 조건부 액세스를 처리하는 방법을 보여줍니다.

 플랫폼 |  Microsoft Graph 호출 | 또 다른 ASP.NET 또는 ASP.NET Core 2.0 Web API 호출
 -------- |  --------------------- | -------------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="other-microsoft-graph-samples"></a>다른 Microsoft Graph 샘플

Azure AD 인증을 포함하여 Microsoft Graph API에 대한 여러 사용 패턴을 보여주는 샘플 및 자습서를 보려면 [Microsoft Graph 커뮤니티 샘플 및 자습서](https://github.com/microsoftgraph/msgraph-community-samples)를 참조하세요.

## <a name="see-also"></a>참고 항목

[Azure Active Directory 개발자 가이드](v1-overview.md)

[Azure Active Directory 인증 라이브러리](active-directory-authentication-libraries.md)

[Azure AD Graph API 개념 및 참조](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API 도우미 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
