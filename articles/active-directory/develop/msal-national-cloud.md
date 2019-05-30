---
title: Microsoft id 플랫폼-국가 클라우드에서 Microsoft 인증 라이브러리 (MSAL) 사용
description: Microsoft 인증 라이브러리 (MSAL) 응용 프로그램 개발자를 보안된 web Api를 호출 하기 위해 토큰을 획득할 수 있습니다. 이러한 웹 Api는 Microsoft Graph, 기타 Microsoft Api, 파트너 웹 Api 또는 사용자 고유의 웹 API 수 있습니다. MSAL은 다수의 애플리케이션 아키텍처와 플랫폼을 지원합니다.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9958356cae3c486ecf68e280f33d63c6a537b14
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235260"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>MSAL을 사용 하 여 국가별 클라우드 환경에서

[내셔널 클라우드](authentication-national-cloud.md) Azure의 물리적으로 격리 된 인스턴스입니다. 이러한 Azure 지역에 지리적 경계 내에서 데이터 상주, 주권, 및 규정 준수 요구 사항이 적용 하 고 있는지 확인 하는 데 도움이 되 합니다.

Microsoft worldwide cloud 외에도 Microsoft 인증 라이브러리 (MSAL) 응용 프로그램 개발자는 national 클라우드에서 인증 하 고 보안된 웹 Api를 호출 하기 위해 토큰을 획득 하 합니다. 이러한 웹 Api는 Microsoft Graph 또는 기타 Microsoft Api 수 있습니다.

글로벌 클라우드를 비롯 하 여 Azure Active Directory (Azure AD)는 다음 국가별 클라우드에 배포 됩니다.  

- Azure Government
- Azure China 21Vianet
- Azure Germany

이 가이드를 작동 및 학교 계정, 액세스 토큰을 가져오기 Microsoft Graph API 호출에 로그인 하는 방법에 설명 합니다 [Azure Government 클라우드](https://azure.microsoft.com/global-infrastructure/government/) 환경입니다.

## <a name="prerequisites"></a>필수 조건

시작 하기 전에 이러한 필수 구성이 요소를 충족 하는지 확인 합니다.

### <a name="choose-the-appropriate-identities"></a>적절 한 id를 선택 합니다.

[Azure Government](https://docs.microsoft.com/azure/azure-government/) 응용 프로그램 사용자 인증을 Azure AD Government id와 Azure AD 공용 id를 사용할 수 있습니다. 이러한 id 중 하나를 사용할 수 있으므로 시나리오에 대 한 선택 해야 인증 기관 끝점을 결정 해야 합니다.

- Azure AD 공개 합니다. 조직에는 Azure AD 공개 테 넌 트를 Office 365 지원 (공개 또는 GCC) 또는 다른 응용 프로그램에 있는 경우에 흔히 사용 합니다.
- Azure AD 정부: 이미 Azure AD 정부 테 넌 트에 Office 365 지원 (GCC High 또는 DoD) 또는 Azure AD 정부에 새 테 넌 트를 만드는 것 조직의 경우에 흔히 사용 합니다.

결정 한 후는 특별 한 고려 사항은 앱 등록을 수행 하는 위치입니다. Azure Government 응용 프로그램에 대 한 Azure AD 공개 identities를 선택 하면 Azure AD 공개 테 넌 트에서 응용 프로그램을 등록 해야 합니다.

### <a name="get-an-azure-government-subscription"></a>Azure Government 구독 가져오기

Azure Government 구독을 참조 하세요 [관리 및 Azure Government에서 구독을 연결할](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)합니다.

Azure Government 구독을 없다면 만들기는 [무료 계정](https://azure.microsoft.com/global-infrastructure/government/request/) 시작 하기 전에 합니다.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

1. [Azure Portal](https://portal.azure.us/)에 로그인합니다.
    
   Azure 포털 끝점 다른 국가 클라우드에 대 한 참조 하세요 [앱 등록 끝점](authentication-national-cloud.md#app-registration-endpoints)합니다.

1. 둘 이상의 테 넌 트에 대 한 액세스, 오른쪽 위 모서리에서 계정을 선택 하 고 원하는 Azure ad 포털 세션을 설정 하 여 계정에서는 테 넌 트 경우.
1. 로 이동 합니다 [앱 등록](https://aka.ms/ra/ff) 개발자를 위한 Microsoft id 플랫폼에는 페이지입니다.
1. **애플리케이션 등록** 페이지가 나타나면 애플리케이션의 이름을 입력합니다.
1. 아래 **지원 되는 계정 유형**를 선택 **조직 디렉터리의 계정**합니다.
1. 에 **리디렉션 URI** 섹션에서는 **웹** 플랫폼 및 웹 서버에 따라 응용 프로그램의 URL로 값을 설정 합니다. Visual Studio와 노드 리디렉션 URL을 가져오고 설정 하는 방법에 대 한 지침은 다음 섹션을 참조 합니다.
1. **등록**을 선택합니다.
1. 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
1. 이 자습서를 사용 하도록 설정 해야 합니다 [암시적 허용 흐름](v2-oauth2-implicit-grant-flow.md)합니다. 등록된 된 응용 프로그램의 왼쪽된 창에서 선택 **인증**합니다.
1. **고급 설정**아래에 있는 **암시적 권한 부여**를 선택 합니다 **ID 토큰** 및 **액세스 토큰을** 확인란 합니다. ID 토큰 및 액세스 토큰은이 앱이 사용자를 로그인 하 고 API를 호출 해야 하기 때문에 필요 합니다.
1. **저장**을 선택합니다.

### <a name="step-2--set-up-your-web-server-or-project"></a>2단계:  웹 서버 또는 프로젝트 설정

- [프로젝트 파일 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) 노드 같은 로컬 웹 서버에 대 한 합니다.

  또는

- [Visual Studio 프로젝트를 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)합니다.

로 건너뜁니다 [JavaScript SPA 구성](#step-4-configure-your-javascript-spa) 실행 하기 전에 코드 샘플을 구성 합니다.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>3단계: Microsoft 인증 라이브러리를 사용 하 여 사용자를 로그인

단계를 수행 합니다 [JavaScript 자습서](tutorial-v2-javascript-spa.md#create-your-project) 프로젝트를 만들고 사용자를 로그인 하 여 MSAL을 사용 하 여 통합 합니다.

### <a name="step-4-configure-your-javascript-spa"></a>4단계: JavaScript SPA 구성

프로젝트 설정 중에 생성된 `index.html` 파일에서 애플리케이션 등록 정보를 추가합니다. 맨 위에 있는 다음 코드를 `index.html` 파일 본문의 `<script></script>` 태그 내에 추가합니다.

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

해당 코드:

- `Enter_the_Application_Id_here` **응용 프로그램 (클라이언트) ID** 등록 응용 프로그램에 대 한 값입니다.
- `Enter_the_Tenant_Info_Here` 다음 옵션 중 하나로 설정 됩니다.
    - 응용 프로그램이 지 원하는 경우 **이 조직 디렉터리의 계정**, 테 넌 트 ID를 사용 하 여이 값을 대체 또는 테 넌 트 이름 (예를 들어 contoso.microsoft.com).
    - 응용 프로그램이 지 원하는 경우 **모든 조직 디렉터리의 계정**를 사용 하 여이 값을 대체 `organizations`합니다.
    
    인증 끝점 national 모든 클라우드에 대 한 참조 하세요 [Azure AD 인증 끝점](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)합니다.

    > [!NOTE]
    > 개인 Microsoft 계정 국가 클라우드에서 지원 되지 않습니다.
  
- `graphEndpoint` 미국 정부용 Microsoft 클라우드를 위한 Microsoft Graph 끝점이입니다.

   Microsoft Graph 끝점 national 모든 클라우드에 대 한 참조 하세요 [국가 클라우드에서 Microsoft Graph 끝점](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)합니다.

## <a name="net"></a>.NET

사용자를 로그인 토큰을 획득 하 고 국가 클라우드에서 Microsoft Graph API를 호출 하려면 MSAL.NET을 사용할 수 있습니다.

다음 자습서에는.NET Core 2.2 MVC 웹 앱을 빌드하는 방법을 보여 줍니다. 앱 국가별 클라우드에 속해 있는 조직의 회사 및 학교 계정을 사용 하 여 사용자를 로그인에 OpenID Connect을 사용 합니다.

- 사용자를 로그인 하 고 토큰을 획득 하려면 따릅니다 [이 자습서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)합니다.
- Microsoft Graph API를 호출 하려면 다음과 [이 자습서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)합니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure 독일](https://docs.microsoft.com/azure/germany/)