---
title: Azure Active Directory ID 및 액세스 관리 작업 참조 가이드
description: 이 작업 참조 가이드에서는 ID 및 액세스 관리 작업을 보호하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.
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
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298617"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Azure Active Directory ID 및 액세스 관리 작업 참조 가이드

[Azure AD 작업 참조 가이드의](active-directory-ops-guide-intro.md) 이 섹션에서는 ID 및 해당 할당의 수명 주기를 보호하고 관리하기 위해 고려해야 할 검사 및 작업에 대해 설명합니다.

> [!NOTE]
> 이러한 권장 사항은 게시 날짜를 기준으로 최신이지만 시간이 지남에 따라 변경될 수 있습니다. Microsoft 제품 및 서비스가 시간이 지남에 따라 발전함에 따라 조직은 ID 관행을 지속적으로 평가해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>주요 작업에 소유자 할당

Azure Active Directory를 관리하려면 롤아웃 프로젝트의 일부가 아닐 수 있는 주요 운영 작업 및 프로세스를 지속적으로 실행해야 합니다. 환경을 유지 관리하려면 이러한 작업을 설정하는 것이 여전히 중요합니다. 주요 작업 및 권장 소유자는 다음과 같습니다.

| Task | 소유자 |
| :- | :- |
| Azure 구독을 만드는 프로세스 정의 | 조직에 따라 다름 |
| 엔터프라이즈 모빌리티 + 보안 라이선스를 받을 수 있는 사람 결정 | IAM 운영 팀 |
| Office 365 라이선스를 받는 사람 결정 | 생산성 팀 |
| 동적, VSO 와 같은 다른 라이선스를 받을 사용자 결정 | 애플리케이션 소유자 |
| 라이선스 할당 | IAM 운영 팀 |
| 라이센스 할당 오류 문제 해결 및 해결 | IAM 운영 팀 |
| Azure AD의 응용 프로그램에 ID 프로비전 | IAM 운영 팀 |

목록을 검토할 때 소유자가 없는 작업에 대해 소유자를 할당하거나 위의 권장 사항과 일치하지 않는 소유자와 작업에 대한 소유권을 조정해야 할 수 있습니다.

#### <a name="assigning-owners-recommended-reading"></a>소유자 추천 읽기 할당

- [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure에서 거버넌스](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>온-프레미스 ID 동기화

### <a name="identify-and-resolve-synchronization-issues"></a>동기화 문제 식별 및 해결

클라우드에 동기화 문제가 발생할 수 있는 온-프레미스 환경의 문제에 대한 좋은 기준과 이해가 있는 것이 좋습니다. [IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) 및 [Azure AD Connect Health와](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) 같은 자동화된 도구는 많은 양의 오탐지를 생성할 수 있으므로 오류가 있는 개체를 정리하여 100일 이상 해결되지 않은 동기화 오류를 식별하는 것이 좋습니다. 장기적으로 해결되지 않은 동기화 오류는 지원 인시던트를 생성할 수 있습니다. [동기화 중에 오류를 해결하는 것은](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors) 다양한 유형의 동기화 오류, 이러한 오류를 유발하는 가능한 시나리오 중 일부에 대한 개요 및 오류를 수정하는 잠재적인 방법을 제공합니다.

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD 연결 동기화 구성

모든 하이브리드 환경, 장치 기반 보안 태세 및 Azure AD와의 통합을 사용하려면 직원이 데스크톱에 로그인하는 데 사용하는 사용자 계정을 동기화해야 합니다.

사용자가 로그인한 포리스트를 동기화하지 않으면 적절한 포리스트에서 오도록 동기화를 변경해야 합니다.

#### <a name="synchronization-scope-and-object-filtering"></a>동기화 범위 및 개체 필터링

동기화할 필요가 없는 알려진 개체 버킷을 제거하면 다음과 같은 운영상의 이점이 있습니다.

- 동기화 오류의 소스 감소
- 더 빠른 동기화 주기
- 클라우드와 관련이 없는 온-프레미스 서비스 계정에 대한 전역 주소 목록의 오염과 같은 온-프레미스에서 전달하는 "쓰레기"가 줄어듭니다.

> [!NOTE]
> 클라우드로 내보내지지 않는 많은 개체를 가져오는 경우 OU 또는 특정 특성으로 필터링해야 합니다.

제외할 객체의 예는 다음과 같습니다.

- 클라우드 응용 프로그램에 사용되지 않는 서비스 계정
- 리소스에 대한 액세스 권한을 부여하는 데 사용되는 그룹과 같은 클라우드 시나리오에서 사용할 수 없는 그룹
- Azure AD B2B 공동 작업으로 나타낼 외부 ID인 사용자 또는 연락처
- 직원이 서버와 같은 클라우드 응용 프로그램에 액세스하지 않는 컴퓨터 계정

> [!NOTE]
> 단일 휴먼 ID에 레거시 도메인 마이그레이션, 합병 또는 인수와 같은 내용에서 프로비전된 계정이 여러 개인 경우 사용자가 사용하는 계정(예: 컴퓨터에 로그인하는 데 사용하는 계정)만 동기화해야 합니다. .

동기화할 개체 수를 줄이는 것과 규칙의 복잡성 을 줄이는 것 사이의 균형을 맞추는 것이 좋습니다. 일반적으로 OU/컨테이너 [필터링과](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) cloudFiltered 특성에 대한 간단한 특성 매핑 간의 조합은 효과적인 필터링 조합입니다.

> [!IMPORTANT]
> 프로덕션 환경에서 그룹 필터링을 사용하는 경우 다른 필터링 방법으로 전환해야 합니다.

#### <a name="sync-failover-or-disaster-recovery"></a>장애 조치(failover) 또는 재해 복구 동기화

Azure AD Connect는 프로비저닝 프로세스에서 중요한 역할을 합니다. 어떤 이유로든 동기화 서버가 오프라인 상태가 되면 온-프레미스에 대한 변경 내용을 클라우드에서 업데이트할 수 없으며 사용자에 대한 액세스 문제가 발생할 수 있습니다. 따라서 동기화 서버가 오프라인 상태가 된 후 관리자가 동기화를 신속하게 다시 시작할 수 있는 장애 조치 전략을 정의하는 것이 중요합니다. 이러한 전략은 다음과 같은 범주에 속할 수 있습니다.

- **스테이징 모드에서 Azure AD Connect Server(의 배포)** - 관리자는 간단한 구성 스위치를 통해 스테이징 서버를 프로덕션 서버로 "승격"할 수 있습니다.
- **가상화 사용** - Azure AD 연결이 가상 시스템(VM)에 배포된 경우 관리자는 가상화 스택을 활용하여 VM을 실시간으로 마이그레이션하거나 신속하게 재배포하여 동기화를 다시 시작할 수 있습니다.

조직에 Sync에 대한 재해 복구 및 장애 조치 전략이 부족한 경우 스테이징 모드에서 Azure AD Connect를 배포하는 것을 주저하지 말아야 합니다. 마찬가지로 프로덕션 구성과 스테이징 구성 간에 불일치가 있는 경우 소프트웨어 버전 및 구성을 포함하여 프로덕션 구성과 일치하도록 Azure AD Connect 스테이징 모드를 다시 기준화해야 합니다.

![Azure AD Connect 스테이징 모드 구성의 스크린샷](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>최신 상태 유지

Microsoft는 Azure AD Connect를 정기적으로 업데이트합니다. 최신 상태를 유지하여 각 새 버전에서 제공하는 성능 향상, 버그 수정 및 새로운 기능을 활용하십시오.

Azure AD Connect 버전이 6개월 이상 지난 경우 최신 버전으로 업그레이드해야 합니다.

#### <a name="source-anchor"></a>소스 앵커

[소스 앵커로](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts) **ms-DS 일관성을** 사용하면 AD 도메인 통합/정리, 합병, 인수 및 매각에서 흔히 볼 수 있는 포리스트및 도메인 간에 개체를 보다 쉽게 마이그레이션할 수 있습니다.

현재 **ObjectGuid를** 소스 앵커로 사용하는 경우 **ms-DS-ConsistencyGuid를**사용하는 것으로 전환하는 것이 좋습니다.

#### <a name="custom-rules"></a>사용자 지정 규칙

Azure AD Connect 사용자 지정 규칙은 온-프레미스 개체와 클라우드 개체 간의 특성 흐름을 제어하는 기능을 제공합니다. 그러나 사용자 지정 규칙을 남용하거나 오용하면 다음과 같은 위험이 발생할 수 있습니다.

- 복잡성 문제 해결
- 개체 간에 복잡한 작업을 수행할 때 성능 저하
- 프로덕션 서버와 스테이징 서버 간의 구성 차이 가능성이 높습니다.
- 사용자 지정 규칙이 우선 순위 내에서 100보다 큰 경우 Azure AD Connect를 업그레이드할 때 추가 오버헤드(기본 제공 규칙에서 사용)

지나치게 복잡한 규칙을 사용하는 경우 복잡성에 대한 이유를 조사하고 단순화할 수 있는 기회를 찾아야 합니다. 마찬가지로 우선 순위가 100을 초과하는 사용자 지정 규칙을 만든 경우 기본 집합과 위험하거나 충돌하지 않도록 규칙을 수정해야 합니다.

사용자 지정 규칙을 오용하는 예는 다음과 같습니다.

- **디렉터리에서 더러운 데이터 보정** - 이 경우 AD 팀의 소유자와 함께 작업하고 디렉터리에서 데이터를 업데이트 작업으로 정리하고 잘못된 데이터의 재도입을 방지하기 위해 프로세스를 조정하는 것이 좋습니다.
- **개별 사용자의 일회성 수정** - 일반적으로 특정 사용자의 문제로 인해 특별한 대/소문자 이상값규칙을 찾는 것이 일반적입니다.
- **지나치게 복잡한 "CloudFiltering"** - 개체 수를 줄이는 것이 좋지만 많은 동기화 규칙을 사용하여 동기화 범위를 만들고 지나치게 복잡할 위험이 있습니다. OU 필터링 이외의 개체를 포함/제외하는 복잡한 논리가 있는 경우 이 논리를 동기화 외부에서 처리하고 간단한 동기화 규칙으로 흐를 수 있는 간단한 "cloudFiltered" 특성으로 개체에 레이블을 지정하는 것이 좋습니다.

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD 연결 구성 문서 작성자

[Azure AD Connect 구성 문서 작성자는](https://github.com/Microsoft/AADConnectConfigDocumenter) Azure AD Connect 설치에 대한 설명서를 생성하여 동기화 구성을 더 잘 이해하고, 올바른 작업을 수행할 수 있도록 하고, Azure AD Connect의 새 빌드 또는 구성을 적용하거나 추가 또는 업데이트된 사용자 지정 동기화 규칙을 적용했을 때 변경된 내용을 알 수 있는 도구입니다. 이 도구의 현재 기능은 다음과 같습니다.

- Azure AD Connect 동기화의 전체 구성에 대한 설명서입니다.
- 두 Azure AD Connect 동기화 서버의 구성 변경 내용 또는 지정된 구성 기준의 변경 사항에 대한 설명서입니다.
- 동기화 규칙 차이 또는 사용자 지정을 한 서버에서 다른 서버로 마이그레이션하는 PowerShell 배포 스크립트 생성

## <a name="assignment-to-apps-and-resources"></a>앱 및 리소스에 할당

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Microsoft 클라우드 서비스에 대한 그룹 기반 라이선스

Azure Active Directory는 Microsoft 클라우드 서비스에 대한 [그룹 기반 라이선스를](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) 통해 라이선스 관리를 간소화합니다. 이러한 방식으로 IAM은 그룹 인프라와 해당 그룹의 관리를 조직의 적절한 팀에 위임합니다. Azure AD에서 그룹의 구성원 자격을 설정하는 방법에는 다음과 같은 여러 가지 방법이 있습니다.

- **온-프레미스에서 동기화 -** 그룹은 Office 365에서 라이센스를 할당하도록 확장할 수 있는 그룹 관리 프로세스를 수립한 조직에 적합할 수 있는 온-프레미스 디렉터리에서 올 수 있습니다.

- **특성 기반/동적** - 그룹 (예: Department는 "sales")과 같은 사용자 특성을 기반으로 하는 식을 기반으로 클라우드에서 그룹을 만들 수 있습니다. Azure AD는 그룹의 구성원을 유지 관리하여 정의된 식과 일관되게 유지합니다. 이러한 종류의 라이선스 할당그룹을 사용하면 특성 기반 라이센스 할당을 사용할 수 있으므로 디렉터리에서 데이터 품질이 높은 조직에 적합합니다.

- **위임된 소유권** - 그룹은 클라우드에서 만들 수 있으며 지정된 소유자일 수 있습니다. 이렇게 하면 비즈니스 소유자(예: 공동 작업 팀 또는 BI 팀)가 액세스 권한이 있어야 하는 사람을 정의할 수 있습니다.

현재 수동 프로세스를 사용하여 사용자에게 라이선스 및 구성 요소를 할당하는 경우 그룹 기반 라이선스를 구현하는 것이 좋습니다. 현재 프로세스에서 라이선스 오류를 모니터링하지 않거나 할당된 내용과 사용 가능한 프로세스를 모니터링하지 않는 경우 라이선스 오류를 해결하고 라이선스 할당을 모니터링하기 위한 프로세스의 개선 사항을 정의해야 합니다.

라이센스 관리의 또 다른 측면은 조직의 작업 기능에 따라 활성화해야 하는 서비스 계획(라이센스의 구성 요소)의 정의입니다. 필요하지 않은 서비스 계획에 대한 액세스 권한을 부여하면 사용자가 Office 포털에서 교육을 받지 않았거나 사용하지 않아야 하는 도구를 볼 수 있습니다. 예를 들어, 비즈니스용 OneDrive를 콘텐츠를 공유할 수 없는 개인에게 프로비저닝할 때 추가 헬프 데스크 볼륨, 불필요한 프로비저닝 및 규정 준수 및 거버넌스를 위험에 빠뜨릴 수 있습니다.

다음 지침을 사용하여 사용자에게 서비스 계획을 정의합니다.

- 관리자는 자신의 역할에 따라 사용자에게 제공될 서비스 계획의 "번들"을 정의해야 합니다(예: 화이트 칼라 작업자 대 플로어 작업자).
- 클러스터별로 그룹을 만들고 서비스 계획에 라이선스를 할당합니다.
- 선택적으로 사용자패키지를 보관하도록 특성을 정의할 수 있습니다.

> [!IMPORTANT]
> Azure AD의 그룹 기반 라이선스는 라이선스 오류 상태의 사용자 개념을 소개합니다. 라이선스 오류가 발견되면 라이선스 할당 문제를 즉시 [식별하고 해결해야](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems) 합니다.

![컴퓨터 화면의 스크린샷 설명이 자동으로 생성됨](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>라이프사이클 관리

현재 온-프레미스 인프라에 의존하는 [Microsoft ID Manager](https://docs.microsoft.com/microsoft-identity-manager/) 또는 타사 시스템과 같은 도구를 사용하는 경우 기존 도구에서 할당을 오프로드하고 그룹 기반 라이선스를 구현하며 [그룹을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups)기반으로 그룹 수명 주기 관리를 정의하는 것이 좋습니다. 마찬가지로 기존 프로세스에서 조직을 떠나는 새 직원이나 직원을 고려하지 않는 경우 동적 그룹을 기반으로 그룹 기반 라이선스를 배포하고 그룹 구성원 수명 주기를 정의해야 합니다. 마지막으로 수명 주기 관리가 없는 온-프레미스 그룹에 대해 그룹 기반 라이선스가 배포되는 경우 클라우드 그룹을 사용하여 위임된 소유권 또는 특성 기반 동적 구성원 자격과 같은 기능을 사용하도록 설정하는 것이 좋습니다.

### <a name="assignment-of-apps-with-all-users-group"></a>"모든 사용자" 그룹이 있는 앱 할당

리소스 소유자는 모든 **사용자** 그룹에 실제로 **엔터프라이즈** 직원과 **게스트가**모두 포함될 수 있는 경우 **엔터프라이즈 직원만** 포함한다고 판단할 수 있습니다. 따라서 **모든 사용자** 그룹을 응용 프로그램 할당에 사용하고 SharePoint 콘텐츠 또는 응용 프로그램과 같은 리소스에 대한 액세스 권한을 부여할 때는 각별한 주의를 기울여야 합니다.

> [!IMPORTANT]
> 모든 **사용자** 그룹을 사용하도록 설정하고 조건부 액세스 정책, 앱 또는 리소스 할당에 사용되는 경우 게스트 사용자를 포함하지 않으려면 [그룹을 보호해야](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups) 합니다. 또한 **엔터프라이즈 직원만** 포함하는 그룹에 할당하여 라이선스 할당을 수정해야 합니다. 반면에 **모든 사용자** 그룹이 활성화되었지만 리소스에 대한 액세스 권한을 부여하는 데 사용되지 않는 경우 조직의 운영 지침이 엔터프라이즈 **직원과** **게스트**모두를 모두 포함하는 해당 그룹을 의도적으로 사용하는 것입니다.

### <a name="automated-user-provisioning-to-apps"></a>앱에 대한 자동 사용자 프로비저닝

응용 프로그램에 [대한 자동화된 사용자 프로비전은](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) 여러 시스템에서 ID의 일관된 프로비저닝, 디프로비저닝 및 수명 주기를 만드는 가장 좋은 방법입니다.

현재 임시 방식으로 앱을 프로비전하거나 CSV 파일, JIT 또는 수명 주기 관리를 처리하지 않는 온-프레미스 솔루션을 사용하는 경우 지원되는 응용 프로그램에 대해 Azure AD를 사용한 [응용 프로그램 프로비저닝을 구현하고](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application) Azure AD에서 아직 지원되지 않는 응용 프로그램에 대한 일관된 패턴을 정의하는 것이 좋습니다.

![Azure AD 프로비전 서비스](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD 연결 델타 동기화 주기 기준

조직의 변경 내용을 이해하고 예측 가능한 동기화 시간을 갖기까지 시간이 너무 오래 걸리지 않도록 하는 것이 중요합니다.

[기본 델타 동기화](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) 빈도는 30분입니다. 델타 동기화가 일관되게 30분 이상 걸리거나 스테이징 및 프로덕션의 델타 동기화 성능 간에 상당한 차이가 있는 경우 [Azure AD Connect의 성능에 영향을 미치는 요인을](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors)조사하고 검토해야 합니다.

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD 연결 문제 해결 권장 읽기

- [IdFix 도구 - Office 365를 사용하여 Office 365와 동기화할 디렉터리 특성 준비](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD 연결: 동기화 하는 동안 오류 문제 해결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>요약

보안 ID 인프라에는 다섯 가지 측면이 있습니다. 이 목록은 조직에서 ID 및 해당 권한의 수명 주기를 보호하고 관리하는 데 필요한 작업을 신속하게 찾고 조치를 취하는 데 도움이 됩니다.

- 주요 작업에 소유자를 할당합니다.
- 동기화 문제를 찾아 해결합니다.
- 재해 복구를 위한 장애 조치 전략을 정의합니다.
- 라이선스 관리 및 앱 할당을 간소화합니다.
- 앱에 대한 사용자 프로비저닝을 자동화합니다.

## <a name="next-steps"></a>다음 단계

인증 관리 [검사 및 작업을](active-directory-ops-guide-auth.md)시작합니다.
