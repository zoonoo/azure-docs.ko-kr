---
title: Azure Active Directory에 대 한 외부 액세스를 위한 보안 계획 만들기
description: 조직의 리소스에 대 한 외부 액세스를 위한 보안을 계획 합니다.
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
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554023"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. 외부 액세스를 위한 보안 계획 만들기 

[외부 액세스에 대해 원하는 보안 상태 보안](1-secure-access-posture.md) 상태를 결정 하 고 [현재 공동 작업 상태를 발견](2-secure-access-current-state.md)했으므로 외부 사용자 보안 및 거 버 넌 스 계획을 만들 수 있습니다. 

이 계획은 다음을 문서화 해야 합니다.

* 액세스를 위해 그룹화 해야 하는 응용 프로그램 및 기타 리소스입니다.

* 외부 사용자에 대 한 적절 한 로그인 조건입니다. 여기에는 장치 상태, 로그인 위치, 클라이언트 응용 프로그램 요구 사항 및 사용자 위험이 포함 될 수 있습니다.

* 액세스를 검토 및 제거 하는 시기에 대 한 비즈니스 정책 

* 유사 하 게 그룹화 및 처리 되는 사용자 채우기입니다.

이러한 영역을 문서화 한 후에는 Microsoft 또는 다른 id 공급자 (IdP)의 id 및 액세스 관리 정책을 사용 하 여이 계획을 구현할 수 있습니다.

## <a name="document-resources-to-be-grouped-for-access"></a>액세스를 위해 그룹화 할 문서 리소스

액세스를 위해 리소스를 그룹화 하는 방법에는 여러 가지가 있습니다. 

* Microsoft 팀은 파일, 대화 스레드 및 기타 리소스를 한 곳에서 그룹화 합니다. Microsoft 팀에 대 한 외부 액세스 전략을 세워야 합니다. [팀, OneDrive 및 SharePoint에 대 한 보안 액세스를](9-secure-access-teams-sharepoint.md)참조 하세요.

* 자격 관리 액세스 패키지를 사용 하 여 액세스 권한을 부여할 수 있는 응용 프로그램 및 기타 리소스의 단일 패키지를 만들 수 있습니다. 

* 조건부 액세스 정책은 동일한 액세스 요구 사항으로 최대 250 개의 응용 프로그램에 적용할 수 있습니다.

그러나 액세스를 관리 하 고, 함께 그룹화 해야 하는 응용 프로그램을 문서화 해야 합니다. 고려해 야 할 사항은 다음과 같습니다.

* **위험 프로필**. 잘못 된 행위자가 응용 프로그램에 대 한 액세스 권한을 얻은 경우 비즈니스에 미치는 위험은 무엇입니까? 각 응용 프로그램을 높음, 중간 또는 낮음 위험으로 코딩 해 보세요. 위험 수준이 낮은 위험 수준 응용 프로그램을 그룹화 하는 경우에는 주의 해야 합니다. 

   * 외부 사용자와 공유 해서는 안 되는 응용 프로그램을 문서화 합니다.

* **준수 프레임 워크**. 응용 프로그램에서 충족 해야 하는 준수 프레임 워크가 있으면 어떻게 되나요? 액세스 및 검토 요구 사항은 무엇 인가요?

* **특정 작업 역할 또는 부서에 대 한 응용 프로그램** 입니다. 특정 작업 역할 또는 부서의 모든 사용자에 게 액세스 권한이 필요 하기 때문에 그룹화 해야 하는 응용 프로그램이 있나요?

* **공동 작업 중심 응용 프로그램**. 외부 사용자가 액세스할 수 있는 공동 작업 중심 응용 프로그램은 무엇 인가요? Microsoft 팀과 SharePoint에 액세스할 수 있어야 합니다. Word 및 Excel과 같은 Office 365 내 생산성 응용 프로그램의 경우 외부 사용자가 자신의 라이선스를 제공 하거나 라이선스를 부여 하 고 액세스를 제공 해야 하나요?

외부 사용자가 액세스할 수 있도록 하려는 응용 프로그램 및 리소스의 각 그룹에 대해 다음을 문서화 합니다.

* 그룹에 대 한 설명이 포함 된 이름입니다 (예: *High_Risk_External_Access_Finance*). 

* 그룹에 있는 모든 응용 프로그램 및 리소스의 전체 목록입니다.

* 응용 프로그램 및 리소스 소유자와 연락처 정보입니다.

* 액세스를 제어 하거나 비즈니스 소유자에 게 위임 했는지 여부입니다.

* 액세스를 위한 백그라운드 검사 또는 학습 완료와 같은 모든 필수 구성 요소

* 리소스에 액세스 하기 위한 규정 준수 요구 사항

* 특정 리소스에 대해 multi-factor authentication을 요구 하는 등의 추가 과제가 있습니다.

* 액세스를 검토 하는 빈도 및 문서에 대 한 설명입니다.

이러한 종류의 거 버 넌 스 요금제는 내부 액세스에 대해서도 완료 해야 합니다.

## <a name="document-sign-in-conditions-for-external-users"></a>외부 사용자에 대 한 로그인 조건을 문서화 합니다.

계획의 일환으로, 리소스에 액세스할 때 외부 사용자에 대 한 로그인 요구 사항을 확인 해야 합니다. 로그인 요구 사항은 종종 리소스의 위험 프로필 및 사용자 로그인에 대 한 위험 평가를 기반으로 합니다.

로그인 조건은 [AZURE AD 조건부 액세스](../conditional-access/overview.md) 에서 구성 되며 조건 및 결과로 구성 됩니다. 예: multi-factor authentication을 요구 하는 경우

**리소스 위험 기반 로그인 조건입니다.**

| 응용 프로그램 위험 프로필| Multi-factor authentication을 트리거하기 위한 이러한 정책 고려 |
| - |-|
| 낮은 위험| 특정 응용 프로그램 집합에 대해 MFA 필요 |
| 중간 위험| 다른 위험이 있을 때 MFA 필요 |
| 높은 위험| 외부 사용자에 대해 MFA 항상 필요 |


현재는 [테 넌 트의 B2B 사용자에 대해 multi-factor authentication을 적용할](../external-identities/b2b-tutorial-require-mfa.md)수 있습니다. 

**사용자 및 장치 기반 로그인 조건**

| 사용자 또는 로그인 위험| 다음 정책 고려 |
| - | - |
| 디바이스| 준수 디바이스 필요 |
| 모바일 앱| 승인 된 앱 필요 |
| Id 보호에 높은 위험 표시| 사용자가 암호를 변경 해야 함 |
| 네트워크 위치| 특정 IP 주소 범위에서 매우 기밀 프로젝트에 로그인 해야 합니다. |

오늘날에는 장치 상태를 정책에 대 한 입력으로 사용 하려면 장치를 등록 하거나 테 넌 트에 조인 해야 합니다. 

[Id 보호 위험 기반 정책을](../conditional-access/howto-conditional-access-policy-risk.md) 사용할 수 있습니다. 그러나 사용자의 홈 테 넌 트에서 문제를 완화 해야 합니다.

[네트워크 위치의](../conditional-access/howto-conditional-access-policy-location.md)경우 소유 하는 모든 IP 주소 범위에 대 한 액세스를 제한할 수 있습니다. 외부 파트너가 조직에서 사이트에 있는 동안 응용 프로그램에 액세스 하려는 경우에만이를 사용할 수 있습니다.

[조건부 액세스 정책에 대해 자세히 알아보세요](../conditional-access/overview.md).

## <a name="document-access-review-policies"></a>문서 액세스 검토 정책

리소스에 대 한 액세스를 검토 해야 하는 경우 및 외부 사용자에 대 한 계정 액세스를 제거 해야 하는 경우에 대 한 비즈니스 정책을 문서화 합니다. 이러한 결정에 대 한 입력은 다음과 같습니다.

* 규정 준수 프레임 워크에 자세히 설명 되어 있습니다.

* 내부 비즈니스 정책 및 프로세스

* 사용자 동작

사용자의 요구에 맞게 정책을 사용자 지정 하는 경우 다음 사항을 고려 하세요.

* **자격 관리 액세스 검토**. 자격 관리의 기능을 사용 하 여

   * 는 [액세스 패키지를 자동으로 만료](../governance/entitlement-management-access-package-lifecycle-policy.md)하므로 외부 사용자는 포함 된 리소스에 액세스할 수 있습니다.

   * 액세스 검토에 [필요한 검토 빈도](../governance/entitlement-management-access-reviews-create.md) 를 설정 합니다.

   * [연결 된 조직을](../governance/entitlement-management-organization.md) 사용 하 여 단일 파트너의 모든 사용자를 그룹화 하는 경우 비즈니스 소유자 및 파트너 담당자와 정기적으로 검토를 예약 합니다.

* **그룹을 Microsoft 365** 합니다. 외부 사용자가 초대 되는 Microsoft 365 그룹에 대 한 [그룹 만료 정책을](/microsoft-365/solutions/microsoft-365-groups-expiration-policy) 설정 합니다. 

* **기타 옵션** 입니다. 외부 사용자가 권한 부여 관리 액세스 패키지 또는 Microsoft 365 그룹 외부에서 액세스할 수 있는 경우, 계정을 비활성화 하거나 삭제 해야 하는 경우를 검토 하도록 비즈니스 프로세스를 설정 합니다. 예를 들면 다음과 같습니다.

   * 90 일 동안에 로그인 하지 않은 모든 계정에 대 한 로그인 기능을 제거 합니다.

   * 모든 프로젝트의 끝에서 외부 사용자에 대 한 액세스 요구를 평가 하 고 조치를 취합니다.

 

## <a name="determine-your-access-control-methods"></a>액세스 제어 방법 결정

이제 액세스를 제어 하려는 항목, 일반적인 액세스를 위해 이러한 자산을 그룹화 하는 방법, 필요한 로그인 및 액세스 검토 정책을 배웠으므로 계획을 완료 하는 방법을 결정할 수 있습니다. 

[자격 관리](../governance/entitlement-management-overview.md)와 같은 일부 기능은 P2 (Azure AD Premium 2) 라이선스 에서만 사용할 수 있습니다. Microsoft 365 E5 및 Office 365 E5 라이선스는 Azure AD P2 라이선스를 포함 합니다. 

Microsoft 365, Office 365 및 Azure AD의 다른 조합은 외부 사용자를 관리 하기 위한 몇 가지 기능을 사용할 수 있습니다. 자세한 내용은 [Information Protection](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) 를 참조 하세요.

> [!NOTE]
> 라이선스는 사용자 당입니다. 따라서 모든 사용자에 대해 해당 라이선스를 사용 하도록 설정 하지 않고 Azure AD P2 또는 Microsoft 365 E5 수준에서 액세스 제어를 위임 하는 관리자 및 비즈니스 소유자를 비롯 한 특정 사용자를 가질 수 있습니다. 처음 5만 외부 사용자는 무료입니다. 다른 내부 사용자에 대해 P2 라이선스를 사용 하지 않는 경우 액세스 패키지와 같은 권한 관리 기능을 사용할 수 없습니다. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Azure AD P2 및 Microsoft/Office 365 E5를 사용 하 여 액세스 제어
Azure AD P2 및 Microsoft 365 E5에는 전체 보안 및 거 버 넌 스 도구 모음이 있습니다.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>프로 비전, 로그인, 액세스 검토 및 프로 비전 해제 굵게 표시 된 항목은 선호 되는 방법입니다.

| 기능| 외부 사용자 프로 비전| 로그인 요구를 적용 합니다.| 액세스 검토| 액세스 프로 비전 해제 |
| - | - | - | - | - |
| Azure AD B2B 공동 작업| 전자 메일, OTP, 셀프 서비스를 통한 초대| | **파트너 당 정기적으로 검토**| 계정 제거<br>로그인 제한 |
| 자격 관리| **할당 또는 셀프 서비스 액세스를 통해 사용자 추가**| | 액세스 검토|**액세스 패키지의 만료 또는 제거**|
| Office 365 그룹| | | 그룹 멤버 자격 검토| 그룹 만료 또는 삭제<br> 제거 양식 그룹 |
| Azure AD 보안 그룹| | **조건부 액세스 정책** (필요에 따라 보안 그룹에 외부 사용자 추가)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>리소스에 대 한 액세스. 굵게 표시 된 항목은 선호 되는 방법입니다.

|기능 | 앱 & 리소스 액세스| SharePoint & OneDrive 액세스| 팀 액세스| 전자 메일 & 문서 보안 |
| - |-|-|-|-|
| 자격 관리| **할당 또는 셀프 서비스 액세스를 통해 사용자 추가**| **액세스 패키지**| **액세스 패키지**|  |
| Office 365 그룹| | 그룹에 포함 된 사이트 및 관련 내용에 대 한 액세스| 그룹에 포함 된 팀 (및 관련 콘텐츠)에 대 한 액세스|  |
| 민감도 레이블| | **수동 및 자동으로 액세스를 분류 하 고 제한**| **수동 및 자동으로 액세스를 분류 하 고 제한**| **수동 및 자동으로 액세스를 분류 하 고 제한** |
| Azure AD 보안 그룹| **액세스 패키지에 포함 되지 않은 액세스에 대 한 조건부 액세스 정책**| | |  |


### <a name="entitlement-management"></a>자격 관리 

[자격 관리 액세스 패키지](../governance/entitlement-management-access-package-create.md) 를 통해 그룹과 팀, 응용 프로그램 및 SharePoint 사이트에 대 한 액세스를 프로 비전 하 고 프로 비전을 해제 합니다. 액세스를 허용 하는 연결 된 조직, 셀프 서비스 요청이 허용 되는지 여부 및 액세스 권한을 부여 하는 데 필요한 승인 워크플로를 정의할 수 있습니다. 액세스가 필요한 것 보다 오래 지속 되지 않도록 하려면 각 액세스 패키지에 대 한 만료 정책 및 액세스 검토를 정의할 수 있습니다. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Azure AD P1 및 Microsoft/Office 365 E3을 사용 하 여 액세스 제어
Azure AD P1 및 Microsoft 365 E3을 사용 하 여 강력한 거 버 넌 스를 달성할 수 있습니다.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>프로 비전, 로그인, 액세스 검토 및 프로 비전 해제


|기능 | 외부 사용자 프로 비전| 로그인 요구 사항 적용| 액세스 검토| 액세스 프로 비전 해제 |
| - |-|-|-|-|
| Azure AD B2B 공동 작업| **전자 메일, OTP, 셀프 서비스를 통한 초대**| 직접 B2B 페더레이션| **파트너 당 정기적으로 검토**| 계정 제거<br>로그인 제한 |
| Microsoft 또는 Office 365 그룹| | | | 그룹이 만료 되었거나 삭제 되었습니다.<br>그룹에서 제거 합니다. |
| 보안 그룹| | **외부 사용자를 보안 그룹 (조직, 팀, 프로젝트 등)에 추가 합니다.**| |  |
| 조건부 액세스 정책| | **외부 사용자에 대 한 조건부 액세스 정책 로그인**| |  |


 ### <a name="access-to-resources"></a>리소스에 대 한 액세스.

|기능 | 앱 & 리소스 액세스| SharePoint & OneDrive 액세스| 팀 액세스| 전자 메일 & 문서 보안 |
| - |-|-|-|-|
| Microsoft 또는 Office 365 그룹| | **그룹에 포함 된 사이트에 대 한 액세스 (및 관련 콘텐츠)**|**Microsoft 365 그룹 및 관련 콘텐츠를 포함 하는 팀에 대 한 액세스**|  |
| 민감도 레이블| | 수동 분류 및 액세스 제한| 액세스를 수동으로 분류 하 고 제한 합니다.| 수동으로 분류 하 여 제한 및 암호화 |
| 조건부 액세스 정책| 액세스 제어에 대 한 조건부 액세스 정책| | |  |
| 추가 방법| | SharePoint 사이트 액세스 세부적으로을 보안 그룹으로 제한 합니다.<br>직접 공유를 허용 하지 않습니다.| **팀 내에서 외부 초대 제한**|  |


### <a name="next-steps"></a>다음 단계

리소스에 대 한 외부 액세스 보안에 대 한 다음 문서를 참조 하세요. 이러한 작업은 나열 된 순서 대로 수행 하는 것이 좋습니다.

1. [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거 버 넌 스 계획을 만듭니다](3-secure-access-plan.md) .

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용 하 여 안전 하 게 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용 하 여 안전 하 게 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용 하 여 안전 하 게 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft 팀, OneDrive 및 SharePoint에 대 한 액세스 보호](9-secure-access-teams-sharepoint.md)