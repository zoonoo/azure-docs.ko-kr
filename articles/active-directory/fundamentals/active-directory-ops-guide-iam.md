---
title: Azure Active Directory Id 및 액세스 관리 작업 참조 가이드
description: 이 작업 참조 가이드는 id 및 액세스 관리 작업을 보호 하기 위해 수행 해야 하는 검사 및 작업에 대해 설명 합니다.
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
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298617"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Azure Active Directory Id 및 액세스 관리 작업 참조 가이드

[AZURE AD 작업 참조 가이드](active-directory-ops-guide-intro.md) 의이 섹션에서는 id 및 해당 할당의 수명 주기를 보호 하 고 관리 하기 위해 고려해 야 하는 검사 및 작업에 대해 설명 합니다.

> [!NOTE]
> 이러한 권장 사항은 게시 날짜를 따라 최신 이지만 시간이 지남에 따라 변경 될 수 있습니다. 조직에서는 Microsoft 제품 및 서비스가 시간이 지남에 따라 발전 함에 따라 id 사례를 지속적으로 평가 해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>키 작업에 소유자 할당

Azure Active Directory를 관리 하려면 롤아웃 프로젝트에 포함 될 수 없는 주요 운영 작업 및 프로세스를 지속적으로 실행 해야 합니다. 사용자 환경을 유지 하기 위해 이러한 작업을 설정 하는 것도 중요 합니다. 핵심 작업과 권장 소유자는 다음과 같습니다.

| Task | 소유자 |
| :- | :- |
| Azure 구독을 만드는 방법에 대 한 프로세스 정의 | 조직 마다 다름 |
| Enterprise Mobility + Security 라이선스를 가져오는 사람 결정 | IAM 운영 팀 |
| Office 365 라이선스를 받는 사람 결정 | 생산성 팀 |
| 다른 라이선스를 가져오는 사용자 (예: Dynamics, VSO)를 결정 합니다. | 애플리케이션 소유자 |
| 라이선스 할당 | IAM 운영 팀 |
| 라이선스 할당 오류 문제 해결 및 재구성 | IAM 운영 팀 |
| Azure AD의 응용 프로그램에 id 프로 비전 | IAM 운영 팀 |

목록을 검토할 때 소유자가 없는 작업의 소유자를 할당 하거나 위의 권장 사항에 맞지 않는 소유자가 있는 작업에 대 한 소유권을 조정 해야 할 수 있습니다.

#### <a name="assigning-owners-recommended-reading"></a>소유자 할당 권장 읽기

- [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure에서 거버넌스](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>온-프레미스 id 동기화

### <a name="identify-and-resolve-synchronization-issues"></a>동기화 문제 식별 및 해결

온-프레미스 환경에서 클라우드로의 동기화 문제를 일으킬 수 있는 문제를 파악 하 고 이해 하는 것이 좋습니다. [Idfix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) 및 [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) 와 같은 자동화 된 도구는 많은 양의 가양성을 생성할 수 있으므로 오류가 발생 한 개체를 정리 하 여 100 일 넘게 남아 있는 동기화 오류를 식별 하는 것이 좋습니다. 해결 되지 않은 장기 동기화 오류로 인해 지원 인시던트가 생성 될 수 있습니다. [동기화 하는 동안 발생 하는 오류 문제 해결](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors) 에서는 서로 다른 유형의 동기화 오류에 대 한 개요를 제공 하며, 이러한 오류를 야기 하는 가능한 몇 가지 시나리오와 오류 해결 방법을 제공 합니다.

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD Connect 동기화 구성

모든 하이브리드 환경, 장치 기반 보안 상태 및 Azure AD와의 통합을 사용 하도록 설정 하려면 직원이 데스크톱에 로그인 하는 데 사용 하는 사용자 계정을 동기화 해야 합니다.

사용자가 로그인 하는 포리스트를 동기화 하지 않는 경우 적절 한 포리스트에서 동기화를 변경 해야 합니다.

#### <a name="synchronization-scope-and-object-filtering"></a>동기화 범위 및 개체 필터링

동기화 하지 않아도 되는 개체의 알려진 버킷을 제거 하면 다음과 같은 작업 이점이 있습니다.

- 동기화 오류의 소스 수가 적음
- 더 빠른 동기화 주기
- 온-프레미스에서 전달 하는 "가비지"가 부족 합니다 (예: 클라우드에서 관련이 없는 온-프레미스 서비스 계정의 전체 주소 목록에 대 한 오염).

> [!NOTE]
> 클라우드로 내보내지 않는 많은 개체를 가져오는 경우 OU 또는 특정 특성을 기준으로 필터링 해야 합니다.

제외할 개체의 예는 다음과 같습니다.

- 클라우드 응용 프로그램에 사용 되지 않는 서비스 계정
- 리소스에 대 한 액세스 권한을 부여 하는 데 사용 되는 것과 같은 클라우드 시나리오에서 사용 하지 않는 그룹
- Azure AD B2B 공동 작업으로 표현 되는 외부 id 인 사용자 또는 연락처
- 직원이 클라우드 응용 프로그램에 액세스할 수 없는 컴퓨터 계정 (예: 서버)

> [!NOTE]
> 단일 사용자 id에 레거시 도메인 마이그레이션, 병합기 또는 획득과 같은 항목에서 프로 비전 된 계정이 여러 개 있는 경우 사용자가 컴퓨터에 로그인 하는 데 사용 하는 것과 같이 일상적인 계정을 매일 동기화 해야 합니다. .

이상적으로는 동기화 할 개체 수와 규칙의 복잡성을 줄이는 것 사이의 균형을 유지 해야 합니다. 일반적으로 OU/컨테이너 [필터링](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) 과 cloudfiltered 된 특성에 대 한 단순 특성 매핑의 조합은 효과적인 필터링 조합입니다.

> [!IMPORTANT]
> 프로덕션에서 그룹 필터링을 사용 하는 경우 다른 필터링 방법으로 전환 해야 합니다.

#### <a name="sync-failover-or-disaster-recovery"></a>동기화 장애 조치 (failover) 또는 재해 복구

Azure AD Connect는 프로 비전 프로세스에서 주요 역할을 수행 합니다. 어떤 이유로 든 동기화 서버가 오프 라인 상태가 되 면 온-프레미스에 대 한 변경 내용이 클라우드에서 업데이트 되지 않으며 사용자에 대 한 액세스 문제가 발생할 수 있습니다. 따라서 동기화 서버가 오프 라인 상태가 된 후 관리자가 신속 하 게 동기화를 다시 시작할 수 있도록 장애 조치 (failover) 전략을 정의 하는 것이 중요 합니다. 이러한 전략은 다음과 같은 범주로 나눌 수 있습니다.

- **준비 모드에서 Azure AD Connect 서버 배포** -관리자가 간단한 구성 스위치를 사용 하 여 스테이징 서버를 프로덕션으로 "승격" 할 수 있습니다.
- **가상화 사용** -Azure AD CONNECT를 vm (가상 머신)에 배포 하는 경우 관리자는 가상화 스택을 활용 하 여 vm을 실시간으로 마이그레이션하거나 빠르게 재배포 하 여 동기화를 다시 시작할 수 있습니다.

조직에서 동기화에 대 한 재해 복구 및 장애 조치 (failover) 전략을 주저 준비 모드에서 Azure AD Connect 배포 하지 않아야 합니다. 마찬가지로 프로덕션 구성과 준비 구성이 일치 하지 않는 경우 소프트웨어 버전 및 구성을 포함 하 여 프로덕션 구성과 일치 하도록 Azure AD Connect 준비 모드의 기준을 다시 설정 해야 합니다.

![Azure AD Connect 준비 모드 구성의 스크린샷](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>최신 상태 유지

Microsoft 업데이트는 정기적으로 Azure AD Connect. 새 버전이 제공 하는 성능 향상, 버그 수정 및 새로운 기능을 활용 하려면 최신 상태를 유지 하세요.

Azure AD Connect 버전이 6 개월 이상 지난 경우 최신 버전으로 업그레이드 해야 합니다.

#### <a name="source-anchor"></a>원본 앵커

[원본 앵커](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts) 로 **msds-consistencyguid** 를 사용 하면 AD 도메인 통합/정리, 인수, 인수 및 divestitures에서 공통적으로 사용 되는 포리스트와 도메인에서 개체를 더 쉽게 마이그레이션할 수 있습니다.

현재 **ObjectGuid** 를 원본 앵커로 사용 하 고 있는 경우 **msds-consistencyguid**를 사용 하도록 전환 하는 것이 좋습니다.

#### <a name="custom-rules"></a>사용자 지정 규칙

Azure AD Connect 사용자 지정 규칙은 온-프레미스 개체와 클라우드 개체 간 특성의 흐름을 제어 하는 기능을 제공 합니다. 그러나 사용자 지정 규칙을 과도 하거나 오용 하면 다음과 같은 위험이 발생할 수 있습니다.

- 복잡성 문제 해결
- 개체 간에 복잡 한 작업을 수행할 때 성능 저하
- 프로덕션 서버와 준비 서버 간에 구성이 확산 될 확률이 높습니다.
- 기본 제공 규칙에서 사용 하는 100 보다 큰 우선 순위 내에서 사용자 지정 규칙을 만들 경우 Azure AD Connect를 업그레이드할 때 추가 오버 헤드가 발생 합니다.

과도 하 게 복잡 한 규칙을 사용 하는 경우 복잡성의 원인을 조사 하 고 간소화를 위한 기회를 찾아야 합니다. 마찬가지로 100 보다 우선 순위 값이 있는 사용자 지정 규칙을 만든 경우에는 규칙이 위험 하지 않거나 기본 집합과 충돌 하도록 규칙을 수정 해야 합니다.

잘못 된 사용자 지정 규칙의 예는 다음과 같습니다.

- **디렉터리의 더티 데이터를 보정** 합니다 .이 경우 AD 팀의 소유자에 게 문의 하 여 디렉터리의 데이터를 수정 작업으로 정리 하 고 잘못 된 데이터를 다시 도입 하지 않도록 프로세스를 조정 하는 것이 좋습니다.
- **개별 사용자의 일회용 재구성** -일반적으로 특정 사용자의 문제로 인 한 특별 한 대/소문자를 구분 하는 규칙을 찾는 것이 일반적입니다.
- **과도 하 게 복잡 한 "CloudFiltering"** -개체 수를 줄이는 것이 좋지만 많은 동기화 규칙을 사용 하 여 동기화 범위를 만들고 지나치게 복잡 하 게 만들 위험이 있습니다. OU 필터링을 통해 개체를 포함/제외 하는 복잡 한 논리가 있는 경우 동기화 외부에서이 논리를 처리 하 고 간단한 동기화 규칙을 사용 하 여 흐를 수 있는 간단한 "cloudFiltered" 특성을 사용 하 여 개체에 레이블을 적용 하는 것이 좋습니다.

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD Connect 구성 구조

[Azure AD Connect 구성 구조](https://github.com/Microsoft/AADConnectConfigDocumenter) 는 Azure AD Connect 설치 설명서를 생성 하는 데 사용할 수 있는 도구입니다 .이 도구를 사용 하 여 동기화 구성을 보다 잘 이해 하 고, 적절 한 작업을 수행 하 고, 새 빌드 또는 구성 Azure AD Connect를 적용 하거나 사용자 지정 동기화 규칙을 추가 하거나 업데이트할 때 변경 된 내용을 확인할 수 있습니다. 도구의 현재 기능에는 다음이 포함 됩니다.

- Azure AD Connect 동기화의 전체 구성에 대 한 설명서입니다.
- 두 Azure AD Connect 동기화 서버 구성의 변경 내용 또는 지정 된 구성 기준에서의 변경 내용에 대 한 설명서입니다.
- 동기화 규칙의 차이점이 나 사용자 지정을 서버 간에 마이그레이션하는 PowerShell 배포 스크립트를 생성 합니다.

## <a name="assignment-to-apps-and-resources"></a>앱 및 리소스에 대 한 할당

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Microsoft 클라우드 서비스에 대 한 그룹 기반 라이선스

Azure Active Directory Microsoft 클라우드 서비스에 대 한 [그룹 기반 라이선스](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) 를 통해 라이선스 관리를 간소화 합니다. 이러한 방식으로 IAM은 그룹 인프라를 제공 하 고 해당 그룹의 위임 된 관리를 조직의 적절 한 팀에 제공 합니다. Azure AD에서 그룹의 멤버 자격을 설정 하는 방법에는 다음을 비롯 한 여러 가지가 있습니다.

- 온 **-프레미스에서 동기화** -그룹은 온-프레미스 디렉터리에서 가져올 수 있으며,이는 office 365에서 라이선스를 할당 하도록 확장할 수 있는 그룹 관리 프로세스를 설정한 조직에 적합 합니다.

- **특성 기반/동적** 그룹은 사용자 특성을 기반으로 하는 식을 기반으로 클라우드에서 만들 수 있습니다. 예를 들어 부서는 "sales"와 같습니다. Azure AD는 정의 된 식과 일관 되 게 유지 하면서 그룹의 멤버를 유지 관리 합니다. 라이선스 할당을 위해 이러한 종류의 그룹을 사용 하면 특성 기반 라이선스 할당을 사용할 수 있습니다 .이는 해당 디렉터리에서 데이터 품질이 높은 조직에 적합 합니다.

- **위임 된 소유권** -그룹은 클라우드에서 만들 수 있으며 소유자가 지정 될 수 있습니다. 이러한 방식으로 공동 작업 팀 또는 BI 팀과 같은 비즈니스 소유자가 액세스할 수 있는 사용자를 정의할 수 있습니다.

현재 수동 프로세스를 사용 하 여 사용자에 게 라이선스 및 구성 요소를 할당 하는 경우 그룹 기반 라이선스를 구현 하는 것이 좋습니다. 현재 프로세스가 라이선스 오류 또는 할당 된 및 사용 가능한 항목을 모니터링 하지 않는 경우에는 프로세스에 대 한 향상 된 기능을 정의 하 여 라이선스 오류를 해결 하 고 라이선스 할당을 모니터링 해야 합니다.

라이선스 관리의 또 다른 측면은 조직의 작업 기능에 따라 사용 하도록 설정 해야 하는 서비스 계획 (라이선스의 구성 요소)의 정의입니다. 필요 하지 않은 서비스 계획에 대 한 액세스 권한을 부여 하면 사용자가 Office 포털에서 학습 되지 않았거나 사용 하지 않는 도구를 볼 수 있습니다. 추가 지원 센터 볼륨, 불필요 한 프로 비전을 구동 하 고, 콘텐츠를 공유 하지 못할 수 있는 개인에 게 비즈니스용 OneDrive를 프로 비전 하는 경우와 같은 위험에 준수 및 거 버 넌 스를 배치할 수 있습니다.

다음 지침을 사용 하 여 사용자에 대 한 서비스 계획을 정의 합니다.

- 관리자는 해당 역할에 따라 사용자에 게 제공 되는 서비스 계획의 "번들"을 정의 해야 합니다. 예를 들어, 흰색의 작업자와 floor worker를 정의 해야 합니다.
- 클러스터 별로 그룹을 만들고 서비스 계획으로 라이선스를 할당 합니다.
- 필요에 따라 특성을 정의 하 여 사용자에 대 한 패키지를 저장할 수 있습니다.

> [!IMPORTANT]
> Azure AD의 그룹 기반 라이선스에는 라이선스 오류 상태의 사용자에 대 한 개념이 도입 되었습니다. 라이선스 오류가 발생 하는 경우에는 즉시 라이선스 할당 문제를 [식별 하 고 해결](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems) 해야 합니다.

![자동으로 생성 된 컴퓨터 화면 설명의 스크린샷](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>수명 주기 관리

현재 온-프레미스 인프라에 의존 하는 [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/) 또는 타사 시스템과 같은 도구를 사용 하 고 있는 경우 기존 도구에서 할당을 오프 로드 하 고 그룹 기반 라이선스를 구현 하며 [그룹을 기반](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups)으로 그룹 수명 주기 관리를 정의 하는 것이 좋습니다. 마찬가지로 기존 프로세스에서 조직을 떠나는 새 직원 또는 직원을 고려 하지 않는 경우 동적 그룹을 기반으로 그룹 기반 라이선스를 배포 하 고 그룹 멤버 자격 수명 주기를 정의 해야 합니다. 마지막으로, 그룹 기반 라이선스가 수명 주기 관리가 없는 온-프레미스 그룹에 대해 배포 된 경우 클라우드 그룹을 사용 하 여 위임 된 소유권 또는 특성 기반 동적 멤버 자격과 같은 기능을 사용 하도록 설정 하는 것이 좋습니다.

### <a name="assignment-of-apps-with-all-users-group"></a>"모든 사용자" 그룹을 사용 하 여 앱 할당

리소스 소유자는 **Enterprise 직원과** **게스트가**실제로 모두 포함 될 수 있는 경우 **모든 사용자** 그룹에 **엔터프라이즈 직원만** 포함 되어 있다고 생각할 수 있습니다. 따라서 응용 프로그램 할당을 위해 **모든 사용자** 그룹을 사용 하 고 SharePoint 콘텐츠 또는 응용 프로그램과 같은 리소스에 대 한 액세스 권한을 부여 하는 경우 특별 한 주의가 필요 합니다.

> [!IMPORTANT]
> **모든 사용자** 그룹을 사용 하도록 설정 하 고 조건부 액세스 정책, 앱 또는 리소스 할당에 사용 하는 경우 게스트 사용자를 포함 하지 않으려는 경우 [그룹을 보호](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups) 해야 합니다. 또한 **Enterprise 직원만** 포함 된 그룹을 만들고 할당 하 여 라이선스 할당을 수정 해야 합니다. 반면에 **모든 사용자** 그룹을 사용 하도록 설정 하 고 리소스에 대 한 액세스 권한을 부여 하는 데 사용 하지 않는 경우 조직의 운영 지침이 해당 그룹 ( **Enterprise 직원과** **게스트**모두 포함)을 의도적으로 사용 하 고 있는지 확인 해야 합니다.

### <a name="automated-user-provisioning-to-apps"></a>앱에 대 한 자동화 된 사용자 프로비저닝

응용 프로그램에 대 한 [자동화 된 사용자 프로 비전은](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) 일관 된 프로 비전, 프로 비전 해제 및 여러 시스템에서 id 수명 주기를 만드는 가장 좋은 방법입니다.

현재 임시 방식으로 앱을 프로 비전 하거나, 수명 주기 관리를 처리 하지 않는 CSV 파일, JIT 또는 온-프레미스 솔루션과 같은 항목을 사용 하는 경우, 지원 되는 응용 프로그램에 대해 Azure AD를 사용 하 여 [응용 프로그램 프로 비전을 구현](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application) 하 고 azure ad에서 아직 지원 되지 않는 응용 프로그램에 대해 일관적인 패턴을 정의 하는 것이 좋습니다.

![Azure AD 프로 비전 서비스](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect 델타 동기화 주기 기준선

조직의 변화에 대 한 변화를 이해 하 고 동기화 시간을 예측 하는 데 시간이 너무 오래 걸리는 지 확인 하는 것이 중요 합니다.

[기본 델타 동기화](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) 빈도는 30 분입니다. 델타 동기화가 30 분 이상 지속 되는 경우 또는 스테이징 및 프로덕션의 델타 동기화 성능 간에 상당한 차이가 있는 경우 [Azure AD Connect의 성능에 영향](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors)을 주는 요인을 조사 하 고 검토 해야 합니다.

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect 문제 해결 권장 사항

- [IdFix 도구를 사용 하 여 Office 365와 동기화 하기 위한 디렉터리 특성 준비-Office 365](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: 동기화 중 오류 문제 해결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>요약

보안 Id 인프라에는 5 가지 측면이 있습니다. 이 목록을 통해 조직에서 id 및 자격 증명의 수명 주기를 안전 하 게 보호 하 고 관리 하는 데 필요한 작업을 빠르게 찾고 수행할 수 있습니다.

- 키 작업에 소유자를 할당 합니다.
- 동기화 문제를 찾아 해결 합니다.
- 재해 복구에 대 한 장애 조치 (failover) 전략을 정의 합니다.
- 앱의 라이선스 및 할당 관리를 간소화 합니다.
- 앱에 대 한 사용자 프로 비전을 자동화 합니다.

## <a name="next-steps"></a>다음 단계

[인증 관리 확인 및 작업](active-directory-ops-guide-auth.md)을 시작 합니다.
