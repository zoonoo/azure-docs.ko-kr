---
title: Virtual Machine Scale Sets에 대한 Azure 보안 기준
description: Virtual Machine Scale Sets 보안 기준은 Azure Security 벤치마크에 지정된 보안 권장 사항을 구현하기 위한 절차 참고 자료와 리소스를 제공합니다.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2d902bbdc03596fe246fc36813895e72c53da05a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571412"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets에 대한 Azure 보안 기준

Virtual Machine Scale Sets에 대한 Azure 보안 기준에는 배포의 보안 태세를 개선하는 데 유용한 권장 사항이 포함되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](../security/benchmarks/security-baselines-overview.md)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**참고 자료**: Azure VM(가상 머신)을 만들 때 가상 네트워크를 만들거나 기존 가상 네트워크를 사용하여 서브넷으로 VM을 구성해야 합니다. 배포된 모든 서브넷에 애플리케이션의 신뢰할 수 있는 포트 및 원본에만 해당하는 네트워크 액세스 제어와 함께 적용되는 네트워크 보안 그룹이 있는지 확인합니다.

또는 중앙 집중식 방화벽에 대한 특정 사용 사례가 있는 경우 Azure Firewall을 사용하여 해당 요구 사항을 충족할 수도 있습니다.

* [Azure 가상 머신 확장 집합에 대한 네트워킹](./virtual-machine-scale-sets-networking.md)

* [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall을 배포 및 구성하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 및 기록

**참고 자료**: Azure Security Center를 사용하여 Azure에서 Azure VM(가상 머신) 리소스를 보호하는 데 도움이 되는 네트워크 보호 권장 사항을 식별하고 준수합니다. NSG 흐름 로그를 사용하도록 설정하고, VM의 수상한 활동에 대한 트래픽 감사를 위해 로그를 스토리지 계정에 보냅니다.

* [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Azure Security Center에서 제공하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**참고 자료**: VMSS(Virtual Machine Scale Set)를 사용하여 웹 애플리케이션을 호스트하는 경우 VMSS 서브넷의 NSG(네트워크 보안 그룹)를 통해 통신할 수 있는 네트워크 트래픽, 포트, 프로토콜을 제한합니다. 애플리케이션에 필요한 트래픽만 허용하도록 NSG를 구성하는 경우 최소 권한이 부여된 네트워크 접근 방식을 따릅니다.

들어오는 트래픽의 추가 검사를 위해 중요한 웹 애플리케이션 앞에 Azure WAF(Web Application Firewall)를 배포할 수 있습니다. WAF에 대한 진단 설정을 사용하도록 설정하고 로그를 스토리지 계정, Event Hub 또는 Log Analytics 작업 영역에서 수집합니다.

* [Azure 가상 머신 확장 집합에 대한 네트워킹](./virtual-machine-scale-sets-networking.md)

* [Azure Portal을 사용하여 웹 애플리케이션 방화벽이 있는 애플리케이션 게이트웨이를 만듭니다.](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**참고 자료**: 가상 네트워크에서 DDoS(배포된 서비스 거부) 표준 보호를 사용하도록 설정하여 DDoS 공격으로부터 보호합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 IP 주소와의 통신을 모니터링할 수 있습니다. 위협 인텔리전스를 사용하도록 설정하고 악의적인 네트워크 트래픽에 대해 “경고 및 거부”로 구성하여 각 가상 네트워크 세그먼트에서 Azure Firewall을 구성합니다.

Azure Security Center의 Just-in-time 네트워크 액세스를 사용하여 제한된 기간 동안 Windows Virtual Machines의 노출을 승인된 IP 주소로 제한할 수 있습니다. 또한 Azure Security Center 적응형 네트워크 강화를 사용하여 실제 트래픽 및 위협 인텔리전스에 따라 포트와 원본 IP를 제한하는 NSG 구성을 권장합니다.

* [DDoS 보호를 구성하는 방법](../ddos-protection/manage-ddos-protection.md)

* [Azure Firewall을 배포하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Security Center 통합 위협 인텔리전스 이해](../security-center/azure-defender.md)

* [Azure Security Center 적응형 네트워크 강화 이해](../security-center/security-center-adaptive-network-hardening.md)

* [Azure Security Center Just-in-time 네트워크 Access Control 이해](../security-center/security-center-just-in-time.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**참고 자료**: Azure Virtual Machines에 대한 흐름 레코드를 생성하기 위해 NSG 흐름 로그를 스토리지 계정에 기록할 수 있습니다. 비정상적인 활동을 조사할 때 비정상적이고 예기치 않은 활동에 대한 네트워크 트래픽을 검토할 수 있도록 Network Watcher 패킷 캡처를 사용하도록 설정할 수 있습니다.

* [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**참고 자료**: Network Watcher에서 제공하는 패킷 캡처와 오픈 소스 IDS 도구를 결합하여 광범위한 위협에 대해 네트워크 침입 검색을 수행할 수 있습니다. 또한 위협 인텔리전스를 사용하도록 설정하고 악의적인 네트워크 트래픽에 대해 “경고 및 거부”로 구성하여 가상 네트워크 세그먼트에 Azure Firewall을 적절하게 배포할 수 있습니다.

* [Network Watcher 및 오픈 소스 도구를 사용하여 네트워크 침입 검색 수행](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Azure Firewall을 배포하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Firewall을 사용하여 경고를 구성하는 방법](../firewall/threat-intel.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**참고 자료**: VMSS(Virtual Machine Scale Set)를 사용하여 웹 애플리케이션을 호스트하는 경우 신뢰할 수 있는 인증서에 대해 HTTPS/SSL을 사용하도록 설정된 웹 애플리케이션용 Azure Application Gateway를 배포할 수 있습니다. Azure Application Gateway를 통해 수신기를 포트에 할당하고, 규칙을 만들고, VMSS 등의 백 엔드 풀에 리소스를 추가하여 애플리케이션 웹 트래픽을 특정 리소스로 보냅니다.

* [Application Gateway를 배포하는 방법](../application-gateway/quick-create-portal.md)

* [HTTPS를 사용하도록 Application Gateway를 구성하는 방법](../application-gateway/create-ssl-portal.md)

* [Application Gateway를 참조하는 확장 집합 만들기](./virtual-machine-scale-sets-networking.md#create-a-scale-set-that-references-an-application-gateway)

* [Azure 웹 Application Gateway를 사용한 계층 7 부하 분산 이해](../application-gateway/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**참고 자료**: 가상 네트워크 서비스 태그를 사용하여 네트워크 보안 그룹에 대한 네트워크 액세스 제어 또는 Azure 가상 머신에 대하여 구성된 Azure Firewall을 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

* [서비스 태그 이해 및 사용](../virtual-network/service-tags-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**참고 자료**: Azure Policy를 사용하여 Azure Virtual Machine Scale Sets에 대한 표준 보안 구성을 정의하고 구현합니다. 또한 Azure Blueprints를 사용하여 주요 환경 아티팩트(예: Azure Resource Manager 템플릿, 역할 할당 및 Azure Policy 할당)를 단일 청사진 정의로 패키징하고 대규모 Azure VM 배포를 간소화할 수 있습니다. 청사진을 구독에 적용하고 청사진 버전 관리를 통해 리소스 관리를 사용하도록 설정할 수 있습니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [가상 머신 확장 집합 템플릿에 대해 알아보기](./virtual-machine-scale-sets-mvss-start.md)

* [네트워킹을 위한 Azure Policy 샘플](../governance/policy/samples/built-in-policies.md#network)

* [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**참고 자료**: NSG(네트워크 보안 그룹) 및 Windows 가상 머신에 대해 구성된 네트워크 보안 및 트래픽 흐름과 관련된 기타 리소스에 대한 태그를 사용할 수 있습니다. 개별 NSG 규칙의 경우 “설명” 필드를 사용하여 네트워크에서 주고받는 트래픽을 허용하는 모든 규칙의 비즈니스 요구 사항 및/또는 기간을 지정합니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

* [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**참고 자료**: Azure 활동 로그를 사용하여 Azure Virtual Machine Scale Set와 관련된 네트워크 리소스 구성에 대한 변경 내용을 모니터링합니다. Azure Monitor 내에서 중요한 네트워크 설정 또는 리소스가 변경되면 트리거되는 경고를 만듭니다.

Azure Policy를 사용하여 Virtual Machine Scale Set와 관련된 네트워크 리소스에 대한 구성을 확인(및/또는 재구성)합니다.

* [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [네트워킹을 위한 Azure Policy 샘플](../governance/policy/samples/built-in-policies.md#network)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**참고 자료**: Azure 리소스에 대한 시간 원본은 Microsoft에서 유지 관리되지만 Virtual Machines에 대한 시간 동기화 설정은 사용자가 관리할 수 있습니다.

* [Azure Windows 컴퓨팅 리소스에 대한 시간 동기화를 구성하는 방법](../virtual-machines/windows/time-sync.md)

* [Azure Linux 컴퓨팅 리소스에 대한 시간 동기화를 구성하는 방법](../virtual-machines/linux/time-sync.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**참고 자료**: 활동 로그를 사용하여 Virtual Machine Scale Set 리소스에서 수행되는 작업 및 동작을 감사할 수 있습니다. 활동 로그에는 읽기 작업(가져오기)을 제외한 리소스에 대한 모든 쓰기 작업(삽입, 게시, 삭제)이 포함됩니다. 활동 로그를 사용하여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

Azure 활동 로그 또는 가상 머신 리소스에서 생성된 온보딩 로그 데이터를 Azure Sentinel 또는 중앙 보안 로그 관리에 대한 타사 SIEM으로 사용하도록 설정할 수 있습니다.

Azure Security Center를 사용하여 Azure Virtual Machines에 대한 보안 이벤트 로그 모니터링을 제공합니다. 보안 이벤트 로그가 생성하는 데이터의 양을 고려할 때 이는 기본적으로 저장되지 않습니다.

조직에서 가상 머신의 보안 이벤트 로그 데이터를 유지하려는 경우 Azure Security Center 내에 구성된 원하는 데이터 수집 계층의 Log Analytics 작업 영역에 저장할 수 있습니다.

* [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

* [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

* [Azure Security Center에서 데이터 수집](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Azure에서 가상 머신을 모니터링하는 방법](../azure-monitor/vm/monitor-vm-azure.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**참고 자료**: 활동 로그를 사용하여 Virtual Machine Scale Set 리소스에서 수행되는 작업 및 동작을 감사할 수 있습니다. 활동 로그에는 읽기 작업(가져오기)을 제외한 리소스에 대한 모든 쓰기 작업(삽입, 게시, 삭제)이 포함됩니다. 활동 로그를 사용하여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

VM(Virtual Machines)에 진단 확장을 배포하여 게스트 OS 진단 데이터 수집을 사용하도록 설정합니다. 진단 확장을 사용하여 Azure 가상 머신에서 애플리케이션 로그 또는 성능 카운터와 같은 진단 데이터를 수집할 수 있습니다.

Azure Virtual Machine Scale Set에서 지원하는 애플리케이션과 서비스의 고급 표시를 위해 VM용 Azure Monitor와 Application Insights를 모두 사용하도록 설정할 수 있습니다. Application Insights를 사용하여 애플리케이션을 모니터링하고 HTTP 요청, 예외 등의 원격 분석을 캡처할 수 있으므로 VM과 애플리케이션 간의 문제를 상호 연결할 수 있습니다.

* [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md)

* [Azure 활동 로그 이벤트를 보고 검색하기](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Azure에서 가상 머신을 모니터링하는 방법](../azure-monitor/vm/monitor-vm-azure.md)

* [Application Insights 개요](../azure-monitor/app/app-insights-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**참고 자료**: Azure Security Center를 사용하여 Azure Virtual Machines에 대한 보안 이벤트 로그 모니터링을 제공합니다. 보안 이벤트 로그가 생성하는 데이터의 양을 고려할 때 이는 기본적으로 저장되지 않습니다.

조직에서 가상 머신의 보안 이벤트 로그 데이터를 유지하려는 경우 Azure Security Center 내에 구성된 원하는 데이터 수집 계층의 Log Analytics 작업 영역에 저장할 수 있습니다.

* [Azure Security Center에서 데이터 수집](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Azure에서 가상 머신을 모니터링하는 방법](../azure-monitor/vm/monitor-vm-azure.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**참고 자료**: 가상 머신 로그를 저장하는 데 사용되는 스토리지 계정 또는 Log Analytics 작업 영역에 조직의 준수 규정에 따라 설정된 로그 보존 기간이 있는지 확인합니다.

* [Azure에서 가상 머신을 모니터링하는 방법](../azure-monitor/vm/monitor-vm-azure.md)

* [Log Analytics 작업 영역 보존 기간을 구성하는 방법](../azure-monitor/logs/manage-cost-storage.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**참고 자료**: 로그를 분석하고 모니터링하여 비정상 동작이 있는지 확인하고 결과를 정기적으로 검토합니다. Azure Monitor를 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다.

또는 로그를 모니터링하고 검토하기 위해 데이터를 사용하도록 설정하고 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

* [Log Analytics 작업 영역 이해](../azure-monitor/logs/log-analytics-tutorial.md)

* [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**참고 자료**: Azure Virtual Machines에 대한 보안 로그 및 이벤트에 있는 비정상적인 활동을 모니터링하고 경고하기 위해 Log Analytics 작업 영역으로 구성된 Azure Security Center를 사용합니다.

또는 Azure Sentinel이나 타사 SIEM에 대한 온보딩 데이터를 사용하도록 설정하여 비정상 활동에 대한 경고를 설정할 수 있습니다.

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

* [Azure Security Center에서 경고를 관리하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

* [로그 분석 로그 데이터에 관해 경고하는 방법](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**참고 자료**: Azure Cloud Services 및 Virtual Machines를 위한 Microsoft Anti-malware를 사용하고 Windows Virtual Machines를 구성하여 Azure Storage 계정에 이벤트를 로그하도록 할 수 있습니다. 스토리지 계정에서 이벤트를 수집하고 해당하는 경우 경고를 만들도록 Log Analytics 작업 영역을 구성합니다. Azure Security Center “컴퓨팅 &amp; 앱”의 권장 사항을 따릅니다. Linux Virtual Machines의 경우 맬웨어 방지 취약성 검색을 위한 타사 도구가 필요합니다.

* [Cloud Services 및 Virtual Machines를 위한 Microsoft Anti-malware를 구성하는 방법](../security/fundamentals/antimalware.md)

* [Virtual Machines에 대한 게스트 수준 모니터링을 사용하도록 설정하는 방법](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

* [Azure Security Center에 Linux 서버를 온보딩하는 방법에 대한 지침](../security-center/quickstart-onboard-machines.md)

* [다음 링크는 선택한 취약성 소프트웨어의 조건 목록으로 사용할 수 있는 Microsoft 권장 보안 지침을 제공합니다](../virtual-machines/security-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**참고 자료**: DNS 로깅 솔루션에 대한 Azure Marketplace에서 조직의 요구에 따라 타사 솔루션을 구현합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**참고 자료**: Azure Security Center는 Azure VM(가상 머신)에 대한 보안 이벤트 로그 모니터링을 제공합니다. Security Center는 지원되는 모든 Azure VM 및 자동 프로비저닝을 사용하도록 설정된 경우에 만들어지는 새 VM에 대한 Microsoft Monitoring Agent를 프로비저닝하거나 또는 에이전트를 수동으로 설치할 수 있습니다. 에이전트는 프로세스 생성 이벤트 4688 및 이벤트 4688 내의 CommandLine 필드를 활성화합니다. VM에서 생성된 새로운 프로세스는 이벤트 로그에서 기록되고 Security Center의 검색 서비스에 의해 모니터링됩니다.

Linux Virtual Machines의 경우 노드 단위로 콘솔 로그온을 수동으로 구성하고 syslog를 사용하여 데이터를 저장할 수 있습니다. 또한 Azure Monitor의 Log Analytics 작업 영역을 사용하여 Azure Virtual Machines의 syslog 데이터에서 로그를 검토하고 쿼리를 수행합니다.

* [Azure Security Center에서 데이터 수집](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/logs/get-started-queries.md)

* [Azure Monitor의 Syslog 데이터 원본](../azure-monitor/agents/data-sources-syslog.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**참고 자료**: Azure Active Directory는 사용자 액세스를 관리할 수 있는 권장 방법이지만, Azure Virtual Machines에는 로컬 계정이 있을 수 있습니다. 로컬 및 도메인 계정은 일반적으로 최소한의 공간을 사용하여 검토하고 관리해야 합니다. 또한 가상 머신 리소스에 액세스하는 데 사용되는 관리 계정에 대해 Azure Privileged Identity Management를 활용합니다.

* [로컬 계정에 대한 정보는 ](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)에서 확인할 수 있습니다.

* [Privileged Identity Manager에 대한 정보](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**참고 자료**: Azure Virtual Machine Scale Set 및 Azure Active Directory에는 기본 암호 개념이 없습니다. 기본 암호를 사용할 수 있는 타사 애플리케이션 및 마켓플레이스 서비스를 담당하는 고객입니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**참고 자료**: 가상 머신에 액세스할 수 있는 전용 관리 계정의 사용에 대한 표준 운영 프로시저를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정의 수를 모니터링합니다. Azure Virtual Machine 리소스에 액세스하는 데 사용되는 모든 관리자 계정은 Azure PIM(Privileged Identity Management)에서 관리할 수도 있습니다. Azure Privileged Identity Management는 역할을 가정하기 전에 다단계 인증을 필요로 하는 Just-in-Time 권한 상승 및 위임과 같은 몇 가지 옵션을 제공하여, 특정한 시간 프레임에만, 그리고 승인자가 있어야 권한을 사용할 수 있습니다.

* [Azure Security Center ID 및 액세스 이해](../security-center/security-center-identity-access.md)

* [Privileged Identity Manager에 대한 정보](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory SSO(Single Sign-On) 사용

**참고 자료**: 가능하면 서비스별로 개별 독립 실행형 자격 증명을 구성하는 대신 Azure Active Directory로 SSO를 사용합니다. Azure Security Center ID 및 액세스 관리 권장 사항을 사용합니다.

* [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD MFA를 사용하도록 설정하고 Azure Security Center ID 및 액세스 관리 권장 사항을 따릅니다.

* [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에 안전한 Azure 관리 워크스테이션 사용

**지침**: MFA가 구성된 PAW(Privileged Access Workstation)를 사용하여 Azure 리소스에 로그인하고 구성합니다.

* [권한 있는 액세스 워크스테이션에 대해 알아보기](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**참고 자료**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 Azure AD PIM(Privileged Identity Management)을 사용하여 로그 및 경고를 생성할 수 있습니다. Azure AD 위험 검색을 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다. 필요에 따라 고객은 Azure Security Center 위험 검색 경고를 Azure Monitor에 수집하고 작업 그룹을 사용하여 사용자 지정 경고/알림을 구성할 수 있습니다.

* [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure Security Center 위험 탐지 이해(의심스러운 활동)](../active-directory/identity-protection/overview-identity-protection.md)

* [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [사용자 지정 경고 및 알림에 대한 작업 그룹을 구성하는 방법](../azure-monitor/alerts/action-groups.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**참고 자료**: Azure Active Directory 조건부 액세스 정책 및 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.

* [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**참고 자료**: Azure AD(Azure Active Directory)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다. 관리 ID를 사용하면 코드 내 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다. 가상 머신에 실행되는 코드는 관리 ID를 사용하여 Azure AD 인증을 지원하는 서비스용 액세스 토큰을 요청할 수 있습니다.

* [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory-domain-services/tutorial-create-instance.md)

* [Azure 리소스에 대한 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD는 부실 계정을 검색하는 데 유용한 로그를 제공합니다. 또한 Azure Active Directory ID 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다. Azure Virtual Machines를 사용하는 경우 로컬 보안 그룹 및 사용자를 검토하여 시스템을 손상시킬 수 있는 예기치 않은 계정이 없는지 확인해야 합니다.

* [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**참고 자료**: Azure Active Directory에 대한 진단 설정을 구성하여 감사 로그 및 로그인 로그를 Log Analytics 작업 영역에 보냅니다. 또한 Azure Monitor를 사용하여 Azure Virtual Machines에서 로그 데이터에 대한 로그를 검토하고 쿼리를 수행합니다.

* [Log Analytics 작업 영역 이해](../azure-monitor/logs/log-analytics-tutorial.md)

* [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/logs/get-started-queries.md)

* [Azure에서 가상 머신을 모니터링하는 방법](../azure-monitor/vm/monitor-vm-azure.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**참고 자료**: Azure Active Directory의 위험 및 ID 보호 기능을 사용하여 스토리지 계정 리소스와 관련하여 검색된 의심스러운 동작에 대한 자동 응답을 구성합니다. 조직의 보안 대응을 구현하기 위해 Azure Sentinel을 통해 자동화된 대응을 사용하도록 설정해야 합니다.

* [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

* [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**참고 자료**: Microsoft에서 고객 데이터에 액세스해야 하는 지원 시나리오에서(예: 지원 요청 시) Azure Virtual Machines에 대한 고객 Lockbox를 사용하여 고객 데이터 액세스 요청을 검토하고 승인하거나 거부합니다.

* [고객 Lockbox 이해](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**참고 자료**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 가상 머신을 추적할 수 있도록 지원합니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 리소스는 가상 네트워크/서브넷으로 구분되고, 적절하게 태그가 지정되고, NSG(네트워크 보안 그룹) 또는 Azure Firewall 내에서 보호되어야 합니다. 중요한 데이터를 Virtual Machines에 저장하거나 처리하는 경우 사용하지 않을 때 해제하도록 하는 정책 및 프로시저를 구현합니다.

* [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

* [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

* [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall을 배포하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Firewall을 사용하여 경고 또는 거부를 구성하는 방법](../firewall/threat-intel.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**참고 자료**: 네트워크 경계에서 타사 솔루션을 구현하여 중요한 정보에 대한 무단 전송을 모니터링하고 정보 보안 전문가에게 경고하는 동안 해당 전송을 차단합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출로부터 보호합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

* [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**참고 자료**: Windows를 실행하는 VM(Virtual Machines) 간에 전송되는 데이터는 RDP 또는 SSH 세션에서 VM에 연결할 때와 같은 연결의 특성에 따라 여러 가지 방법으로 암호화됩니다.

Microsoft는 클라우드 서비스와 고객 간에 이동할 때 TLS(전송 계층 보안) 프로토콜을 사용하여 데이터를 보호합니다.

* [VM의 전송 중 암호화](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**참고 자료**: 타사 활성 검색 도구를 사용하여 현장 또는 원격 서비스 공급자를 비롯하여 조직의 기술 시스템에서 저장, 처리 또는 전송하는 중요한 정보를 모두 식별하고 조직의 중요한 정보 인벤토리를 업데이트합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**참고 자료**: Azure RBAC(역할 기반 액세스 제어)를 사용하면 팀 내에서 업무를 분리하고 VM(가상 머신)에서 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다. VM에서 모든 사람에게 무제한 권한을 제공하는 대신 특정 작업만 허용할 수 있습니다. Azure CLI 또는 Azure PowerShell을 사용하여 Azure Portal에서 VM에 대한 액세스 제어를 구성할 수 있습니다.

* [Azure RBAC](../role-based-access-control/overview.md)

* [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**참고 자료**: 자동화된 호스트 기반 데이터 손실 방지 솔루션과 같은 타사 도구를 구현하여 데이터 위반 위험을 완화하는 액세스 제어를 적용합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**참고 자료**: VM(Virtual Machines)의 가상 디스크는 서버 쪽 암호화 또는 ADE(Azure Disk Encryption)를 사용하여 미사용 상태로 암호화됩니다. Azure Disk Encryption은 Linux의 DM-Crypt 기능을 활용하여 게스트 VM 내에서 고객 관리형 키를 사용하여 관리 디스크를 암호화합니다. 고객 관리형 키를 사용하는 서버 쪽 암호화는 스토리지 서비스의 데이터를 암호화하여 VM에 대한 모든 OS 유형 및 이미지를 사용할 수 있도록 설정하여 ADE에서 향상됩니다.

* [Virtual Machine Scale Sets를 위한 Azure Disk Encryption](./disk-encryption-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**참고 자료**: Azure 활동 로그와 Azure Monitor를 사용하여 Virtual Machine Scale Sets 및 관련 리소스에 변경 내용이 있을 때 경고를 만듭니다.

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

* [Azure Storage 분석 로깅](../storage/common/storage-analytics-logging.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**참고 자료**: Azure Virtual Machines에 대한 취약성 평가를 수행하는 Azure Security Center의 권장 사항을 따릅니다. 권장되는 Azure 보안 또는 타사 솔루션을 사용하여 가상 머신에 대한 취약성 평가를 수행합니다.

* [Azure Security Center 취약성 평가 권장 사항을 구현하는 방법](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**참고 자료**: 지원되는 운영 체제 버전에 대해 자동 OS 업그레이드를 사용하도록 설정하거나 Shared Image Gallery에 저장된 사용자 지정 이미지를 사용하도록 설정합니다.

* [Azure의 가상 머신 확장 집합에 대한 자동 OS 업그레이드](./virtual-machine-scale-sets-automatic-upgrade.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀을 위한 자동화된 패치 관리 솔루션 배포

**참고 자료**: VMSS(Azure Virtual Machine Scale Sets)는 자동 OS 이미지 업그레이드를 사용할 수 있습니다. VMSS의 기본 가상 머신에 대해 Azure DSC(Desired State Configuration) 확장을 사용할 수 있습니다. DSC는 VM이 온라인으로 전환되어 원하는 소프트웨어를 실행하도록 구성하는 데 사용합니다.

* [Azure DSC 확장에 Virtual Machine Scale Sets 사용](./virtual-machine-scale-sets-dsc.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검색 비교

**참고 자료**: 검사 결과를 일관된 간격으로 내보내고 결과를 비교하여 취약성이 수정되었는지 확인합니다. Azure Security Center에서 제안하는 취약성 관리 권장 사항을 사용하는 경우 고객은 선택한 솔루션의 포털로 피벗하여 기록 검사 데이터를 볼 수 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**참고 자료**: Azure Security Center에서 제공하는 기본 위험 등급(보안 점수)을 사용합니다.

* [Azure Security Center 보안 점수의 이해](../security-center/secure-score-security-controls.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**참고 자료**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(Virtual Machines 포함)를 쿼리하고 발견합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

* [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

* [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**참고 자료**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 분류법에 따라 논리적으로 구성합니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**참고 자료**: 적합할 경우 태그 지정, 관리 그룹, 별도의 구독을 사용하여 Virtual Machine Scale Sets 및 관련 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

* [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

* [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**참고 자료**: 조직 요구 사항에 따라 승인된 Azure 리소스 및 컴퓨팅 리소스에 대한 승인된 소프트웨어의 인벤토리를 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 이는 스토리지 계정을 사용하는 환경 등 높은 보안 기반 환경에서 유용할 수 있습니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**참고 자료**: Azure Automation은 워크로드와 리소스의 배포, 작업, 해제 중에 완벽한 제어 기능을 제공합니다. Azure Virtual Machine 인벤토리를 활용하여 Virtual Machines의 모든 소프트웨어에 대한 정보 수집을 자동화할 수 있습니다. 참고: 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간은 Azure Portal에서 사용할 수 있습니다. 설치 날짜 및 기타 정보에 대한 액세스 권한을 얻으려는 고객은 게스트 수준 진단을 사용하도록 설정하고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 가져와야 합니다.

현재 적응형 애플리케이션 제어는 Virtual Machine Scale Sets에 사용할 수 없습니다.

* [Azure Automation 소개](../automation/automation-intro.md)

* [Azure VM 인벤토리를 사용하도록 설정하는 방법](../automation/automation-tutorial-installed-software.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**참고 자료**: Azure Automation은 워크로드와 리소스의 배포, 작업, 해제 중에 완벽한 제어 기능을 제공합니다. 변경 내용 추적을 사용하여 Virtual Machines에 설치된 모든 소프트웨어를 식별할 수 있습니다. 사용자 고유의 프로세스를 구현하거나 인증되지 않은 소프트웨어를 제거하기 위해 Azure Automation State Configuration을 사용할 수 있습니다.

* [Azure Automation 소개](../automation/automation-intro.md)

* [변경 내용 추적 솔루션으로 사용자 환경의 변경 내용 추적](../automation/change-tracking/overview.md)

* [Azure Automation State Configuration 개요](../automation/automation-dsc-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**참고 자료**: 현재 적응형 애플리케이션 제어는 Virtual Machine Scale Sets에 대하여 사용할 수 없습니다. 타사 소프트웨어를 사용하여 승인된 애플리케이션만 사용하도록 제어합니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/index.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인된 소프트웨어 타이틀의 인벤토리 유지 관리

**참고 자료**: 현재 적응형 애플리케이션 제어는 Virtual Machine Scale Sets에 대하여 사용할 수 없습니다. 조직의 요구 사항에 맞지 않는 경우 타사 솔루션을 구현합니다.

* [Azure Security Center 적응형 애플리케이션 제어를 사용하는 방법](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

* [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**참고 자료**: 스크립트 유형에 따라 운영 체제별 구성 또는 타사 리소스를 사용하여 Azure 컴퓨팅 리소스 내에서 스크립트를 실행하는 사용자의 기능을 제한할 수 있습니다.

* [Windows 환경에서 PowerShell 스크립트 실행을 제어하는 방법](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**참고 자료**: Azure 환경에 배포된 위험도 높은 애플리케이션은 가상 네트워크, 서브넷, 구독, 관리 그룹 등을 사용하여 격리될 수 있으며, Azure Firewall, WAF(Web Application Firewall) 또는 NSG(네트워크 보안 그룹)를 사용하여 충분히 안전하게 보호할 수 있습니다.

* [Azure의 가상 네트워크 및 가상 머신](../virtual-machines/network-overview.md)

* [Azure Firewall 개요](../firewall/overview.md)

* [웹 애플리케이션 방화벽 개요](../web-application-firewall/overview.md)

* [네트워크 보안 개요](../virtual-network/network-security-groups-overview.md)

* [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)

* [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md)

* [구독 관련 결정 가이드](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**참고 자료**: Azure Policy 또는 Azure Security Center를 사용하여 모든 Azure 리소스에 대한 보안 구성을 유지 관리합니다. 또한 Azure Resource Manager는 구성이 회사의 보안 요구 사항을 충족하거나 초과하는지 확인하기 위해 검토해야 하는 JSON(JavaScript Object Notation)에서 템플릿을 내보낼 수 있습니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [VM 템플릿을 다운로드하는 방법에 대한 정보](/previous-versions/azure/virtual-machines/windows/download-template)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**참고 자료**: 모든 컴퓨팅 리소스에 대한 보안 구성을 유지 관리하려면 Azure Security Center 권장 사항[Virtual Machines 보안 구성의 취약성 수정]을 사용합니다.

* [Azure Security Center 권장 사항을 모니터링하는 방법](../security-center/security-center-recommendations.md)

* [Azure Security Center 권장 사항을 수정하는 방법](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**참고 자료**: Azure Resource Manager 템플릿 및 Azure 정책을 사용하여 Virtual Machine Scale Sets에 연결된 Azure 리소스를 안전하게 구성합니다. Azure Resource Manager 템플릿은 Azure 리소스와 함께 가상 머신을 배포하는 데 사용되는 JSON 기반 파일이며 사용자 지정 템플릿은 유지 관리해야 합니다. Microsoft는 기본 템플릿에 대한 유지 관리를 수행합니다. Azure 리소스 전체에서 보안 설정을 적용하려면 Azure Policy [거부] 및 [없는 경우 배포]를 사용합니다.

* [Azure Resource Manager 템플릿 만들기에 대한 정보](../virtual-machines/windows/ps-template.md)

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**참고 자료**: 배포를 위해 Azure VM(Virtual Machines)에 대한 보안 구성을 유지 관리할 수 있는 다음의 몇 가지 옵션이 있습니다.

1. Azure Resource Manager 템플릿: Azure Portal에서 VM을 배포하는 데 사용되는 JSON 기반 파일로, 사용자 지정 템플릿을 유지 관리해야 합니다. Microsoft는 기본 템플릿에 대한 유지 관리를 수행합니다.

2. 사용자 지정 VHD(가상 하드 디스크): 다른 방법을 통해 관리할 수 없는 복잡한 환경을 처리할 때와 같이 사용자 지정 VHD 파일을 사용해야 하는 경우도 있습니다.

3. Azure Automation State Configuration: 기본 OS가 배포되면 이를 사용하여 설정을 보다 세부적으로 제어하고 자동화 프레임워크를 통해 적용할 수 있습니다.

대부분의 시나리오에서 Azure Automation Desired State Configuration과 결합된 Microsoft 기본 VM 템플릿은 보안 요구 사항을 충족하고 유지 관리하는 데 도움이 될 수 있습니다.

* [VM 템플릿을 다운로드하는 방법에 대한 정보](/previous-versions/azure/virtual-machines/windows/download-template)

* [ARM 템플릿 만들기에 대한 정보](../virtual-machines/windows/ps-template.md)

* [Azure에 사용자 지정 VM VHD를 업로드하는 방법](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**참고 자료**: Azure DevOps를 사용하여 사용자 지정 Azure 정책, Azure Resource Manager 템플릿, Desired State Configuration 스크립트 등과 같은 코드를 안전하게 저장하고 관리합니다. 코드, 빌드 및 작업 추적과 같이 Azure DevOps에서 관리하는 리소스에 액세스하려면 해당 특정 리소스에 대한 사용 권한이 있어야 합니다. 대부분의 사용 권한은 사용 권한 및 액세스에 설명된 대로 기본 제공 보안 그룹을 통해 부여됩니다. Azure DevOps와 통합된 경우에는 Azure AD(Azure Active Directory)에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에, 또는 TFS와 통합된 경우에는 Active Directory에 사용 권한을 부여하거나 거부할 수 있습니다.

* [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**참고 자료**: 사용자 지정 이미지(예: 가상 하드 디스크)를 사용하는 경우 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다.

* [Azure RBAC 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Azure RBAC를 구성하는 방법](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**참고 자료**: Azure Policy를 활용하여 가상 머신에 대한 시스템 구성을 경고, 감사 및 적용합니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구 배포

**참고 자료**: Azure Automation State Configuration은 클라우드나 온-프레미스 데이터 센터의 DSC(Desired State Configuration) 노드에 대한 구성 관리 서비스입니다. 안전한 중앙 위치에서 수천 대의 컴퓨터를 빠르고 간편하게 확장할 수 있습니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다.

* [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](../automation/automation-dsc-onboarding.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**참고 자료**: Azure Security Center를 활용하여 Azure Virtual Machines에 대한 기준 검사를 수행합니다. 자동화된 구성을 하는 다른 방법으로는 Azure Automation State Configuration을 사용하는 것이 있습니다.

* [Azure Security Center에서 권장 사항을 수정하는 방법](../security-center/security-center-remediate-recommendations.md)

* [Azure Automation 상태 구성 시작하기](../automation/automation-dsc-getting-started.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**참고 자료**: Azure Automation State Configuration은 클라우드나 온-프레미스 데이터 센터의 DSC(Desired State Configuration) 노드에 대한 구성 관리 서비스입니다. 안전한 중앙 위치에서 수천 대의 컴퓨터를 빠르고 간편하게 확장할 수 있습니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다.

* [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](../automation/automation-dsc-onboarding.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**참고 자료**: Azure Key Vault와 함께 관리 서비스 ID를 사용하여 클라우드 애플리케이션에 대한 비밀 관리를 간소화하고 보호합니다.

* [Azure 관리 ID와 통합하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Key Vault를 만드는 방법](../key-vault/general/quick-create-portal.md)

* [Key Vault에 인증하는 방법](../key-vault/general/authentication.md)

* [Key Vault 액세스 정책을 할당하는 방법](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**참고 자료**: 관리 ID를 사용하여 Azure AD에서 자동 관리 ID를 Azure 서비스에 제공합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

* [관리 ID를 구성하는 방법](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

* [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리형 맬웨어 방지 소프트웨어 사용

**참고 자료**: Azure Windows 가상 머신에 대한 Microsoft Antimalware를 사용하여 리소스를 지속적으로 모니터링하고 방어할 수 있습니다. Azure Linux Virtual Machines에서 맬웨어 방지 보호를 위한 타사 도구가 필요합니다.

* [Cloud Services 및 Virtual Machines에 대해 Microsoft Antimalware를 구성하는 방법](../security/fundamentals/antimalware.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**참고 자료**: Azure Virtual Machines는 컴퓨팅 리소스이므로 적용되지 않습니다.

**Azure Security Center 모니터링**: 예

**책임**: 해당 없음

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**참고 자료**: Windows Virtual Machines에 배포하는 경우 Azure용 Microsoft Antimalware는 기본적으로 최신 서명, 플랫폼 및 엔진 업데이트를 자동으로 설치합니다. Azure Security Center에서 “컴퓨팅 &amp; 앱”의 권장 사항에 따라 모든 엔드포인트가 최신 서명으로 최신 상태를 유지하도록 합니다. Azure Security Center와 통합되는 Microsoft Defender Advanced Threat Protection 서비스를 사용하여 바이러스 또는 맬웨어 기반 공격의 위험을 제한하는 추가 보안으로 Windows OS를 추가로 보호할 수 있습니다.

Azure Linux Virtual Machines에서 맬웨어 방지 보호를 위한 타사 도구가 필요합니다.

* [Azure Cloud Services 및 Virtual Machines용 Microsoft Antimalware 배포 방법](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

* [Cloud Services 및 Virtual Machines에 대해 Microsoft Antimalware를 구성하는 방법](../virtual-machines/security-recommendations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**참고 자료**: PowerShell 또는 REST API를 사용하여 인스턴스에 연결된 Azure Virtual Machine Scale Set 인스턴스 또는 관리 디스크의 스냅샷을 만듭니다. Azure Automation을 사용하여 정기적으로 백업 스크립트를 실행할 수도 있습니다.

* [Virtual Machine Scale Set 인스턴스 및 관리 디스크의 스냅샷을 만드는 방법](./virtual-machine-scale-sets-faq.md#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Azure Automation에 대한 소개](../automation/automation-intro.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**참고 자료**: PowerShell 또는 REST API를 사용하여 Azure 가상 머신 또는 해당 인스턴스에 연결된 관리 디스크의 스냅샷을 만듭니다. Azure Key Vault 내에서 고객 관리형 키를 백업합니다.

원하는 주파수와 보존 기간뿐만 아니라 Azure Backup 및 대상 Azure VM(Virtual Machines)을 사용하도록 설정합니다. 여기에는 전체 시스템 상태 백업이 포함됩니다. Azure Disk Encryption을 사용하는 경우 Azure VM 백업은 고객 관리형 키의 백업을 자동으로 처리합니다.

* [암호화를 사용하는 Azure VM에 백업](../backup/backup-azure-vms-encryption.md)

* [Azure VM 백업 개요](../backup/backup-azure-vms-introduction.md)

* [Virtual Machine Scale Set 인스턴스 및 관리 디스크의 스냅샷을 만드는 방법](./virtual-machine-scale-sets-faq.md#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**참고 자료**: Azure Backup 내에서 관리 디스크의 데이터 복원을 주기적으로 수행할 수 있는지 확인합니다. 필요한 경우 격리된 가상 네트워크 또는 구독에 대한 콘텐츠 복원을 테스트합니다. 백업된 고객 관리형 키의 복원을 고객이 테스트합니다.

Azure Disk Encryption을 사용하는 경우 디스크 암호화 키를 통해 Virtual Machine Scale Sets를 복원할 수 있습니다. 디스크 암호화를 사용하는 경우 디스크 암호화 키를 사용하여 Azure VM을 복원할 수 있습니다.

* [암호화를 사용하는 Azure VM에 백업](../backup/backup-azure-vms-encryption.md)

* [Azure에서 디스크 복원 및 복구된 VM 만들기](../backup/tutorial-restore-disk.md)

* [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Azure Virtual Machine Scale Sets에 대해 디스크 암호화를 사용하도록 설정하는 방법](./disk-encryption-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**참고 자료**: 잠금을 사용하여 관리 디스크에 대한 삭제 방지를 사용하도록 설정합니다. Key Vault에서 일시 삭제 및 제거 방지를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다.

* [예기치 않은 변경을 방지하기 위해 리소스 잠그기](../azure-resource-manager/management/lock-resources.md)

* [Azure Key Vault 일시 삭제 및 제거 방지 개요](../key-vault/general/soft-delete-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](../security/benchmarks/security-control-incident-response.md)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

* [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 고유한 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

* [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

* [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

* [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

* [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

* [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

* [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**참고 자료**: Azure 리소스를 보호하기 위해 Azure Security Center의 Workflow Automation 기능을 사용하여 “Logic Apps”를 통해 보안 경고 및 권장 사항에 대한 응답을 자동으로 트리거합니다.

* [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft 관리형 클라우드 인프라, 서비스, 애플리케이션에 대한 Microsoft의 Red Teaming 및 라이브 사이트 침투 테스트 전략과 실행을 사용합니다.

* [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.