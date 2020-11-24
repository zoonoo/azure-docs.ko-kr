---
title: Azure AD 앱 프록시를 사용 하는 온-프레미스 앱에 대 한 헤더 기반 Single Sign-On
description: 헤더 기반 인증으로 보호 되는 온-프레미스 응용 프로그램에 대 한 Single Sign-On를 제공 하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: d39d72a79d4b273918986d0d350df4706592c77d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503172"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy-preview"></a>Azure AD 앱 프록시 (미리 보기)를 사용 하는 온-프레미스 앱에 대 한 헤더 기반 Single Sign-On

Azure Active Directory (Azure AD) 응용 프로그램 프록시는 인증에 헤더를 사용 하는 응용 프로그램에 대 한 Single Sign-On 액세스를 기본적으로 지원 합니다. Azure AD에서 응용 프로그램에 필요한 헤더 값을 구성할 수 있습니다. 헤더 값은 응용 프로그램 프록시를 통해 응용 프로그램으로 전송 됩니다. 응용 프로그램 프록시를 사용 하 여 헤더 기반 인증에 대 한 기본 지원을 사용할 경우의 몇 가지 이점은 다음과 같습니다.  

* **온-프레미스 앱에 대 한 원격 액세스 제공 간소화** -앱 프록시를 사용 하면 기존 원격 액세스 아키텍처를 단순화할 수 있습니다. 이러한 앱에 대 한 VPN 액세스를 바꿀 수 있습니다. 인증을 위해 온-프레미스 id 솔루션에 대 한 종속성을 제거할 수도 있습니다. 사용자는 회사 응용 프로그램을 사용 하기 위해 로그인 할 때 다른 것을 알 수 없습니다. 여전히 어디에서든지 모든 디바이스에서 작업할 수 있습니다.  

* **앱에 대 한 추가 소프트웨어 또는 변경 없음** -기존 응용 프로그램 프록시 커넥터를 사용할 수 있으며 추가 소프트웨어를 설치할 필요가 없습니다.  

* **사용 가능한 다양 한 특성 및 변환 목록** -사용 가능한 모든 헤더 값은 Azure AD에서 발급 한 표준 클레임을 기반으로 합니다. [SAML 또는 OIDC 응용 프로그램에 대 한 클레임을 구성](../develop/active-directory-saml-claims-customization.md#attributes) 하는 데 사용할 수 있는 모든 특성 및 변환을 헤더 값으로 사용할 수도 있습니다. 

## <a name="pre-requisites"></a>필수 구성 요소
헤더 기반 인증 앱에 대 한 Single Sign-On를 시작 하기 전에 다음과 같은 설정 및 구성을 사용 하 여 환경이 준비 되어 있는지 확인 합니다.
- 응용 프로그램 프록시를 사용 하도록 설정 하 고 응용 프로그램에 사이트 회선이 있는 커넥터를 설치 해야 합니다. [응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) 자습서를 참조 하 여 온-프레미스 환경을 준비 하 고, 커넥터를 설치 및 등록 하 고, 커넥터를 테스트 하는 방법을 알아보세요. 

## <a name="supported-capabilities"></a>지원되는 기능

다음 표에서는 응용 프로그램 프록시에서 지원 되는 헤더 기반 인증 응용 프로그램에 필요한 일반적인 기능을 보여 줍니다. 

|요구 사항   |Description|
|----------|-----------|
|페더레이션 SSO |사전 인증 모드에서 모든 응용 프로그램은 Azure AD 인증을 사용 하 여 보호 되 고 사용자가 Single Sign-On 수 있습니다. |
|원격 액세스 |응용 프로그램 프록시를 사용 하면 앱에 원격으로 액세스할 수 있습니다. 사용자는 외부 URL을 사용 하 여 브라우저에서 인터넷을 통해 응용 프로그램에 액세스할 수 있습니다. 응용 프로그램 프록시는 회사 액세스에 사용 하기 위한 것이 아닙니다. |
|헤더 기반 통합 |응용 프로그램 프록시는 Azure AD와 SSO 통합을 수행한 다음 id 또는 기타 응용 프로그램 데이터를 HTTP 헤더로 응용 프로그램에 전달 합니다. |
|응용 프로그램 권한 부여 |액세스 되는 응용 프로그램, 사용자의 그룹 멤버 자격 및 기타 정책에 따라 일반적인 정책을 지정할 수 있습니다. Azure AD에서 정책은 [조건부 액세스](../conditional-access/overview.md)를 사용 하 여 구현 됩니다. 응용 프로그램 권한 부여 정책은 초기 인증 요청에만 적용 됩니다. |
|인증 단계 |예를 들어 중요 한 리소스에 대 한 액세스 권한을 얻기 위해 정책을 추가 하 여 추가 된 인증을 강제 적용할 수 있습니다. |
|세분화 한 권한 부여 |URL 수준에서 액세스 제어를 제공 합니다. 액세스 되는 URL에 따라 추가 된 정책을 적용할 수 있습니다. 앱에 대해 구성 된 내부 URL은 정책이 적용 되는 앱의 범위를 정의 합니다. 가장 세분화 된 경로에 대해 구성 된 정책이 적용 됩니다.  |

> [!NOTE] 
> 이 문서에서는 응용 프로그램 프록시를 사용 하 여 헤더 기반 인증 응용 프로그램을 Azure AD에 연결 하는 기능을 권장 합니다. 또는 Azure AD와 함께 Azure AD에 대 한 액세스를 사용 하 여 헤더 기반 인증을 사용 하는 통합 패턴도 있습니다. 자세한 내용은 [응용 프로그램 프록시를 사용 하는 Single Sign-On에 대 한 헤더 기반 인증 및 액세스](application-proxy-ping-access-publishing-guide.md)를 참조 하세요.

## <a name="how-it-works"></a>작동 방법

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="헤더 기반 Single Sign-On 응용 프로그램 프록시와 작동 하는 방법입니다." lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. 관리자는 Azure AD 포털에서 응용 프로그램에 필요한 특성 매핑을 사용자 지정 합니다. 
2. 사용자가 앱에 액세스 하면 응용 프로그램 프록시는 사용자가 Azure AD에서 인증 됨을 확인 합니다. 
3. 응용 프로그램 프록시 클라우드 서비스는 필요한 특성을 인식 합니다. 따라서 서비스는 인증 중에 받은 ID 토큰에서 해당 클레임을 페치합니다. 그런 다음 서비스는 커넥터에 대 한 요청의 일부로 값을 필요한 HTTP 헤더로 변환 합니다. 
4. 그러면 요청은 커넥터에 따라 전달 되 고 백 엔드 응용 프로그램에 전달 됩니다. 
5. 응용 프로그램은 헤더를 수신 하 고 필요에 따라 이러한 헤더를 사용할 수 있습니다. 

## <a name="publish-the-application-with-application-proxy"></a>응용 프로그램 프록시를 사용 하 여 응용 프로그램 게시

1. 응용 프로그램 [프록시를 사용 하 여](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)응용 프로그램 게시에 설명 된 지침에 따라 응용 프로그램을 게시 합니다.  
    - 내부 URL 값은 응용 프로그램의 범위를 결정 합니다. 응용 프로그램의 루트 경로에서 내부 URL 값을 구성 하는 경우 루트 아래의 모든 하위 경로에 동일한 헤더 구성과 기타 응용 프로그램 구성이 표시 됩니다. 
    - 구성 된 응용 프로그램 보다 더 세분화 된 경로에 대 한 다른 헤더 구성 또는 사용자 할당을 설정 하는 새 응용 프로그램을 만듭니다. 새 응용 프로그램에서 필요한 특정 경로를 사용 하 여 내부 URL을 구성한 다음이 URL에 필요한 특정 헤더를 구성 합니다. 응용 프로그램 프록시는 항상 구성 설정과 응용 프로그램에 대해 설정 된 가장 세분화 된 경로를 일치 시킵니다. 

2.  **Azure Active Directory**    **사전 인증 방법** 으로 Azure Active Directory를 선택 합니다. 
3. **사용자 및 그룹** 으로 이동 하 고 적절 한 사용자 및 그룹을 할당 하 여 테스트 사용자를 할당 합니다. 
4. 브라우저를 열고 응용 프로그램 프록시 설정에서 **외부 URL** 로 이동 합니다   . 
5. 응용 프로그램에 연결할 수 있는지 확인 합니다. 연결할 수 있지만 헤더가 구성 되지 않았기 때문에 앱에 액세스할 수 없습니다. 

## <a name="configure-single-sign-on"></a>Single Sign-On 구성 
헤더 기반 응용 프로그램에 대 한 Single Sign-On를 시작 하기 전에 응용 프로그램 프록시 커넥터를 이미 설치 했 고 커넥터가 대상 응용 프로그램에 액세스할 수 있어야 합니다. 그렇지 않은 경우 [자습서: Azure AD 응용 프로그램 프록시](application-proxy-add-on-premises-application.md)에 있는 단계를 수행 하   고 여기로 돌아옵니다. 

1. 응용 프로그램이 엔터프라이즈 응용 프로그램 목록에 표시 되 면 해당 응용 프로그램을 선택 하 고 **Single sign-on** 을 선택 합니다. 
2. Single Sign-On 모드를 **헤더 기반** 으로 설정 합니다. 
3. 기본 구성에서 **Azure Active Directory** 는 기본값으로 선택 됩니다. 
4. 응용 프로그램에 보낼 헤더를 구성 하는 헤더에서 연필 편집을 선택 합니다. 
5. **새 헤더 추가** 를 선택 합니다. 헤더의 **이름을** 제공 하 고, **특성** 또는 **변환** 중 하나를 선택 하 고, 응용 프로그램에 필요한 헤더를 드롭다운에서 선택 합니다.  
    - 사용할 수 있는 특성 목록에 대 한 자세한 내용은 [클레임 사용자 지정-특성](../develop/active-directory-saml-claims-customization.md#attributes)을 참조 하세요. 
    - 사용 가능한 변환 목록에 대해 자세히 알아보려면 [클레임 사용자 지정-클레임 변환](../develop/active-directory-saml-claims-customization.md#claim-transformations)을 참조 하세요. 
    - **그룹 헤더** 를 추가 하 여 사용자가 속한 모든 그룹 또는 응용 프로그램에 헤더로 할당 된 그룹을 보낼 수도 있습니다. 그룹을 값으로 구성 하는 방법에 대 한 자세한 내용은 [응용 프로그램에 대 한 그룹 클레임 구성](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration)을 참조 하세요. 
6. [저장]을 선택합니다. 

## <a name="test-your-app"></a>앱 테스트 

이러한 모든 단계를 완료 하면 앱이 실행 중이 고 사용 가능 해야 합니다. 앱을 테스트 하려면: 
1. 새 브라우저 또는 개인 브라우저 창을 열어 이전에 캐시 된 헤더가 지워지는 지 확인 합니다. 그런 다음 **External URL**   응용 프로그램 프록시 설정에서 외부 URL로 이동 합니다.
2. 앱에 할당한 테스트 계정으로 로그인합니다. SSO를 사용 하 여 응용 프로그램을 로드 하 고 로그인 할 수 있으면 좋습니다. 


## <a name="next-steps"></a>다음 단계

- [Single Sign-On이란?](what-is-single-sign-on.md)
- [응용 프로그램 프록시 란?](what-is-application-proxy.md)
- [애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)
