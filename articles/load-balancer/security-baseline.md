---
title: Azure Load Balancer에 대한 Azure 보안 기준
description: Azure Load Balancer 보안 기준은 Azure Security Benchmark에 지정된 보안 추천 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 64e0a8cfcaf00c55038fbe5d1cdc7423b681b690
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589264"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure Load Balancer에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 지침을 Microsoft Azure Load Balancer에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure Security Benchmark에서 정의한 **보안 제어** 및 Azure Load Balancer에 적용되는 관련 지침에 따라 그룹화됩니다. Azure Load Balancer에 적용할 수 없는 **컨트롤** 은 제외되었습니다.

 
Azure Load Balancer가 Azure 보안 벤치마크에 완전히 매핑되는 방식을 확인하려면 [전체 Azure Load Balancer 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 내부 Azure Load Balancer를 사용하여 인터넷에 노출되지 않은 피어링된 가상 네트워크 또는 특정 가상 네트워크 내에서 백 엔드 리소스로의 트래픽만 허용합니다. 원본 네트워크를 사용하여 외부 Load Balancer 구현

주소 변환(SNAT)을 통해 직접적인 인터넷 노출을 방지하기 위해 백 엔드 리소스의 IP 주소를 가장합니다.

Azure는 두 가지 유형(표준 및 기본)의 Load Balancer 제품을 제공합니다. 표준 Load Balancer는 모든 프로덕션 워크로드에 사용합니다. 네트워크 보안 그룹을 구현하고 애플리케이션의 신뢰할 수 있는 포트 및 IP 주소 범위에 대한 액세스만 허용합니다. 백 엔드 서브넷 또는 백 엔드 가상 머신의 NIC에 할당된 네트워크 보안 그룹이 없으면, 부하 분산 장치에서 이러한 리소스로의 트래픽이 허용되지 않습니다. 표준 Load Balancer에서는 네트워크 보안 그룹으로 아웃바운드 NAT를 정의하는 아웃바운드 규칙을 제공합니다. 이러한 아웃바운드 규칙을 검토하여 아웃바운드 연결의 동작을 조정합니다.

프로덕션 워크로드에는 표준 Load Balancer를 사용하는 것이 좋으며, 일반적으로 기본 Load Balancer는 테스트에만 사용됩니다. 기본 유형은 기본적으로 인터넷 연결에 개방되어 있고 운영을 위해 네트워크 보안 그룹이 필요하지 않기 때문입니다.

- [Azure에서 아웃바운드 연결](load-balancer-outbound-connections.md)

- [Azure Public Load Balancer 업그레이드](upgrade-basic-standard.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure 보안 벤치마크](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초가 됩니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 NIC의 트래픽 및 구성을 모니터링 및 기록

**지침**: Load Balancer는 백 엔드 리소스에 적용되는 네트워크 보안 그룹 규칙과 인터넷 액세스를 제어하기 위해 구성된 아웃바운드 규칙에 의존하기 때문에 패스스루 서비스입니다.

Load Balancer의 아웃바운드 규칙 블레이드 및 암시적 아웃바운드 규칙을 활성화할 수 있는 부하 분산 규칙 블레이드를 통해 표준 Load Balancer에 대해 구성된 아웃바운드 규칙을 검토합니다.

아웃바운드 연결 수를 모니터링하여 리소스가 인터넷에 얼마나 자주 도달하는지 추적합니다. 

Security Center를 사용하고 네트워크 보호 권장 사항을 따르면 Azure 네트워크 리소스를 보호하는 데 유용합니다.

백 엔드 리소스에 대한 보안 권장 사항을 따르고 네트워크 보안 그룹 흐름 로그를 사용하도록 설정하고 감사를 위해 Azure Storage 계정으로 로그를 보냅니다.

또한 흐름 로그를 Log Analytics 작업 영역으로 보낸 다음, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 패턴에 대한 인사이트를 제공합니다. 트래픽 분석의 장점에는 네트워크 활동을 시각화하고, 핫 스폿과 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 네트워크 구성 오류를 정확히 찾아내는 기능이 있습니다.

- [네트워크 보안 그룹 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

- [Azure Security Center에서 제공하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

- [아웃바운드 연결 통계는 어떻게 확인하나요?](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure 보안 벤치마크](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초가 됩니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: Load Balancer를 사용하여 아웃바운드 규칙 및 네트워크 보안 그룹을 통해 인터넷 연결 및 유효한 원본 IP를 명시 적으로 정의하고 Microsoft의 위협 인텔리전스를 사용하여 웹 애플리케이션을 보호합니다.

- [Azure Firewall 통합](../firewall/integrate-lb.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Azure Virtual Network에서 Azure DDoS(분산 서비스 거부) 표준 보호를 사용하도록 설정하여 DDoS 공격으로부터 보호합니다. 

위협 인텔리전스 기반 필터링을 사용하도록 설정하고 악성 네트워크 트래픽에 대해 "경고 및 거부"하도록 구성된 조직의 각 네트워크 경계에 Azure Firewall을 배포합니다.

 

Security Center 위협 방지를 사용하여 알려진 악성 IP 주소와의 통신을 탐지합니다. 

표준 Load Balancer는 기본적으로 보안을 유지하도록 설계되었으며 프라이빗 및 격리된 Virtual Network의 일부입니다. 네트워크 보안 그룹에서 열지 않는 한 인바운드 흐름에 대해 닫혀 있어서 허용된 트래픽을 명시적으로 허용하고 알려진 악성 IP 주소는 허용하지 않습니다. 가상 머신 리소스의 NIC 또는 서브넷에 있는 네트워크 보안 그룹이 Load Balancer 뒤에 존재하지 않는 한 이러한 리소스에는 트래픽이 도달할 수 없습니다. 

위협 인텔리전스 기반 필터링을 사용하도록 설정하고 악성 네트워크 트래픽에 대해 "경고 및 거부"하도록 구성된 조직의 각 네트워크 경계에 Azure Firewall을 배포하여 악성 IP 주소의 공격을 방지합니다. 

Azure Firewall을 Load Balancer와 통합하기 위한 지침을 구현합니다.

Security Center 위협 방지 기능을 사용하여 알려진 악성 IP 주소와의 통신을 탐지합니다. 

Security Center(표준 계층)는 Just-In-Time 가상 머신 액세스를 제공하며 승인된 IP 주소/범위에서만 액세스가 가능하도록 허용된 원본 IP 주소를 구성합니다.
 

Security Center의 적응형 네트워크 강화 기능을 사용하여 실제 트래픽 및 위협 인텔리전스를 기반으로 포트 및 원본 IP를 제한하는 네트워크 보안 그룹 구성을 권장할 수 있습니다.
 

- [Azure Portal을 사용하여 Azure DDoS Protection 표준 관리](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall 위협 인텔리전스 기반 필터링](../firewall/threat-intel.md)

- [위협 방지 및 Azure Security Center](../security-center/azure-defender.md)

- [Just-In-Time 액세스를 사용하여 관리 포트 보호](../security-center/security-center-just-in-time.md)

- [Azure Security Center의 적응형 네트워크 강화](../security-center/security-center-adaptive-network-hardening.md)

- [Load Balancer와 Azure Firewall 통합](../firewall/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure 보안 벤치마크](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초가 됩니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: Network Watcher 패킷 캡처를 사용하도록 설정하여 비정상적인 활동을 조사합니다.

- [Network Watcher 인스턴스를 만드는 방법](../network-watcher/network-watcher-create.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure 보안 벤치마크](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초가 됩니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: 페이로드 검사 기능과 함께 IDS/IPS 기능을 지원하는 Azure Marketplace의 제품을 Load Balancer 환경에 구현합니다. 

페이로드 검사가 요구 사항이 아닌 경우 Azure Firewall 위협 인텔리전스를 사용합니다. Azure Firewall 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하거나 차단하는 데 사용됩니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

조직의 각 네트워크 경계에 선택한 방화벽 솔루션을 배포하여 악성 트래픽을 탐지 및/또는 차단합니다.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall을 배포하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall을 사용하여 경고를 구성하는 방법](../firewall/threat-intel.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: Load Balancer를 사용하여 아웃바운드 규칙 및 네트워크 보안 그룹을 통해 인터넷 연결 및 유효한 원본 IP를 명시 적으로 정의하고 Microsoft의 위협 인텔리전스 기능을 사용하여 웹 애플리케이션을 보호합니다.

- [Azure Firewall 통합](../firewall/integrate-lb.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용합니다. 규칙의 원본 또는 대상 필드에 서비스 태그 이름을 지정하여 해당 서비스에 대한 트래픽을 허용하거나 거부합니다. 

Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다. 

기본적으로, 모든 네트워크 보안 그룹은 상태 프로브 트래픽을 허용하기 위해 서비스 태그 AzureLoadBalancer를 포함합니다. 

네트워크 보안 그룹 규칙에 사용할 수 있는 모든 서비스 태그는 Azure 설명서를 참조하세요.

- [사용 가능한 서비스 태그](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Policy를 사용하여 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다.

Azure Blueprints에서 주요 환경 아티팩트(예: Azure Resource Manager 템플릿, Azure RBAC 제어 및 정책)를 단일 청사진 정의로 패키징하여 대규모 Azure 배포를 간소화합니다. 

청사진을 새로운 구독에 적용하고 버전 관리를 통해 제어 및 관리를 세부적으로 조정합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [네트워킹을 위한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: 네트워크 보안 그룹 및 네트워크 보안 및 트래픽 흐름과 관련된 기타 리소스에 리소스 태그를 사용합니다. 

"설명" 필드를 사용하여 개별 네트워크 보안 그룹 규칙에 대해 네트워크와 주고받는 트래픽을 허용하는 규칙을 문서화합니다.

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 구현하여 모든 리소스가 태그로 생성되도록 하고 태그가 없는 기존 리소스에 대해 알립니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기반으로 리소스를 조회하거나 작업을 수행합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Azure Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

- [네트워크 보안 그룹 규칙을 사용하여 네트워크 트래픽을 필터링하는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 리소스 구성을 모니터하고 Azure 리소스의 변경 내용을 검색합니다. 

Azure Monitor에서 경고를 만들어서 중요한 리소스가 변경될 때 알려줍니다.

- [Azure 활동 로그 이벤트를 살펴보고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 구독에서 활동 로그를 살펴보고 Load Balancer에 대한 아웃바운드 규칙 및 네트워크 보안 그룹의 변경 내용을 검토합니다. 

내부 호스트 로그를 살펴보고 백 엔드 리소스가 안전한지 확인합니다.

이러한 로그를 Log Analytics 또는 다른 스토리지 플랫폼으로 내보냅니다. Azure Monitor에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, 장기 및 보관 스토리지에 Azure Storage 계정을 사용합니다.

조직의 비즈니스 요구 사항에 따라 이러한 데이터를 Azure Sentinel 또는 타사 SIEM에 온보딩하고 사용하도록 설정합니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Monitor를 사용하여 Azure 가상 머신 내부 호스트 로그를 수집하는 방법](../azure-monitor/vm/quick-collect-azurevm.md)

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [플랫폼 활동 로그](../azure-monitor/essentials/activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 기본 Load Balancer에 대한 활동 로그에서 캡처한 제어 및 관리 평면 로깅 및 감사 정보를 검토합니다. 이러한 캡처 설정은 기본적으로 사용하도록 설정됩니다. 

활동 로그를 사용하여 리소스에 대한 작업을 모니터링하여 모든 활동과 상태를 살펴봅니다. 

활동 로그를 사용하여 구독의 리소스에 대해 수행된 작업을 확인합니다. 

- 작업을 시작한 사람
- 작업이 발생한 시간
- 작업의 상태

- 작업을 조사하는 데 도움이 될 수 있는 기타 속성 값

Azure PowerShell, Azure CLI(명령줄 인터페이스), Azure REST API 또는 Azure Portal을 통해 활동 로그에서 정보를 검색합니다. 

Azure Monitor를 사용하여 표준 Load Balancer 구성 기능에 대한 다차원 진단을 구현합니다.  여기에는 SNAT(원본 네트워크 주소 변환) 연결, 포트에 대한 정보를 포함하여 보안에 사용 가능한 메트릭이 포함됩니다. 또한 SYN(동기화) 패킷 및 패킷 카운터에 대한 메트릭도 사용할 수 있습니다. 

API를 통해 프로그래밍 방식으로 다차원 메트릭을 검색하고 '모든 메트릭' 옵션을 통해 스토리지 계정에 기록합니다.

Microsoft Power BI와 함께 Azure 감사 로그 콘텐츠 팩을 사용하여 미리 구성된 대시보드로 데이터를 분석하거나 요구 사항에 따라 보기를 사용자 지정합니다.

이벤트 허브 또는 Log Analytics 작업 영역으로 로그를 스트리밍합니다. Azure Blob Storage에서 추출하여 다양한 도구(예: Excel 및 Power BI)에서 볼 수도 있습니다. 

비즈니스 요구 사항에 따라 데이터를 사용하도록 설정하고 Azure Sentinel 또는 타사 SIEM에 온보딩합니다.

- [Resource Manager로 작업 감사에 자세히 설명되어 있는 각 방법에 대한 단계별 지침과 함께 이 문서를 검토하세요.](../azure-resource-manager/management/view-activity-logs.md)

- [공용 기본 Load Balancer용 Azure Monitor 로그](load-balancer-monitor-log.md)

- [활동 로그를 살펴보고 리소스에 대한 작업 모니터링](../azure-resource-manager/management/view-activity-logs.md)

- [API를 통해 프로그래밍 방식으로 다차원 메트릭 검색](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: 활동 로그는 기본적으로 사용하도록 설정되며 Azure의 이벤트 로그 저장소에 90일 동안 보존됩니다. Azure Monitor에서 조직의 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다. Azure Storage 계정을 장기 및 보관 스토리지에 사용합니다.

- [활동 로그를 살펴보고 리소스에 대한 작업 모니터링 문서](../azure-resource-manager/management/view-activity-logs.md)

- [Log Analytics에서 데이터 보존 기간 변경](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Azure Storage 계정 로그에 관한 보존 정책을 구성하는 방법](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure Portal의 Load Balancer 페이지 및 Azure Monitor의 Resource Health 페이지를 사용하여 표준 Load Balancer 리소스를 모니터링 및 관리하고 문제를 해결합니다. 보안에 사용 가능한 메트릭에는 SNAT(원본 네트워크 주소 변환) 연결, 포트에 대한 정보가 포함됩니다. 또한 SYN(동기화) 패킷 및 패킷 카운터에 대한 메트릭도 사용할 수 있습니다. 

Azure Monitor를 사용하여 표준, 외부 및 내부 부하 분산 장치에 대한 다차원 메트릭으로 엔드포인트 프로브 상태를 검토합니다. API를 통해 프로그래밍 방식으로 이러한 메트릭을 수집하고 '모든 메트릭' 옵션을 통해 스토리지 계정에 기록합니다.

내부 기본 Load Balancer에는 Azure Monitor 로그를 사용할 수 없습니다. 

Azure Monitor를 사용하여 기본 외부 Load Balancer에 대해 백 엔드 풀별로 요약된 프로브 상태(예: 상태 프로브 실패로 인해 Load Balancer에서 요청을 받지 못하는 백 엔드 풀의 인스턴스 수)를 확인합니다. 

Azure Operational Insights로 로깅을 구현하여 부하 분산 장치 상태에 대한 통계를 제공합니다. 

활동 로그를 사용하여 경고를 보고 Azure 구독의 리소스에 대한 작업과 해당 상태를 모니터링합니다. 활동 로그는 기본적으로 사용하도록 설정되며 Azure Portal에서 볼 수 있습니다. 

Azure 감사 로그 콘텐츠 팩과 함께 Microsoft Power BI를 사용하고 미리 구성된 대시보드로 데이터를 분석합니다. 비즈니스 요구 사항에 따라 사용자의 요구에 맞게 보기를 사용자 지정합니다. 

이벤트 허브 또는 Log Analytics 작업 영역으로 로그를 스트리밍합니다. Azure Blob Storage에서 추출하여 다양한 도구(예: Excel 및 Power BI)에서 볼 수도 있습니다. 데이터를 사용하도록 설정하고 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

- [Load Balancer 상태 프로브](load-balancer-custom-probe-overview.md)

- [Azure Monitor REST API](/rest/api/monitor)

- [REST API를 통해 메트릭을 검색하는 방법](/rest/api/monitor/metrics/list)

- [메트릭, 경고 및 리소스 상태를 사용하는 표준 Load Balancer 진단](load-balancer-standard-diagnostics.md)

- [공용 기본 Load Balancer용 Azure Monitor 로그](load-balancer-monitor-log.md)

- [Azure Portal에서 부하 분산 장치 메트릭 보기](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Log Analytics 작업 영역과 함께 Security Center를 사용하여 보안 로그 및 이벤트에서 Load Balancer와 관련된 비정상적인 활동을 모니터링하고 경고합니다.

데이터를 사용하도록 설정하고 Azure Sentinel 또는 타사 SIEM에 온보딩합니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Security Center에서 경고를 관리하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics 로그 데이터에 관해 경고하는 방법](../azure-monitor/alerts/tutorial-response.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하면 역할 할당을 통해 Load Balancer와 같은 Azure 리소스에 대한 액세스를 관리할 수 있습니다. 이러한 역할을 사용자, 그룹 서비스 주체, 관리 ID에 할당합니다.

Azure CLI, Azure PowerShell 또는 Azure Portal과 같은 도구를 사용하여 특정 리소스에 대해 미리 정의된 기본 제공 역할을 인벤토리에 포함합니다.

- [PowerShell을 사용하여 Azure AD(Azure Active Directory)에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하고, Security Center의 ID 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: Azure 네트워크 리소스를 관리하고 액세스하도록 구성된 다단계 인증과 함께 PAW(Privileged Access Workstation)를 사용합니다. 

- [Privileged Access Workstation에 대한 자세한 정보](/security/compass/privileged-access-devices)

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹에서만 액세스하도록 허용합니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD(Azure Active Directory)를 서비스에 대한 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 미사용 데이터와 전송 중인 데이터에 강력한 암호화를 사용하여 데이터를 보호하며 사용자 자격 증명을 솔트 및 해시하고 안전하게 저장합니다.  

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory-domain-services/tutorial-create-instance.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory)를 사용하여 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 

Azure ID 액세스 검토를 수행하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스를 정기적으로 검토하여 활성 사용자만 계속 액세스할 수 있도록 합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: Azure AD(Azure Active Directory) 로그인 활동, 감사 및 위험 이벤트 로그 원본을 액세스를 기반으로 하는 모든 SIEM 또는 모니터링 도구와 통합합니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보내서 이 프로세스를 간소화합니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory)의 위험 및 ID 보호 기능을 사용하여 사용자 ID와 관련하여 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 추가 조사를 위해 Azure Sentinel에 데이터를 수집합니다.

- [Azure AD 위험한 로그인을 확인하는 방법](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Azure 리소스에 연결하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인합니다.

해당하는 경우 저장 데이터 암호화 및 전송 중 암호화에 대한 Azure Security Center 권장 사항을 따릅니다.

- [Azure를 사용한 전송 중 암호화 이해](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 관리

**지침**: Azure RBAC를 사용하여 Load Balancer 리소스에 대한 액세스를 제어합니다.

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: Load Balancer는 고객 데이터를 저장하지 않는 패스스루 서비스입니다. Microsoft가 관리하는 기본 플랫폼의 일부입니다. 

Microsoft는 모든 고객 콘텐츠를 중요하게 취급하며 고객 데이터 손실 및 노출을 방지하기 위해 최선을 다하고 있습니다. 

Azure의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다. 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure Monitor를 Azure 활동 로그와 함께 사용하여 중요한 Azure 리소스(예: 중요한 프로덕션 워크로드에 사용되는 Load Balancer)가 변경될 때 경고를 생성합니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트, 프로토콜 등)를 쿼리하고 검색합니다. 현재 리소스를 만들고 사용하려면 Azure Resource Manager를 사용하는 것이 좋습니다.

테넌트에서 적절한(읽기) 권한을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 사용하여 Azure 리소스에 태그를 적용하여 분류에 따라 논리적으로 구성합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 자산을 구성하고 추적합니다. 

정기적으로 인벤토리를 조정하고, 권한 없는 리소스가 구독에서 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인된 Azure 리소스 목록을 만들어서 허용 목록 메커니즘으로 활용할 수 있습니다. 이렇게 하면 새로 사용 가능한 Azure 서비스를 조직의 일반적인 보안 평가 프로세스를 통해 공식적으로 검토하고 승인한 후 온보딩할 수 있습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy를 사용하여 구독에 만들 수 있는 리소스 형식에 대한 제한을 설정합니다.

소유하는 구독 내에서 Azure Resource Graph를 사용하여 리소스를 쿼리하고 검색합니다. 

환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy를 사용하여 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [가상 네트워크용 Azure Policy 샘플 기본 제공](/azure/virtual-network/policy-samples)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: Azure AD(Azure Active Directory) 조건부 액세스를 사용하여 "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 비즈니스 운영에 필요하지만 조직에 높은 위험을 초래할 수 있는 소프트웨어는 자체 가상 머신 및/또는 가상 네트워크 내에 격리하고 Azure Firewall 또는 네트워크 보안 그룹으로 충분히 보호해야 합니다.

- [가상 네트워크를 만드는 방법](../virtual-network/quick-create-portal.md)

- [보안 구성으로 네트워크 보안 그룹을 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy 별칭을 사용하여 Azure 리소스의 구성을 감사하거나 적용하기 위한 사용자 지정 정책을 만듭니다. 기본 제공 Azure Policy 정의를 사용합니다.

Azure Resource Manager는 템플릿을 JSON(JavaScript Object Notation) 형식으로 내보낼 수 있는 기능이 있으며, 내보낸 템플릿은 검토하여 조직의 보안 요구 사항을 충족하는지 확인해야 합니다.

Azure Resource Manager 템플릿을 JSON(JavaScript Object Notation) 형식으로 내보내고 주기적으로 검토하여 구성이 조직 보안 요구 사항을 충족하는지 확인합니다.

Security Center의 권장 사항을 Azure 리소스에 대한 보안 구성 기준으로 구현합니다.

- [사용 가능한 Azure Policy 별칭을 확인하는 방법](/powershell/module/az.resources/get-azpolicyalias)

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Portal에서 템플릿으로 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.  또한 Azure Resource Manager 템플릿을 사용하여 조직에 필요한 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure DevOps를 사용하여 사용자 지정 Azure Policy 정의, Azure Resource Manager 템플릿 및 필요한 상태 구성 스크립트와 같은 코드를 안전하게 저장하고 관리합니다.

Azure DevOps 또는 TFS와 통합된 경우 Azure AD(Azure Active Directory)에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대한 권한을 부여하거나 거부합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps의 그룹 및 사용 권한 정보](/azure/devops/organizations/security/about-permissions)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: Azure Policy를 사용하여 Azure 리소스에 대한 표준 보안 구성을 정의하고 구현합니다.  Azure Policy 별칭을 사용하여 Azure 리소스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 특정 Azure Load Balancer 리소스와 관련된 기본 제공 정책 정의를 구현합니다.  또한 Azure Automation을 사용하여 구성 변경 내용을 Azure Load Balancer 리소스에 배포합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [별칭을 사용하는 방법](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: Security Center를 사용하여 Azure 리소스 및 Azure Policy에 대한 기준 검색을 수행하여 리소스 구성을 경고하고 감사합니다.

- [Azure Security Center에서 권장 사항을 수정하는 방법](../security-center/security-center-remediate-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다. 

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md) 

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 사용자 고유의 인시던트 대응 계획을 만들 수도 있음](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 

심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

태그를 사용하여 구독을 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다.  

인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별한 다음, 필요에 따라 대응 계획을 수정합니다. 

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 교육 및 연습 프로그램 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Security Center 경고 및 권장 사항을 내보냅니다. 

Security Center의 연속 내보내기 기능을 사용하여 경고 및 권장 사항을 수동으로 또는 진행 중에 지속적으로 내보낼 수 있습니다. 

Security Center 데이터 커넥터를 활용하여 Azure Sentinel에 경고를 스트리밍합니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 응답을 자동으로 트리거하여 Azure 리소스를 보호합니다.

- [Security Center에서 워크플로 자동화를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: 침투 테스트가 Microsoft 정책을 위반하지 않도록 Microsoft Cloud 침투 테스트 참여 규칙을 따릅니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
