---
title: Azure Active Directory에 대한 외부 액세스를 위한 보안 계획 만들기
description: 조직의 리소스에 대한 외부 액세스를 위한 보안 계획
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266142240ba9e892c905ac8aa6521da5a14c4c3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554023"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. 외부 액세스를 위한 보안 계획 만들기 

현재 [외부 액세스를 위한 원하는 보안 상태를 결정](1-secure-access-posture.md)하고 [현재 협업 상태를 발견](2-secure-access-current-state.md)했으므로 외부 사용자 보안 및 거버넌스 계획을 만들 수 있습니다. 

이 계획은 다음을 문서화해야 합니다.

* 액세스를 위해 그룹화해야 하는 애플리케이션 및 기타 리소스.

* 외부 사용자에 대한 적절한 로그인 조건. 여기에는 디바이스 상태, 로그인 위치, 클라이언트 애플리케이션 요구 사항 및 사용자 위험이 포함될 수 있습니다.

* 액세스를 검토 및 제거하는 시기에 대한 비즈니스 정책. 

* 유사하게 그룹화 및 처리되는 사용자 채우기.

이러한 영역을 문서화한 후에는 Microsoft 또는 다른 IdP(ID 공급자)의 ID 및 액세스 관리 정책을 사용하여 이 계획을 구현할 수 있습니다.

## <a name="document-resources-to-be-grouped-for-access"></a>액세스를 위해 그룹화할 문서 리소스

액세스를 위해 리소스를 그룹화하는 방법에는 여러 가지가 있습니다. 

* Microsoft Teams는 파일, 대화 스레드 및 기타 리소스를 한 곳에서 그룹화합니다. Microsoft Teams에 대한 외부 액세스 전략을 세워야 합니다. [Teams, OneDrive 및 SharePoint에 대한 액세스 보호](9-secure-access-teams-sharepoint.md)를 참조하세요.

* 권한 관리 액세스 패키지를 사용하여 액세스 권한을 부여할 수 있는 애플리케이션 및 기타 리소스의 단일 패키지를 만들 수 있습니다. 

* 조건부 액세스 정책은 동일한 액세스 요구 사항으로 최대 250개의 애플리케이션에 적용할 수 있습니다.

그러나 액세스를 관리하고, 함께 그룹화해야 하는 애플리케이션을 문서화해야 합니다. 고려해야 할 사항은 다음과 같습니다.

* **위험 프로필**. 잘못된 행위자가 애플리케이션에 대한 액세스 권한을 얻은 경우 비즈니스에 미치는 위험은 무엇인가요? 각 애플리케이션을 높음, 중간 또는 낮음 위험으로 코딩하는 것을 고려해야 합니다. 위험 수준이 높은 애플리케이션을 위험 수준이 낮은 애플리케이션과 그룹화하는 경우에는 주의해야 합니다. 

   * 외부 사용자와 공유해서는 안 되는 애플리케이션을 문서화합니다.

* **규정 준수 프레임워크**. 애플리케이션이 충족해야 하는 규정 준수 프레임워크가 있는 경우에는 어떻게 되나요? 액세스 및 검토 요구 사항은 무엇인가요?

* **특정 작업 역할 또는 부서에 대한 애플리케이션** 입니다. 특정 작업 역할 또는 부서의 모든 사용자에게 액세스 권한이 필요하기 때문에 그룹화해야 하는 애플리케이션이 있나요?

* **협업 중심 애플리케이션**. 외부 사용자가 액세스할 수 있는 협업 중심 애플리케이션은 무엇인가요? Microsoft Teams와 SharePoint에 액세스할 수 있어야 합니다. Word 및 Excel과 같은 Office 365 내 생산성 애플리케이션의 경우 외부 사용자가 자신의 라이선스를 제공하거나, 아니면 사용자가 라이선스를 부여하고 액세스를 제공해야 하나요?

외부 사용자가 액세스할 수 있도록 하려는 애플리케이션 및 리소스의 각 그룹화에 대해 다음을 문서화합니다.

* 그룹에 대한 설명이 포함된 이름입니다(예: *High_Risk_External_Access_Finance*). 

* 그룹에 있는 모든 애플리케이션 및 리소스의 전체 목록입니다.

* 애플리케이션 및 리소스 소유자와 연락처 정보입니다.

* 액세스가 IT 부서에서 제어하는지, 아니면 비즈니스 소유자에게 위임되었는지 여부입니다.

* 액세스를 위한 백그라운드 검사 또는 학습의 완료와 같은 모든 필수 구성 요소.

* 리소스에 액세스하기 위한 규정 준수 요구 사항.

* 특정 리소스에 대해 다단계 인증을 요구하는 등의 추가 과제가 있습니다.

* 액세스가 검토되는 빈도, 검토 주체 및 문서화되는 위치입니다.

이러한 유형의 거버넌스 계획은 내부 액세스에 대해서도 완료해야 합니다.

## <a name="document-sign-in-conditions-for-external-users"></a>외부 사용자를 위한 로그인 조건을 문서화합니다.

계획의 일환으로, 리소스에 액세스할 때 외부 사용자에 대한 로그인 요구 사항을 확인해야 합니다. 로그인 요구 사항은 종종 리소스의 위험 프로필 및 사용자 로그인에 대한 위험 평가를 기반으로 합니다.

로그인 조건은 [Azure AD 조건부 액세스](../conditional-access/overview.md)에서 구성되며 조건 및 결과로 구성됩니다. 예: 다단계 인증을 요구하는 경우

**리소스 위험 기반 로그인 조건입니다.**

| 애플리케이션 위험 프로필| 다단계 인증을 트리거하기 위해 고려할 정책 |
| - |-|
| 위험 수준 낮음| 특정 애플리케이션 세트에 대한 MFA 필요 |
| 위험 수준 중간| 다른 위험이 있을 때 MFA 필요 |
| 위험 수준 높음| 외부 사용자에 대해 MFA 항상 필요 |


현재는 [테넌트의 B2B 사용자에 대해 다단계 인증을 적용할](../external-identities/b2b-tutorial-require-mfa.md) 수 있습니다. 

**사용자 및 디바이스 기반 로그인 조건**

| 사용자 또는 로그인 위험| 고려할 정책 |
| - | - |
| 디바이스| 준수 디바이스 필요 |
| 모바일 앱| 승인된 앱 필요 |
| ID 보호가 높은 위험을 표시| 사용자가 암호를 변경해야 함 |
| 네트워크 위치| 특정 IP 주소 범위에서 극비 프로젝트에 로그인해야 함 |

현재 디바이스 상태를 정책에 대한 입력으로 사용하려면 디바이스를 등록하거나 테넌트에 가입해야 합니다. 

[ID 보호 위험 기반 정책](../conditional-access/howto-conditional-access-policy-risk.md)을 사용할 수 있습니다. 그러나 사용자의 홈 테넌트에서 문제를 완화해야 합니다.

[네트워크 위치](../conditional-access/howto-conditional-access-policy-location.md)의 경우 소유하는 모든 IP 주소 범위에 대한 액세스를 제한할 수 있습니다. 외부 파트너가 조직에서 현장에 있는 동안 애플리케이션에 액세스하려는 경우에만 이를 사용할 수 있습니다.

[조건부 액세스 정책에 대해 자세히 알아보세요](../conditional-access/overview.md).

## <a name="document-access-review-policies"></a>액세스 검토 정책 문서화

리소스에 대한 액세스를 검토해야 하는 경우 및 외부 사용자에 대한 계정 액세스를 제거해야 하는 경우에 대한 비즈니스 정책을 문서화합니다. 이러한 결정에 대한 입력은 다음을 포함합니다.

* 요구 사항은 규정 준수 프레임워크에 자세히 설명되어 있습니다.

* 내부 비즈니스 정책 및 프로세스

* 사용자 동작

사용자의 요구에 맞게 사용자 지정하는 경우 다음 사항을 고려하세요.

* **권한 관리 액세스 검토**. 권한 관리의 기능을 사용하여 다음을 수행합니다.

   * [액세스 패키지가 자동으로 만료](../governance/entitlement-management-access-package-lifecycle-policy.md)되므로 외부 사용자가 포함된 리소스에 액세스합니다.

   * 액세스 검토에 [필요한 검토 빈도](../governance/entitlement-management-access-reviews-create.md)를 설정합니다.

   * [연결된 조직](../governance/entitlement-management-organization.md)을 사용하여 단일 파트너의 모든 사용자를 그룹화하는 경우 비즈니스 소유자 및 파트너 담당자와 정기적으로 검토 일정을 세웁니다.

* **Microsoft 365 그룹**. 외부 사용자가 초대되는 Microsoft 365 그룹에 대한 [그룹 만료 정책](/microsoft-365/solutions/microsoft-365-groups-expiration-policy)을 설정합니다. 

* **기타 옵션**. 외부 사용자가 권한 관리 액세스 패키지 또는 Microsoft 365 그룹 외부에서 액세스할 수 있는 경우, 계정을 비활성화하거나 삭제해야 하는 경우를 검토하도록 비즈니스 프로세스를 설정합니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

   * 90일 동안 로그인하지 않은 모든 계정에 대한 로그인 기능을 제거합니다.

   * 모든 프로젝트의 마지막에 외부 사용자에 대한 액세스 요구를 평가하고 조치를 취합니다.

 

## <a name="determine-your-access-control-methods"></a>액세스 제어 방법 결정

이제 액세스를 제어할 대상, 공통 액세스를 위해 자산을 그룹화하는 방법, 필수 로그인 및 액세스 검토 정책에 대해 알아보았으므로 계획을 수행하는 방법을 결정할 수 있습니다. 

[권한 관리](../governance/entitlement-management-overview.md)와 같은 일부 기능은 Azure AD P2(Premium 2) 라이선스에서만 사용할 수 있습니다. Microsoft 365 E5 및 Office 365 E5 라이선스는 Azure AD P2 라이선스를 포함합니다. 

Microsoft 365, Office 365 및 Azure AD의 다른 조합은 외부 사용자를 관리하기 위한 몇 가지 기능을 사용할 수 있습니다. 자세한 내용은 [Information Protection](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance)을 참조하세요.

> [!NOTE]
> 라이선스는 사용자별로 제공됩니다. 따라서 모든 사용자에 대해 해당 라이선스를 사용하도록 설정하지 않고 Azure AD P2 또는 Microsoft 365 E5 수준에서 액세스 제어를 위임하는 관리자 및 비즈니스 소유자를 비롯한 특정 사용자를 가질 수 있습니다. 처음 5만 명의 외부 사용자는 무료입니다. 다른 내부 사용자에 대해 P2 라이선스를 사용하지 않도록 설정하는 경우 액세스 패키지와 같은 권한 관리 기능을 사용할 수 없습니다. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Azure AD P2 및 Microsoft/Office 365 E5를 사용하여 액세스 거버넌스
Azure AD P2 및 Microsoft 365 E5에는 전체 보안 및 거버넌스 도구 모음이 있습니다.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>프로비저닝, 로그인, 액세스 검토 및 프로비저닝 해제. 굵게 표시된 항목은 선호되는 방법입니다.

| 기능| 외부 사용자 프로비전| 로그인 요구 사항 적용| 액세스 검토| 액세스 프로비전 해제 |
| - | - | - | - | - |
| Azure AD B2B 협업| 이메일, OTP, 셀프 서비스를 통한 초대| | **파트너별 정기적 검토**| 계정 제거<br>로그인 제한 |
| 권한 관리| **할당 또는 셀프 서비스 액세스를 통해 사용자 추가**| | 액세스 검토|**액세스 패키지의 만료 또는 제거**|
| Office 365 그룹| | | 그룹 멤버 자격 검토| 그룹의 만료 또는 삭제<br> 그룹에서 제거 |
| Azure AD 보안 그룹| | **조건부 액세스 정책**(필요에 따라 보안 그룹에 외부 사용자 추가)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>리소스에 액세스. 굵게 표시된 항목은 선호되는 방법입니다.

|기능 | 앱 및 리소스 액세스| SharePoint 및 OneDrive 액세스| Teams 액세스| 이메일 및 문서 보안 |
| - |-|-|-|-|
| 권한 관리| **할당 또는 셀프 서비스 액세스를 통해 사용자 추가**| **액세스 패키지**| **액세스 패키지**|  |
| Office 365 그룹| | 그룹에 포함된 사이트(및 관련 콘텐츠)에 대한 액세스| 그룹에 포함된 팀(및 관련 콘텐츠)에 대한 액세스|  |
| 민감도 레이블| | **수동 및 자동으로 액세스를 분류하고 제한**| **수동 및 자동으로 액세스를 분류하고 제한**| **수동 및 자동으로 액세스를 분류하고 제한** |
| Azure AD 보안 그룹| **액세스 패키지에 포함되지 않은 액세스에 대한 조건부 액세스 정책**| | |  |


### <a name="entitlement-management"></a>권한 관리 

[권한 관리 액세스 패키지](../governance/entitlement-management-access-package-create.md)를 통해 그룹과 팀, 애플리케이션 및 SharePoint 사이트에 대한 액세스를 프로비저닝하고 프로비저닝을 해제합니다. 액세스가 허용된 연결된 조직, 셀프 서비스 요청이 허용되는지 여부 및 액세스 권한을 부여하는 데 필요한 승인 워크플로(있는 경우)를 정의할 수 있습니다. 액세스가 필요한 것보다 오래 지속되지 않도록 하려면 각 액세스 패키지에 대한 만료 정책 및 액세스 검토를 정의할 수 있습니다. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Azure AD P1 및 Microsoft/Office 365 E3을 사용하여 액세스 거버넌스
Azure AD P1 및 Microsoft 365 E3을 사용하여 강력한 거버넌스를 달성할 수 있습니다.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>프로비저닝, 로그인, 액세스 검토 및 프로비저닝 해제


|기능 | 외부 사용자 프로비전| 로그인 요구 사항 적용| 액세스 검토| 액세스 프로비전 해제 |
| - |-|-|-|-|
| Azure AD B2B 협업| **이메일, OTP, 셀프 서비스를 통한 초대**| 직접 B2B 페더레이션| **파트너별 정기적 검토**| 계정 제거<br>로그인 제한 |
| Microsoft 또는 Office 365 그룹| | | | 그룹의 만료 또는 삭제<br>그룹에서 제거 |
| 보안 그룹| | **외부 사용자를 보안 그룹(조직, 팀, 프로젝트 등)에 추가**| |  |
| 조건부 액세스 정책| | **외부 사용자에 대한 로그인 조건부 액세스 정책**| |  |


 ### <a name="access-to-resources"></a>리소스에 액세스.

|기능 | 앱 및 리소스 액세스| SharePoint 및 OneDrive 액세스| Teams 액세스| 이메일 및 문서 보안 |
| - |-|-|-|-|
| Microsoft 또는 Office 365 그룹| | **그룹에 포함된 사이트(및 관련 콘텐츠)에 대한 액세스**|**Microsoft 365 그룹에 포함된 팀(및 관련 콘텐츠)에 대한 액세스**|  |
| 민감도 레이블| | 수동 분류 및 액세스 제한| 수동 분류 및 액세스 제한| 수동 분류 및 암호화 제한 |
| 조건부 액세스 정책| 액세스 제어에 대한 조건부 액세스 정책| | |  |
| 추가 방법| | 보안 그룹으로 SharePoint 사이트 액세스를 세부적으로 제한합니다.<br>직접 공유를 허용하지 않습니다.| **팀 내에서 외부 초대 제한**|  |


### <a name="next-steps"></a>다음 단계

리소스에 대한 외부 액세스 보안에 대해서는 다음 문서를 참조하세요. 이러한 작업은 나열된 순서대로 수행하는 것이 좋습니다.

1. [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거버넌스 계획 만들기](3-secure-access-plan.md)(현재 위치)

4. [보안을 위한 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 통한 보안 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 통한 보안 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 통한 보안 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams, OneDrive 및 SharePoint에 대한 보안 액세스](9-secure-access-teams-sharepoint.md)