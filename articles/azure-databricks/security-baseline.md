---
title: Azure Databricks에 대 한 Azure 보안 기준
description: Azure Databricks에 대 한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7d417f412592d87dec751b9d98a26bf786c719e5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796851"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Azure Databricks에 대 한 Azure 보안 기준

Azure Databricks에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

모범 사례 지침을 사용 하 여 Azure에서 클라우드 솔루션을 보호할 수 있는 방법에 대 한 권장 사항을 제공 하는 [Azure Security 벤치 마크 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서이 서비스에 대 한 기준을 가져옵니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조 하세요.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Virtual Network에서 네트워크 보안 그룹 또는 Azure 방화벽을 사용 하 여 리소스를 보호 합니다.

**지침**: Azure VNet (virtual network)에 Azure Databricks를 배포 합니다. Azure Databricks의 기본 배포는 Azure에서 완전히 관리 되는 서비스입니다. 모든 클러스터가 연결 될 VNet을 포함 하 여 모든 데이터 평면 리소스는 잠긴 리소스 그룹에 배포 됩니다. 그러나 네트워크 사용자 지정이 필요한 경우 사용자 고유의 가상 네트워크 (VNet 삽입)에서 데이터 평면 리소스 Azure Databricks 배포할 수 있으므로 사용자 지정 네트워크 구성을 구현할 수 있습니다. 사용자 지정 규칙을 사용 하 여 사용자 고유의 NSG (네트워크 보안 그룹)에 특정 송신 트래픽 제한 사항을 적용할 수 있습니다.

또한 Azure Databricks 인스턴스가 배포 되는 서브넷에서 송신 트래픽 제한을 지정 하도록 NSG 규칙을 구성할 수 있습니다. VNET 삽입 작업 영역에 대 한 Azure 방화벽을 구성할 수 있습니다.

* [사용자 고유의 Virtual Network에 Azure Databricks를 배포 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [NSGs를 관리 하는 방법](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NIC의 구성 및 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure VNet (virtual network)에 Azure Databricks를 배포 합니다. NSG (네트워크 보안 그룹)는 자동으로 만들어지지 않습니다. 기본 Azure 규칙만 사용 하 여 기준 NSG를 만들어야 합니다. 작업 영역을 배포 하는 경우 Databricks에 필요한 규칙이 추가 됩니다. 비어 있는 NSG로 시작할 수도 있으며 적절 한 규칙이 자동으로 추가 됩니다. NSG 흐름 로그를 사용 하도록 설정 하 고 로그를 트래픽 감사에 대 한 저장소 계정으로 보냅니다. 또한 Log Analytics 작업 영역에 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 정보를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화 하 고 핫 스폿을 식별 하 고, 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 구성을 잘못 파악할 수 있다는 것입니다.

* [사용자 고유의 Virtual Network에 Azure Databricks를 배포 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [NSG 흐름 로그를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [트래픽 분석 사용 및 사용 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Network Watcher를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="13-protect-critical-web-applications"></a>1.3: 중요 한 웹 응용 프로그램 보호

**지침**: 해당 사항 없음 이 권장 사항은 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 배포 된 서비스 거부 공격 으로부터 보호 하기 위해 Azure Databricks 인스턴스와 연결 된 Azure 가상 네트워크에서 Azure DDoS Protection Standard를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용 하 여 알려진 악성 또는 사용 되지 않는 공용 IP 주소와의 통신을 거부 합니다.

* [Azure Portal을 사용하여 Azure DDoS Protection 표준 관리](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Azure Security Center에서 Azure 네트워크 계층에 대 한 위협 방지 이해](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Azure VNet (virtual network)에 Azure Databricks를 배포 합니다. NSG (네트워크 보안 그룹)는 자동으로 만들어지지 않습니다. 기본 Azure 규칙만 사용 하 여 기준 NSG를 만들어야 합니다. 작업 영역을 배포 하는 경우 Databricks에 필요한 규칙이 추가 됩니다. NSG 흐름 로그를 사용 하도록 설정 하 고 트래픽 감사를 위해 저장소 계정에 로그를 보냅니다. 또한 Log Analytics 작업 영역에 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 정보를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화 하 고 핫 스폿을 식별 하 고, 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 구성을 잘못 파악할 수 있다는 것입니다.

* [사용자 고유의 Virtual Network에 Azure Databricks를 배포 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [NSG 흐름 로그를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [트래픽 분석 사용 및 사용 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Network Watcher를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS)을 배포 합니다.

**지침**: Azure Marketplace에서 NVA (IDP/IPS 가능 네트워크 가상 어플라이언스)를 구현 하 여 모든 Azure Databricks 클러스터에 단일 IP 아웃 바운드 주소가 있는 가상 네트워크 통합 작업 영역을 만듭니다. 단일 IP 주소는 특정 IP 주소에 따라 액세스를 허용 하는 다른 Azure 서비스 및 응용 프로그램에서 추가 보안 계층으로 사용할 수 있습니다. Azure 방화벽 또는 NVA와 같은 타사 도구를 사용 하 여 수신 및 송신 트래픽을 관리할 수 있습니다.

페이로드 검사를 기반으로 하는 침입 탐지 및/또는 방지 기능이 반드시 필요한 것은 아닙니다. 위협 인텔리전스를 포함 하는 Azure 방화벽을 사용할 수 있습니다. Azure 방화벽 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과의 트래픽을 경고 하 고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

* [Azure Firewall을 사용하여 VNet 삽입 작업 영역에 대한 단일 공용 IP를 할당하는 방법](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [NVA를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 응용 프로그램에 대 한 트래픽 관리

**지침**: 해당 사항 없음 이 권장 사항은 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버 헤드를 최소화 합니다.

**지침**: 서비스 태그를 사용 하 여 Azure Databricks 인스턴스가 연결 된 서브넷에 연결 된 네트워크 보안 그룹에 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: Microsoft.apimanagement)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그가 들어 있는 주소 접두사를 관리 하 고 주소가 변경 되 면 서비스 태그를 자동으로 업데이트 합니다. 서비스 태그는 삽입 되지 않은 VNET 작업 영역에서 지원 되지 않습니다.

* [서비스 태그 이해](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대 한 표준 보안 구성 유지

**지침**: Azure Policy를 사용 하 여 Azure Databricks 인스턴스에 대 한 네트워크 보안 구성을 정의 하 고 구현 합니다. "Databricks" 네임 스페이스에 Azure Policy 별칭을 사용 하 여 사용자 지정 정책 정의를 정의할 수 있습니다. 정책은 관리 되는 리소스 그룹 내의 리소스에 적용 되지 않습니다.

또한 azure 청사진을 사용 하 여 azure 리소스 관리 템플릿, RBAC (역할 기반 액세스 제어) 및 정책과 같은 주요 환경 아티팩트를 단일 청사진 정의로 패키지화 하 여 대규모 Azure 배포를 간소화할 수 있습니다. 새 구독과 환경에 청사진을 쉽게 적용 하 고 버전 관리를 통해 제어 및 관리를 세부적으로 조정 합니다.

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 별칭 및 정의 구조 이해](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Azure Blueprint를 만드는 방법](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: Azure Databricks 인스턴스와 연결 된 네트워크 보안 및 트래픽 흐름과 관련 된 nsgs 및 기타 리소스에 대 한 태그를 사용 합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용 하 여 네트워크에서 들어오고 나가는 트래픽을 허용 하는 모든 규칙에 대 한 비즈니스 필요 및/또는 기간 (등)을 지정 합니다.

* [태그를 만들고 사용 하는 방법](httphttps://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동 도구를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 변경 내용을 검색 합니다.

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 Azure Databricks 인스턴스와 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. 중요 한 네트워크 리소스에 대 한 변경 내용이 발생 하는 경우 트리거할 Azure Monitor 내에서 경고를 만듭니다.

* [Azure 활동 로그 이벤트를 확인 하 고 검색 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Azure Monitor에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조 하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인 된 시간 동기화 원본을 사용 하십시오.

**지침**: Microsoft는 Azure 리소스에 대 한 시간 원본을 유지 관리 하지만 계산 리소스에 대 한 시간 동기화 설정을 관리할 수 있는 옵션이 있습니다. Azure Databricks는 PaaS 서비스 이므로 Azure Databricks 클러스터의 Vm에 직접 액세스할 수 없으며 시간 동기화 설정을 지정할 수 없습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor을 통해 로그를 수집 하 여 Azure Databricks에서 생성 된 보안 데이터를 집계 합니다. Azure Monitor 내에서 Databricks 및 진단 로그를 수신 하도록 구성 된 Log Analytics 작업 영역을 쿼리할 수 있습니다. 장기/보관 로그 저장소 또는 event hubs에 대 한 Azure Storage 계정을 사용 하 여 다른 시스템으로 데이터를 내보냅니다. 또는 Azure 센티널 또는 타사 SIEM (보안 인시던트 및 이벤트 관리)에 대 한 온보드 데이터를 사용할 수 있습니다.

참고: Azure Databricks 진단 로그에 Azure Databricks Premium 계획이 필요 합니다.

* [진단 설정을 구성 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Azure 센티널을 등록 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Azure Monitor 및 타사 SIEM 통합을 시작 하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대 한 감사 로깅 사용

**지침**: Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 보관을 위해 Log Analytics 작업 영역, azure 이벤트 허브 또는 azure storage 계정으로 로그를 보냅니다. Azure 활동 로그 데이터를 사용 하 여 Azure 리소스에 대 한 제어 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 "무엇을, 누가, 언제"를 결정할 수 있습니다.

감사 로깅의 경우 Azure Databricks는 Azure Databricks 사용자가 수행한 활동의 포괄적인 종단 간 진단 로그를 제공 하 여 enterprise에서 자세한 Azure Databricks 사용 패턴을 모니터링할 수 있도록 합니다.

참고: Azure Databricks 진단 로그에 Azure Databricks Premium 계획이 필요 합니다.

* [Azure 활동 로그에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure Databricks에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: Azure Databricks는 Azure Databricks 사용자가 수행한 작업의 포괄적인 종단 간 진단 로그를 제공 하 여 enterprise에서 자세한 Azure Databricks 사용 패턴을 모니터링할 수 있도록 합니다.

참고: Azure Databricks 진단 로그에는 Azure Databricks Premium 계획이 필요 합니다. OS 보안 로깅을 사용할 수 없습니다.

* [Azure 활동 로그에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure Databricks에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

**지침**: Azure Databricks에 대 한 진단 설정을 사용 하도록 설정 합니다. Log Analytics 작업 영역에 로그를 저장 하도록 선택 하는 경우 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 합니다. 장기/보관 저장소에 Azure Storage 계정을 사용 합니다. 보안 관련 작업은 Databricks 감사 로그에서 추적 됩니다.

참고: Azure Databricks 진단 로그에 Azure Databricks Premium 계획이 필요 합니다.

* [Azure Databricks에서 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Log Analytics 작업 영역에 대 한 로그 보존 매개 변수를 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: Azure Databricks에 대 한 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역으로 로그를 보냅니다. Log Analytics 작업 영역을 사용 하 여 비정상적인 동작에 대 한 Azure Databricks 로그를 분석 및 모니터링 하 고 정기적으로 결과를 검토할 수 있습니다.

또는 Azure 센티널 또는 타사 SIEM에 대 한 온보드 데이터를 사용할 수 있습니다.

참고: Azure Databricks 진단 로그에 Azure Databricks Premium 계획이 필요 합니다.

* [Azure Databricks에서 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Log Analytics 작업 영역으로 전송 Azure Databricks 로그를 쿼리 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Azure 센티널을 등록 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure Databricks 인스턴스에 대 한 진단 설정을 구성 하 고 Log Analytics 작업 영역으로 로그를 보냅니다. Log Analytics 작업 영역 내에서 미리 정의 된 조건 집합이 발생 하는 경우 발생 하는 경고를 구성 합니다.

또는 Azure 센티널 또는 타사 SIEM에 대 한 온보드 데이터를 사용할 수 있습니다.

참고: Azure Databricks 진단 로그에 Azure Databricks Premium 계획이 필요 합니다.

* [Azure Databricks 로그를 Log Analytics 작업 영역으로 보내는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery)

* [Log Analytics 작업 영역에서 경고를 구성 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅을 중앙 집중화

**지침**: 해당 사항 없음

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅을 사용 하도록 설정

**지침**: 해당 사항 없음 Azure Databricks는 사용자가 액세스할 수 있는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅을 사용 합니다.

**지침**: 해당 사항 없음 이 지침은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: id 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure Databricks Scim api를 사용 하 여 Azure Databricks 작업 영역에서 사용자를 관리 하 고 지정 된 사용자에 게 관리 권한을 부여할 수 있습니다. Azure Databricks UI를 통해 관리할 수도 있습니다.

* [SCIM Api를 사용 하는 방법](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure Databricks에서 사용자를 추가 및 제거 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당 하는 경우 기본 암호를 변경 합니다.

**지침**: Azure Databricks AD (Azure Active Directory)를 사용 하 여 Azure Portal 및 Azure Databricks 관리 콘솔에 대 한 액세스를 제공 합니다. Azure AD에는 기본 암호 개념이 없지만 사용자 지정 또는 타사 응용 프로그램에 대 한 기본 암호를 변경 하거나 허용 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Azure Databricks Scim api를 사용 하 여 Azure Databricks에서 관리자 권한이 있는 사용자를 추가할 수 있습니다. Azure Databricks UI를 통해 관리할 수도 있습니다.

* [SCIM Api를 사용 하는 방법](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure Databricks에서 사용자를 추가 및 제거 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에 Single Sign-On (SSO)를 사용 하십시오.

**지침**: Azure Databricks은 Azure Active Directory Single Sign-On를 사용 하 여 사용자를 인증 하도록 자동으로 설정 됩니다. 조직 외부 사용자는 초대 프로세스를 완료 하 고 Active Directory 테 넌 트에 추가 해야 Single Sign-On를 통해 Azure Databricks에 로그인 할 수 있습니다. SCIM을 구현 하 여 작업 영역에서 사용자 프로 비전 및 프로 비전 해제를 자동화할 수 있습니다.

* [SCIM Api를 사용 하는 방법](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure Databricks에 대 한 Single Sign-On 이해](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Azure AD 테 넌 트에 사용자를 초대 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: AZURE AD MFA를 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

* [Azure에서 MFA를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center 내에서 id 및 액세스를 모니터링 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터 (권한 있는 액세스 워크스테이션)를 사용 합니다.

**지침**: MFA가 구성 된 paw (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure 리소스에 로그인 하 고 구성 합니다.

* [권한 있는 액세스 워크스테이션에 대 한 자세한 정보](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure에서 MFA를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고 생성에 Azure Active Directory 보안 보고서를 사용 합니다. Azure Security Center를 사용 하 여 id 및 액세스 작업을 모니터링할 수 있습니다. 또한 Azure Databricks 진단 로그를 활용할 수 있습니다.

* [위험한 활동에 대해 플래그가 지정 된 Azure AD 사용자를 확인 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Azure Security Center에서 사용자의 id 및 액세스 활동을 모니터링 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인 된 위치 에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스할 수 있도록 합니다.

* [Azure에서 명명 된 위치를 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Databricks은 Azure Active Directory Single Sign-On를 사용 하 여 사용자를 인증 하도록 자동으로 설정 됩니다. 조직 외부 사용자는 초대 프로세스를 완료 하 고 Active Directory 테 넌 트에 추가 해야 Single Sign-On를 통해 Azure Databricks에 로그인 할 수 있습니다.

* [Azure Databricks에 대 한 Single Sign-On 이해](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Azure AD 테 넌 트에 사용자를 초대 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스를 검토 하 고 조정 합니다.

**지침**: Azure AD는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다. SCIM Api를 구현 하 고 진단 로그를 Azure Databricks 하 여 사용자 액세스를 검토할 수도 있습니다. SCIM Api를 사용 하 고 진단 로그를 Azure Databricks 하 여 사용자 액세스를 검토할 수도 있습니다.

또한 Azure Databricks 관리 콘솔 내에서 사용자 액세스를 정기적으로 검토 하 고 관리 합니다.

* [Azure AD 보고](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Azure id 액세스 검토를 사용 하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Azure Databricks 관리 콘솔 내에서 사용자 액세스를 관리 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화 되는 계정에 대 한 액세스 시도를 모니터링 합니다.

**지침**: siem/모니터링 도구와 통합할 수 있도록 하는 Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다. 또한 Azure Databricks 진단 로그를 사용 하 여 사용자 액세스 작업을 검토할 수 있습니다.

Azure Active Directory 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

* [SCIM Api를 사용 하는 방법](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure Monitor에 Azure 활동 로그를 통합 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure AD 위험 및 id 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 Azure 센티널로 데이터를 수집할 수도 있습니다. 또한 Azure Databricks 진단 로그를 사용 하 여 사용자 액세스 작업을 검토할 수 있습니다.

* [Azure AD 위험한 로그인을 확인 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Id 보호 위험 정책을 구성 하 고 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure 센티널을 등록 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 Microsoft에 관련 고객 데이터에 대 한 액세스 권한을 제공 합니다.

**지침**: 지원 티켓이 열리면 고객 서비스 및 지원 엔지니어가 관련 고객 데이터에 대 한 액세스를 승인 하도록 요청 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조 하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요 한 정보 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 중요 한 정보를 처리 하는 Azure Databricks 인스턴스 추적을 지원 합니다.

* [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요 한 정보를 저장 하거나 처리 하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현 합니다. Azure Databricks의 기본 배포는 자체 가상 네트워크 내에 배포 되는 완전히 관리 되는 서비스입니다. 네트워크 사용자 지정이 필요한 경우 사용자 고유의 가상 네트워크에 Azure Databricks을 배포할 수 있습니다. 다른 비즈니스 팀 또는 부서에 대해 별도의 Azure Databricks 작업 영역을 만드는 것이 가장 좋습니다.

* [사용자 고유의 Virtual Network에 Azure Databricks를 배포 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹를 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Virtual Network를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요 한 정보에 대 한 무단 전송을 모니터링 하 고 차단 합니다.

**지침**: Databricks의 반출 보호 아키텍처를 따라 데이터 반출의 가능성을 완화 합니다.

* [Azure Databricks를 사용 하 여 데이터 Exfiltration 보호](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Microsoft는 Azure Databricks에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요 한 정보를 암호화 합니다.

**지침**: Azure Portal 또는 Azure Databricks 콘솔을 통해 Azure Databricks 인스턴스를 관리 하는 경우 Microsoft는 기본적으로 TLS 1.2을 협상 합니다. Databricks 웹 앱은 TLS 1.3를 지원 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용 하 여 중요 한 데이터 식별

**지침**: 현재 사용할 수 없음 현재 Azure Databricks에는 데이터 식별, 분류 및 손실 방지 기능을 사용할 수 없습니다. 중요 한 정보를 처리할 수 있는 Azure Databricks 인스턴스 및 관련 리소스에 태그를 적용 하 고 규정 준수를 위해 필요한 경우 타사 솔루션을 구현 합니다.

Databricks 플랫폼은 계산 전용 이며 모든 데이터는 다른 Azure 데이터 서비스에 저장 됩니다. Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전 하 게 유지 하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현 하 고 유지 관리 합니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 제어

**지침**: Azure Databricks에서는 acl (액세스 제어 목록)을 사용 하 여 데이터 테이블, 클러스터, 풀, 작업 및 전자 필기장, 실험, 폴더 등의 작업 영역 개체에 액세스할 수 있는 권한을 구성할 수 있습니다. 액세스 제어 목록을 관리할 수 있는 권한을 위임받은 사용자가 할 수 있듯이 모든 관리 사용자는 액세스 제어 목록을 관리할 수 있습니다. Azure RBAC를 사용 하 여 Azure Databricks 작업 영역에 대 한 권한을 설정할 수 있습니다.

참고: 테이블, 클러스터, 풀, 작업 및 작업 영역 액세스 제어는 Azure Databricks Premium 계획 에서만 사용할 수 있습니다.

* [Azure Databricks에서 액세스 제어를 관리 하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용 하 여 액세스 제어를 적용 하십시오.

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

Microsoft는 Azure Databricks에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 휴지 상태의 중요 정보 암호화

**지침**: Azure Databricks 작업 영역은 Azure Databricks 관리 되는 가상 네트워크에 호스트 되는 관리 평면과 고객이 관리 하는 가상 네트워크에 배포 된 데이터 평면으로 구성 됩니다. 컨트롤 평면은 모든 사용자의 노트북 및 관련 된 노트북 결과를 데이터베이스에 저장 합니다. 기본적으로 모든 노트북 및 결과는 다른 암호화 키를 사용 하 여 미사용에서 암호화 됩니다.

DBFS (Databricks File System)는 Azure Databricks 작업 영역에 탑재 되 고 Azure Databricks 클러스터에서 사용할 수 있는 분산 파일 시스템입니다. DBFS는 Azure Databricks 작업 영역에 관리 되는 리소스 그룹의 저장소 계정으로 구현 됩니다. 기본적으로 저장소 계정은 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 사용자가 소유 하 고 있는 Azure 데이터 서비스에 데이터가 저장 되며 암호화를 선택할 수 있습니다. DBFS를 사용 하 여 프로덕션 데이터를 저장 하지 않는 것이 좋습니다.

참고: 이러한 기능은 모든 Azure Databricks 구독에서 사용할 수 없습니다. 액세스를 요청 하려면 Microsoft 또는 Databricks 계정 담당자에 게 문의 하세요.

* [Azure Databricks 노트북에 대해 고객이 관리 하는 키를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Azure Databricks 파일 시스템에 대해 고객이 관리 하는 키를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요 한 Azure 리소스에 대 한 변경 내용을 기록 하 고 경고 합니다.

**지침**: 중요 한 Azure Databricks 작업 영역에 변경 내용이 발생 하는 경우에 대 한 경고를 만들기 위해 Azure 활동 로그와 Azure Monitor를 사용 합니다.

* [Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조 하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화 된 취약점 검사 도구 실행

**지침**: 타사 취약성 관리 솔루션을 구현 합니다.

취약성 관리 플랫폼 구독이 있는 경우 Azure Databricks 초기화 스크립트를 사용 하 여 Azure Databricks 클러스터 노드에 취약성 평가 에이전트를 설치 하 고 해당 포털을 통해 노드를 관리할 수 있습니다. 모든 타사 솔루션은 서로 다르게 작동 합니다.

* [Rapid7 Agent를 수동으로 설치 하는 방법](https://insightagent.help.rapid7.com/docs/install)

* [Qualys Agent를 수동으로 설치 하는 방법](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 초기화 스크립트](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화 된 운영 체제 패치 관리 솔루션 배포

**지침**: Microsoft는 클러스터 노드 기본 이미지를 유지 관리 하지만 클러스터 노드가 패치 된 상태로 유지 되도록 하는 것이 Azure Databricks. 실행 중인 기존 클러스터에 유지 관리 업데이트를 추가 하려면 클러스터를 다시 시작 해야 합니다.

* [Azure Databricks에 대 한 런타임 유지 관리 업데이트 이해](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화 된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: Microsoft는 Azure Databricks 클러스터 노드 기본 이미지를 유지 관리 하지만, 설치 하는 타사 응용 프로그램이 모두 패치 된 상태로 유지 되도록 할 책임이 있습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 백엔드 취약성 검색 비교

**지침**: 시간 경과에 따른 취약성 검색을 비교할 수 있는 타사 취약성 관리 솔루션을 구현 합니다. 취약성 관리 구독이 있는 경우 해당 공급 업체의 포털을 사용 하 여 백 엔드 취약성 검색을 보고 비교할 수 있습니다. 모든 타사 솔루션은 서로 다르게 작동 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용 하 여 검색 된 취약점의 재구성에 우선 순위를 지정 합니다.

**지침**: 일반적인 위험 점수 매기기 프로그램 (예: 일반적인 취약성 점수 매기기 시스템) 또는 타사 검사 도구에서 제공 하는 기본 위험 등급을 사용 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 고객

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)를 참조 하세요.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내에서 모든 리소스 (예: 계산, 저장소, 네트워크, 포트 및 프로토콜 등)를 쿼리하고 검색 합니다. 테 넌 트에 적절 한 (읽기) 권한이 있는지 확인 하 고 구독 내의 리소스 뿐만 아니라 모든 Azure 구독을 열거 합니다.

클래식 Azure 리소스는 리소스 그래프를 통해 검색 될 수 있지만 앞으로 Azure Resource Manager 리소스를 만들고 사용 하는 것이 좋습니다.

* [Azure 리소스 그래프를 사용 하 여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure 구독을 보는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타 데이터 유지 관리

**지침**: Azure 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 하는 메타 데이터를 제공 합니다.

* [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한이 없는 Azure 리소스를 삭제 합니다.

**지침**: 적절 한 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 Azure 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정 하 고 권한이 없는 리소스가 적시에 구독에서 삭제 되도록 합니다.

또한 Azure policy를 사용 하 여 다음과 같은 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹를 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인 된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리를 유지 관리 합니다.

**지침**: 계산 리소스에 대해 승인 된 Azure 리소스 및 승인 된 소프트웨어를 정의 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인 되지 않은 Azure 리소스에 대 한 모니터링

**지침**: Azure policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정 합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 환경에 있는 모든 Azure 리소스가 승인 되었는지 확인 합니다.

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Graph를 사용 하 여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 계산 리소스 내에서 승인 되지 않은 소프트웨어 응용 프로그램 모니터링

**지침**: 해당 사항 없음 Azure Databricks는 PaaS 서비스 이며, 고객은 Azure Databricks 클러스터의 Vm에 직접 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인 되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내에서 Azure Databricks 인스턴스를 비롯 한 모든 리소스 (예: 계산, 저장소, 네트워크, 포트 및 프로토콜 등)를 쿼리하고 검색 합니다. 검색 된 승인 되지 않은 모든 Azure 리소스를 제거 합니다. Azure Databricks 클러스터 노드의 경우 승인 되지 않은 소프트웨어를 제거 하거나 경고 하는 타사 솔루션을 구현 합니다.

* [Azure Graph를 사용 하 여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="68-use-only-approved-applications"></a>6.8: 승인 된 응용 프로그램만 사용 하십시오.

**지침**: 해당 사항 없음 PaaS 서비스 Azure Databricks Azure Databricks 클러스터의 Vm에 직접 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인 된 Azure 서비스만 사용 합니다.

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy를 사용 하 여 특정 리소스 종류를 거부 하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="610-implement-approved-application-list"></a>6.10: 승인 된 응용 프로그램 목록 구현

**지침**: 해당 사항 없음 PaaS 서비스 Azure Databricks Azure Databricks 클러스터의 Vm에 직접 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>사용자가 스크립트를 통해 Azure Resource Manager 상호 작용할 수 있도록 제한</div>

**지침**: Azure 조건부 액세스를 사용 하 여 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 함으로써 사용자의 Azure Resource Manager 상호 작용 하는 기능을 제한 합니다.

* [Azure Resource Manager에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 계산 리소스 내에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침**: 해당 사항 없음 이는 클러스터의 관리자가 아닌 사용자가 작업을 실행 하기 위해 개별 노드에 액세스할 필요가 없기 때문에 Azure Databricks에는 적용 되지 않습니다. 클러스터 관리자에 게는 기본적으로 모든 클러스터 노드에 대 한 루트 액세스 권한이 있습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 응용 프로그램을 물리적 또는 논리적으로 분리

**지침**: 해당 사항 없음 벤치 마크는 Azure 앱 서비스 또는 웹 응용 프로그램을 호스트 하는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대 한 보안 구성을 설정 합니다.

**지침**: Azure Policy를 사용 하 여 Azure Databricks 인스턴스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Databricks" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Databricks 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 적용 된 정책은 Databricks 관리 되는 리소스 그룹에서 작동 하지 않습니다.

* [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성을 설정 합니다.

**지침**: 해당 사항 없음 PaaS 서비스 Azure Databricks Azure Databricks 클러스터의 Vm에 직접 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지

**지침**: Azure Policy를 사용 하 여 Azure Databricks 인스턴스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Databricks" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Databricks 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. Azure 정책 [거부] 및 [없는 경우 배포]를 사용 하 여 Azure 리소스에서 보안 설정을 적용 합니다.

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지

**지침**: Microsoft에서 관리 및 유지 관리 하는 운영 체제 이미지를 Azure Databricks 합니다. OS 수준 상태 구성을 구현 해야 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전 하 게 저장

**지침**: 사용자 지정 azure 정책 정의를 사용 하는 경우 Azure devops 또는 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

* [Azure DevOps에서 코드를 저장 하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전 하 게 저장

**지침**: Azure Databricks 클러스터 노드에 대 한 사용자 지정 이미지를 사용 하는 경우 사용자 지정 기본 이미지를 ACR (Azure Container Registry)와 같은 Docker 레지스트리로 푸시합니다.

* [Databricks Container Service를 사용 하 여 컨테이너를 사용자 지정 하는 방법](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Azure Container Registry 이해](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: Azure Policy를 사용 하 여 Azure Databricks 인스턴스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Databricks" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Databricks 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당 사항 없음 PaaS 서비스 Azure Databricks Azure Databricks 클러스터의 Vm에 직접 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Azure Policy를 사용 하 여 Azure Databricks 인스턴스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Databricks" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Databricks 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

* [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대 한 자동화 된 구성 모니터링 구현

**지침**: 타사 솔루션을 구현 하 여 Azure Databricks 클러스터 노드 운영 체제의 상태를 모니터링 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure 암호를 안전 하 게 관리

**지침**: 비밀을 안전 하 게 관리 하 고 사용 하기 위해 Azure Databricks 비밀 범위와 함께 Azure Key Vault를 사용 합니다.

* [Azure Databricks에서 Azure Key Vault를 사용 하는 방법](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: id를 안전 하 게 자동으로 관리

**지침**: 현재 사용할 수 없음 관리 되는 Id는 현재 Azure Databricks에서 사용할 수 없습니다.

* [Azure 리소스에 대한 관리 ID를 지원하는 서비스](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도 하지 않은 자격 증명 노출을 제거 합니다.

**지침**: 자격 증명 스캐너를 구현 하 여 코드 내에서 자격 증명을 식별 합니다. 또한 자격 증명 스캐너는 검색 된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동 하는 것을 권장 합니다.

* [자격 증명 스캐너를 설정 하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조 하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리 하는 맬웨어 방지 소프트웨어 사용

**지침**: 취약성 관리 플랫폼 구독이 있는 경우 Azure Databricks 초기화 스크립트를 사용 하 여 Azure Databricks 클러스터 노드에 취약성 평가 에이전트를 설치 하 고 해당 포털을 통해 노드를 관리할 수 있습니다. 모든 타사 솔루션은 서로 다르게 작동 합니다.

* [Rapid7 Agent를 수동으로 설치 하는 방법](https://insightagent.help.rapid7.com/docs/install)

* [Qualys Agent를 수동으로 설치 하는 방법](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 초기화 스크립트](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 계산 되지 않는 Azure 리소스에 업로드할 파일 사전 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: Azure App Service)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 콘텐츠에서 실행 되지 않습니다.

Azure Databricks 클러스터 노드 또는 관련 된 리소스에 업로드 되는 파일을 미리 검색 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명을 업데이트 해야 합니다.

**지침**: 취약성 관리 플랫폼 구독이 있는 경우 Azure Databricks 초기화 스크립트를 사용 하 여 Azure Databricks 클러스터 노드에 취약성 평가 에이전트를 설치 하 고 해당 포털을 통해 노드를 관리할 수 있습니다. 모든 타사 솔루션은 서로 다르게 작동 합니다.

* [Rapid7 Agent를 수동으로 설치 하는 방법](https://insightagent.help.rapid7.com/docs/install)

* [Qualys Agent를 수동으로 설치 하는 방법](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 초기화 스크립트](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: Azure Databricks 데이터 원본의 경우 사용 사례에 적합 한 수준의 데이터 중복성을 구성 했는지 확인 합니다. 예를 들어 Azure Databricks 데이터 저장소에 Azure Storage 계정을 사용 하는 경우 적절 한 중복성 옵션 (LRS, ZRS, GRS, RA-GRS)을 선택 합니다.

* [Azure Databricks의 데이터 원본](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Azure Databricks 클러스터의 지역 재해 복구](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Azure Key Vault 내의 Azure Databricks 구현과 관련 된 모든 고객 관리 키를 백업 합니다. REST API 및 CLI를 사용 하 여 Databricks 구성의 매일 백업을 만들 수도 있습니다. REST API/CLI를 사용 하 여 Databricks 구성의 매일 백업을 만들 수도 있습니다.

* [Azure에서 주요 자격 증명 모음 키를 백업 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함 한 모든 백업의 유효성을 검사 합니다.

**지침**: Azure Databricks 구현과 관련 된 백업 된 고객 관리 키의 복원을 테스트 합니다.

* [Azure에서 주요 자격 증명 모음 키를 복원 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키의 보호 보장

**지침**: Key Vault에서 일시 삭제를 사용 하도록 설정 하 여 실수로 또는 악의적으로 삭제 되지 않도록 키를 보호 합니다.

* [Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center 모니터링**: 예

**책임**: 고객

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 응답](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 응답 가이드 만들기

**지침**: 조직에 대 한 인시던트 대응 가이드를 작성 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 인시던트 처리/관리 단계를 정의 합니다.

* [Azure Security Center 내에서 워크플로 자동화를 구성 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [사용자 고유의 보안 인시던트 대응 프로세스를 구축 하는 방법에 대 한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수도 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 경고를 실행 하는 데 사용 되는 검색 또는 분석에 사용 되는 Security Center의 신뢰도와 경고를 발생 시킨 활동의 악의적인 의도를 가진 신뢰도 수준을 기준으로 합니다.

또한 구독을 명확 하 게 표시 합니다 (예: 프로덕션, 비 prod) 및 Azure 리소스를 명확 하 게 식별 하 고 범주화 하는 명명 시스템을 만듭니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="103-test-security-response-procedures"></a>10.3: 보안 응답 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하는 연습을 수행 합니다. 필요에 따라 weak 점수와 격차 및 수정 계획을 식별 합니다.

* [IT 계획 및 기능에 대 한 NIST의 게시: 테스트, 학습 및 연습 프로그램 가이드를 참조 하세요.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트에 대 한 세부 정보를 제공 하 고 보안 인시던트에 대 한 경고 알림을 구성 하십시오.

**지침**: Microsoft 보안 대응 센터 (MSRC)에서 불법적인 또는 권한이 없는 당사자가 고객 데이터에 액세스 한 것을 발견 한 경우 microsoft에서 사용자에 게 연락 하는 데 사용 됩니다. 문제가 해결 되었는지 확인 하기 위해 팩트 이후에 인시던트를 검토 합니다.

* [Azure Security Center 보안 연락처를 설정 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 응답 시스템에 통합 하세요.

**지침**: 연속 내보내기 기능을 사용 하 여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용 하면 경고 및 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있습니다. Azure Security Center data connector를 사용 하 여 경고 센티널을 스트리밍할 수 있습니다.

* [연속 내보내기를 구성 하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Azure 센티널로 경고를 스트리밍하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대 한 응답을 자동화 합니다.

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용 하 여 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

* [워크플로 자동화를 구성 하 고 Logic Apps 하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 60 일 내에 모든 중요 한 보안 결과를 수정 하세요.

**지침**: * Microsoft [Engagement 규칙에 따라 침투 테스트가 microsoft 정책을 위반 하지 않는지 확인](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 합니다.

* [Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트에 대 한 자세한 내용은 Microsoft에서 확인할 수 있습니다.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치 마크](https://docs.microsoft.com/azure/security/benchmarks/overview) 를 참조 하세요.
- [Azure 보안 기준](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) 에 대 한 자세한 정보
