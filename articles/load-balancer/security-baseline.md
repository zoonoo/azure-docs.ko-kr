---
title: Azure Load Balancer에 대 한 Azure 보안 기준
description: Azure Load Balancer 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e62da791e8c60f884855fba16315a03fe22cecb5
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450715"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure Load Balancer에 대 한 Azure 보안 기준

Microsoft Azure Load Balancer의 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다. 이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다. 자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조 하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 내부 Azure 부하 분산 장치를 사용 하 여 인터넷에 대 한 노출을 제외 하 고 특정 가상 네트워크 또는 피어 링 가상 네트워크 내에서 백 엔드 리소스에 대 한 트래픽만 허용 합니다. 다이렉트 인터넷 노출 으로부터 보호 하기 위해 백 엔드 리소스의 IP 주소를 모조 하기 위해 원본 SNAT (네트워크 주소 변환)을 사용 하 여 외부 Load Balancer를 구현 합니다.

Azure는 Standard 및 Basic 이라는 두 가지 유형의 Load Balancer 제공 합니다. 모든 프로덕션 작업에 대해 표준 Load Balancer를 사용 합니다. 네트워크 보안 그룹을 구현 하 고 응용 프로그램의 신뢰할 수 있는 포트 및 IP 주소 범위에 대 한 액세스만 허용 합니다. 백 엔드 서브넷 또는 백 엔드 가상 머신의 NIC에 할당 된 네트워크 보안 그룹이 없는 경우 부하 분산 장치에서 이러한 리소스에 대 한 트래픽이 허용 되지 않습니다. 표준 부하 분산 장치에서는 네트워크 보안 그룹을 사용 하 여 아웃 바운드 NAT를 정의 하는 아웃 바운드 규칙을 제공 합니다. 이러한 아웃 바운드 규칙을 검토 하 여 아웃 바운드 연결의 동작을 조정 합니다. 

프로덕션 워크 로드에는 표준 Load Balancer를 사용 하는 것이 좋으며, 일반적으로 기본 Load Balancer는 기본적으로 인터넷에서 연결에 대 한 기본 유형이 열려 있기 때문에 테스트에만 사용 되며 작업에 네트워크 보안 그룹이 필요 하지 않습니다. 

- [Azure에서 아웃바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#outboundrule)

- [Azure Public Load Balancer 업그레이드](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Load Balancer는 백 엔드 리소스에 적용 되는 네트워크 보안 그룹 규칙을 사용 하는 통과 서비스와 인터넷 액세스를 제어 하는 구성 된 아웃 바운드 규칙을 사용 합니다.

Load Balancer의 아웃 바운드 규칙 블레이드 및 암시적 아웃 바운드 규칙을 사용할 수 있는 부하 분산 규칙 블레이드를 통해 표준 Load Balancer에 대해 구성 된 아웃 바운드 규칙을 검토 합니다.

아웃 바운드 연결 수를 모니터링 하 여 리소스가 인터넷에 연결 되는 빈도를 추적 합니다. 

Security Center를 사용 하 고 네트워크 보호 권장 사항을 따라 Azure 네트워크 리소스를 보호 합니다.

백 엔드 리소스에 대 한 보안 권장 사항을 따르고 네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하 고 감사를 위해 Azure Storage 계정으로 로그를 보냅니다.

또한 흐름 로그를 Log Analytics 작업 영역으로 보낸 다음 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 패턴에 대 한 통찰력을 제공 합니다. 트래픽 분석의 장점으로는 네트워크 활동을 시각화 하 고, 핫 스폿 및 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 오류를 정확 하 게 파악할 수 있습니다.

- [네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [트래픽 분석 사용 및 사용 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [아웃 바운드 연결 통계를 확인 어떻게 할까요?](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 웹 응용 프로그램을 보호 하기 위해 Microsoft의 위협 인텔리전스를 사용 하는 Load Balancer으로 아웃 바운드 규칙 및 네트워크 보안 그룹을 통해 인터넷 연결 및 유효한 원본 ip를 명시적으로 정의 합니다.

- [Azure 방화벽 통합](https://docs.microsoft.com/azure/firewall/integrate-lb)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: azure Virtual Network에서 azure DDoS (서비스 거부) 표준 보호를 사용 하도록 설정 하 여 DDoS 공격 으로부터 보호 합니다. 

위협 인텔리전스 기반 필터링을 사용 하도록 설정 하 고 악의적인 네트워크 트래픽에 대해 "경고 및 거부"로 구성 된 각 조직의 네트워크 경계에서 Azure 방화벽을 배포 합니다.

 

Security Center 위협 방지를 사용 하 여 알려진 악성 IP 주소와의 통신을 검색 합니다. 

표준 Load Balancer은 기본적으로 보안을 유지 하 고 개인 및 격리 된 Virtual Network의 일부로 설계 되었습니다. 허용 된 트래픽을 명시적으로 허용 하 고 알려진 악성 IP 주소를 허용 하지 않는 한 네트워크 보안 그룹에서 열지 않는 한 인바운드 흐름으로 닫힙니다. 가상 컴퓨터 리소스의 서브넷 또는 NIC에서 네트워크 보안 그룹이 Load Balancer 뒤에 있지 않으면 트래픽이이 리소스에 도달할 수 없습니다. 

위협 인텔리전스 기반 필터링을 사용 하도록 설정 하 고 악의적인 네트워크 트래픽에 대 한 "경고 및 거부"로 구성 된 각 조직의 네트워크 경계에 Azure 방화벽을 배포 하 여 악의적인 IP 주소의 공격을 방지 합니다. 

Azure 방화벽을 Load Balancer와 통합 하기 위한 지침을 구현 합니다.

Security Center 위협 방지 기능을 사용 하 여 알려진 악성 IP 주소와의 통신을 검색 합니다. 

Security Center (표준 계층)은 just-in-time 가상 컴퓨터 액세스를 제공 하며 승인 된 IP 주소/범위 에서만 액세스할 수 있도록 허용 된 원본 IP 주소를 구성 합니다.
 

Security Center의 적응 네트워크 강화 기능을 사용 하 여 실제 트래픽 및 위협 인텔리전스에 따라 포트와 원본 Ip를 제한 하는 네트워크 보안 그룹 구성을 권장 합니다.
 

- [Azure Portal을 사용하여 Azure DDoS Protection 표준 관리](https://docs.microsoft.com/azure/virtual-network/manage-ddos`protection)

- [Azure Firewall 위협 인텔리전스 기반 필터링](https://docs.microsoft.com/azure/firewall/threat-intel)

- [위협 방지 및 Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection)

- [Just-in-time 액세스를 사용 하 여 관리 포트 보호](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

- [Azure Security Center의 적응 네트워크 강화](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Load Balancer와 Azure 방화벽 통합](https://docs.microsoft.com/azure/firewall/overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: Network Watcher 패킷 캡처를 사용 하도록 설정 하 여 비정상적인 활동을 조사할 수 있습니다.

- [Network Watcher 인스턴스를 만드는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: Load Balancer 환경에 대 한 페이로드 검사 기능을 사용 하 여 IDS/IPS 기능을 지 원하는 Azure Marketplace에서 제공 하는 제품을 구현 합니다. 

페이로드 검사는 요구 사항이 아닌 경우 Azure 방화벽 위협 인텔리전스를 사용 합니다. Azure 방화벽 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과의 트래픽을 경고 및/또는 차단 하는 데 사용 됩니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

각 조직의 네트워크 경계에서 원하는 방화벽 솔루션을 배포 하 여 악성 트래픽을 검색 및/또는 차단할 수 있습니다.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure 방화벽을 배포 하는 방법](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Azure 방화벽을 사용 하 여 경고를 구성 하는 방법](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: Microsoft의 위협 인텔리전스 기능을 사용 하 여 웹 응용 프로그램을 보호 하는 Load Balancer로 아웃 바운드 규칙 및 네트워크 보안 그룹을 통해 인터넷 연결 및 유효한 원본 ip를 명시적으로 정의 합니다.

- [Azure 방화벽 통합](https://docs.microsoft.com/azure/firewall/integrate-lb)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용 합니다. 해당 서비스에 대 한 트래픽을 허용 하거나 거부 하는 규칙의 원본 또는 대상 필드에 서비스 태그 이름을 지정 합니다. 

Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다. 

기본적으로, 모든 네트워크 보안 그룹은 상태 프로브 트래픽을 허용하기 위해 서비스 태그 AzureLoadBalancer를 포함합니다. 

네트워크 보안 그룹 규칙에서 사용할 수 있는 모든 서비스 태그는 Azure 설명서를 참조 하세요.

- [사용 가능한 서비스 태그](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy을 사용 하 여 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다.

Azure 청사진을 사용 하 여 Azure 리소스 관리자 템플릿, Azure RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 단일 청사진 정의로 패키지화 하 여 대규모 Azure 배포를 간소화 합니다. 

청사진을 새 구독에 적용 하 고 버전 관리를 통해 제어 및 관리를 세부적으로 조정 합니다.

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [네트워킹에 대 한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint를 만드는 방법](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 네트워크 보안 그룹 및 네트워크 보안 및 트래픽 흐름과 관련 된 기타 리소스에 대 한 리소스 태그를 사용 합니다. 

"설명" 필드를 사용 하 여 개별 네트워크 보안 그룹 규칙에 대 한 네트워크에서 들어오고 나가는 트래픽을 허용 하는 규칙을 문서화 합니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하는 "태그 및 해당 값 필요"와 같이 태그 지정과 관련 된 기본 제공 Azure Policy 정의를 모두 구현 합니다.

Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Azure Virtual Network을 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [네트워크 보안 그룹 규칙을 사용 하 여 네트워크 트래픽을 필터링 하는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: azure 활동 로그를 사용 하 여 리소스 구성을 모니터링 하 고 azure 리소스에 대 한 변경 내용을 검색 합니다. 

중요 한 리소스가 변경 될 때 사용자에 게 알리도록 경고를 Azure Monitor 만듭니다.

- [Azure 활동 로그 이벤트를 확인 하 고 검색 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조 하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 구독에서 활동 로그를 확인 하 여 부하 분산 장치에 대 한 아웃 바운드 규칙 및 네트워크 보안 그룹에 대 한 변경 내용을 검토 합니다. 

내부 호스트 로그를 확인 하 여 백 엔드 리소스를 안전 하 게 보호 합니다.

Log Analytics 또는 다른 저장소 플랫폼으로 이러한 로그를 내보냅니다. Azure Monitor에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기 및 보관 저장소에 Azure Storage 계정을 사용할 수 있습니다.

조직의 비즈니스 요구 사항에 따라이 데이터를 Azure 센티널 또는 타사 SIEM에 사용 하도록 설정 합니다.

- [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Azure Monitor를 사용 하 여 Azure 가상 머신 내부 호스트 로그를 수집 하는 방법](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [플랫폼 활동 로그](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 기본 Load Balancer에 대 한 활동 로그를 사용 하 여 캡처한 제어 및 관리 평면 로깅 및 감사 정보를 검토 합니다. 이러한 캡처 설정은 기본적으로 사용 하도록 설정 되어 있습니다. 

활동 로그를 사용 하 여 리소스에 대 한 작업을 모니터링 하 여 모든 활동 및 해당 상태를 볼 수 있습니다. 

활동 로그를 사용 하 여 구독의 리소스에서 수행 된 작업을 확인 합니다. 

- 작업을 시작한 사람
- 작업이 발생한 시간
- 작업의 상태

- 작업을 조사하는 데 도움이 될 수 있는 기타 속성 값

Azure PowerShell, Azure CLI (명령줄 인터페이스), Azure REST API 또는 Azure Portal를 통해 활동 로그에서 정보를 검색 합니다. 

Azure Monitor를 사용 하 여 표준 Load Balancer 구성 기능에 대 한 다차원 진단을 구현 합니다.  여기에는 SNAT (원본 네트워크 주소 변환) 연결에 대 한 정보, 포트를 포함 하 여 보안을 위한 사용 가능한 메트릭이 포함 됩니다. SYN (동기화) 패킷과 패킷 카운터에 대 한 추가 메트릭을 사용할 수도 있습니다. 

Api를 통해 프로그래밍 방식으로 다차원 메트릭을 검색 하 고 ' 모든 메트릭 ' 옵션을 통해 저장소 계정에 기록 합니다.

Azure Audit Logs 콘텐츠 팩을 Microsoft Power BI와 함께 사용 하 여 미리 구성 된 대시보드로 데이터를 분석 하거나 요구 사항에 따라 보기를 사용자 지정할 수 있습니다.

이벤트 허브 또는 Log Analytics 작업 영역으로 로그를 스트리밍합니다. 또한 Azure blob storage에서 추출 하 고 Excel 및 Power BI와 같은 다른 도구에서 볼 수 있습니다. 

비즈니스 요구 사항에 따라 Azure 센티널 또는 타사 SIEM에 대 한 온-보드 데이터를 사용 하도록 설정 합니다.

- [리소스 관리자 감사 작업에 자세히 설명 된 각 방법에 대 한 단계별 지침을 사용 하 여이 문서를 검토 합니다.](https://docs.microsoft.com/azure/azure-resource-manager/management/view-activity-logs)

- [공용 기본 Load Balancer용 Azure Monitor 로그](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)

- [활동 로그를 확인 하 여 리소스에 대 한 작업 모니터링](https://docs.microsoft.com/azure/azure-resource-manager/management/view-activity-logs)

- [API를 통해 프로그래밍 방식으로 다차원 메트릭 검색](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: 활동 로그는 기본적으로 사용 하도록 설정 되며 Azure의 이벤트 로그 저장소에서 90 일 동안 보존 됩니다. Azure Monitor에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 합니다. 장기 및 보관 저장소에 Azure Storage 계정을 사용 합니다.

- [활동 로그를 확인 하 여 리소스에 대 한 작업 모니터링 문서](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)

- [Log Analytics에서 데이터 보존 기간 변경](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Azure Storage 계정 로그에 대 한 보존 정책을 구성 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure Portal의 Load Balancer 페이지 및 Azure Monitor의 Resource Health 페이지를 사용 하 여 표준 Load Balancer 리소스를 모니터링 하 고 관리 하 고 문제를 해결 합니다. 보안에 사용 가능한 메트릭은 SNAT (원본 네트워크 주소 변환) 연결, 포트에 대 한 정보를 포함 합니다. 또한 SYN (동기화) 패킷과 패킷 카운터에 대 한 메트릭을 사용할 수 있습니다. 

Azure Monitor를 사용 하 여 표준, 외부 및 내부 부하 분산 장치에 대 한 다단계 메트릭을 사용 하 여 끝점 상태 프로브 상태를 검토할 수 있습니다. Api를 통해 프로그래밍 방식으로 이러한 메트릭을 수집 하 고 ' 모든 메트릭 ' 옵션을 통해 저장소 계정에 기록 합니다.

Azure Monitor 로그는 내부 기본 부하 분산 장치에 사용할 수 없습니다. 

Azure Monitor를 사용 하 여 상태 프로브 실패로 인해 Load Balancer에서 요청을 수신 하지 않는 백 엔드 풀의 인스턴스 수와 같은 기본 외부 Load Balancer에 대해 백 엔드 풀에 요약 된 상태 프로브 상태를 확인할 수 있습니다. 

Azure Operational Insights로 로깅을 구현 하 여 부하 분산 장치 상태에 대 한 통계를 제공 합니다. 

활동 로그를 사용 하 여 경고를 확인 하 고 Azure 구독에서 리소스 및 해당 상태에 대 한 작업을 모니터링할 수 있습니다. 활동 로그는 기본적으로 사용 하도록 설정 되며 Azure Portal에서 볼 수 있습니다. 

Azure Audit Logs 콘텐츠 팩과 함께 Microsoft Power BI를 사용 하 여 미리 구성 된 대시보드로 데이터를 분석 합니다. 비즈니스 요구 사항에 따라 사용자의 요구 사항에 맞게 보기를 사용자 지정 합니다. 

이벤트 허브 또는 Log Analytics 작업 영역으로 로그를 스트리밍합니다. 또한 Azure blob storage에서 추출 하 고 Excel 및 Power BI와 같은 다른 도구에서 볼 수 있습니다. Azure 센티널 또는 타사 SIEM에 대 한 온-보드 데이터를 사용 하도록 설정할 수 있습니다.

- [Load Balancer 상태 프로브](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

- [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor)

- [REST API를 통해 메트릭을 검색 하는 방법](https://docs.microsoft.com/rest/api/monitor/metrics/list)

- [메트릭, 경고 및 리소스 상태를 사용 하 여 진단 표준 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)

- [공용 기본 Load Balancer용 Azure Monitor 로그](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)

- [Azure Portal에서 부하 분산 장치 메트릭 보기](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 보안 로그 및 이벤트의 Load Balancer와 관련 된 비정상적인 활동을 모니터링 하 고 경고 하기 위해 Log Analytics 작업 영역에서 Security Center를 사용 합니다.

및 온-보드 데이터를 Azure 센티널 또는 타사 SIEM 도구로 사용 하도록 설정 합니다.

- [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Azure Security Center에서 경고를 관리 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Log analytics 로그 데이터를 경고 하는 방법](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: Azure Load Balancer에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: Azure Load Balancer는 적용 되지 않습니다. DNS 쿼리를 수행 하지 않는 핵심 네트워킹 서비스입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**:이 권장 사항이 계산 리소스에 적용 되므로 Azure Load Balancer에는 적용 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: azure RBAC (역할 기반 액세스 제어)를 통해 역할 할당을 통해 Load Balancer와 같은 azure 리소스에 대 한 액세스를 관리할 수 있습니다. 이러한 역할을 사용자, 그룹 서비스 사용자 및 관리 되는 id에 할당 합니다. 

Azure CLI, Azure PowerShell 또는 Azure Portal와 같은 도구를 사용 하 여 특정 리소스에 대 한 미리 정의 된 기본 제공 역할을 인벤토리에 포함 합니다.

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조 하세요.*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 관리

**지침**: Azure RBAC를 사용 하 여 Load Balancer 리소스에 대 한 액세스를 제어 합니다.

- [Azure에서 RBAC를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: Load Balancer은 고객 데이터를 저장 하지 않는 통과 서비스입니다. Microsoft에서 관리 하는 기본 플랫폼의 일부입니다. 

Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. 

Azure의 고객 데이터를 안전 하 게 유지 하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현 하 고 유지 관리 합니다. 

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: 중요 한 프로덕션 작업에 사용 되는 부하 분산 장치와 같은 중요 한 azure 리소스에 변경 내용이 발생 하는 경우 Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독의 모든 리소스 (예: 계산, 저장소, 네트워크, 포트, 프로토콜 등)를 쿼리하고 검색 합니다. Azure Resource Manager를 사용 하 여 현재 리소스를 만들고 사용 하는 것이 좋습니다. 

테 넌 트에서 적절 한 (읽기) 권한을 확인 하 고 구독의 모든 Azure 구독 및 리소스를 열거 합니다.

- [Azure 리소스 그래프 탐색기를 사용 하 여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Azure 구독을 보는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타 데이터를 사용 하 여 Azure 리소스에 태그를 적용 하 여 분류에 따라 논리적으로 구성 합니다.

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당 하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 자산을 구성 하 고 추적 합니다. 

정기적으로 인벤토리를 조정 하 고 권한이 없는 리소스가 적시에 구독에서 삭제 되도록 합니다.

- [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 허용 목록 메커니즘으로 활용할 수 있는 조직 요구 사항에 따라 승인 된 Azure 리소스 목록을 만듭니다. 이렇게 하면 조직에서 일반적인 보안 평가 프로세스를 공식적으로 검토 하 고 승인한 후 새로 사용 가능한 Azure 서비스를 등록할 수 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

소유 하는 구독 내에서 Azure 리소스 그래프를 사용 하 여 리소스를 쿼리하고 검색 합니다. 

환경에 있는 모든 Azure 리소스가 승인 되었는지 확인 합니다.

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure 리소스 그래프 탐색기를 사용 하 여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: Azure AD 조건부 액세스를 사용 하 여 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 함으로써 사용자의 Azure Resource Manager 상호 작용 하는 기능을 제한 합니다.

- [Azure 리소스 관리자에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 비즈니스 운영에 필요 하지만 조직에 대 한 위험을 초래할 수 있는 소프트웨어, 자체 가상 머신 및/또는 가상 네트워크 내에서 격리 하 고 Azure 방화벽 또는 네트워크 보안 그룹을 사용 하 여 충분히 안전 하 게 보호 해야 합니다.

- [가상 네트워크를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [보안 구성을 사용 하 여 네트워크 보안 그룹을 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy 별칭을 사용 하 여 Azure 리소스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 기본 제공 Azure Policy 정의를 사용 합니다.

Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다.

Azure Resource Manager 템플릿을 JavaScript Object Notation (JSON) 형식으로 내보내고 정기적으로 검토 하 여 구성이 조직의 보안 요구 사항을 충족 하는지 확인 합니다. 

Security Center에서 Azure 리소스에 대 한 보안 구성 기준으로 권장 사항을 구현 합니다. 

- [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [보안 권장 사항 - 참조 가이드](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.  또한 Azure Resource Manager 템플릿을 사용 하 여 조직에서 요구 하는 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 

- [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [규정 준수를 적용 하는 정책 만들기 및 관리](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Resource Manager 템플릿 개요](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure devops를 사용 하 여 사용자 지정 Azure Policy 정의, Azure Resource Manager 템플릿 및 필요한 상태 구성 스크립트와 같은 코드를 안전 하 게 저장 하 고 관리 합니다. 

Azure DevOps와 통합 된 경우에는 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부 하 고, TFS와 통합 된 경우에는 Active Directory 합니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps의 사용 권한 및 그룹 정보](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: Azure Policy을 사용 하 여 Azure 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다.  Azure Policy 별칭을 사용 하 여 Azure 리소스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 특정 Azure Load Balancer 리소스와 관련 된 기본 제공 정책 정의를 구현 합니다.  또한 Azure Automation를 사용 하 여 구성 변경 내용을 배포 합니다. Azure Load Balancer 리소스

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [별칭을 사용 하는 방법](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Security Center을 사용 하 여 Azure 리소스에 대 한 기준 검사를 수행 하 고 리소스 구성에 대 한 경고 및 감사를 Azure Policy 합니다.

- [Azure Security Center에서 권장 사항을 수정 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조 하세요.*

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 

심각도는 경고를 실행 하는 데 사용 되는 찾기 또는 분석에 사용 되는 것과 경고를 발생 시킨 활동의 악의적인 의도를 받은 신뢰 수준에 Security Center 따라 달라 집니다.

태그를 사용 하 여 구독을 표시 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 식별 및 분류 하는 명명 시스템을 만듭니다.  

인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [태그를 사용하여 Azure 리소스 구성](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags).

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되도록 연속 내보내기 기능을 사용 하 여 Security Center 경고 및 권장 사항을 내보냅니다. 

경고 및 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있는 Security Center의 연속 내보내기 기능을 사용 합니다. 

Security Center data connector를 활용 하 여 경고를 Azure 센티널로 스트리밍합니다.

- [연속 내보내기를 구성하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export)

- [경고를 Azure Sentinel로 스트림하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Security Center의 Workflow Automation 기능을 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 응답을 자동으로 트리거합니다.

- [Security enter에서 워크플로 자동화를 구성 하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Engagement의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다. 

- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
