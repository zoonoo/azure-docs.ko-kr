---
title: 웹 앱 (앱 등록)-사용자가 로그인 하는 Microsoft id 플랫폼
description: 사용자 (앱 등록)에 로그인 하는 웹 앱을 빌드하는 방법을 알아봅니다
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
ms.openlocfilehash: 0ae638f8cbef29c5d167a3ab59188169cbd934ef
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150218"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>사용자-앱 등록에서에서 로그인 하는 웹 앱

이 페이지는 로그인 사용자에 게 웹 앱에 대 한 앱 등록 세부 정보를 설명합니다.

응용 프로그램을 등록 하려면 다음을 사용할 수 있습니다.

- 합니다 [웹 앱 빠른 시작](#register-an-app-using-the-quickstarts) -Azure portal에서 빠른 시작의 라는 단추가 포함 될 뿐 아니라 첫 번째 환경을 개선 응용 프로그램 만들기, **르네 이와 같이 변경**합니다. 기존 앱에 대해서도 필요한 속성을 설정 하려면이 단추를 사용할 수 있습니다. 자신의 사례에 이러한 속성의 값을 조정 해야 합니다. 특히, 앱에 대 한 웹 API URL 것에 영향을 로그 아웃 URI는 제안 된 기본값을 다를 것입니다.
- Azure portal [응용 프로그램을 수동으로 등록](#register-an-app-using-azure-portal)
- PowerShell 및 명령줄 도구

## <a name="register-an-app-using-the-quickstarts"></a>퀵 스타트를 사용 하 여 앱 등록

이 링크로 이동 하면 만들면 부트스트랩 웹 응용 프로그램의 생성 됩니다.

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Azure portal을 사용 하 여 앱 등록

> [!NOTE]
> 포털을 사용 하는 국가 또는 소 버린 클라우드 또는 Microsoft Azure 공용 클라우드에서 응용 프로그램을 실행 하는 경우에 따라 다릅니다. 자세한 내용은 참조 하세요. [내셔널 클라우드](./authentication-national-cloud.md#app-registration-endpoints)

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다. 또는 국가 클라우드 선택한 Azure portal에 로그인 합니다.
1. 둘 이상의 테 넌 트에 대 한 액세스, 오른쪽 위 모서리에서 계정을 선택 하 고 원하는 Azure ad 포털 세션을 설정 하 여 계정에서는 테 넌 트 경우.
1. 왼쪽 탐색 창에서 선택 합니다 **Azure Active Directory** 서비스를 선택한 후 **앱 등록** > **새 등록**합니다.
1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. 응용 프로그램에 대해 지원 되는 계정 유형 선택 (참조 [지원 되는 계정 유형](./v2-supported-account-types.md))
   1. **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `AspNetCore-WebApp`)을 입력합니다.
   1. **리디렉션 URI**응용 프로그램 종류를 추가 하 고 허용 하는 URI 대상을 성공적으로 인증 한 후 토큰 응답을 반환 합니다. 예: `https://localhost:44321/`  **등록**을 선택합니다.
1. **인증** 메뉴를 선택한 후 다음 정보를 추가합니다.
   1. **회신 URL**에 추가 `https://localhost:44321/signin-oidc`합니다.
   1. **고급 설정** 섹션에서 **로그아웃 URL**을 `https://localhost:44321/signout-oidc`으로 설정합니다.
   1. **암시적 권한 부여**에서 **ID 토큰**을 선택합니다.
   1. **저장**을 선택합니다.

### <a name="register-an-app-using-powershell"></a>PowerShell을 사용 하 여 앱 등록

> [!NOTE]
> 현재 Azure AD PowerShell 같은 지원 되는 계정 유형을 사용 하 여 응용 프로그램을만 만듭니다.
>
> - MyOrg (만이 조직 디렉터리의 계정)
> - AnyOrg (임의의 조직 디렉터리에 있는 계정)입니다.
>
> 응용 프로그램 로그인 사용자가 자신의 개인 Microsoft 계정 (예: Skype, XBox, Outlook.com)을 만들려면 하려는 경우 다중 테 넌 트 응용 프로그램을 처음 만들 수 있습니다 (지원 되는 계정 유형 모든 조직 디렉터리의 계정 =)를 변경한 후 `signInAudience` Azure portal에서 응용 프로그램 매니페스트에 속성입니다. 이 단계에서 세부 정보에 설명 되어 [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) ASP.NET Core 자습서 (및 모든 언어에서 web apps를 일반화할 수 있습니다).

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-web-app-sign-user-app-configuration.md)
