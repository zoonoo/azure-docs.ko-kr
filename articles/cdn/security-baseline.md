---
title: Content Delivery Network에 대 한 Azure 보안 기준
description: Content Delivery Network 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: azure-cdn
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3b04deacce09e267bbf3c5b5b8fa51740a508082
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530972"
---
# <a name="azure-security-baseline-for-content-delivery-network"></a>Content Delivery Network에 대 한 Azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 2.0](../security/benchmarks/overview.md) 의 지침을 Content Delivery Network 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Content Delivery Network에 적용 되는 관련 지침에 따라 그룹화 됩니다. Content Delivery Network에 적용할 수 없는 **컨트롤** 은 제외 되었습니다.

Content Delivery Network 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Content Delivery Network 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="identity-management"></a>ID 관리

자세한 내용은 [Azure Security Benchmark: ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)를 참조하세요.

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: 조건에 따라 Azure 리소스 액세스 제한

**지침**: 국가 또는 지역에 따라 Content Delivery Network 콘텐츠에 대 한 액세스를 제한 합니다. 지역 필터링 기능을 사용 하 여 선택한 국가나 지역의 콘텐츠를 허용 하거나 차단 하도록 CDN 끝점의 특정 경로에 대 한 규칙을 만듭니다.

- [국가 또는 지역별 Azure CDN 콘텐츠 제한](cdn-restrict-access-by-country.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="privileged-access"></a>권한 있는 액세스

자세한 내용은 [Azure Security Benchmark: 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)를 참조하세요.

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: 중요 비즈니스용 시스템에 대한 관리 액세스 제한

**지침**: Content Delivery Network의 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 구독 및 관리 그룹에 대 한 권한 있는 액세스 권한이 부여 된 계정을 제한 함으로써 업무상 중요 한 시스템에 대 한 액세스를 격리 합니다.

또한 업무상 중요 한 시스템에 설치 된 에이전트를 사용 하 여 Dc (Active Directory 도메인 컨트롤러), 보안 도구 및 시스템 관리 도구와 같이 업무상 중요 한 액세스에 대 한 관리 권한이 있는 관리, id 및 보안 시스템에 대 한 액세스를 제한 합니다. 이러한 관리 및 보안 시스템을 손상 시키는 공격자는 비즈니스에 중요 한 자산을 손상 시키기 위해 즉시 weaponize 수 있습니다.

모든 종류의 액세스 제어는 일관 된 액세스 제어를 보장 하기 위해 엔터프라이즈 조각화 전략에 맞춰야 합니다.

- [Azure 구성 요소 및 참조 모델](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [관리 그룹 액세스](../governance/management-groups/overview.md#management-group-access) 

- [Azure 구독 관리자](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: 권한 있는 액세스 워크스테이션 사용

**지침**: 안전하고 격리된 워크스테이션은 관리자, 개발자 및 중요 서비스 운영자와 같은 중요한 역할의 보안에 매우 중요합니다. 관리 작업에는 매우 안전한 사용자 워크스테이션 및/또는 Azure 방호를 사용 합니다. Azure Active Directory (Azure AD), Microsoft Defender ATP (Advanced Threat Protection) 및/또는 Microsoft Intune를 사용 하 여 관리 작업을 위한 안전 하 고 관리 되는 사용자 워크스테이션을 배포할 수 있습니다. 보안 워크스테이션을 중앙에서 관리하여 강력한 인증, 소프트웨어 및 하드웨어 기준, 제한된 논리적 액세스 및 네트워크 액세스를 비롯한 보안 구성을 적용할 수 있습니다.

- [권한 있는 액세스 워크스테이션 이해](../active-directory/devices/concept-azure-managed-workstation.md) 

- [권한 있는 액세스 워크스테이션 배포](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: 충분한 관리 수행(최소 권한 원칙) 

**지침**: Azure 역할 기반 액세스 제어 (RBAC)를 사용 하 여 리소스를 관리 하는 Content Delivery Network. Azure RBAC를 사용 하 여 역할 할당을 통해 Azure 리소스 액세스를 관리 합니다. 이러한 역할을 사용자, 그룹 서비스 사용자 및 관리 되는 id에 할당 합니다. 특정 리소스에 대해 미리 정의된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal과 같은 도구를 통해 쿼리하거나 인벤토리에 포함할 수 있습니다. 

역할의 요구에 따라 Azure RBAC를 통해 리소스에 할당 하는 권한을 제한 합니다. 이는 Azure AD PIM(Privileged Identity Management)의 JIT(Just-in-Time) 접근 방식을 보완하며 정기적으로 검토해야 합니다. 

또한 기본 제공 역할을 사용 하 여 사용 권한을 할당 하 고 필요한 경우에만 사용자 지정 역할을 만듭니다.

- [Azure 역할 기반 access control (Azure RBAC) 이란?](../role-based-access-control/overview.md) 

- [Azure에서 RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

## <a name="asset-management"></a>자산 관리

자세한 내용은 [Azure Security Benchmark: 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)를 참조하세요.

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: 보안 팀이 자산의 위험에 대한 가시성 확보

**지침**: 보안 팀에 게 Azure Security Center를 사용 하 여 보안 위험을 모니터링 하기 위해 Azure 테 넌 트 및 구독에 대 한 보안 판독기 권한이 부여 되었는지 확인 합니다. 

보안 팀의 책임이 구성된 방식에 따라 보안 위험 모니터링은 중앙 보안 팀 또는 로컬 팀의 책임이 될 수 있습니다. 그러나 보안 정보 및 위험은 항상 조직 내에서 중앙에서 집계 되어야 합니다. 

보안 읽기 권한자 권한은 전체 테넌트(루트 관리 그룹)에 광범위하게 적용하거나 범위를 관리 그룹 또는 특정 구독으로 지정할 수 있습니다. 

참고: 작업 및 서비스에 대 한 가시성을 위해 추가 사용 권한이 필요할 수 있습니다. 

- [보안 읽기 권한자 역할 개요](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 관리 그룹 개요](../governance/management-groups/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: 보안 팀에 자산 인벤토리 및 메타데이터에 대한 액세스 권한이 있는지 확인

**지침**: Azure 리소스, 리소스 그룹 및 구독에 태그를 적용 하 여 논리적으로 분류로 구성 합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다.

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center asset inventory management](../security-center/asset-inventory.md) 

- [리소스 명명 및 태그 지정 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 사용자 환경에서 사용자가 프로 비전 할 수 있는 서비스를 감사 하 고 제한할 수 있습니다. Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하고 검색합니다. 또한 Azure Monitor를 사용하여 승인되지 않은 서비스가 검색되면 경고를 트리거하는 규칙을 만들 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/built-in-policies.md#general) 

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: 자산 수명 주기 관리의 보안 보장

**지침**: 수명 주기 관리 프로세스에서 자산에 대 한 보안을 보장 하기 위해 콘텐츠 데이터 네트워크를 사용할 수 없습니다. 높은 영향을 미치는 자산의 네트워크 구성과 특성을 유지 관리 하는 것은 고객의 책임입니다. 특성 및 네트워크 구성 변경 사항을 캡처하고 변경 영향을 측정 하 고 해당 하는 수정 작업을 만드는 프로세스를 만드는 것이 좋습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-threat-detection"></a>로깅 및 위협 탐지

자세한 내용은 [Azure Security Benchmark: 로깅 및 위협 탐지](/azure/security/benchmarks/security-controls-v2-data-protection)를 참조하세요.

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure 리소스에 대 한 위협 감지 사용

**지침**: Content Delivery Network은 해당 리소스와 관련 된 보안 위협을 모니터링 하는 기본 기능을 제공 하지 않습니다.

Content Delivery Network의 모든 로그를 사용자 지정 위협 검색을 설정 하는 데 사용할 수 있는 SIEM (보안 정보 및 이벤트 관리) 솔루션으로 전달 합니다. 

잠재적 위협 및 이상에 대 한 다양 한 유형의 Azure 자산을 모니터링 하 여 분석가가 정렬할 수 있는 가양성 경고를 줄이는 데 중점을 둡니다. 경고는 로그 데이터, 에이전트 또는 기타 데이터에서 원본으로 사용할 수 있습니다.

- [위협 검색을 위한 사용자 지정 분석 규칙 만들기](../sentinel/tutorial-detect-threats-custom.md) 

- [Azure 센티널로 위협 인텔리전스 사이버](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure 네트워크 활동에 대한 로깅 사용

**지침**: Content Delivery Network 가상 네트워크에 배포 하기 위한 것이 아닙니다 .이로 인해 네트워크 보안 그룹 흐름 로깅을 사용 하도록 설정 하거나, 방화벽을 통해 트래픽을 라우팅하거나, 패킷 캡처를 수행할 수 없습니다.

Content Delivery Network는 고객이 액세스를 위해 처리 하는 모든 네트워크 트래픽을 기록 합니다. 배포 된 제공 리소스 내에서 네트워크 흐름 기능을 사용 하도록 설정 하 고 장기 보존 및 감사를 위해 이러한 로그를 저장소 계정에 보내도록 구성 합니다.

- [자습서-Azure Content Delivery Network에서 진단 로그를 설정 하는 방법](cdn-azure-diagnostic-logs.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure 리소스에 대한 로깅 사용

**지침**: 자동으로 사용할 수 있는 활동 로그에는 읽기 작업 (GET)을 제외 하 고 Content Delivery Network 리소스에 대 한 모든 쓰기 작업 (PUT, POST, DELETE)이 포함 됩니다. 활동 로그를 사용 하 여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

CDN에 대 한 Azure 리소스 로그를 사용 하도록 설정 Azure Security Center 및 Azure Policy를 사용 하 여 리소스 로그 및 로그 데이터 수집을 사용 하도록 설정할 수 있습니다. 이러한 로그는 나중에 보안 인시던트를 조사 하 고 법정 연습을 수행 하는 데 중요할 수 있습니다.

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure의 로깅 및 다른 로그 유형 이해](../azure-monitor/platform/platform-logs-overview.md) 

- [Azure Security Center 데이터 수집 이해](../security-center/security-center-enable-data-collection.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)을 참조하세요.

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 - Azure에 대한 인시던트 응답 프로세스 업데이트

**지침**: 조직이 보안 인시던트에 대응하는 프로세스를 보유하고, 해당 프로세스를 Azure에 대해 업데이트했으며, 준비 상태를 확인하기 위해 프로세스를 정기적으로 수행하는지 확인합니다.

- [엔터프라이즈 환경에서 보안 구현](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [인시던트 응답 참조 가이드](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 준비 - 인시던트 알림 설정

**지침**: Azure Security Center에서 보안 인시던트 연락처 정보를 설정합니다. 이 연락처 정보는 MSRC(Microsoft 보안 대응 센터)가 불법적인 당사자나 권한 없는 당사자가 데이터에 액세스한 것을 발견한 경우 Microsoft가 연락하는 데 사용됩니다. 또한 인시던트 응답 요구 사항에 따라 다양한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정하는 옵션도 있습니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 검색 및 분석 – 고품질 경고에 기반한 인시던트 만들기

**지침**: 고품질 경고를 만들고 경고 품질을 측정하는 프로세스가 있는지 확인합니다. 이를 통해 과거 인시던트로부터 교훈을 습득하고 분석가에 대한 경고의 우선 순위를 지정할 수 있으므로 가양성에 시간을 낭비하지 않습니다. 

고품질 경고는 과거 인시던트로부터의 경험, 유효성이 검사된 커뮤니티 원본 및 다양한 신호 원본을 융합하고 상관 관계를 설정하여 경고를 생성하고 정리하도록 설계된 도구를 기반으로 하여 작성될 수 있습니다. 

Azure Security Center는 많은 Azure 자산에 대해 고품질 경고를 제공합니다. ASC 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. Azure Sentinel을 사용하면 조사를 위해 인시던트를 자동으로 생성하는 고급 경고 규칙을 만들 수 있습니다. 

Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 경고 및 추천 사항을 수동 또는 지속적인 방식으로 내보냅니다.

- [내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 검색 및 분석 – 인시던트 조사

**지침**: 분석가가 잠재적 인시던트를 조사할 때 다양한 데이터 원본을 쿼리하고 사용하여 발생한 상황에 대한 전체 보기를 작성할 수 있는지 확인합니다. 사각지대를 방지하기 위해 다양한 로그를 수집하여 킬 체인 전체에서 잠재적 공격자의 활동을 추적해야 합니다.  또한 다른 분석가 및 향후 기록 참조를 위해 인사이트 및 습득 지식을 캡처해야 합니다.  

조사할 데이터 원본에는 범위 내 서비스 및 실행 중인 시스템에서 이미 수집되고 있는 중앙 집중식 로깅 원본이 포함되지만 다음과 같은 원본도 포함될 수 있습니다.

- 네트워크 데이터 – 네트워크 보안 그룹의 흐름 로그, Azure Network Watcher 및 Azure Monitor를 사용하여 네트워크 흐름 로그 및 기타 분석 정보를 캡처합니다. 

- 실행 중인 시스템의 스냅샷 

    - Azure 가상 머신의 스냅샷 기능을 사용하여 실행 중인 시스템의 디스크에 대한 스냅샷을 만듭니다. 

    - 운영 체제의 기본 메모리 덤프 기능을 사용하여 실행 중인 시스템의 메모리에 대한 스냅샷을 만듭니다.

    - Azure 서비스의 스냅샷 기능 또는 소프트웨어의 자체 기능을 사용하여 실행 중인 시스템의 스냅샷을 만듭니다.

Azure Sentinel은 거의 모든 로그 원본 및 사례 관리 포털에서 광범위한 데이터 분석을 제공하여 인시던트의 전체 수명 주기를 관리합니다. 조사 중에 인텔리전스 정보는 추적 및 보고를 위해 인시던트에 연결할 수 있습니다. 

- [Windows 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 지원 진단 정보 및 메모리 덤프 수집](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel을 사용하여 인시던트 조사](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 탐지 및 분석 – 인시던트 우선 순위 지정

**지침**: 경고 심각도 및 자산 민감도에 따라 먼저 집중해야 하는 인시던트에 대한 컨텍스트를 분석가에게 제공합니다. 

Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 리소스를 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 차단, 제거, 복구 - 인시던트 처리 자동화

**지침**: 수동 반복 작업을 자동화하여 응답 시간을 단축하고 분석가의 부담을 줄입니다. 수동 작업은 실행하는 데 더 오래 걸려 각 인시던트의 속도를 늦추고 분석가가 처리할 수 있는 인시던트 수를 줄입니다. 또한 수동 작업은 분석가를 지치게 하여 지연을 유발하는 인간 오류의 위험을 높이고, 복잡한 작업에 효과적으로 집중할 수 있는 분석가의 능력을 저하시킵니다. Azure Security Center 및 Azure Sentinel의 워크플로 자동화 기능을 사용하여 작업을 자동으로 트리거하거나 플레이북을 실행하여 들어오는 보안 경고에 대응합니다. 플레이북은 알림 보내기, 계정 사용 안 함 및 문제가 있는 네트워크 격리와 같은 작업을 수행합니다. 

- [Security Center에서 워크플로 자동화 구성](../security-center/workflow-automation.md)

- [Azure Security Center에서 자동화된 위협 대응 설정](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="posture-and-vulnerability-management"></a>태세 및 취약성 관리

자세한 내용은 [Azure Security Benchmark: 태세 및 취약성 관리](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)를 참조하세요.

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV 3: 계산 리소스에 대 한 보안 구성 설정

**지침**: 해당 사항 없음 Content Delivery Network에는 보안 구성 기능이 제공 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: 자동으로 신속하게 소프트웨어 취약성 수정

**지침**: 소프트웨어 업데이트를 신속하게 배포하여 운영 체제 및 애플리케이션의 소프트웨어 취약성을 수정합니다. 

일반적인 위험 점수 매기기 프로그램 (예: 일반적인 취약성 점수 매기기 시스템) 또는 타사 검사 도구에서 제공 하는 기본 위험 등급을 사용 하 여 수정 우선 순위를 지정 합니다. 높은 수준의 보안 위험을 제공 하는 응용 프로그램 및 높은 가동 시간이 필요한 응용 프로그램의 컨텍스트를 사용 하 여 사용자 환경에 맞게 조정 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 정기적인 공격 시뮬레이션 수행

**지침**: 필요에 따라 Azure 리소스에 대한 침투 테스트 또는 레드 팀 활동을 수행하고 모든 중요한 보안 결과를 수정해야 합니다.
Microsoft Cloud 침투 테스트 시행 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다.

- [Azure의 침투 테스트](../security/fundamentals/pen-testing.md)

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="governance-and-strategy"></a>거버넌스 및 전략

자세한 내용은 [Azure Security Benchmark: 거버넌스 및 전략](../security/benchmarks/security-controls-v2-governance-strategy.md)을 참조하세요.

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의 

**지침**: 시스템 및 데이터를 지속적으로 모니터링하고 보호하기 위한 명확한 전략을 문서화하고 전달해야 합니다. 중요 비즈니스용 데이터 및 시스템의 검색, 평가, 보호, 모니터링에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   비즈니스 위험에 따른 데이터 분류 표준

-   위험 및 자산 인벤토리에 대한 보안 조직의 가시성 

-   사용할 Azure 서비스에 대한 보안 조직의 승인 

-   수명 주기 전체에서 자산 보안

-   조직 데이터 분류에 따른 필수 액세스 제어 전략

-   Azure 기본 및 타사 데이터 보호 기능 사용

-   전송 중 및 저장 사용 사례에 대한 데이터 암호화 요구 사항

-   적절한 암호화 표준

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [Azure 보안 아키텍처 권장 사항 - 스토리지, 데이터, 암호화](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 보안 기본 사항 - Azure 데이터 보안, 암호화, 스토리지](../security/fundamentals/encryption-overview.md)

- [클라우드 채택 프레임워크 - Azure 데이터 보안 및 암호화 모범 사례](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - 자산 관리](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Benchmark - 데이터 보호](/azure/security/benchmarks/security-controls-v2-data-protection)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 구분 전략 정의 

**지침**: ID, 네트워크, 애플리케이션, 구독, 관리 그룹 및 기타 컨트롤의 조합을 사용하여 자산에 대한 액세스를 구분하는 엔터프라이즈 전체 전략을 수립합니다.

서로 통신하고 데이터에 액세스해야 하는 시스템의 일상 작업을 사용하도록 설정해야 할 필요성과 보안 분리의 필요성을 신중하게 조정해야 합니다.

구분 전략이 네트워크 보안, ID 및 액세스 모델, 애플리케이션 권한/액세스 모델 및 사용자 프로세스 컨트롤을 비롯하여 컨트롤 형식 간에 일관되게 구현되는지 확인합니다.

- [Azure의 구분 전략에 대한 지침(동영상)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 구분 전략에 대한 지침(문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [엔터프라이즈 구분 전략에 맞춰 네트워크 구분 조정](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 태세 관리 전략 정의

**지침**: 개별 자산과 해당 자산이 호스트되는 환경에 대한 위험을 지속적으로 측정하고 완화합니다. 게시된 애플리케이션, 네트워크 수신 및 송신 지점, 사용자 및 관리자 엔드포인트 등과 같은 고가치 자산과 노출이 많은 공격 노출 영역에 우선 순위를 지정합니다.

- [Azure Security Benchmark - 태세 및 취약성 관리](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임, 의무 조정

**지침**: 보안 조직의 역할 및 책임에 대한 명확한 전략을 문서화하고 전달하는지 확인합니다. 보안 의사 결정에 대한 명확한 책임을 제시하고, 공유 책임 모델을 모두에게 교육하고, 클라우드를 보호하는 기술에 관해 기술 팀을 교육하는 일에 우선 순위를 지정합니다.

- [Azure 보안 모범 사례 1 – 사용자: 클라우드 보안 경험에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 보안 모범 사례 2 – 사용자: 클라우드 보안 기술에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 보안 모범 사례 3 – 프로세스: 클라우드 보안 결정에 대한 책임 할당](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   중앙 집중식 네트워크 관리 및 보안 책임

-   엔터프라이즈 구분 전략에 맞춰 조정된 가상 네트워크 구분 모델

-   다양한 위협 및 공격 시나리오에서 수정 전략

-   인터넷 에지 및 수신/송신 전략

-   하이브리드 클라우드 및 온-프레미스 상호 연결 전략

-   최신 네트워크 보안 아티팩트(예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - 네트워크 보안](/azure/security/benchmarks/security-controls-v2-network-security)

- [Azure 네트워크 보안 개요](../security/fundamentals/network-overview.md)

- [엔터프라이즈 네트워크 아키텍처 전략](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID 및 권한 있는 액세스 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure ID 및 권한 있는 액세스 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   중앙 집중식 ID 및 인증 시스템과 다른 내부 및 외부 ID 시스템 간의 상호 연결

-   다양한 사용 사례 및 조건에서 강력한 인증 방법

-   권한이 높은 사용자 보호

-   변칙 사용자 활동 모니터링 및 처리  

-   사용자 ID 및 액세스 검토와 조정 프로세스

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [Azure Security Benchmark - ID 관리](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security Benchmark - 권한 있는 액세스](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 관리 보안 개요](../security/fundamentals/identity-management-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 대응 전략 정의

**지침**: 로그 및 위협 대응 전략을 수립하여 규정 준수 요구 사항을 충족하면서 위협을 신속하게 탐지하고 수정합니다. 분석가가 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 분석가에게 고품질 경고와 원활한 환경을 제공하는 데 적용되는 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다.

-   보안 운영(SecOps) 조직의 역할 및 책임 

-   NIST 또는 다른 업계 프레임워크를 사용하여 잘 정의된 인시던트 응답 프로세스 

-   위협 탐지, 인시던트 응답 및 규정 준수 요구 사항을 지원하는 로그 캡처 및 보존

-   SIEM, 네이티브 Azure 기능, 기타 소스를 사용하여 위협에 대한 중앙 집중식 가시성 및 상관 관계 정보 

-   고객, 공급자 및 공공 당사자와의 통신 및 알림 계획

-   로깅 및 위협 탐지, 포렌식, 공격 수정, 제거와 같은 인시던트 처리를 위해 Azure 네이티브 및 타사 플랫폼 사용

-   확인한 상황 및 증거 보존을 포함하여 인시던트 및 인시던트 후 활동을 처리하는 프로세스

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [Azure Security Benchmark - 로깅 및 위협 탐지](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark - 인시던트 응답](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure 보안 모범 사례 4 - 프로세스 클라우드에 대한 인시던트 응답 프로세스 업데이트](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 채택 프레임워크, 로깅, 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 엔터프라이즈 규모, 관리, 모니터링](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.