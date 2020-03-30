---
title: 사용자에 서명하는 웹 앱 등록 - Microsoft ID 플랫폼 | Azure
description: 사용자에게 서명하는 웹 앱을 등록하는 방법 알아보기
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f2513b1fee022199f040ba0dcf83110c0b3b1365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701572"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>사용자에 서명하는 웹 앱: 앱 등록

이 문서에서는 사용자가 서명하는 웹 앱에 대한 앱 등록 세부 사항을 설명합니다.

응용 프로그램을 등록하려면 다음을 사용할 수 있습니다.

- [웹 앱은 을 빠르게 시작합니다.](#register-an-app-by-using-the-quickstarts) Azure 포털의 빠른 시작에는 응용 프로그램을 만드는 데 있어 훌륭한 첫 번째 환경일 뿐만 아니라 **나를 위해 이 변경**확인이라는 단추가 포함되어 있습니다. 이 단추를 사용하여 기존 앱에 대해서도 필요한 속성을 설정할 수 있습니다. 이러한 속성의 값을 사용자 고유의 케이스에 맞게 조정해야 합니다. 특히 앱의 웹 API URL은 제안된 기본값과 다를 수 있으며 이는 로그아웃 URI에도 영향을 미칩니다.
- 응용 프로그램을 [수동으로 등록하는](#register-an-app-by-using-the-azure-portal)Azure 포털입니다.
- PowerShell 및 명령줄 도구.

## <a name="register-an-app-by-using-the-quickstarts"></a>빠른 시작을 사용하여 앱 등록

이러한 링크를 사용하여 웹 응용 프로그램 만들기를 부트스트랩할 수 있습니다.

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Azure 포털을 사용하여 앱 등록

> [!NOTE]
> 사용할 포털은 응용 프로그램이 Microsoft Azure 퍼블릭 클라우드에서 실행되는지 또는 국가 또는 주권 클라우드에서 실행되는지에 따라 다릅니다. 자세한 내용은 [내셔널 클라우드를](./authentication-national-cloud.md#app-registration-endpoints)참조하십시오.


1. 회사 또는 학교 계정 또는 개인 Microsoft 계정을 사용하여 [Azure 포털에](https://portal.azure.com) 로그인합니다. 또는 국가 클라우드에 대해 선택한 Azure 포털에 로그인합니다.
1. 계정에서 두 개 이상의 테넌트에 액세스할 수 있는 권한을 부여하는 경우 오른쪽 상단 모서리에서 계정을 선택합니다. 그런 다음 포털 세션을 원하는 Azure Active Directory(Azure AD) 테넌트로 설정합니다.
1. 왼쪽 창에서 **Azure Active Directory** 서비스를 선택한 다음 앱 등록**새 등록을** **선택합니다.** > 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. 응용 프로그램에 대해 지원되는 계정 유형을 선택합니다. [(지원되는 계정 유형](./v2-supported-account-types.md)참조))
   1. **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름을 입력합니다. 예를 들어 **AspNetCore-WebApp을**입력합니다.
   1. **Uri 리디렉션의**경우 성공적인 인증 후 반환된 토큰 응답을 수락하는 응용 프로그램 유형과 URI 대상을 추가합니다. 예를 들어 **https://localhost:44321**을 입력합니다. 그런 다음 **등록**을 선택합니다.
1. **인증** 메뉴를 선택한 후 다음 정보를 추가합니다.
   1. **https://localhost:44321/signin-oidc** **회신 URL의**경우 **웹**형식추가.
   1. **고급 설정** 섹션에서 **로그아웃 URL**을 **https://localhost:44321/signout-oidc**으로 설정합니다.
   1. **암시적 허용**에서 **ID 토큰**을 선택합니다.
   1. **저장**을 선택합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. 응용 **프로그램 등록 페이지가** 나타나면 응용 프로그램의 등록 정보를 입력합니다.
   1. 응용 프로그램에 대해 지원되는 계정 유형을 선택합니다. [(지원되는 계정 유형](./v2-supported-account-types.md)참조))
   1. **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름을 입력합니다. 예를 들어 **MailApp-openidconnect-v2를**입력합니다.
   1. URI **리디렉션(선택 사항)** 섹션에서 콤보 상자에서 **웹을** 선택하고 다음 리디렉션 **https://localhost:44326/** URI를 입력합니다.
1. **등록**을 선택하여 애플리케이션을 만듭니다.
1. **인증** 메뉴를 선택합니다.
1. 고급 **설정** | **암시적 부여** 섹션에서 ID **토큰을 선택합니다.** 이 샘플에서는 [암시적 권한 부여 흐름을](v2-oauth2-implicit-grant-flow.md) 사용하여 사용자에 로그인해야 합니다.
1. **저장**을 선택합니다.

# <a name="java"></a>[Java](#tab/java)

1. 응용 **프로그램 등록 페이지가** 나타나면 응용 프로그램의 표시 이름을 입력합니다. 예를 들어 **java-webapp**을 입력합니다.
1. **모든 조직 디렉터리 및 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)에서 계정을**선택한 다음 응용 프로그램 **유형에**대한 **웹 앱/ API를** 선택합니다.
1. **등록을** 선택하여 신청서를 등록합니다.
1. 왼쪽 메뉴에서 **인증을**선택합니다. **리디렉션 URI에서** **웹**을 선택합니다.

1. 두 개의 리디렉션 URI를 입력합니다: 로그인 페이지에 대 한 하나, 그래프 페이지에 대 한 하나. 둘 다 에 대 한 동일한 호스트 및 포트 번호를 사용 하 여 다음 **/msal4jsample/secure/aad** 로그인 페이지에 대 한 및 **msal4jsample/graph/me** 사용자 정보 페이지에 대 한.

   기본적으로 샘플은 다음을 사용합니다.

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  그런 다음 **저장을**선택합니다.

1. 메뉴에서 **인증서 & 비밀을** 선택합니다.
1. 클라이언트 **보안 정보** 섹션에서 **새 클라이언트 보안**설정을 선택한 다음 다음을 수행합니다.

   1. 키 설명을 입력합니다.
   1. 1년 **에서**키 기간을 선택합니다.
   1. **추가**를 선택합니다.
   1. 키 값이 나타나면 나중에 복사합니다. 이 값은 다시 표시되거나 다른 방법으로 검색할 수 없습니다.

# <a name="python"></a>[Python](#tab/python)

1. 응용 **프로그램 등록 페이지가** 나타나면 응용 프로그램의 등록 정보를 입력합니다.
   1. **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름을 입력합니다. 예를 들어 **파이썬 웹랩을**입력합니다.
   1. **지원되는 계정 유형을** **조직 디렉터리 및 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)의 계정으로 변경합니다.**
   1. URI **리디렉션(선택 사항)** 섹션에서 콤보 상자에서 **웹을** 선택하고 다음 리디렉션 **http://localhost:5000/getAToken**URI를 입력합니다.
1. **등록**을 선택하여 애플리케이션을 만듭니다.
1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다. 이 프로젝트의 Visual Studio 구성 파일을 구성하는 데 필요합니다.
1. 왼쪽 메뉴에서 **인증서 & 비밀을 선택합니다.**
1. 클라이언트 **보안 정보** 섹션에서 **새 클라이언트 보안**설정을 선택한 다음 다음을 수행합니다.

   1. 키 설명을 입력합니다.
   1. **1년 후** 키 기간을 선택합니다.
   1. **추가**를 선택합니다.
   1. 키 값이 나타나면 복사합니다. 나중에 필요합니다.
---

## <a name="register-an-app-by-using-powershell"></a>PowerShell을 사용하여 앱 등록

> [!NOTE]
> 현재 Azure AD PowerShell은 다음과 같은 지원되는 계정 유형만 있는 응용 프로그램을 만듭니다.
>
> - MyOrg(이 조직 디렉터리에만 계정)
> - AnyOrg (모든 조직 디렉토리의 계정)
>
> 개인 Microsoft 계정(예: Skype, Xbox 또는 Outlook.com)을 사용하여 사용자에게 로그인하는 응용 프로그램을 만들 수 있습니다. 먼저 다중 테넌트 응용 프로그램을 만듭니다. 지원되는 계정 유형은 모든 조직 디렉터리에서 거래처입니다. 그런 다음 `signInAudience` Azure 포털에서 응용 프로그램 매니페스트의 속성을 변경합니다. 자세한 내용은 ASP.NET 코어 자습서의 [1.3 단계를](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) 참조하십시오. 이 단계를 모든 언어로 웹 앱으로 일반화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-web-app-sign-user-app-configuration.md)
