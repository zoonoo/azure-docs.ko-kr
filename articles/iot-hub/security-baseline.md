---
title: Azure IoT Hub에 관한 Azure 보안 기준
description: Azure IoT Hub 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99243dbfac7fddb8a4fe9d64ed64ab706245ec3c
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587632"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Azure IoT Hub에 관한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 지침을 Microsoft Azure IoT Hub에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure Security Benchmark에서 정의한 **보안 컨트롤** 및 Azure IoT Hub에 해당되는 관련 지침에 따라 그룹화됩니다. Azure IoT Hub에 해당되지 않는 **컨트롤** 은 제외되었습니다.

 
Azure IoT Hub가 Azure Security Benchmark에 완전히 매핑되는 방법을 확인하려면 [전체 Azure IoT Hub 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: IoT Hub는 다중 테넌트 PaaS(Platform-as-a-Service)로, 여러 고객이 동일한 컴퓨팅, 네트워킹 및 스토리지 하드웨어 리소스 풀을 공유합니다. IoT Hub의 호스트 이름은 인터넷을 통해 공개적으로 라우팅할 수 있는 IP 주소를 갖는 퍼블릭 엔드포인트에 매핑됩니다. 이 IoT Hub 퍼블릭 엔드포인트는 여러 고객이 공유하며, 광역 네트워크와 온-프레미스 네트워크에 있는 IoT 디바이스가 여기에 액세스할 수 있습니다. Microsoft는 각 테넌트의 데이터 간에 완전한 격리를 위해 서비스를 설계하고 이러한 결과를 보장하기 위해 지속적으로 노력합니다.

메시지 라우팅, 파일 업로드, 대량 디바이스 가져오기/내보내기를 비롯한 IoT Hub 기능을 사용할 때도 IoT Hub에서 고객 소유 Azure 리소스에 대한 퍼블릭 엔드포인트를 통한 연결이 필요합니다. 이러한 연결 경로는 종합적으로 IoT Hub에서 고객 리소스까지의 송신 트래픽을 구성합니다.

격리된 네트워크에서 연결 노출을 줄이고 온-프레미스 네트워크를 Azure 백본 네트워크에 직접 연결할 수 있도록 현재 소유하고 운영하는 가상 네트워크를 통해 Azure IoT Hub를 포함한 Azure 리소스에 대한 연결을 제한하는 것이 좋습니다. Azure Private Link 및 Azure 프라이빗 엔드포인트(가능한 경우)를 사용하여 다른 가상 네트워크에서 서비스에 대해 프라이빗 액세스를 사용하도록 설정합니다. 

프라이빗 액세스를 설정한 후에는 IoT Hub에 대한 공용 네트워크 액세스를 사용하지 않도록 설정하여 보안을 강화합니다. 이 네트워크 수준 제어는 특정 IoT Hub 리소스에 적용되므로 격리가 보장됩니다. 공용 경로를 사용하여 다른 고객 리소스에 대해 서비스를 계속 활성화하기 위해 공용 엔드포인트는 확인 가능한 상태로 유지되고, IP 주소는 검색 가능하며, 포트는 열린 상태로 유지됩니다. Microsoft는 여러 계층의 보안을 통합하여 테넌트 간의 완전한 격리를 보장하므로 이는 문제가 되지 않습니다.

원치 않는 액세스를 방지하기 위해 디바이스의 오픈 하드웨어 포트를 최소한의 상태로 유지합니다. 또한 디바이스의 물리적 변조를 방지하거나 검색하는 메커니즘을 빌드합니다.

- [IoT 가상 네트워크 지원](virtual-network-support.md)

- [IoT Hub에 대한 공용 네트워크 액세스 관리](iot-hub-public-network-access.md)

- [Azure에서 테넌트 격리](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices#tenant-level-isolation)

- [loT 네트워킹 모범 사례](https://docs.microsoft.com/azure/iot-fundamentals/security-recommendations#networking)

- [Azure Private Link 개요](../private-link/private-link-overview.md)

- [Azure 네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷, NIC의 구성과 트래픽 모니터링 및 기록

**지침**: Azure Security Center를 사용하고 네트워크 보호 권장 사항에 따라 Azure 네트워크 리소스의 보안을 유지합니다. 네트워크 보안 그룹 흐름 로그를 사용하도록 설정하고 감사를 위해 로그를 Azure Storage 계정으로 보냅니다. 또한 흐름 로그를 Log Analytics 작업 영역으로 보낸 다음, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 패턴에 대한 인사이트를 제공합니다. 트래픽 분석의 몇 가지 장점으로는 네트워크 활동을 시각화하고, 핫 스폿과 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 네트워크 구성 오류를 정확히 찾아내는 기능이 있습니다.
 
- [네트워크 보안 그룹 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)
 
- [Azure Security Center에서 제공하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: IoT Hub IP 필터 규칙을 사용하여 알려진 악성 IP를 차단합니다. 또한 IoT용 Azure Security Center를 통해 악성 시도가 기록되고 경고됩니다.

Azure DDoS Protection Basic은 이미 사용하도록 설정되어 있으며 IoT Hub의 일부로 추가 비용 없이 사용할 수 있습니다. 항상 켜져 있는 모니터링과 일반적인 네트워크 수준 공격의 실시간 완화는 Microsoft의 온라인 서비스에서 활용하는 것과 동일한 방어를 제공합니다. Azure 글로벌 네트워크의 전체 규모를 활용하여 지역 간에 공격 트래픽을 분산하고 완화할 수 있습니다.

- [IoT Hub IP 필터](iot-hub-ip-filtering.md)

- [의심스러운 IP 주소 통신 IoT용 Azure Security Center](/azure/asc-for-iot/concept-security-alerts)

- [Azure DDoS Protection Basic 관리](../ddos-protection/ddos-protection-overview.md)

- [위협 방지 및 Azure Security Center](../security-center/azure-defender.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: 해당 사항 없음. 이 권장 사항은 고객이 기록하고 볼 수 있는 네트워크 패킷을 생성하는 제품을 위한 것입니다. IoT Hub는 고객이 대면하는 네트워크 패킷을 생성하지 않으며, Azure 가상 네트워크에 직접 배포하도록 설계되지 않았습니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: 페이로드 검사 기능을 사용하여 IDS/IPS 기능을 지원하는 Azure Marketplace 제품을 선택합니다.  페이로드 검사가 필요하지 않은 경우 Azure Firewall 위협 인텔리전스를 사용할 수 있습니다. Azure Firewall 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인 간의 트래픽을 경고 및/또는 차단하는 데 사용됩니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

조직의 각 네트워크 경계에 선택한 방화벽 솔루션을 배포하여 악성 트래픽을 탐지 및/또는 차단합니다. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall을 배포하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall을 사용하여 경고를 구성하는 방법](../firewall/threat-intel.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Azure IoT Hub 인스턴스에 액세스해야 하는 리소스의 경우, 가상 네트워크 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure Firewall에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: AzureIoTHub)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

- [Azure IoT에 서비스 태그를 사용하는 방법](iot-hub-understand-ip-address.md)
- [서비스 태그를 사용하는 방법에 대한 자세한 정보](../virtual-network/service-tags-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Policy를 사용하여 Azure IoT Hub 네임스페이스와 연결된 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.Devices" 및 "Microsoft.Network" 네임스페이스에서 Azure Policy 별칭을 사용하여 Machine Learning 네임스페이스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: Azure IoT Hub 배포와 연결된 네트워크 리소스에 태그를 사용하여 이러한 리소스를 논리적인 분류법으로 구성합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고, Azure IoT Hub 관련 네트워크 리소스에 대한 변경 내용을 검색합니다. 중요한 네트워크 리소스에 변경 내용이 발생하는 경우 트리거하는 Azure Monitor 내에서 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor를 통해 로그를 수집하여 Azure IoT Hub에서 생성된 보안 데이터를 수집합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, 스토리지 계정을 장기/보관 스토리지에 사용할 수 있습니다. 또는 Azure Sentinel 또는 타사 SIEM(Security Incident and Event Management)을 사용하도록 설정하고 데이터를 온보딩할 수 있습니다.

- [Azure IoT 로그 설정](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 감사, 보안 및 리소스 로그에 액세스하기 위해 Azure 리소스에 대한 Azure IoT 진단 설정을 사용하도록 설정합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다.

- [Azure IoT Hub 로그 설정](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure의 로깅 및 여러 로그 형식 이해](../azure-monitor/essentials/platform-logs-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Devices**:

[!INCLUDE [Resource Policy for Microsoft.Devices 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.devices-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure IoT Hub 인스턴스와 연결된 Log Analytics 작업 영역에 대한 로그 보존 기간을 설정합니다.

- [로그 보존 매개 변수를 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure IoT Hub에서 로그를 분석하고 모니터링하여 비정상 동작이 있는지 확인하고 결과를 정기적으로 검토합니다. Azure Monitor 및 Log Analytics 작업 영역을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행할 수 있습니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Azure IoT 상태 모니터링](monitor-iot-hub.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)
  
- [Log Analytics 쿼리 시작](../azure-monitor/logs/log-analytics-tutorial.md)
   
- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/logs/get-started-queries.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: 보안 로그 및 이벤트에서 발견된 비정상 활동을 모니터링하고 경고하기 위해 Log Analytics 작업 영역에서 IoT용 Azure Security Center를 사용합니다. 또는 데이터를 사용하도록 설정하고 Azure Sentinel에 온보딩할 수 있습니다. Azure Monitor를 사용하여 트래픽이 예기치 않게 떨어지는 경우와 같이 보안에 영향을 미칠 수 있는 운영 경고를 정의할 수도 있습니다.

- [Azure IoT Hub 상태 모니터링](monitor-iot-hub.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [IoT용 Azure Security Center 경고](/azure/asc-for-iot/concept-security-alerts)

- [로그 분석 로그 데이터에 경고하는 방법](../azure-monitor/alerts/tutorial-response.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당 없음. Azure IoT Hub는 맬웨어 방지 관련 로그를 처리하거나 생성하지 않습니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 없음. Azure IoT Hub는 DNS 관련 로그를 처리하거나 생성하지 않습니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure RBAC(역할 기반 액세스 제어)를 사용하면 역할 할당을 통해 Azure IoT Hub에 관한 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 주체 및 관리 ID에 할당할 수 있습니다. 특정 리소스에 대해 미리 정의된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal과 같은 도구를 통해 쿼리하거나 인벤토리에 포함할 수 있습니다.

- [PowerShell을 사용하여 Azure AD(Azure Active Directory)에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 구성원을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure IoT Hub 리소스에 대한 액세스 관리는 Azure AD(Azure Active Directory)를 통해 제어됩니다. Azure AD에는 기본 암호 개념이 없습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다.

Azure AD(Azure Active Directory) Privileged Identity Management 및 Azure Resource Manager를 사용하여 관리 계정에 Just-in-time 액세스를 사용하도록 설정할 수도 있습니다.

- [Privileged Identity Management에 대한 자세한 정보](/azure/active-directory/privileged-identity-management/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: IoT Hub에 액세스하는 사용자의 경우 Azure AD(Azure Active Directory) SSO를 사용합니다. Azure Security Center의 ID 및 액세스 권장 사항을 사용합니다.

- [Azure AD에서 사용하는 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory) 다단계 인증을 사용하여 전체 Azure 테넌트를 보호함으로써 모든 서비스에 혜택을 줍니다. IoT Hub 서비스는 다단계 인증을 지원하지 않습니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 상승된 권한이 필요한 관리 작업에 PAW(Privileged Access Workstation)를 사용합니다.

- [안전한 Privileged Access Workstation 이해](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: Azure AD(Azure Active Directory) 보안 보고서를 사용하고 모니터링하여 사용 환경에서 의심스럽거나 안전하지 않은 활동 발생을 탐지합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: IoT Hub에 액세스하는 사용자의 경우 조건부 액세스가 지원되지 않습니다. 이를 완화하려면 Azure AD(Azure Active Directory) 명명된 위치를 사용하여 전체 Azure 테넌트에 대한 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스를 허용하여 IoT Hub를 포함한 모든 서비스에 혜택을 줍니다.

- [Azure AD 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: IoT Hub에 액세스하는 사용자의 경우 Azure AD(Azure Active Directory)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

디바이스 및 서비스 액세스의 경우 IoT Hub는 보안 토큰 및 SAS(공유 액세스 서명) 토큰을 사용하여 네트워크에서 키를 보내지 않도록 디바이스 및 서비스를 인증합니다. 

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT Hub 보안 토큰](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory)는 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure AD ID 및 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

사용 환경에서 의심스러운 작업이나 안전하지 않은 활동이 발생하는 경우 Azure AD PIM(Privileged Identity Management)을 사용하여 로그 및 경고를 생성할 수 있습니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

- [Azure AD PIM(Privileged Identity Management) 배포](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: 모든 SIEM/모니터링 도구와 통합할 수 있는Azure AD(Azure Active Directory) 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보내면 이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

Azure Monitor 리소스 로그를 사용하여 연결 범주에서 무단 연결 시도를 모니터링합니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [IoT Hub에 대한 리소스 로그 구성](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory) ID 보호 기능을 사용하여 사용자 ID와 관련해 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 추가 조사를 위해 Azure Sentinel에 데이터를 수집할 수도 있습니다.

- [Azure AD 위험한 로그인을 보는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: Microsoft에서 고객 데이터에 액세스해야 하는 지원 시나리오에서는 Microsoft가 고객에게 직접 요청을 받습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.
 
- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 환경 유형, 데이터 민감도 수준 등의 개별 보안 도메인에 별도의 구독 및 관리 그룹을 사용하여 격리를 구현합니다. 애플리케이션 및 엔터프라이즈 환경에서 요구하는 Azure 리소스에 대한 액세스 수준을 제한할 수 있습니다. Azure RBAC를 통해 Azure 리소스에 대한 액세스를 제어할 수 있습니다.
  
- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 네트워크 경계에서 Azure Marketplace의 타사 솔루션을 사용하여 중요한 정보의 무단 전송 여부를 모니터링하고 그러한 전송을 차단하며 정보 보안 전문가에게 경고합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터가 손실되거나 노출되지 않도록 보호합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: IoT Hub는 TLS(전송 계층 보안)를 사용하여 IoT 디바이스 및 서비스의 연결 보안을 유지합니다. 현재 세 가지 버전의 TLS 프로토콜(버전 1.0, 1.1 및 1.2)이 지원됩니다. IoT Hub에 연결할 때 TLS 1.2를 기본 TLS 버전으로 사용하는 것이 좋습니다.

해당하는 경우 미사용 암호화 및 전송 중인 암호화에 대한 Azure Security Center의 권장 사항을 따릅니다.

- [IoT Hub의 TLS 지원](iot-hub-tls-support.md)
- [Azure를 사용하여 전송 중인 암호화 이해](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류, 손실 방지 기능은 아직 Azure IoT Hub에서 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 Azure 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하며 고객 데이터가 손실되거나 노출되지 않도록 최선을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 관리

**지침**: IoT Hub에 대한 컨트롤 플레인 사용자 액세스의 경우 Azure RBAC를 사용하여 액세스를 제어합니다. IoT Hub에 대한 데이터 평면 액세스의 경우 IoT Hub에 대한 공유 액세스 정책을 사용합니다.

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)

- [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용하여 Azure IoT Hub의 프로덕션 인스턴스 및 기타 중요 리소스 또는 관련 리소스에 대한 변경 사항이 발생하는 경우에 알리는 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [Azure Security Benchmark: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀을 위한 자동화된 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검색 비교

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Azure 리소스에 태그(모든 리소스가 태그를 지원하는 것은 아니지만 대부분 태그를 지원함)를 적용하여 논리적인 분류법으로 구성합니다.

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

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인된 Azure 리소스 및 컴퓨팅 리소스에 승인된 소프트웨어의 인벤토리를 만듭니다.

각 IoT Hub에는 서비스에서 디바이스마다 리소스를 만드는 데 사용할 수 있는 ID 레지스트리가 있습니다. 개별 또는 그룹 디바이스 ID를 허용 목록 또는 차단 목록에 추가하여 디바이스 액세스를 완벽하게 제어할 수 있습니다.

- [IoT Hub ID 레지스트리](iot-hub-devguide-identity-registry.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy를 사용하여 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정합니다. 

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하고 검색합니다.  환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음의 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

* 허용되지 않는 리소스 종류
* 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)
- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: Azure AD(Azure Active Directory) 조건부 액세스를 사용하여 "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용하여 Azure Iot Hub 서비스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.Devices" 네임스페이스에 Azure Policy 별칭을 사용하여 Azure IoT Hub 서비스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다.

Azure Resource Manager는 템플릿을 JSON(JavaScript Object Notation) 형식으로 내보낼 수 있는 기능이 있으며, 내보낸 템플릿은 검토하여 구성이 조직의 보안 요구 사항을 충족하는지 확인해야 합니다.

Azure Security Center의 권장 사항을 Azure 리소스에 대한 보안 구성 기준으로 사용할 수도 있습니다.

- [사용 가능한 Azure Policy 별칭을 확인하는 방법](/powershell/module/az.resources/get-azpolicyalias)

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Portal에서 템플릿으로 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다. 또한 Azure Resource Manager 템플릿을 사용하여 조직에서 요구하는 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다.  
 
- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)
- [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)
- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure IoT Hub 또는 관련 리소스에 사용자 지정 Azure Policy 정의를 사용하는 경우 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 설명서](/azure/devops/repos)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: "Microsoft.Devices" 네임스페이스에서 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)
- [별칭을 사용하는 방법](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구 배포

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Security Center를 사용하여 Azure 리소스에 대한 기준 검사를 수행합니다. 또한 Azure Policy를 사용하여 Azure 리소스 구성을 경고하고 감사합니다. 
 
- [Azure Security Center에서 권장 사항을 수정하는 방법](../security-center/security-center-remediate-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**책임**: 해당 없음

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: IoT Hub는 보안 토큰 및 SAS(공유 액세스 서명) 토큰을 사용하여 네트워크에서 키를 보내지 않도록 디바이스 및 서비스를 인증합니다. 

Azure Key Vault와 함께 관리 ID를 사용하여 클라우드 애플리케이션의 비밀 관리를 간소화합니다.

- [IoT Hub 보안 토큰](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [IoT Hub에 관리 ID를 사용하는 방법](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

- [키 자격 증명 모음을 만드는 방법](../key-vault/general/quick-create-portal.md)

- [관리 ID를 사용하여 Key Vault 인증을 제공하는 방법](../key-vault/general/assign-access-policy-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: IoT Hub는 보안 토큰 및 SAS(공유 액세스 서명) 토큰을 사용하여 네트워크에서 키를 보내지 않도록 디바이스 및 서비스를 인증합니다.

관리 ID를 사용하여 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [IoT Hub 보안 토큰](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [IoT Hub의 관리 ID를 구성하는 방법](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 
 
- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure Security Benchmark: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지는 Azure 서비스(예: Azure IoT Hub)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 고객 콘텐츠에서는 실행되지 않습니다.

비컴퓨팅 Azure 리소스에 업로드되는 콘텐츠를 미리 검사하는 것은 사용자의 책임입니다. Microsoft는 고객 데이터에 액세스할 수 없으므로 사용자를 대신해서 고객 콘텐츠의 맬웨어 방지 검사를 수행할 수 없습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure IoT Hub 서비스는 특정 비즈니스 목표에 따라 재해로부터 IoT Hub 서비스의 가용성을 높이고 복구할 수 있도록 하는 방법과 프레임워크를 제공합니다. 

- [IoT Hub 고가용성 및 재해 복구](iot-hub-ha-dr.md)

- [IoT Hub를 복제하는 방법](iot-hub-how-to-clone.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Azure IoT Hub는 보조 IoT Hub가 솔루션에 연결할 수 있는 모든 디바이스 ID를 포함하는 것을 권장합니다. 솔루션은 지역에서 복제된 디바이스 ID의 백업을 유지하고 이를 보조 IoT Hub로 업로드한 후 디바이스에 대한 활성 엔드포인트를 전환해야 합니다. 이 경우 IoT Hub의 디바이스 ID 내보내기 기능은 유용합니다.

- [IoT Hub 고가용성 및 재해 복구](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [IoT Hub 디바이스 ID 내보내기](iot-hub-bulk-identity-mgmt.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: Azure IoT Hub는 보조 IoT Hub가 솔루션에 연결할 수 있는 모든 디바이스 ID를 포함하는 것을 권장합니다. 솔루션은 지역에서 복제된 디바이스 ID의 백업을 유지하고 이를 보조 IoT Hub로 업로드한 후 디바이스에 대한 활성 엔드포인트를 전환해야 합니다. 이 경우 IoT Hub의 디바이스 ID 내보내기 기능은 유용합니다.

정기적으로 백업에서 콘텐츠의 데이터 복원을 수행합니다. 백업된 고객 관리형 키를 복원할 수 있는지 확인합니다.

- [IoT Hub 고가용성 및 재해 복구](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [IoT Hub 디바이스 ID 내보내기](iot-hub-bulk-identity-mgmt.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Key Vault에서 일시 삭제 및 제거 보호를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다. Azure Storage를 사용하여 백업을 저장하는 경우 BLOB 또는 BLOB 스냅샷이 삭제될 때 일시 삭제를 사용하여 데이터를 저장하고 복구할 수 있습니다.

 
- [Azure RBAC 이해](../role-based-access-control/overview.md)

- [Azure Blob Storage에 대한 일시 삭제](../storage/blobs/soft-delete-blob-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 개발합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다. 
  
- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용하여 고유한 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center가 결과에 얼마나 확신하는지 또는 경고를 발생하는 데 분석적으로 사용되었는지, 그리고 경보를 유발한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기반으로 합니다.

  
 또한 태그를 사용하여 구독을 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.
  
- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)
  
- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별한 다음, 필요에 따라 대응 계획을 수정합니다.
  
- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.
  
- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별할 수 있도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 권장 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.
  
- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)
 
- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 응답을 자동으로 트리거하여 Azure 리소스를 보호합니다.
  
- [Security Center에서 워크플로 자동화를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**참고 자료**: 침투 테스트가 Microsoft 정책을 위반하지 않도록 Microsoft 클라우드 침투 테스트 시행 규칙을 따릅니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다.

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
