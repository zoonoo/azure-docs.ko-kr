---
title: Azure Cloud Services에 대 한 azure 보안 기준
description: Azure Cloud Services 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2c088bce3bc763bf813298ef91b4e07f0b20c50c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328711"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Azure Cloud Services에 대 한 azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 1.0](../security/benchmarks/overview-v1.md) 의 지침을 Microsoft Azure Cloud Services 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Cloud Services에 적용 되는 관련 지침에 따라 그룹화 됩니다. Cloud Services에 적용할 수 없는 **컨트롤** 은 제외 되었습니다.

 
Cloud Services 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Cloud Services 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조 하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침** : 신뢰할 수 있는 포트 및 IP 범위에 따라 격리를 적용 하기 위해 별도의 공용 및 개인 서브넷을 사용 하 여 클래식 Azure Virtual Network를 만듭니다. 이러한 가상 네트워크 및 서브넷은 현재 Azure Resource Manager 리소스가 아니라 클래식 Virtual Network (클래식 배포) 기반 리소스 여야 합니다.  

트래픽 방향, 프로토콜, 원본 주소 및 포트, 대상 주소 및 포트를 기반으로 하는 액세스 제어 규칙이 포함 된 네트워크 보안 그룹을 사용 하 여 트래픽을 허용 하거나 거부 합니다. 네트워크 보안 그룹의 규칙은 언제 든 지 변경할 수 있으며, 변경 내용은 연결 된 모든 인스턴스에 적용 됩니다.

Microsoft Azure Cloud Services (클래식)은 (는) Azure Resource Manager 가상 네트워크에 배치할 수 없습니다. 그러나 리소스 관리자 기반 가상 네트워크와 클래식 배포 기반 가상 네트워크는 피어 링을 통해 연결할 수 있습니다. 

- [네트워크 보안 그룹 개요](../virtual-network/network-security-groups-overview.md)

- [Virtual Network 피어 링](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq?&amp;preserve-view=true#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침** : Azure Cloud Services 구성을 문서화 하 고 변경 내용을 모니터링 합니다. 서비스의 구성 파일을 사용 하 여 서비스의 각 역할에 대해 배포할 역할 인스턴스 수, 구성 설정의 값 및 역할과 연결 된 인증서의 지문을 지정 합니다. 

서비스가 가상 네트워크의 일부인 경우에는 가상 네트워킹 구성 파일 뿐 아니라 서비스 구성 파일에 네트워크에 대 한 구성 정보를 제공 해야 합니다. 서비스 구성 파일의 기본 확장명은 .cscfg입니다. 클래식 배포에서는 구성 적용에 대 한 Azure Policy 지원 되지 않습니다.

서비스 구성 파일 (.cscfg) 및 서비스 정의 파일 (.csdef)의 정의에 클라우드 서비스의 구성 값을 설정 합니다. 서비스 정의 파일을 사용 하 여 응용 프로그램에 대 한 서비스 모델을 정의 합니다. 클라우드 서비스에서 사용할 수 있는 역할을 정의 하 고 서비스 끝점도 지정 합니다. 서비스 구성 파일을 사용 하 여 Azure Cloud Services에 대 한 구성을 로깅합니다. 모든 재구성은 ServiceConfig 파일을 통해 수행할 수 있습니다. 

지정 된 내부 끝점과 통신할 수 있는 역할을 제한 하는 선택적 NetworkTrafficRules element 서비스 정의를 모니터링 합니다. 서비스 정의 파일의 선택적 요소인 NetworkTrafficRules 노드를 구성 하 여 역할이 서로 통신 하는 방법을 지정 합니다. 특정 역할의 내부 끝점에 액세스할 수 있는 역할을 제한 합니다.  서비스 정의는 변경할 수 없습니다. 

네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하 고 감사를 위해 로그를 Azure Storage 계정으로 보냅니다. 흐름 로그를 Log Analytics 작업 영역으로 보내고 트래픽 분석를 사용 하 여 Azure 테 넌 트의 트래픽 패턴에 대 한 정보를 제공 합니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화 하 고, 핫 스폿 및 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 구성을 잘못 파악할 수 있다는 것입니다.

- [Azure Resource Manager 및 클래식 배포-배포 모델 및 리소스 상태 이해](../azure-resource-manager/management/deployment-models.md)

- [Cloud Services 구성 파일](schema-cscfg-file.md)

- [Azure Policy에서 지 원하는 서비스 목록](https://docs.microsoft.com/cli/azure/azure-services-the-azure-cli-can-manage?&amp;preserve-view=true)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침** : MICROSOFT는 TLS (Transport Layer Security) 프로토콜 v 1.2를 사용 하 여 Azure Cloud Services와 고객 간에 이동할 때 데이터를 보호 합니다. Microsoft 데이터 센터는 Azure 서비스에 연결되는 클라이언트 시스템과 TLS 연결을 협상합니다. TLS는 강력한 인증, 메시지 개인 정보 및 무결성(메시지 변조, 가로채기 및 위조의 검색 가능), 상호 운용성, 알고리즘 유연성, 배포 및 사용 편의성을 제공합니다.

- [암호화 기본 사항](../security/fundamentals/encryption-overview.md)

- [TLS/SSL 인증서 구성](cloud-services-configure-ssl-certificate-portal.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침** : Azure 클라우드는 DDoS (분산 된 서비스 거부) 공격에 대해 플랫폼 서비스를 보호 하기 위해 다중 계층 네트워크 보안을 구현 합니다. Azure DDoS Protection는 Azure 클라우드의 연속 모니터링 프로세스의 일부 이며 침투 테스트를 통해 지속적으로 향상 됩니다. 이 DDoS Protection는 외부의 공격 뿐만 아니라 다른 Azure 테 넌 트의 공격에도 견딜 수 있도록 설계 되었습니다. 

Azure Cloud Services 내에서 플랫폼 수준 보호 외에도 통신을 차단 하거나 거부 하는 몇 가지 방법이 있습니다. 이러한 항목은 다음과 같습니다. 

-  특정 IP 주소를 선택적으로 차단 하는 시작 작업 만들기
-  IIS web.config 파일을 수정 하 여 Azure 웹 역할에 지정 된 IP 주소 집합에 대 한 액세스 제한

들어오는 트래픽을 Cloud Services의 기본 URL 또는 이름 (예:. cloudapp.net)으로 방지 합니다. *호스트 헤더를 Cloud Services 정의 파일 (.csdef)의 사이트 바인딩 구성에서 사용자 지정 DNS 이름으로 설정* 합니다.

클래식 구독 관리자 할당에 대 한 거부 적용 규칙을 구성 합니다. 기본적으로 내부 엔드포인트가 정의된 후에 어떤 역할에서도 제한 없이 역할의 내부 엔드포인트로 통신할 수 있습니다. 통신을 제한하려면 서비스 정의 파일의 ServiceDefinition 요소에 NetworkTrafficRules 요소를 추가해야 합니다.

- [내 클라우드 서비스의 기본 URL에 들어오는 트래픽을 차단 하거나 사용 하지 않도록 설정 하는 방법](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq?&amp;preserve-view=true#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Azure DDOS 보호](https://docs.microsoft.com/azure/cloud-services/cloud-services-connectivity-and-networking-faq?&amp;preserve-view=true#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [특정 IP 주소 차단](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침** : azure 네트워크를 모니터링할 수 있는 azure Network Watcher, 네트워크 성능 모니터링, 진단 및 분석 서비스를 사용 합니다. Network Watcher 에이전트 가상 컴퓨터 확장은 주문형 네트워크 트래픽 및 Azure Virtual Machines의 기타 고급 기능을 캡처하기 위한 요구 사항입니다. Network Watcher 에이전트 가상 컴퓨터 확장을 설치 하 고 네트워크 보안 그룹 흐름 로그를 켭니다.

네트워크 보안 그룹에 대 한 흐름 로깅을 구성 합니다. 클래식 배포 모델을 통해 배포 된 기존 가상 컴퓨터에 Network Watcher 가상 컴퓨터 확장을 배포 하는 방법에 대 한 세부 정보를 검토 합니다.

- [네트워크 보안 그룹에 대 한 흐름 로깅 구성](../virtual-machines/extensions/network-watcher-linux.md)

- [흐름 로그를 구성 하는 방법에 대 한 자세한 내용은을 참조 하세요.](https://docs.microsoft.com/cli/azure/azure-services-the-azure-cli-can-manage?&amp;preserve-view=true)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침** : Azure Cloud Services에는 기본 제공 ID 또는 ip 기능이 없습니다. 고객은 조직의 요구 사항에 따라 Azure Marketplace에서 보충 네트워크 기반 ID 또는 IP 솔루션을 선택 하 고 배포할 수 있습니다. 타사 솔루션을 사용 하는 경우 적절 한 작업 및 기능을 보장 하기 위해 Azure Cloud Services를 사용 하 여 선택한 ID 또는 IP 솔루션을 철저 하 게 테스트 해야 합니다.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침** : Azure Cloud Services에 연결 된 서비스 인증서는 서비스와의 보안 통신을 가능 하 게 합니다. 이러한 인증서는 서비스 정의에 정의 되며 웹 역할의 인스턴스를 실행 하는 가상 컴퓨터에 자동으로 배포 됩니다. 예를 들어, 웹 역할의 경우 노출 된 HTTPS 끝점을 인증할 수 있는 서비스 인증서를 사용할 수 있습니다. 

인증서를 업데이트 하려면 새 인증서를 업로드 하 고 서비스 구성 파일에서 지문 값을 변경 해야 합니다.

가장 일반적으로 사용 되는 데이터 보안 방법 인 TLS 1.2 프로토콜을 사용 하 여 기밀성과 무결성 보호를 제공 합니다. 

일반적으로 웹 응용 프로그램을 보호 하 고 OWASP 상위 10과 같은 공격 으로부터 보호 하기 위해 웹 응용 프로그램을 보호 하기 위한 Azure 웹 응용 프로그램 방화벽 사용 Azure 애플리케이션 게이트웨이를 배포할 수 있습니다. 

- [서비스 인증서](cloud-services-certs-create.md)

- [Azure에서 응용 프로그램에 대 한 TLS 구성](cloud-services-configure-ssl-certificate-portal.md)

- [Application Gateway 배포 하는 방법](../application-gateway/quick-create-portal.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침** : Azure Cloud Services 구성을 강화 하 고 변경 내용을 모니터링 합니다. 서비스 구성 파일은 서비스의 각 역할에 대해 배포할 역할 인스턴스의 수, 구성 설정의 값 및 역할에 연결된 인증서의 지문을 지정합니다. 

서비스가 가상 네트워크의 일부인 경우에는 가상 네트워킹 구성 파일 뿐 아니라 서비스 구성 파일에 네트워크에 대 한 구성 정보를 제공 해야 합니다. 서비스 구성 파일의 기본 확장명은 .cscfg입니다.

구성 적용을 위해 Azure Cloud Services에서 Azure Policy 지원 되지 않습니다.

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침** : azure 네트워크 보안 그룹을 사용 하 여 azure Virtual Network에서 azure 리소스에 대 한 네트워크 트래픽을 필터링 할 수 있습니다. 네트워크 보안 그룹에는 여러 유형의 Azure 리소스에 대 한 인바운드 네트워크 트래픽, 또는 아웃 바운드 네트워크 트래픽을 허용 하거나 거부 하는 보안 규칙이 포함 되어 있습니다. 규칙마다 원본 및 대상, 포트, 프로토콜을 지정할 수 있습니다.

Azure Cloud Services 내에서 개별 네트워크 보안 그룹 규칙에 대 한 "설명" 필드를 사용 하 여 트래픽을 허용 하거나 네트워크에서 트래픽을 허용 하는 규칙을 문서화 합니다.

- [네트워크 보안 그룹 규칙을 사용 하 여 네트워크 트래픽을 필터링 하는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침** : Azure Traffic Manager의 기본 제공 끝점 모니터링 및 자동 끝점 장애 조치 (failover) 기능을 사용 합니다. 이를 통해 끝점 및 Azure 지역 오류에 탄력적으로 사용할 수 있는 고가용성 응용 프로그램을 제공할 수 있습니다. 끝점 모니터링을 구성 하려면 Traffic Manager 프로필에 특정 설정을 지정 해야 합니다.

활동 로그, Azure의 플랫폼 로그에서 구독 수준 이벤트에 대 한 통찰력을 수집 합니다. 리소스를 수정 하거나 가상 컴퓨터를 시작할 때와 같은 정보를 포함 합니다. Azure Portal에서 활동 로그를 보거나 PowerShell 및 CLI를 사용 하 여 항목을 검색 합니다. 

활동 로그를 Azure 외부에서 전달 하거나 보관을 위해 Azure Storage Event Hubs Azure Monitor으로 전송 하는 진단 설정을 만듭니다. Azure Cloud Services의 중요 한 리소스가 변경 될 때 알림 경고에 대 한 Azure Monitor를 구성 합니다. 

- [Azure 활동 로그](../azure-monitor/platform/activity-log.md)

- [Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리하기](../azure-monitor/platform/alerts-activity-log.md)

- [Traffic Manager 모니터링](../traffic-manager/traffic-manager-monitoring.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침** : azure Cloud Services에 대 한 azure 리소스의 시간 소스를 유지 관리 합니다. UDP 프로토콜을 사용 하는 포트 123을 통해 환경에서 사용 되는 시간 서버에 대 한 액세스를 허용 하는 네트워크 규칙을 만들어야 할 수 있습니다.

- [NTP 서버 액세스](../firewall/protect-windows-virtual-desktop.md#additional-considerations)

**Azure Security Center 모니터링** : 예

**책임** : 공유됨

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침** : Azure Event Hubs를 사용 하 여 프로그래밍 방식으로 클라우드 서비스 스트리밍 데이터를 사용 합니다. 이 모든 데이터를 통합 하 여 Azure 센티널에 전송 하 여 로그를 모니터링 하 고 검토 하거나 타사 SIEM을 사용 합니다. 중앙 보안 로그 관리를 위해 선택한 Azure Security Center 데이터의 연속 내보내기를 Azure Event Hubs 구성 하 고 SIEM에 적절 한 커넥터를 설정 합니다. 타사 도구를 비롯 한 Azure 센티널에 대 한 몇 가지 옵션은 다음과 같습니다.

- Azure 센티널-기본 Security Center 경고 데이터 커넥터 사용
- Splunk-Splunk에 대 한 Azure Monitor 추가 기능 사용
- IBM QRadar-수동으로 구성 된 로그 원본 사용
- ArcSight – SmartConnector 사용

Azure 센티널에서 사용 가능한 커넥터에 대 한 자세한 내용은 Azure 센티널 설명서를 검토 하세요. 

- [데이터 원본 연결](../sentinel/connect-data-sources.md)

- [SIEM과 통합](../security-center/continuous-export.md)

- [진단 데이터 저장](diagnostics-extension-to-storage.md)

- [Azure Event Hubs를 통한 SIEM 통합 구성](../security-center/continuous-export.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침** : azure Cloud Services를 실행 하는 가상 컴퓨터 인스턴스를 비롯 하 여 가상 컴퓨터의 시스템 및 로깅 데이터를 캡처하는 azure Cloud Services 문제를 해결 하기 위한 Azure 진단를 설정 하도록 Visual Studio를 구성 합니다. 진단 데이터는 사용자가 선택한 저장소 계정으로 전송 됩니다. 배포 전에 Azure Cloud Services 프로젝트에서 진단을 켭니다.

 
Azure Monitor 내의 활동 로그에서 일부 이벤트에 대 한 변경 기록을 봅니다. 이벤트 기간 중에 발생 한 변경 내용을 감사 합니다. 변경 기록 (미리 보기) 탭을 사용 하 여 자세한 검사를 위해 활동 로그에서 이벤트를 선택 합니다. Visual Studio에서 Azure Cloud Services를 게시할 때 Application Insights에 진단 데이터를 보냅니다. 해당 시간에 Application Insights Azure 리소스를 만들거나 기존 Azure 리소스에 데이터를 보냅니다. 

Azure Cloud Services는 가용성, 성능, 실패 및 사용에 대 한 Application Insights에서 모니터링할 수 있습니다. 사용자 지정 차트를 Application Insights에 추가하여 가장 중요한 데이터를 볼 수 있습니다. Azure Cloud Services 프로젝트에서 Application Insights SDK를 사용 하 여 역할 인스턴스 데이터를 수집할 수 있습니다. 

- [배포 하기 전에 Visual Studio에서 진단 설정](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?&amp;preserve-view=true#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [변경 기록 보기](../azure-monitor/platform/activity-log.md#view-change-history)

- [Azure 클라우드 서비스에 대 한 Application Insights (클래식)](../azure-monitor/app/cloudservices.md)

- [Azure Cloud service (클래식) 및 virtual machines에 대 한 진단 설정](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?toc=%2Fazure%2Fcloud-services%2Ftoc.json&amp;preserve-view=true)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침** : 추가 메트릭을 허용 하는 Azure Cloud Services 고급 모니터링을 5 분, 1 시간 및 12 시간 간격으로 샘플링 하 고 수집할 수 있습니다. 집계 된 데이터는 저장소 계정에 저장 되 고 테이블에 저장 되며 10 일 후에 제거 됩니다. 그러나 사용 된 저장소 계정은 역할별로 구성 되며, 역할 마다 다른 저장소 계정을 사용할 수 있습니다. 이러한 내용은 .csdef 및 .cscfg 파일에 연결 문자열로 구성됩니다.

고급 모니터링에서는 모니터링 하려는 역할에 Azure 진단 확장 (Application Insights SDK는 선택 사항)을 사용 해야 합니다. 진단 확장은 diagnostics.wadcfgx라는 구성 파일(역할당)을 사용하여 모니터링되는 진단 메트릭을 구성합니다. Azure 진단 확장은 데이터를 수집하고 Azure Storage 계정에 저장합니다. 이러한 설정은 .wadcfgx, .csdef 및 .cscfg 파일에서 구성됩니다.

- [클라우드 서비스 모니터링 소개](cloud-services-how-to-monitor.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침** : Azure Cloud Services에 기본 또는 고급 모니터링 모드를 사용할 수 있습니다. Azure Cloud Services는 호스트 가상 머신에서 기본 모니터링 데이터 (CPU 비율, 네트워크 인/출력 및 디스크 읽기/쓰기)를 자동으로 수집 합니다. Azure Portal에 있는 클라우드 서비스의 개요 및 메트릭 페이지에서 수집한 모니터링 데이터를 확인 합니다. 

Azure 진단 확장을 사용 하 여 응용 프로그램 로그, 성능 카운터 등과 같은 진단 데이터를 수집 하도록 Azure Cloud Services에서 진단을 사용 하도록 설정 합니다. Set-AzureServiceDiagnosticsExtension cmdlet을 사용 하 여 이미 실행 중인 클라우드 서비스에서 진단 구성을 사용 하도록 설정 하거나 업데이트 하거나 진단 확장을 사용 하 여 클라우드 서비스를 자동으로 배포 합니다. 필요에 따라 Application Insights SDK를 설치 합니다. Azure Monitor로 성능 카운터를 보냅니다.

Azure 진단 확장은 데이터를 수집하고 Azure Storage 계정에 저장합니다. 진단 데이터를 Microsoft Azure Storage 에뮬레이터에 전송 하거나 영구적으로 저장 되지 않으므로 Azure Storage 합니다. 저장소에서 사용 가능한 여러 도구 중 하나 (예: Visual Studio의 서버 탐색기, Microsoft Azure Storage 탐색기, Azure Management Studio에서 볼 수 있습니다. 진단 확장에서 diagnostics.wadcfgx 라는 구성 파일을 사용 하 여 모니터링할 진단 메트릭을 구성 합니다. 

- [클라우드 서비스 모니터링 소개](cloud-services-how-to-monitor.md)

- [작업자 역할에서 진단을 사용 하도록 설정 하는 방법-SIEM과 통합](../security-center/continuous-export.md)

- [PowerShell을 사용하여 Azure Cloud Services에 진단 사용](cloud-services-diagnostics-powershell.md)

- [Azure Storage에서 진단 데이터 저장 및 보기](diagnostics-extension-to-storage.md?&amp;preserve-view=true)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침** : 비정상적인 활동에 대 한 경고를 사용 하도록 설정 하 여 azure 센티널 또는 타사 siem과 통합 하 여 azure Cloud Services 로그 데이터를 모니터링할 수 있습니다.

- [SIEM과 통합](../security-center/continuous-export.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침** : azure에 대 한 Microsoft 맬웨어 방지 프로그램은 azure Cloud Services 및 가상 머신을 보호 합니다. 웹 응용 프로그램 방화 벽, 네트워크 방화벽, 맬웨어 방지 프로그램, 침입 감지 및 방지 시스템 (ID 또는 IP) 등과 같은 타사 보안 솔루션을 배포할 수도 있습니다.

- [Azure 기본 IP/ID 및 DDOS에서 제공 하는 기능 및 기능](https://docs.microsoft.com/azure/cloud-services/cloud-services-configuration-and-management-faq?&amp;preserve-view=true#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침** : azure RBAC (역할 기반 액세스 제어)를 사용 하 여 azure 리소스에 대 한 액세스를 관리 하는 것이 좋습니다. 그러나 azure Cloud Services는 Azure Resource Manager 기반 서비스가 아니므로 클래식 구독을 사용 해야 하므로 Azure RBAC 모델을 지원 하지 않습니다.

기본적으로 계정 관리자, 서비스 관리자 및 Co-Administrator는 Azure의 세 가지 클래식 구독 관리자 역할입니다. 

클래식 구독 관리자는 Azure 구독에 대한 모든 권한을 보유합니다. 이들은 Azure Portal, Azure Resource Manager API 및 클래식 배포 모델 API를 사용하여 리소스를 관리할 수 있습니다. Azure에 등록하는 데 사용된 계정은 자동으로 계정 관리자와 서비스 관리자로 설정됩니다. 추가 Co-Administrators 나중에 추가할 수 있습니다. 

서비스 관리자와 Co-Administrators는 구독 범위에서 소유자 역할 (Azure 역할)을 할당 받은 사용자에 게 동일한 액세스 권한을 가집니다. Azure Portal의 클래식 관리자 탭을 사용 하 여 Co-Administrators를 관리 하거나 서비스 관리자를 확인 합니다. 

다음 명령을 사용 하 여 PowerShell을 통해 클래식 서비스 관리자 및 공동에 대 한 역할 할당을 나열 합니다.

Get-AzRoleAssignment-IncludeClassicAdministrators

클래식 구독 관리 역할 간의 차이점을 검토 합니다. 

- [3 가지 클래식 구독 관리 역할 간의 차이점](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**참고** : 사용 가능한 역할 및 Azure Cloud Services 리소스를 운영 하 고 관리 하는 데 필요한 권한을 바탕으로 전용 관리 계정을 사용 하는 표준 운영 절차를 만드는 것이 좋습니다.

- [클래식 구독 관리 역할 간의 차이점](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침** : Azure Cloud Services에서 실행 되는 응용 프로그램에 대해 별도의 id를 관리 하지 않습니다. 사용자가 여러 id와 자격 증명을 관리할 필요가 없도록 Single Sign-On 구현 합니다.

- [SSO (Single Sign-On)](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침** : 높은 권한이 필요한 관리 작업에 대해 Azure에서 관리 하는 안전한 워크스테이션 (권한 있는 액세스 워크스테이션이 라고도 함)을 사용 하는 것이 좋습니다.

- [안전 하 고 Azure로 관리 되는 워크스테이션 이해](../active-directory/devices/concept-azure-managed-workstation.md)

- [Azure AD MFA를 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조 하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침** : Azure 클라우드 서비스 REST api를 사용 하 여 Azure 클라우드 서비스 리소스의 인벤토리를 통해 중요 한 정보를 제공 합니다. 배포 된 클라우드 서비스 리소스를 폴링하여 구성 및. pkg 리소스를 가져옵니다.

 예를 들어 몇 가지 Api가 아래에 나열 되어 있습니다.

- 배포 가져오기-배포 가져오기 작업은 배포에 대 한 구성 정보, 상태 및 시스템 속성을 반환 합니다.
- 패키지 가져오기-패키지 가져오기 작업은 배포에 대 한 클라우드 서비스 패키지를 검색 하 고 패키지 파일을 Microsoft Azure Blob 저장소에 저장 합니다.
- 클라우드 서비스 속성 가져오기-클라우드 서비스 속성 가져오기 작업은 지정 된 클라우드 서비스에 대 한 속성을 검색 합니다.

Azure Cloud Service REST Api 설명서를 검토 하 고 조직의 요구 사항에 따라 중요 한 정보에 대 한 데이터 보호를 위한 프로세스를 만듭니다.

- [배포 가져오기](/rest/api/compute/cloudservices/rest-get-deployment)

- [클라우드 서비스 속성 가져오기](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Get Package](/rest/api/compute/cloudservices/rest-get-package)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침** : Azure Cloud Services에 대 한 환경 유형 및 데이터 민감도 수준과 같은 개별 보안 도메인에 대해 별도의 구독 및 관리 그룹을 사용 하 여 격리를 구현 합니다.

Azure 클라우드 서비스의 Certificate 요소에서 "사용자 수준"을 편집 하 여 역할 프로세스에 부여 된 액세스 권한을 지정할 수도 있습니다. 승격 된 프로세스만 개인 키에 액세스할 수 있도록 하려면 승격 된 권한을 지정 합니다. limitedOrElevated 사용 권한을 통해 모든 역할 프로세스가 개인 키에 액세스할 수 있습니다. 가능한 값은 limitedOrElevated 또는 상승입니다. 기본값은 limitedOrElevated입니다.

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create)

- [WebRole 스키마](schema-csdef-webrole.md#Certificate)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침** : network 경계에서 Azure Marketplace의 타사 솔루션을 사용 하 여 중요 한 정보를 무단으로 전송 하는 행위를 모니터링 하 고 정보 보안 전문가에 게 경고 하는 동안 이러한 전송을 차단 하는 것이 좋습니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : 공유됨

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침** : Azure Cloud Services에 대 한 TLS v2를 구성 합니다. Azure Portal를 사용 하 여 스테이징 된 Azure Cloud Services 배포에 인증서를 추가 하 고 서비스의 .CSDEF 및 .CSCFG 파일에 인증서 정보를 추가 합니다. 응용 프로그램을 다시 패키지 하 고 새 패키지를 사용 하도록 준비 된 배포를 업데이트 합니다. 

Azure Cloud Services에 연결 된 Azure의 서비스 인증서를 사용 하 여 서비스와의 보안 통신을 사용 하도록 설정 합니다. 노출 된 HTTPS 끝점을 인증할 수 있는 인증서를 제공 합니다. 클라우드 서비스의 서비스 정의에서 서비스 인증서를 정의 하 고 역할의 인스턴스를 실행 하 여 가상 머신에 자동으로 배포 합니다.

관리 인증서를 사용 하 여 관리 API를 사용 하 여 인증) 관리 인증서를 사용 하면 클래식 배포 모델을 사용 하 여 인증할 수 있습니다. Visual Studio 또는 Azure SDK와 같은 많은 프로그램 및 도구에서 이러한 인증서를 사용하여 다양한 Azure 서비스의 구성 및 배포를 자동화합니다. 

추가 참조를 위해 Azure Service Management API는 Azure Portal를 통해 제공 되는 서비스 관리 기능에 대 한 프로그래밍 방식의 액세스를 제공 합니다. Python 용 azure SDK를 사용 하 여 Azure Cloud Services 및 Azure Storage 계정을 관리할 수 있습니다. Python 용 Azure SDK는 REST API Service Management API를 래핑합니다. 모든 API 작업은 TLS를 통해 수행 되며 x.509 v3 인증서를 사용 하 여 상호 인증 됩니다. 관리 서비스는 Azure에서 실행되는 서비스 내에서 액세스할 수 있습니다. HTTPS 요청을 보내고 HTTPS 응답을 받을 수 있는 애플리케이션에서 인터넷을 통해 직접 액세스할 수도 있습니다.

- [Azure에서 응용 프로그램에 대 한 TLS 구성](cloud-services-configure-ssl-certificate-portal.md)

- [Python에서 서비스 관리 사용](cloud-services-python-how-to-use-service-management.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침** : 타사 활성 검색 도구를 사용 하 여 사이트 또는 원격 서비스 공급자를 비롯 한 조직의 기술 시스템에서 저장, 처리 또는 전송 되는 중요 한 정보를 모두 식별 한 다음 조직의 중요 한 정보 인벤토리를 업데이트 하는 것이 좋습니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : 공유됨

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침** : 클라우드 서비스 (클래식)에 적용할 수 없습니다. 데이터 손실 방지는 적용 되지 않습니다.

시스템에서 데이터를 복사 하는 경우에도 데이터에 대 한 액세스 제어를 적용 하기 위해 자동화 된 호스트 기반 데이터 손실 방지 솔루션과 같은 타사 도구를 구현 하는 것이 좋습니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure의 고객 데이터를 안전 하 게 유지 하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현 하 고 유지 관리 합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : 공유됨

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침** : Azure Cloud Services는 미사용 암호화를 지원 하지 않습니다. Azure Cloud Services는 상태 비저장으로 설계 되었기 때문입니다. Azure Cloud Services는 외부 저장소 (예: 기본적으로 암호화 된 Azure Storage)를 지원 합니다.  

임시 디스크에 저장 된 응용 프로그램 데이터는 암호화 되지 않습니다. 고객은 필요에 따라이 데이터를 관리 하 고 암호화할 책임이 있습니다.  

- [Azure에서 미사용 암호화 이해](../security/fundamentals/encryption-atrest.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침** : Azure Monitor에서 클래식 메트릭 경고를 사용 하 여 중요 한 리소스에 적용 된 메트릭 중 하나가 임계값을 초과 하는 경우 알림을 받을 수 있습니다. 클래식 메트릭 경고는 차원이 아닌 메트릭에 대해서만 경고를 허용 하는 이전 기능입니다. 클래식 메트릭 경고보다 향상된 기능을 가진, 메트릭 경고라는 최신 기능도 있습니다. 

또한 Azure Cloud Services 앱의 가용성, 성능, 실패 및 사용 현황을 모니터링할 수 Application Insights. 이는 Application Insights Sdk의 결합 된 데이터를 Azure Cloud Services의 Azure 진단 데이터와 함께 사용 합니다.

- [Azure Monitor를 사용 하 여 클래식 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/platform/alerts-classic-portal.md)

- [메트릭 경고 개요](../azure-monitor/platform/alerts-metric-overview.md) 

- [Azure 클라우드 서비스에 대 한 Application Insights (클래식)](../azure-monitor/app/cloudservices.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조 하세요.*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침** :이 정보는 PaaS (Platform As a Service)를 사용 하는 azure Cloud Services 작업자 및 웹 역할에 대 한 azure 게스트 운영 체제와 관련이 있습니다. 그러나 IaaS (Infrastructure as a service)를 사용 하는 Virtual Machines에는 적용 되지 않습니다.

기본적으로 Azure는 고객의 게스트 운영 체제를 서비스 구성 (.cscfg) (예: Windows Server 2016)에 지정 된 운영 체제 제품군 내에서 지원 되는 최신 이미지로 정기적으로 업데이트 합니다.

고객이 Azure Cloud Services 배포에 대 한 특정 운영 체제 버전을 선택 하면 자동 운영 체제 업데이트를 사용 하지 않도록 설정 하 고 책임을 패치 합니다. 고객은 자신의 역할 인스턴스가 업데이트를 수신 하 고 있는지 확인 해야 합니다. 그렇지 않으면 응용 프로그램이 보안 취약점에 노출 될 수 있습니다.

- [Azure 게스트 OS](cloud-services-guestos-msrc-releases.md)

- [Azure 게스트 OS 지원 가능성 및 사용 중지 정책](cloud-services-guestos-retirement-policy.md)

- [클라우드 서비스를 구성 하는 방법 (클래식)](cloud-services-how-to-configure-portal.md)

- [게스트 OS 버전 관리](cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Azure Security Center 모니터링** : 해당 없음

**책임** : 공유됨

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침** : 타사 패치 관리 솔루션을 사용 합니다. 환경에서 이미 Configuration Manager를 사용 하는 고객은 System Center Updates Publisher를 사용 하 여 사용자 지정 업데이트를 Windows Server Update Service에 게시할 수 있습니다. 

이를 통해 Configuration Manager를 사용 하는 컴퓨터를 타사 소프트웨어를 사용 하는 업데이트 리포지토리로 패치 업데이트 관리 수 있습니다.

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침** : 고객이 지속적으로 DDoS 공격의 위험 범위를 이해 하는 것이 좋습니다. 

다음 시나리오를 통해 생각 하는 것이 좋습니다.

- 보호가 필요한 새로 공개된 Azure 리소스는 무엇인가요?
- 서비스에 단일 실패 지점이 있나요?
- 서비스를 격리하여 공격의 영향을 제한하면서도 유효한 고객에게 서비스를 계속 제공하려면 어떻게 해야 할까요?
- DDoS Protection Standard를 사용하도록 설정해야 하는데 설정하지 않은 가상 네트워크가 있나요?
- 내 서비스가 여러 지역에서 활성화/장애 조치(failover)와 함께 활성화됩니까?

지원 설명서:

- [Azure 리소스의 위험 평가](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침** : Azure Cloud Services에는 적용 되지 않습니다. 이 권장 사항은 IaaS 계산 리소스에 적용 됩니다.

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침** : 정기적으로 인벤토리를 조정 하 고 적절 한 시간 내에 권한 없는 리소스가 구독에서 삭제 되도록 하는 것이 좋습니다.

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침** : 고객은 계산 리소스에 대해 승인 된 Azure 리소스 및 승인 된 소프트웨어를 정의 해야 합니다.

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침** : Azure Security Center에서 사용할 수 있는 적응 응용 프로그램 제어 기능을 사용 합니다. 이 솔루션은 Windows 및 Linux, Azure 및 비 Azure 컴퓨터에서 실행할 수 있는 응용 프로그램을 제어 하는 데 도움이 되는 Security Center의 지능적이 고 자동화 된 종단 간 솔루션입니다. 또한 맬웨어로부터 컴퓨터를 강화 하는 데 도움이 됩니다. 

이 기능은 Azure 및 비 Azure Windows (모든 버전, 클래식 또는 Azure Resource Manager) 및 Linux 컴퓨터에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용 하 여 컴퓨터에서 실행 중인 응용 프로그램을 분석 하 고이 인텔리전스에서 허용 목록을 만듭니다. 이 기능은 응용 프로그램 허용 목록 정책을 구성 하 고 유지 관리 하는 프로세스를 간소화 하 여 다음과 같은 작업을 수행할 수 있도록 합니다.
- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.
- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.
- 오래되고 지원되지 않는 앱을 실행하지 않습니다.
- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.
- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침** : Azure Security Center에서 사용할 수 있는 적응 응용 프로그램 제어 기능을 사용 합니다. 이 솔루션은 Windows 및 Linux, Azure 및 비 Azure 컴퓨터에서 실행할 수 있는 응용 프로그램을 제어 하는 데 도움이 되는 Security Center의 지능적이 고 자동화 된 종단 간 솔루션입니다. 또한 맬웨어로부터 컴퓨터를 강화 하는 데 도움이 됩니다. 

이 기능은 Azure 및 비 Azure Windows (모든 버전, 클래식 또는 Azure Resource Manager) 및 Linux 컴퓨터에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용 하 여 컴퓨터에서 실행 중인 응용 프로그램을 분석 하 고이 인텔리전스에서 허용 목록을 만듭니다. 이 기능은 응용 프로그램 허용 목록 정책을 구성 하 고 유지 관리 하는 프로세스를 간소화 하 여 다음과 같은 작업을 수행할 수 있도록 합니다.

- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.

- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.

- 오래되고 지원되지 않는 앱을 실행하지 않습니다.

- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.

- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침** : Azure Security Center에서 사용할 수 있는 적응 응용 프로그램 제어 기능을 사용 합니다. 이 솔루션은 Windows 및 Linux, Azure 및 비 Azure 컴퓨터에서 실행할 수 있는 응용 프로그램을 제어 하는 데 도움이 되는 Security Center의 지능적이 고 자동화 된 종단 간 솔루션입니다. 또한 맬웨어로부터 컴퓨터를 강화 하는 데 도움이 됩니다. 

이 기능은 Azure 및 비 Azure Windows (모든 버전, 클래식 또는 Azure Resource Manager) 및 Linux 컴퓨터에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용 하 여 컴퓨터에서 실행 중인 응용 프로그램을 분석 하 고이 인텔리전스에서 허용 목록을 만듭니다. 이 기능은 응용 프로그램 허용 목록 정책을 구성 하 고 유지 관리 하는 프로세스를 간소화 하 여 다음과 같은 작업을 수행할 수 있도록 합니다.

- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.

- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.

- 오래되고 지원되지 않는 앱을 실행하지 않습니다.

- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.

- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침** : Azure Security Center에서 사용할 수 있는 적응 응용 프로그램 제어 기능을 사용 합니다. 이 솔루션은 Windows 및 Linux, Azure 및 비 Azure 컴퓨터에서 실행할 수 있는 응용 프로그램을 제어 하는 데 도움이 되는 Security Center의 지능적이 고 자동화 된 종단 간 솔루션입니다. 또한 맬웨어로부터 컴퓨터를 강화 하는 데 도움이 됩니다. 

이 기능은 Azure 및 비 Azure Windows (모든 버전, 클래식 또는 Azure Resource Manager) 및 Linux 컴퓨터에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용 하 여 컴퓨터에서 실행 중인 응용 프로그램을 분석 하 고이 인텔리전스에서 허용 목록을 만듭니다. 이 기능은 응용 프로그램 허용 목록 정책을 구성 하 고 유지 관리 하는 프로세스를 간소화 하 여 다음과 같은 작업을 수행할 수 있도록 합니다.

- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.

- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.

- 오래되고 지원되지 않는 앱을 실행하지 않습니다.

- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.

- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침** : Azure Security Center에서 사용할 수 있는 적응 응용 프로그램 제어 기능을 사용 합니다. 이 솔루션은 Windows 및 Linux, Azure 및 비 Azure 컴퓨터에서 실행할 수 있는 응용 프로그램을 제어 하는 데 도움이 되는 Security Center의 지능적이 고 자동화 된 종단 간 솔루션입니다. 또한 맬웨어로부터 컴퓨터를 강화 하는 데 도움이 됩니다. 

이 기능은 Azure 및 비 Azure Windows (모든 버전, 클래식 또는 Azure Resource Manager) 및 Linux 컴퓨터에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용 하 여 컴퓨터에서 실행 중인 응용 프로그램을 분석 하 고이 인텔리전스에서 허용 목록을 만듭니다. 이 기능은 응용 프로그램 허용 목록 정책을 구성 하 고 유지 관리 하는 프로세스를 간소화 하 여 다음과 같은 작업을 수행할 수 있도록 합니다.
- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.
- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.
- 오래되고 지원되지 않는 앱을 실행하지 않습니다.
- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.
- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: 사용자가 계산 리소스에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침** : Azure Security Center에서 사용할 수 있는 적응 응용 프로그램 제어 기능을 사용 합니다. 이 솔루션은 Windows 및 Linux, Azure 및 비 Azure 컴퓨터에서 실행할 수 있는 응용 프로그램을 제어 하는 데 도움이 되는 Security Center의 지능적이 고 자동화 된 종단 간 솔루션입니다. 또한 맬웨어로부터 컴퓨터를 강화 하는 데 도움이 됩니다. 

이 기능은 Azure 및 비 Azure Windows (모든 버전, 클래식 또는 Azure Resource Manager) 및 Linux 컴퓨터에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용 하 여 컴퓨터에서 실행 중인 응용 프로그램을 분석 하 고이 인텔리전스에서 허용 목록을 만듭니다. 이 기능은 응용 프로그램 허용 목록 정책을 구성 하 고 유지 관리 하는 프로세스를 간소화 하 여 다음과 같은 작업을 수행할 수 있도록 합니다.

- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.

- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.

- 오래되고 지원되지 않는 앱을 실행하지 않습니다.

- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.

- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침** : Azure Cloud Services를 사용 하는 중요 한 위험 수준의 응용 프로그램의 경우 격리를 제공 하는 별도의 구독 또는 관리 그룹을 구현 합니다.

네트워크 보안 그룹을 사용 하 여 인바운드 보안 규칙을 만들고, http와 같은 서비스를 선택 하 고, 사용자 지정 포트를 선택 하 고, 우선 순위 및 이름을 지정 합니다. 우선 순위는 규칙이 적용 되는 순서에 영향을 주며, 숫자 값이 낮을수록 이전 규칙이 적용 됩니다. 비즈니스 요구 사항에 따라 네트워크 트래픽을 분리 하거나 분할 하려면 네트워크 보안 그룹을 서브넷 또는 특정 네트워크 인터페이스에 연결 해야 합니다.

참조 된 링크에서 자세한 내용을 확인할 수 있습니다.

- [자습서-Azure Portal를 사용 하 여 네트워크 보안 그룹을 통해 네트워크 트래픽 필터링](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침** : Azure Cloud Services 리소스에 대 한 보안 구성 기준으로 Azure Security Center의 권장 사항을 사용 합니다. 

Azure Portal에서 Security Center를 선택 하 고, 계산 &amp; 앱을 선택 하 고, Azure Cloud Services를 선택 하 여 서비스 리소스에 적용 되는 권장 사항을 확인 합니다.

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침** : Azure Cloud Services에는 적용 되지 않습니다. 이는 클래식 배포 모델을 기반으로 합니다. 보안 Azure 리소스 구성을 유지 하려면 타사 솔루션을 사용 하는 것이 좋습니다.

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침** : Azure 클라우드 서비스의 구성 파일은 리소스에 대 한 운영 특성을 저장 합니다. 구성 파일의 복사본을 보안 저장소 계정에 저장할 수 있습니다.

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침** : Azure Cloud Services에는 적용 되지 않습니다. 이는 클래식 배포 모델을 기반으로 하며 Azure Resource Manager 배포 기반 구성 도구를 통해 관리할 수 없습니다.

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침** : Azure Cloud Services에는 적용 되지 않습니다. 이 권장 사항은 IaaS (Infrastructure as a service) 기반 계산 리소스에 적용 됩니다.

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침** : Azure Security Center을 사용 하 여 Azure 리소스에 대 한 기준 검색을 수행 합니다.  

- [Azure Security Center에서 권장 사항을 수정 하는 방법](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침** : Azure Security Center에서 계산 &amp; 앱 기능을 선택 하 고 가상 컴퓨터, 서버 및 컨테이너에 대 한 권장 사항을 따릅니다.

- [Azure Security Center 컨테이너 권장 사항 이해](/azure/security-center/security-center-container-recommendations)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침** : Azure Cloud Services는 클래식 배포 모델을 기반으로 하며 Azure Key Vault와 통합 되지 않습니다.

Azure Cloud Services에 사용 되는 자격 증명과 같은 비밀을 보호할 수 있으므로 매번 암호를 입력 하지 않아도 됩니다. 시작 하려면 일반 텍스트 암호를 지정 하 고 Convertto-html, PowerShell 명령을 사용 하 여 보안 문자열로 변환 합니다. 그런 다음 Convertfrom-csv를 사용 하 여이 보안 문자열을 암호화 된 표준 문자열로 변환 합니다.  이제 설정 된 콘텐츠를 사용 하 여이 암호화 된 표준 문자열을 파일에 저장할 수 있습니다.

또한 Azure Cloud Services에 사용 되는 인증서의 개인 키를 보안 저장소에 저장 하는 것이 좋습니다.

- [PowerShell에서 원격 데스크톱 구성](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침** : 매번 암호를 입력 하지 않아도 되도록 Azure Cloud Services에 사용 되는 자격 증명과 같은 보안 암호입니다. 
 

시작 하려면 일반 텍스트 암호를 지정 하 고 Convertto-html, PowerShell 명령을 사용 하 여 보안 문자열로 변경 합니다. 그런 다음 Convertfrom-csv를 사용 하 여이 보안 문자열을 암호화 된 표준 문자열로 변환 합니다. 이제 Set-Content 명령을 사용 하 여이 암호화 된 표준 문자열을 파일에 저장 합니다.

Azure Cloud Services에 사용 되는 인증서의 개인 키를 보안 저장소 위치에 저장 합니다.

- [PowerShell에서 원격 데스크톱 구성](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조 하세요.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: 중앙에서 관리 되는 맬웨어 방지 소프트웨어 사용

**지침** : azure Cloud Services 및 Virtual Machines에서 azure 용 Microsoft 맬웨어 방지 프로그램을 사용할 수 있습니다. 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 하 고 제거 하는 데 도움이 되는 무료 실시간 보호입니다. 알려진 악성 또는 원치 않는 소프트웨어가 Azure 시스템에서 스스로의 설치나 실행을 시도할 때 경고를 생성합니다. 

PowerShell 기반 맬웨어 방지 cmdlet을 사용 하 여 "Get-azureserviceantimalwareconfig"로 맬웨어 방지 구성을 가져옵니다.

Azure Cloud Services에서 시작 작업의 PowerShell 스크립트를 사용 하 여 맬웨어 방지 확장을 사용 하도록 설정 합니다.

지능적이 고 자동화 된 종단 간 솔루션인 Azure Security Center에서 적응 응용 프로그램 제어 기능을 선택 합니다. 맬웨어 방지를 위해 컴퓨터를 강화 하는 데 도움이 되며 맬웨어 방지 솔루션에 의해 누락 될 수 있는 응용 프로그램을 포함 하 여 악의적인 응용 프로그램의 실행 시도를 차단 하거나 경고할 수 있습니다.

- [Azure Cloud Services에 대 한 맬웨어 방지 확장을 자동화 된 방식으로 추가 하려면 어떻게 해야 하나요?](https://docs.microsoft.com/azure/cloud-services/cloud-services-configuration-and-management-faq?&amp;preserve-view=true#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [맬웨어 방지 프로그램 배포 시나리오](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침** : 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md)

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침** : Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다. 

구독 (예: 프로덕션, 비프로덕션)을 명확 하 게 표시 하 고 Azure 리소스를 명확 하 게 식별 하 고 범주화 하기 위한 이름 지정 시스템을 만듭니다.

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침** : 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다. 

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침** : MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침** : 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다. 

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md) 

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침** : Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침** : 다음 Microsoft 시행 규칙에 따라 침투 테스트에서 Microsoft 정책을 위반하지 않는지 확인합니다. https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 레드 팀 및 라이브 사이트 침투 테스트 실행에 대한 자세한 내용은 ](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 참조하세요.

**Azure Security Center 모니터링** : 해당 없음

**책임** : 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
