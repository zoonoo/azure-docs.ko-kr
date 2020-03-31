---
title: 마이크로 소프트 ID 플랫폼 & SAML 보유자 어설션 흐름 | Azure
description: SAML 보유자 어설션 흐름을 사용하여 자격 증명을 표시하지 않고 Microsoft 그래프에서 데이터를 가져오는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: umeshbarapatre
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 16f30473ded5f1de5dc94c1cff9da96165b1a01c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76700212"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>마이크로소프트 아이덴티티 플랫폼 및 OAuth 2.0 SAML 보유자 어설션 흐름
OAuth 2.0 SAML 보유자 어설션 흐름을 사용하면 클라이언트가 기존 트러스트 관계를 사용해야 할 때 SAML 어설션을 사용하여 OAuth 액세스 토큰을 요청할 수 있습니다. SAML 어설션에 적용된 서명은 인증된 앱의 인증을 제공합니다. SAML 어설션은 ID 공급자가 발급하고 서비스 공급자가 사용하는 XML 보안 토큰입니다. 서비스 공급자는 보안 관련 목적으로 어설션의 주체를 식별하기 위해 해당 콘텐츠에 의존합니다.

SAML 어설션은 OAuth 토큰 끝점에 게시됩니다.  끝점은 어설션을 처리하고 앱의 사전 승인을 기반으로 액세스 토큰을 발행합니다. 클라이언트는 새로 고침 토큰을 갖거나 저장할 필요가 없으며 토큰 끝점에 전달해야 하는 클라이언트 보안도 없습니다.

SAML 베어러 어설션 흐름은 사용자에게 자격 증명을 요청하지 않고 위임된 권한만 지원하는 Microsoft Graph API에서 데이터를 가져올 때 유용합니다. 이 시나리오에서는 백그라운드 프로세스에 대 한 기본 설정 된 클라이언트 자격 증명 부여 작동 하지 않습니다.

대화형 브라우저 기반 로그인을 수행하여 SAML 어설션을 얻고 OAuth 보호 API(예: Microsoft Graph)에 대한 액세스를 추가하려는 응용 프로그램의 경우 OAuth 요청을 수행하여 API에 대한 액세스 토큰을 얻을 수 있습니다. 브라우저가 사용자를 인증하기 위해 Azure AD로 리디렉션되면 브라우저는 SAML 로그인에서 세션을 선택하고 사용자는 자격 증명을 입력할 필요가 없습니다.

OAuth SAML 보유자 어설션 흐름은 Azure Active Directory에 페더레이션된 ADFS(Active Directory Federation Services)와 같은 ID 공급자를 사용하여 인증하는 사용자에게도 지원됩니다.  ADFS에서 얻은 SAML 어설션은 OAuth 흐름에서 사용하여 사용자를 인증할 수 있습니다.

![오트 흐름](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>SAML 베어러 어설션을 사용하여 호출 그래프
이제 SAML 어설션을 프로그래밍적으로 실제로 가져올 수 있는 방법을 살펴보겠습니다. 이 방법은 ADFS로 테스트됩니다. 그러나 SAML 어설션의 반환을 프로그래밍적으로 지원하는 ID 공급자와 함께 작동합니다. 기본 프로세스는 SAML 어설션을 얻고, 액세스 토큰을 얻고, Microsoft Graph에 액세스하는 것입니다.

### <a name="prerequisites"></a>사전 요구 사항

권한 부여 서버/환경(Microsoft 365)과 ID 공급자 또는 SAML 2.0 보유자 어설션(ADFS) 발급자 간에 트러스트 관계를 설정합니다. 단일 사인온 및 ID 공급자에 대해 ADFS를 구성하려면 [이 문서를](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/)참조할 수 있습니다.

[포털에](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)응용 프로그램을 등록합니다.
1. [포털의 앱 등록 블레이드에](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 로그인합니다(그래프 API에 v2.0 끝점을 사용하므로 이 포털에서 응용 프로그램을 등록해야 합니다. 그렇지 않으면 Azure active 디렉터리에서 등록을 사용할 수 있습니다. 
1. **새 등록**을 선택합니다.
1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다. 
    1. **이름** -앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름을 입력합니다.
    1. **지원되는 계정 유형** - 애플리케이션이 지원하려는 계정을 선택합니다.
    1. **리디렉션 URI(선택 사항)** - 빌드하는 앱, 웹 또는 공용 클라이언트(모바일 & 데스크톱)를 선택한 다음 응용 프로그램에 대한 리디렉션 URI(또는 회신 URL)를 입력합니다.
    1. 작업을 마쳤으면 **등록**을 선택합니다.
1. 응용 프로그램(클라이언트) ID를 기록합니다.
1. 왼쪽 창에서 인증서 **& 비밀을 선택합니다.** 클라이언트 **보안 섹션에서 새 클라이언트 비밀을** **클릭합니다.** 새 클라이언트 비밀을 복사하면 블레이드를 떠날 때 검색할 수 없습니다.
1. 왼쪽 창에서 API **권한을** 선택한 다음 **사용 권한을 추가합니다.** **Microsoft 그래프를**선택한 다음 **권한을 위임한**다음 Outlook 그래프 API를 사용하려는 이후 **Tasks.read를** 선택합니다. 

[Postman,](https://www.getpostman.com/)샘플 요청을 테스트 하는 데 필요한 도구를 설치 합니다.  나중에 요청을 코드로 변환할 수 있습니다.

### <a name="get-the-saml-assertion-from-adfs"></a>ADFS에서 SAML 어설션 가져오기
SOAP 봉투를 사용하여 ADFS 끝점에 POST 요청을 만들어 SAML 어설션을 가져옵니다.

![SAML 어설션 받기](./media/v2-saml-bearer-assertion/2.png)

헤더 값:

![헤더 값](./media/v2-saml-bearer-assertion/3.png)

ADFS 요청 본문:

![ADFS 요청 본문](./media/v2-saml-bearer-assertion/4.png)

이 요청이 성공적으로 게시되면 ADFS에서 SAML 어설션을 받게 됩니다. **SAML:어설션** 태그 데이터만 필요하며 추가 요청에 사용할 base64 인코딩으로 변환합니다.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>SAML 어설션을 사용하여 OAuth2 토큰 받기 
이 단계에서는 ADFS 어설션 응답을 사용하여 OAuth2 토큰을 가져옵니다.

1. 헤더 값으로 아래와 같이 POST 요청을 만듭니다.

    ![POST 요청](./media/v2-saml-bearer-assertion/5.png)
1. 요청 본문에서 **client_id**바꾸기 , **client_secret**및 **어설션(base64** 인코딩된 SAML 어설션은 이전 단계를 수득했습니다).

    ![요청 본문](./media/v2-saml-bearer-assertion/6.png)
1. 요청이 성공하면 Azure 활성 디렉터리에서 액세스 토큰을 받게 됩니다.

### <a name="get-the-data-with-the-oauth-token"></a>Oauth 토큰으로 데이터 얻기

액세스 토큰을 받은 후 그래프 API(이 예제의 Outlook 작업)를 호출합니다. 

1. 이전 단계에서 가져온 액세스 토큰으로 GET 요청을 만듭니다.

    ![GET 요청](./media/v2-saml-bearer-assertion/7.png)

1. 요청이 완료되면 JSON 응답을 받게 됩니다.

## <a name="next-steps"></a>다음 단계

다양한 인증 [흐름 및 응용 프로그램 시나리오에](authentication-flows-app-scenarios.md)대해 알아봅니다.