---
title: "Azure의 네트워크 성능 모니터 | Microsoft Docs"
description: "Azure의 네트워크 성능 모니터를 사용하면 네트워크 성능을 거의 실시간으로 모니터링하여 네트워크 성능 병목을 감지하고 찾을 수 있습니다."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 399fe552d5c7d9a96cdabc2a1dfafe99635d4a61
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Azure의 네트워크 성능 모니터 솔루션

![네트워크 성능 모니터 기호](./media/log-analytics-network-performance-monitor/npm-symbol.png)


NPM(네트워크 성능 모니터)은 네트워크 인프라의 다양한 지점 간 네트워크 성능을 모니터링하고, 서비스/응용 프로그램 끝점에 대한 네트워크 연결을 모니터링하고, Azure ExpressRoute의 성능을 모니터링하는 데 도움이 되는 클라우드 기반 하이브리드 네트워크 모니터링 솔루션입니다.  

NPM은 트래픽 블랙홀, 라우팅 오류 등의 네트워크 문제와 기존 네트워크 모니터링 방법으로 감지할 수 없는 문제를 감지합니다. 이 솔루션은 경고를 생성하고, 네트워크 링크에 대한 임계값이 위반될 때 알리고, 네트워크 성능 모니터는 네트워크 성능 문제를 빠르게 감지하고, 문제의 소스를 특정 네트워크 세그먼트 또는 장치로 지역화합니다. 

NPM는 다음과 같은 세 가지 광범위한 기능을 제공합니다. 

[성능 모니터](log-analytics-network-performance-monitor-performance-monitor.md): 클라우드 배포 및 온-프레미스 위치, 여러 데이터 센터, 지점, 업무에 중요한 다중 계층 응용 프로그램/마이크로 서비스 간의 네트워크 연결을 모니터링합니다. 성능 모니터를 사용하여 사용자가 불만을 제기하기 전에 네트워크 문제를 감지할 수 있습니다.  

[서비스 끝점 모니터](log-analytics-network-performance-monitor-service-endpoint.md): 사용자로부터 관심 있는 서비스로의 연결을 모니터링하고, 경로에 있는 인프라 및 네트워크 병목 현상이 발생하는 위치를 확인할 수 있습니다. 사용자보다 먼저 중단에 대해 알고, 네트워크 경로에서 문제의 정확한 위치를 확인합니다. 

이 기능은 http, HTTPS, TCP 및 ICMP 기반 테스트를 수행하여 거의 실시간으로 또는 시간별로 서비스 가용성 및 응답 시간과 네트워크 패킷 손실 및 대기 시간의 원인을 모니터링할 수 있습니다. 네트워크 토폴로지 맵을 통해 노드에서 서비스까지 네트워크 경로에서 발생하는 문제 지점을 확인하여 각 홉의 대기 시간 데이터와 함께 네트워크 속도 저하를 파악할 수 있습니다. 기본 제공 테스트를 통해 사전 구성할 필요 없이 Office365, Dynamics CRM 및 Azure 서비스에 대한 연결을 모니터링하세요. 이 기능을 사용하여 웹 사이트, SaaS, PaaS 응용 프로그램, SQL Database 등과 같은 TCP 지원 끝점으로의 네트워크 연결을 모니터링할 수 있습니다.  

[ExpressRoute 모니터링](log-analytics-network-performance-monitor-expressroute.md): Azure ExpressRoute를 통해 지사와 Azure 간의 끝점 간 연결 및 성능을 모니터링합니다.  
 

## <a name="set-up-and-configure"></a>설정 및 구성

### <a name="install-and-configure-agents"></a>에이전트 설치 및 구성 

[Log Analytics에 Windows 컴퓨터 연결](log-analytics-windows-agents.md) 및 [Log Analytics에 Operations Manager 연결](log-analytics-om-agents.md)에 나와 있는 기본 프로세스에 따라 에이전트를 설치합니다.

### <a name="where-to-install-the-agents"></a>에이전트 설치 위치 

**성능 모니터:** 다른 하위 네트워크에 대한 네트워크 연결을 모니터링하려는 각 하위 네트워크에 연결된 하나 이상의 노드에 OMS 에이전트를 설치합니다.  

네트워크 링크를 모니터링하려면 해당 링크의 두 끝점에 에이전트를 설치해야 합니다.  네트워크 토폴로지를 확실히 모를 경우 사이의 네트워크 성능을 모니터링하려는 중요 워크로드가 있는 서버에 에이전트를 설치합니다. 예를 들어, 웹 서버와 SQL을 실행하는 서버 간의 네트워크 연결을 모니터링하려는 경우 두 서버 모두에 에이전트를 설치합니다. 에이전트는 호스트 자체가 아닌 호스트 간의 네트워크 연결(링크)을 모니터링합니다. 

**서비스 끝점 모니터:** 서비스 끝점에 대한 네트워크 연결을 모니터링하려는 각 노드에 OMS 에이전트를 설치합니다. 예를 들어, 사무실 사이트 O1, O2 및 O3에서 Office 365로의 네트워크 연결을 모니터링하려는 경우 O1, O2, 및 O3 각각에 있는 하나 이상의 노드에 OMS 에이전트에서 설치합니다. 

**ExpressRoute 모니터:** Azure VNET과 ExpressRoute 개인 피어링을 통해 연결된 온-프레미스 하위 네트워크에 있는 하나 이상의 에이전트에 OMS 에이전트를 설치합니다.  

### <a name="configure-oms-agents-for-monitoring"></a>모니터링을 위해 OMS 에이전트 구성  

NPM은 가상 트랜잭션을 사용하여 원본 및 대상 에이전트 간의 네트워크 성능을 모니터링합니다. ExpressRoute 모니터에는 TCP가 사용되지만, 이 솔루션에서는 성능 모니터 및 서비스 끝점 모니터 기능에서 모니터링하기 위한 프로토콜로 TCP 또는 ICMP 중에서 선택할 수 있습니다. 방화벽이 모니터링을 위해 선택한 프로토콜에서 모니터링하는 데 사용되는 OMS 에이전트 간의 통신을 허용하는지 확인합니다.  

**TCP 프로토콜:** 모니터링을 위한 프로토콜로 TCP를 선택했으면 성능 모니터 및 ExpressRoute 모니터 기능에 사용되는 에이전트에서 방화벽 포트를 열어 에이전트가 서로 연결될 수 있는지 확인합니다. 이렇게 하려면 PowerShell 창에서 관리자 권한으로 EnableRules.ps1 PowerShell 스크립트를 매개 변수 없이 실행합니다.  

이 스크립트는 솔루션에 필요한 레지스트리 키를 만들며 에이전트가 상호 TCP 연결을 만들 수 있도록 하는 Windows 방화벽 규칙을 만듭니다. 또한 이 스크립트로 생성된 레지스트리 키는 디버그 로그와 로그 파일의 패스를 로깅해야 하는지 여부를 지정하며 통신에 사용되는 에이전트 TCP 포트도 정의합니다. 이러한 키 값은 스크립트에서 자동으로 설정하므로 해당 키를 수동으로 변경하지 않아야 합니다. 기본적으로 열리는 포트는 8084입니다. 스크립트에 매개 변수 portNumber를 지정하여 사용자 지정 포트를 사용할 수 있습니다. 하지만 스크립트가 실행되는 모든 컴퓨터에서 동일한 포트를 사용해야 합니다. 

>[!NOTE]
> 스크립트는 Windows 방화벽을 로컬로만 구성합니다. 네트워크 방화벽이 있는 경우 NPM에서 사용하는 TCP 포트로 트래픽이 전송될 수 있도록 해야 합니다. 

>[!NOTE]
> 서비스 끝점 모니터에 대해 EnableRules.ps1 PowerShell 스크립트를 실행할 필요가 없습니다. 

 

**ICMP 프로토콜** - 모니터링을 위한 프로토콜로 ICMP를 선택한 경우 ICMP를 안정적으로 활용하기 위해 다음과 같은 방화벽 규칙을 사용하도록 설정합니다. 

 
```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```
 

### <a name="configure-the-solution"></a>솔루션 구성 

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)에서 또는 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에 설명된 절차에 따라 작업 영역에 네트워크 성능 모니터 솔루션을 추가합니다. 
2. Log Analytics 작업 영역을 열고 **개요** 타일을 클릭합니다  
3.  *솔루션에 추가 구성이 필요합니다.*라는 메시지가 포함된 **네트워크 성능 모니터**  타일이 새로 추가된 것을 확인할 수 있습니다.

    ![NPM 타일](media/log-analytics-network-performance-monitor/npm-config.png)

3. **설치** 페이지에 OMS 에이전트를 설치하고 **일반 설정** 보기에서 모니터링하기 위해 에이전트를 구성하는 옵션이 제공됩니다. 위에서 설명한 것처럼 OMS 에이전트를 이미 설치하고 구성한 경우, 사용하려는 기능을 구성하기 위한 **설정** 보기를 클릭합니다.  

    **성능 모니터 보기** - 기본 성능 모니터 규칙에서 가상 트랜잭션에 사용할 프로토콜을 선택하고 저장 후 계속을 클릭합니다. 이 프로토콜 선택 항목은 시스템에서 생성된 기본 규칙에 대해서만 유지되므로, 성능 모니터 규칙을 명시적으로 만들 때마다 프로토콜을 선택해야 합니다. 항상 성능 모니터 탭에서 기본 규칙 설정(당일 구성을 완료한 후 표시)으로 이동하고 나중에 프로토콜을 변경할 수 있습니다. 성능 모니터 기능을 원하지 않는 경우 성능 모니터 탭의 기본 규칙 설정에서 기본 규칙을 사용하지 않도록 설정할 수 있습니다. 

    ![NPM 구성](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
    **서비스 끝점 모니터 보기** - 이 기능은 에이전트에서 Office 365 및 Dynamics 365로의 네트워크 연결을 모니터링하기 위한 기본 제공 사전 구성 테스트를 제공합니다. 옆에 있는 확인란을 선택하여 모니터링하려는 Office 365 및 Dynamics 365 서비스를 선택합니다. 에이전트 추가 단추를 클릭하여 모니터링를 수행하려는 에이전트를 선택합니다. 이 기능을 사용하지 않거나 나중에 설정하려면 이 단계를 건너뛰고 아무 내용도 선택하지 않은 상태로 **저장** 후 **계속**을 바로 클릭할 수 있습니다.  

    ![NPM 구성](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

    **ExpressRoute 모니터 보기** - **검색 지금** 단추를 클릭하여 이 Log Analytics 작업 영역에 연결된 Azure 구독의 VNET에 연결되어 있는 모든 ExpressRoute 개인 피어링을 검색합니다.  


    >[!NOTE] 
    > 이 솔루션은 현재, ExpressRoute 개인 피어링만 검색합니다. 

    >[!NOTE] 
    > 이 Log Analytics 작업 영역에 연결된 구독 관련 VNET에 연결되어 있는 개인 피어링만 검색됩니다. ExpressRoute가 이 작업 영역에 연결된 구독 이외의 VNET에 연결된 경우 해당 구독에 Log Analytics 작업 영역을 만들고 NPM을 사용하여 해당 피어링을 모니터링해야 합니다. 

    ![NPM 구성](media/log-analytics-network-performance-monitor/npm-express-route.png)

    검색이 완료되면 검색된 개인 피어링이 표에 나열됩니다.  

    ![NPM 구성](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
    이러한 피어링에 대한 모니터링은 처음에는 사용할 수 없는 상태입니다. 모니터링하려는 각 피어링을 클릭하고 RHS(오른쪽) 세부 정보 보기에서 해당 작업에 대한 모니터링을 구성합니다.  저장 단추를 클릭하여 구성을 저장합니다. 자세한 내용은 [ExpressRoute 모니터링 구성]()을 참조하세요.  

    설치가 완료되면 데이터를 채우는 데 30분~1시간이 소요됩니다. 이 솔루션이 네트워크에서 데이터를 집계하는 동안 NPM 개요 타일에 *솔루션에 추가 구성이 필요합니다.*가 표시됩니다. 데이터가 수집되고 인덱싱되면 개요 타일이 변경되고, 네트워크의 상태 요약이 표시됩니다. 그런 후 OMS 에이전트가 설치된 노드와 작업 환경에서 검색된 서브넷의 모니터링을 편집하도록 선택할 수 있습니다. 

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>서브넷 및 노드에 대한 모니터링 설정 편집 

하나 이상의 에이전트가 설치된 모든 서브넷은 구성 페이지의 서브네트워크 탭에 나열됩니다. 


특정 서브네트워크의 모니터링을 사용하거나 사용하지 않도록 설정하려면 

1.  **서브네트워크 ID**  옆의 확인란을 선택 또는 선택 취소한 다음  **모니터링에 사용** 이 적절히 선택 또는 선택 취소되었는지 확인합니다. 여러 서브넷을 선택 또는 선택 취소할 수 있습니다. 이 설정을 사용하지 않도록 설정할 경우 에이전트가 업데이트되면서 다른 에이전트에 대한 ping을 중지하므로 서브네트워크가 모니터링되지 않습니다. 
2. 목록에서 서브네트워크를 선택하고 모니터링되지 않는 노드와 모니터링되는 노드가 포함된 목록 사이에서 필요한 노드를 이동하여 특정 서브네트워크에 대해 모니터링하려는 노드를 선택합니다. 서브네트워크에 **사용자 지정 설명**을 추가할 수 있습니다. 
3. 구성을 저장하려면 **Save**를 클릭합니다. 

#### <a name="choose-nodes-to-monitor"></a>모니터링할 노드 선택

에이전트가 설치된 모든 노드는 **Nodes** 탭에 나열됩니다. 

1. 모니터링하거나 모니터링을 중지하려는 노드를 선택 또는 선택 취소합니다. 
2.  **모니터링에 사용**을 적절히 클릭하거나 선택 취소합니다. 
3. **저장**을 클릭합니다. 


관심 있는 기능을 구성합니다. 
- [성능 모니터](log-analytics-network-performance-monitor-performance-monitor.md#configuration) 구성
- [서비스 끝점 모니터](log-analytics-network-performance-monitor-performance-monitor.md#configuration) 구성
- [ExpressRoute 모니터](log-analytics-network-performance-monitor-expressroute.md#configuration) 구성

 

## <a name="data-collection-details"></a>데이터 수집 세부 정보
네트워크 성능 모니터는 TCP를 선택한 경우 TCP SYN-SYNACK-ACK 핸드셰이크 패킷을 사용하고 ICMP를 선택한 경우 ICMP ECHO ICMP ECHO REPLY를 사용하여 손실 및 대기 시간 정보를 수집합니다. 토폴로지 정보를 가져오는 데도 경로 추적이 사용됩니다.

다음 표에는 데이터 수집 방법 및 네트워크 성능 모니터에 대한 데이터가 수집되는 방식에 대한 기타 세부 정보가 나와 있습니다.

| 플랫폼 | 직접 에이전트 | SCOM 에이전트 | Azure Storage | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP는 5초마다 핸드셰이크/ICMP ECHO 메시지를 전송하며 3분마다 데이터가 전송됩니다. |
 

 
이 솔루션은 가상 트랜잭션을 사용하여 네트워크 상태를 평가합니다. 네트워크 교환 TCP 패킷 또는 ICMP Echo(모니터링을 위해 선택한 프로토콜에 따라 다름)의 다양한 지점에 설치된 OMS 에이전트는 다른 에이전트와 통신합니다. 이 과정에서 에이전트는 왕복 시간과 패킷 손실(있는 경우)을 확인합니다. 또한 각 에이전트는 다른 에이전트에 대해 정기적으로 추적 경로를 수행하여 네트워크에서 테스트가 필요한 다양한 경로를 모두 찾아냅니다. 에이전트는 이 데이터를 사용하여 네트워크 대기 시간 및 패킷 손실 수치를 추론할 수 있습니다. 테스트는 5초마다 반복되며 에이전트는 이 데이터를 3분 기간마다 집계하여 Log Analytics 서비스에 업로드합니다. 



>[!NOTE]
> 에이전트가 서로 자주 통신하긴 하지만 테스트를 수행하는 동안 많은 네트워크 트래픽을 생성하지 않습니다. 에이전트는 TCP SYN-SYNACK-ACK 핸드셰이크에만 의존하여 손실과 대기 시간을 결정하며 데이터 패킷은 교환되지 않습니다. 이 프로세스에서 에이전트는 필요할 때에만 다른 에이전트와 통신하며 네트워크 트래픽을 줄이도록 에이전트 통신 토폴로지가 최적화됩니다.

## <a name="using-the-solution"></a>솔루션 사용 

### <a name="npm-overview-tile"></a>NPM 개요 타일 

네트워크 성능 모니터 솔루션을 사용하도록 설정하면 개요 페이지의 솔루션 타일에 네트워크 상태에 대한 간략한 개요가 표시됩니다. 

 ![NPM 개요 타일](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>네트워크 성능 모니터 대시보드 

**상위 네트워크 상태 이벤트** 페이지에는 시스템의 최근 상태 이벤트 및 경고의 목록과 이벤트가 활성화된 이후 경과된 시간이 표시됩니다. 모니터링 규칙에 대해 선택한 메트릭(손실, 대기 시간, 응답 시간 또는 대역폭 사용률) 값이 임계값을 초과할 때마다 상태 이벤트 또는 경고가 생성됩니다. 

 **성능 모니터** 페이지는 솔루션에서 모니터링되는 네트워크 링크 및 서브네트워크 링크의 상태 요약을 제공합니다. 토폴로지 타일은 네트워크에서 모니터링되는 네트워크 경로 수를 표시합니다. 이 타일을 클릭하여 토폴로지 보기로 직접 이동합니다. 

 **서비스 끝점 모니터** 페이지는 만든 다른 테스트의 상태 요약을 표시합니다. 토폴로지 타일에는 모니터링되는 끝점의 수가 표시됩니다. 이 타일을 클릭하여 토폴로지 보기로 직접 이동합니다.

 **ExpressRoute 모니터** 페이지는 솔루션에서 모니터링되는 다양한 ExpressRoute 피어링 연결의 상태 요약을 제공합니다. 토폴로지 타일은 네트워크에서 모니터링되는 ExpressRoute 회로를 통과하는 네트워크 경로 수를 표시합니다. 이 타일을 클릭하여 토폴로지 보기로 직접 이동합니다.

 **일반 쿼리** 페이지에는 원시 네트워크 모니터링 데이터를 직접 가져오는 검색 쿼리 집합이 포함되어 있습니다. 이러한 쿼리를 시작점으로 사용하여 사용자 지정 보고를 위한 쿼리를 만들 수 있습니다. 

![NPM 대시보드](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>상세하게 드릴다운 

솔루션 대시보드의 다양한 링크를 클릭하여 관심 영역을 상세하게 드릴다운할 수 있습니다. 예를 들어 대시보드에 경고 또는 비정상 네트워크 링크가 나타날 경우 해당 링크를 클릭하여 자세히 조사할 수 있습니다. 그러면 특정 네트워크 링크의 모든 서브네트워크 링크가 나열된 페이지로 이동합니다. 각 서브네트워크 링크의 손실, 대기 시간, 상태를 확인하고 문제의 원인이 된 서브네트워크 링크를 빠르게 찾을 수 있습니다. 그런 다음  **노드 링크 보기** 를 클릭하여 비정상 서브넷 링크의 모든 노드 링크를 볼 수 있습니다. 그런 다음 노드 간 개별 링크를 확인하고 비정상 노드 링크를 찾을 수 있습니다. 

원본-대상 노드 간 경로에서 각 홉 사이의 토폴로지를 보려면  **토폴로지 보기** 를 클릭합니다. 비정상 경로는 빨간색으로 표시되고, 각 홉의 영향을 받은 대기 시간을 확인할 수 있으므로 네트워크의 특정 부분에서 문제를 빠르게 식별할 수 있습니다. 

 

### <a name="network-state-recorder"></a>네트워크 상태 레코더 

각 보기에는 특정 시점에서 네트워크 상태에 대한 스냅숏이 표시됩니다. 기본적으로 가장 최근 상태가 표시됩니다. 페이지 맨 위에 있는 막대에 상태가 표시되는 시점이 표시됩니다. 시간을 뒤로 이동하도록 선택하거나 작업에서 막대를 클릭하여 네트워크 상태에 대한 스냅숏을 볼 수 있습니다. 또한 최신 상태를 보고 있는 동안 페이지 자동 새로 고침을 사용 또는 사용하지 않도록 설정할 수도 있습니다. 

 ![네트워크 상태 레코더](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>추세 차트 

드릴다운하는 각 수준에서 적용 가능한 메트릭(손실, 대기 시간, 응답 시간, 대역폭 사용률)의 추세를 볼 수 있습니다. 차트 상단에 있는 시간 컨트롤을 사용하여 추세의 시간 간격을 변경할 수 있습니다. 

추세 차트는 성능 메트릭의 성능에 대한 기존 관점을 보여줍니다. 일부 네트워크 문제는 일시적인 것이며 현재 네트워크 상태를 보는 것만으로 확인하기 어려울 수 있습니다. 그 이유는 문제가 빠르게 나타났다 누군가 알기 전에 사라지고 나중에 다시 나타날 수 있기 때문입니다. 이와 같은 일시적 문제는 모든 애플리케이션 구성 요소가 원활하게 실행되는 것처럼 보일 경우에도 애플리케이션 응답 시간이 알 수 없는 이유로 증가하는 문제로 나타나는 경우가 많기 때문에 애플리케이션 관리자도 확인이 어려울 수 있습니다. 

이러한 유형의 문제는 추세 차트에서 네트워크 대기 시간 또는 패킷 손실이 급증하는 부분을 찾아 쉽게 감지할 수 있습니다. 네트워크 상태 레코더를 사용하여 문제가 발생한 시점의 네트워크 스냅숏 및 토폴로지를 통해 문제를 조사할 수 있습니다. 

 
![추세 차트](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>토폴로지 맵 

NPM은 인터랙티브 토폴로지 맵에서 원본 및 대상 끝점 사이의 홉 단위 경로 토폴로지를 보여줍니다. 솔루션 대시보드에서 **토폴로지** 타일을 클릭하거나 드릴다운 페이지에서 **토폴로지 보기** 링크를 클릭하여 토폴로지 맵을 볼 수 있습니다.  

토폴로지 맵에는 원본 및 대상 간 경로 수와 데이터 패킷이 사용하는 경로가 표시됩니다. 각 네트워크 홉으로 인한 대기 시간도 표시됩니다. 전체 경로 대기 시간이 임계값(해당 모니터링 규칙에 설정)을 초과하는 모든 경로가 빨간색으로 표시됩니다.  

노드를 클릭하거나 토폴로지 맵에서 노드에 커서를 올려 놓으면 FQDN, IP 주소와 같은 노드 속성이 표시됩니다. 홉의 IP 주소를 보려면 해당 홉을 클릭합니다. 믄제가 있는 네트워크 홉은 해당 홉으로 인한 대기 시간을 통해 식별할 수 있습니다. 축소 가능한 작업 창에서 필터를 사용하여 특정 경로를 필터링하도록 선택할 수도 있습니다. 작업 창에 있는 슬라이더를 사용하여 중간 홉을 숨기는 방법으로 네트워크 토폴로지를 간소화할 수도 있습니다. 마우스 휠을 사용하여 토폴로지 맵을 확대 또는 축소할 수 있습니다. 

맵에 표시된 토폴로지는 계층 3 토폴로지이며 계층 2 장치와 연결이 포함되어 있지 않습니다. 

 
![토폴로지 맵](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Log Analytics 검색 

모든 데이터는 NPM 대시보드를 통해 그래픽으로 노출되며 [Log Analytics 검색](log-analytics-log-search-new.md) 시에도 드릴다운 페이지를 기본적으로 사용할 수 있습니다. 리포지토리의 데이터에 대해 대화형 분석을 수행하고, 서로 다른 원본의 데이터 간 상관 관계를 파악하고, 사용자 지정 경고를 만들고, 사용자 지정 보기를 만들고, Excel, PowerBI 또는 공유할 수 있는 링크로 데이터를 내보낼 수 있습니다. 대시보드의 일반 쿼리 영역에는 자신의 쿼리와 보고서를 만드는 데 사용할 수 있는 유용한 쿼리 몇 가지가 있습니다. 

 

## <a name="provide-feedback"></a>피드백 제공 

**UserVoice** - 사용하려는 네트워크 성능 모니터 기능에 대한 아이디어를 게시할 수 있습니다.  [UserVoice 페이지](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring)를 방문하세요. 

**코호트 조인**  - 코호트에 조인하는 새 고객을 항상 환영합니다. 그 일환으로 새로운 기능에 대한 초기 액세스 권한을 통해 네트워크 성능 모니터를 개선하는 데 참여하실 수 있습니다. 조인에 관심이 있을 경우 이  [빠른 설문 조사](https://aka.ms/npmcohort)에 참여하세요. 

## <a name="next-steps"></a>다음 단계 
- [성능 모니터](log-analytics-network-performance-monitor-performance-monitor.md), [서비스 끝점 모니터](log-analytics-network-performance-monitor-performance-monitor.md) 및 [ExpressRoute 모니터](log-analytics-network-performance-monitor-expressroute.md)에 대해 알아봅니다. 
