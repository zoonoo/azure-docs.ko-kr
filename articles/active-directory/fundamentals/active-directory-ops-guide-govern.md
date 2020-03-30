---
title: Azure Active Directory 거버넌스 운영 참조 가이드
description: 이 작업 참조 가이드에서는 거버넌스 관리를 보호하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535458"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure Active Directory 거버넌스 운영 참조 가이드

[Azure AD 작업 참조 가이드의](active-directory-ops-guide-intro.md) 이 섹션에서는 환경에 대한 권한 및 권한 없는 ID, 감사 및 제어 변경 권한을 부여받은 액세스를 평가하고 증명하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.

> [!NOTE]
> 이러한 권장 사항은 게시 날짜를 기준으로 최신이지만 시간이 지남에 따라 변경될 수 있습니다. Microsoft 제품 및 서비스가 시간이 지남에 따라 발전함에 따라 조직은 거버넌스 관행을 지속적으로 평가해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>주요 작업에 소유자 할당

Azure Active Directory를 관리하려면 롤아웃 프로젝트의 일부가 아닐 수 있는 주요 운영 작업 및 프로세스를 지속적으로 실행해야 합니다. 환경을 최적화하기 위해 이러한 작업을 설정하는 것은 여전히 중요합니다. 주요 작업 및 권장 소유자는 다음과 같습니다.

| Task | 소유자 |
| :- | :- |
| SIEM 시스템에서 Azure AD 감사 로그 보관 | 인포섹 운영 팀 |
| 규정 준수를 위해 관리되는 애플리케이션 검색 | IAM 운영 팀 |
| 응용 프로그램에 대한 액세스 권한을 정기적으로 검토합니다. | 인포섹 아키텍처 팀 |
| 외부 ID에 대한 액세스를 정기적으로 검토 | 인포섹 아키텍처 팀 |
| 권한이 있는 역할이 있는 사람을 정기적으로 검토합니다. | 인포섹 아키텍처 팀 |
| 권한 있는 역할을 활성화하기 위한 보안 게이트 정의 | 인포섹 아키텍처 팀 |
| 정기적으로 동의 교부금을 검토 | 인포섹 아키텍처 팀 |
| 조직의 직원을 기반으로 하는 응용 프로그램 및 리소스에 대한 카탈로그 및 액세스 패키지 설계 | 앱 소유자 |
| 사용자에게 패키지 액세스 권한을 할당하는 보안 정책 정의 | InfoSec 팀 + 앱 소유자 |
| 정책에 승인 워크플로가 포함된 경우 워크플로 승인을 정기적으로 검토합니다. | 앱 소유자 |
| 액세스 검토를 사용하여 조건부 액세스 정책과 같은 보안 정책의 예외 검토 | 인포섹 운영 팀 |

목록을 검토할 때 소유자가 없는 작업에 대해 소유자를 할당하거나 위의 권장 사항과 일치하지 않는 소유자와 작업에 대한 소유권을 조정해야 할 수 있습니다.

#### <a name="owner-recommended-reading"></a>소유자 추천 독서

- [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure에서 거버넌스](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>구성 변경 테스트

테스트 할 때 대상 하위 집합을 롤아웃하는 것과 같은 간단한 기술에서 병렬 테스트 테넌트에 변경 내용을 배포하는 것에 이르기까지 테스트 할 때 특별한 고려 사항이 필요한 변경 사항이 있습니다. 테스트 전략을 구현하지 않은 경우 아래 표의 지침에 따라 테스트 방법을 정의해야 합니다.

| 시나리오| 권장 |
|-|-|
|인증 유형을 페더레이션에서 PHS/PTA로 변경하거나 그 반대의 경우도 마찬가지입니다.| [단계적 롤아웃을](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) 사용하여 인증 유형 변경의 영향을 테스트합니다.|
|새 조건부 액세스(CA) 정책 또는 ID 보호 정책 롤아웃|새 CA 정책을 만들고 테스트 사용자에게 할당합니다.|
|응용 프로그램의 테스트 환경 온보딩|프로덕션 환경에 응용 프로그램을 추가하고 MyApps 패널에서 숨기고 품질 보증(QA) 단계에서 사용자를 테스트하도록 할당합니다.|
|동기화 규칙 변경|현재 프로덕션 중인 구성(스테이징 모드라고도 함)으로 테스트 Azure AD Connect에서 변경 내용을 수행하고 CSExport 결과를 분석합니다. 만족하면 준비가 되면 프로덕션으로 교체하십시오.|
|브랜딩 변경|별도의 테스트 테넌트에서 테스트합니다.|
|새로운 기능 출시|이 기능이 대상 사용자 집합에 롤아웃을 지원하는 경우 파일럿 사용자를 식별하고 빌드합니다. 예를 들어 셀프 서비스 암호 재설정 및 다단계 인증은 특정 사용자 또는 그룹을 대상으로 할 수 있습니다.|
|온-프레미스 ID 공급자(IdP)에서 Active Directory로 응용 프로그램을 Azure AD로 잘라냅니다.|응용 프로그램이 여러 IdP 구성(예: Salesforce)을 지원하는 경우 응용 프로그램이 HRD 페이지를 도입하는 경우 변경 기간 동안 Azure AD를 모두 구성하고 테스트합니다. 응용 프로그램이 여러 IdP를 지원하지 않는 경우 변경 제어 기간 및 프로그램 가동 중지 시간 동안 테스트를 예약합니다.|
|동적 그룹 규칙 업데이트|새 규칙으로 병렬 동적 그룹을 만듭니다. 예를 들어 계산된 결과와 비교하여 동일한 조건으로 PowerShell을 실행합니다.<br>테스트 가 통과하면 이전 그룹이 사용된 장소를 교체합니다(가능한 경우).|
|제품 라이센스 마이그레이션|Azure [Active Directory에서 라이선스가 부여된 그룹의 단일 사용자에 대한 라이선스 변경을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses)참조하십시오.|
|승인, 발급, MFA 와 같은 AD FS 규칙 변경|그룹 클레임을 사용하여 사용자의 하위 집합을 대상으로 합니다.|
|AD FS 인증 환경 변경 또는 팜 전체 변경 사항|호스트 이름이 같은 병렬 팜을 만들고, 구성 변경 사항을 구현하고, HOSTS 파일, NLB 라우팅 규칙 또는 이와 유사한 라우팅을 사용하여 클라이언트에서 테스트합니다.<br>대상 플랫폼이 HOSTS 파일(예: 모바일 장치)을 지원하지 않는 경우 제어 변경을 합니다.|

## <a name="access-reviews"></a>액세스 검토

### <a name="access-reviews-to-applications"></a>응용 프로그램에 대한 검토 에 액세스

시간이 지남에 따라 사용자는 서로 다른 팀과 포지션을 통해 이동할 때 리소스에 대한 액세스 권한을 누적할 수 있습니다. 리소스 소유자는 정기적으로 응용 프로그램에 대한 액세스를 검토하고 사용자의 수명 주기 동안 더 이상 필요하지 않은 권한을 제거하는 것이 중요합니다. Azure AD [액세스 검토를](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) 통해 조직은 그룹 구성원 자격, 엔터프라이즈 응용 프로그램에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 리소스 소유자는 정기적으로 사용자의 액세스를 검토하여 올바른 사용자만 계속 액세스할 수 있도록 해야 합니다. 이상적으로는 이 작업에 Azure AD 액세스 검토를 사용하는 것이 좋습니다.

![액세스 리뷰 시작 페이지](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> 액세스 검토와 상호 작용하는 각 사용자에게는 유료 Azure AD Premium P2 라이선스가 있어야 합니다.

### <a name="access-reviews-to-external-identities"></a>외부 ID에 대한 검토 액세스

필요한 시간 동안 필요한 리소스에만 제한되는 외부 ID에 대한 액세스를 유지하는 것이 중요합니다. Azure AD 액세스 검토를 사용하여 모든 외부 ID 및 응용 프로그램 액세스에 대한 정기적인 자동 액세스 [검토 프로세스를 설정합니다.](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) 프로세스가 이미 온-프레미스에 있는 경우 Azure AD 액세스 검토를 사용하는 것이 좋습니다. 응용 프로그램이 사용 중지되거나 더 이상 사용되지 않는 경우 응용 프로그램에 액세스할 수 있는 모든 외부 ID를 제거합니다.

> [!NOTE]
> 액세스 검토와 상호 작용하는 각 사용자에게는 유료 Azure AD Premium P2 라이선스가 있어야 합니다.

## <a name="privileged-account-management"></a>권한 있는 계정 관리

### <a name="privileged-account-usage"></a>권한 있는 계정 사용

해커는 종종 중요한 데이터 및 시스템에 빠르게 액세스하기 위해 관리 계정 및 권한 있는 액세스의 다른 요소를 대상으로 합니다.권한 있는 역할을 가진 사용자는 시간이 지남에 따라 누적되는 경향이 있으므로 정기적으로 관리자 액세스를 검토하고 관리하고 Azure AD 및 Azure 리소스에 대한 적절한 시간 권한 있는 액세스를 제공하는 것이 중요합니다.

조직에 권한 있는 계정을 관리하는 프로세스가 없거나 현재 일반 사용자 계정을 사용하여 서비스 및 리소스를 관리하는 관리자가 있는 경우, 즉시 별도의 계정(예: 일상적인 계정)을 즉시 사용해야 합니다. 활동; 다른 하나는 권한 있는 액세스에 대한 것이며 MFA로 구성됩니다. 더 나은 아직, 조직에 Azure AD 프리미엄 P2 구독, 다음 즉시 [Azure AD 권한 ID 관리](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) (PIM)를 배포 해야 합니다. 동일한 토큰에서 해당 권한 있는 계정을 검토하고 해당되는 경우 [권한이 낮은 역할을 할당해야](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) 합니다.

구현해야 하는 권한 있는 계정 관리의 또 다른 측면은 [PIM을 통해](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review)수동으로 또는 자동화된 해당 계정에 대한 [액세스 검토를](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) 정의하는 것입니다.

#### <a name="privileged-account-management-recommended-reading"></a>권한 있는 계정 관리 권장 읽기

- [Azure AD Privileged Identity Management의 역할](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>응급 액세스 계정

조직은 다음과 같은 인증 중단과 같은 경우에 대비하여 Azure AD를 관리할 수 있도록 [긴급 계정을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) 만들어야 합니다.

- 인증 인프라의 중단 구성 요소(AD FS, 온-프레미스 AD, MFA 서비스)
- 관리 직원 이직률

관리자로서 기존 개별 사용자의 계정을 로그인하거나 활성화할 수 없기 때문에 실수로 테넌트에서 잠기지 않도록 하려면 두 개 이상의 긴급 계정을 만들고 [Microsoft의 모범 사례에](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) 맞게 구현되고 정렬되어 있고 [유리 절차를 중단해야 합니다.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)

### <a name="privileged-access-to-azure-ea-portal"></a>Azure EA 포털에 대한 권한 있는 액세스

[Azure 엔터프라이즈 계약(Azure EA) 포털을](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) 사용하면 엔터프라이즈 내에서 강력한 역할을 하는 마스터 엔터프라이즈 계약에 대해 Azure 구독을 만들 수 있습니다. Azure AD를 제자리에 배치하기 전에 이 포털을 만드는 것을 부트스트랩하는 것이 일반적이므로 Azure AD ID를 사용하여 잠그고, 포털에서 개인 계정을 제거하고, 적절한 위임이 제자리에 있는지 확인하고, 잠금의 위험을 완화해야 합니다. .

EA 포털 권한 부여 수준이 현재 "혼합 모드"로 설정되어 있는 경우 EA 포털의 모든 권한 있는 액세스에서 [Microsoft 계정을](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) 제거하고 Azure AD 계정만 사용하도록 EA 포털을 구성해야 합니다. EA 포털 위임된 역할이 구성되지 않은 경우 부서 및 계정에 대해 위임된 역할도 찾아구현해야 합니다.

#### <a name="privileged-access-recommended-reading"></a>권한 있는 액세스 권장 읽기

- [Azure Active Directory의 관리자 역할 사용 권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>권한 관리

[EM(권한 부여 관리)을](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) 사용하면 앱 소유자가 리소스를 번들로 묶고 조직의 특정 페르소나(내부 및 외부 모두)에 할당할 수 있습니다. EM은 거버넌스 정책을 유지하면서 비즈니스 소유자에게 셀프 서비스 등록 및 위임을 허용하고 액세스 권한을 부여하고 액세스 기간을 설정하며 승인 워크플로를 허용합니다. 

> [!NOTE]
> Azure AD 권한 부여 관리에는 Azure AD 프리미엄 P2 라이선스가 필요합니다.

## <a name="summary"></a>요약

보안 ID 거버넌스에는 8가지 측면이 있습니다. 이 목록은 권한이 없는 ID 및 권한 있는 ID, 감사 및 환경 변경 에 대한 제어 변경에 부여된 액세스를 평가하고 증명하기 위해 취해야 할 작업을 식별하는 데 도움이 됩니다.

- 주요 작업에 소유자를 할당합니다.
- 테스트 전략을 구현합니다.
- Azure AD Access 검토를 사용하여 그룹 구성원 자격, 엔터프라이즈 응용 프로그램에 대한 액세스 및 역할 할당을 효율적으로 관리합니다.
- 모든 유형의 외부 ID 및 응용 프로그램 액세스에 대한 정기적인 자동 액세스 검토 프로세스를 설정합니다.
- 정기적으로 관리자 액세스를 검토 및 관리하고 Azure AD 및 Azure 리소스에 대한 적절한 시간 권한 있는 액세스를 제공하기 위해 액세스 검토 프로세스를 설정합니다.
- 예기치 않은 중단에 대비하여 Azure AD를 관리할 수 있도록 긴급 계정을 프로비전합니다.
- Azure EA 포털에 대한 액세스를 잠급합니다.
- 권한 부여 관리를 구현하여 리소스 컬렉션에 대한 관리되는 액세스를 제공합니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 운영 검사 및 작업을](active-directory-ops-guide-ops.md)시작하십시오.
