---
title: Azure 전면 도어에 대 한 azure 보안 기준
description: Azure 전면 도어 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9e9eca3bb230bbfc969a5a429f664e602ad0d78b
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888320"
---
# <a name="azure-security-baseline-for-azure-front-door"></a>Azure 전면 도어에 대 한 azure 보안 기준

이 보안 기준은 [Azure 보안 벤치 마크 버전 2.0](../security/benchmarks/overview.md) 의 지침을 Azure Front 도어에 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 azure 보안 벤치 마크에 정의 된 **보안 컨트롤과** Azure Front 문에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure Front 문에 적용할 수 없는 **컨트롤이** 제외 되었습니다.

Azure 프런트 도어가 Azure 보안 벤치 마크에 완전히 매핑되는 방식을 보려면 [전체 Azure Front 도어 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](/azure/security/benchmarks/security-controls-v2-network-security)을 참조 하세요.*

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 외부 네트워크 공격 으로부터 응용 프로그램 및 서비스 보호

**지침**: Azure PowerShell을 사용 하 여 지역 필터링 정책을 만들고 정책을 기존 Azure Front 도어가 프런트 엔드 호스트와 연결 합니다. 이 지역 필터링 정책은 미국 이외의 다른 국가 또는 지역과 같은 외부 네트워크의 요청을 차단 합니다.

- [자습서-Front 문에 대 한 지역 필터링 WAF 정책을 설정 하는 방법](front-door-tutorial-geo-filtering.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: 네트워크 보안 규칙 단순화

**지침**: Virtual Network 서비스 태그를 사용 하 여 Azure Front 도어 제공 리소스에 대해 구성 된 네트워크 보안 그룹에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (AzureFrontDoor, AzureFrontDoor, AzureFrontDoor)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. 

Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

- [서비스 태그 이해 및 사용](../virtual-network/service-tags-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="identity-management"></a>ID 관리

*자세한 내용은 [Azure 보안 벤치 마크: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management)를 참조 하세요.*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: 조건에 따라 Azure 리소스 액세스 제한

**지침**: 기본적으로 Azure Front 도어가 요청이 시작 된 위치에 관계 없이 모든 사용자 요청에 응답 합니다. 고객은 국가 또는 지역에 따라 웹 응용 프로그램에 대 한 액세스를 제한할 수도 있습니다. Azure 전면 도어의 웹 응용 프로그램 방화벽 서비스를 사용 하면 고객이 지정 된 국가나 지역의 액세스를 허용 하거나 차단 하는 사용자 지정 액세스 규칙을 사용 하 여 해당 끝점의 특정 경로에 대 한 정책을 정의할 수 있습니다.

- [자습서-Azure Front 문에 대해 지역 필터링 WAF 정책을 설정 하는 방법](front-door-tutorial-geo-filtering.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="privileged-access"></a>권한 있는 액세스

*자세한 내용은 [Azure 보안 벤치 마크: 권한 있는 액세스](/azure/security/benchmarks/security-controls-v2-privileged-access)를 참조 하세요.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: 업무상 중요 한 시스템에 대 한 관리 액세스 제한

**지침**: Azure 프런트 도어는 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 비즈니스에 중요 한 시스템에 대 한 액세스를 격리 합니다. Azure RBAC를 사용 하 여 구독 및 관리 그룹에 대 한 권한 있는 액세스 권한이 부여 된 계정을 제한 합니다.

Active Directory 도메인 컨트롤러, 보안 도구 및 시스템 관리 도구와 같이 업무상 중요 한 시스템에 대 한 관리 권한이 있는 관리, id 및 보안 시스템에 대 한 제한 된 액세스를 보장 합니다. 연속 및 일관적인 구현을 위해 모든 종류의 액세스 제어를 기업의 조각화 전략에 맞춥니다.

- [Azure 구성 요소 및 참조 모델](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [관리 그룹 액세스](../governance/management-groups/overview.md#management-group-access) 

- [Azure 구독 관리자](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: 권한 있는 액세스 워크스테이션 사용

**지침**: 보안이 유지 되 고 격리 된 워크스테이션은 관리자, 개발자 및 중요 서비스 운영자와 같은 중요 한 역할의 보안에 매우 중요 합니다. 

관리 작업에 대해 Azure 방호에서 매우 안전한 사용자 워크스테이션을 사용 합니다. Azure Active Directory (Azure AD), Microsoft Defender ATP (Advanced Threat Protection) 및 Microsoft Intune를 선택 하 여 관리 작업을 위한 안전 하 고 관리 되는 사용자 워크스테이션을 배포 합니다. 강력한 인증, 소프트웨어 및 하드웨어 기준, 제한 된 논리적 및 네트워크 액세스를 포함 하 여 보안 구성을 적용 하려면 보안 워크스테이션을 중앙에서 관리 해야 합니다.

- [권한 있는 액세스 워크스테이션 이해](../active-directory/devices/concept-azure-managed-workstation.md) 

- [권한 있는 액세스 워크스테이션 배포](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: 충분 한 관리 수행 (최소 권한 원칙) 

**지침**: Azure 프런트 도어는 리소스를 관리 하기 위해 azure 역할 기반 액세스 제어 (azure RBAC)와 통합 됩니다. Azure RBAC를 사용 하면 역할 할당을 통해 Azure 리소스 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 사용자 및 관리 되는 id에 할당할 수 있습니다. 특정 리소스에 대 한 미리 정의 된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal와 같은 도구를 통해 인벤토리 또는 쿼리할 수 있습니다. 

Azure RBAC를 사용 하 여 리소스에 할당 된 역할 기반 사용 권한에 대 한 최소 권한 모델을 따르고 비즈니스 요구를 기반으로 하는지 확인 합니다. 이는 Azure AD Privileged Identity Management (PIM)의 JIT (just-in-time) 접근 방식을 보완 하므로 정기적으로 검토 해야 합니다.

기본 제공 역할을 사용 하 여 사용 권한을 할당 하 고 비즈니스 요구 사항에 따라 사용자 지정 역할만 만듭니다.

- [Azure 역할 기반 access control (Azure RBAC) 이란?](../role-based-access-control/overview.md) 

- [Azure에서 RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD id 및 액세스 검토를 사용 하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](/azure/security/benchmarks/security-controls-v2-data-protection)를 참조 하세요.*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: 전송 중인 중요 한 정보 암호화

**지침**: 액세스 제어를 보완 하기 위해 전송 중인 데이터를 암호화를 사용 하 여 ' 대역 외 ' 공격 (예: 트래픽 캡처) 으로부터 보호 하 여 공격자가 데이터를 쉽게 읽거나 수정할 수 없도록 해야 합니다.

전면 도어는 TLS 버전 1.0, 1.1 및 1.2을 지원 합니다. TLS 1.3은 아직 지원 되지 않습니다. 9 월 2019 일 이후에 만들어진 모든 전방 도어 프로필은 TLS 1.2를 기본 최소값으로 사용 합니다.

개인 네트워크의 트래픽에 대 한 옵션은 선택 사항 이지만 외부 및 공용 네트워크의 트래픽에 매우 중요 합니다. HTTP 트래픽의 경우 Azure 리소스에 연결 하는 모든 클라이언트에서 TLS v 1.2 이상을 협상할 수 있는지 확인 합니다. 원격 관리의 경우 암호화 되지 않은 프로토콜 대신 SSH (Linux) 또는 RDP/TLS (Windows 용)를 사용 합니다. 사용 되지 않는 SSL, TLS, SSH 버전 및 프로토콜, 약한 암호를 사용 하지 않도록 설정 해야 합니다.

기본적으로 Azure는 Azure 데이터 센터 간에 전송 중인 데이터에 대 한 암호화를 제공 합니다.

- [자습서-Front 도어 사용자 지정 도메인에서 HTTPS를 구성 하는 방법](front-door-custom-domain-https.md)

- [Azure를 사용 하 여 전송 중인 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [TLS 보안에 대 한 정보](/security/engineering/solving-tls1-problem) 

- [전송 중인 Azure 데이터에 대 한 이중 암호화](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

## <a name="asset-management"></a>자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 자산 관리](/azure/security/benchmarks/security-controls-v2-asset-management)를 참조 하세요.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>오전-1: 보안 팀이 자산에 대 한 위험을 볼 권한이 있는지 확인

**지침**: 보안 팀에 Azure Security Center를 사용 하 여 보안 위험을 모니터링 하기 위해 Azure 테 넌 트 및 구독에 대 한 보안 판독기 권한이 부여 되었는지 확인 합니다. 

보안 팀의 책임이 구성 된 방식에 따라 보안 위험에 대 한 모니터링은 중앙 보안 팀 또는 로컬 팀의 책임입니다. 그러나 보안 정보 및 위험은 항상 조직 내에서 중앙에서 집계 되어야 합니다. 

보안 읽기 권한자 권한은 전체 테 넌 트 (루트 관리 그룹)에 광범위 하 게 적용 하거나 관리 그룹 또는 특정 구독으로 범위를 지정할 수 있습니다. 

참고: 작업 및 서비스에 대 한 가시성을 위해 추가 사용 권한이 필요할 수 있습니다. 

- [보안 읽기 권한자 역할 개요](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 관리 그룹 개요](../governance/management-groups/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>오전-2: 보안 팀에 asset inventory and metadata에 대 한 액세스 권한이 있는지 확인

**지침**: Azure 리소스, 리소스 그룹 및 구독에 태그를 적용 하 여 논리적으로 분류로 구성 합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 및 "프로덕션" 값을 적용할 수 있습니다.

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center asset inventory management](../security-center/asset-inventory.md) 

- [리소스 명명 및 태그 지정 의사 결정 가이드](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>오전 3: 승인 된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 사용자 환경에서 사용자가 프로 비전 할 수 있는 서비스를 감사 하 고 제한할 수 있습니다. Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하고 검색 합니다.

Azure Monitor를 사용 하 여 승인 되지 않은 서비스가 검색 될 때 경고를 트리거하는 규칙을 만들 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/built-in-policies.md#general) 

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: 자산 수명 주기 관리의 보안 보장

**지침**: 높은 영향을 받는 Azure Front 도어 자산의 특성 및 네트워크 구성을 유지 관리 하는 것은 고객의 책임입니다.

특성 및 네트워크 구성 변경 사항을 캡처하고 변경 영향을 측정 하 고 해당 하는 수정 작업을 만드는 프로세스를 만드는 것이 좋습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-threat-detection"></a>로깅 및 위협 검색

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 위협 감지](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)를 참조 하세요.*

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure 네트워크 작업에 대 한 로깅 사용

**지침**: Azure 프런트 도어는 가상 네트워크에 배포 하기 위한 것이 아닙니다. 이 고객은 네트워크 보안 그룹 흐름 로깅을 사용 하도록 설정 하거나 방화벽을 통해 트래픽을 라우팅하거나 패킷 캡처를 수행할 수 없습니다.

Azure 전면 도어는 고객 액세스를 위해 처리 하는 모든 네트워크 트래픽을 기록 합니다. 네트워크 흐름 로그 기능을 사용 하도록 설정 하 고 장기 보존 및 감사를 위해 이러한 로그를 저장소 계정에 보내도록 구성 합니다.

- [자습서-Azure Front 도어에서 모니터링 메트릭 및 로그를 설정 하는 방법](front-door-diagnostics.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure 리소스에 대 한 로깅 사용

**참고**: 자동으로 사용할 수 있는 활동 로그에는 읽기 작업 (GET)을 제외 하 고 Azure Front 도어 리소스에 대 한 모든 쓰기 작업 (PUT, POST, DELETE)이 포함 됩니다. 활동 로그를 사용 하 여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

Azure Front 도어에 대해 Azure 리소스 로그를 사용 하도록 설정 합니다. Azure Security Center 및 Azure Policy를 사용 하 여 리소스 로그 및 로그 데이터 수집을 사용 하도록 설정할 수 있습니다. 이러한 로그는 나중에 보안 인시던트를 조사 하 고 법정 연습을 수행 하는 데 중요할 수 있습니다.

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure의 로깅 및 다른 로그 유형 이해](../azure-monitor/platform/platform-logs-overview.md) 

- [Azure Security Center 데이터 수집 이해](../security-center/security-center-enable-data-collection.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](/azure/security/benchmarks/security-controls-v2-incident-response)을 참조 하세요.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 – Azure에 대 한 인시던트 응답 프로세스 업데이트

**지침**: 조직에서 보안 인시던트에 대응 하는 프로세스를 정의 했는지 확인 합니다. Azure 리소스에 대해 이러한 프로세스를 업데이트 된 상태로 유지 하 고 정기적으로 테스트 하 여 준비 상태를 확인 합니다.

- [엔터프라이즈 환경에서 보안 구현](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [인시던트 대응 참조 가이드](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 준비-설치 인시던트 알림

**지침**: Azure Security Center에서 보안 인시던트 연락처 정보를 설정 합니다. 이 연락처 정보는 microsoft 보안 대응 센터 (MSRC)가 불법적인 또는 권한이 없는 당사자가 데이터에 액세스 한 것을 발견 한 경우 사용자에 게 연락 하는 데 사용 됩니다. 또한 인시던트 대응 요구 사항에 따라 다양 한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정 하는 옵션도 있습니다. 

- [Azure Security Center 보안 연락처를 설정 하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 검색 및 분석 – 고품질 경고를 기반으로 인시던트 만들기

**지침**: 높은 품질의 경고를 만들고 경고 품질을 측정 하는 프로세스가 있는지 확인 합니다. 이를 통해 이전 인시던트에 대 한 교훈을 파악 하 고 분석가에 대 한 경고의 우선 순위를 지정할 수 있으므로 가양성을 낭비 하지 않습니다. 

높은 품질의 경고는 다양 한 신호 원본을 융합 하 고 상관 관계를 설정 하 여 경고를 생성 하 고 정리 하도록 설계 된 과거 인시던트, 유효성 검사 된 커뮤니티 원본 및 도구의 환경을 기반으로 작성할 수 있습니다. 

Azure Security Center는 많은 Azure 자산에서 고품질의 경고를 제공 합니다. ASC 데이터 커넥터를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다. Azure 센티널을 사용 하면 조사를 위해 인시던트를 자동으로 생성 하는 고급 경고 규칙을 만들 수 있습니다. 

내보내기 기능을 사용 하 여 Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되는 Azure Security Center 경고 및 권장 사항을 내보냅니다. 경고 및 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보냅니다.

- [내보내기를 구성 하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 검색 및 분석 – 인시던트 조사

**지침**: 분석가는 잠재적 인시던트를 조사 하 여 발생 하는 상황에 대 한 전체 보기를 작성할 수 있도록 다양 한 데이터 원본을 쿼리하고 사용할 수 있습니다. 모든 블라인드 지점을 방지 하려면 다양 한 로그 유형을 수집 하 여 kill 체인에서 잠재적 공격자의 활동을 추적 해야 합니다.  다른 분석가 및 향후 기록 참조에 대 한 정보 및 학습이 캡처되고 있는지 확인 합니다.  

조사를 위한 데이터 원본에는 범위 내 서비스 및 실행 중인 시스템에서 이미 수집 되는 중앙 집중식 로깅 원본이 포함 되지만 다음이 포함 될 수도 있습니다.

- 네트워크 데이터 – 네트워크 보안 그룹의 흐름 로그, Azure Network Watcher 및 Azure Monitor를 사용 하 여 네트워크 흐름 로그 및 기타 분석 정보를 캡처합니다. 

- 실행 중인 시스템의 스냅숏: 

    - Azure 가상 머신의 스냅숏 기능을 사용 하 여 실행 중인 시스템의 디스크에 대 한 스냅숏을 만듭니다. 

    - 운영 체제의 기본 메모리 덤프 기능을 사용 하 여 실행 중인 시스템의 메모리에 대 한 스냅숏을 만듭니다.

    - Azure 서비스 또는 소프트웨어의 고유한 기능에 대 한 스냅숏 기능을 사용 하 여 실행 중인 시스템의 스냅숏을 만듭니다.

Azure 센티널은 거의 모든 로그 원본 및 사례 관리 포털에서 광범위 한 데이터 분석을 제공 하 여 사고의 전체 수명 주기를 관리 합니다. 조사 중에 인텔리전스 정보는 추적 및 보고를 위해 인시던트에 연결할 수 있습니다. 

- [Windows 컴퓨터의 디스크 스냅숏 만들기](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 컴퓨터의 디스크 스냅숏 만들기](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 지원 진단 정보 및 메모리 덤프 수집](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure 센티널을 사용 하 여 인시던트 조사](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 검색 및 분석 – 인시던트 우선 순위 지정

**지침**: 경고 심각도 및 자산 민감도에 따라 첫 번째 인시던트에 집중할 분석가에 게 컨텍스트를 제공 합니다. 

Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 경고를 실행 하는 데 사용 되는 찾기 또는 분석의 Security Center 얼마나 확실 한지에 따라 달라 지 며, 경고를 발생 시킨 활동의 악의적인 의도를가지고 있는 신뢰도 수준을 기준으로 합니다.

또한 태그를 사용 하 여 리소스를 표시 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 식별 하 고 범주화 하는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags).

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 포함, eradication 및 복구-인시던트 처리를 자동화 합니다.

**지침**: 수동 반복적인 작업을 자동화 하 여 응답 시간을 단축 하 고 분석가의 부담을 줄입니다. 수동 작업은 실행 하는 데 더 오랜 시간이 걸리고 각 인시던트를 느려지고 분석가가 처리할 수 있는 인시던트 수를 줄입니다. 또한 수동 작업을 통해 분석가 피로 증가 하 여 지연을 유발 하는 인간 오류의 위험을 높이고 분석가가 복잡 한 작업에 효과적으로 집중할 수 있는 기능을 저하 시킬 수 있습니다. Azure Security Center 및 Azure 센티널의 워크플로 자동화 기능을 사용 하 여 자동으로 작업을 트리거하거나 들어오는 보안 경고에 응답 하는 플레이 북를 실행 합니다. 플레이 북는 알림 보내기, 계정 사용 안 함, 문제가 있는 네트워크 격리 등의 작업을 수행 합니다. 

- [Security Center에서 워크플로 자동화 구성](../security-center/workflow-automation.md)

- [Azure Security Center에서 자동화 된 위협 응답 설정](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="posture-and-vulnerability-management"></a>상황 및 취약성 관리

*자세한 내용은 [Azure 보안 벤치 마크: 상태 및 취약성 관리](/azure/security/benchmarks/security-controls-v2-vulnerability-management)를 참조 하세요.*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV 3: 계산 리소스에 대 한 보안 구성 설정

**지침**: Azure Security Center 및 Azure Policy를 사용 하 여 Virtual Machines, 컨테이너 등을 비롯 한 모든 계산 리소스에 대 한 보안 구성을 설정 합니다.

- [Azure Security Center 권장 사항을 모니터링 하는 방법](../security-center/security-center-recommendations.md) 

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: 소프트웨어 취약성을 신속 하 고 자동으로 해결

**지침**: 소프트웨어 업데이트를 신속 하 게 배포 하 여 운영 체제 및 응용 프로그램의 소프트웨어 취약점을 해결 합니다.

우선 순위는 일반적인 위험 점수 매기기 프로그램 (예: 일반적인 취약성 점수 매기기 시스템) 또는 사용 중인 타사 검사 도구에서 제공 하는 기본 위험 등급을 사용 합니다. 그리고 응용 프로그램에 높은 수준의 보안 위험을 제공 하는 컨텍스트를 사용 하 여 환경에 맞게 조정 하 고 높은 가동 시간이 필요한 응용 프로그램을 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV 8: 일반적인 공격 시뮬레이션 수행

**지침**: 필요에 따라 Azure 리소스에 대 한 침투 테스트 또는 레드 팀 활동을 수행 하 고 모든 중요 한 보안 결과를 수정 합니다.
참여의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.

- [Azure의 침투 테스트](../security/fundamentals/pen-testing.md)

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="governance-and-strategy"></a>거버넌스 및 전략

*자세한 내용은 [Azure 보안 벤치 마크: 거 버 넌 스 및 전략](/azure/security/benchmarks/security-controls-v2-governance-strategy)을 참조 하세요.*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의 

**지침**: 시스템 및 데이터의 지속적인 모니터링과 보호를 위한 명확한 전략을 문서화 하 고 전달 해야 합니다. 비즈니스에 중요 한 데이터 및 시스템의 검색, 평가, 보호 및 모니터링에 우선 순위를 지정 합니다. 

이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

-   비즈니스 위험에 따라 데이터 분류 표준

-   위험 및 자산 인벤토리에 대 한 보안 조직 가시성 

-   사용할 Azure 서비스의 보안 조직 승인 

-   수명 주기를 통한 자산의 보안

-   조직 데이터 분류에 따라 필요한 액세스 제어 전략

-   Azure 기본 및 타사 데이터 보호 기능 사용

-   전송 중 및 미사용 사용 사례에 대 한 데이터 암호화 요구 사항

-   적절 한 암호화 표준

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [Azure 보안 아키텍처 권장 사항-저장소, 데이터 및 암호화](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 보안 기본 사항-Azure 데이터 보안, 암호화 및 저장소](../security/fundamentals/encryption-overview.md)

- [Azure 보안 벤치 마크-데이터 보호](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 조각화 전략 정의 

**지침**: id, 네트워크, 응용 프로그램, 구독, 관리 그룹 및 기타 컨트롤의 조합을 사용 하 여 자산에 대 한 액세스를 분할 하는 엔터프라이즈 수준의 전략을 수립 합니다.

서로 통신 해야 하는 시스템의 일상 작업을 수행 하 고 데이터에 액세스 해야 하는 경우에는 보안 분리의 필요성을 신중 하 게 조정 해야 합니다.

조각화 전략은 네트워크 보안, id 및 액세스 모델, 응용 프로그램 권한/액세스 모델 및 사용자 프로세스 컨트롤을 비롯 하 여 컨트롤 형식 간에 일관 되 게 구현 되는지 확인 합니다.

- [Azure의 조각화 전략에 대 한 지침 (비디오)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 조각화 전략에 대 한 지침 (문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [기업 구분 전략을 사용 하 여 네트워크 조각화 맞추기](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 상태 관리 전략 정의

**지침**: 개별 자산 및 사용자가 호스트 하는 환경에 대 한 위험을 지속적으로 측정 하 고 완화 합니다. 게시 된 응용 프로그램, 네트워크 수신 및 송신 위치, 사용자 및 관리자 끝점과 같은 높은 가치 자산 및 높은 수준의 공격 노출 영역에 우선 순위를 지정 합니다.

- [Azure 보안 벤치 마크-상태 및 취약성 관리](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임 및 accountabilities 정렬

**지침**: 보안 조직의 역할 및 책임에 대 한 명확한 전략을 문서화 하 고 전달 하는지 확인 합니다. 보안에 대 한 책임을 명확 하 게 제공 하 고, 공유 책임 모델에 대 한 모든 것을 교육 하 고, 클라우드를 보호 하는 기술 팀을 교육 합니다.

- [Azure 보안 모범 사례 1 – 사람: 클라우드 보안 경험에 대 한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 보안 모범 사례 2-사람: 클라우드 보안 기술에 대 한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 보안 모범 사례 3-프로세스: 클라우드 보안 결정에 대 한 책임 할당](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

**지침**: 조직의 전체 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정 합니다.  

이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

-   중앙 집중식 네트워크 관리 및 보안 책임

-   엔터프라이즈 조각화 전략에 맞춘 가상 네트워크 조각화 모델

-   다양 한 위협 및 공격 시나리오에서 수정 전략

-   인터넷에 지 및 수신 및 송신 전략

-   하이브리드 클라우드 및 온-프레미스 상호 연결과 전략

-   최신 네트워크 보안 아티팩트 (예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [Azure 보안 모범 사례 11-아키텍처. 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 보안 벤치 마크-네트워크 보안](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure 네트워크 보안 개요](../security/fundamentals/network-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: id 및 권한 있는 액세스 전략 정의

**지침**: 조직의 전체 보안 액세스 제어 전략의 일부로 Azure id 및 권한 있는 액세스 접근 방법을 설정 합니다.  

이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

-   다른 내부 및 외부 id 시스템을 사용 하는 중앙 집중식 id와 인증 시스템 및 상호 연결과

-   다양 한 사용 사례 및 조건에서 강력한 인증 방법

-   높은 권한이 있는 사용자의 보호

-   변칙 사용자 활동 모니터링 및 처리  

-   사용자 id 및 액세스 검토 및 조정 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure 보안 벤치 마크-Id 관리](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure 보안 벤치 마크-권한 있는 액세스](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure 보안 모범 사례 11-아키텍처. 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 관리 보안 개요](../security/fundamentals/identity-management-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 응답 전략 정의

**지침**: 규정 준수 요구 사항을 충족 하면서 신속 하 게 위협을 감지 하 고 수정할 수 있는 로깅 및 위협 대응 전략을 수립 합니다. 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 고품질 경고와 원활한 환경을 제공 하는 분석가의 우선 순위를 지정 합니다. 

이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

-   보안 작업 (SecOps) 조직의 역할 및 책임 

-   NIST 또는 다른 업계 프레임 워크를 사용 하 여 잘 정의 된 인시던트 응답 프로세스 

-   위협 감지, 인시던트 대응 및 규정 준수 요구 사항을 지원 하기 위한 로그 캡처 및 보존

-   SIEM, 네이티브 Azure 기능 및 기타 소스를 사용 하 여 위협에 대 한 중앙 집중식 및 상관 관계 정보 표시 

-   고객, 공급자 및 공공 당사자와의 통신 및 알림 계획

-   로깅 및 위협 검색, 관련자, 공격 수정 및 eradication 같은 인시던트 처리를 위해 Azure native 및 타사 플랫폼 사용

-   학습 및 증거 보관과 같은 인시던트 및 인시던트 후 활동을 처리 하는 프로세스

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [Azure 보안 벤치 마크-로깅 및 위협 검색](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure 보안 벤치 마크-인시던트 응답](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure 보안 모범 사례 4-프로세스. 클라우드에 대 한 인시던트 응답 프로세스 업데이트](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 채택 프레임 워크, 로깅 및 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure Security 벤치 마크 V2 개요](/azure/security/benchmarks/overview) 를 참조 하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
