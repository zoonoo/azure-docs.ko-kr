---
title: 사용자를 로그인 하는 웹 앱 (앱 등록)-Microsoft identity platform
description: 사용자를 로그인 하는 웹 앱을 빌드하는 방법 알아보기 (앱 등록)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ab7c3699abdd5c094b1b14cd53f76023fa8c1ac
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309609"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>사용자가 로그인 하는 웹 앱-앱 등록

이 페이지에서는 사용자를 로그인 하는 웹 앱에 대 한 앱 등록 세부 사항을 설명 합니다.

응용 프로그램을 등록 하려면 다음을 사용할 수 있습니다.

- [웹 앱](#register-an-app-using-the-quickstarts) 빠른 시작-응용 프로그램을 만드는 데 가장 적합 한 경험을 제공 하는 것 외에도, Azure Portal 빠른 시작에는 **이 변경을 위해**이름이 지정 된 단추가 포함 되어 있습니다. 이 단추를 사용 하 여 기존 응용 프로그램의 경우에도 필요한 속성을 설정할 수 있습니다. 이러한 속성의 값은 사용자의 사례에 맞게 조정 해야 합니다. 특히 앱에 대 한 web API URL은 제안 된 기본값과 다를 수 있으며,이는 로그 아웃 URI에도 영향을 줍니다.
- [응용 프로그램을 수동으로 등록](#register-an-app-using-azure-portal) 하는 Azure Portal
- PowerShell 및 명령줄 도구

## <a name="register-an-app-using-the-quickstarts"></a>빠른 시작을 사용 하 여 앱 등록

이 링크로 이동 하는 경우 웹 응용 프로그램을 만드는 부트스트랩을 만들 수 있습니다.

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-using-azure-portal"></a>Azure Portal를 사용 하 여 앱 등록

> [!NOTE]
> 사용할 포털은 응용 프로그램이 Microsoft Azure 공용 클라우드 또는 국가 또는 소 버린 클라우드에서 실행 되는지에 따라 달라 집니다. 자세한 내용은 [국가별 클라우드](./authentication-national-cloud.md#app-registration-endpoints) 를 참조 하세요.


1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다. 또는 국가별 클라우드 Azure Portal에 로그인 합니다.
1. 계정에서 둘 이상의 테넌트에 액세스할 수 있는 경우 오른쪽 위 모서리에서 있는 계정을 선택하고 포털 세션을 원하는 Azure AD 테넌트로 설정합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택 하 고 **앱 등록** > **새 등록**을 선택 합니다.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

4. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. 응용 프로그램에 대해 지원 되는 계정 유형 선택 ( [지원 되는 계정 유형](./v2-supported-account-types.md)참조)
   1. **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `AspNetCore-WebApp`)을 입력합니다.
   1. **Uri 리디렉션**에서 성공적으로 인증 된 후 반환 된 토큰 응답을 받을 응용 프로그램의 형식 및 URI 대상을 추가 합니다. 예를 들어, `https://localhost:44321/`을 입력합니다.  **등록**을 선택합니다.
1. **인증** 메뉴를 선택한 후 다음 정보를 추가합니다.
   1. **회신 URL**에서 "Web `https://localhost:44321/signin-oidc` " 유형의를 추가 합니다.
   1. **고급 설정** 섹션에서 **로그아웃 URL**을 `https://localhost:44321/signout-oidc`으로 설정합니다.
   1. **암시적 권한 부여**에서 **ID 토큰**을 선택합니다.
   1. **저장**을 선택합니다.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

4. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. 응용 프로그램에 대해 지원 되는 계정 유형 선택 ( [지원 되는 계정 유형](./v2-supported-account-types.md)참조)
   - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `MailApp-openidconnect-v2`)을 입력합니다.
   - URI 리디렉션 (선택 사항) 섹션의 콤보 상자에서 **웹** 을 선택 하 고 다음 리디렉션 uri `https://localhost:44326/`를 입력 합니다.
1. **등록**을 선택하여 애플리케이션을 만듭니다.
1. **인증** 메뉴를 선택한 후 다음 정보를 추가합니다.
   - **고급 설정** | **암시적 권한 부여** 섹션에서이 샘플로 **ID 토큰** 을 확인 하려면 사용자에 게 로그인 할 수 있는 [암시적 grant 흐름이](v2-oauth2-implicit-grant-flow.md) 설정 되어 있어야 합니다.
1. **저장**을 선택합니다.

# <a name="javatabjava"></a>[Java](#tab/java)

4. **응용 프로그램 등록 페이지가** 표시 되 면 응용 프로그램의 이름 (예: ' webapp ')을 입력 하 고 "모든 조직 디렉터리 및 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com)의 계정"을 선택 하 고 ' 웹 앱 '을 선택 합니다. /API '를 *응용 프로그램 형식으로 입력*합니다.
1. **등록** 을 클릭 하 여 응용 프로그램을 등록 합니다.
1. 왼쪽 메뉴에서 **인증**을 클릭 하 고, *리디렉션 Uri*에서 "웹"을 선택 합니다. 두 개의 서로 다른 리디렉션 Uri를 입력 해야 합니다. 하나는 로그인 페이지이 고 다른 하나는 그래프 사용자 페이지에 대 한 것입니다. 둘 다에 대해 동일한 호스트 및 포트 번호를 사용 하 고, 로그인 페이지의 경우 "/msal4jsample/secure/aad"이 고, 사용자 페이지에는 "msal4jsample/graph/users"가와 야 합니다.
 기본적으로이 샘플은 다음을 사용 합니다. 

    - `http://localhost:8080/msal4jsample/secure/aad`. 
    - `http://localhost:8080/msal4jsample/graph/users`

**저장**을 클릭 합니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

4. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `python-webapp`)을 입력합니다.
   - **모든 조직 디렉터리와 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com)에서** **지원 되는 계정 유형을** 계정으로 변경 합니다.
   - URI 리디렉션 (선택 사항) 섹션의 콤보 상자에서 **웹** 을 선택 하 고 다음 리디렉션 uri `http://localhost:5000/getAToken`를 입력 합니다.
1. **등록**을 선택하여 애플리케이션을 만듭니다.
1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다. 이 프로젝트에 대 한 Visual Studio 구성 파일을 구성 하는 데 필요 합니다.
1. 앱의 개요 페이지에서 **인증** 섹션을 선택 합니다.
   - **고급 설정** 섹션에서 **로그 아웃 URL** 을로 설정 합니다.`http://localhost:5000/logout`
1. **저장**을 선택합니다.

---

## <a name="register-an-app-using-powershell"></a>PowerShell을 사용 하 여 앱 등록

> [!NOTE]
> 현재 Azure AD PowerShell은 다음과 같은 지원 되는 계정 유형으로 응용 프로그램을 만듭니다.
>
> - MyOrg (이 조직 디렉터리의 계정에만 해당)
> - AnyOrg (모든 조직 디렉터리의 계정).
>
> 개인 Microsoft 계정 (예: Skype, XBox, Outlook.com)을 사용 하 여 사용자를 로그인 하는 응용 프로그램을 만들려면 먼저 다중 테 넌 트 응용 프로그램 (지원 되는 계정 유형 = 모든 조직 디렉터리의 계정)을 만든 다음 변경할 수 있습니다. Azure Portal 응용 프로그램 매니페스트의 속성입니다.`signInAudience` 이에 대해서는 ASP.NET Core 자습서의 [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) 단계에 자세히 설명 되어 있으며, 모든 언어로 된 웹 앱으로 일반화 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-web-app-sign-user-app-configuration.md)
