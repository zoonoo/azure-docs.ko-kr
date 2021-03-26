---
title: Azure Cosmos DB에 대 한 Azure 보안 기준
description: Azure Cosmos DB 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: dead43f2e9f2e8913bcebde43d543b8df8d33ced
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565676"
---
# <a name="azure-security-baseline-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 Azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 1.0](../security/benchmarks/overview-v1.md) 의 지침을 Azure Cosmos DB 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Azure Cosmos DB에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure Cosmos DB에 적용할 수 없는 **컨트롤** 은 제외 되었습니다.

 
Azure Cosmos DB 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Azure Cosmos DB 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Azure 개인 링크를 사용 하 여 개인 끝점을 통해 azure Cosmos 계정에 연결할 수 있습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. 또한 nsg (네트워크 보안 그룹)에서 엄격한 아웃 바운드 규칙 집합을 구성 하 고 해당 nsg를 서브넷에 연결 하 여 데이터 반출의 위험을 줄일 수 있습니다.

서비스 끝점을 사용 하 여 Azure Cosmos 계정을 보호할 수도 있습니다. 서비스 끝점을 사용 하도록 설정 하면 azure 가상 네트워크의 특정 서브넷 에서만 액세스할 수 있도록 Azure Cosmos 계정을 구성할 수 있습니다. Azure Cosmos DB 서비스 끝점을 사용 하도록 설정 하면 가상 네트워크에 있는 서브넷의 연결을 사용 하 여 Azure Cosmos 계정에 대 한 액세스를 제한할 수 있습니다.

IP 방화벽을 사용 하 여 Azure Cosmos 계정에 저장 된 데이터를 보호할 수도 있습니다. Azure Cosmos DB는 인바운드 방화벽 지원을 위해 IP 기반 액세스 제어를 지원합니다. Azure Portal, Azure Resource Manager 템플릿 또는 Azure CLI 또는 Azure PowerShell를 사용 하 여 Azure Cosmos 계정에 IP 방화벽을 설정할 수 있습니다.

- [Azure Private Link 개요](../private-link/private-link-overview.md)

- [Azure Cosmos DB에 대 한 개인 끝점을 구성 하는 방법](how-to-configure-private-endpoints.md) 

- [보안 구성을 사용 하 여 네트워크 보안 그룹을 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

- [Cosmos DB에서 IP 방화벽을 구성 하는 방법](how-to-configure-firewall.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure 보안 벤치 마크](/azure/governance/policy/samples/azure-security-benchmark) 는 Security Center에 대 한 기본 정책 이니셔티브 이며 [Security Center 권장 사항의](/azure/security-center/security-center-recommendations)기초가 됩니다. 이 컨트롤과 관련 된 Azure Policy 정의는 Security Center에 의해 자동으로 설정 됩니다. 이 컨트롤과 관련 된 경고에는 관련 서비스에 대 한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의-Microsoft.DocumentDB**:

[!INCLUDE [Resource Policy for Microsoft.DocumentDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.documentdb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure Security Center을 사용 하 고 네트워크 보호 권장 사항을 따라 Azure Cosmos 계정과 관련 된 네트워크 리소스를 보호 합니다.

Azure Cosmos 계정과 동일한 가상 네트워크에 가상 머신을 배포 하는 경우 NSG (네트워크 보안 그룹)를 사용 하 여 데이터 exfiltration의 위험을 줄일 수 있습니다. NSG 흐름 로그를 사용 하도록 설정 하 고 로그를 트래픽 감사에 대 한 Azure Storage 계정으로 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: CORS (원본 간 리소스 공유) 기능을 사용 하 여 한 도메인에서 실행 되는 웹 응용 프로그램이 다른 도메인의 리소스에 액세스할 수 있도록 설정 합니다. 웹 브라우저는 웹 페이지가 다른 도메인의 API를 호출하지 못하게 차단하는 동일 원본 정책이라고 하는 보안 제한을 구현합니다. 그러나 CORS는 원본 도메인이 다른 도메인의 Api를 호출할 수 있는 안전한 방법을 제공 합니다. Azure Cosmos 계정에 CORS 지원을 사용하도록 설정하면 오직 인증된 요청만 평가하여 사용자가 지정한 규칙에 따라 허용 여부가 결정됩니다.

- [원본 간 리소스 공유 구성](how-to-configure-cross-origin-resource-sharing.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Azure Cosmos DB에 대해 ATP (Advanced Threat Protection)를 사용 합니다. Azure Cosmos DB에 대 한 ATP는 Azure Cosmos 계정을 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 이 보호 계층을 사용 하면 위협을 해결 하 고 중앙 보안 모니터링 시스템과 통합할 수 있습니다.

DDoS 공격 으로부터 보호 하기 위해 Azure Cosmos DB 인스턴스와 연결 된 가상 네트워크에서 DDoS Protection 표준을 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

- [Azure Cosmos DB Advanced Threat Protection을 구성 하는 방법](cosmos-db-advanced-threat-protection.md)

- [DDoS 보호를 구성 하는 방법](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center 통합 위협 인텔리전스 이해](../security-center/azure-defender.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: nsg (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 하 고 트래픽 감사를 위해 저장소 계정에 로그를 보냅니다. Log Analytics 작업 영역에 NSG 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: Azure Cosmos DB에 대해 ATP (Advanced Threat Protection)를 사용 합니다. Azure Cosmos DB에 대 한 ATP는 Azure Cosmos 계정을 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 이 보호 계층을 사용 하면 위협을 해결 하 고 중앙 보안 모니터링 시스템과 통합할 수 있습니다. 

- [Cosmos DB Advanced Threat Protection을 구성 하는 방법](cosmos-db-advanced-threat-protection.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: azure Cosmos 계정에 액세스 해야 하는 리소스의 경우 Virtual Network 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: Microsoft.azurecosmosdb)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

- [서비스 태그를 사용 하는 방법에 대 한 자세한 내용](../virtual-network/service-tags-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy을 사용 하 여 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft.DocumentDB" 및 "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 Azure Cosmos DB 인스턴스의 네트워크 구성을 감사 하거나 적용 합니다. 다음과 같이 Azure Cosmos DB에 대 한 기본 제공 정책 정의를 사용할 수도 있습니다.

- Cosmos DB 계정에 대한 Advanced Threat Protection 배포

- Cosmos DB는 가상 네트워크 서비스 엔드포인트를 사용해야 함

또한 Azure 청사진을 사용 하 여 Azure Resource Manager 템플릿, azure RBAC (역할 기반 액세스 제어) 및 단일 청사진 정의의 정책과 같은 주요 환경 아티팩트를 패키지 하 여 대규모 Azure 배포를 간소화할 수 있습니다. 청사진을 새 구독 및 환경에 쉽게 적용하고 버전 관리를 통해 제어와 관리를 세부적으로 조정할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 논리적으로 분류로 구성 하기 위해 Azure Cosmos DB 배포와 연결 된 네트워크 리소스에 대 한 태그를 사용 합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 Azure Cosmos DB 인스턴스와 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다. 

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor을 통해 로그를 수집 하 여 Azure Cosmos DB에서 생성 된 보안 데이터를 집계 합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기/보관 저장소에 저장소 계정을 사용 합니다. 또는 Azure 센티널 또는 타사 SIEM (보안 인시던트 및 이벤트 관리)에 대 한 온보드 데이터를 사용할 수 있습니다. 

- [Azure Cosmos DB에 대 한 진단 로그를 사용 하도록 설정 하는 방법](./monitor-cosmos-db.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Cosmos DB에 대 한 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역 또는 Azure storage 계정으로 로그를 보냅니다. Azure Cosmos DB의 진단 설정은 리소스 로그를 수집 하는 데 사용 됩니다. 이러한 로그는 요청당 캡처되고 **데이터 평면 로그** 라고도 합니다. 데이터 평면 작업의 몇 가지 예로는 delete, insert 및 read가 있습니다. 

또한 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 해당 로그를 Azure Cosmos DB 로그에 사용 하는 것과 동일한 Log Analytics 작업 영역으로 보낼 수 있습니다.

- [Azure Cosmos DB에 대 한 진단 설정을 사용 하도록 설정 하는 방법](./monitor-cosmos-db.md)

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure Cosmos DB 인스턴스와 연결 된 Log Analytics 작업 영역에 대 한 로그 보존 기간을 설정 합니다.

- [로그 보존 매개 변수를 설정 하는 방법](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: 작업 영역 Log Analytics에서 쿼리를 수행 하 여 용어를 검색 하 고, 추세를 식별 하 고, 패턴을 분석 하 고, 수집한 Azure Cosmos DB 로그를 기반으로 많은 기타 정보를 제공할 수 있습니다.

- [Log Analytics 작업 영역에서 Azure Cosmos DB에 대 한 쿼리를 수행 하는 방법](monitor-cosmos-db.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure Security Center에서는 Azure Cosmos DB에 대해 고급 위협 방지를 사용 하 여 보안 로그 및 이벤트에서 비정상적인 활동을 모니터링할 수 있습니다. Azure Cosmos DB에서 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역으로 로그를 보냅니다.

 

또한 Log Analytics 작업 영역을 Azure 센티널에 등록 하 여 보안 오케스트레이션 자동화 된 응답 (대화 충성도) 솔루션을 제공할 수 있습니다. 이를 통해 플레이북(자동화된 솔루션)을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다. 또한 Azure Monitor를 사용 하 여 Log Analytics 작업 영역에서 사용자 지정 로그 경고를 만들 수 있습니다.

- [Azure Cosmos DB에 대 한 위협 방지 경고 목록](../security-center/alerts-reference.md#alerts-azurecosmos)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure 보안 벤치 마크: id 및 Access Control](../security/benchmarks/security-control-identity-access-control.md)을 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: AZURE PORTAL의 IAM (Id 및 액세스 제어) 창을 사용 하 여 RBAC (역할 기반 액세스 제어)를 구성 하 고 Azure Cosmos DB 리소스에 대 한 인벤토리를 유지 관리할 수 있습니다. 역할은 Azure Active Directory (Azure AD)의 사용자, 그룹, 서비스 사용자 및 관리 되는 id에 적용 됩니다. 개인 및 그룹에 대 한 기본 제공 역할 또는 사용자 지정 역할을 사용할 수 있습니다.

Azure Cosmos DB은 Azure Cosmos DB의 일반적인 관리 시나리오에 대 한 Azure RBAC를 기본적으로 제공 합니다. Azure AD에서 프로필을 사용 하는 개인은 이러한 Azure 역할을 사용자, 그룹, 서비스 주체 또는 관리 되는 id에 할당 하 여 Azure Cosmos DB 리소스에 대 한 리소스 및 작업에 대 한 액세스를 부여 하거나 거부할 수 있습니다.

또한 Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색할 수 있습니다.

또한 Azure Cosmos DB의 일부 작업은 Azure AD 및 계정 특정 마스터 키를 사용 하 여 제어할 수 있습니다. ' DisableKeyBasedMetadataWriteAccess ' 계정 설정을 사용 하 여 키 액세스를 제어 합니다.

- [Azure Cosmos DB의 역할 기반 액세스 제어 이해](role-based-access-control.md)

- [Azure Cosmos DB 작업을 사용 하 여 사용자 지정 역할 작성 (Microsoft.DocumentDB 네임 스페이스)](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

- [Azure AD에서 새 역할 만들기](../role-based-access-control/custom-roles.md)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [데이터 작업에 대해서만 사용자 액세스 제한](how-to-restrict-user-data.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: 기본 또는 빈 암호의 개념은 Azure Active Directory (Azure AD) 또는 Azure Cosmos DB와 관련 하 여 존재 하지 않습니다. 대신, Azure Cosmos DB는 두 가지 유형의 키를 사용 하 여 사용자를 인증 하 고 해당 데이터 및 리소스에 대 한 액세스를 제공 합니다. 마스터 키 및 리소스 토큰입니다. 언제 든 지 키를 다시 생성할 수 있습니다.

- [Azure Cosmos DB 데이터에 대 한 보안 액세스 이해](secure-access-to-data.md)

- [Azure Cosmos DB 키를 다시 생성 하는 방법](./manage-with-powershell.md#regenerate-keys)

- [Azure AD를 사용 하 여 프로그래밍 방식으로 키에 액세스 하는 방법](certificate-based-authentication.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 해당 사항 없음 Azure Cosmos DB는 관리자 계정을 지원 하지 않습니다. 모든 액세스는 Azure Active Directory (Azure AD) 및 Azure 역할 기반 액세스 제어 (azure RBAC)와 통합 됩니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: SSO (Azure Active Directory Single Sign-On)를 사용 하십시오.

**지침**: Azure Cosmos DB는 두 가지 유형의 키를 사용 하 여 사용자에 게 권한을 부여 하 고 데이터 평면 수준에서 Sso (Single Sign-On)를 지원 하지 않습니다. Cosmos DB에 대 한 제어 평면에 대 한 액세스는 REST API를 통해 제공 되며 SSO를 지원 합니다. 인증 하려면 요청에 대 한 인증 헤더를 Azure Active Directory (Azure AD)에서 가져오는 JSON Web Token 설정 합니다.

- [Azure Database for Cosmos DB REST API 이해](/rest/api/cosmos-db/)

- [Azure AD를 사용 하 여 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: Azure Active Directory (Azure AD) 다단계 인증을 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 다단계 인증을 사용 하는 PAW (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure 리소스에 로그인 하 고 구성 합니다. 

- [Privileged Access Workstation에 대한 자세한 정보](/security/compass/privileged-access-devices)
 
- [Azure에서 다단계 인증을 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: Azure Cosmos DB에 대해 ATP (Advanced Threat Protection)를 사용 합니다. Azure Cosmos DB에 대 한 ATP는 Azure Cosmos 계정을 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 이 보호 계층을 사용 하면 위협을 해결 하 고 중앙 보안 모니터링 시스템과 통합할 수 있습니다.

또한 환경에서 의심 스러운 작업이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)를 사용할 수 있습니다.

Azure AD 위험 검색을 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

- [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 정책의 위치 조건을 구성 하 고 명명 된 위치를 관리 합니다. 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가 및 지역의 논리적 그룹을 만들 수 있습니다. 구성 된 명명 된 위치에 대 한 Azure Cosmos DB 인스턴스와 같은 중요 한 리소스에 대 한 액세스를 제한할 수 있습니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory-domain-services/tutorial-create-instance.md)

- [Azure SQL을 사용 하 여 Azure AD 인증을 구성 하 고 관리 하는 방법](../azure-sql/database/authentication-aad-configure.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory (Azure AD)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다.

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Azure Active Directory (Azure AD) 사용자 계정에 대 한 진단 설정을 만들고, 원하는 경고를 구성할 수 있는 Log Analytics 작업 영역에 감사 로그 및 로그인 로그를 보낼 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure Cosmos DB에 대해 ATP (Advanced Threat Protection)를 사용 합니다. Azure Cosmos DB에 대 한 ATP는 Azure Cosmos 계정을 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 이 보호 계층을 사용 하면 위협을 해결 하 고 중앙 보안 모니터링 시스템과 통합할 수 있습니다.

또한 Azure Active Directory (Azure AD) Id 보호 및 위험 검색 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성할 수 있습니다. 또한 추가 조사를 위해 Azure 센티널에 로그를 수집할 수 있습니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 Azure Cosmos DB 인스턴스를 추적 하는 데 도움을 줍니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. Azure Cosmos DB 인스턴스는 가상 네트워크/서브넷으로 구분 되며, 적절 하 게 태그가 지정 되 고, NSG (네트워크 보안 그룹) 또는 Azure 방화벽 내에서 보안이 유지 됩니다. 중요 한 데이터를 저장 하는 Azure Cosmos DB 인스턴스는 격리 되어야 합니다. Azure 개인 링크를 사용 하 여 개인 끝점을 통해 Azure Cosmos DB 인스턴스 계정에 연결할 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크 내부에 있는 서브넷의 개인 IP 주소 세트입니다. 그런 다음 선택한 개인 IP 주소에 대 한 액세스를 제한할 수 있습니다. 

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Azure Cosmos DB에 대 한 개인 끝점을 구성 하는 방법](how-to-configure-private-endpoints.md)

- [보안 구성을 사용 하 여 네트워크 보안 그룹을 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: Azure Cosmos DB에 대 한 Advanced Threat Protection을 배포할 수 있습니다 .이를 통해 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다. 현재 다음과 같은 경고를 트리거할 수 있습니다.

- 비정상적인 위치에서 액세스

- 비정상적인 데이터 추출

또한 가상 컴퓨터를 사용 하 여 Azure Cosmos DB 인스턴스에 액세스 하는 경우 개인 링크, 방화벽, 네트워크 보안 그룹 및 서비스 태그를 활용 하 여 데이터 exfiltration의 가능성을 완화 합니다. Microsoft는 Azure Cosmos DB에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

- [Cosmos DB Advanced Threat Protection을 구성 하는 방법](cosmos-db-advanced-threat-protection.md)

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 자동 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Cosmos DB 사용할 수 없습니다. 그러나 분류 및 데이터 분석을 위해 Azure Cognitive Search 통합을 사용할 수 있습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현할 수도 있습니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure Cognitive Search를 사용 하 여 Azure Cosmos DB 데이터 인덱싱](../search/search-howto-index-cosmosdb.md?bc=%2fazure%2fcosmos-db%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcosmos-db%2ftoc.json)

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용 하 여 리소스에 대 한 액세스를 제어 합니다.

**지침**: Azure Cosmos DB에서는 Azure Cosmos DB 일반적인 관리 시나리오에 대 한 azure RBAC (역할 기반 액세스 제어)를 기본 제공 합니다. Azure AD (Azure Active Directory)에 프로필이 있는 개인은 이러한 Azure 역할을 사용자, 그룹, 서비스 주체 또는 관리 id에 할당 하 여 리소스에 대 한 액세스를 부여 하거나 거부 하 고 Azure Cosmos DB 리소스에 대 한 작업에 대 한 액세스를 거부할 수 있습니다. 역할 할당은 제어 평면 액세스 전용으로 범위가 지정 됩니다. 여기에는 Azure Cosmos 계정, 데이터베이스, 컨테이너 및 제안 (처리량)에 대 한 액세스가 포함 됩니다.

- [Azure Cosmos DB에서 Azure RBAC를 구현 하는 방법](role-based-access-control.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Cosmos DB에 저장 된 모든 사용자 데이터는 기본적으로 미사용 암호화 됩니다. 해제할 컨트롤이 없습니다. Azure Cosmos DB는 계정이 실행되는 모든 지역에서 AES-256 암호화를 사용합니다.

기본적으로 Microsoft는 Azure Cosmos 계정에서 데이터를 암호화 하는 데 사용 되는 키를 관리 합니다. 필요에 따라 사용자 고유의 키를 사용 하 여 두 번째 암호화 계층을 추가 하도록 선택할 수 있습니다.

- [Azure Cosmos DB를 사용 하 여 미사용 암호화 이해](database-encryption-at-rest.md)

- [Azure Cosmos DB를 사용 하 여 미사용 암호화를 위한 키 관리 이해]()

- [Azure Cosmos DB 계정에 대해 고객이 관리 하는 키를 구성 하는 방법](how-to-setup-cmk.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 Azure Cosmos DB의 프로덕션 인스턴스로 변경 될 경우에 대 한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조 하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Azure Cosmos DB 인스턴스에 대 한 Azure Security Center의 권장 사항을 따릅니다. 

Microsoft는 Azure Cosmos DB 인스턴스를 지 원하는 기본 호스트에서 시스템 패치 및 취약성 관리를 수행 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure Security Center에서 사용할 수 있는 지원되는 기능](../security-center/security-center-services.md?tabs=features-windows)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure Portal 또는 Azure 리소스 그래프를 사용 하 여 구독 내에서 모든 리소스 (Azure Cosmos DB으로 제한 되지 않고 계산, 다른 저장소, 네트워크, 포트, 프로토콜 등의 리소스 등)를 검색 합니다. 테 넌 트에서 적절 한 권한이 있는지 확인 하 고 구독 내의 리소스 뿐만 아니라 모든 Azure 구독을 열거할 수 있습니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Azure 역할 기반 액세스 제어 이해](../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타 데이터를 사용 하 여 Azure Cosmos DB 인스턴스 및 관련 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [태그를 지 원하는 Azure Cosmos DB 리소스](../azure-resource-manager/management/tag-support.md#microsoftdocumentdb)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당 하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 Azure Cosmos DB 리소스를 비롯 하 여 자산을 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하고 검색 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/built-in-policies.md#general)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하려면 Azure 조건부 액세스를 사용합니다. 이렇게 하면 보안 수준이 높은 환경에서 리소스를 만들고 변경 하지 못할 수 있습니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용 하 여 Cosmos DB 인스턴스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. **Microsoft.DocumentDB** 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Cosmos DB 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 다음과 같이 Azure Cosmos DB에 대 한 기본 제공 정책 정의를 사용할 수도 있습니다.
- Cosmos DB 계정에 대한 Advanced Threat Protection 배포
- Cosmos DB는 가상 네트워크 서비스 엔드포인트를 사용해야 함

- [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Cosmos DB 또는 관련 리소스에 대 한 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure Repos를 사용 하 여 사용자 지정 정책 정의를 코드로 저장 하 고 관리 합니다.

- [코드 워크플로서의 디자인 정책](../governance/policy/concepts/policy-as-code.md)

- [Azure Repos 설명서](/azure/devops/repos/git/gitworkflow)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: "Microsoft.DocumentDB" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: "Microsoft.DocumentDB" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. Azure Cosmos DB 인스턴스 및 관련 리소스에 대 한 구성을 자동으로 적용 하려면 Azure Policy [감사], [거부] 및 [배포 되지 않은 경우 배포]를 사용 합니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Cosmos DB 인스턴스에 액세스 하는 데 사용 되는 Azure App Service에서 실행 되는 Azure 가상 머신 또는 웹 응용 프로그램의 경우 Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 Azure Cosmos DB 비밀 관리를 간소화 하 고 보호 합니다. Key Vault 일시 삭제를 사용하도록 설정되어 있는지 확인합니다.

- [Azure 관리 Id와 통합 하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault를 만드는 방법](../key-vault/secrets/quick-create-portal.md)

- [Key Vault에 인증 하는 방법](../key-vault/general/authentication.md)

- [Key Vault 액세스 정책을 할당 하는 방법](../key-vault/general/assign-access-policy-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Azure Cosmos DB 인스턴스에 액세스 하는 데 사용 되는 Azure App Service에서 실행 되는 Azure 가상 머신 또는 웹 응용 프로그램의 경우 Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 Azure Cosmos DB 비밀 관리를 간소화 하 고 보호 합니다.

관리 되는 id를 사용 하 여 azure AD (Azure Active Directory)에서 자동으로 관리 되는 id를 Azure 서비스에 제공 합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [관리 Id를 구성 하는 방법](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Azure 관리 Id와 통합 하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정 하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조 하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 azure 서비스 (예: azure CosmosDB)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

Azure Cosmos DB를 포함 하 여 비 계산 Azure 리소스에 업로드 되는 파일을 미리 검색 하는 것은 사용자의 책임입니다. Microsoft는 고객 데이터에 액세스할 수 없으므로 사용자를 대신해 서 고객 콘텐츠의 맬웨어 방지 검색을 수행할 수 없습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Azure Cosmos DB 정기적으로 데이터의 백업을 자동으로 수행 합니다. 데이터베이스 또는 컨테이너를 삭제 한 경우 지원 티켓을 제출 하거나 Azure 지원에 문의 하 여 자동 온라인 백업에서 데이터를 복원할 수 있습니다. Azure 지원은 표준, 개발자 및 그 보다 높은 요금제와 같은 선택 된 계획에만 사용할 수 있습니다. 백업의 특정 스냅샷을 복원하려면 Azure Cosmos DB에서 해당 스냅샷의 백업 주기 동안 데이터를 사용할 수 있어야 합니다. 

Key Vault를 사용 하 여 Cosmos DB 인스턴스의 자격 증명을 저장 하는 경우 키의 자동 백업을 정기적으로 수행 해야 합니다.

- [자동화 된 백업 Azure Cosmos DB 이해](online-backup-and-restore.md)

- [Azure Cosmos DB에서 데이터를 복원 하는 방법](./online-backup-and-restore.md)

- [Key Vault 키를 백업 하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 데이터베이스 또는 컨테이너를 삭제 한 경우 지원 티켓을 제출 하거나 Azure 지원에 문의 하 여 자동 온라인 백업에서 데이터를 복원할 수 있습니다. Azure 지원은 표준, 개발자 및 그 보다 높은 요금제와 같은 선택 된 계획에만 사용할 수 있습니다. 백업의 특정 스냅샷을 복원하려면 Azure Cosmos DB에서 해당 스냅샷의 백업 주기 동안 데이터를 사용할 수 있어야 합니다.

PowerShell을 사용 하 여 Azure Key Vault에 저장 된 암호의 복원을 테스트 합니다. Restore-AzureKeyVaultKey cmdlet은 지정 된 키 자격 증명 모음에 키를 만듭니다. 이 키는 입력 파일에 있는 백업 키의 복제본 이며 원래 키와 동일한 이름을 갖습니다.

- [자동화 된 백업 Azure Cosmos DB 이해](online-backup-and-restore.md)

- [Azure Cosmos DB에서 데이터를 복원 하는 방법](./online-backup-and-restore.md)

- [Azure Key Vault 비밀을 복원 하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Cosmos DB에 저장 된 모든 사용자 데이터는 미사용 및 전송 시 암호화 되므로 어떤 조치도 취할 필요가 없습니다. 또한 미사용 암호화가 기본적으로 "설정" 상태라는 것도 이러한 노력 중 하나입니다. 설정하거나 해제하는 데 사용되는 컨트롤이 없습니다. Azure Cosmos DB는 계정이 실행되는 모든 지역에서 AES-256 암호화를 사용합니다.

Key Vault에서 일시 삭제를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다.

- [Azure Cosmos DB의 데이터 암호화 이해](database-encryption-at-rest.md)

- [Key Vault에서 일시 삭제를 사용하도록 설정하는 방법](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

- [또한 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만들 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md)

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 경고를 실행 하는 데 사용 되는 Security Center 또는 경고를 실행 하는 데 사용 되는 분석 및 경고를 발생 시키는 활동의 악의적인 의도를 받은 신뢰 수준에 따라 달라 집니다.

또한 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하는 연습을 수행 합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다.  문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Engagement의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
