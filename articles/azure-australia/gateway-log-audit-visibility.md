---
title: Azure 오스트레일리아의 게이트웨이 로깅, 감사 및 표시 유형
description: 오스트레일리아 정부 정책, 규정 및 법규의 특정 요구 사항을 충족 하도록 오스트레일리아 지역 내에서 로깅, 감사 및 표시 여부를 구성 하는 방법입니다.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: b7a9f28d06b5e921b5f1b8defa151641bb039940
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990209"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Azure 오스트레일리아의 게이트웨이 로깅, 감사 및 표시 유형

사이버 보안 위협 검색 및 대응은 시스템 작업과 관련 된 데이터 생성, 수집 및 분석을 사용 합니다.

Microsoft는 azure에 배포 된 시스템의 보안을 관리 하는 로깅, 감사 및 가시성을 구현 하는 데 도움이 되는 Azure의 기본 제공 도구를 제공 합니다. 또한 ACSC (오스트레일리아 사이버 Security Center) 소비자 지침과 ISM (Information Security Manual)의 의도에 맞춰 조정 되는 참조 아키텍처도 있습니다.

게이트웨이는 네트워크 계층에서 정보 흐름 제어 메커니즘으로 작동 하며, OSI (Open System Interconnect) 모델의 상위 계층에서 정보를 제어할 수도 있습니다. 게이트웨이는 보안 도메인 간의 데이터 흐름을 제어 하 고 외부 네트워크에서 무단 액세스를 방지 하는 데 필요 합니다. 보안 도메인 간의 정보 흐름을 제어 하기 위해 게이트웨이의 중요도를 고려 하 여 모든 오류, 특히 더 높은 분류에는 심각한 영향을 미칠 수 있습니다. 이와 같이 사이버 보안 인시던트를 야기 하는 상황에 대 한 경고를 야기 하는 강력한 메커니즘은 게이트웨이에 특히 중요 합니다.

게이트웨이에 대 한 로깅 및 경고 기능을 구현 하는 것은 사이버 보안 인시던트, 시도 하는 침입 및 비정상적인 사용 패턴을 검색 하는 데 도움이 됩니다. 또한 이벤트 로그를 별도의 보안 로그 서버에 저장 하면 악의적 사용자가 대상 사이버 침입 증명 정보를 제거 하기 위해 로깅 정보를 삭제 하기가 어려워집니다.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>ACSC (오스트레일리아 사이버 Security Center) 요구 사항

사용자 시스템에 대 한 전반적인 보안 요구 사항은 ACSC ISM (정보 보안 설명서)에 정의 되어 있습니다. Azure 내에서 이러한 요구 사항을 충족 하는 법인 엔터티를 지원 하기 위해 *ACSC 소비자 가이드 – PROTECTED* 및 *ACSC 인증 보고서에 Microsoft Azure-Microsoft Azure* 게시는 다음과 같은 특정 요구 사항을 자세히 설명 합니다. 로깅, 감사 및 표시 유형:

1. 공유 되는 기본 클라우드 리소스를 사용 하 여 발생 하는 위험을 완화 하기 위해, Azure Security Center, Azure Monitor, Azure Policy 및 Azure Advisor를 비롯 한 제공 된 기능을 Microsoft Azure 하 여 엔터티를 수행 하는 데 도움을 받아야 합니다. Azure 워크 로드에 대 한 실시간 모니터링

2. 또한 ACSC는 오스트레일리아 정부 모니터링 전체의 모든 보안 로그를 ACSC로 전달 하는 것을 권장 합니다.

3. 위험 완화를 지원 하기 위해 Azure 구독 내에서 다음을 구성 해야 합니다.

    * Azure Security Center 사용
    * 표준 계층으로 업그레이드
    * 지원 되는 Azure Vm에 대 한 Microsoft Monitoring Agent 자동 프로 비전 사용
    * 보안 센터 대시보드의 보안 권장 사항 및 경고를 정기적으로 검토, prioritise 및 완화

4. 정부 엔터티는 Azure 구독에서 ACSC로의 로그 및 이벤트 전달을 활성화 하 여 ACSC에이 지침을 준수 하지 않는 표시 유형을 제공 해야 합니다. Azure Event Hubs는 Azure에 대 한 외부 로그 스트리밍을 수행 하는 기능을 제공 합니다.

5. Azure 내에서 사용 하도록 설정 된 로깅은 ISM에 지정 된 요구 사항에 맞게 설정 해야 합니다.

6. Microsoft는 Azure 내에서 90 일 동안 로그를 유지 합니다. 고객 엔터티는 NAA AFDA에서 7 년 동안 로그를 보존할 수 있도록 로그 보관 regime을 구현 해야 합니다.

7. 온-프레미스 또는 Azure 기반 SIEM (보안 정보 및 이벤트 관리) 기능을 포함 하는 정보 엔터티가 해당 시스템에 로그를 전달할 수도 있습니다.

8. 는 NSGs (네트워크 보안 그룹) 및 Virtual Machines에 대 한 Network Watcher 흐름 로그를 구현 해야 합니다. 이러한 로그는 보안 로그만 포함 하는 전용 저장소 계정에 저장 해야 하며, 저장소 계정에 대 한 액세스는 역할 기반 액세스 제어를 사용 하 여 보호 해야 합니다.

9. Azure 워크 로드가 로깅 및 모니터링에 대 한 ISM의 의도를 충족 하는지 확인 하기 위해 Azure 워크 로드가 ACSC 소비자 지침을 구현 해야 합니다. 또한 Azure의 오스트레일리아 정부 사용과 관련 된 실시간 모니터링, 경고 및 로그를 받을 수 있도록 지 원하는 Azure 기능을 사용 해야 합니다.

## <a name="architecture"></a>아키텍처

Azure 환경에서 들어오고 나가는 네트워크 트래픽을 안전 하 게 이해 하려면 적절 한 구성 요소 집합에서 필요한 로깅을 사용 하도록 설정 해야 합니다. 이렇게 하면 환경의 전체 표시를 보장 하 고 분석을 수행 하는 데 필요한 데이터를 제공 합니다.

![Azure 모니터링 아키텍처](media/visibility.png)

## <a name="components"></a>구성 요소

위에 표시 된 아키텍처는 로그 원본, 로그 수집, 로그 보존, 로그 분석 또는 인시던트 응답의 기능을 제공 하는 불연속 구성 요소로 구성 되어 있습니다. 이 아키텍처에는 일반적으로 인터넷에 액세스할 수 있는 Azure 배포와 관련 된 개별 구성 요소가 포함 됩니다.

|함수|구성 요소|
|---|---|
|로그 원본|<ul><li>애플리케이션 게이트웨이</li><li>VPN 게이트웨이</li><li>Azure Firewall</li><li>네트워크 가상 어플라이언스</li><li>Azure Load Balancer</li><li>가상 머신</li><li>DNS (Domain Name System) 서버</li><li>Syslog 및/또는 로그 수집 서버</li><li>NSG</li><li>Azure 동작 로그</li><li>Azure 진단 로그</li><li>Azure Policy</li></ul>|
|로그 수집|<ul><li>Event Hubs</li><li>Network Watcher</li><li>Log Analytics</li></ul>|
|로그 보존|<ul><li>Azure Storage</li></ul>|
|로그 분석|<ul><li>Azure Security Center (ASC)</li><li>Azure Advisor</li><li>Log Analytics 솔루션<ul><li>트래픽 분석</li><li>DNS 분석(미리 보기)</li><li>Activity Log Analytics</li></ul></li><li>SIEM</li><li>ACSC</li></ul>|
|사고 대응|<ul><li>Azure 경고</li><li>Azure Automation</li></ul>|
|

아키텍처는 먼저 필요한 원본에서 로그를 생성 한 다음 centralised 리포지토리로 수집 하 여 작동 합니다. 로그를 수집한 후에는 다음을 수행할 수 있습니다.

* Azure analysis services에서 통찰력을 얻는 데 사용 됩니다.
* 외부 시스템으로 전달 하거나
* 장기 보존을 위해 저장소에 보관 하세요.

분석 도구에 의해 식별 되는 주요 이벤트 또는 인시던트에 응답 하기 위해 경고를 구성 하 고 자동화 된 관리 및 대응에 필요한 작업을 수행 하도록 개발 된 자동화를 만들 수 있습니다.

## <a name="general-guidance"></a>일반 지침

이 문서에 나열 된 구성 요소를 구현 하는 경우 다음과 같은 일반적인 지침이 적용 됩니다.

* 서비스의 지역 가용성의 유효성을 검사 하 여 모든 데이터가 공인 위치 내에 유지 되 고 보호 된 워크 로드에 대 한 첫 번째 기본 설정으로 AU Central 또는 AU Central 2에 배포 합니다.

* 개별 서비스의 인증 상태는 *Azure-Acsc 인증 보고서 – 보호 된 2018* 게시를 참조 하 고, *ACSC 소비자 가이드에 따라 보고서에 포함 되지 않은 관련 구성 요소에 대해 자체 평가를 수행 합니다. 보호 된 Microsoft Azure*

* 이 문서에서 참조 하지 않는 구성 요소의 경우에는 로그 생성, 캡처, 분석 및 유지와 관련 된 원칙을 따라야 합니다.

* Azure에서 호스트 되는 시스템에 대 한 모든 네트워크 수신 및 송신 지점과 높은 가치의 시스템에서 로깅, 감사 및 표시 여부를 식별 하 고 prioritise 합니다.

* 로그를 통합 하 고 저장소 계정, Log Analytics 작업 영역 및 Event Hubs 같은 로깅 도구 인스턴스 수를 최소화 합니다.

* 역할 기반 액세스 제어를 통해 관리자 권한 제한

* 계정에 대해 MFA (Multi-factor Authentication)를 사용 하 여 Azure에서 리소스 관리 또는 구성

* 여러 구독에서 로그 수집을 centralising 하는 경우 관리자에 게 각 구독에 필요한 권한이 있는지 확인 합니다.

* Nva (네트워크 가상 어플라이언스), 로그 수집 서버 및 DNS 서버를 비롯 하 여 Virtual Machines에 대 한 네트워크 연결 및 필요한 프록시 구성을 확인 하 여 Log Analytics 작업 영역과 같은 필수 Azure 서비스에 연결 Event Hubs , 및 저장소

* Microsoft Monitoring Agent (MMA)를 제품 TLS 버전 1.2으로 구성 합니다.

* Azure Policy를 사용 하 여 요구 사항을 모니터링 하 고 준수 적용

* 저장소 및 데이터베이스와 같은 모든 데이터 리포지토리에 암호화 적용

* 저장소 계정 및 연결 된 데이터의 가용성을 위해 LRS (로컬 중복 저장소) 및 스냅숏 사용

* 재해 복구 전략에 맞게 GRS (지역 중복 저장소) 또는 오프 사이트 저장소 고려

|리소스|URL|
|---|---|
|오스트레일리아 규정 및 정책 준수 문서|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure 제품-오스트레일리아 지역 및 비 지역|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, 오스트레일리아-중부, 오스트레일리아-중부-2, 오스트레일리아-동부, 오스트레일리아-동남](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Microsoft Azure 보안 및 감사 로그 관리 백서|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Microsoft Monitoring Agent 구성|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)|
|

## <a name="component-guidance"></a>구성 요소 지침

이 섹션에서는 전체 로깅, 감사 및 가시성 아키텍처에서 각 구성 요소 및 해당 역할의 용도에 대 한 정보를 제공 합니다. 참조 설명서, 가이드 및 자습서와 같은 유용한 리소스에 액세스 하기 위한 추가 링크가 제공 됩니다.

## <a name="log-sources"></a>로그 원본

분석, 경고 또는 보고를 완료할 수 있도록 하려면 필요한 로그를 생성 해야 합니다. Azure 로그는 제어/관리 로그, 데이터 평면 로그 및 처리 된 이벤트로 분류 됩니다.

|형식|Description|
|---|---|
|제어/관리 로그|Azure Resource Manager 작업에 대 한 정보 제공|
|데이터 평면 로그|Azure 리소스 사용의 일부로 발생 하는 이벤트에 대 한 정보를 제공 합니다. 예를 들어 가상 컴퓨터의 로그 및 사용 가능한 진단 로그는 Azure Monitor|
|처리 된 이벤트|Azure에서 처리 된 일부 이벤트가 분석 이벤트/경고에 대 한 정보를 제공 합니다. 예를 들어 Azure Security Center를 처리 하 고 구독을 일부 이벤트가 분석 하 여 보안 경고를 제공 합니다.|
|

### <a name="application-gateway"></a>애플리케이션 게이트웨이

Azure 애플리케이션 게이트웨이는 Azure 환경에서 사용할 수 있는 진입점 중 하나 이며, 웹 응용 프로그램과 통신 하는 들어오는 연결과 관련 된 정보를 캡처해야 합니다. Application Gateway 웹 응용 프로그램 사용과 관련 된 중요 한 정보를 제공 하 고 사이버 보안 인시던트를 검색 하는 기능을 지원할 수 있습니다. Application Gateway은 Azure Monitor의 활동 로그 및 진단 로그에 메타 데이터를 보내고,이를 Log Analytics 하거나 이벤트 허브 또는 저장소 계정에 배포할 수 있습니다.

|리소스|링크|
|---|---|
|Application Gateway 설명서|[https://docs.microsoft.com/azure/application-gateway/](https://docs.microsoft.com/azure/application-gateway/)|
|Application Gateway 빠른 시작 가이드|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)|
|

### <a name="vpn-gateway"></a>VPN 게이트웨이

VPN Gateway은 온-프레미스 환경 및 관리 트래픽에 대 한 연결과 같이 Azure 환경에 대 한 광범위 한 통신에 대 한 잠재적인 진입점입니다. VPN gateway에 대 한 로깅은 Azure 환경에 대 한 연결에 대 한 통찰력 및 추적 기능을 제공 합니다. 로깅은 감사 및 분석을 제공 하 고 악의적인 또는 비정상적인 연결의 검색 또는 조사를 지원할 수 있습니다. VPN Gateway 로그는 Azure Monitor 활동 로그로 전송 되며, Log Analytics 하거나 이벤트 허브 또는 저장소 계정에 배포할 수 있습니다.

|리소스|링크|
|---|---|
|VPN Gateway 설명서|[https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway)|
|오스트레일리아 정부 관련 VPN Gateway 지침|[Azure VPN Gateway 구성](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Azure Firewall

Azure 방화벽은 Azure 환경에서 제어 된 종료 지점을 제공 하 고, 시도 된 아웃 바운드 연결에 대 한 정보를 포함 하 여 생성 된 로그는 로깅 전략의 중요 한 요소입니다. 이러한 로그는 시스템이 설계 된 대로 작동 하는지 확인 하 고 무단 외부 시스템에 대 한 연결을 시도 하는 악성 코드 또는 행위자를 검색 하는 데 도움을 줍니다. Azure 방화벽은 Azure Monitor의 활동 로그 및 진단 로그에 로그를 기록 하 여 Log Analytics에 사용 하거나 이벤트 허브 또는 저장소 계정에 배포할 수 있습니다.

|리소스|링크|
|---|---|
|Azure Firewall 설명서|[https://docs.microsoft.com/azure/firewall/](https://docs.microsoft.com/azure/firewall)|
|자습서: Azure Firewall 로그 및 메트릭 모니터링|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)|
|

### <a name="network-virtual-appliances-nva"></a>NVA (네트워크 가상 어플라이언스)

Nva는 Azure에서 기본적으로 사용할 수 있는 보안 기능을 보완 하는 데 사용할 수 있습니다. Nva에서 생성 된 로그는 사이버 보안 인시던트를 검색 하는 데 중요 한 리소스가 될 수 있으며 전체 로깅, 감사 및 표시 유형 전략의 핵심 부분입니다. Nva에서 로그를 캡처하려면 제품 (MMA)를 Microsoft Monitoring Agent 합니다. MMA 설치를 지원 하지 않는 Nva의 경우 Syslog 또는 다른 로그 수집 서버를 사용 하 여 로그를 릴레이 하는 것이 좋습니다.

|리소스|링크|
|---|---|
|네트워크 가상 어플라이언스 개요|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|NVA 설명서|Azure의 관련 NVA 구현에 대 한 공급 업체 설명서를 참조 하세요.|
|

### <a name="azure-load-balancer"></a>Azure Load Balancer

Azure Load Balancer 로그는 Azure에 배포 된 시스템과 관련 된 연결 및 사용에 대 한 유용한 정보를 얻는 데 사용 됩니다. 상태 및 가용성 모니터링에는 사용할 수 있지만, 통신 트래픽에 필요한 통찰력을 얻고 악성 또는 비정상적인 트래픽 패턴을 검색 하는 또 다른 주요 구성 요소가 형성 됩니다. Azure Load Balancer는 Azure Monitor의 활동 로그 및 진단 로그에 기록 하 여 Log Analytics 하거나 이벤트 허브 또는 저장소 계정에 배포할 수 있습니다.

|리소스|링크|
|---|---|
|Azure Load Balancer 설명서|[https://docs.microsoft.com/azure/load-balancer](https://docs.microsoft.com/azure/load-balancer)|
|표준 Load Balancer에 대한 메트릭 및 상태 진단|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)|
|

### <a name="virtual-machines"></a>가상 머신

Virtual Machines는 네트워크 통신을 보내고 받고, 데이터를 처리 하 고, 서비스를 제공 하는 끝점입니다. Virtual Machines 데이터 또는 중요 한 시스템 서비스를 호스트할 수 있으므로 올바르게 작동 하는지 확인 하 고 사이버 보안 인시던트를 검색 하는 것이 중요할 수 있습니다. 시스템의 작업 및 해당 시스템에서 수행 되는 작업을 추적할 수 있는 다양 한 이벤트 및 감사 로그를 수집 Virtual Machines 합니다. Virtual Machines에서 수집 된 로그는 Azure Security Center 및 적용 가능한 Log Analytics 솔루션으로 일부 이벤트가 분석 수 있는 Microsoft Monitoring Agent를 사용 하 여 Log Analytics 작업 영역으로 전달 될 수 있습니다. 또한 Virtual Machines는 직접 또는 로그 수집 서버를 통해 Azure Event Hubs 또는 SIEM에 직접 통합할 수 있습니다.

|리소스|링크|
|---|---|
|가상 머신|[https://docs.microsoft.com/azure/virtual-machines](https://docs.microsoft.com/azure/virtual-machines)|
|Virtual Machines에서 데이터 수집|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|
|Event Hubs로 가상 컴퓨터 로그 스트림|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs)|
|

### <a name="domain-name-services-dns-servers"></a>DNS (도메인 이름 서비스) 서버

DNS 서버 로그는 시스템에서 액세스 하려고 하는 서비스와 관련 된 주요 정보를 내부적으로 또는 외부적으로 제공 합니다. DNS 로그를 캡처하면 사이버 보안 인시던트를 식별 하 고 인시던트 유형과 영향을 받을 수 있는 시스템에 대 한 통찰력을 제공할 수 있습니다. MMA (Microsoft Management Agent)를 사용 하 여 Log Analytics DNS 분석 (미리 보기)에서 사용할 수 있도록 로그를 전달할 수 있습니다.

|리소스|링크|
|---|---|
|가상 네트워크에 대 한 Azure 이름 확인|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)|
|

### <a name="syslog-and-log-collection-servers"></a>Syslog 및 로그 수집 서버

네트워크 가상 어플라이언스 또는 SIEM 내에서 사용 하기 위해 다른 시스템의 사용자 지정 보안 로그에서 로그를 받으려면 전용 서버를 Azure Vnet 내에 배포할 수 있습니다. Syslog 로그는 Syslog 서버에서 수집 되 고 분석을 위해 Log Analytics로 릴레이할 수 있습니다. 로그 수집 서버는 centralised 모니터링 시스템 또는 SIEMs에서 사용 하는 모든 로그 집계 및 배포 기능에 대 한 일반적인 용어입니다. 이러한 기능을 사용 하 여 네트워크 아키텍처와 보안을 간소화 하 고 centralised 기능에 배포 되기 전에 로그를 필터링 및 집계할 수 있습니다.

|리소스|링크|
|---|---|
|Log Analytics의 Syslog 데이터 원본|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)|
|로그 수집 서버|모니터링 및 SIEM 아키텍처에 대 한 자세한 내용은 공급 업체 설명서를 참조 하세요.|
|

### <a name="network-security-groups-nsgs"></a>NSG(네트워크 보안 그룹)

NSGs는 Azure에서 가상 네트워크로 들어오고 나가는 트래픽을 제어 합니다. NSGs는 Azure 내의 트래픽과 온-프레미스 또는 인터넷과 같은 외부 네트워크와 Azure 간의 트래픽을 포함 하 여 허용 되거나 거부 되는 트래픽 흐름에 대 한 규칙을 적용 합니다. NSGs는 가상 네트워크 내의 서브넷 또는 개별 네트워크 인터페이스에 적용 됩니다. Azure에서 시스템이 들어오고 나가는 트래픽에 대 한 정보를 캡처하려면 nsg 흐름 로그 기능을 Network Watcher 통해 NSG 로그를 사용 하도록 설정할 수 있습니다. 이러한 로그는 시스템의 표준 작업에 대 한 기준선을 형성 하는 데 사용 되며, Azure에서 호스트 되는 시스템의 트래픽 패턴에 대 한 자세한 정보를 제공 하는 트래픽 분석의 데이터 원본입니다.

|리소스|링크|
|---|---|
|네트워크 보안 그룹 설명서|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|네트워크 보안 그룹에 대한 흐름 로깅 소개|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|
|자습서: Azure Portal를 사용 하 여 가상 컴퓨터와의 네트워크 트래픽 로그|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)|
|

### <a name="azure-activity-log"></a>Azure 동작 로그

Azure Monitor의 일부인 azure 활동 로그는 Azure에서 발생 한 구독 수준 이벤트에 대 한 통찰력을 제공 하는 구독 로그입니다. 활동 로그를 통해 구독의 리소스 ***에*** 대해 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 ' 무엇을, 누가, 언제 '를 확인할 수 있습니다. 활동 로그는 Azure 환경 내에서 수행 되는 구성 변경 내용을 추적 하는 데 매우 중요 합니다. Azure 활동 로그는 Log Analytics 솔루션에서 자동으로 사용할 수 있으며, Event Hubs 또는 Azure Storage 처리 또는 보존을 위해 보낼 수 있습니다.

|리소스|링크|
|---|---|
|Azure 활동 로그 설명서|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|Azure 활동 로그를 Event Hubs로 스트림|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)|
|

### <a name="azure-diagnostic-log"></a>Azure 진단 로그

Azure Monitor 진단 로그는 해당 서비스의 작업에 대 한 풍부 하 고 빈번한 데이터를 제공 하는 Azure 서비스에서 내보낸 로그입니다. 진단 로그는 상세 수준에서 리소스 작업에 대 한 통찰력을 제공 하며 감사 또는 문제 해결 등의 다양 한 요구 사항에 사용할 수 있습니다. Azure 진단 로그는 Log Analytics 솔루션에서 자동으로 사용할 수 있으며, Event Hubs 또는 Azure Storage 처리 또는 보존을 위해 보낼 수 있습니다.

|리소스|링크|
|---|---|
|Azure 진단 로그 설명서|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|진단 로그에 대 한 지원 서비스|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)|
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy는 형식, 위치, 구성 등 리소스를 배포할 수 있는 방법에 대 한 규칙을 적용 합니다. 요구 사항을 준수 하는 경우에만 리소스를 배포할 수 있도록 Azure Policy 구성할 수 있습니다. Azure Policy는 Azure 환경의 무결성을 유지 하기 위한 핵심 구성 요소입니다. Azure Policy 관련 된 이벤트는 Azure 활동 로그에 기록 되 고 Log Analytics 솔루션에서 자동으로 사용할 수 있거나 처리 또는 보존을 위해 Event Hubs 하거나 Azure Storage로 전송 될 수 있습니다.

|리소스|링크|
|---|---|
|Azure Policy 설명서|[https://docs.microsoft.com/azure/governance/policy](https://docs.microsoft.com/azure/governance/policy)|
|Azure 청사진을 사용 하 여 Azure Policy 및 리소스 관리자 템플릿 활용|[https://docs.microsoft.com/azure/governance/blueprints/overview](https://docs.microsoft.com/azure/governance/blueprints/overview)|
|

## <a name="log-collection"></a>로그 수집

여러 로그 원본에서 생성 된 로그는 지속적인 액세스 및 분석을 위해 centralised 위치에 저장 해야 합니다. Azure는 로그 형식 및 요구 사항에 따라 utilised 수 있는 로그 컬렉션에 대 한 여러 메서드와 옵션을 제공 합니다.

### <a name="event-hubs"></a>Event Hubs

이벤트 허브의 목적은 배포를 위해 다양 한 원본에 대 한 로그 데이터를 집계 하는 것입니다. 이벤트 허브에서 로그 데이터를 SIEM, 규정 준수를 위한 ACSC, 장기 보존을 위한 저장소로 전송할 수 있습니다.

|리소스|링크|
|---|---|
|Event Hubs 설명서|[https://docs.microsoft.com/azure/event-hubs](https://docs.microsoft.com/azure/event-hubs)|
|Event Hubs 및 외부 도구에 대 한 지침|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics은 Azure Monitor의 일부 이며 로그 분석에 사용 됩니다. Log Analytics는 Azure 내에서 사용할 수 있는 다양 한 분석 도구와 솔루션에 대해 로그 데이터를 사용할 수 있는 저장소 메커니즘으로 작업 영역을 사용 합니다. Log Analytics는 광범위 한 Azure 구성 요소와 직접 통합 되며 Microsoft Monitoring Agent를 통해 Virtual Machines.

|리소스|링크|
|---|---|
|Log Analytics 설명서|[https://docs.microsoft.com/azure/azure-monitor](https://docs.microsoft.com/azure/azure-monitor)|
|자습서: Log Analytics에서 데이터 분석|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)|
|

### <a name="network-watcher"></a>Network Watcher

Network Watcher를 사용 하는 것은 Azure 구독에서 네트워크 트래픽을 이해 하 고 캡처하기 위해 ACSC에서 권장 됩니다. NSG 흐름 로그는 Azure를 통해 기본적으로 향상 된 가시성, 분석 및 보고 기능을 제공 하는 Log Analytics 트래픽 분석 솔루션에 대 한 입력을 제공 합니다. 또한 Network Watcher는 가상 컴퓨터에 로그인 할 필요 없이 Azure Portal에서 직접 패킷 캡처 기능을 제공 합니다. 패킷 캡처를 사용 하면 가상 머신에서 들어오고 나가는 트래픽을 추적 하는 패킷 캡처 세션을 만들 수 있습니다.

|리소스|링크|
|---|---|
|Network Watcher|[https://docs.microsoft.com/azure/network-watcher](https://docs.microsoft.com/azure/network-watcher)|
|패킷 캡처 개요|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)|
|

## <a name="log-retention"></a>로그 보존

오스트레일리아 정부 조직의 경우 Azure 내에서 캡처된 로그는 최대 7 년 동안 로그를 보존 하도록 지정 하는 오스트레일리아 [da (관리 기능 삭제 기관)](http://www.naa.gov.au/information-management/records-authorities/types-of-records-authorities/AFDA/index.aspx)의 국가별 보관에 따라 유지 되어야 합니다.

|로그 위치|보존 기간|
|---|---|
|Azure 동작 로그|최대 90 일|
|Log Analytics 작업 영역|최대 2 년|
|Event Hubs|최대 7 일|
|

AFDA 및 기타 입법 요구 사항을 준수 하기 위해 로그를 적절 하 게 보관 해야 합니다.

### <a name="azure-storage"></a>Azure Storage

Azure Storage은 Azure에서 장기 보존에 대 한 로그에 대 한 리포지토리입니다. Azure Storage는 Event Hubs, Azure 활동 로그 및 Azure 진단 로그를 포함 하 여 Azure에서 로그를 보관 하는 데 사용할 수 있습니다. 저장소에 있는 데이터의 보존 기간은 0으로 설정 하거나 일 수로 지정할 수 있습니다. 0 일의 보존은 로그가 영원히 유지 됨을 의미 하 고, 그렇지 않은 경우 값은 1에서 2147483647 사이의 일 수 있습니다.

|리소스|링크|
|---|---|
|Azure Storage 설명서|[https://docs.microsoft.com/azure/storage](https://docs.microsoft.com/azure/storage)|
|Azure Blob Storage 또는 Azure Data Lake Storage에서 Azure Event Hubs를 통해 이벤트 캡처|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|자습서: Azure Storage를 사용하여 Azure 메트릭 보관 및 데이터 기록|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data)|
|Azure Storage 복제|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy)|
|Blob의 스냅숏 만들기|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>Log Analytics

Centralised 위치에 생성 되 고 저장 된 후에는 로그를 일부 이벤트가 분석 하 여 시도 또는 성공한 보안 인시던트를 검색 하도록 지원 해야 합니다. 보안 인시던트가 감지 되 면 에이전시는 이러한 인시던트에 대응 하 고 위협을 추적, 포함 및 수정 하는 기능이 필요 합니다.

### <a name="azure-security-center-asc"></a>Azure Security Center (ASC)

Azure Security Center는 통합 보안 관리 및 고급 위협 방지 기능을 제공 합니다. Azure Security Center는 워크 로드에 보안 정책을 적용 하 고, 위협에 대 한 노출을 제한 하 고, 공격을 감지 하 고 대응할 수 있습니다. Azure Security Center는 광범위 한 Azure 구성 요소에 대 한 대시보드 및 분석을 제공 합니다. Azure Security Center 사용은 ACSC 소비자 지침에서 요구 사항으로 지정 됩니다.

|리소스|링크|
|---|---|
|Azure Security Center 설명서|[https://docs.microsoft.com/azure/security-center](https://docs.microsoft.com/azure/security-center)|
|빠른 시작: Security Center 표준에 Azure 구독 온보딩|[https://docs.microsoft.com/azure/security-center/security-center-get-started](https://docs.microsoft.com/azure/security-center/security-center-get-started)|
|

### <a name="traffic-analytics"></a>트래픽 분석

트래픽 분석는 Azure의 사용자 및 응용 프로그램 작업에 대 한 가시성을 제공 하는 클라우드 기반 솔루션입니다. 트래픽 분석 분석에서는 NSG 흐름 로그를 Network Watcher 하 여 Azure에서 트래픽 흐름에 대 한 통찰력을 제공 합니다. 트래픽 분석은 가상 네트워크에서 보이는 네트워크 트래픽과 관련 된 대시보드, 보고서, 분석 및 이벤트 응답 기능을 제공 하는 데 사용 됩니다. 트래픽 분석는 사이버 보안 인시던트를 식별 하 고 해결 하는 데 도움이 되는 중요 한 통찰력을 제공 합니다.

|리소스|링크|
|---|---|
|트래픽 분석 설명서|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)|
|

### <a name="azure-advisor"></a>Azure Advisor

리소스 구성 및 기타 데이터를 분석 하 여 전체 Azure 비용을 줄일 수 있는 기회를 찾는 동안 리소스의 성능, 보안 및 고가용성을 향상 시키는 데 도움이 되는 솔루션을 권장 합니다. Azure Advisor Azure Advisor은 ACSC에서 권장 하며, Azure 환경의 구성에 대 한 액세스 및 자세한 조언을 제공 합니다.

|리소스|링크|
|---|---|
|Azure 관리자 설명서|[https://docs.microsoft.com/azure/advisor](https://docs.microsoft.com/azure/advisor)|
|Azure Adviser 시작|[https://docs.microsoft.com/azure/advisor/advisor-get-started](https://docs.microsoft.com/azure/advisor/advisor-get-started)|
|

### <a name="dns-analytics-preview"></a>DNS 분석(미리 보기)

DNS 분석는 Windows DNS 분석 및 감사 로그 및 기타 관련 데이터를 수집, 분석 및 연관 시키는 Log Analytics 솔루션입니다. DNS 분석 악의적인 도메인 이름, 오래 된 리소스 레코드, 자주 쿼리 되는 도메인 이름 및 talkative DNS 클라이언트를 확인 하려고 시도 하는 클라이언트를 식별 합니다. 또한 DNS 분석는 DNS 서버에 대 한 요청 로드 및 동적 DNS 등록 오류에 대 한 통찰력을 제공 합니다. DNS 분석는 Azure 환경 내에서 수행 된 DNS 쿼리와 관련 된 대시보드, 보고서, 분석 및 이벤트 응답 기능을 제공 하는 데 사용 됩니다. DNS 분석는 사이버 보안 인시던트를 식별 하 고 해결 하는 데 도움이 되는 중요 한 통찰력을 제공 합니다.

|리소스|링크|
|---|---|
|DNS 분석 설명서|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)|
|

### <a name="activity-log-analytics"></a>Activity Log Analytics

활동 로그 분석는 여러 Azure 구독에서 Azure 활동 로그를 분석 하 고 검색 하는 데 도움이 되는 Log Analytics 솔루션입니다. 활동 로그 분석는 전체 Azure 환경에서 리소스에 대해 수행 되는 작업과 관련 된 centralised 대시보드, 보고서, 분석 및 이벤트 응답 기능을 제공 하는 데 사용 됩니다. 활동 로그 분석 감사 및 조사를 지원할 수 있습니다.

|리소스|링크|
|---|---|
|Log Analytics에서 Azure 활동 로그 수집 및 분석|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)|
|

### <a name="security-information-and-event-management-siem"></a>SIEM (보안 정보 및 이벤트 관리)

SIEM은 centralised 저장소, 보안 로그 감사 및 분석을 위한 정의 된 메커니즘과 분석, 보고 및 인시던트 검색 및 대응을 위한 지능형 도구를 제공 하는 시스템입니다. Azure에서 기본적으로 제공 하는 보안 기능을 보완 하기 위해 Azure 로깅 정보를 포함 하는 SIEM 기능을 사용할 수 있습니다. 사내 엔터티는 특정 요구 사항에 따라 Azure, 온-프레미스 또는 SaaS (Software as a Service)의 Virtual Machines에서 호스트 되는 SIEM을 제품 수 있습니다.

|리소스|링크|
|---|---|
|Azure 센티널 (미리 보기)|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|SIEM 설명서|SIEM 아키텍처 및 지침에 대해서는 공급 업체 설명서를 참조 하세요.|
|Azure Monitor를 사용 하 여 SIEM 도구와 통합|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>오스트레일리아 사이버 보안 센터

ACSC (오스트레일리아 사이버 Security Center)는 미국 사이버 보안을 위한 오스트레일리아 정부의 리드입니다. 오스트레일리아 정부의 사이버 보안 기능을 함께 사용 하 여 오스트레일리아 커뮤니티의 사이버 복원 력을 높이고 디지털 시대의 오스트레일리아 및 사회 prosperity를 지원 합니다. ACSC는 모든 시스템 생성 로그 파일, 이벤트 및 로그를 오스트레일리아 정부 모니터링 전체의 ACSC로 전달 하는 것을 권장 합니다.

|리소스|링크|
|---|---|
|오스트레일리아 사이버 보안 센터 웹 사이트|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>사고 대응

적절 한 로그를 생성 하 고 centralised 리포지토리에 수집 하 고 분석을 수행 하면 시스템에 대 한 이해가 늘어나고 사이버 보안 인시던트를 검색 하는 메커니즘이 제공 됩니다. 인시던트 또는 이벤트가 검색 된 후 다음 단계는 이러한 이벤트에 대응 하 고 시스템 상태를 유지 관리 하 고 서비스 및 데이터의 손상 으로부터 보호 하는 작업을 수행 하는 것입니다. Azure는 발생 하는 모든 이벤트에 효과적으로 응답 하는 서비스 조합을 제공 합니다.

### <a name="azure-alerts"></a>Azure 경고

Azure 경고는 특정 이벤트에 대 한 응답으로 지원 및 보안 담당자에 게 알리는 데 사용할 수 있습니다. 이를 통해이 문서에 나열 된 analysis services에 의해 발생 하는 관련 이벤트를 검색 하는 데 사용 됩니다.

|리소스|링크|
|---|---|
|Microsoft Azure 경고 개요|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)|
|Azure Security Center에서 보안 경고 관리 및 대응|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)|
|Azure Monitor 경고로 이벤트에 응답|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)|
|

### <a name="azure-automation"></a>Azure Automation

Azure Automation를 사용 하 여 이벤트에 대 한 응답으로이 작업을 트리거할 수 있습니다. 이는 Virtual Machines에서 패킷 캡처를 시작 하거나, 워크플로를 실행 하거나, Virtual Machines 또는 서비스를 실행 하거나, 다른 작업을 시작할 수 있습니다. Automation은 수동 개입 없이 경고에 대 한 신속한 응답을 지원 하므로 인시던트 또는 이벤트의 응답 시간 및 심각도를 줄일 수 있습니다.

|리소스|링크|
|---|---|
|Azure Automation 설명서|[https://docs.microsoft.com/azure/automation](https://docs.microsoft.com/azure/automation)|
|방법 가이드: 경고를 사용하여 Azure Automation Runbook 트리거|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)|
|

## <a name="next-steps"></a>다음 단계

Azure에서 게이트웨이 환경을 안전 하 게 관리 하는 방법에 대 한 자세한 내용은 [게이트웨이 보안 원격 관리](gateway-secure-remote-administration.md) 의 문서를 검토 하세요.
