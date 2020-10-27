---
title: Azure AD B2C에서 ID 보호 및 조건부 액세스 설정
description: Azure AD B2C 테넌트에서 위험한 로그인 및 기타 위험 이벤트를 확인하고 위험 검색을 기반으로 하여 정책을 만들 수 있도록 ID 보호 및 조건부 액세스를 구성하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb585e2ccf8c8ed071b5156961adf48d4e4b108d
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309779"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>Azure AD B2C에서 ID 보호 및 조건부 액세스 설정

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

ID 보호는 Azure AD B2C 테넌트에 대한 지속적인 위험 검색을 제공합니다. Azure AD B2C 테넌트 가격 책정 계층이 Premium P2인 경우 Azure Portal에서 자세한 ID 보호 위험 이벤트를 확인할 수 있습니다. 또한 이러한 위험 검색을 기반으로 하는 [조건부 액세스](../active-directory/conditional-access/overview.md) 정책을 사용하여 작업을 결정하고 조직 정책을 적용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure AD B2C 테넌트는 [Azure AD 구독에 연결](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription)되어야 합니다.
- 로그인 및 사용자 위험 기반 조건부 액세스를 사용하려면 Azure AD B2C Premium P2가 필요합니다. 필요한 경우 [Azure AD B2C 가격 책정 계층을 Premium P2로 변경](https://aka.ms/exid-pricing-tier)하세요. 
- B2C 테넌트에서 ID 보호 및 조건부 액세스를 관리하려면 전역 관리자 역할 또는 보안 관리자 역할이 할당된 계정이 필요합니다.
- 테넌트에서 이러한 기능을 사용하려면 먼저 Azure AD B2C Premium P2 가격 책정 계층으로 전환해야 합니다.

## <a name="set-up-identity-protection"></a>ID 보호 설정

ID 보호는 기본적으로 설정되어 있습니다. Azure AD B2C 테넌트에서 ID 보호 위험 이벤트를 확인하려면 Azure AD B2C 테넌트를 Azure AD 구독에 연결하고 Azure AD B2C Premium P2 가격 책정 계층을 선택하면 됩니다. Azure Portal에서 자세한 위험 이벤트 보고서를 확인할 수 있습니다.

### <a name="supported-identity-protection-risk-detections"></a>지원되는 ID 보호 위험 검색

현재 Azure AD B2C에 지원되는 위험 검색은 다음과 같습니다.  

|위험 탐지 유형  |Description  |
|---------|---------|
| 비정상적 이동     | 사용자의 최근 로그인을 기준으로 비정상적인 위치에서 로그인합니다.        |
|익명 IP 주소     | 익명 IP 주소에서 로그인(예: Tor 브라우저, 익명성 도구 VPN)        |
|맬웨어 연결 IP 주소     | 맬웨어 연결 IP 주소에서 로그인합니다.         |
|일반적이지 않은 로그인 속성     | 지정된 사용자에게 최근 확인되지 않은 속성으로 로그인합니다.        |
|관리자가 확인한 사용자 손상    | 관리자가 사용자가 손상된 것으로 표시했습니다.             |
|암호 스프레이     | 암호 스프레이 공격을 통해 로그인합니다.      |
|Azure AD 위협 인텔리전스     | Microsoft의 내부 및 외부 위협 인텔리전스 소스가 알려진 공격 패턴을 식별했습니다.        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트에 대한 위험 이벤트 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.

1. **보안** 아래에서 **위험한 사용자(미리 보기)** 를 선택합니다.

   ![위험한 사용자](media/conditional-access-identity-protection-setup/risky-users.png)

1. **보안** 아래에서 **위험 검색(미리 보기)** 을 선택합니다.

   ![위험 탐지](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>조건부 액세스 정책 추가 

ID 보호 위험 검색을 기반으로 하는 조건부 액세스 정책을 추가하려면 보안 기본값을 Azure AD B2C 테넌트에 사용하지 않도록 설정한 다음, 조건부 액세스 정책을 만들어야 합니다.

### <a name="to-disable-security-defaults"></a>보안 기본값을 사용하지 않도록 설정하려면

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

3. Azure Portal에서 **Azure Active Directory** 를 검색하고 선택합니다.

4. **속성** 을 선택한 다음, **보안 기본값 관리** 를 선택합니다.

   ![보안 기본값 사용 안 함](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. [보안 기본값 사용] 아래에서 [아니요]를 선택합니다. 

   ![보안 기본값 사용 토글을 아니요로 설정](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>조건부 액세스 정책을 만들려면

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.

1. **보안** 아래에서 **조건부 액세스(미리 보기)** 를 선택합니다. **조건부 액세스 정책** 페이지가 열립니다. 

1. **새 정책** 을 선택하고, Azure AD 조건부 액세스 설명서에 따라 새 정책을 만듭니다. 다음은 이에 대한 예입니다.

   - [로그인 위험 기반 조건부 액세스: 조건부 액세스 정책을 통해 사용](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy)

   > [!IMPORTANT]
   > 정책을 적용하려는 사용자를 선택하는 경우 **모든 사용자** 만 선택하지 마세요. 그렇지 않으면 로그인하지 못하도록 차단될 수 있습니다.

## <a name="test-the-conditional-access-policy"></a>조건부 액세스 정책 테스트

1. 위에서 설명한 대로 다음 설정을 사용하여 조건부 액세스 정책을 만듭니다.
   
   - **사용자 및 그룹** 에 대해 테스트 사용자를 선택합니다. **모든 사용자** 를 선택하지 마세요. 그렇지 않으면 로그인하지 못하도록 차단될 수 있습니다.
   - **클라우드 앱 또는 작업** 에 대해 **앱 선택** 을 선택한 다음, 신뢰 당사자 애플리케이션을 선택합니다.
   - [조건]에 대해 **로그인 위험** 과 **높음** , **중간** 및 **낮음** 위험 수준을 선택합니다.
   - **권한 부여** 에 대해 **액세스 차단** 을 선택합니다.

      ![액세스 차단 선택](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. **만들기** 를 선택하여 테스트 조건부 액세스 정책을 사용하도록 설정합니다.

1. [Tor Browser](https://www.torproject.org/download/)를 사용하여 위험한 로그인을 시뮬레이션합니다. 

1. 시도한 로그인에 대한 jwt.ms로 디코딩된 토큰에서 로그인이 차단되었음을 확인할 수 있습니다.

   ![차단된 로그인 테스트](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>감사 보고서의 조건부 액세스 결과 검토

조건부 액세스 이벤트의 결과를 검토하려면

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

3. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.

4. **활동** 아래에서 **감사 로그** 를 선택합니다.

5. **범주** 를 **B2C** 로 설정하고 **활동 리소스 종류** 를 **IdentityProtection** 으로 설정하여 감사 로그를 필터링합니다. 그런 다음, **적용** 을 선택합니다.

6. 최대 7일 동안의 최근 감사 활동을 검토합니다. 포함되는 활동 유형은 다음과 같습니다.

   - **조건부 액세스 정책 평가** : 이 감사 로그 항목은 인증 중에 조건부 액세스 평가가 수행되었음을 나타냅니다.
   - **사용자 수정** : 이 항목은 최종 사용자가 조건부 액세스 정책의 권한 부여 또는 요구 사항을 충족했으며 사용자의 위험을 줄이기(완화) 위해 이 활동이 위험 엔진에 보고되었음을 나타냅니다.

7. 목록에서 **조건부 액세스 정책 평가** 로그 항목을 선택하여 **활동 세부 정보: 감사 로그** 페이지를 엽니다. **추가 세부 정보** 섹션에서 감사 로그 식별자를 다음 정보와 함께 보여 줍니다.

   - ConditionalAccessResult: 조건부 정책 평가에 필요한 권한 부여입니다.
   - AppliedPolicies: 조건이 충족되고 정책이 설정된 모든 조건부 액세스 정책의 목록입니다.
   - ReportingPolicies: 보고서 전용 모드로 설정되고 조건이 충족된 조건부 액세스 정책의 목록입니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스를 사용자 흐름에 추가](conditional-access-user-flow.md)합니다.
