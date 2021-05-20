---
title: Synapse Analytics에 대한 Azure 보안 기준
description: Synapse Analytics 보안 기준은 Azure Security Benchmark에 지정된 보안 추천 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1a426a86ddb25733b8425c5887d2c0522f915e2d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587717"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Synapse Analytics에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 지침을 Synapse Analytics에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure Security Benchmark에서 정의한 **보안 컨트롤** 및 Synapse Analytics에 적용되는 관련 지침에 따라 그룹화됩니다. Synapse Analytics에 적용할 수 없는 **컨트롤** 은 제외되었습니다.

 
Synapse Analytics가 Azure Security Benchmark에 완전히 매핑되는 방식을 확인하려면 [전체 Synapse Analytics 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Private Link를 통해 Azure SQL Server를 가상 네트워크에 보호합니다. Azure Private Link를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure PaaS 서비스에 액세스할 수 있습니다. 가상 네트워크와 서비스 사이의 트래픽은 Microsoft 백본 네트워크를 통해 이동합니다.

또는 Synapse SQL 풀에 연결할 때 네트워크 보안 그룹을 사용하여 SQL 데이터베이스로 나가는 연결의 범위를 좁힙니다. [Azure 서비스 허용]을 끄기로 설정하여 퍼블릭 엔드포인트를 통해 SQL Database로 향하는 모든 Azure 서비스 트래픽을 사용하지 않도록 설정합니다. 방화벽 규칙에는 공용 IP 주소가 허용되지 않아야 합니다.

- [Azure Private Link 이해](../private-link/private-link-overview.md)

- [Azure Synapse SQL에 대한 Private Link 이해](../azure-sql/database/private-endpoint-overview.md)

- [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 및 기록

**지침**: 전용 SQL 풀에 연결하고 NSG(네트워크 보안 그룹) 흐름 로그를 사용하도록 설정하면 트래픽 감사를 위해 로그가 Azure Storage 계정으로 전송됩니다.

또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Security Center에서 제공하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

- [Azure Security Center에서 제공하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Azure Synapse SQL에 대한 ATP(Advanced Threat Protection)를 사용합니다. ATP는 데이터베이스를 악용하거나 액세스하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 감지하고 "잠재적인 SQL 삽입" 및 "비정상적인 위치에서 액세스"와 같은 다양한 경고를 트리거할 수 있습니다. ATP는 ADS(Advanced Data Security) 제품의 일부이며 중앙 SQL ADS 포털을 통해 액세스하고 관리할 수 있습니다.

분산 서비스 거부 공격으로부터 보호하기 위해 Azure Synapse SQL과 연결된 가상 네트워크에서 DDoS Protection 표준을 사용하도록 설정합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

- [Azure Synapse SQL에 대한 ATP 이해](../azure-sql/database/threat-detection-overview.md)

- [Azure SQL Database에 대한 Advanced Data Security를 사용하도록 설정하는 방법](../azure-sql/database/azure-defender-for-sql.md)

- [ADS 개요](../azure-sql/database/azure-defender-for-sql.md)

- [DDoS 보호를 구성하는 방법](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center 통합 위협 인텔리전스 이해](../security-center/azure-defender.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: 전용 SQL 풀에 연결하고 NSG(네트워크 보안 그룹) 흐름 로그를 사용하도록 설정한 경우, 트래픽 감사를 위해 로그를 Azure Storage 계정으로 보냅니다. 흐름 로그를 Log Analytics 작업 영역으로 보내거나 Event Hubs로 스트리밍할 수도 있습니다.  비정상 활동을 조사하는 데 필요한 경우 Network Watcher 패킷 캡처를 사용하도록 설정합니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: Azure Synapse SQL에 대한 ATP(Advanced Threat Protection)를 사용합니다. ATP는 데이터베이스를 악용하거나 액세스하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 감지하고 "잠재적인 SQL 삽입" 및 "비정상적인 위치에서 액세스"와 같은 다양한 경고를 트리거할 수 있습니다. ATP는 ADS(Advanced Data Security) 제품의 일부이며 중앙 SQL ADS 포털을 통해 액세스하고 관리할 수 있습니다. 또한 ATP는 경고를 Azure Security Center와 통합합니다.

- [Azure Synapse SQL에 대한 ATP 이해](../azure-sql/database/threat-detection-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 가상 네트워크 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure Firewall에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

전용 SQL 풀에 서비스 엔드포인트를 사용하는 경우 Azure SQL 데이터베이스 공용 IP 주소에 대한 아웃바운드가 필요합니다. 연결을 허용하려면 NSG(네트워크 보안 그룹)가 Azure SQL Database IP에 대해 열려야 합니다. Azure SQL Database에 대해 NSG 서비스 태그를 사용하면 이 작업을 수행할 수 있습니다.

- [Azure SQL Database용 서비스 엔드포인트를 사용한 서비스 태그 이해](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [서비스 태그 이해 및 사용](../virtual-network/service-tags-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Policy를 사용하여 전용 SQL 풀과 관련된 리소스에 대한 네트워크 보안 구성을 정의하고 구현합니다. "Microsoft.Sql" 네임스페이스를 사용하여 사용자 지정 정책 정의를 정의하거나 Azure SQL 데이터베이스/서버 네트워크 보호를 위해 설계된 기본 제공 정책 정의를 사용할 수 있습니다. Azure SQL Database 서버에 적용 가능한 기본 제공 네트워크 보안 정책의 예: "SQL Server는 가상 네트워크 서비스 엔드포인트를 사용해야 함"입니다.

Azure Blueprints를 사용하여 주요 환경 아티팩트(예: Azure 리소스 관리 템플릿, Azure RBAC(Azure 역할 기반 액세스 제어) 및 정책)를 단일 청사진 정의로 패키지하여 대규모 Azure 배포를 간소화합니다. Blueprint를 새로운 구독 및 환경에 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 세부적으로 조정합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: NSG(네트워크 보안 그룹) 및 네트워크 보안 및 트래픽 흐름과 관련된 기타 리소스에 태그를 사용합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 사용하여 모든 리소스에서 태그를 포함하도록 만들고 태그가 없는 기존 리소스를 알립니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기반으로 리소스를 조회하거나 작업을 수행할 수 있습니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고 전용 SQL 풀 관련 네트워크 리소스에 대한 변경 내용을 검색합니다. 중요한 네트워크 리소스에 변경 내용이 발생하는 경우 트리거하는 Azure Monitor 내에서 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 감사 정책은 특정 데이터베이스에 대해 정의하거나 Azure(Azure Synapse를 호스트하는)의 기본 서버 정책으로 정의할 수 있습니다. 서버에 있는 모든 기존 데이터베이스 및 새로 만든 데이터베이스에 서버 정책이 적용됩니다.

서버 감사를 사용하도록 설정된 경우 항상 데이터베이스에 적용됩니다. 데이터베이스 감사 설정에 관계없이 데이터베이스를 감사합니다.

감사를 사용하도록 설정하면 Azure Storage 계정, Log Analytics 작업 영역 또는 Event Hubs의 감사 로그에 기록할 수 있습니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

- [Azure SQL 리소스에 대한 감사를 설정하는 방법](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 전용 SQL 풀에 대해 Azure SQL 서버 수준에서 감사를 사용하도록 설정하고 감사 로그의 스토리지 위치를 선택합니다(Azure Storage, Log Analytics 또는 Event Hubs).

감사는 데이터베이스 또는 서버 수준 모두에서 사용하도록 설정할 수 있으며, 특정 데이터베이스에 대해 별도의 데이터 싱크 또는 보존을 구성해야 하는 경우가 아니면 서버 수준에서만 사용 하도록 설정하는 것이 좋습니다.

- [Azure SQL Database에 대한 감사를 사용하도록 설정하는 방법](../azure-sql/database/auditing-overview.md)

- [서버에 대한 감사를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [서버 수준과 데이터베이스 수준 감사 정책의 차이점](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Storage 계정, Log Analytics 작업 영역 또는 이벤트 허브에 전용 SQL 풀과 관련된 로그를 저장하는 경우 조직의 규정 준수 규정에 따라 로그 보존 기간을 설정합니다.

- [Azure Blob Storage 수명 주기 관리](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Log Analytics 작업 영역에서 로그 보존 매개 변수를 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Event Hubs에서 스트리밍 이벤트 캡처](../event-hubs/event-hubs-capture-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 로그를 분석하고 모니터링하여 비정상 동작이 있는지 확인하고 결과를 정기적으로 검토합니다. Azure Security Center와 함께 Azure SQL Database에 대한 Advanced Threat Protection을 사용하여 SQL 데이터베이스와 관련된 비정상적인 활동을 경고합니다. 아니면, 메트릭 값 또는 SQL 데이터베이스와 관련된 Azure 활동 로그 항목을 기반으로 경고를 구성합니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

- [Azure SQL Database에 대한 경고 및 Advanced Threat Protection 이해](../azure-sql/database/threat-detection-overview.md)

- [Azure SQL Database에 대한 Advanced Data Security를 사용하도록 설정하는 방법](../azure-sql/database/azure-defender-for-sql.md)

- [Azure SQL Database에 대한 사용자 지정 경고를 구성하는 방법](../azure-sql/database/alerts-insights-configure-portal.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Azure Security Center와 함께 Azure SQL Database에 대한 ATP(Advanced Threat Protection)를 사용하여 비정상적인 활동을 모니터링하고 경고합니다. ATP는 ADS(Advanced Data Security) 제품의 일부이며 포털의 중앙 SQL ADS를 통해 액세스하고 관리할 수 있습니다. ADS에는 중요한 데이터 검색 및 분류, 잠재적인 데이터베이스 취약성 표시 및 완화, 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 탐지 기능이 포함됩니다.

또는 데이터를 사용하도록 설정하고 Azure Sentinel에 온보딩할 수 있습니다.

- [Azure SQL Database에 대한 경고 및 Advanced Threat Protection 이해](../azure-sql/database/threat-detection-overview.md)

- [Azure SQL Database에 대한 Advanced Data Security를 사용하도록 설정하는 방법](../azure-sql/database/azure-defender-for-sql.md)

- [Azure Security Center에서 경고를 관리하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: 사용자는 Azure AD(Azure Active Directory) 또는 SQL 인증을 사용하여 인증됩니다.

처음으로 Azure SQL을 배포할 때 해당 로그인에 대한 관리자 로그인과 연결된 암호를 지정합니다. 이 관리 계정을 서버 관리자라고합니다. Azure Portal을 열고 서버 또는 관리되는 인스턴스의 속성 탭으로 이동하여 데이터베이스에 대한 관리자 계정을 식별할 수 있습니다. 전체 관리 권한으로 Azure AD 관리자 계정을 구성할 수도 있으며, 이러한 구성은 Azure AD 인증을 사용하려는 경우 필요합니다.

관리 작업의 경우 명시적으로 할당해야 하는 Azure AD 기본 제공 역할을 사용합니다. Azure AD PowerShell 모듈을 사용하여 임시 쿼리를 수행해서 관리 그룹의 구성원인 계정을 검색합니다.

- [SQL Database에 대한 인증](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [관리자가 아닌 사용자를 위한 계정 만들기](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [인증에 Azure AD 계정 사용](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 구성원을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure SQL에서 기존 로그인 및 관리자 계정을 관리하는 방법](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure AD(Azure Active Directory)에는 기본 암호 개념이 없습니다. 전용 SQL 풀을 프로비전할 때 인증을 Azure AD와 통합하도록 선택하는 것이 좋습니다. 이 인증 방법을 사용하면 사용자가 사용자 계정 이름을 제출하고 서비스가 Azure AD에 저장된 자격 증명 정보를 사용하도록 요청합니다.

- [Azure SQL에서 Azure AD 인증을 구성하고 관리하는 방법](../azure-sql/database/authentication-aad-configure.md)

- [Azure SQL의 인증 이해](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 정책과 절차를 만듭니다. Azure Security Center ID 및 액세스 관리를 사용하여 Azure AD(Azure Active Directory)를 통해 로그인하는 관리 계정 수를 모니터링합니다.

데이터베이스의 관리자 계정을 식별하려면 Azure Portal을 열고 서버 또는 관리형 인스턴스의 속성 탭으로 이동합니다.

- [Azure Security Center ID 및 액세스 이해](../security-center/security-center-identity-access.md)

- [Azure SQL에서 기존 로그인 및 관리자 계정을 관리하는 방법](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory SSO(Single Sign-On) 사용

**지침**: Azure 앱 등록(서비스 주체)을 사용하여 API 호출을 통해 제어 평면(Azure Portal)에서 데이터 웨어하우스와 상호 작용하는 데 사용할 수 있는 토큰을 검색합니다.

- [Azure REST API를 호출하는 방법](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Azure AD(Azure Active Directory)를 사용하여 클라이언트 애플리케이션(서비스 주체)을 등록하는 방법](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Synapse SQL REST API 정보](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리 추천 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

- [Azure SQL의 다단계 인증 이해](../azure-sql/database/authentication-mfa-ssms-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에 안전한 Azure 관리 워크스테이션 사용

**지침**: Azure 리소스에 로그인하고 구성하도록 구성된 다단계 인증과 함께 PAW(Privileged Access Workstation)를 사용합니다.

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: Azure AD(Azure Active Directory) 보안 보고서를 사용하여 환경에서 의심스럽거나 안전하지 않은 활동이 발생하면 로그 및 경고를 생성합니다.

Azure SQL Database에 대한 Advanced Threat Protection을 Azure Security Center와 함께 사용하여 데이터베이스를 악용하거나 액세스하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 탐지하고 경고합니다.

SQL Server 감사를 사용하면 서버 수준 이벤트에 대한 서버 감사 사양과 데이터베이스 수준 이벤트에 대한 데이터베이스 감사 사양을 포함할 수 있는 서버 감사를 만들 수 있습니다. 감사 이벤트는 이벤트 로그 또는 감사 파일에 기록될 수 있습니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 작업을 모니터링하는 방법](../security-center/security-center-identity-access.md)

- [Advanced Threat Protection 및 잠재적 경고 검토](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Azure SQL의 로그인 및 사용자 계정 이해](../azure-sql/database/logins-create-manage.md)

- [SQL Server 감사 이해](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹화에서만 포털 및 Azure Resource 관리 액세스를 허용합니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: 전용 SQL 풀에Azure SQL Database 서버에 대한 Azure AD(Azure Active Directory) 관리자를 만듭니다.

- [Azure SQL에서 Azure AD 인증을 구성하고 관리하는 방법](../azure-sql/database/authentication-aad-configure.md)

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory-domain-services/tutorial-create-instance.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory)는 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure AD 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스하도록 할 수 있습니다.

SQL 인증을 사용하는 경우 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다. 한 명 이상의 데이터베이스 사용자를 해당 사용자 그룹에 적절한 권한을 갖는 사용자 지정 데이터베이스 역할에 배치해야 합니다.

- [액세스 검토 사용 방법](../active-directory/governance/access-reviews-overview.md)

- [Azure SQL의 로그인 및 사용자 계정 이해](../azure-sql/database/logins-create-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: Azure SQL을 사용하여 Azure AD(Azure Active Directory) 인증을 구성하고 Azure AD 사용자 계정에 대한 진단 설정을 사용하도록 설정하여 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 내에서 원하는 경고를 구성합니다.

SQL 인증을 사용하는 경우 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다. 한 명 이상의 데이터베이스 사용자를 해당 사용자 그룹에 적절한 권한을 갖는 사용자 지정 데이터베이스 역할에 배치해야 합니다.

- [액세스 검토 사용 방법](../active-directory/governance/access-reviews-overview.md)

- [Azure SQL Database를 사용하여 Azure AD 인증을 구성하고 관리하는 방법](../azure-sql/database/authentication-aad-configure.md)

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure SQL의 로그인 및 사용자 계정 이해](../azure-sql/database/logins-create-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory) ID 보호 및 위험 검색 기능을 사용하여 사용자 ID와 관련하여 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 추가 조사를 위해 Azure Sentinel에 온보딩하고 데이터를 수집합니다.

SQL 인증을 사용하는 경우 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다. 한 명 이상의 데이터베이스 사용자를 해당 사용자 그룹에 적절한 권한을 갖는 사용자 지정 데이터베이스 역할에 배치해야 합니다.

- [Azure AD 위험한 로그인을 보는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/connect-data-sources.md)

- [Azure SQL의 로그인 및 사용자 계정 이해](../azure-sql/database/logins-create-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: Microsoft가 전용 SQL 풀의 Azure SQL Database와 관련된 데이터에 액세스해야 하는 지원 시나리오에서, Azure 고객 Lockbox는 데이터 액세스 요청을 검토하고 승인하거나 거부할 수 있는 인터페이스를 제공합니다.

- [고객 Lockbox 이해](../security/fundamentals/customer-lockbox-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

데이터 검색 &amp; 분류는 Azure Synapse SQL에 기본 제공됩니다. 데이터베이스에서 중요한 데이터의 검색, 분류, 레이블 지정 및 보고를 위한 고급 기능을 제공합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [데이터 검색 &amp; 분류 이해](../azure-sql/database/data-discovery-and-classification-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 리소스는 가상 네트워크/서브넷으로 구분되고, 적절하게 태그가 지정되고, 네트워크 보안 그룹 또는 Azure Firewall 내에서 보호되어야 합니다. 중요한 데이터를 저장하거나 처리하는 리소스는 격리되어야 합니다. Private Link 사용; Azure SQL Server를 가상 네트워크 내에 배포하고 Private Link를 사용하여 안전하게 연결합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Azure SQL Database용 Private Link를 설정하는 방법](../azure-sql/database/private-endpoint-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 중요한 정보를 저장하거나 처리하는 전용 SQL 풀의 모든 Azure SQL Database의 경우 태그를 사용하여 데이터베이스 및 관련 리소스를 중요한 것으로 표시합니다. 중요한 정보의 반출을 방지하기 위해 Azure SQL Database 인스턴스에서 NSG(네트워크 보안 그룹) 서비스 태그와 함께 Private Link를 구성합니다.

또한 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse에 대한 Advanced Threat Protection은 데이터베이스를 악용하거나 액세스하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 감지합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 투자합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure SQL Database 인스턴스에서 데이터 반출을 방지하도록 Private Link 및 NSG를 구성하는 방법](../azure-sql/database/private-endpoint-overview.md)

- [Azure SQL Database에 대한 Advanced Threat Protection 이해](../azure-sql/database/threat-detection-overview.md)

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure Synapse SQL 데이터 검색 &amp; 분류 기능을 사용합니다. 데이터 검색 &amp; 분류는 데이터베이스의 중요한 데이터의 검색, 분류, 레이블 지정 &amp; 보호를 위해 Azure SQL Database에 고급 기능을 기본 제공합니다.

데이터 검색 &amp; 분류는 고급 SQL 보안 기능용 통합 패키지인 Advanced Data Security 제품에 포함되어 있습니다. 중앙 SQL ADS 포털을 통해 데이터 검색 &amp; 분류에 액세스하고 관리할 수 있습니다.

또한 Azure Portal에서 DDM(동적 데이터 마스킹) 정책을 설정할 수 있습니다. DDM 권장 사항 엔진은 데이터베이스의 특정 필드를 마스킹에 적합한 잠재적으로 중요한 필드로 플래그를 지정합니다.

- [Azure SQL Server에 데이터 검색 및 분류를 사용하는 방법](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Azure Synapse SQL에 대한 동적 데이터 마스킹 이해](../azure-sql/database/dynamic-data-masking-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4.6: Azure RBAC 액세스 제어를 사용하여 리소스에 대한 액세스 제어 

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 전용 SQL 풀에서 Azure SQL 데이터베이스에 대한 액세스를 관리합니다.

권한 부여는 사용자 계정의 데이터베이스 역할 구성원 자격 및 개체 수준 권한에 의해 제어됩니다. 사용자에게 필요한 최소한의 권한을 부여하는 것이 가장 좋습니다.

- [인증을 위해 Azure SQL Server를 Azure AD(Azure Active Directory)와 통합하는 방법](../azure-sql/database/authentication-aad-overview.md)

- [Azure SQL Server에서 액세스를 제어하는 방법](../azure-sql/database/logins-create-manage.md)

- [Azure SQL의 권한 부여 및 인증 이해](../azure-sql/database/logins-create-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: TDE(투명한 데이터 암호화)는 미사용 데이터를 암호화하여 악의적인 오프라인 활동의 위협으로부터 Azure Synapse SQL을 보호하는 데 도움이 됩니다. 애플리케이션에 대한 변경 없이 미사용 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행합니다. Azure에서 TDE에 대한 기본 설정은 DEK가 기본 제공 서버 인증서로 보호되는 것입니다. 또는 고객 관리형 TDE를 사용할 수 있으며, 이것은 TDE에 대한 BYOK(Bring Your Own Key) 지원이라고도 합니다. 이 시나리오에서 DEK를 암호화하는 TDE 보호기는 고객이 소유 및 관리하는 Azure Key Vault(Azure의 클라우드 기반 외부 키 관리 시스템)에 저장되고 키 자격 증명 모음을 벗어날 수 없는 고객 관리형 비대칭 키입니다.

- [서비스 관리형 투명한 데이터 암호화 이해](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [고객 관리형 투명한 데이터 암호화 이해](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [자체 키를 사용하여 TDE를 켜는 방법](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure Monitor에서 Azure 활동 로그를 사용하여 Synapse SQL 풀의 프로덕션 인스턴스 및 기타 중요하거나 관련된 리소스가 변경되는 경우에 대한 경고를 만듭니다.

또한 Azure Portal을 사용하여 SQL Synapse 풀의 데이터베이스에 대한 경고를 설정할 수 있습니다. 메트릭(예: 데이터베이스 크기 또는 CPU 사용량)이 임계값에 도달하면 경고에서 이메일을 보내거나 webhook를 호출할 수 있습니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure SQL Synapse에 대한 경고를 만드는 방법](../azure-sql/database/alerts-insights-configure-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [Azure Security Benchmark: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Advanced Data Security를 사용하도록 설정하고 Azure SQL Database에서 취약성 평가 수행에 대한 Azure Security Center의 권장 사항을 따릅니다.

- [Azure SQL 데이터베이스에서 취약성 평가를 실행하는 방법](../azure-sql/database/sql-vulnerability-assessment.md)

- [Advanced Data Security를 사용하도록 설정하는 방법](../azure-sql/database/azure-defender-for-sql.md)

- [Azure Security Center 취약성 평가 권장 사항을 구현하는 방법](../security-center/deploy-vulnerability-assessment-vm.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검색 비교

**지침**: 취약성 평가는 Azure Synapse SQL에 기본 제공 되는 검색 서비스입니다. 이 서비스는 보안 취약성에 플래그를 지정하는 규칙의 기술 자료를 채택합니다. 잘못된 구성, 과도한 권한, 보호되지 않는 중요한 데이터 등 모범 사례의 편차를 강조합니다.  취약성 평가는 중앙 SQL ADS(Advanced Data Security) 포털을 통해 액세스하고 관리할 수 있습니다.

- [SQL ADS 포털에서 취약성 평가 검사 관리 및 내보내기](../azure-sql/database/sql-vulnerability-assessment.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: Azure Security Center에서 제공하는 기본 위험 등급(보안 점수)을 사용합니다.

데이터 검색 &amp; 분류는 Azure Synapse SQL에 기본 제공됩니다. 데이터베이스에서 중요한 데이터의 검색, 분류, 레이블 지정 및 보고를 위한 고급 기능을 제공합니다.

- [Azure Security Center 보안 점수 이해](../security-center/secure-score-security-controls.md)

- [데이터 검색 &amp; 분류 이해](../azure-sql/database/data-discovery-and-classification-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내 전용 SQL 풀과 관련된 모든 리소스를 쿼리하고 검색합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

클래식 Azure 리소스는 Azure Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 보는 방법](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적으로 분류 체계로 구성합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 자산을 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 전용 SQL 풀 관련 승인된 Azure 리소스 목록을 정의합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy를 사용하여 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음과 같은 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: "Microsoft.Sql" 네임스페이스에 Azure Policy 별칭을 사용하여 전용 SQL 풀과 관련된 리소스 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. Azure Databases/Server에 대해 다음과 같은 기본 제공 정책 정의를 사용할 수도 있습니다.

- SQL 서버에 위협 탐지 배포
- SQL Server는 가상 네트워크 서비스 엔드포인트를 사용해야 함

- [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias)

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

**지침**: 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure DevOps 또는 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 설명서](/azure/devops/repos/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: 해당 사항 없음; Azure Synapse SQL에는 구성 가능한 보안 설정이 없습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Security Center를 활용하여 전용 SQL 풀과 관련된 모든 리소스에 대한 기준 검사를 수행합니다.

- [Azure Security Center에서 권장 사항을 수정하는 방법](../security-center/security-center-remediate-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault 에서 고객 관리형 키를 사용한 TDE(투명화 데이터 암호화)를 사용하면 TDE 보호기라는 고객 관리형 비대칭 키를 사용하여 자동으로 생성된 DEK(데이터베이스 암호화 키)를 암호화할 수 있습니다. 일반적으로 투명한 데이터 암호화를 위한 BYOK(Bring Your Own Key) 지원이라고도 합니다. BYOK 시나리오에서 TDE 보호기는 고객이 소유하고 관리하는 Azure Key Vault에 저장됩니다. 또한, Azure Key Vault에서 일시 삭제를 사용하도록 설정합니다.

- [Azure Key Vault의 고객 관리형 키를 사용하여 TDE를 사용하도록 설정하는 방법](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Azure Key Vault에서 일시 삭제를 사용하도록 설정하는 방법](../key-vault/general/key-vault-recovery.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**참고 자료**: 관리 ID를 사용하여 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 관리 ID를 사용하면 코드의 자격 증명 없이 Azure Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [자습서: Windows VM 시스템 할당 관리 ID를 사용하여 Azure SQL에 액세스](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [관리 ID를 구성하는 방법](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**참고 자료**: 자격 증명 검사기를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure Security Benchmark: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스(예: Azure Synapse SQL)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 고객 콘텐츠에서 실행되지 않습니다.

비 컴퓨팅 Azure 리소스(예: App Service, Data Lake Storage, Blob Storage, Azure SQL Server 등)로 업로드되는 콘텐츠를 미리 검사합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

- [Azure Cloud Services 및 Virtual Machines용 Microsoft Antimalware 이해](../security/fundamentals/antimalware.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: 전용 SQL 풀의 스냅샷은 하루 종일 자동으로 생성되어 7일 동안 사용 가능한 복원 지점이 생성됩니다. 이 보존 기간은 변경할 수 없습니다. 전용 SQL 풀은 8시간 RPO(복구 지점 목표)를 지원합니다. 지난 7일 동안 수행된 스냅샷 중 하나에서 주 지역의 데이터 웨어하우스를 복원할 수 있습니다. 필요한 경우 스냅샷을 수동으로 트리거할 수도 있습니다.

- [전용 SQL 풀에서 백업 및 복원](sql-data-warehouse/backup-and-restore.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: 데이터 웨어하우스의 스냅샷은 하루 종일 자동으로 생성되어 7일 동안 사용 가능한 복원 지점이 생성됩니다. 이 보존 기간은 변경할 수 없습니다. 전용 SQL 풀은 8시간 RPO(복구 지점 목표)를 지원합니다. 지난 7일 동안 수행된 스냅샷 중 하나에서 주 지역의 데이터 웨어하우스를 복원할 수 있습니다. 필요한 경우 스냅샷을 수동으로 트리거할 수도 있습니다.

고객 관리형 키를 사용하여 데이터베이스 암호화 키를 암호화하는 경우 키가 백업되고 있는지 확인합니다.

- [전용 SQL 풀에서 백업 및 복원](sql-data-warehouse/backup-and-restore.md)

- [Azure Key Vault 키를 백업하는 방법](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**책임**: 공유됨

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 정기적으로 복원 지점을 테스트하여 스냅샷이 유효한지 확인합니다. 복원 지점에서 기존 전용 SQL 풀을 복원하려면 Azure Portal 또는 PowerShell을 사용할 수 있습니다. 백업된 고객 관리형 키의 복원을 테스트합니다.

- [Azure Key Vault 키를 복원하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [전용 SQL 풀에서 백업 및 복원](sql-data-warehouse/backup-and-restore.md)

- [기존 전용 SQL 풀을 복원하는 방법](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Azure SQL Database에서는 최대 10년 동안 별도의 Azure Blob Storage 컨테이너에 데이터베이스 백업을 자동으로 보관하도록 LTR(장기 백업 보존) 정책을 사용하여 단일 데이터베이스나 풀링된 데이터베이스를 구성할 수 있습니다. Azure Storage의 데이터는 사용 가능한 가장 강력한 블록 암호 중 하나인 256비트 AES 암호화를 사용하여 투명하게 암호화 및 암호 해독되며 FIPS 140-2를 준수합니다.

기본적으로 스토리지 계정의 데이터는 Microsoft 관리형 키로 암호화됩니다. Microsoft 관리형 키를 데이터 암호화에 사용할 수도 있고 자체 키를 사용하여 암호화를 관리할 수도 있습니다. Key Vault를 사용하여 자체 키를 관리하는 경우 일시 삭제를 사용하도록 설정해야 합니다.

- [Azure SQL Database 장기 백업 보존 관리](../azure-sql/database/long-term-backup-retention-configure.md)

- [미사용 데이터에 대한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)

- [Key Vault에서 일시 삭제를 사용하도록 설정하는 방법](../storage/blobs/soft-delete-blob-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 담당자의 역할을 정의하는 서면으로 작성된 인시던트 대응 계획은 물론 인시던트 처리/관리 단계가 있어야 합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center는 경고에 심각도를 할당하기 때문에 각 경고를 처리하는 우선 순위를 지정하는 데 유용하며, 이를 통해 리소스가 손상되면 즉시 액세스할 수 있습니다. 심각도는 Security Center가 경고를 발행하는 데 사용된 결과 또는 메트릭에 얼마나 확신하는지 그리고 경고를 유발한 활동 배경에 악의적인 의도가 있었다는 신뢰 수준을 기반으로 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

- [NIST의 게시물(IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램 가이드)을 참조하세요.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**참고 자료**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 주체가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다.

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

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: Microsoft Rules of Engagement에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인하세요. https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

- [다음 백서에서는 Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 Microsoft의 Red Teaming 및 라이브 사이트 침투 테스트 전략 및 실행에 대한 자세한 내용을 확인할 수 있습니다.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
