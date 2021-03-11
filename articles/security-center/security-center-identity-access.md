---
title: MFA에 대 한 Azure Security Center의 보안 권장 사항
description: Azure Security Center를 사용 하 여 Azure 구독에 multi-factor authentication을 적용 하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631900"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>구독에 대 한 MFA (multi-factor authentication) 적용 관리

사용자를 인증 하는 데 암호만 사용 하는 경우 공격 벡터가 열려 있습니다. 사용자는 종종 취약 한 암호를 사용 하거나 여러 서비스에 대해 다시 사용 합니다. [MFA](https://www.microsoft.com/security/business/identity/mfa)를 사용하도록 설정하면 계정이 더 안전하게 유지되고 사용자는 SSO(Single Sign-On)를 사용하여 거의 모든 애플리케이션에서 인증을 받을 수 있습니다.

조직에서 소유 하는 라이선스에 따라 AD (Azure Active Directory) 사용자에 대해 MFA를 사용 하도록 설정 하는 방법에는 여러 가지가 있습니다. 이 페이지에서는 Azure Security Center의 컨텍스트에서 각에 대 한 세부 정보를 제공 합니다.


## <a name="mfa-and-security-center"></a>MFA 및 Security Center 

Security Center 높은 값을 MFA에 배치 합니다. 보안 점수를 가장 많이 제공 하는 보안 제어는 **MFA를 사용 하도록 설정** 하는 것입니다. 

MFA 사용 제어의 권장 사항은 구독 사용자에 대해 권장 되는 방법을 충족 하 고 있는지 확인 합니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

MFA를 사용 하도록 설정 하 고 보안 기본값, 사용자별 할당, CA (조건부 액세스) 정책 Security Center의 두 가지 권장 사항을 준수 하는 세 가지 방법이 있습니다. 이러한 각 옵션에 대해서는 아래에서 설명 합니다.

### <a name="free-option---security-defaults"></a>무료 옵션-보안 기본값
무료 버전의 Azure AD를 사용 하는 경우에는 [보안 기본값](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) 을 사용 하 여 테 넌 트에서 multi-factor authentication을 사용 하도록 설정 합니다.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>Microsoft 365 Business, E3 또는 E5 고객에 대 한 MFA
Microsoft 365 있는 고객은 **사용자별 할당** 을 사용할 수 있습니다. 이 시나리오에서 Azure AD MFA는 모든 사용자에 대해 모든 로그인 이벤트에 대해 사용 하거나 사용 하지 않도록 설정 됩니다. 사용자의 하위 집합 또는 특정 시나리오에서 multi-factor authentication을 사용 하도록 설정할 수 있는 기능은 없으며, Office 365 포털을 통해 관리할 수 있습니다.

### <a name="mfa-for-azure-ad-premium-customers"></a>Azure AD Premium 고객에 대 한 MFA
사용자 환경을 개선 하려면 **조건부 액세스 (CA) 정책** 옵션의 Azure AD Premium P1 또는 P2로 업그레이드 합니다. CA 정책을 구성 하려면 [AD (Azure Active Directory) 테 넌 트 권한이](../active-directory/roles/permissions-reference.md)필요 합니다.

CA 정책은 다음을 수행 해야 합니다.
- MFA 적용
- Microsoft Azure Management app ID (797f4846-ba00-4fd7-ba43-dac1f8f63013) 또는 모든 앱 포함
- Microsoft Azure 관리 앱 ID 제외 안 함

**Azure AD Premium P1** 고객은 AZURE AD CA를 사용 하 여 비즈니스 요구 사항에 맞게 특정 시나리오 또는 이벤트 중에 사용자에 게 multi-factor authentication을 요청할 수 있습니다. 이 기능을 포함 하는 다른 라이선스: Enterprise Mobility + Security E3, Microsoft 365 F1 및 Microsoft 365 E3.

**Azure AD Premium P2** 는 가장 강력한 보안 기능과 향상 된 사용자 환경을 제공 합니다. 이 라이선스는 Azure AD Premium P1 기능에 대 한 [위험 기반 조건부 액세스](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) 를 추가 합니다. 위험 기반 CA는 사용자의 패턴에 적응 하 고 multi-factor authentication 프롬프트를 최소화 합니다. 이 기능을 포함 하는 다른 라이선스: E5 또는 Microsoft 365 E5를 Enterprise Mobility + Security 합니다.

[Azure 조건부 액세스 설명서](../active-directory/conditional-access/overview.md)에서 자세히 알아보세요.

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>MFA (multi-factor authentication)를 사용 하도록 설정 하지 않은 계정 식별

Security Center 권장 사항 세부 정보 페이지 또는 Azure 리소스 그래프를 사용 하 여 MFA를 사용 하지 않고 사용자 계정 목록을 볼 수 있습니다.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Azure Portal에서 MFA를 사용 하도록 설정 하지 않은 계정을 확인 합니다.
권장 사항 세부 정보 페이지의 **비정상 리소스** 목록에서 구독을 선택 하거나 **작업 수행** 을 선택 하면 목록이 표시 됩니다.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Azure 리소스 그래프를 사용 하 여 MFA를 사용 하도록 설정 하지 않은 계정 보기
MFA를 사용 하도록 설정 되지 않은 계정을 확인 하려면 다음 Azure 리소스 그래프 쿼리를 사용 합니다. 이 쿼리는 "구독에 대 한 소유자 권한이 있는 계정에 대해 MFA를 사용 하도록 설정 해야 합니다." 라는 권장 사항의 모든 비정상 리소스-계정을 반환 합니다. 

1. **Azure 리소스 그래프 탐색기** 를 엽니다.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure 리소스 그래프 탐색기 시작 * * 권장 사항 페이지" :::

1. 다음 쿼리를 입력 하 고 **쿼리 실행** 을 선택 합니다.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData`속성은 MFA가 적용 되지 않은 계정에 대 한 계정 개체 id의 목록을 표시 합니다. 

    > [!NOTE]
    > 계정 소유자의 개인 정보를 보호 하기 위해 계정 이름이 아니라 개체 Id로 표시 됩니다.

> [!TIP]
> 또는 Security Center의 REST API 메서드 [평가 (Get)](/rest/api/securitycenter/assessments/get)를 사용할 수 있습니다.


## <a name="faq---mfa-in-security-center"></a>FAQ-Security Center의 MFA

- [이미 CA 정책을 사용 하 여 MFA를 적용 하 고 있습니다. 그래도 Security Center 권장 사항이 있는 이유는 무엇 인가요?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [타사 MFA 도구를 사용 하 여 MFA를 적용 하 고 있습니다. 그래도 Security Center 권장 사항이 있는 이유는 무엇 인가요?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [에서 구독에 대 한 사용 권한이 없는 사용자 계정을 "MFA를 요구" 하는 이유 Security Center는 무엇 인가요?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [PIM을 사용 하 여 MFA를 적용 합니다. PIM 계정이 비준수로 표시 되는 이유는 무엇 인가요?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [일부 계정을 제외 하거나 해제할 수 있나요?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Security Center의 id 및 액세스 보호에 대 한 제한이 있나요?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>이미 CA 정책을 사용 하 여 MFA를 적용 하 고 있습니다. 그래도 Security Center 권장 사항이 있는 이유는 무엇 인가요?
권장 사항이 여전히 생성 되는 이유를 조사 하려면 MFA CA 정책에서 다음 구성 옵션을 확인 합니다.

- MFA CA 정책의 **사용자** 섹션 (또는 **그룹** 섹션의 그룹 중 하나)에 계정을 포함 했습니다.
- Azure 관리 앱 ID (797f4846-ba00-4fd7-ba43-dac1f8f63013) 또는 모든 앱은 MFA CA 정책의 **앱** 섹션에 포함 됩니다.
- Azure 관리 앱 ID는 MFA CA 정책의 **앱** 섹션에서 제외 되지 않습니다.

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>타사 MFA 도구를 사용 하 여 MFA를 적용 하 고 있습니다. 그래도 Security Center 권장 사항이 있는 이유는 무엇 인가요?
Security Center의 MFA 권장 사항은 타사 MFA 도구 (예: DUO)를 지원 하지 않습니다.

권장 사항이 조직과 관련이 없는 경우에는 [제외 resources (보안 점수의 권장 사항](exempt-resource.md))에 설명 된 대로 "완화 된" 것으로 표시 하는 것이 좋습니다. [권장 사항을 사용 하지 않도록 설정할](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)수도 있습니다.

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>에서 구독에 대 한 사용 권한이 없는 사용자 계정을 "MFA를 요구" 하는 이유 Security Center는 무엇 인가요?
Security Center의 MFA 권장 사항은 [AZURE RBAC](../role-based-access-control/role-definitions-list.md) 역할 및 [azure 클래식 구독 관리자](../role-based-access-control/classic-administrators.md) 역할을 참조 합니다. 이러한 역할이 있는 계정이 없는지 확인합니다.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>PIM을 사용 하 여 MFA를 적용 합니다. PIM 계정이 비준수로 표시 되는 이유는 무엇 인가요?
Security Center의 MFA 권장 사항은 현재 PIM 계정을 지원 하지 않습니다. 이러한 계정을 사용자 및 그룹 섹션의 CA 정책에 추가할 수 있습니다.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>일부 계정을 제외 하거나 해제할 수 있나요?
MFA를 사용 하지 않는 일부 계정을 제외 하는 기능은 현재 지원 되지 않습니다.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Security Center의 id 및 액세스 보호에 대 한 제한이 있나요?
Security Center의 id 및 액세스 보호에는 몇 가지 제한 사항이 있습니다.

- 600 개 이상의 계정을 사용 하는 구독에는 id 권장 사항을 사용할 수 없습니다. 이 경우 이러한 권장 사항은 "사용할 수 없는 평가" 아래에 나열 됩니다.
- CSP (클라우드 솔루션 공급자) 파트너의 관리 에이전트에는 id 권장 사항을 사용할 수 없습니다.
- Id 권장 사항은 PIM (권한 있는 id 관리) 시스템을 사용 하 여 관리 되는 계정을 식별 하지 않습니다. PIM 도구를 사용 하는 경우 **액세스 및 권한 관리** 제어에서 부정확 한 결과가 표시 될 수 있습니다.


## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용 되는 권장 사항에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)