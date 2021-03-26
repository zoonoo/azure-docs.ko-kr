---
title: Event Hubs에 대 한 Azure 보안 기준
description: Event Hubs 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 57862a450d313f5d8850b14047ecc3d25d6ba5c1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563687"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Event Hubs에 대 한 Azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 1.0](../security/benchmarks/overview-v1.md) 의 지침을 Event Hubs 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Event Hubs에 적용 되는 관련 지침에 따라 그룹화 됩니다. Event Hubs에 적용할 수 없는 **컨트롤** 은 제외 되었습니다.

 
Event Hubs 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Event Hubs 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Azure Event Hubs와 가상 네트워크 서비스 끝점을 통합 하면 가상 네트워크에 바인딩된 가상 컴퓨터와 같은 작업에서 메시징 기능에 안전 하 게 액세스할 수 있으며, 두 end에서 네트워크 트래픽 경로를 안전 하 게 보호할 수 있습니다.

하나 이상의 가상 네트워크 서브넷 서비스 끝점에 바인딩되면 해당 Event Hubs 네임 스페이스는 더 이상 가상 네트워크에 있는 모든 권한이 있는 서브넷의 트래픽을 허용 하지 않습니다. 가상 네트워크 관점에서, Event Hubs 네임 스페이스를 서비스 끝점에 바인딩하면 가상 네트워크 서브넷에서 메시징 서비스로 격리 된 네트워킹 터널이 구성 됩니다. 

또한 Azure 개인 링크 서비스를 사용 하 여 Event Hubs 서비스에 안전 하 게 연결 하는 네트워크 인터페이스인 개인 끝점을 만들 수 있습니다. 개인 끝점은 가상 네트워크의 개인 IP 주소를 사용 하 여 서비스를 가상 네트워크에 효과적으로 제공 합니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 

방화벽을 사용 하 여 Azure Event Hubs 네임 스페이스를 보호할 수도 있습니다. Event Hubs는 인바운드 방화벽 지원에 대 한 IP 기반 액세스 제어를 지원 합니다. Azure Portal, Azure Resource Manager 템플릿 또는 Azure CLI 또는 Azure PowerShell를 사용 하 여 방화벽 규칙을 설정할 수 있습니다.

- [Azure Event Hubs에서 virtual network 서비스 끝점을 사용 하는 방법](event-hubs-service-endpoints.md)

- [Azure Private Link와 Azure Event Hubs 통합](private-link-service.md)

- [Azure Event Hubs 네임 스페이스에 대 한 IP 방화벽 규칙을 구성 하는 방법](event-hubs-ip-filtering.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure 보안 벤치 마크](/azure/governance/policy/samples/azure-security-benchmark) 는 Security Center에 대 한 기본 정책 이니셔티브 이며 [Security Center 권장 사항의](/azure/security-center/security-center-recommendations)기초가 됩니다. 이 컨트롤과 관련 된 Azure Policy 정의는 Security Center에 의해 자동으로 설정 됩니다. 이 컨트롤과 관련 된 경고에는 관련 서비스에 대 한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의-Microsoft EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure Security Center을 사용 하 고 네트워크 보호 권장 사항을 따라 Azure에서 Event Hubs 리소스를 보호 합니다. Azure virtual machines를 사용 하 여 event hubs에 액세스 하는 경우 네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하 고 로그를 트래픽 감사에 대 한 저장소 계정으로 보냅니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS (배포 된 서비스 거부) 공격 으로부터 보호 하기 위해 event hubs와 연결 된 가상 네트워크에서 DDoS Protection Standard를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

- [DDoS 보호를 구성 하는 방법](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center 통합 위협 인텔리전스에 대 한 자세한 내용은](../security-center/azure-defender.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: Azure virtual machines를 사용 하 여 event hubs에 액세스 하는 경우 네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하 고 트래픽 감사를 위해 저장소 계정에 로그를 보냅니다. 또한 네트워크 보안 그룹 흐름 로그를 Log Analytics 작업 영역으로 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 정보를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

비정상적인 활동을 조사 하는 데 필요한 경우 Network Watcher 패킷 캡처를 사용 하도록 설정 합니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

- [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: Azure virtual machines를 사용 하 여 event hubs에 액세스 하는 경우 페이로드 검사 기능을 사용 하 여 IDS/IPS 기능을 지 원하는 Azure Marketplace에서 제공 하는 제품을 선택 합니다. 조직에서 페이로드 검사를 기반으로 하는 침입 탐지 및/또는 방지가 필요 하지 않은 경우 Azure Event Hubs ' 기본 제공 방화벽 기능을 사용할 수 있습니다. 제한 된 범위의 IP 주소 또는 방화벽 규칙을 사용 하 여 특정 IP 주소에 대 한 Event Hubs 네임 스페이스에 대 한 액세스를 제한할 수 있습니다.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [지정 된 IP 주소에 대 한 Event Hubs에서 방화벽 규칙을 추가 하는 방법](event-hubs-ip-filtering.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy를 사용 하 여 Azure Event Hubs 네임 스페이스와 연결 된 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. **Microsoft EventHub** 및 **microsoft 네트워크** 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Event Hubs 네임 스페이스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책 정의를 만듭니다. 다음과 같이 Azure Event Hubs와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다.

- 이벤트 허브는 가상 네트워크 서비스 끝점을 사용 해야 합니다.

참조 된 링크에서 추가 정보를 확인할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Event Hubs 네임 스페이스에 대 한 Azure 기본 제공 정책](../governance/policy/samples/built-in-policies.md#event-hub)

- [네트워킹에 대 한 Azure Policy 샘플](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: event hubs와 연결 된 네트워크 보안 및 트래픽 흐름과 관련 된 가상 네트워크 및 기타 리소스에 대 한 태그를 사용 합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 azure Event Hubs 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor 내에서 활동 로그 및 이벤트 허브 진단 설정 내에서 event hubs와 관련 된 로그를 구성 하 여 장기 보관 저장소에 대 한 저장소 계정으로 쿼리하거나 쿼리 될 Log Analytics 작업 영역으로 로그를 보냅니다.

- [Azure Event Hubs에 대 한 진단 설정을 구성 하는 방법](event-hubs-diagnostic-logs.md)

- [Azure 활동 로그 이해](../azure-monitor/essentials/platform-logs-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Event Hubs 네임 스페이스에 대 한 진단 설정을 사용 하도록 설정 합니다. Azure Event Hubs에 대 한 진단 설정에는 보관 로그, 작업 로그, 자동 크기 조정 로그의 세 가지 범주가 있습니다. 작업 로그를 사용 하 여 Event Hubs 작업 중 발생 하는 작업, 특히 이벤트 허브 만들기, 사용 된 리소스, 작업 상태 등의 작업 유형에 대 한 정보를 캡처할 수 있습니다.

또한 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 Azure Storage 계정, 이벤트 허브 또는 Log Analytics 작업 영역으로 보낼 수 있습니다. 활동 로그는 Azure Event Hubs 및 기타 리소스에서 수행 된 작업에 대 한 통찰력을 제공 합니다. 활동 로그를 사용 하 여 Azure Event Hubs 네임 스페이스에 대해 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 "무엇을, 누가, 언제"를 결정할 수 있습니다.

- [Azure Event Hubs에 대 한 진단 설정을 사용 하도록 설정 하는 방법](event-hubs-diagnostic-logs.md)

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure 보안 벤치 마크](/azure/governance/policy/samples/azure-security-benchmark) 는 Security Center에 대 한 기본 정책 이니셔티브 이며 [Security Center 권장 사항의](/azure/security-center/security-center-recommendations)기초가 됩니다. 이 컨트롤과 관련 된 Azure Policy 정의는 Security Center에 의해 자동으로 설정 됩니다. 이 컨트롤과 관련 된 경고에는 관련 서비스에 대 한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의-Microsoft EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 하 여 이벤트 허브 관련 인시던트를 캡처하고 검토 합니다.

- [Log Analytics 작업 영역에 대 한 로그 보존 매개 변수를 설정 하는 방법](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 event hubs와 관련 된 결과를 정기적으로 검토 합니다. Azure Monitor의 Log Analytics를 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 Azure 센티널 또는 타사 시스템 정보 및 이벤트 관리 솔루션에 대 한 온보드 데이터를 사용할 수 있습니다.

- [Log Analytics 작업 영역에 대 한 자세한 내용](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/logs/get-started-queries.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure Monitor 내에서 활동 로그 내에서 Azure Event Hubs 관련 된 로그를 구성 하 고, 장기 보관 저장소에 대 한 저장소 계정으로 쿼리 또는 저장소 계정에 로그를 전송 하 Log Analytics는 진단 설정을 Event Hubs 합니다. Log Analytics 작업 영역을 사용 하 여 보안 로그 및 이벤트에 있는 비정상적인 활동에 대 한 경고를 만들 수 있습니다.

또는 온보드 데이터를 Azure 센티널로 설정할 수 있습니다. 

- [Azure 활동 로그 이해](../azure-monitor/essentials/platform-logs-overview.md)

- [Azure Event Hubs에 대 한 진단 설정을 구성 하는 방법](event-hubs-diagnostic-logs.md)

- [Log Analytics 작업 영역 로그 데이터를 경고 하는 방법](../azure-monitor/alerts/tutorial-response.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure 보안 벤치 마크: id 및 Access Control](../security/benchmarks/security-control-identity-access-control.md)을 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure Active Directory (Azure AD)에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Event Hubs에 대 한 제어 평면 액세스는 Azure Active Directory (Azure AD)를 통해 제어 됩니다. Azure AD에는 기본 암호 개념이 없습니다.

Event Hubs에 대 한 데이터 평면 액세스는 관리 되는 Id 또는 앱 등록 및 공유 액세스 서명을 사용 하 여 Azure AD를 통해 제어 됩니다. 공유 액세스 서명은 event hubs에 연결 하는 클라이언트에서 사용 되며 언제 든 지 다시 생성할 수 있습니다.

- [Event Hubs에 대 한 공유 액세스 서명 이해](authenticate-shared-access-signature.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.

- 구독에 둘 이상의 소유자를 할당해야 합니다.

- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.

- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

참조 된 링크에서 추가 정보를 확인할 수 있습니다.

- [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법(미리 보기)](../security-center/security-center-identity-access.md)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: SSO (Azure Active Directory Single Sign-On)를 사용 하십시오.

**지침**: Microsoft Azure에서는 Azure Active Directory (Azure AD) 기반 리소스 및 응용 프로그램에 대 한 통합 액세스 제어 관리 기능을 제공 합니다. Azure Event Hubs에서 Azure AD를 사용 하는 경우의 주요 이점은 더 이상 코드에 자격 증명을 저장할 필요가 없다는 것입니다. 대신 Microsoft Id 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. 토큰을 요청 하는 리소스 이름은 https: \/ /eventhubs.azure.net/입니다. Azure AD는 응용 프로그램을 실행 하는 보안 주체 (사용자, 그룹 또는 서비스 사용자)를 인증 합니다. 인증에 성공 하면 Azure AD는 응용 프로그램에 액세스 토큰을 반환 하 고 응용 프로그램은 액세스 토큰을 사용 하 여 Azure Event Hubs 리소스에 대 한 요청에 권한을 부여할 수 있습니다.

- [Event Hubs 리소스에 액세스 하기 위해 Azure AD로 응용 프로그램을 인증 하는 방법](authenticate-application.md)

- [Azure AD를 사용 하 여 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: Azure Active Directory (Azure AD) 다단계 인증을 사용 하도록 설정 하 고 Azure Security Center id 및 액세스 관리 권장 사항에 따라 Event Hub 사용 리소스를 보호 합니다.

- [Azure에서 다단계 인증을 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 다단계 인증을 사용 하 여 PAW (권한 있는 액세스 워크스테이션)를 사용 하 여 이벤트 허브 사용 리소스에 로그인 하 고 구성 합니다.

- [Privileged Access Workstation에 대한 자세한 정보](/security/compass/privileged-access-devices)

- [Azure에서 다단계 인증을 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 Azure Active Directory (Azure AD) Privileged Identity Management를 사용 합니다. Azure AD 위험 감지를 사용 하 여 위험한 사용자 동작에 대 한 경고 및 보고서를 봅니다. 추가 로깅을 수행 하려면 Azure Security Center 위험 검색 경고를 Azure Monitor에 보내고 작업 그룹을 사용 하 여 사용자 지정 경고/알림을 구성 합니다.

- [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

- [사용자 지정 경고 및 알림에 대 한 작업 그룹을 구성 하는 방법](../azure-monitor/alerts/action-groups.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Event Hubs와 같은 azure 리소스에 대 한 중앙 인증 및 권한 부여 시스템으로 Azure Active Directory (azure AD)를 사용 합니다. Azure RBAC (역할 기반 액세스 제어)를 통해 중요 한 리소스를 관리할 수 있습니다.

- [ Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure AD를 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](authorize-access-azure-active-directory.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory (Azure AD)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

또한 Event Hubs의 공유 액세스 서명을 정기적으로 회전 합니다.

- [Azure AD 보고 이해](../active-directory/reports-monitoring/index.yml)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

- [Event Hubs에 대 한 공유 액세스 서명 이해](authenticate-shared-access-signature.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: 모든 타사 시스템 정보 및 이벤트 관리 솔루션 또는 모니터링 도구와 통합할 수 있도록 하는 Azure Active Directory (Azure AD) 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 내에서 원하는 로그 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure AD를 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](authorize-access-azure-active-directory.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure Active Directory (Azure AD) 내에서 id 보호 및 위험 검색 기능을 사용 하 여 Event Hubs 사용 가능한 리소스와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 조직의 보안 응답을 구현 하기 위해 Azure 센티널을 통해 자동화 된 응답을 사용 하도록 설정 해야 합니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 현재 사용할 수 없음 고객 Lockbox는 Event Hubs에 대해 아직 지원 되지 않습니다.

- [Customer Lockbox 지원 서비스 목록](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 중요 한 정보를 저장 하거나 처리 하는 Azure 리소스를 추적 하는 데 도움이 되도록 Event Hubs와 관련 된 리소스에 태그를 사용 합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 필요에 따라 개발, 테스트 및 프로덕션을 위한 관리 그룹을 사용 하 여 별도의 구독을 구현 합니다. Event Hubs 네임 스페이스는 서비스 끝점을 사용 하도록 설정 하 고 적절 하 게 태그를 지정 하 여 가상 네트워크로 구분 해야

방화벽을 사용 하 여 Azure Event Hubs 네임 스페이스를 보호할 수도 있습니다. Azure Event Hubs는 인바운드 방화벽 지원에 대 한 IP 기반 액세스 제어를 지원 합니다. Azure Portal, Azure Resource Manager 템플릿 또는 Azure CLI 또는 Azure PowerShell를 사용 하 여 방화벽 규칙을 설정할 수 있습니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [Azure Event Hubs 네임 스페이스에 대 한 IP 방화벽 규칙 구성](event-hubs-ip-filtering.md)

- [태그를 만들고 활용 하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: virtual machines를 사용 하 여 event hubs에 액세스할 때 가상 네트워크, 서비스 끝점, Event Hubs 방화벽, 네트워크 보안 그룹 및 서비스 태그를 활용 하 여 데이터 exfiltration의 가능성을 완화 합니다.

Microsoft는 Azure Event Hubs에 대 한 기본 인프라를 관리 하 고 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

- [Azure Event Hubs 네임 스페이스에 대 한 IP 방화벽 규칙 구성](event-hubs-ip-filtering.md)

- [Azure Event Hubs를 사용 하 여 Virtual Network 서비스 끝점 이해](event-hubs-service-endpoints.md)

- [Azure Private Link와 Azure Event Hubs 통합](private-link-service.md)

- [네트워크 보안 그룹 및 서비스 태그 이해](../virtual-network/network-security-groups-overview.md)

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure Event Hubs에 대 한 데이터 식별, 분류 및 손실 방지 기능을 아직 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용 하 여 리소스에 대 한 액세스를 제어 합니다.

**지침**: azure Event Hubs에서는 Azure Active Directory (azure AD)를 사용 하 여 Event Hubs 리소스에 대 한 요청에 권한을 부여 합니다. Azure AD를 사용 하면 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자 또는 응용 프로그램 서비스 사용자 일 수 있는 보안 주체에 권한을 부여할 수 있습니다.

- [Azure Event Hubs에 대 한 Azure RBAC 및 사용 가능한 역할 이해](authorize-access-azure-active-directory.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Event Hubs는 Microsoft에서 관리 하는 키 또는 고객이 관리 하는 키를 사용 하 여 미사용 데이터를 암호화 하는 옵션을 지원 합니다. 이 기능을 사용 하면 미사용 Azure Event Hubs 데이터를 암호화 하는 데 사용 되는 고객 관리 키에 대 한 액세스를 만들고, 회전 하 고, 사용 하지 않도록 설정할 수 있습니다.

- [Azure Event Hubs을 암호화 하기 위해 고객이 관리 하는 키를 구성 하는 방법](configure-customer-managed-key.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: azure 활동 로그에서 Azure Monitor를 사용 하 여 azure Event Hubs의 프로덕션 인스턴스 및 기타 중요 한 리소스 또는 관련 된 리소스에 대 한 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내의 모든 리소스 (azure Event Hubs 네임 스페이스 포함)를 쿼리하고 검색 합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 적절 한 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 Azure Event Hubs 네임 스페이스 및 관련 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

참조 된 링크에서 추가 정보를 확인할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/built-in-policies.md#general)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Event Hubs 배포에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. **Microsoft EventHub** 네임 스페이스의 Azure Policy 별칭을 사용 하 여 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 다음과 같이 Azure Event Hubs에 대 한 기본 제공 정책 정의를 사용할 수도 있습니다.

- Event Hub의 진단 로그를 사용하도록 설정해야 합니다.

- Event Hub는 가상 네트워크 서비스 엔드포인트를 사용해야 함

참조 된 링크에서 추가 정보를 확인할 수 있습니다.

- [Event Hubs 네임 스페이스에 대 한 Azure 기본 제공 정책](../governance/policy/samples/built-in-policies.md#event-hub)

- [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy [거부] 및 [없는 경우 배포] 효과를 사용 하 여 Event Hubs 사용 가능한 리소스에서 보안 설정을 적용 합니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

 
- [Azure Policy 효과에 대 한 자세한 내용은](../governance/policy/concepts/effects.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: "Microsoft EventHub" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: "Microsoft EventHub" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. Azure Event Hubs 배포 및 관련 리소스에 대 한 구성을 자동으로 적용 하려면 Azure Policy [감사], [거부] 및 [없는 경우 배포] 효과를 사용 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: event hubs에 액세스 하는 데 사용 되는 Azure App Service에서 실행 되는 azure 가상 머신 또는 웹 응용 프로그램의 경우 Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 azure Event Hubs 배포에 대 한 공유 액세스 서명 관리를 간소화 하 고 보호 합니다. 일시 삭제를 사용 하도록 설정 된 Key Vault 구성 되어 있는지 확인 합니다.

- [Event Hubs 리소스에 액세스할 수 있도록 Azure Active Directory (Azure AD)를 사용 하 여 관리 id 인증](./authenticate-managed-identity.md?tabs=latest)

- [Event Hubs에 대 한 고객 관리 키 구성](configure-customer-managed-key.md)

- [Azure 관리 Id와 통합 하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault를 만드는 방법](../key-vault/secrets/quick-create-portal.md)

- [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](../key-vault/general/assign-access-policy-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: event hubs에 액세스 하는 데 사용 되는 Azure App Service에서 실행 되는 azure 가상 머신 또는 웹 응용 프로그램의 경우 Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 azure Event Hubs를 간소화 하 고 보호 합니다. 일시 삭제를 사용 하도록 설정 된 Key Vault 구성 되어 있는지 확인 합니다.

관리 되는 id를 사용 하 여 azure AD (Azure Active Directory)에서 자동으로 관리 되는 id를 Azure 서비스에 제공 합니다. 관리 Id를 사용 하면 코드에 자격 증명 없이 Azure Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다.

- [Event Hubs 리소스에 액세스 하기 위해 Azure AD를 사용 하 여 관리 id 인증](./authenticate-managed-identity.md?tabs=latest)

- [Event Hubs에 대 한 고객 관리 키 구성](configure-customer-managed-key.md)

- [관리 Id를 구성 하는 방법](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Azure 관리 Id와 통합 하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조 하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: 비 계산 azure 리소스 (예: azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL 등)로 업로드 되는 콘텐츠를 미리 검색 합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

Microsoft 맬웨어 방지는 Azure 서비스 (예: Azure Cache for Redis)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: Azure Event Hubs에 대 한 지역 재해 복구를 구성 합니다. (가용성 영역을 사용하지 않는 경우)전체 Azure 지역 또는 데이터 센터에서 가동 중지 시간이 발생하면 데이터 처리가 다른 지역 또는 데이터 센터에서 계속 작동되는 것이 중요합니다. 따라서 지리적 재해 복구 및 지리적 복제는 기업에 중요한 기능입니다. Azure Event Hubs는 네임스페이스 수준에서 지리적 재해 복구 및 지리적 복제를 둘 다 지원합니다. 

- [Azure Event Hubs에 대 한 지역 재해 복구 이해](./event-hubs-geo-dr.md#availability-zones)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: azure Event Hubs는 azure SSE (Azure Storage 서비스 암호화)를 사용 하 여 미사용 데이터의 암호화를 제공 합니다. Event Hubs는 Azure Storage를 사용 하 여 데이터를 저장 하 고, 기본적으로 Azure Storage와 함께 저장 되는 모든 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. Azure Key Vault를 사용 하 여 고객이 관리 하는 키를 저장 하는 경우 키의 자동 백업을 정기적으로 수행 해야 합니다.

다음 PowerShell 명령을 사용 하 여 Key Vault 암호의 자동 백업을 정기적으로 수행 하세요. Backup-AzKeyVaultSecret

- [휴지 상태의 Azure Event Hubs 데이터를 암호화 하기 위해 고객이 관리 하는 키를 구성 하는 방법](configure-customer-managed-key.md)

- [Key Vault 비밀을 백업 하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 백업 된 고객이 관리 하는 키의 복원 테스트

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Key Vault에서 일시 삭제를 사용 하도록 설정 하 여 실수로 또는 악의적인 삭제 로부터 키를 보호 합니다. Azure Event Hubs에는 고객이 관리 하는 키에 일시 삭제 및 구성 된 제거 안 함이 필요 합니다.

Event Hubs 데이터를 캡처하는 데 사용 되는 Azure storage 계정에 대해 일시 삭제를 구성 합니다. 이 기능은 Azure Data Lake Storage Gen 2에서 아직 지원 되지 않습니다.

- [Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [키를 사용 하 여 키 자격 증명 모음 설정](configure-customer-managed-key.md)

- [Azure Storage Blob에 대한 일시 삭제](//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 직원의 역할 및 인시던트 처리/관리 단계를 정의 하는 사고 대응 계획을 작성 해야 합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center는 경고에 대 한 심각도를 할당 하 여 각 경고에 참석 하는 순서의 우선 순위를 지정할 수 있도록 합니다. 따라서 리소스가 손상 되 면 즉시 가져올 수 있습니다. 심각도는 경고를 실행 하는 데 사용 되는 찾기 또는 분석에 사용 되는 것과 경고를 발생 시킨 활동의 악의적인 의도를 받은 신뢰 수준에 Security Center 따라 달라 집니다.

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

**지침**: Engagement의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
