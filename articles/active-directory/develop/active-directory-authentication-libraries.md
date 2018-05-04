---
title: Azure Active Directory 인증 라이브러리 | Microsoft Docs
description: Azure AD 인증 라이브러리(ADAL)는 클라이언트 응용 프로그램 개발자들이 클라우드 또는 온-프레미스 Active Directory(AD)에 쉽게 사용자를 인증하고 API 호출 보안을 위한 액세스 토큰을 가져올 수 있게 합니다.
services: active-directory
documentationcenter: ''
author: bryanla
manager: mtillman
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: e2c77b2e47ce3dd900b1b277e802a07cfed84702
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory 인증 라이브러리
Azure ADAL(Active Directory 인증 라이브러리)은 응용 프로그램 개발자들이 클라우드 또는 온-프레미스 AD(Active Directory)에 사용자를 인증하고 API 호출 보안을 위한 토큰을 가져올 수 있게 합니다. ADAL은 개발자가 다음과 같은 기능을 통해 더 쉽게 인증하도록 합니다.
 - 액세스 토큰 및 새로 고침 토큰을 저장하는 구성 가능한 토큰 캐시
 - 액세스 토큰이 만료되고 새로 고침 토큰을 사용할 수 있을 때 자동 토큰 새로 고침
 - 비동기 메서드 호출에 대한 지원
 - 등

> [!NOTE]
> Azure AD v2.0 라이브러리(MSAL)에 대한 내용은 [MSAL 라이브러리 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)를 참조하세요.
>
>

### <a name="client-libraries"></a>클라이언트 라이브러리

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참고 자료
| --- | --- | --- | --- | --- | --- |
| .NET 클라이언트, Windows 스토어, UWP, Xamarin iOS 및 Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [데스크톱 앱](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[참조](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| .NET 클라이언트, Windows 스토어, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [데스크톱 앱](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[단일 페이지 앱](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS 앱](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [참조](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[중앙 리포지토리](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android 앱](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| 자바 |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java 웹앱](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| 파이썬 |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python 웹앱](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) | |

### <a name="server-libraries"></a>서버 라이브러리

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참고 자료
| --- | --- | --- | --- | --- | --- |
| .NET |AzureAD용 OWIN|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC 앱](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OpenIDConnect용 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[웹앱](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [앱 API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |WS-Federation용 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC 웹앱](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |.NET 4.5용 ID 프로토콜 확장 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |.NET 4.5 JWT 핸들러 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>v2.0 클라이언트 라이브러리(MSAL)

[Azure AD v2.0 끝점](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare)은 단일 끝점 뒤에서 Azure AD와 Microsoft 계정을 결합합니다. 이 끝점에 액세스하기 위해 개발자는 ADAL 대신 [프로덕션 지원 미리 보기 MSAL 라이브러리](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)를 사용할 수 있습니다.

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참고 자료
| --- | --- | --- | --- | --- | --- |
| .NET 클라이언트, Windows 스토어, UWP, Xamarin iOS 및 Android |.NET용 MSAL(미리 보기) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [데스크톱 앱](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[참조](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) |
| JavaScript |JavaScript용 MSAL(미리 보기) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [단일 페이지 앱](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [참조](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) |
| iOS |iOS용 MSAL(미리 보기) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS 앱](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [참조](https://azuread.github.io/docs/objc/) |
| Android |Android용 MSAL(미리 보기) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android 앱](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [참조](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>시나리오

ADAL을 원격 리소스에 액세스하는 클라이언트 인증에 사용할 수 있는 3가지 공통 시나리오는 다음과 같습니다.  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>장치에서 실행되는 네이티브 클라이언트 응용 프로그램의 사용자 인증

이 시나리오에서는 개발자가 웹 API와 같이 Azure AD에서 보호하는 원격 리소스에 액세스해야 하는 WPF 클라이언트 응용 프로그램을 가지고 있습니다. 개발자는 Azure 구독을 가지고 있고 다운스트림 웹 API 호출 방법을 알고 있으며 웹 API가 사용하는 Azure AD 테넌트를 알고 있습니다. 결과적으로, ADAL을 사용하여 인증 경험을 ADAL에 위임하거나 사용자 자격 증명을 명시적으로 처리함으로써 Azure AD와의 인증을 수월하게 지원할 수 있습니다. ADAL을 사용하면 쉽게 사용자를 인증하고 Azure AD에서 액세스 토큰 및 새로고침 토큰을 얻은 다음 액세스 토큰을 사용하여 웹 API에 요청할 수 있습니다.

Azure AD에 대한 인증을 사용하여 이 시나리오를 설명하는 코드 샘플의 경우 [웹 API에 네이티브 클라이언트 WPF 응용 프로그램](https://github.com/azureadsamples/nativeclient-dotnet)을 참조하세요.

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>웹 서버에서 실행되는 비밀 클라이언트 응용 프로그램 인증

이 시나리오에서는 개발자가 웹 API와 같이 Azure AD에서 보호하는 원격 리소스에 액세스해야 하는 서버에서 실행 중인 응용 프로그램을 가지고 있습니다. 개발자는 Azure 구독을 가지고 있고 다운스트림 서비스 호출 방법을 알고 있으며 웹 API가 사용하는 Azure AD 테넌트를 알고 있습니다. 결과적으로, 개발자는 ADAL을 사용하여 응용 프로그램의 자격 증명을 명시적으로 처리함으로써 Azure AD로 인증을 용이하게 지원할 수 있습니다. ADAL을 사용하면 쉽게 응용 프로그램의 클라이언트 자격 증명을 사용하여 Azure AD에서 토큰을 점검한 다음 해당 토큰을 사용하여 웹 API에 요청할 수 있습니다. ADAL은 또한 액세스 토큰을 캐시화하고 필요한 대로 갱신하여 액세스 토큰의 수명 관리도 처리합니다. 이 시나리오를 보여 주는 코드 샘플에 대해서는 [웹 API에 디먼 콘솔 응용 프로그램](https://github.com/AzureADSamples/Daemon-DotNet)을 참조하세요.

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>사용자를 대신하여 서버에서 실행되는 비밀 클라이언트 응용 프로그램 인증

이 시나리오에서는 개발자가 웹 API와 같이 Azure AD에서 보호하는 원격 리소스에 액세스해야 하는 서버에서 실행 중인 응용 프로그램을 가지고 있습니다. 이것은 또한 Azure AD 사용자 대신에 요청되어야 합니다. 개발자는 Azure 구독을 가지고 있고 다운스트림 웹 API 호출 방법을 알고 있으며 서비스가 사용하는 Azure AD 테넌트를 알고 있습니다. 사용자가 웹 응용 프로그램에 인증되면 응용 프로그램이 Azure AD로부터 사용자에 대한 인증 코드를 가져올 수 있습니다. 그러면 웹 응용 프로그램에서 ADAL을 사용하여 Azure AD의 응용 프로그램과 연결된 클라이언트 자격 증명과 인증 코드를 사용하는 사용자 대신 액세스 토큰과 새로고침 토큰을 얻을 수 있습니다. 웹 응용 프로그램을 액세스 토큰에서 소유하게 되면 토큰이 만료될 때까지 웹 API를 호출할 수 있습니다. 토큰이 만료되면 웹 응용 프로그램이 ADAL을 사용하여 이전에 받은 새로고침 토큰을 사통해 새 액세스 토큰을 가져올 수 있습니다. 이 시나리오를 보여 주는 코드 샘플에 대해서는 [웹 API에 네이티브 클라이언트](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)를 참조하세요.

## <a name="see-also"></a>참고 항목

- [Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)
- [Azure Active directory 인증 시나리오](active-directory-authentication-scenarios.md)
- [Azure Active Directory 코드 샘플](active-directory-code-samples.md)
