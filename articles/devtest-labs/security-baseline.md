---
title: Azure DevTest Labs에 대 한 Azure 보안 기준
description: Azure DevTest Labs 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: devtest-lab
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 13585173b54c79158161da54f968ba66eb018f6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101705741"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure DevTest Labs에 대 한 Azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 1.0](../security/benchmarks/overview-v1.md) 의 지침을 Azure DevTest Labs 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Azure DevTest Labs에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure DevTest Labs에 적용할 수 없는 **컨트롤** 은 제외 되었습니다.

Azure DevTest Labs 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Azure DevTest Labs 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Azure DevTest Labs 리소스를 배포할 때 기존 가상 네트워크를 만들거나 사용 해야 합니다. 선택한 가상 네트워크에 해당 서브넷에 적용 된 네트워크 보안 그룹 및 응용 프로그램의 신뢰할 수 있는 포트 및 원본에 맞게 구성 된 네트워크 액세스 제어가 있는지 확인 합니다. 랩 리소스는 가상 네트워크를 사용 하 여 구성 된 경우 공개적으로 주소를 지정할 수 없으며 가상 네트워크 내 에서만 액세스할 수 있습니다. 또한 랩 환경, 저장소 계정 및 키 자격 증명 모음과 같은 랩 리소스도 완전히 격리 되 고 지정 된 끝점을 통해서만 액세스할 수 있는 네트워크 격리 랩을 만들도록 선택할 수 있습니다. 

조직의 요구 사항에 따라 Azure 방화벽 서비스를 사용 하 여 구독 및 가상 네트워크에서 응용 프로그램 및 네트워크 연결 정책을 중앙에서 만들고 적용 하 고 기록 합니다.

- [Azure DevTest Labs에 대 한 가상 네트워크를 구성 하는 방법](devtest-lab-configure-vnet.md)

- [네트워크 격리 DevTest Labs 인스턴스를 만드는 방법](network-isolation.md)

- [보안 규칙을 사용 하 여 네트워크 보안 그룹을 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure 방화벽을 배포 및 구성 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure DevTest Labs 리소스가 배포 되는 네트워크에 네트워크 보안 그룹을 배포 합니다. 트래픽 감사를 위해 네트워크 보안 그룹에서 네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 합니다.

또한 Log Analytics 작업 영역에 NSG 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 들어오는 트래픽의 추가 검사를 위해 Azure Resource Manager 템플릿을 사용 하 여 배포 된 중요 웹 응용 프로그램 앞에 AZURE Waf (웹 응용 프로그램 방화벽)를 배포 합니다. WAF에 대해 진단 설정을 사용 하도록 설정 하 고 로그를 저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역에 수집 합니다.

- [Azure WAF를 배포 하는 방법](../web-application-firewall/ag/create-waf-policy-ag.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 랩에 대 한 Azure Virtual Network (VNet)를 배포 하면 랩에 대 한 보안 및 격리가 향상 됩니다. 서브넷, 액세스 제어 정책 및 기타 기능도 액세스를 제한 하는 데 도움이 됩니다. VNet에 배포 하는 경우 Azure DevTest Labs는 공개적으로 주소를 지정할 수 없으며 VNet 내의 가상 머신 및 응용 프로그램 에서만 액세스할 수 있습니다.

DDoS 공격 으로부터 보호 하기 위해 Azure 가상 네트워크에서 DDoS 표준 보호를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용 하 여 알려진 악성 IP 주소와의 통신을 거부 합니다.

위협 인텔리전스를 사용 하도록 설정 하 고 악의적인 네트워크 트래픽에 대해 "경고 및 거부"로 구성 된 각 조직의 네트워크 경계에서 Azure 방화벽을 배포 합니다. Azure Security Center Just-in-time 네트워크 액세스를 사용 하 여 제한 된 기간 동안 끝점의 노출을 승인 된 IP 주소로 제한 하는 NSGs를 구성 합니다. 적응 네트워크 강화 Azure Security Center 사용 하 여 실제 트래픽 및 위협 인텔리전스에 따라 포트와 원본 Ip를 제한 하는 NSG 구성을 권장 합니다.

- [Azure DevTest Labs에 대 한 가상 네트워크를 구성 하는 방법](devtest-lab-configure-vnet.md)

- [DDoS 보호를 구성 하는 방법](/azure/virtual-network/manage-ddos-protection)

- [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Security Center 통합 위협 인텔리전스 이해](/azure/security-center/threat-protection)

- [적응 네트워크 강화 Azure Security Center 이해](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center Just-in-time 네트워크 Access Control 이해](../security-center/security-center-just-in-time.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: 비정상적인 활동을 조사 하기 위해 랩 가상 네트워크에서 Network Watcher 패킷 캡처를 사용 하도록 설정 합니다. 

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: 위협 인텔리전스를 사용 하 여 가상 네트워크에 배포 된 Azure 방화벽을 사용 합니다. Azure Firewall 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다. 조직에서 Azure 방화벽이 제공할 수 있는 기능을 기반으로 추가 기능을 요구 하는 경우 페이로드 검사 기능을 사용 하 여 IDS/IPS 기능을 지 원하는 Azure Marketplace에서 적절 한 제품을 선택 합니다.

악의적인 트래픽을 감지 하거나 거부 하기 위해 각 조직의 네트워크 경계에서 원하는 방화벽 솔루션을 배포 합니다.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)
 

- [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)
 

- [Azure 방화벽을 사용 하 여 경고를 구성 하는 방법](../firewall/threat-intel.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 웹 응용 프로그램을 포함 하는 DevTest Labs Azure Resource Manager 기반 환경으로 작업할 때 신뢰할 수 있는 인증서에 대해 HTTPS/TLS를 사용 하는 Azure 애플리케이션 게이트웨이를 배포 합니다.

- [Application Gateway 배포 하는 방법](../application-gateway/quick-create-portal.md)

- [HTTPS를 사용 하도록 Application Gateway를 구성 하는 방법](../application-gateway/create-ssl-portal.md)

- [Azure 웹 응용 프로그램 게이트웨이를 사용 하 여 계층 7 부하 분산 이해](../application-gateway/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Virtual Network 서비스 태그를 사용 하 여 DevTest Labs 리소스에 대해 구성 된 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

응용 프로그램 보안 그룹을 사용 하 여 복잡 한 보안 구성의 간소화를 지원할 수도 있습니다. 애플리케이션 보안 그룹을 사용하면 네트워크 보안을 애플리케이션 구조의 자연 확장으로 구성하여 가상 머신을 그룹화하고 해당 그룹에 따라 네트워크 보안 정책을 정의할 수 있습니다.

- [서비스 태그 이해 및 사용](../virtual-network/service-tags-overview.md)

- [응용 프로그램 보안 그룹 이해 및 사용](/azure/virtual-network/security-overview#application-security-groups)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy을 사용 하 여 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다.

Azure 청사진을 사용 하 여 azure 리소스 관리자 템플릿, RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 단일 청사진 정의로 패키지화 하 여 대규모 Azure 배포를 간소화할 수도 있습니다. 청사진을 새 구독에 적용 하 고 버전 관리를 통해 제어 및 관리를 세부적으로 조정할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [네트워킹에 대 한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 논리적으로 분류로 구성 하기 위해 Azure DevTest Labs 배포와 연결 된 네트워크 리소스에 대 한 태그를 사용 합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 리소스 구성을 모니터링 하 고 azure 리소스에 대 한 변경 내용을 검색 합니다. 중요 한 리소스의 변경 내용이 발생 하는 경우 트리거할 Azure Monitor 내에서 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](/azure/azure-monitor/platform/alerts-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Microsoft는 Azure 리소스에 대 한 시간 소스를 유지 관리 합니다. 그러나 계산 리소스에 대 한 시간 동기화 설정을 관리할 수 있습니다.

- [Azure의 Windows VM에 대한 시간 동기화](../virtual-machines/windows/time-sync.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure 활동 로그 진단 설정을 사용하도록 설정하고 보관을 위해 로그를 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure 스토리지 계정으로 보냅니다. 활동 로그는 관리 평면 수준에서 Azure DevTest Labs 인스턴스에서 수행 된 작업에 대 한 통찰력을 제공 합니다. Azure 활동 로그 데이터를 사용 하 여 DevTest Labs 인스턴스에 대 한 관리 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대해 "무엇을, 누가, 언제"를 확인할 수 있습니다.

- [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](/azure/azure-monitor/platform/diagnostic-settings)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure 활동 로그 진단 설정을 사용하도록 설정하고 보관을 위해 로그를 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure 스토리지 계정으로 보냅니다. 활동 로그는 관리 평면 수준에서 Azure DevTest Labs 인스턴스에서 수행 된 작업에 대 한 통찰력을 제공 합니다. Azure 활동 로그 데이터를 사용 하 여 DevTest Labs 인스턴스에 대 한 관리 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대해 "무엇을, 누가, 언제"를 결정할 수 있습니다.

- [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](/azure/azure-monitor/platform/diagnostic-settings)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 고객이 vm (가상 머신)을 만들고 소유 하 고 Azure DevTest Labs 합니다. 따라서이를 모니터링 하는 것은 조직의 책임입니다. Azure Security Center를 사용 하 여 계산 OS를 모니터링할 수 있습니다. 운영 체제에서 Security Center에 의해 수집 되는 데이터에는 OS 유형 및 버전, OS (Windows 이벤트 로그), 실행 중인 프로세스, 컴퓨터 이름, IP 주소 및 로그인 한 사용자가 포함 됩니다. 또한 Log Analytics 에이전트는 크래시 덤프 파일을 수집 합니다.

자세한 내용은 다음 아티클을 참조하세요.

- [Azure Monitor를 사용 하 여 Azure 가상 머신 내부 호스트 로그를 수집 하는 방법](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Azure Security Center 데이터 수집 이해](../security-center/security-center-enable-data-collection.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure DevTest Labs 인스턴스와 연결 된 Log Analytics 작업 영역에 대 한 로그 보존 기간을 설정 합니다.

- [자세한 내용은 다음 문서를 참조 하세요.](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics에서 쿼리를 실행 하 여 용어를 검색 하 고, 추세를 식별 하 고, 패턴을 분석 하 고, Azure DevTest Labs에 대해 수집 되었을 수 있는 활동 로그 데이터를 기반으로 다양 한 통찰력을 제공 합니다.

자세한 내용은 다음 아티클을 참조하세요.

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](/azure/azure-monitor/platform/diagnostic-settings)

- [Azure Monitor의 Log Analytics 작업 영역에서 Azure 활동 로그를 수집 하 고 분석 하는 방법](/azure/azure-monitor/platform/activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure Log Analytics 작업 영역을 사용 하 여 보안 로그 및 Azure DevTest Labs 관련 된 이벤트의 비정상적인 활동을 모니터링 하 고 경고 합니다.

- [Log analytics 로그 데이터를 경고 하는 방법](/azure/azure-monitor/learn/tutorial-response)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure 보안 벤치 마크: id 및 Access Control](../security/benchmarks/security-control-identity-access-control.md)을 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure Active Directory (Azure AD)에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 실행 하 고 관리 그룹의 구성원 인 계정을 검색 합니다.

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure DevTest Labs 역할](devtest-lab-add-devtest-user.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure Active Directory (Azure AD)에는 기본 암호 개념이 없습니다. 암호를 요구 하는 다른 Azure 리소스는 복잡성 요구 사항과 최소 암호 길이를 사용 하 여 암호를 만들어야 합니다 .이는 서비스에 따라 다릅니다. 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 Marketplace 서비스를 담당 하 고 있습니다.

DevTest Labs에는 기본 암호 개념이 없습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.

- 구독에 둘 이상의 소유자를 할당해야 합니다.

- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

- [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법(미리 보기)](../security-center/security-center-identity-access.md)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure DevTest Labs 역할](devtest-lab-add-devtest-user.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: DevTest Labs는 id 관리에 Azure Active Directory (Azure AD) 서비스를 사용 합니다. DevTest Labs를 기반으로 환경에 대 한 액세스 권한을 사용자에 게 부여 하는 경우 다음 두 가지 주요 측면을 고려 하세요.
- 리소스 관리: 리소스를 관리 하는 Azure Portal에 대 한 액세스를 제공 합니다 (Vm 만들기, 환경 만들기, 시작, 중지, 다시 시작, 삭제 및 아티팩트 적용 등). 리소스 관리는 azure에서 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 수행 됩니다. 사용자에 게 역할을 할당 하 고 리소스 및 액세스 수준 사용 권한을 설정 합니다.
- 가상 컴퓨터 (네트워크 수준): 기본 구성에서 Vm은 로컬 관리자 계정을 사용 합니다. 사용 가능한 도메인 (Azure Active Directory Domain Services (Azure AD DS), 온-프레미스 도메인 또는 클라우드 기반 도메인)이 있으면 컴퓨터를 도메인에 조인할 수 있습니다. 그런 다음 사용자는 도메인 가입 아티팩트를 사용 하 여 도메인 기반 id를 사용 하 여 컴퓨터에 연결할 수 있습니다.

- [DevTest Labs에 대 한 참조 아키텍처](https://docs.microsoft.com/azure/devtest-labs/devtest-lab-reference-architecture#architecture)

- [Azure AD를 사용 하 여 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory (Azure AD) 다단계 인증을 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 다단계 인증을 사용 하는 paw (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure 리소스에 로그인 하 고 구성 합니다. 

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 
- [Azure에서 다단계 인증을 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 Azure AD (Azure Active Directory) 보안 보고서를 사용 합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: 승인 된 위치 에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스할 수 있도록 합니다.

- [Azure에서 명명 된 위치를 구성 하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory (Azure AD)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리 합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

- [Azure AD 보고 이해](../active-directory/reports-monitoring/overview-reports.md)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Siem (보안 정보 및 이벤트 관리)/모니터링 도구와 통합할 수 있도록 하는 Azure Active Directory (Azure AD) 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Active Directory (Azure AD) 위험 및 id 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 관리

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure DevTest Labs에서 실습에 대 한 액세스를 제어 합니다.

- [Azure RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md)

- [DevTest Labs의 역할 이해](devtest-lab-add-devtest-user.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 Azure Monitor를 사용 하 여 변경 내용이 DevTest Labs 인스턴스 및 기타 중요 한 리소스 또는 관련 된 리소스에 적용 되는 경우에 대 한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법](/azure/azure-monitor/platform/alerts-activity-log)

- [DevTest Labs 활동 로그 이벤트에 대 한 경고를 만드는 방법](create-alerts.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내의 모든 리소스 (DevTest Labs 리소스 포함)를 쿼리하고 검색 합니다. 테 넌 트에서 적절 한 (읽기) 권한이 있는지 확인 하 고 구독 내에서 모든 Azure 구독 및 리소스를 열거할 수 있습니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Azure 리소스에 태그를 적용 하 여 분류에 따라 논리적으로 구성 하는 메타 데이터를 제공 합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [DevTest Labs에 대 한 태그 구성](devtest-lab-add-tag.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태깅, 관리 그룹 및 별도의 구독을 사용 하 고, 해당 하는 경우 별도의 랩을 사용 하 여 랩 및 랩 관련 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정 하 고 권한 없는 리소스가 구독에서 빠르게 삭제 되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [DevTest Labs를 사용 하 여 랩을 만드는 방법](devtest-lab-create-lab.md)

- [태그를 만들고 사용하는 방법](devtest-lab-add-tag.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스 및 계산 리소스에 대 한 승인 된 소프트웨어의 인벤토리를 만듭니다. 구독 관리자는 구성 된 랩 컴퓨터 그룹에서 실행할 수 있는 응용 프로그램 집합을 정의 하는 데 도움이 되는 적응 응용 프로그램 제어 인 Azure Security Center 기능을 사용할 수도 있습니다. 이 기능은 Azure 및 비 Azure Windows (모든 버전, 클래식 또는 Azure Resource Manager) 및 Linux 컴퓨터에서 사용할 수 있습니다.

- [적응형 응용 프로그램 제어를 사용 하도록 설정 하는 방법](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정 합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 저장소 계정을 사용 하는 환경 등의 높은 보안 기반 환경에서 유용 하 게 사용할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: Azure Automation는 작업 및 리소스의 배포, 작업 및 서비스 해제 중에 완전 한 제어를 제공 합니다. 구독 관리자는 Azure Virtual Machine 인벤토리를 사용 하 여 구독에서 DevTest Labs Vm의 모든 소프트웨어에 대 한 정보 수집을 자동화할 수 있습니다. 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간 속성은 Azure Portal에서 사용할 수 있습니다. 설치 날짜 및 기타 정보에 대 한 액세스 권한을 얻으려면 게스트 수준 진단을 사용 하도록 설정 하 고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 가져와야 합니다.

소프트웨어 응용 프로그램 모니터링을 위해 변경 내용 추적를 사용 하는 것 외에도 Azure Security Center의 적응 응용 프로그램 컨트롤은 기계 학습을 사용 하 여 컴퓨터에서 실행 되는 응용 프로그램을 분석 하 고이 인텔리전스에서 허용 목록을 만듭니다. 이 기능은 응용 프로그램 허용 목록 정책을 구성 하 고 유지 관리 하는 프로세스를 간소화 하 여 사용자 환경에서 원치 않는 소프트웨어를 사용 하지 않도록 할 수 있도록 합니다. 감사 모드를 구성 하거나 모드를 적용할 수 있습니다. 감사 모드는 보호 된 Vm의 활동만 감사 합니다. 적용 모드는 규칙을 적용 하 고 실행이 허용 되지 않은 응용 프로그램이 차단 되도록 합니다. 

- [Azure Automation 소개](../automation/automation-intro.md)

- [Azure VM 인벤토리를 사용 하도록 설정 하는 방법](../automation/automation-tutorial-installed-software.md)

- [적응 응용 프로그램 컨트롤 이해](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure Automation는 작업 및 리소스의 배포, 작업 및 서비스 해제 중에 완전 한 제어를 제공 합니다. 구독 관리자는 변경 내용 추적를 사용 하 여 DevTest Labs에서 호스트 되는 Vm에 설치 된 모든 소프트웨어를 식별할 수 있습니다. 사용자 고유의 프로세스를 구현 하거나 인증 되지 않은 소프트웨어를 제거 하기 위해 Azure Automation 상태 구성을 사용할 수 있습니다.

- [Azure Automation 소개](../automation/automation-intro.md)

- [변경 내용 추적 솔루션으로 사용자 환경의 변경 내용 추적](../automation/change-tracking/overview.md)

- [Azure Automation 상태 구성 개요](../automation/automation-dsc-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**참고**: 구독 관리자는 Azure Security Center 적응 응용 프로그램 제어를 사용 하 여 권한이 있는 소프트웨어만 실행 되도록 하 고 권한이 없는 모든 소프트웨어는 DevTest Labs에서 호스트 되는 Azure vm에서 실행 되지 않도록 차단할 수 있습니다.

- [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정 합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

참조 자료:

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 적응 응용 프로그램 제어는 DevTest Labs에서 호스트 되는 azure 및 비 azure 컴퓨터 (Windows 및 Linux)에서 실행할 수 있는 응용 프로그램을 제어 하는 데 도움이 되는 Azure Security Center의 지능적이 고 자동화 된 종단 간 솔루션입니다. 참고 DevTest Labs에서 호스트 되는 기본 계산 리소스에 대해이 설정을 구성 하려면 구독 관리자 여야 합니다. 이 설정이 조직의 요구 사항을 충족 하지 않는 경우 타사 솔루션을 구현 합니다.

- [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: Azure 조건부 액세스를 사용 하 여 **Microsoft Azure 관리** 앱에 대 한 **액세스 차단** 을 구성 하 여 사용자의 Azure Resource Manager 상호 작용 하는 기능을 제한 합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: 사용자가 계산 리소스에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침**: 스크립트 유형에 따라 운영 체제별 구성 또는 타사 리소스를 사용 하 여 DevTest Labs에서 호스트 되는 vm 내에서 사용자가 스크립트를 실행 하는 기능을 제한할 수 있습니다. Azure Security Center 적응 응용 프로그램 제어를 사용 하 여 권한이 있는 소프트웨어만 실행 되 고 모든 권한이 없는 소프트웨어가 기본 Azure Vm에서 실행 되지 않도록 차단할 수도 있습니다.

- [Windows 환경에서 PowerShell 스크립트 실행을 제어 하는 방법](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

- [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: Azure 환경에 배포 된 위험도 높은 응용 프로그램은 가상 네트워크, 서브넷, 구독, 관리 그룹 등을 사용 하 여 격리할 수 있습니다. 그리고 Azure 방화벽, WAF (웹 응용 프로그램 방화벽) 또는 NSG (네트워크 보안 그룹)를 사용 하 여 충분히 안전 하 게 보호 합니다.

- [DevTest Labs에 대 한 가상 네트워크 구성](devtest-lab-configure-vnet.md)

- [Azure Firewall 개요](../web-application-firewall/overview.md)

- [웹 애플리케이션 방화벽 개요](../virtual-network/network-security-groups-overview.md)

- [네트워크 보안 개요](security-baseline.md)

- [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md)

- [구독 관련 결정 가이드](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 DevTest Labs의 일부로 생성 된 Azure 리소스의 구성을 감사 하거나 적용 합니다. 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

또한 Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하거나 초과 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다.

Azure Security Center의 권장 사항을 Azure 리소스에 대 한 보안 구성 기준으로 사용할 수도 있습니다.

- [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: Azure Security Center 권장 사항을 사용 하 여 DevTest Labs의 일부로 생성 된 모든 기본 계산 리소스에 대 한 보안 구성을 유지 합니다. 또한 사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성 또는 DevTest Labs 아티팩트를 사용 하 여 조직에 필요한 운영 체제의 보안 구성을 설정할 수 있습니다.

- [Azure Security Center 권장 사항을 모니터링 하는 방법](../security-center/security-center-recommendations.md)

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

- [Azure Automation 상태 구성 개요](../automation/automation-dsc-overview.md)

- [VHD를 업로드 하 고이를 사용 하 여 Azure에서 새 Windows Vm 만들기](../virtual-machines/windows/upload-generalized-managed.md)

- [Azure CLI를 사용하여 사용자 지정 디스크에서 Linux VM 만들기](../virtual-machines/linux/upload-vhd.md)

- [여러 DevTest Labs에 사용자 지정 이미지 만들기 및 배포](image-factory-save-distribute-custom-images.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: DevTest Labs의 일부로 생성 된 Azure 리소스 전체에서 보안 설정을 적용 하려면 Azure Policy **거부** 및 **배포** 규칙을 사용 합니다. 또한 Azure Resource Manager 템플릿을 사용 하 여 조직에서 요구 하는 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다.

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 랩의 일부로 생성 된 기본 Azure 계산 리소스에 대 한 취약성 평가를 수행 하는 Azure Security Center의 권장 사항을 따릅니다. 또한 Azure Resource Manager 템플릿, 사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성을 사용 하 여 조직에 필요한 운영 체제의 보안 구성을 유지할 수 있습니다. 이미지 팩터리 솔루션을 사용할 수도 있습니다. 이미지 팩터리 솔루션은 모든 desired 구성에서 정기적으로 이미지를 자동으로 빌드하고 배포 하는 코드를 작성 하는 솔루션입니다.

또한 Microsoft에서 게시 하는 가상 머신 이미지 Azure Marketplace Microsoft에서 관리 하 고 유지 관리 합니다.

- [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](../security-center/deploy-vulnerability-assessment-vm.md)

- [Azure Automation 상태 구성 개요](../automation/automation-dsc-overview.md)

- [Azure에 VHD를 업로드하고 새 VM을 만드는 샘플 스크립트](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

- [DevTest Labs에서 이미지 팩터리를 만드는 방법](image-factory-create.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure devops를 사용 하 여 사용자 지정 azure 정책, Azure Resource Manager 템플릿 및 필요한 상태 구성 스크립트와 같은 코드를 안전 하 게 저장 하 고 관리 합니다. Azure DevOps에서 관리 하는 리소스에 액세스 하려면 Azure DevOps와 통합 된 경우 특정 사용자, 기본 제공 보안 그룹 또는 Azure Active Directory (Azure AD)에 정의 된 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다.

- [Azure Repos Git 자습서](/azure/devops/repos/git/gitworkflow)

- [권한 및 그룹 정보](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions?view=azure-devops&amp;tabs=preview-page&amp;preserve-view=true)

- [Azure DevTest Labs와 Azure DevOps 워크플로 간 통합](devtest-lab-dev-ops.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 사용자 지정 이미지를 사용 하는 경우 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다. 공유 이미지 갤러리를 사용 하 여 이미지를 필요한 특정 랩에 공유할 수 있습니다. 컨테이너 이미지의 경우 Azure Container Registry에 저장 하 고 Azure RBAC를 사용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다.

- [Azure RBAC 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Azure RBAC를 구성 하는 방법](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [DevTest Labs에 대 한 공유 이미지 갤러리 구성](configure-shared-image-gallery.md)

- [Container Registry에 대 한 Azure RBAC 이해](../container-registry/container-registry-roles.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: Azure Policy을 사용 하 여 Azure 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. Azure Policy 별칭을 사용 하 여 DevTest Labs에서 만든 Azure 리소스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다. 또한 Azure Automation를 사용 하 여 구성 변경 내용을 배포할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [별칭을 사용 하는 방법](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: Azure Automation 상태 구성은 모든 클라우드 또는 온-프레미스 데이터 센터에서 DSC (Desired State configuration) 노드에 대 한 구성 관리 서비스입니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다. 또한 조직 정책을 따르도록 모든 랩 컴퓨터에 설치할 수 있는 사용자 지정 아티팩트를 작성할 수 있습니다. 

- [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](../automation/automation-dsc-onboarding.md)

- [DevTest Labs 가상 머신에 대 한 사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Azure Security Center을 사용 하 여 DevTest Labs에서 만든 Azure 리소스에 대 한 기준 검색을 수행 합니다. 또한 Azure Policy를 사용 하 여 Azure 리소스 구성을 경고 하 고 감사 합니다.

- [Azure Security Center에서 권장 사항을 수정 하는 방법](../security-center/security-center-remediate-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Security Center을 사용 하 여 랩에서 실행 되는 컨테이너에 대 한 OS 및 Docker 설정에 대 한 기준 검색을 수행 합니다.

- [Azure Security Center 컨테이너 권장 사항 이해](../security-center/container-security.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 하 고 보호 합니다.

- [DevTest Labs에서 Azure Resource Manager 환경을 배포 하도록 관리 되는 id 구성](use-managed-identities-environments.md)

- [DevTest Labs에서 가상 컴퓨터를 배포 하도록 관리 되는 id 구성](enable-managed-identities-lab-vms.md)

- [주요 자격 증명 모음을 만드는 방법](../key-vault/general/quick-create-portal.md)

- [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](../key-vault/general/authentication.md)

- [Key Vault 액세스 정책을 할당 하는 방법](../key-vault/general/assign-access-policy-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 관리 되는 id를 사용 하 여 azure AD (Azure Active Directory)에서 자동으로 관리 되는 Id를 azure 서비스에 제공 합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [DevTest Labs에서 Azure Resource Manager 환경을 배포 하도록 관리 되는 id 구성](use-managed-identities-environments.md)

- [DevTest Labs에서 가상 컴퓨터를 배포 하도록 관리 되는 id 구성](enable-managed-identities-lab-vms.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정 하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조 하세요.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: 중앙에서 관리 되는 맬웨어 방지 소프트웨어 사용

**지침**: Azure 용 Microsoft 맬웨어 방지를 사용 하 여 리소스를 지속적으로 모니터링 하 고 방어할 수 있습니다. Linux의 경우 타사 맬웨어 방지 솔루션을 사용 합니다.  또한 데이터 서비스에 대 한 Azure Security Center의 위협 검색을 사용 하 여 저장소 계정에 업로드 된 맬웨어를 검색 합니다. 

- [Azure 용 Microsoft 맬웨어 방지를 구성 하는 방법](../security/fundamentals/antimalware.md) 

- [위협 방지 및 Azure Security Center](/azure/security-center/threat-protection)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스를 지 원하는 기본 호스트에서 사용 하도록 설정 됩니다 (예: 랩에서 호스트 되는 Azure App Service). 그러나 콘텐츠에서 실행 되지 않습니다. 

App Service, Data Lake Storage, Blob Storage 등의 비 계산 Azure 리소스에 업로드 된 파일을 미리 검색 합니다. 

데이터 서비스에 대 한 Azure Security Center의 위협 검색을 사용 하 여 저장소 계정에 업로드 된 맬웨어를 검색 합니다. 

- [Azure에 대 한 Microsoft 맬웨어 방지 이해](../security/fundamentals/antimalware.md) 

- [데이터 서비스에 대 한 Azure Security Center의 위협 검색 이해](/azure/security-center/security-center-alerts-data-services)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명을 업데이트 해야 합니다.

**지침**: 배포할 때 Azure 용 Microsoft 맬웨어 방지 프로그램은 기본적으로 최신 서명, 플랫폼 및 엔진 업데이트를 자동으로 설치 합니다. Azure Security Center "계산 앱"의 권장 사항에 따라 &amp; DevTest Labs 기본 계산 리소스의 모든 끝점이 최신 서명으로 최신 상태 인지 확인 합니다. Windows OS는 Azure Security Center와 통합 되는 Microsoft Defender Advanced Threat Protection 서비스를 사용 하 여 바이러스 또는 맬웨어 기반 공격의 위험을 제한 하는 추가 보안으로 추가로 보호할 수 있습니다.

- [Azure 용 Microsoft 맬웨어 방지 프로그램을 배포 하는 방법](../security/fundamentals/antimalware.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: 현재 AZURE DEVTEST LABS는 VM 백업 및 스냅숏을 지원 하지 않습니다. 그러나 DevTest Labs에서 호스트 되는 기본 Azure Vm에서 Azure Backup를 사용 하도록 설정 하 고 구성할 수 있습니다. 또한 기본 계산 리소스에 대 한 적절 한 액세스 권한이 있는 경우 자동 백업에 대 한 원하는 빈도 및 보존 기간을 구성할 수 있습니다. 

- [Azure VM 백업 개요](../backup/backup-azure-vms-introduction.md)

- [VM 설정에서 Azure VM 백업](../backup/backup-azure-vms-first-look-arm.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: 현재 AZURE DEVTEST LABS는 VM 백업 및 스냅숏을 지원 하지 않습니다. 그러나 기본 계산 리소스에 대 한 적절 한 액세스 권한이 있는 한 PowerShell 또는 REST Api를 사용 하 여 해당 인스턴스에 연결 된 관리 디스크 또는 DevTest Labs에서 호스트 되는 기본 Azure Vm의 스냅숏을 만들 수 있습니다. 또한 Azure Key Vault 내에서 고객 관리 키를 백업할 수 있습니다.

대상 Azure Vm에서 Azure Backup를 사용 하도록 설정 하 고 원하는 빈도와 보존 기간을 설정 합니다. 여기에는 전체 시스템 상태 백업이 포함 됩니다. Azure disk encryption을 사용 하는 경우 Azure VM 백업은 고객이 관리 하는 키의 백업을 자동으로 처리 합니다.

- [암호화를 사용 하는 Azure Vm에서 백업](../backup/backup-azure-vms-encryption.md)

- [Azure VM 백업 개요](../backup/backup-azure-vms-introduction.md)

- [Azure VM 백업 개요](../backup/backup-azure-vms-introduction.md)

- [Azure에서 Key Vault 키를 백업 하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: Azure Backup 내에서 콘텐츠의 데이터 복원을 정기적으로 수행할 수 있는지 확인 합니다. 필요한 경우 격리 된 가상 네트워크 또는 구독에 대 한 콘텐츠 복원을 테스트 합니다. 또한 백업 된 고객이 관리 하는 키의 복원을 테스트 합니다.

Azure disk encryption을 사용 하는 경우 디스크 암호화 키를 사용 하 여 Azure VM을 복원할 수 있습니다. 디스크 암호화를 사용 하는 경우 디스크 암호화 키를 사용 하 여 Azure VM을 복원할 수 있습니다.

- [암호화를 사용 하는 Azure Vm에서 백업](../backup/backup-azure-vms-encryption.md)

- [Azure VM 백업에서 파일을 복구 하는 방법](../backup/backup-azure-restore-files-from-vm.md)

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [암호화 된 VM을 백업 및 복원 하는 방법](../backup/backup-azure-vms-encryption.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Azure Backup을 사용 하 여 관리 디스크를 백업 하는 경우 vm은 저장소 서비스 암호화 (SSE)를 사용 하 여 미사용 상태로 암호화 됩니다. Azure Backup Azure Disk Encryption를 사용 하 여 암호화 된 Azure Vm을 백업할 수도 있습니다. Azure Disk Encryption는 키 자격 증명 모음에 비밀으로 보호는 BEKs (BitLocker 암호화 키)와 통합 됩니다. Azure Disk Encryption은 KEKs (Azure Key Vault 키 암호화 키)와도 통합 됩니다. Key Vault에서 일시 삭제를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다.

- [Vm에 대 한 일시 삭제](../backup/soft-delete-virtual-machines.md)

- [Azure Key Vault 일시 삭제 개요](../key-vault/general/soft-delete-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대 한 인시던트 대응 가이드를 개발 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 해결 방법을 확인 합니다. 

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 됩니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 태그를 사용 하 여 구독을 표시 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 식별 하 고 분류 하는 명명 시스템을 만듭니다.  사고가 발생 한 Azure 리소스 및 환경의 중요도에 따라 경고 수정의 우선 순위를 지정 하는 것은 사용자의 책임입니다. 

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 약한 지점과 격차를 확인 한 후 필요에 따라 응답 계획을 수정 합니다. 

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되도록 연속 내보내기 기능을 사용 하 여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용 하면 경고 및 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있습니다. Azure Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다. 

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md) 

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: 워크플로 자동화 기능 Azure Security Center 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 응답을 자동으로 트리거합니다. 

- [Security Center에서 워크플로 자동화를 구성 하는 방법](../security-center/workflow-automation.md)

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

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
