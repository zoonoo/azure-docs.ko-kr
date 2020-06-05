---
title: Azure Databricks에 대한 Azure 보안 기준
description: Azure Databricks에 대한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 23057723d46ed5d9203741ab2eb1a15edb5bd510
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681703"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Azure Databricks에 대한 Azure 보안 기준

Azure Databricks에 대한 Azure 보안 기준에는 배포의 보안 상태를 개선하는 데 유용한 권장 사항이 포함되어 있습니다.

이 서비스에 대한 기준은 [Azure Security Benchmark 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 컨트롤: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하세요.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Virtual Network에서 네트워크 보안 그룹 또는 Azure Firewall을 사용하여 리소스 보호

**지침**: 자체 Azure 가상 네트워크(VNet)에서 Azure Databricks 배포. Azure Databricks의 기본 배포는 Azure의 완전 관리형 서비스입니다. 모든 클러스터가 연결될 VNet을 비롯한 모든 데이터 평면 리소스가 잠긴 리소스 그룹에 배포됩니다. 하지만 네트워크 사용자 지정이 필요한 경우, 자체 가상 네트워크(VNet 삽입)에 Azure Databricks 데이터 평면 리소스를 배포할 수 있기 때문에 사용자 지정 네트워크 구성을 구현할 수 있습니다. 사용자 지정 규칙을 통해 자체 NSG(네트워크 보안 그룹)를 특정 송신 트래픽 제한에 적용할 수 있습니다.

또한 Azure Databricks 인스턴스가 배포된 서브넷에서 송신 트래픽 제한을 지정하도록 NSG 규칙을 구성할 수 있습니다. VNET 삽입 작업 영역에 대해 Azure Firewall을 구성할 수 있습니다.

* [자체 Virtual Network에 Azure Databricks를 배포하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [NSG를 관리하는 방법](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NICS의 구성 및 트래픽을 모니터링 및 기록

**지침**: 자체 Azure 가상 네트워크(VNet)에서 Azure Databricks 배포. NSG(네트워크 보안 그룹)는 자동으로 생성되지 않습니다. 기본 Azure 규칙만 사용하여 기준 NSG를 만들어야 합니다. 작업 영역을 배포할 때 Databricks에 필요한 규칙이 추가됩니다. 비어 있는 NSG로 시작할 수도 있으며 적절한 규칙이 자동으로 추가됩니다. NSG 흐름 로그를 사용하도록 설정하고 트래픽 감사를 위해 스토리지 계정에 로그를 보냅니다. 또한 Log Analytics 작업 영역에 흐름 로그를 보내고 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 인사이트를 제공할 수도 있습니다. 트래픽 분석의 장점으로 네트워크 활동의 시각화 핫 스폿 식별, 보안 위협 식별, 트래픽 흐름 패턴 이해, 네트워크 구성 오류 파악 등의 기능이 있습니다.

* [자체 Virtual Network에 Azure Databricks를 배포하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [NSG 흐름 로그를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Network Watcher를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 없음. 이 권장 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 분산 서비스 거부 공격(DDoS)으로부터 보호하기 위해 Azure Databricks 인스턴스와 연결된 Azure Virtual Network에서 Azure DDoS Protection 표준을 사용하도록 설정합니다. Azure Security Center에 통합된 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 공용 IP 주소와의 통신을 거부합니다.

* [Azure Portal을 사용하여 Azure DDoS Protection 표준 관리](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Azure Security Center에서 Azure 네트워크 계층에 대한 위협 방지 이해](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: 자체 Azure 가상 네트워크(VNet)에서 Azure Databricks 배포. NSG(네트워크 보안 그룹)는 자동으로 생성되지 않습니다. 기본 Azure 규칙만 사용하여 기준 NSG를 만들어야 합니다. 작업 영역을 배포할 때 Databricks에 필요한 규칙이 추가됩니다. NSG 흐름 로그를 사용하도록 설정하고 트래픽 감사를 위해 스토리지 계정에 로그를 보냅니다. 또한 Log Analytics 작업 영역에 흐름 로그를 보내고 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 인사이트를 제공할 수도 있습니다. 트래픽 분석의 장점으로 네트워크 활동의 시각화 핫 스폿 식별, 보안 위협 식별, 트래픽 흐름 패턴 이해, 네트워크 구성 오류 파악 등의 기능이 있습니다.

* [자체 Virtual Network에 Azure Databricks를 배포하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [NSG 흐름 로그를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Network Watcher를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: Azure Marketplace에서 IDP/IPS 지원 NVA(네트워크 가상 어플라이언스)를 구현하여 모든 Azure Databricks 클러스터에 단일 IP 아웃바운드 주소가 있는 가상 네트워크 통합 작업 영역을 만듭니다. 단일 IP 주소는 특정 IP 주소에 따라 액세스를 허용하는 다른 Azure 서비스 및 애플리케이션에서 추가 보안 계층으로 사용할 수 있습니다. Azure 방화벽 또는 NVA와 같은 타사 도구를 사용하여 수신 및 송신 트래픽을 관리할 수 있습니다.

페이로드 검사에 기반한 침입 탐지 및/또는 방지 기능이 요구 사항이 아니면, 위협 인텔리전스가 포함된 Azure Firewall을 사용할 수 있습니다. Azure Firewall 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

* [Azure Firewall을 사용하여 VNet 삽입 작업 영역에 대한 단일 공용 IP를 할당하는 방법](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [NVA를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 없음. 이 권장 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 서비스 태그를 사용하여 Azure Databricks 인스턴스가 연결되어 있는 서브넷에 연결된 네트워크 보안 그룹에 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절한 원본 또는 대상 필드에 서비스 태그 이름(예: ApiManagement)을 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다. 삽입되지 않은 VNET 작업 영역에서는 서비스 태그가 지원되지 않습니다.

* [서비스 태그 이해](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Policy를 사용하여 Azure Databricks 인스턴스에 대한 네트워크 보안 구성을 정의하고 구현합니다. "Microsoft.Databricks" 네임스페이스에서 Azure Policy 별칭을 사용하여 사용자 지정 정책 정의를 정의할 수 있습니다. 관리되는 리소스 그룹 내의 리소스에는 정책이 적용되지 않습니다.

Azure Blueprints를 사용하여 주요 환경 아티팩트(예: Azure Resource 관리 템플릿, RBAC(역할 기반 액세스 제어) 및 정책)를 단일 Blueprint 정의로 패키징하여 대규모 Azure 배포를 간소화할 수도 있습니다. Blueprint를 새로운 구독 및 환경에 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 세부적으로 조정합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 별칭 및 정의 구조 이해](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Azure Blueprint를 만드는 방법](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: Azure Databricks 인스턴스와 연결되어 있는 트래픽 흐름 및 네트워크 보안과 관련된 NSG 및 기타 리소스에 대한 태그를 사용합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고 Azure Databricks 인스턴스 관련 네트워크 리소스에 대한 변경 내용을 검색합니다. 중요한 네트워크 리소스에 변경 내용이 발생하는 경우 트리거하는 Azure Monitor 내에서 경고를 만듭니다.

* [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Azure Monitor에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 컨트롤: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 소스 사용

**지침**: Azure 리소스에 대한 시간 원본은 Microsoft에서 유지 관리되지만 컴퓨팅 리소스에 대한 시간 동기화 설정을 관리할 수 있는 옵션이 있습니다. Azure Databricks는 PaaS 서비스이므로 Azure Databricks 클러스터의 VM에 직접 액세스할 수 없으며 시간 동기화 설정을 지정할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor를 통해 로그를 수집하여 Azure Databricks에서 생성된 보안 데이터를 집계합니다. Azure Monitor 내에서 Databricks 및 진단 로그를 수신하도록 구성된 Log Analytics 작업 영역을 쿼리할 수 있습니다. 장기/보관 로그 스토리지 또는 이벤트 허브에 Azure Storage 계정을 사용하면 데이터를 다른 시스템으로 내보낼 수 있습니다. 또는 Azure Sentinel 또는 타사 SIEM(Security Incident and Event Management)을 사용하도록 설정하고 데이터를 온보딩할 수 있습니다.

참고: Azure Databricks 진단 로그에는 Azure Databricks 프리미엄 플랜이 필요합니다.

* [진단 설정을 구성하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure 활동 로그 진단 설정을 사용하도록 설정하고 보관을 위해 로그를 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure 스토리지 계정으로 보냅니다. Azure 활동 로그 데이터를 사용하면 Azure 리소스의 컨트롤 플레인 수준에서 수행되는 모든 쓰기 작업(PUT, POST, DELETE)에 대한 "무엇을, 누가, 언제"를 판단할 수 있습니다.

감사 로깅을 위해 Azure Databricks는 Azure Databricks 사용자가 수행한 활동에 대한 포괄적인 엔드투엔드 진단 로그를 제공하기 때문에 기업에서 자세한 Azure Databricks 사용 패턴을 모니터링할 수 있습니다.

참고: Azure Databricks 진단 로그에는 Azure Databricks 프리미엄 플랜이 필요합니다.

* [Azure 활동 로그에 대한 진단 설정을 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy))

* [Azure Databricks에 대한 진단 설정을 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: Azure Databricks는 Azure Databricks 사용자가 수행한 활동에 대한 포괄적인 엔드투엔드 진단 로그를 제공하기 때문에 기업에서 자세한 Azure Databricks 사용 패턴을 모니터링할 수 있습니다.

참고: Azure Databricks 진단 로그에는 Azure Databricks 프리미엄 플랜이 필요합니다. OS 보안 로깅을 사용할 수 없습니다.

* [Azure 활동 로그에 대한 진단 설정을 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure Databricks에 대한 진단 설정을 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Databricks에 대한 진단 설정을 사용하도록 설정합니다. Log Analytics 작업 영역에 로그를 저장하도록 선택한 경우 조직의 규정 준수 규칙에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다. 장기/보관 스토리지에 Azure Storage 계정을 사용합니다. 보안 관련 활동은 Databricks 감사 로그에서 추적됩니다.

참고: Azure Databricks 진단 로그에는 Azure Databricks 프리미엄 플랜이 필요합니다.

* [Azure Databricks에서 진단 설정을 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Log Analytics 작업 영역에 대한 로그 보존 매개 변수를 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure Databricks에 대한 진단 설정을 사용하도록 설정하고 Log Analytics 작업 영역으로 로그를 보냅니다. Log Analytics 작업 영역을 사용하여 비정상적인 동작에 대해 Azure Databricks 로그를 분석 및 모니터링하고 정기적으로 결과를 검토합니다.

또는 Azure Sentinel 또는 타사 SIEM를 사용하도록 설정하고 데이터를 온보딩할 수 있습니다.

참고: Azure Databricks 진단 로그에는 Azure Databricks 프리미엄 플랜이 필요합니다.

* [Azure Databricks에서 진단 설정을 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Log Analytics 작업 영역으로 전송된 Azure Databricks 로그를 쿼리하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Azure Databricks 인스턴스에 대한 진단 설정을 구성하고 Log Analytics 작업 영역으로 로그를 보냅니다. Log Analytics 작업 영역 내에서 미리 정의한 일련의 조건이 발생하면 경보가 발생하도록 구성합니다.

또는 Azure Sentinel 또는 타사 SIEM를 사용하도록 설정하고 데이터를 온보딩할 수 있습니다.

참고: Azure Databricks 진단 로그에는 Azure Databricks 프리미엄 플랜이 필요합니다.

* [Log Analytics 작업 영역으로 Azure Databricks 로그를 보내는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery))

* [Log Analytics 작업 영역에서 경고를 구성하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당 사항 없음

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 없음. Azure Databricks는 사용자가 액세스할 수 있는 DNS 관련 로그를 처리하거나 생성하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 컨트롤: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure Databricks SCIM API를 사용하여 Azure Databricks 작업 영역에서 사용자를 관리하고 지정된 사용자에게 관리자 권한을 부여할 수 있습니다. Azure Databricks UI를 통해 관리할 수도 있습니다.

* [SCIM API를 사용하는 방법](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure Databricks에서 사용자를 추가 및 제거하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당하는 경우 기본 암호 변경

**지침**: Azure Databricks는 AD(Azure Active Directory)를 사용하여 Azure Portal 및 Azure Databricks 관리 콘솔에 대한 액세스를 제공합니다. Azure AD에는 기본 암호 개념이 없지만 사용자 지정 또는 타사 애플리케이션의 기본 암호를 변경하거나 허용하지 않을 책임이 사용자에게 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Azure Databricks SCIM API를 사용하여 Azure Databricks에서 관리자 권한이 있는 사용자를 추가할 수 있습니다. Azure Databricks UI를 통해 관리할 수도 있습니다.

* [SCIM API를 사용하는 방법](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure Databricks에서 사용자를 추가 및 제거하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Azure Databricks는 Azure Active Directory Single Sign-On을 사용하여 사용자를 인증하도록 자동으로 설정됩니다. 조직 외부의 사용자는 초대 프로세스를 완료하고 Active Directory 테넌트에 추가되어야 Single Sign-On을 통해 Azure Databricks에 로그인할 수 있습니다. SCIM을 구현하여 작업 영역에서 사용자 프로비저닝 및 프로비저닝 해제를 자동화할 수 있습니다.

* [SCIM API를 사용하는 방법](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure Databricks에 대한 Single Sign-On 이해](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Azure AD 테넌트에 사용자를 초대하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD MFA를 사용하도록 설정하고 Azure Security Center ID 및 액세스 관리 권장 사항을 따릅니다.

* [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 머신(권한 있는 액세스 워크스테이션) 사용

**지침**: MFA가 구성된 PAW(Privileged Access Workstation)를 사용하여 Azure 리소스에 로그인하고 구성합니다.

* [권한 있는 액세스 워크스테이션에 대해 알아보기](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: Azure Active Directory 보안 보고서를 사용하여 환경에서 의심스럽거나 안전하지 않은 활동이 발생하면 로그 및 경고를 생성합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다. 또한 Azure Databricks 진단 로그를 활용할 수 있습니다.

* [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.

* [Azure에서 명명된 위치를 구성하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Databricks는 Azure Active Directory Single Sign-On을 사용하여 사용자를 인증하도록 자동으로 설정됩니다. 조직 외부의 사용자는 초대 프로세스를 완료하고 Active Directory 테넌트에 추가되어야 Single Sign-On을 통해 Azure Databricks에 로그인할 수 있습니다.

* [Azure Databricks에 대한 Single Sign-On 이해](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Azure AD 테넌트에 사용자를 초대하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 사용자 액세스를 정기적으로 검토 및 조정

**지침**: Azure AD는 부실 계정을 검색하는 데 유용한 로그를 제공합니다. 또한 Azure ID 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다. SCIM API 및 Azure Databricks 진단 로그를 구현하여 사용자 액세스를 검토할 수도 있습니다. SCIM API 및 Azure Databricks 진단 로그를 사용하여 사용자 액세스를 검토할 수도 있습니다.

또한 Azure Databricks 관리 콘솔 내에서 사용자 액세스를 정기적으로 검토하고 관리합니다.

* [Azure AD 보고](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Azure ID 액세스 검토를 사용하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Azure Databricks 관리 콘솔 내에서 사용자 액세스를 관리하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 대한 액세스 시도 모니터링

**지침**: Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 소스에 액세스가 가능하므로 SIEM/모니터링 툴과 통합할 수 있습니다. Azure Databricks 진단 로그를 사용하여 사용자 액세스 활동을 검토할 수도 있습니다.

Azure Active Directory 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics Workspace로 보내면 이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

* [SCIM API를 사용하는 방법](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure 활동 로그를 Azure Monitor에 통합하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD 위험 및 ID 보호 기능을 사용하여 사용자 ID와 관련하여 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 추가 조사를 위해 Azure Sentinel에 데이터를 수집할 수도 있습니다. Azure Databricks 진단 로그를 사용하여 사용자 액세스 활동을 검토할 수도 있습니다.

* [Azure AD 위험한 로그인을 확인하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 지원 티켓을 열면 고객 서비스 및 지원 엔지니어가 관련 고객 데이터에 액세스하는 데 동의를 요청합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 컨트롤: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 처리하는 Azure Databricks 인스턴스 추적을 지원합니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. Azure Databricks의 기본 배포는 자체 가상 네트워크 내에 배포되는 완전 관리형 서비스입니다. 네트워크 사용자 지정이 필요하면 자체 가상 네트워크에 Azure Databricks를 배포하면 됩니다. 비즈니스 팀이나 부서마다 별도의 Azure Databricks 작업 영역을 만드는 것이 가장 좋습니다.

* [자체 Virtual Network에 Azure Databricks를 배포하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Virtual Network를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: Databricks의 반출 방지 아키텍처에 따라 데이터 반출 가능성을 완화합니다.

* [Azure Databricks를 사용하여 데이터 반출 방지](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Microsoft는 Azure Databricks의 기본 인프라를 관리하고, 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Microsoft는 Azure Portal 또는 Azure Databricks 콘솔을 통해 Azure Databricks 인스턴스를 관리할 때 기본적으로 TLS 1.2를 협상합니다. Databricks 웹앱은 TLS 1.3을 지원합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 현재 사용할 수 없음: 현재 Azure Databricks에서는 데이터 식별, 분류 및 손실 방지 기능을 사용할 수 없습니다. 중요한 정보를 처리할만한 Azure Databricks 인스턴스 및 관련 리소스에 태그를 지정하고 규정 준수 목적에 필요한 경우 타사 솔루션을 구현합니다.

Databricks 플랫폼은 컴퓨팅 전용이며 모든 데이터는 다른 Azure 데이터 서비스에 저장됩니다. Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 투자합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: Azure Databricks에서 ACL(액세스 제어 목록)을 사용하여 데이터 테이블, 클러스터, 풀, 작업 및 작업 영역 개체(예: Notebook, 실험 및 폴더)에 대한 액세스 권한을 구성할 수 있습니다. 액세스 제어 목록을 관리할 수 있는 권한을 위임받은 사용자가 할 수 있듯이 모든 관리 사용자는 액세스 제어 목록을 관리할 수 있습니다. Azure RBAC를 사용하여 Azure Databricks 작업 영역에 대한 권한을 설정할 수 있습니다.

참고: 테이블, 클러스터, 풀, 작업 및 작업 영역 액세스 제어는 Azure Databricks 프리미엄 플랜에서만 사용할 수 있습니다.

* [Azure Databricks에서 액세스 제어를 관리하는 방법](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 없음. 이 권장 사항은 컴퓨팅 리소스를 위한 것입니다.

Microsoft는 Azure Databricks의 기본 인프라를 관리하고, 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Databricks 작업 영역은 Azure Databricks 관리 가상 네트워크에서 호스트되는 관리 평면과 고객 관리형 가상 네트워크에 배포된 데이터 평면으로 구성됩니다. 컨트롤 플레인은 모든 사용자의 Notebook 및 관련 Notebook 결과를 데이터베이스에 저장합니다. 기본적으로 모든 Notebook과 결과는 다른 암호화 키를 사용하여 미사용 상태로 암호화됩니다.

DBFS(Databricks 파일 시스템)는 Azure Databricks 작업 영역에 탑재되고 Azure Databricks 클러스터에서 사용할 수 있는 분산 파일 시스템입니다. DBFS는 Azure Databricks 작업 영역의 관리형 리소스 그룹에서 스토리지 계정으로 구현됩니다. 기본적으로 스토리지 계정은 Microsoft 관리형 키로 암호화됩니다. 내 데이터는 내가 소유한 Azure 데이터 서비스에 저장되며 암호화하도록 선택할 수 있습니다. DBFS를 사용하여 프로덕션 데이터를 저장하지 않는 것이 좋습니다.

참고: 일부 Azure Databricks 구독에서는 이 기능을 사용할 수 없습니다. 액세스를 요청하려면 Microsoft 또는 Databricks 계정 담당자에게 문의하세요.

* [Azure Databricks Notebook에 고객 관리형 키를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Azure Databricks 파일 시스템에 고객 관리형 키를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure 활동 로그에 Azure Monitor를 사용하여 중요한 Azure Databricks 작업 영역이 변경될 때 경고를 생성합니다.

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 컨트롤: 취약성 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 타사 취약성 관리 솔루션을 구현합니다.

취약성 관리 플랫폼 구독이 있으면 Azure Databricks 초기화 스크립트를 사용하여 Azure Databricks 클러스터 노드에 취약성 평가 에이전트를 설치하고 해당 포털을 통해 노드를 관리할 수 있습니다. 모든 타사 솔루션은 서로 다르게 작동합니다.

* [Rapid7 Agent를 수동으로 설치하는 방법](https://insightagent.help.rapid7.com/docs/install)

* [Qualys Agent를 수동으로 설치하는 방법](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 초기화 스크립트](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: Azure Databricks 클러스터 노드 기본 이미지는 Microsoft가 유지 관리하지만 사용자의 클러스터 노드에 최신 패치를 적용하는 것은 사용자의 책임입니다. 실행 중인 기존 클러스터에 유지 관리 업데이트를 추가하려면 클러스터를 다시 시작해야 합니다.

* [Azure Databricks의 런타임 유지 관리 업데이트 이해](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: Azure Databricks 클러스터 노드 기본 이미지는 Microsoft가 유지 관리하지만 사용자가 설치한 타사 애플리케이션에 최신 패치를 적용하는 것은 사용자의 책임입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검색 비교

**지침**: 시간 경과에 따른 취약성 검사를 비교할 수 있는 타사 취약성 관리 솔루션을 구현합니다. 취약성 관리 구독이 있으면 해당 공급 업체의 포털을 사용하여 연속 취약성 검사를 살펴보고 비교할 수 있습니다. 모든 타사 솔루션은 서로 다르게 작동합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 일반적인 위험 채점 프로그램(예: Common Vulnerability Scoring System) 또는 타사 검사 도구에 제공된 기본 위험 등급을 사용합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 컨트롤: 인벤토리 및 자산 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure 자산 검색 사용

**지침**: Azure Resource Graph를 사용하여 구독 내 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트, 프로토콜 등)를 쿼리/검색합니다. 테넌트에서 적절한 (읽기) 권한이 있는지 확인하고 구독 내의 리소스뿐만 아니라 모든 Azure 구독을 열거합니다.

Resource Graph를 통해 클래식 Azure 리소스를 검색할 수 있지만 앞으로는 Azure Resource Manager 리소스를 만들어서 사용하는 것이 좋습니다.

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure 구독을 보는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적으로 분류 체계로 구성합니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독(해당하는 경우)을 사용하여 Azure 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고 권한이 없는 리소스가 제시간에 구독에서 삭제되도록 합니다.

또한, Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 컴퓨팅 리소스에 대해 승인된 Azure 리소스 및 승인된 소프트웨어를 정의합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당 없음. Azure Databricks는 PaaS 서비스이며 고객은 Azure Databricks 클러스터의 VM에 직접 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure Resource Graph를 사용하여 구독 내 Azure Databricks 인스턴스를 포함한 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트, 프로토콜 등)를 쿼리/검색합니다. 승인되지 않은 Azure 리소스가 검색되면 제거합니다. Azure Databricks 클러스터 노드의 경우 승인되지 않은 소프트웨어를 제거하거나 경고하는 타사 솔루션을 구현합니다.

* [Azure Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 없음. Azure Databricks는 PaaS 서비스이며 Azure Databricks 클러스터의 VM에 직접 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 애플리케이션 목록 구현

**지침**: 해당 없음. Azure Databricks는 PaaS 서비스이며 Azure Databricks 클러스터의 VM에 직접 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>스크립트를 통해 Azure Resource Manager와 상호 작용하는 사용자 기능 제한</div>

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

* [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 컴퓨팅 리소스 내에서 스크립트를 실행하는 사용자 기능 제한

**지침**: 해당 없음. 클러스터의 사용자(비관리자)가 작업을 실행하기 위해 개별 노드에 액세스할 필요가 없기 때문에 Azure Databricks에는 적용되지 않습니다. 클러스터 관리자는 기본적으로 모든 클러스터 노드에 대한 루트 액세스 권한이 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 고위험 애플리케이션을 물리적으로 분리하거나 논리적으로 분리

**지침**: 해당 없음. 벤치 마크는 Azure Apps Service 또는 웹 애플리케이션을 호스트하는 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 컨트롤: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용하여 Azure Databricks 인스턴스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.Databricks" 네임스페이스에 Azure Policy 별칭을 사용하여 Azure Databricks 인스턴스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 적용된 정책은 Databricks 관리되는 리소스 그룹에서 작동하지 않습니다.

* [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 없음. Azure Databricks는 PaaS 서비스이며 Azure Databricks 클러스터의 VM에 직접 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy를 사용하여 Azure Databricks 인스턴스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.Databricks" 네임스페이스에 Azure Policy 별칭을 사용하여 Azure Databricks 인스턴스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. Azure 리소스 전체에서 보안 설정을 적용하려면 Azure Policy [거부] 및 [없는 경우 배포]를 사용합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: Microsoft에서 관리하고 유지하는 Azure Databricks 운영 체제 이미지입니다. OS 수준 상태 구성을 구현하는 것은 사용자의 책임입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure DevOps 또는 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

* [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: Azure Databricks 클러스터 노드에 사용자 지정 이미지를 사용하는 경우 사용자 지정 기본 이미지를 ACR(Azure Container Registry)과 같은 Docker 레지스트리로 푸시합니다.

* [Databricks Container Service를 사용하여 컨테이너를 사용자 지정하는 방법](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Azure Container Registry 이해](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: Azure Policy를 사용하여 Azure Databricks 인스턴스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.Databricks" 네임스페이스에 Azure Policy 별칭을 사용하여 Azure Databricks 인스턴스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당 없음. Azure Databricks는 PaaS 서비스이며 Azure Databricks 클러스터의 VM에 직접 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Policy를 사용하여 Azure Databricks 인스턴스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.Databricks" 네임스페이스에 Azure Policy 별칭을 사용하여 Azure Databricks 인스턴스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다.

* [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Databricks 클러스터 노드 운영 체제의 상태를 모니터링하는 타사 솔루션을 구현합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Databricks 비밀 범위와 함께 Azure Key Vault를 사용하여 비밀을 안전하게 관리하고 사용합니다.

* [Azure Databricks에서 Azure Key Vault를 사용하는 방법](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 현재 사용할 수 없음. 현재 Azure Databricks에는 관리 ID를 사용할 수 없습니다.

* [Azure 리소스에 대한 관리 ID를 지원하는 서비스](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 자격 증명 스캐너를 사용하면 검색된 자격 증명을 Azure Key Vault와 같이 보다 안전한 위치로 이동하는 것이 좋습니다.

* [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 컨트롤: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리하는 맬웨어 방지 소프트웨어 사용

**지침**: 취약성 관리 플랫폼 구독이 있으면 Azure Databricks 초기화 스크립트를 사용하여 Azure Databricks 클러스터 노드에 취약성 평가 에이전트를 설치하고 해당 포털을 통해 노드를 관리할 수 있습니다. 모든 타사 솔루션은 서로 다르게 작동합니다.

* [Rapid7 Agent를 수동으로 설치하는 방법](https://insightagent.help.rapid7.com/docs/install)

* [Qualys Agent를 수동으로 설치하는 방법](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 초기화 스크립트](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft Antimalware는 Azure 서비스(예: Azure App Service)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 콘텐츠에서 실행되지 않습니다.

Azure Databricks 클러스터 노드나 관련 리소스에 업로드할 파일이 있으면 미리 검색합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 취약성 관리 플랫폼 구독이 있으면 Azure Databricks 초기화 스크립트를 사용하여 Azure Databricks 클러스터 노드에 취약성 평가 에이전트를 설치하고 해당 포털을 통해 노드를 관리할 수 있습니다. 모든 타사 솔루션은 서로 다르게 작동합니다.

* [Rapid7 Agent를 수동으로 설치하는 방법](https://insightagent.help.rapid7.com/docs/install)

* [Qualys Agent를 수동으로 설치하는 방법](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 초기화 스크립트](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 컨트롤: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기적으로 자동화된 백업 보장

**지침**: Azure Databricks 데이터 원본의 경우 사용 사례에 적합한 수준의 데이터 중복성을 구성했는지 확인해야 합니다. 예를 들어 Azure Databricks 데이터 저장소에 Azure Storage 계정을 사용하는 경우 적절한 중복 옵션(LRS, ZRS, GRS, RA-GRS)을 선택합니다.

* [Azure Databricks의 데이터 원본](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Azure Databricks 클러스터의 지역 재해 복구](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Azure Key Vault 내에 Azure Databricks 구현과 관련된 고객 관리형 키가 있으면 백업합니다. REST API 및 CLI를 사용하여 Databricks 구성 매일 백업을 만들 수 있습니다. REST API/CLI를 사용하여 Databricks 구성 매일 백업을 만들 수도 있습니다.

* [Azure에서 키 자격 증명 모음 키를 백업하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: Azure Databricks 구현과 관련이 있는 백업한 고객 관리형 키에 대한 복원을 테스트합니다.

* [Azure에서 키 자격 증명 모음 키를 복원하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Key Vault에서 일시 삭제를 사용하도록 설정하여 키가 실수로 또는 악의적으로 삭제되지 않도록 보호합니다.

* [Key Vault에서 일시 삭제를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 컨트롤: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 가이드 만들기

**지침**: 조직에 대한 인시던트 대응 가이드를 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다.

* [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 자체 인시던트 대응 계획을 만들 수도 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 채점 및 우선 순위 지정 절차 만들기

**지침**: Security Center는 각 경고에 심각도를 할당하여 어떤 경고를 먼저 조사해야 하는지 우선 순위를 지정합니다. 심각도는 경고를 실행하는 데 사용된 Security Center의 검색 또는 분석의 신뢰도와 악의적인 의도로 경고를 발생시킨 활동의 신뢰도 수준을 기준으로 합니다.

또한 구독을 명확하게 표시(예: 프로덕션, 비프로덕션)하고 Azure 리소스를 명확하게 식별 및 범주화하는 이름 지정 시스템을 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 대응 기능을 테스트하는 연습을 수행합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

* [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적인 또는 권한 없는 당사자가 고객 데이터에 액세스한 것을 발견하면 Microsoft는 보안 인시던트 담당자 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

* [Azure Security Center 보안 연락처를 설정하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 권장 사항을 수동 또는 지속적인 방식으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고 Sentinel을 스트리밍할 수 있습니다.

* [연속 내보내기를 구성하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export)

* [경고를 Azure Sentinel로 스트리밍하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

* [워크플로 자동화와 Logic Apps를 구성하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 컨트롤: 침투 테스트 및 레드 팀 연습](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 정기적인 침투 테스트를 수행하고 60일 이내에 모든 중요한 보안 결과를 수정합니다.

**지침**: * [Microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 레드 팀 및 라이브 사이트 침투 테스트 실행에 대한 자세한 내용은 ](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 참조하세요.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview) 참조
- [Azure Security 기준](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)에 대해 알아보기
