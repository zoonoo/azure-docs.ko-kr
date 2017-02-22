---
title: "Azure Active Directory 인증 라이브러리 | Microsoft Docs"
description: "Azure AD 인증 라이브러리(ADAL)는 클라이언트 응용 프로그램 개발자들이 클라우드 또는 온-프레미스 Active Directory(AD)에 쉽게 사용자를 인증하고 API 호출 보안을 위한 액세스 토큰을 가져올 수 있게 합니다."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/13/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: f369cac86f96227bb6c0881004d8dda485a26ee2
ms.openlocfilehash: c0b0e94175359bde64cf0d2bcefb53cb7530e391


---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory 인증 라이브러리
ADAL(Azure AD 인증 라이브러리)은 클라이언트 응용 프로그램 개발자들이 클라우드 또는 온-프레미스 AD(Active Directory)에 쉽게 사용자를 인증하고 API 호출 보안을 위한 액세스 토큰을 가져올 수 있게 합니다. ADAL에는 비동기 지원, 액세스 토큰 및 새로고침 토큰을 저장하는 구성 가능한 토큰 캐시, 액세스 토큰이 만료되고 새로고침 토큰을 사용할 수 있을 때 자동 토큰 새로고침 등, 개발자들의 인증을 쉽게 지원하는 많은 기능이 있습니다. 대부분의 복잡성을 처리함으로써 ADAL은 개발자가 응용 프로그램의 비즈니스 논리에 초점을 맞추고 보안 전문가가 아니더라도 리소스를 쉽게 보호할 수 있게 도와줍니다.

ADAL은 다양한 플랫폼에서 사용할 수 있습니다.

### <a name="client-libraries"></a>클라이언트 라이브러리

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참조
| --- | --- | --- | --- | --- | --- |
| .NET 클라이언트, Windows 스토어, UWP, Xamarin iOS 및 Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [데스크톱 앱](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-dotnet) |[참조](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) | 
| .NET 클라이언트, Windows 스토어, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2) | [데스크톱 앱](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) |[참조](https://docs.microsoft.com/en-us/active-directory/adal//v2/microsoft.identitymodel.clients.activedirectory) | 
| JavaScript |ADAL.js |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[단일 페이지 앱](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[CocoaPods](http://cocoadocs.org/docsets/ADAL/) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS 앱](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-ios) | [참조](http://cocoadocs.org/docsets/ADALiOS)|
| Android |ADAL |[중앙 리포지토리](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android 앱](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| 자바 |ADAL4J |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java 웹앱](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-java) | |

### <a name="server-libraries"></a>서버 라이브러리 

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참조
| --- | --- | --- | --- | --- | --- |
| .NET |AzureAD용 OWIN|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC 앱](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OpenIDConnect용 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[웹앱](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[Github](https://github.com/AzureAD/passport-azure-ad) | [앱 API](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |WS-Federation용 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC 웹앱](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |.NET 4.5용 ID 프로토콜 확장 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |.NET 4.5 JWT 핸들러 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |



## <a name="scenarios"></a>시나리오
ADAL을 인증에 사용할 수 있는&3;가지 공통 시나리오는 다음과 같습니다.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>원격 리소스에 클라이언트 응용 프로그램의 사용자를 인증
이 시나리오에서는 개발자가 웹 API와 같이 Azure AD에서 보호하는 원격 리소스에 액세스해야 하는 WPF 응용 프로그램과 같은 클라이언트를 가지고 있습니다. 개발자는 Azure 구독을 가지고 있고 다운스트림 웹 API 호출 방법을 알고 있으며 웹 API가 사용하는 Azure AD 테넌트를 알고 있습니다. 결과적으로, ADAL을 사용하여 인증 경험을 ADAL에 위임하거나 사용자 자격 증명을 명시적으로 처리함으로써 Azure AD와의 인증을 수월하게 지원할 수 있습니다. ADAL을 사용하면 쉽게 사용자를 인증하고 Azure AD에서 액세스 토큰 및 새로고침 토큰을 얻은 다음 액세스 토큰을 사용하여 웹 API에 요청할 수 있습니다.

Azure AD에 대한 인증을 사용하여 이 시나리오를 설명하는 코드 샘플의 경우 [웹 API에 네이티브 클라이언트 WPF 응용 프로그램](https://github.com/azureadsamples/nativeclient-dotnet)을 참조하세요.

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>원격 리소스에 서버 응용 프로그램 인증
이 시나리오에서는 개발자가 웹 API와 같이 Azure AD에서 보호하는 원격 리소스에 액세스해야 하는 서버에서 실행 중인 응용 프로그램을 가지고 있습니다. 개발자는 Azure 구독을 가지고 있고 다운스트림 서비스 호출 방법을 알고 있으며 웹 API가 사용하는 Azure AD 테넌트를 알고 있습니다. 결과적으로, 개발자는 ADAL을 사용하여 응용 프로그램의 자격 증명을 명시적으로 처리함으로써 Azure AD로 인증을 용이하게 지원할 수 있습니다. ADAL을 사용하면 쉽게 응용 프로그램의 클라이언트 자격 증명을 사용하여 Azure AD에서 토큰을 점검한 다음 해당 토큰을 사용하여 웹 API에 요청할 수 있습니다. ADAL은 또한 액세스 토큰을 캐시화하고 필요한 대로 갱신하여 액세스 토큰의 수명 관리도 처리합니다. 이 시나리오를 보여 주는 코드 샘플에 대해서는 [웹 API에 콘솔 응용 프로그램](https://github.com/AzureADSamples/Daemon-DotNet)을 참조하세요.

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>사용자를 대신해서 서버 응용 프로그램이 원격 리소스에 액세스할 수 있도록 인증
이 시나리오에서는 개발자가 웹 API와 같이 Azure AD에서 보호하는 원격 리소스에 액세스해야 하는 서버에서 실행 중인 응용 프로그램을 가지고 있습니다. 이것은 또한 Azure AD에서 사용자 대신에 요청되어야 합니다. 개발자는 Azure 구독을 가지고 있고 다운스트림 웹 API 호출 방법을 알고 있으며 서비스가 사용하는 Azure AD 테넌트를 알고 있습니다. 사용자가 웹 응용 프로그램에 인증되면 응용 프로그램이 Azure AD로부터 사용자에 대한 인증 코드를 가져올 수 있습니다. 그러면 웹 응용 프로그램에서 ADAL을 사용하여 Azure AD의 응용 프로그램과 연결된 클라이언트 자격 증명과 인증 코드를 사용하는 사용자 대신 액세스 토큰과 새로고침 토큰을 얻을 수 있습니다. 웹 응용 프로그램을 액세스 토큰에서 소유하게 되면 토큰이 만료될 때까지 웹 API를 호출할 수 있습니다. 토큰이 만료되면 웹 응용 프로그램이 ADAL을 사용하여 이전에 받은 새로고침 토큰을 사통해 새 액세스 토큰을 가져올 수 있습니다.

## <a name="see-also"></a>참고 항목
[Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)

[Azure Active directory 인증 시나리오](active-directory-authentication-scenarios.md)

[Azure Active Directory 코드 샘플](active-directory-code-samples.md)



<!--HONumber=Jan17_HO3-->


