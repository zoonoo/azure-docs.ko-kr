---
title: Ping Id를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
titleSuffix: Azure AD B2C
description: Ping Id를 사용 하 여 Azure AD B2C 인증을 통합 하는 방법을 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 94e7ae93d05ae8ee35028882e14d8da74814d833
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650229"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>자습서: 보안 하이브리드 액세스를 위해 Azure Active Directory B2C를 사용 하 여 Ping Id 구성

이 샘플 자습서에서는 보안 하이브리드 액세스를 사용 하도록 설정  [하는 B2C](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) (AD)를 Azure Active Directory 확장 하 [는 방법에](https://www.pingidentity.com/en/software/pingfederate.html) 대해 알아봅니다.

인터넷에 노출 되는 전자 상거래 사이트 및 웹 응용 프로그램과 같은 많은 기존 웹 속성은 프록시 시스템 뒤에 배포 되며, 때로는 역방향 프록시 시스템이 라고도 합니다. 이러한 프록시 시스템은 사전 인증, 정책 적용 및 트래픽 라우팅을 비롯 한 다양 한 기능을 제공 합니다. 사용 사례에는 인바운드 웹 트래픽 으로부터 웹 응용 프로그램을 보호 하 고 분산 서버 배포에서 일관 된 세션 관리를 제공 하는 작업이 포함 됩니다.

대부분의 경우이 구성에는 웹 응용 프로그램에서 인증을 페이지로 바뀌었습니다 인증 변환 계층이 포함 됩니다. 역방향 프록시는 인증 된 사용자의 컨텍스트를 일반 형식 또는 다이제스트 형식의 헤더 값과 같은 간단한 형식으로 웹 응용 프로그램에 제공 합니다. 이러한 구성에서 응용 프로그램은 프록시를 사용 하 여 인증 컨텍스트를 제공 하 고 브라우저 또는 네이티브 응용 프로그램과 같은 최종 사용자 에이전트로 세션을 유지 관리 하는 것이 아니라 SAML (Security Assertion Markup Language), OAuth 또는 Open ID Connect (OIDC)와 같은 산업 표준 토큰을 사용 하지 않습니다. "메시지 가로채기 (man-in-the-middle)"에서 실행 되는 서비스의 경우 프록시는 최종 세션 제어를 제공할 수 있습니다. 이는 프록시 서비스를 사용 하는 것이 매우 효율적이 고 확장 가능 해야 한다는 것을 의미 합니다 .이는 프록시 서비스 뒤의 응용 프로그램에 대 한 단일 실패 지점이 나 병목 현상이 아닌 것입니다. 다이어그램은 일반적인 역방향 프록시 구현 및 통신 흐름을 설명 합니다.

![이미지에서 역방향 프록시 구현을 보여 줍니다.](./media/partner-ping/reverse-proxy.png)

이러한 구성에서 id 플랫폼을 현대화 하는 상황에 있는 경우 다음 사항을 고려해 야 합니다.

- 응용 프로그램 현대화 id 플랫폼 현대화에서 분리 될 수 있는 방법은 무엇 인가요?

- 최신 인증과 레거시 인증을 사용 하 여 공존 환경을 설정 하는 방법은 현대화 identity service provider에서 사용 하는 방법

  a. 최종 사용자 환경 일관성을 유지 하는 방법

  b. 여러 응용 프로그램에서 단일 로그인 환경을 제공 하는 방법

이러한 [Azure AD B2C 문제를](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) [해결 하](https://www.pingidentity.com/en/software/pingfederate.html) 는 방법에 대해 설명 합니다.

## <a name="coexistence-environment"></a>공존 환경

기술적으로 실행 가능한 간단한 솔루션인 현대화 id 시스템을 사용 하 여 인증을 위임 하는 역방향 프록시 시스템을 구성 하는 것도 효율적입니다.  
이 경우 프록시는 최신 인증 프로토콜을 지원 하 고 사용자를 새 Id 공급자 (IdP)에 게 보내는 수동 인증 인증을 사용 합니다.

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Id 공급자로 Azure AD B2C

Azure AD B2C에는 서버 쪽에서 오케스트레이션로 **사용자 경험** 불리는 다른 사용자 환경 및 동작을 구동 하는 **정책을** 정의할 수 있는 기능이 있습니다. 이러한 각 정책은 IdP 하는 것 처럼 인증을 수행할 수 있는 프로토콜 끝점을 노출 합니다. 특정 정책에 대 한 응용 프로그램 쪽에는 특별 한 처리가 필요 하지 않습니다. 응용 프로그램은 단지 관련 정책에 의해 노출 되는 프로토콜 관련 인증 끝점에 대 한 업계 표준 인증 요청을 만듭니다.  
Azure AD B2C는 각 정책에 대 한 여러 정책 또는 고유한 발급자에서 동일한 발급자를 공유 하도록 구성할 수 있습니다. 각 응용 프로그램은 프로토콜 네이티브 인증 요청을 만들고 로그인, 등록 및 프로필 편집과 같은 원하는 사용자 동작을 구동 하 여 이러한 정책 중 하나 이상을 가리킬 수 있습니다. 다이어그램은 OIDC 및 SAML 응용 프로그램 워크플로를 보여 줍니다.

![OIDC 및 SAML 구현을 보여 주는 이미지](./media/partner-ping/azure-ad-identity-provider.png)

언급 된 시나리오는 현대화 응용 프로그램에 적합 하지만, 응용 프로그램에 대 한 액세스 요청에 사용자 환경에 대 한 컨텍스트가 포함 되지 않을 수 있으므로 레거시 응용 프로그램에서 사용자를 적절 하 게 리디렉션하는 것이 어려울 수 있습니다. 대부분의 경우 프록시 계층 또는 웹 응용 프로그램의 통합 에이전트가 액세스 요청을 가로챕니다.

### <a name="pingaccess-as-a-reverse-proxy"></a>역방향 프록시로 액세스

대부분의 고객은이 문서의 앞부분에서 설명한 대로 하나 이상의 역할을 재생 하는 역방향 프록시로 액세스를 배포 했습니다. 웹 액세스는 중간자 또는 웹 응용 프로그램 서버에서 실행 되는 에이전트에서 제공 되는 리디렉션으로 직접 요청을 가로챌 수 있습니다.

OIDC, OAuth2 또는 SAML를 사용 하 여 업스트림 인증 공급자에 대 한 인증을 수행 하도록 구성할 수 있습니다. IdP 액세스 서버에서이 목적을 위해 단일 업스트림을 구성할 수 있습니다. 다음 다이어그램에서는 이 구성을 보여 줍니다.

![OIDC 구현에 대 한 액세스를 보여 주는 이미지](./media/partner-ping/authorization-flow.png)

여러 정책이 여러 **Idps** 를 노출 하는 일반적인 Azure AD B2C 배포에서는 챌린지를 발생 시킬 수 있습니다. IdP는 단일 업스트림 구성 될 수 있기 때문입니다.  

### <a name="pingfederate-as-a-federation-proxy"></a>페더레이션 프록시로의 페더레이션

회사는 인증 공급자로 완전히 구성 하거나 필요한 경우 다른 여러 업스트림 IdPs에 대 한 프록시로 완전히 구성할 수 있는 엔터프라이즈 id 브리지입니다. 다음 다이어그램에서는이 기능을 보여 줍니다.

![이미지는 다음 구현을 보여 줍니다.](./media/partner-ping/pingfederate.png)

이 기능은 인바운드 요청을 특정 Azure AD B2C 정책으로 컨텍스트/동적으로 또는 선언적으로 전환 하는 데 사용할 수 있습니다. 다음은이 구성에 대 한 프로토콜 시퀀스 흐름을 묘사 하는 것입니다.

![이미지에서 워크플로를 표시 합니다.](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>필수 구성 요소

시작 하려면 다음이 필요 합니다.

- Azure 구독 계정이 없는 경우 [무료 계정을](https://azure.microsoft.com/free/)받으세요.

- Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](./tutorial-create-tenant.md) 입니다.

- Docker 컨테이너 또는 Azure Vm에서 직접 배포 된 액세스 및 연결

## <a name="connectivity"></a>연결

다음이 연결 되어 있는지 확인 합니다.

- 서비스 **액세스 서버** – Azure AD B2C 서비스에서 게시 하는 서비스 및 서비스에서 게시 하는 서비스와 통신할 수 있습니다.

- **서버** 에 연결 하 고, Azure AD B2C 서비스에서 게시 한 서비스, 클라이언트 브라우저, oidc, OAuth 잘 알려진 및 키 검색과 통신할 수 있습니다.

- **레거시 또는 헤더 기반 인증 응용 프로그램** -통신 액세스 서버와 통신할 수 있습니다.

- **SAML 신뢰 당사자 응용 프로그램** – 클라이언트에서 브라우저 트래픽에 도달할 수 있습니다. Azure AD B2C 서비스에서 게시 하는 SAML 페더레이션 메타 데이터에 액세스할 수 있습니다.

- **최신 응용 프로그램** – 클라이언트에서 브라우저 트래픽에 도달할 수 있습니다. Azure AD B2C 서비스에 의해 게시 된 OIDC, OAuth 잘 알려진 및 키 검색에 액세스할 수 있습니다.

- **REST API** – 네이티브 또는 웹 클라이언트에서 트래픽에 도달할 수 있습니다. Azure AD B2C 서비스에 의해 게시 된 OIDC, OAuth 잘 알려진 및 키 검색에 액세스할 수 있습니다.

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C 구성

이 목적을 위해 기본 사용자 흐름 또는 IEF (advanced Identity enterprise framework) 정책을 사용할 수 있습니다. [WebFinger](https://tools.ietf.org/html/rfc7033) 기반 검색 규칙을 사용 하 여 **발급자** 값을 기반으로 메타 데이터 끝점을 생성 합니다.
이 규칙을 따르려면 사용자 흐름의 정책 속성을 사용 하 여 Azure AD B2C 발급자 업데이트를 업데이트 합니다.

![이미지는 토큰 설정을 보여 줍니다.](./media/partner-ping/token-setting.png)

고급 정책에서는 [JWT 토큰 발급자 기술 프로필](./jwt-issuer-technical-profile.md)의 **IssuanceClaimPattern** metadata 요소 to **AuthorityWithTfp** 값을 사용 하 여이를 구성할 수 있습니다.

## <a name="configure-pingaccesspingfederate"></a>이상 액세스/i s 페더레이션 구성

다음 섹션에서는 필수 구성에 대해 설명 합니다.
이 다이어그램은 통합에 대 한 전반적인 사용자 흐름을 보여 줍니다.

![이미지에 대 한 액세스 및 연결을 보여 주는 통합](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>토큰 공급자로 서를 구성 합니다.

을 (를) 액세스 하기 위한 토큰 공급자로 연결 하는 것을 구성 하려면 대상 페더레이션에서 연결에 대 한 연결을 설정 해야 합니다.  
구성 단계는 [이 문서](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) 를 참조 하세요.

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>헤더 기반 인증에 대 한 연결 액세스 응용 프로그램 구성

헤더 기반 인증을 위해 대상 웹 응용 프로그램에 대 한 액세스 응용 프로그램을 만들어야 합니다. 다음 단계를 수행합니다.

#### <a name="step-1--create-a-virtual-host"></a>1 단계-가상 호스트 만들기

>[!IMPORTANT]
>이 솔루션에 대해를 구성 하려면 모든 응용 프로그램에 대해 가상 호스트를 만들어야 합니다. 구성 고려 사항 및 해당 영향에 대 한 자세한 내용은 [주요 고려 사항](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html)을 참조 하세요.

가상 호스트를 만들려면 다음 단계를 따르세요.

1. **설정**  >  **액세스**  >  **가상 호스트** 로 이동 합니다.

2. **가상 호스트 추가** 선택

3. 호스트 필드에 응용 프로그램 URL의 FQDN 부분을 입력 합니다.

4. 포트 필드에 **443** 을 입력 합니다.

5. **저장** 을 선택합니다.

#### <a name="step-2--create-a-web-session"></a>2 단계 – 웹 세션 만들기

웹 세션을 만들려면 다음 단계를 따르세요.

1. **설정**  >  **액세스**  >  **웹 세션** 으로 이동 합니다.

2. **웹 세션 추가** 를 선택 합니다.

3. 웹 세션의 **이름을** 제공 합니다.

4. **쿠키 유형**( **서명 된 Jwt** 또는 **암호화 된 jwt** )을 선택 합니다.

5. **대상 그룹** 에 대 한 고유한 값을 제공 합니다.

6. **클라이언트 id** 필드에 **Azure AD 응용 프로그램 ID** 를 입력 합니다.

7. Azure AD에서 응용 프로그램에 대해 생성 한 **키** 를 **클라이언트 암호** 필드에 입력 합니다.

8. 선택 사항-Microsoft Graph API를 사용 하 여 사용자 지정 클레임을 만들고 사용할 수 있습니다. 그렇게 하도록 선택 하는 경우 **고급** 을 선택 하 고 **요청 프로필** 및 **사용자 특성 새로 고침** 옵션을 선택 취소 합니다. 사용자 지정 클레임을 사용 하는 방법에 대 한 자세한 내용은 [사용자 지정 클레임 사용](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers.md)을 참조 하세요.

9. **저장** 을 선택합니다.

#### <a name="step-3--create-identity-mapping"></a>3 단계-id 매핑 만들기

>[!NOTE]
>둘 이상의 응용 프로그램이 헤더에 동일한 데이터를 필요로 하는 경우 두 개 이상의 응용 프로그램에서 id 매핑을 사용할 수 있습니다.

Id 매핑을 만들려면 다음 단계를 수행 합니다.

1. **설정**  >  **액세스**  >  **id 매핑** 으로 이동

2. **Id 매핑 추가** 를 선택 합니다.

3. **이름** 지정

4. **헤더 Id 매핑의** Id 매핑 유형 선택

5. **특성 매핑** 테이블에서 필요한 매핑을 지정 합니다. 예를 들면 다음과 같습니다.

   특성 이름 | 헤더 이름 |
   |-------|--------|
   |upn | x-userprinciplename |
   |이메일   |    x-전자 메일  |
   |oid   | x-oid  |
   |scp   |     x 범위 |
   |amr    |    x amr    |

6. **저장** 을 선택합니다.

#### <a name="step-4--create-a-site"></a>4 단계-사이트 만들기

>[!NOTE]
>일부 구성에서는 사이트에 둘 이상의 응용 프로그램이 포함 될 수 있습니다. 사이트는 둘 이상의 응용 프로그램과 함께 사용할 수 있습니다 (해당 하는 경우).

사이트를 만들려면 다음 단계를 따르세요.

1. **기본**  >  **사이트로** 이동

2. **사이트 추가** 선택

3. 사이트의 **이름** 지정

4. 사이트 **대상을** 입력 합니다. 대상은 응용 프로그램을 호스트 하는 서버에 대 한 호스트 이름: 포트 쌍입니다. 이 필드에 응용 프로그램의 경로를 입력 하지 마세요. 예를 들어의 응용 프로그램에는 https://mysite:9999/AppName 사이트의 대상 값: 9999이 포함 됩니다.

5. 대상에 보안 연결이 필요한 지 여부를 나타냅니다.

6. 대상에 보안 연결이 필요 하면 신뢰할 수 있는 인증서 그룹을 **트러스트** 된 인증서 그룹으로 설정 합니다.

7. **저장** 을 선택합니다.

#### <a name="step-5--create-an-application"></a>5 단계 – 응용 프로그램 만들기

보호 하려는 Azure의 각 응용 프로그램에 대 한 액세스 권한으로 응용 프로그램을 만들려면 다음 단계를 수행 합니다.

1. **주**  >  **응용 프로그램** 으로 이동

2. **응용 프로그램 추가** 선택

3. 응용 프로그램의 **이름** 지정

4. 필요에 따라 응용 프로그램에 대 한 **설명을** 입력 합니다.

5. 응용 프로그램에 대 한 **컨텍스트 루트** 를 지정 합니다. 예를 들어의 응용 프로그램은 https://mysite:9999/AppName /AppName.의 컨텍스트 루트를 가집니다. 컨텍스트 루트는 슬래시 (/)로 시작 해야 하 고, 슬래시 (/)로 끝나지 않아야 하며, 둘 이상의 계층/Apps/MyApp. 수 있습니다 (예:).

6. 만든 **가상 호스트** 를 선택 합니다.

   >[!NOTE]
   >가상 호스트와 컨텍스트 루트의 조합은 연결 된 액세스에서 고유 해야 합니다.

7. 만든 **웹 세션** 선택

8. 응용 프로그램을 포함 하는 만든 **사이트** 를 선택 합니다.

9. 만든 **Id 매핑** 선택

10. 저장할 때 사이트를 사용 하도록 **설정 하려면 사용** 을 선택 합니다.

11. **저장** 을 선택합니다.

### <a name="configure-the-pingfederate-authentication-policy"></a>인증 정책 구성

Azure AD B2C 테 넌 트에서 제공 하는 여러 IdPs에 페더레이션 하도록 합니다. 인증 정책 구성

1. IdPs와 SP 간에 특성을 연결 하는 계약을 만듭니다. 자세한 내용은 [페더레이션 허브 및 인증 정책 계약](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html)을 참조 하세요. SP가 각 IdP의 다른 특성 집합을 요구 하지 않는 한 계약이 하나만 필요할 가능성이 높습니다.

2. 각 IdP에 대해 SP로 페더레이션 허브 IdP와 IdP 간의 연결을 만듭니다.

3. **대상 세션 매핑** 창에서 적용 가능한 인증 정책 계약을 IdP 연결에 추가 합니다.

4. **선택기** 창에서 인증 선택기를 구성 합니다. 예를 들어 각 IdP을 인증 정책의 해당 IdP 연결에 매핑하려면 **Identifier First 어댑터** 의 인스턴스를 참조 하세요.

5. IdP 및 SP와 페더레이션 허브 간의 SP 연결을 만듭니다.

6. **인증 원본 매핑** 창의 SP 연결에 해당 하는 인증 정책 계약을 추가 합니다.

7. 각 IdP를 사용 하 여 페더레이션 허브 (SP)에 연결 합니다.

8. SP와 협력 하 여 페더레이션 허브 인 IdP으로 연결 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토 하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C에서 사용자 지정 정책 시작](./custom-policy-get-started.md?tabs=applications)
