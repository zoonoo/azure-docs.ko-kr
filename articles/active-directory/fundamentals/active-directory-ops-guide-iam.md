---
title: Azure Active Directory ID 및 액세스 관리 작업 참조 가이드
description: 이 작업 참조 가이드에서는 ID를 보호하고 관리 작업에 액세스하기 위해 수행해야 하는 검사 및 작업을 설명합니다.
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
ms.openlocfilehash: 66bce573be5a31641bdff809b8e9a79b617a703a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371004"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Azure Active Directory ID 및 액세스 관리 작업 참조 가이드

[Azure AD 작업 참조 가이드](active-directory-ops-guide-intro.md)의 이 섹션에서는 ID와 ID 할당의 수명 주기를 보호하고 관리하기 위해 고려해야 하는 검사와 작업을 설명합니다.

> [!NOTE]
> 이러한 권장 사항은 게시 시점에서 최신이지만 추후에 변경될 수 있습니다. Microsoft 제품과 서비스는 계속해서 개선되므로 조직은 ID 사용 관행을 지속해서 평가해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>키 작업에 소유자 할당

Azure Active Directory를 관리하려면 출시 프로젝트의 일부가 아닐 수 있는 주요 운영 작업 및 프로세스를 지속해서 실행해야 합니다. 환경을 유지하기 위해 이 작업을 설정하는 것도 여전히 중요합니다. 핵심 작업과 권장 소유자는 다음과 같습니다.

| Task | 소유자 |
| :- | :- |
| Azure 구독을 만드는 방법에 대한 프로세스 정의 | 조직마다 다름 |
| Enterprise Mobility + Security 라이선스를 가져오는 사람 결정 | IAM 운영 팀 |
| Microsoft 365 라이선스를 가져오는 사람 결정 | 생산성 팀 |
| 다른 라이선스를 가져오는 사람(예: Dynamics, Visual Studio Codespaces) 결정 | 애플리케이션 소유자 |
| 라이선스 할당 | IAM 운영 팀 |
| 라이선스 할당 오류 문제 해결 및 수정 | IAM 운영 팀 |
| Azure AD에서 애플리케이션으로 ID 프로비저닝 | IAM 운영 팀 |

목록을 검토할 때 소유자가 누락된 작업에 대해 소유자를 할당하거나 위의 권장 사항에 맞지 않는 소유자가 있는 작업에 대한 소유권을 조정해야 할 수도 있습니다.

#### <a name="assigning-owners-recommended-reading"></a>소유자 할당에 권장되는 문서

- [Azure Active Directory에서 관리자 역할 할당](../roles/permissions-reference.md)
- [Azure에서 거버넌스](../../governance/index.yml)

## <a name="on-premises-identity-synchronization"></a>온-프레미스 ID 동기화

### <a name="identify-and-resolve-synchronization-issues"></a>동기화 문제 식별 및 해결

온-프레미스 환경에서 클라우드로의 동기화 문제를 일으킬 수 있는 문제를 파악하고 이 문제에 대한 적절한 기준을 마련해 두고 있는 것이 좋습니다. 자동화된 도구(예: [IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix), [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#why-use-azure-ad-connect-health))는 많은 양의 가양성을 생성할 수 있음으로 오류가 발생한 개체를 정리하여 100일 넘게 해결되지 않은 상태로 남아 있는 동기화 오류를 식별하는 것이 좋습니다. 오랫동안 해결되지 않은 동기화 오류로 인해 지원 인시턴스가 생성될 수 있습니다. [동기화 중 오류 문제 해결](../hybrid/tshoot-connect-sync-errors.md)에서는 다양한 유형의 동기화 오류에 대한 개요와 그러한 오류를 야기하는 가능한 몇 가지 시나리오 및 오류를 해결하는 잠재적인 방법을 설명합니다.

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD Connect 동기화 구성

모든 하이브리드 환경, 디바이스 기반 보안 상태 및 Azure AD와의 통합을 사용하도록 설정하려면 직원이 데스크톱에 로그인하는 데 사용하는 사용자 계정을 동기화해야 합니다.

사용자가 로그인하는 포리스트를 동기화하지 않으면 적절한 포리스트에서 시작되는 동기화를 변경해야 합니다.

#### <a name="synchronization-scope-and-object-filtering"></a>동기화 범위 및 개체 필터링

동기화할 필요가 없는 개체의 알려진 버킷을 제거하면 다음과 같은 운영 혜택이 있습니다.

- 동기화 오류의 원인이 줄어듦
- 더 빨라진 동기화 주기
- 온-프레미스에서 이월할 “가비지” 감소(예: 클라우드와 관련이 없는 온-프레미스 서비스 계정의 전체 주소 목록의 오염)

> [!NOTE]
> 클라우드로 내보내지 않는 개체를 많이 가져오는 경우 OU 또는 특정 특성을 기준으로 필터링해야 합니다.

제외할 개체의 예:

- 클라우드 애플리케이션에 사용되지 않는 서비스 계정
- 클라우드 시나리오에서 사용되지 않는 그룹(예: 리소스에 대한 액세스 권한을 부여하는 데 사용되는 그룹)
- Azure AD B2B Collaboration으로 표현되는 외부 ID인 사용자 또는 연락처
- 직원이 클라우드 애플리케이션에 액세스할 수 없는 컴퓨터 계정(예: 서버)

> [!NOTE]
> 사람 ID 하나에 어떤 것(예: 레거시 도메인 마이그레이션, 합병 또는 인수)에서 프로비저닝된 계정이 여러 개 있는 경우 사용자가 일상적으로 사용하는 계정(예: 컴퓨터에 로그인하는 데 사용하는 계정)만 동기화해야 합니다.

이상적으로는 동기화할 개체 수와 규칙의 복잡성을 줄이는 것 사이의 균형을 유지해야 합니다. 일반적으로 OU/컨테이너 [필터링](../hybrid/how-to-connect-sync-configure-filtering.md)과 cloudFiltered 특성에 대한 단순 특성 매핑의 조합은 효과적인 필터링 조합입니다.

> [!IMPORTANT]
> 프로덕션에서 그룹 필터링을 사용하는 경우 다른 필터링 방법으로 전환해야 합니다.

#### <a name="sync-failover-or-disaster-recovery"></a>동기화 장애 조치(failover) 또는 재해 복구

Azure AD Connect는 프로비저닝 프로세스에서 주요 역할을 수행합니다. 어떤 이유로든 동기화 서버가 오프라인 상태가 되면 온-프레미스에 대한 변경 내용이 클라우드에서 업데이트되지 않으며 사용자에 대한 액세스 문제가 발생할 수 있습니다. 따라서 동기화 서버가 오프라인 상태가 된 후 관리자가 신속하게 동기화를 다시 시작할 수 있도록 장애 조치(failover) 전략을 정의하는 것이 중요합니다. 이러한 전략은 다음과 같은 범주로 분류될 수 있습니다.

- **준비 모드에서 Azure AD Connect 서버 배포** - 관리자가 간단한 구성 스위치로 준비 서버를 프로덕션으로 “승격”할 수 있습니다.
- **가상화 사용** - Azure AD Connect를 VM(가상 머신)에 배포하는 경우 관리자는 가상화 스택을 활용하여 VM을 실시간으로 마이그레이션하거나 빠르게 재배포하여 동기화를 다시 시작할 수 있습니다.

조직에 동기화를 위한 재해 복구 및 장애 조치(failover) 전략이 없는 경우 주저 없이 준비 모드에서 Azure AD Connect를 배포해야 합니다. 마찬가지로 프로덕션 구성과 준비 구성이 일치하지 않는 경우 소프트웨어 버전 및 구성을 포함하여 프로덕션 구성과 일치하도록 Azure AD Connect 준비 모드의 기준을 다시 설정해야 합니다.

![Azure AD Connect 준비 모드 구성 스크린샷](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>최신 상태로 유지

Microsoft는 정기적으로 Azure AD Connect를 업데이트합니다. 각각의 새 버전이 제공하는 성능 향상, 버그 수정, 새로운 기능을 활용하려면 최신 상태를 유지하세요.

Azure AD Connect 버전이 6개월 이상 지난 경우 최신 버전으로 업그레이드해야 합니다.

#### <a name="source-anchor"></a>원본 앵커

[원본 앵커](../hybrid/plan-connect-design-concepts.md)로 **ms-DS-consistencyguid** 를 사용하면 포리스트와 도메인 전체에서 개체를 더 쉽게 마이그레이션할 수 있는데, 이는 AD 도메인 통합/정리, 합병, 인수, 분할에서 일반적입니다.

현재 원본 앵커로 **ObjectGuid** 를 사용하고 있는 경우 **ms-DS-ConsistencyGuid** 사용으로 전환하는 것이 좋습니다.

#### <a name="custom-rules"></a>사용자 지정 규칙

Azure AD Connect 사용자 지정 규칙은 온-프레미스 개체와 클라우드 개체 간 특성의 흐름을 제어하는 기능을 제공합니다. 그러나 사용자 지정 규칙을 과도하게 사용하거나 잘못 사용하면 다음과 같은 위험이 발생할 수 있습니다.

- 문제 해결의 복잡성
- 개체 간에 복잡한 작업 수행 시 성능 저하
- 프로덕션 서버와 준비 서버 간에 구성이 달라질 가능성이 커짐
- (기본 제공 규칙에서 사용하는) 100보다 큰 우선 순위 내에서 사용자 지정 규칙을 만드는 경우 Azure AD Connect를 업그레이드할 때 추가 오버헤드 발생

과도하게 복잡한 규칙을 사용하는 경우 복잡성의 원인을 조사하고 간소화할 수 있는 기회를 찾아야 합니다. 마찬가지로 100을 초과하는 우선 순위 값이 있는 사용자 지정 규칙을 만든 경우에는 규칙이 위험하지 않거나 설정된 기본값과 충돌하지 하도록 규칙을 수정해야 합니다.

잘못 사용한 사용자 지정 규칙의 예는 다음과 같습니다.

- **디렉터리의 불필요한 데이터 보정** - 이 경우 AD 팀의 소유자와 함께 디렉터리의 데이터를 수정 작업으로 정리하고 잘못된 데이터를 다시 불러오지 않도록 프로세스를 조정하는 것이 좋습니다.
- **개별 사용자의 일회성 수정** - 일반적으로 특정 사용자의 문제로 인한 특수한 대/소문자 이상값을 검색하는 규칙을 찾는 것이 일반적입니다.
- **과도하게 복잡한 “CloudFiltering”** - 개체 수를 줄이는 것이 좋지만 많은 동기화 규칙을 사용하여 과도하게 복잡한 동기화 범위를 만들 위험이 있습니다. OU 필터링을 벗어난 개체를 포함/제외하는 복잡한 논리가 있는 경우 동기화 외부에서 이 논리를 처리하고 간단한 동기화 규칙을 사용하여 흐를 수 있는 간단한 “cloudFiltered” 특성으로 개체에 레이블을 적용하는 것이 좋습니다.

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD Connect 구성 구조 분석

[Azure AD Connect 구성 구조](https://github.com/Microsoft/AADConnectConfigDocumenter)는 동기화 구성을 보다 정확하게 이해하고, 잘못된 부분을 자신 있게 바로 잡고, Azure AD Connect의 새 빌드 또는 구성을 적용하거나 사용자 지정 동기화 규칙을 추가 또는 업데이트할 때 변경된 내용을 확인할 수 있도록 Azure AD Connect 설치 설명서를 생성하는 데 사용할 수 있는 도구입니다. 이 도구의 현재 기능은 다음과 같습니다.

- Azure AD Connect 동기화의 전체 구성에 대한 설명서
- 두 Azure AD Connect 동기화 서버의 구성 변경 내용 또는 지정된 구성 기준에서 변경된 내용에 관한 설명서
- 서버 간에 동기화 규칙의 차이점이나 사용자 지정을 마이그레이션하는 PowerShell 배포 스크립트 생성

## <a name="assignment-to-apps-and-resources"></a>앱 및 리소스에 할당

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Microsoft 클라우드 서비스에 대한 그룹 기반 라이선스

Azure Active Directory는 Microsoft 클라우드 서비스를 위한 [그룹 기반 라이선스](./active-directory-licensing-whatis-azure-portal.md)를 통해 라이선스 관리를 간소화합니다. 이러한 방식으로 IAM은 그룹 인프라를 제공하고 그룹 관리를 조직의 적절한 팀에 위임합니다. Azure AD에서 그룹의 멤버 자격을 설정하는 방법에는 다음을 비롯하여 여러 가지가 있습니다.

- **온-프레미스에서 동기화** - 그룹은 온-프레미스 디렉터리에서 가져올 수 있으며, 이는 Microsoft 365에서 라이선스를 할당하도록 확장할 수 있는 그룹 관리 프로세스를 설정한 조직에 적합합니다.

- **특성 기반/동적** - 그룹은 사용자 특성을 기반으로 하는 식(예: Department equals “sales”)을 기반으로 클라우드에서 만들 수 있습니다. Azure AD는 정의된 식에 일치하도록 그룹의 멤버를 유지 관리합니다. 라이선스 할당을 위해 이러한 종류의 그룹을 사용하면 특성 기반 라이선스 할당을 사용하도록 설정할 수 있습니다 .이는 디렉터리에서 데이터 품질이 높은 조직에 적합합니다.

- **위임된 소유권** -그룹은 클라우드에서 만들 수 있으며 소유자가 지정될 수 있습니다. 이러한 방식으로 액세스 권한이 있어야 하는 사람을 정의하도록 비즈니스 소유자(예: 협업 팀 또는 BI 팀)의 역량을 강화할 수 있습니다.

현재 수동 프로세스를 사용하여 사용자에게 라이선스 및 구성 요소를 할당하는 경우 그룹 기반 라이선스를 구현하는 것이 좋습니다. 현재 프로세스가 라이선스 오류나 할당된 항목과 사용 가능한 항목을 모니터링하지 않는 경우에는 프로세스에 대한 향상된 기능을 정의하여 라이선스 오류를 해결하고 라이선스 할당을 모니터링해야 합니다.

라이선스 관리의 또 다른 측면은 조직 내 직무에 따라 사용하도록 설정해야 하는 서비스 계획(라이선스의 구성 요소)의 정의입니다. 필요하지 않은 서비스 계획에 대해 액세스 권한을 부여하면 Office 포털에 학습하지 않았거나 사용하면 안 되는 도구가 표시됩니다. 그러면 지원 센터의 업무량 증가, 불필요한 프로비저닝이 발생하여 예를 들어, 콘텐츠 공유가 허용되면 안 되는 개인에게 비즈니스용 OneDrive를 프로비저닝하는 경우처럼 준수 및 거버넌스가 위험해질 수 있습니다.

다음 지침을 사용하여 사용자에 대한 서비스 계획을 정의합니다.

- 관리자는 역할에 따라 사용자에게 제공되는 서비스 계획의 “번들”을 정의해야 합니다(예: 사무직과 현장 근로자).
- 클러스터별로 그룹을 만들고 서비스 계획을 사용하여 라이선스를 할당합니다.
- 필요에 따라 특성을 정의하여 사용자에 대한 패키지를 유지할 수 있습니다.

> [!IMPORTANT]
> Azure AD의 그룹 기반 라이선스에는 라이선스 오류 상태의 사용자에 대한 개념이 도입되었습니다. 라이선스 오류를 확인하면 즉시 라이선스 할당 문제를 [파악하여 해결](../enterprise-users/licensing-groups-resolve-problems.md)해야 합니다.

![자동으로 생성된 컴퓨터 화면 설명 스크린샷](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>수명 주기 관리

[Microsoft Identity Manager](/microsoft-identity-manager/) 또는 온-프레미스 인프라를 사용하는 타사 시스템과 같은 도구를 현재 사용 중인 경우 기존 도구에서 할당을 제거하고, 그룹 기반 라이선스를 구현하고, [그룹](../enterprise-users/licensing-group-advanced.md#use-group-based-licensing-with-dynamic-groups)을 기반으로 그룹 수명 주기 관리를 정의하는 것이 좋습니다. 마찬가지로 기존 프로세스에서 신규 직원 또는 퇴사 직원을 고려하지 않는 경우 동적 그룹을 기반으로 그룹 기반 라이선스를 배포하고 그룹 멤버 자격 수명 주기를 정의해야 합니다. 마지막으로, 그룹 기반 라이선스가 수명 주기를 관리하지 않는 온-프레미스 그룹에 대해 배포된 경우 클라우드 그룹을 사용하여 위임된 소유권 또는 특성 기반 동적 멤버 자격과 같은 기능을 사용하도록 설정하는 것이 좋습니다.

### <a name="assignment-of-apps-with-all-users-group"></a>“모든 사용자” 그룹을 사용하여 앱 할당

리소스 소유자는 실제로 **기업 직원** 과 **게스트** 를 둘 다 포함할 수 있는 경우 **모든 사용자** 그룹에 **기업 직원** 만 포함된다고 생각할 수 있습니다. 따라서 애플리케이션 할당에 **모든 사용자** 그룹을 사용하고 SharePoint 콘텐츠 또는 애플리케이션과 같은 리소스에 대한 액세스 권한을 부여하는 경우 특별한 주의가 필요합니다.

> [!IMPORTANT]
> **모든 사용자** 그룹을 사용하도록 설정하여 조건부 액세스 정책, 앱 또는 리소스 할당에 사용하는 경우 게스트 사용자를 포함하지 않으려면 [그룹을 보호](../external-identities/use-dynamic-groups.md)해야 합니다. 또한 **기업 직원** 만 포함된 그룹을 만들고 이 그룹에 할당하여 라이선스 할당을 수정해야 합니다. 반면에 **모든 사용자** 그룹을 사용하도록 설정했는데 리소스에 대한 액세스 권한을 부여하는데 사용되지 않는 경우 조직의 운영 지침이 ( **기업 직원** 과 **게스트** 를 둘 다 포함하고 있는) 해당 그룹을 의도적으로 사용하는 것인지 확인해야 합니다.

### <a name="automated-user-provisioning-to-apps"></a>앱에 대해 자동화된 사용자 프로비저닝

애플리케이션에 대해 [자동화된 사용자 프로비저닝](../app-provisioning/user-provisioning.md)은 일관된 프로비저닝, 프로비저닝 해제와 여러 시스템에서 ID 수명 주기를 만드는 가장 좋은 방법입니다.

임시 방편으로 앱을 프로비저닝하거나, 수명 주기 관리를 처리하지 않는 CSV 파일, JIT 또는 온-프레미스 솔루션과 같은 항목을 사용하는 경우, 지원되는 애플리케이션에 대해 Azure AD를 사용하여 [애플리케이션 프로비저닝을 구현](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application)하고 Azure AD에서 아직 지원하지 않는 애플리케이션에 대해 일관적인 패턴을 정의하는 것이 좋습니다.

![Azure AD 프로비저닝 서비스](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect 델타 동기화 주기 기준

조직 내 변화량을 파악하고 예측한 동기화 시간이 너무 길지 않도록 해야 합니다.

[기본 델타 동기화](../hybrid/how-to-connect-sync-feature-scheduler.md) 빈도는 30분입니다. 델타 동기화가 지속적으로 30분 보다 긴 경우 또는 준비와 프로덕션의 델타 동기화 성능 간에 상당한 차이가 있는 경우 [Azure AD Connect의 성능에 영향을 미치는 요인](../hybrid/plan-connect-performance-factors.md)을 조사하고 검토해야 합니다.

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect 문제 해결을 위한 권장 문서

- [IdFix 도구를 사용하여 Microsoft 365와 동기화하기 위한 디렉터리 특성 준비](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: 동기화 중 오류 문제 해결](../hybrid/tshoot-connect-sync-errors.md)

## <a name="summary"></a>요약

보안 ID 인프라에는 5가지 측면이 있습니다. 이 목록은 조직에서 ID 및 자격의 수명 주기를 보호하고 관리하는 데 필요한 작업을 빠르게 찾고 수행하는 데 도움이 될 것입니다.

- 주요 작업에 소유자를 할당합니다.
- 동기화 문제를 찾아 해결합니다.
- 재해 복구를 위한 장애 조치(failover) 전략을 정의합니다.
- 앱의 라이선스 및 할당 관리를 간소화합니다.
- 앱에 대한 사용자 프로비저닝을 자동화합니다.

## <a name="next-steps"></a>다음 단계

[인증 관리 검사와 작업](active-directory-ops-guide-auth.md)으로 시작합니다.