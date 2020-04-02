---
title: 이벤트 허브에 대한 Azure 보안 기준
description: 이벤트 허브에 대한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 10fc822f9f36512405dd4e3b5aba6270b53e163f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549039"
---
# <a name="azure-security-baseline-for-event-hubs"></a>이벤트 허브에 대한 Azure 보안 기준

이벤트 허브에 대한 Azure 보안 기준에는 배포의 보안 상태를 개선하는 데 도움이 되는 권장 사항이 포함되어 있습니다.

이 서비스의 기준은 Azure [Security 벤치마크 버전 1.0에서](https://docs.microsoft.com/azure/security/benchmarks/overview)가져온 것으로, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요를](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)참조하십시오.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하십시오.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: 가상 네트워크에서 네트워크 보안 그룹 또는 Azure 방화벽을 사용하여 리소스 보호

**지침**: 이벤트 허브와 가상 네트워크 서비스 엔드포인트를 통합하면 가상 네트워크에 바인딩된 가상 컴퓨터와 같은 워크로드에서 메시징 기능에 안전하게 액세스할 수 있으며 네트워크 트래픽 경로가 양쪽 끝에 보호됩니다.

하나 이상의 가상 네트워크 서브넷 서비스 엔드포인트에 바인딩되면 각 Event Hubs 네임스페이스는 더 이상 가상 네트워크의 권한이 부여된 서브넷을 제외한 다른 곳에서의 트래픽을 허용하지 않습니다. 가상 네트워크 관점에서 Event Hubs 네임스페이스를 서비스 끝점으로 바인딩하면 가상 네트워크 서브넷에서 메시징 서비스로 격리된 네트워킹 터널이 구성됩니다. 

Azure 개인 링크 서비스를 사용하여 Azure Event Hubs 서비스에 개인적으로 안전하게 연결하는 네트워크 인터페이스인 개인 끝점을 만들 수도 있습니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 

방화벽을 사용하여 Azure 이벤트 허브 네임스페이스를 보호할 수도 있습니다. Azure Event Hubs는 인바운드 방화벽 지원을 위한 IP 기반 액세스 제어를 지원합니다. Azure 포털, Azure 리소스 관리자 템플릿을 사용하거나 Azure CLI 또는 Azure PowerShell을 통해 방화벽 규칙을 설정할 수 있습니다.

Azure 이벤트 허브에서 가상 네트워크 서비스 끝점을 사용하는 방법:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

자세한 내용은 Azure 개인 링크와 Azure 이벤트 https://docs.microsoft.com/azure/event-hubs/private-link-service허브 통합을 참조하십시오.

이벤트 허브 네임스페이스에서 가상 네트워크 통합 및 방화벽 사용:https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Azure 이벤트 허브 네임스페이스에 대한 IP 방화벽 규칙을 구성하는 방법:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NIC의 구성 및 트래픽을 모니터링하고 기록합니다.

**지침**: Azure 보안 센터를 사용하고 네트워크 보호 권장 사항을 수행하여 Azure에서 이벤트 허브 리소스를 보호합니다. Azure 가상 컴퓨터를 사용하여 이벤트 허브에 액세스하는 경우 NSG(네트워크 보안 그룹) 흐름 로그를 활성화하고 트래픽 감사를 위해 저장소 계정으로 로그를 보냅니다.

NSG 흐름 로그를 활성화하는 방법:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure 보안 센터에서 제공하는 네트워크 보안 이해:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당되지 않음; 이 권장 사항은 Azure 앱 서비스에서 실행중인 웹 응용 프로그램 또는 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 이벤트 허브와 연결된 가상 네트워크에서 DDoS 보호 표준을 사용하여 분산 서비스 거부(DDoS) 공격방지를 수행합니다. Azure 보안 센터 통합 위협 인텔리전스를 사용하여 알려진 악성 또는 사용되지 않는 인터넷 IP 주소와의 통신을 거부합니다.

DDoS 보호를 구성하는 방법:[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](/azure/virtual-network/manage-ddos-protection)

Azure 보안 센터 통합 위협 인텔리전스에 대한 자세한 내용은 다음을 참조하십시오.https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Azure 가상 컴퓨터를 사용하여 이벤트 허브에 액세스하는 경우 NSG(네트워크 보안 그룹) 흐름 로그를 사용하도록 설정하고 트래픽 감사를 위해 저장소 계정으로 로그를 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역으로 보내고 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 통찰력을 제공할 수도 있습니다. 트래픽 분석의 몇 가지 장점은 네트워크 활동을 시각화하고 핫스팟을 식별하고, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하고, 네트워크 잘못된 구성을 정확히 찾아낼 수 있다는 점입니다.

비정상적인 활동을 조사하는 데 필요한 경우 네트워크 감시자 패킷 캡처를 사용하도록 설정합니다.

NSG 흐름 로그를 활성화하는 방법:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

트래픽 분석을 활성화하고 사용하는 방법:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

네트워크 감시자 사용 방법:https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템(IDS/IPS) 배포

**지침**: Azure 가상 컴퓨터를 사용하여 이벤트 허브에 액세스하는 경우 페이로드 검사 기능을 사용하여 IDS/IPS 기능을 지원하는 Azure Marketplace에서 오퍼를 선택합니다. 조직에 페이로드 검사를 기반으로 하는 침입 감지 및/또는 방지가 필요하지 않은 경우 Azure Event Hubs의 기본 제공 방화벽 기능을 사용할 수 있습니다. 방화벽 규칙을 사용하여 제한된 범위의 IP 주소 또는 특정 IP 주소에 대한 이벤트 허브 네임스페이스에 대한 액세스를 제한할 수 있습니다.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

지정된 IP 주소에 대해 이벤트 허브에 방화벽 규칙을 추가하는 방법:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure 보안 센터 모니터링**: 아직 사용할 수 없습니다.

**책임**: 고객

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션으로의 트래픽 관리

**지침**: 해당되지 않음; 이 권장 사항은 Azure 앱 서비스에서 실행중인 웹 응용 프로그램 또는 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 이 권장 사항은 Azure App Service에서 실행되는 웹 응용 프로그램 또는 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대한 표준 보안 구성 유지

**지침**: Azure Policy를 통해 Azure Event Hubs 네임스페이스와 연결된 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.EventHub" 및 "Microsoft.Network" 네임스페이스에서 Azure 정책 별칭을 사용하여 이벤트 허브 네임스페이스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 다음과 같은 Azure 이벤트 허브와 관련된 기본 제공 정책 정의를 사용할 수도 있습니다.

- 이벤트 허브는 가상 네트워크 서비스 엔드포인트를 사용해야 합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

이벤트 허브 네임스페이스에 대한 Azure 기본 제공 정책:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



네트워킹을 위한 Azure 정책 샘플:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Azure Blueprint를 만드는 방법:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: 가상 네트워크 및 이벤트 허브와 연결된 네트워크 보안 및 트래픽 흐름과 관련된 기타 리소스에 태그를 사용합니다.

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 을 모니터링하고 변경 사항을 감지합니다.

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고 Azure 이벤트 허브와 관련된 네트워크 리소스의 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 네트워크 리소스에 대한 변경이 발생할 때 트리거되는 경고를 만듭니다.

Azure 활동 로그 이벤트를 보고 검색하는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure 모니터에서 경고를 만드는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하십시오.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 소스 사용

**지침**: 해당되지 않음; Microsoft는 로그의 타임스탬프에 대해 Azure 이벤트 허브와 같은 Azure 리소스에 사용되는 시간 원본을 유지 관리합니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor 내에서 활동 로그 및 이벤트 허브 진단 설정 내의 이벤트 허브와 관련된 로그를 구성하여 로그를 로그 분석 작업 영역으로 보내어 쿼리할 수 있도록 하거나 장기 보관 저장소를 위한 저장소 계정으로 보냅니다.

Azure 이벤트 허브에 대한 진단 설정을 구성하는 방법:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Azure 활동 로그 이해:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure 이벤트 허브 네임스페이스에 대한 진단 설정을 활성화합니다. Azure 이벤트 허브에 대한 진단 설정에는 아카이브 로그, 운영 로그 및 자동 크기 조정 로그라는 세 가지 범주가 있습니다. 운영 로그를 사용하여 이벤트 허브 작업 중 발생하는 상황, 특히 이벤트 허브 만들기, 사용된 리소스 및 작업 상태를 포함한 작업 유형에 대한 정보를 캡처할 수 있습니다.

또한 Azure Activity 로그 진단 설정을 활성화하고 Azure 저장소 계정, 이벤트 허브 또는 로그 분석 작업 영역으로 보낼 수 있습니다. 활동 로그는 Azure Event Hubs 및 기타 리소스에서 수행된 작업에 대한 통찰력을 제공합니다. 활동 로그를 사용하여 Azure Event Hubs 네임스페이스에서 수행된 모든 쓰기 작업(PUT, POST, DELETE)에 대해 "무엇을, 누가, 언제"를 결정할 수 있습니다.

Azure 이벤트 허브에 대한 진단 설정을 활성화하는 방법:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Azure 활동 로그에 대한 진단 설정을 활성화하는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정하여 이벤트 허브 관련 인시던트를 캡처하고 검토합니다.

로그 분석 작업 영역에 대한 로그 보존 매개 변수를 설정하는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작이 없는 지 로그를 분석 및 모니터링하고 이벤트 허브와 관련된 결과를 정기적으로 검토합니다. Azure 모니터의 로그 분석을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 Azure Sentinel 또는 타사 SIEM에 대한 데이터를 활성화및 온보드할 수 있습니다.
 

로그 분석 작업 영역에 대한 자세한 내용은 다음을 참조하십시오.https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure 모니터에서 사용자 지정 쿼리를 수행하는 방법:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Azure 센티넬 온보보드 방법:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대한 경고 사용

**지침**: Azure Monitor 내에서 활동 로그 내의 Azure Event Hubs와 관련된 로그를 구성하고 이벤트 허브 진단 설정을 구성하여 로그를 로그 분석 작업 영역으로 보내어 쿼리하거나 장기 보관 저장소를 위한 저장소 계정으로 보냅니다. Log Analytics 작업 영역을 사용하여 보안 로그 및 이벤트에 있는 비정상적인 활동에 대한 경고를 만듭니다.

또는 Azure Sentinel에 대한 데이터를 활성화하고 온보드할 수 있습니다. 

Azure 활동 로그 이해:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Azure 이벤트 허브에 대한 진단 설정을 구성하는 방법:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

로그 분석 작업 영역 로그 데이터에 경고하는 방법:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Azure 센티넬 온보보드 방법:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 보안 센터 모니터링**: 아직 사용할 수 없습니다.

**책임**: 고객

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당되지 않음; 이벤트 허브는 맬웨어 방지 로깅을 처리하지 않습니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당되지 않음; 이벤트 허브는 DNS 관련 로그를 처리하거나 생성하지 않습니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [보안 제어: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조하십시오.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지

**지침**: AD(Azure Active Directory)에는 명시적으로 할당되어야 하고 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용하여 임시 쿼리를 수행하여 관리 그룹의 구성원인 계정을 검색합니다. 

PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 얻는 방법:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 구성원을 얻는 방법:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당하는 경우 기본 암호 변경

**지침**: Azure Active Directory(AD)를 통해 이벤트 허브에 대한 제어 평면 액세스가 제어됩니다. Azure AD에는 기본 암호의 개념이 없습니다.

이벤트 허브에 대한 데이터 평면 액세스는 관리되는 ID 또는 앱 등록과 공유 액세스 시그니처가 있는 Azure AD를 통해 제어됩니다. 공유 액세스 서명은 이벤트 허브에 연결하는 클라이언트에서 사용되며 언제든지 다시 생성할 수 있습니다.

이벤트 허브에 대한 공유 액세스 서명 이해:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure 보안 센터 ID 및 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되는 Azure 보안 센터의 권장 사항 또는 다음과 같은 기본 제공 Azure 정책을 사용할 수 있습니다.

- 구독에 둘 이상의 소유자를 할당해야 합니다.

- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.

- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

Azure 보안 센터를 사용하여 ID 및 액세스(미리 보기)를 모니터링하는 방법:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure 정책을 사용하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 단일 사인온(SSO) 사용

**지침**: Microsoft Azure는 Azure Active Directory(AD)를 기반으로 하는 리소스 및 응용 프로그램에 대한 통합 액세스 제어 관리를 제공합니다. Azure 이벤트 허브와 Azure AD를 사용하는 주요 이점은 더 이상 코드에 자격 증명을 저장할 필요가 없다는 것입니다. 대신 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. 토큰을 요청하는 리소스 이름은 https://eventhubs.azure.net/. Azure AD는 응용 프로그램을 실행하는 보안 주체(사용자, 그룹 또는 서비스 주체)를 인증합니다. 인증이 성공하면 Azure AD는 응용 프로그램에 대한 액세스 토큰을 반환하고 응용 프로그램은 액세스 토큰을 사용하여 Azure Event Hubs 리소스에 대한 요청을 승인할 수 있습니다.

Azure AD를 사용하여 응용 프로그램을 인증하여 이벤트 허브 리소스에 액세스하는 방법:https://docs.microsoft.com/azure/event-hubs/authenticate-application

Azure AD로 SSO 이해:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory 다단계 인증(MFA)을 사용하도록 설정하고 Azure 보안 센터 ID 및 액세스 관리 권장 사항을 따라 이벤트 허브 지원 리소스를 보호합니다.

Azure에서 MFA를 활성화하는 방법:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure 보안 센터 내에서 ID 및 액세스를 모니터링하는 방법:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(권한 있는 액세스 워크스테이션) 사용

**지침**: 이벤트 허브 지원 리소스에 로그인하고 구성하도록 구성된 MFA(다단계 인증)를 사용하여 권한 있는 액세스 워크스테이션(PAW)을 사용합니다.

권한 있는 액세스 워크스테이션에 대해 자세히 알아보기:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure에서 MFA를 활성화하는 방법:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생할 때 로그 및 경고를 생성하기 위해 AD(Azure Active Directory) 권한 ID 관리(PIM)를 사용합니다. Azure AD 위험 검색을 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 볼 수 있습니다. 추가 로깅을 위해 Azure Security Center 위험 감지 경고를 Azure Monitor로 보내고 작업 그룹을 사용하여 사용자 지정 경고/알림을 구성합니다.

권한 있는 ID 관리(PIM)를 배포하는 방법:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Azure AD 위험 감지 이해:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

사용자 지정 경고 및 알림을 위한 작업 그룹을 구성하는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스할 수 있습니다.



Azure에서 명명된 위치를 구성하는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="39-use-azure-active-directory"></a>3.9: Azure 활성 디렉터리 사용

**지침**: 이벤트 허브와 같은 Azure 리소스에 대한 중앙 인증 및 권한 부여 시스템으로 Azure Active Directory(AD)를 사용합니다. 이렇게 하면 RBAC(역할 기반 액세스 제어)를 관리중요한 리소스에 연결할 수 있습니다.

 Azure AD 인스턴스를 만들고 구성하는 방법:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Azure 이벤트 허브가 Azure Active Directory(AAD)와 통합하는 방법에 대해 알아보려면 Azure Active Directory를 사용하여 이벤트 허브 리소스에 대한 액세스 권한 부여를 참조하세요.https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 사용자 액세스를 정기적으로 검토하고 조정

**지침**: Azure Active Directory(AD)는 오래된 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure ID Access Reviews를 사용하여 그룹 구성원 자격, 엔터프라이즈 응용 프로그램에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스는 정기적으로 검토하여 올바른 사용자만 계속 액세스할 수 있도록 할 수 있습니다.

또한 이벤트 허브의 공유 액세스 서명을 정기적으로 회전합니다.

Azure AD 보고 이해:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure ID 액세스 검토를 사용하는 방법:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

이벤트 허브에 대한 공유 액세스 서명 이해:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 액세스하려는 시도를 모니터링합니다.

**지침**: 모든 SIEM/모니터링 도구와 통합할 수 있는 AD(Azure Active Directory) 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송하여 이 프로세스를 간소화할 수 있습니다. 로그 분석 내에서 원하는 로그 알림을 구성할 수 있습니다.

Azure 활동 로그를 Azure 모니터에 통합하는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Azure Active 디렉터리를 사용하여 이벤트 허브 리소스에 대한 액세스 권한을 부여합니다.https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Active Directory의 ID 보호 및 위험 검색 기능을 사용하여 이벤트 허브 지원 리소스와 관련된 의심스러운 작업을 검색하도록 자동화된 응답을 구성합니다. Azure Sentinel을 통해 자동화된 응답을 활성화하여 조직의 보안 응답을 구현해야 합니다.

Azure AD 위험한 로그인을 보는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure 센티넬 온보보드 방법:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중에 Microsoft에 관련 고객 데이터에 대한 액세스 제공

**지침**: 현재 사용할 수 없습니다. 고객 잠금 상자는 아직 이벤트 허브에 대 한 지원 되지 않습니다.

고객 잠금 상자 지원 서비스 목록:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조하십시오.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 민감한 정보의 인벤토리 유지

**지침**: 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적하는 데 도움이 되도록 이벤트 허브와 관련된 리소스에 태그를 사용합니다.

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 민감한 정보를 저장하거나 처리하는 시스템을 격리

**지침**: 개발, 테스트 및 프로덕션을 위해 별도의 구독 및/또는 관리 그룹을 구현합니다. 이벤트 허브 네임스페이스는 서비스 끝점을 활성화하고 적절하게 태그가 지정된 가상 네트워크로 구분되어야 합니다.

방화벽을 사용하여 Azure 이벤트 허브 네임스페이스를 보호할 수도 있습니다. Azure Event Hubs는 인바운드 방화벽 지원을 위한 IP 기반 액세스 제어를 지원합니다. Azure 포털, Azure 리소스 관리자 템플릿을 사용하거나 Azure CLI 또는 Azure PowerShell을 통해 방화벽 규칙을 설정할 수 있습니다.

추가 Azure 구독을 만드는 방법:https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법:https://docs.microsoft.com/azure/governance/management-groups/create

Azure 이벤트 허브 네임스페이스에 대한 IP 방화벽 규칙 구성:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

태그를 만들고 활용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

가상 네트워크를 만드는 방법:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 민감한 정보의 무단 전송 모니터링 및 차단

**지침**: 가상 컴퓨터를 사용하여 이벤트 허브에 액세스할 때 가상 네트워크, 서비스 끝점, 이벤트 허브 방화벽, 네트워크 보안 그룹 및 서비스 태그를 사용하여 데이터 유출 가능성을 완화합니다.

Microsoft는 Azure Event Hubs의 기본 인프라를 관리하고 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

Azure 이벤트 허브 네임스페이스에 대한 IP 방화벽 규칙 구성:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Azure 이벤트 허브를 통해 가상 네트워크 서비스 끝점 이해:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Azure 이벤트 허브를 Azure 개인 링크와 통합합니다.https://docs.microsoft.com/azure/event-hubs/private-link-service

네트워크 보안 그룹 및 서비스 태그 이해:https://docs.microsoft.com/azure/virtual-network/security-overview

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중의 모든 민감한 정보 암호화

**지침**: Azure 이벤트 허브는 기본적으로 TLS 암호화 통신을 적용합니다. TLS 버전 1.0, 1.1 및 1.2는 현재 지원됩니다. 그러나 TLS 1.0과 1.1은 업계 전반에서 중단되는 경로에 있으므로 가능한 경우 TLS 1.2를 사용하십시오.

이벤트 허브의 보안 기능을 이해하려면 네트워크 보안을 참조하십시오.https://docs.microsoft.com/azure/event-hubs/network-security

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure 이벤트 허브에서 데이터 식별, 분류 및 손실 방지 기능을 아직 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 민감한 것으로 취급하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 보습니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 공유

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스를 제어합니다.

**지침**: Azure Event Hubs는 AD(Azure Active Directory)를 사용하여 이벤트 허브 리소스에 대한 요청을 승인합니다. Azure AD를 사용하면 역할 기반 액세스 제어(RBAC)를 사용하여 사용자 또는 응용 프로그램 서비스 보안 주체일 수 있는 보안 주체에 대한 권한을 부여할 수 있습니다.

Azure AD RBAC 및 Azure 이벤트 허브에 사용할 수 있는 역할 이해:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.

Microsoft는 Event Hubs의 기본 인프라를 관리하고 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 미사용 정보 암호화

**지침**: Azure Event Hubs는 Microsoft에서 관리하는 키 또는 고객 관리 키를 사용하여 미사용 데이터를 암호화하는 옵션을 지원합니다. 이 기능을 사용하면 미사용 Azure Event Hubs 데이터를 암호화하는 데 사용되는 고객 관리 키에 대한 액세스를 생성, 회전, 비활성화 및 취소할 수 있습니다.

Azure 이벤트 허브를 암호화하기 위해 고객 관리 키를 구성하는 방법:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 사항에 대한 로그 및 경고

**지침**: Azure 활동 로그가 있는 Azure 모니터를 사용하여 Azure Event Hubs 및 기타 중요 하거나 관련 리소스의 프로덕션 인스턴스에 대한 변경 이 수행되는 시기에 대한 경고를 만듭니다.

Azure 활동 로그 이벤트에 대한 경고를 만드는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [보안 제어: 취약점 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조하십시오.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약점 검색 도구 실행

**지침**: 해당되지 않음; Microsoft는 이벤트 허브를 지원하는 기본 시스템에서 취약점 관리를 수행합니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당되지 않음; Microsoft는 이벤트 허브를 지원하는 기본 시스템에서 패치 관리를 수행합니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 해당되지 않음; 벤치마크는 컴퓨팅 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 백투백 취약점 검사 비교

**지침**: 해당되지 않음; Microsoft는 이벤트 허브를 지원하는 기본 시스템에서 취약점 관리를 수행합니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 발견된 취약점의 문제 해결에 우선 순위를 지정합니다.

**지침**: 해당되지 않음; Microsoft는 이벤트 허브를 지원하는 기본 시스템에서 취약점 관리를 수행합니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 관리: 인벤토리 및 자산 관리를](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)참조하십시오.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure 자산 검색 사용

**지침**: Azure 리소스 그래프를 사용하여 구독 내의 모든 리소스(Azure Event Hubs 네임스페이스 포함)를 쿼리하고 검색합니다. 테넌트에 적절한 (읽기) 권한이 있고 구독 내의 모든 Azure 구독과 리소스를 등록할 수 있는지 확인합니다.

Azure 그래프를 사용하여 쿼리를 만드는 방법:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure 구독을 보는 방법:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC 이해:https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적으로 분류로 구성합니다.

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 승인되지 않은 Azure 리소스 삭제

**지침:** 적절한 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 Azure Event Hubs 네임스페이스 및 관련 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고 승인되지 않은 리소스가 적시에 구독에서 삭제되도록 합니다.

추가 Azure 구독을 만드는 방법:https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법:https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당되지 않음; 이 권장 사항은 전체 리소스 및 Azure를 계산하기 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure 정책을 사용하여 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형에 대한 제한을 두는 경우:

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용하여 구독 내의 리소스를 쿼리/검색합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 그래프를 사용하여 쿼리를 만드는 방법:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

**지침**: 해당되지 않음; 이 권장 사항은 전체 리소스 및 Azure를 계산하기 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 신청서만 사용

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure 정책을 사용하여 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형에 대한 제한을 두는 경우:

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 정책을 사용하여 특정 리소스 유형을 거부하는 방법:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 신청 목록 구현

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>스크립트를 통해 Azure 리소스 관리자와 상호 작용하는 사용자의 기능 제한</div>

**지침**: "Microsoft Azure 관리" 앱에 대해 "액세스 차단"을 구성하여 사용자가 Azure 리소스 관리자와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

Azure 리소스 관리자에 대한 액세스를 차단하기 위해 조건부 액세스를 구성하는 방법:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 컴퓨팅 리소스 내에서 스크립트를 실행하는 사용자의 기능 제한

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 물리적 또는 논리적으로 고위험 응용 프로그램을 분리

**지침**: 해당되지 않음; 이 권장 사항은 Azure 앱 서비스에서 실행중인 웹 응용 프로그램 또는 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조하십시오.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Event Hubs 배포에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.EventHub" 네임스페이스에서 Azure Policy 별칭을 사용하여 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 다음과 같은 Azure 이벤트 허브에 대한 기본 제공 정책 정의를 사용할 수도 있습니다.

- Event Hub의 진단 로그를 사용하도록 설정해야 합니다.

- 이벤트 허브는 가상 네트워크 서비스 엔드포인트를 사용해야 함

이벤트 허브 네임스페이스에 대한 Azure 기본 제공 정책:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

사용 가능한 Azure 정책 별칭을 보는 방법:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 안전한 Azure 리소스 구성 유지

**지침**: Azure 정책 [거부] 및 [존재하지 않는 경우 배포]를 사용하여 Event Hubs 지원 리소스에서 보안 설정을 적용합니다. 

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Azure 정책 효과에 대한 자세한 내용은 다음을 참조하십시오.https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 안전한 운영 체제 구성 유지

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전하게 저장

**지침**: 이벤트 허브 또는 관련 리소스에 대한 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

Azure DevOps에 코드를 저장하는 방법:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 리포지토리 설명서:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: "Microsoft.EventHub" 네임스페이스에서 Azure 정책 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하기 위한 프로세스 및 파이프라인을 개발합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

**지침**: "Microsoft.EventHub" 네임스페이스에서 Azure 정책 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. Azure 정책 [감사], [거부]및 [존재하지 않는 경우 배포]를 사용하여 Azure Event Hubs 배포 및 관련 리소스에 대한 구성을 자동으로 적용합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제를 위한 자동화된 구성 모니터링 구현

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure 비밀을 안전하게 관리

**지침**: Azure App Service에서 실행되는 Azure 가상 컴퓨터 또는 웹 응용 프로그램의 경우 Azure Key Vault와 함께 관리되는 서비스 ID를 사용하여 Azure Event Hubs 배포에 대한 공유 액세스 서명 관리를 단순화하고 보호합니다. 키 볼트 소프트 삭제가 활성화되어 있는지 확인합니다.

Azure Active Directory를 사용하여 관리되는 ID를 인증하여 이벤트 허브 리소스에 액세스합니다.https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

이벤트 허브에 대해 고객 관리 키 를 구성합니다.https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Azure 관리 ID와 통합하는 방법:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

키 볼트를 만드는 방법:https://docs.microsoft.com/azure/key-vault/quick-create-portal

관리되는 ID로 키 볼트 인증을 제공하는 방법:https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: ID를 안전하고 자동으로 관리

**지침**: Azure App Service에서 실행되는 Azure 가상 컴퓨터 또는 웹 응용 프로그램의 경우 이벤트 허브에 액세스하는 데 사용중인 경우 Azure 키 자격 증명 모음과 함께 관리되는 서비스 ID를 사용하여 Azure Event Hub를 단순화하고 보호합니다. 키 볼트 소프트 삭제가 활성화되어 있는지 확인합니다.

관리되는 ID를 사용하여 Azure Active Directory(AD)에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드의 자격 증명 없이 Azure Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

Azure Active Directory를 사용하여 관리되는 ID를 인증하여 이벤트 허브 리소스에 액세스합니다.https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

이벤트 허브에 대해 고객 관리 키 를 구성합니다.https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

관리되는 ID를 구성하는 방법:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Azure 관리 ID와 통합하는 방법:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동하는 것을 권장합니다.

자격 증명 스캐너를 설정하는 방법:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조하십시오.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리되는 맬웨어 방지 소프트웨어 사용

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 Azure 서비스(예: Azure App Service)를 지원하는 기본 호스트에서 활성화되지만 고객 콘텐츠에서는 실행되지 않습니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비계산 Azure 리소스에 업로드할 파일을 미리 스캔합니다.

**지침**: Azure 이벤트 허브, 앱 서비스, 데이터 레이크 저장소, Blob 저장소, PostgreSQL용 Azure 데이터베이스 등과 같이 계산되지 않은 Azure 리소스에 업로드되는 모든 콘텐츠를 미리 검사합니다. 이러한 경우 Microsoft는 사용자의 데이터에 액세스할 수 없습니다.

Microsoft 맬웨어 방지는 Azure 서비스(예: Redis용 Azure 캐시)를 지원하는 기본 호스트에서 활성화되지만 고객 콘텐츠에서는 실행되지 않습니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조하십시오.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기적인 자동 백업 보장

**지침**: Azure 이벤트 허브에 대한 지역 재해 복구를 구성합니다. (가용성 영역을 사용하지 않는 경우)전체 Azure 지역 또는 데이터 센터에서 가동 중지 시간이 발생하면 데이터 처리가 다른 지역 또는 데이터 센터에서 계속 작동되는 것이 중요합니다. 따라서 지오 재해 복구 및 지리적 복제는 모든 기업에서 중요한 기능입니다. Azure Event Hubs는 네임스페이스 수준에서 지리적 재해 복구 및 지리적 복제를 둘 다 지원합니다. 

Azure 이벤트 허브에 대한 지역 재해 복구 이해:https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리 키 백업

**지침**: Azure 이벤트 허브는 Azure 저장소 서비스 암호화(Azure SSE)를 사용하여 미사용 데이터의 암호화를 제공합니다. 이벤트 허브는 데이터를 저장하기 위해 Azure Storage를 사용하며 기본적으로 Azure Storage에 저장된 모든 데이터는 Microsoft 관리 키를 사용하여 암호화됩니다. 고객 관리 키를 저장하기 위해 Azure Key Vault를 사용하는 경우 키의 정기적인 자동 백업을 보장합니다.

다음 PowerShell 명령: Backup-AzKeyVaultSecret를 통해 키 볼트 비밀의 정기적인 자동 백업 보장

미사용 Azure Event Hubs 데이터를 암호화하기 위해 고객 관리 키를 구성하는 방법:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

키 볼트 비밀을 백업하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함한 모든 백업의 유효성 검사

**지침**: 백업된 고객 관리 키의 복원을 테스트합니다.

 

Azure에서 키 자격 증명 모음 키를 복원 하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키 보호 보장

**지침**: 키 볼트에서 소프트 삭제를 활성화하여 실수로 또는 악의적인 삭제로부터 키를 보호합니다. Azure 이벤트 허브에서는 고객 관리 키가 소프트 삭제 및 제거 안 식 구성을 갖도록 해야 합니다.

이벤트 허브 데이터를 캡처하는 데 사용되는 Azure Storage 계정에 대해 소프트 삭제를 구성합니다. 이 기능은 Azure 데이터 레이크 저장소 세대 2에서 아직 지원되지 않습니다.

키 볼트에서 소프트 삭제를 활성화하는 방법:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

키를 가진 키 금고를 설정합니다:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Azure 저장소 Blob에 대한 소프트 삭제:https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조하십시오.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 사고 대응 가이드 만들기

**지침**: 인력의 역할과 사고 처리/관리 단계를 정의하는 서면 인시던트 대응 계획이 있는지 확인합니다.

Azure 보안 센터 내에서 워크플로 자동화를 구성하는 방법:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 채점 및 우선 순위 지정 절차 만들기

**지침**: 보안 센터는 각 경고에 참석하는 순서의 우선 순위를 지정하는 데 도움이 되도록 경고에 심각도를 할당하므로 리소스가 손상되면 즉시 확인할 수 있습니다. 심각도는 보안 센터가 찾기 또는 경고를 발행하는 데 사용되는 분석 및 경고로 이어진 활동의 배후에 악의적인 의도가 있다는 신뢰 수준에 얼마나 확신을 가지고 있는지에 따라 다집니다.

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 절차 테스트

**지침**: 정기적으로 시스템의 사고 대응 기능을 테스트하기 위한 연습을 수행합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

NIST의 간행물: IT 계획 및 기능에 대한 테스트, 교육 및 운동 프로그램 가이드를 참조하십시오.https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침:** Microsoft 보안 대응 센터(MSRC)에서 고객의 데이터가 불법적이거나 승인되지 않은 당사자가 액세스한 사실을 발견한 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 연락합니다.  사실 이후에 인시던트를 검토하여 문제가 해결되었는지 확인합니다. 

Azure 보안 센터 보안 연락처를 설정 하는 방법:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 사고 대응 시스템에 통합

**지침**: 지속적인 내보내기 기능을 사용하여 Azure 보안 센터 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 수동으로 또는 지속적으로 지속방식으로 경고 및 권장 사항을 내보낼 수 있습니다. Azure 보안 센터 데이터 커넥터를 사용하여 Sentinel 경고를 스트리밍할 수 있습니다.

연속 내보내기를 구성하는 방법:https://docs.microsoft.com/azure/security-center/continuous-export

Azure Sentinel로 경고를 스트리밍하는 방법:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 응답 자동화

**지침**: Azure 보안 센터의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "논리 앱"을 통해 응답을 자동으로 트리거합니다.

워크플로 자동화 및 논리 앱을 구성하는 방법:https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 빨간색 팀 연습을](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)참조하십시오.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 정기적인 침투 테스트를 수행하고 60일 이내에 모든 중요한 보안 결과의 수정을 보장합니다.

**지침**: 보급 시험이 Microsoft 정책을 위반하지 않도록 Microsoft 참여 https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1규칙을 따르십시오.
Microsoft 관리 클라우드 인프라, 서비스 및 응용 프로그램에 대한 Microsoft의 전략 및 Red Teaming 및 라이브 사이트 침투 테스트실행에 대한 자세한 내용은 여기에서 확인할 수 있습니다.https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

## <a name="next-steps"></a>다음 단계

- Azure [보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/overview) 보기
- [Azure 보안 기준에](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) 대해 자세히 알아보기
