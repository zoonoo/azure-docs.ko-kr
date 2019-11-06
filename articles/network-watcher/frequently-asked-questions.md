---
title: Azure Network Watcher에 대 한 FAQ (질문과 대답) | Microsoft Docs
description: 이 문서에서는 Azure Network Watcher 서비스에 대 한 자주 묻는 질문에 답변 합니다.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: 3305590f2d8abf0d894bc1df42b84edcc96a2b2d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598231"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Azure Network Watcher에 대 한 FAQ (질문과 대답)
[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 서비스는 azure 가상 네트워크에서 리소스에 대 한 로그를 모니터링 하 고, 진단 하 고, 보고, 사용 하거나 사용 하지 않도록 설정 하는 도구 모음을 제공 합니다. 이 문서에서는 서비스에 대 한 일반적인 질문에 답변 합니다.

## <a name="general"></a>일반

### <a name="what-is-network-watcher"></a>Network Watcher 이란?
Network Watcher는 Azure virtual network의 Virtual Machines, 가상 네트워크, 응용 프로그램 게이트웨이, 부하 분산 장치 및 기타 리소스를 포함 하는 IaaS (Infrastructure as a Service) 구성 요소의 네트워크 상태를 모니터링 하 고 복구 하도록 설계 되었습니다. PaaS (Platform as a Service) 인프라를 모니터링 하거나 웹/모바일 분석을 받기 위한 솔루션은 아닙니다.

### <a name="what-tools-does-network-watcher-provide"></a>Network Watcher에서 제공 하는 도구
Network Watcher는 세 가지 주요 기능 집합을 제공 합니다.
* 모니터링
  * [토폴로지 보기](https://docs.microsoft.com/azure/network-watcher/view-network-topology) 에는 가상 네트워크의 리소스와 이러한 리소스 간의 관계가 표시 됩니다.
  * [연결 모니터](https://docs.microsoft.com/azure/network-watcher/connection-monitor) 를 사용 하면 VM과 다른 네트워크 리소스 간의 연결 및 대기 시간을 모니터링할 수 있습니다.
  * [네트워크 성능 모니터](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) 를 사용 하면 하이브리드 네트워크 아키텍처, express 경로 회로 및 서비스/응용 프로그램 끝점에서 연결 및 대기 시간을 모니터링할 수 있습니다.  
* 진단
  * [IP 흐름 확인](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) 을 사용 하면 VM 수준에서 트래픽 필터링 문제를 검색할 수 있습니다.
  * [다음 홉](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) 은 트래픽 경로를 확인 하 고 라우팅 문제를 검색 하는 데 도움이 됩니다.
  * [연결 문제 해결](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) 을 사용 하면 VM과 다른 네트워크 리소스 간에 일회성 연결 및 대기 시간을 확인할 수 있습니다.
  * [패킷 캡처](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) 를 사용 하면 가상 네트워크의 VM에 대 한 모든 트래픽을 캡처할 수 있습니다.
  * [Vpn 문제 해결](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) 에서는 문제를 디버그 하는 데 도움이 되도록 vpn gateway 및 연결에 대 한 여러 진단 검사를 실행 합니다.
* 로깅
  * [Nsg 흐름 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) 를 사용 하 여 [Nsg (네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/security-overview) 의 모든 트래픽을 기록할 수 있습니다.
  * [트래픽 분석](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) 는 Nsg 흐름 로그 데이터를 처리 하 여 네트워크 트래픽을 시각화, 쿼리, 분석 및 이해할 수 있도록 합니다.


자세한 내용은 [Network Watcher 개요 페이지](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)를 참조 하세요.


### <a name="how-does-network-watcher-pricing-work"></a>Network Watcher 가격은 어떻게 작동 하나요?
Network Watcher 구성 요소 및 가격 책정에 대 한 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/network-watcher/) 를 방문 하세요.

### <a name="which-regions-is-network-watcher-available-in"></a>Network Watcher 사용할 수 있는 지역은 어디 인가요?
[Azure 서비스 가용성 페이지](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher) 에서 최신 지역의 가용성을 확인할 수 있습니다.

### <a name="what-are-resource-limits-on-network-watcher"></a>Network Watcher에 대 한 리소스 제한은 무엇 인가요?
모든 제한에 대해서는 [서비스 제한](https://docs.microsoft.com/azure/azure-subscription-service-limits#network-watcher-limits) 페이지를 참조 하세요.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>지역별 Network Watcher 인스턴스는 하나만 허용 되는 이유는 무엇 인가요?
Network Watcher 작동 하는 기능에 대 한 구독에 대해 한 번만 사용 하도록 설정 해야 하는 것은 서비스 제한이 아닙니다.

## <a name="nsg-flow-logs"></a>NSG 흐름 로그

### <a name="what-does-nsg-flow-logs-do"></a>NSG 흐름 로그는 어떻게 되나요?
Azure 네트워크 리소스는 [NSGs (네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/security-overview)를 통해 결합 및 관리할 수 있습니다. NSG 흐름 로그를 사용 하 여 Nsg를 통한 모든 트래픽에 대 한 5 튜플 흐름 정보를 기록할 수 있습니다. 원시 흐름 로그는 필요에 따라 추가로 처리, 분석, 쿼리 또는 내보낼 수 있는 Azure Storage 계정에 기록 됩니다.

### <a name="are-there-any-caveats-to-using-nsg-flow-logs"></a>NSG 흐름 로그를 사용 하는 데 주의할 점이 있나요?
NSG 흐름 로그를 사용 하기 위한 필수 구성 요소는 없습니다. 그러나 두 가지 제한 사항이 있습니다.
- **서비스 끝점이 VNET에 없어야 함**: Nsg 흐름 로그는 vm의 에이전트에서 저장소 계정으로 내보내집니다. 그러나 현재는 저장소 계정에 직접 로그를 내보낼 수 있으며 VNET에 추가 된 서비스 끝점을 사용할 수 없습니다.

- **저장소 계정은 방화벽이 사용 하지 않아야 합니다**. 내부 제한 사항으로 인해 저장소 계정은 Nsg 흐름 로그를 사용 하 여 작업할 수 있도록 공용 인터넷을 통해 액세스할 수 있어야 합니다. 트래픽은 Azure를 통해 내부적으로 계속 라우팅됩니다. 추가 송신 요금이 발생 하지 않습니다.

이러한 문제를 해결 하는 방법에 대 한 지침은 다음 두 질문을 참조 하세요. 이러한 제한 사항은 1 월 2020 일에 해결 될 것으로 예상 됩니다.

### <a name="how-do-i-use-nsg-flow-logs-with-service-endpoints"></a>서비스 엔드포인트를 사용 하 여 NSG 흐름 로그를 사용 어떻게 할까요??

*옵션 1: VNET 끝점이 없는 Azure Storage 계정으로 내보내기 위해 NSG 흐름 로그 다시 구성*

* 엔드포인트를 사용하여 서브넷 찾기:

    - Azure Portal의 맨 위에 있는 전역 검색에서 **리소스 그룹**을 검색합니다.
    - 작업 중인 NSG를 포함하는 리소스 그룹으로 이동합니다.
    - 두 번째 드롭다운을 사용 하 여 유형별로 필터링 하 고 **가상 네트워크** 를 선택 합니다.
    - 서비스 엔드포인트를 포함한 가상 네트워크를 클릭합니다.
    - 왼쪽 창에서 **설정** 아래에 있는 **서비스 엔드포인트**를 선택합니다.
    - **Microsoft.Storage**를 사용하도록 설정된 서브넷을 기록해 둡니다.

* 서비스 끝점 사용 안 함:

    - 위에서 계속하여 왼쪽 창의 **설정** 아래에서 **서브넷**을 선택합니다.
    * 서비스 엔드포인트가 포함된 서브넷을 클릭합니다.
    - **서비스 엔드포인트** 섹션의 **서비스**에서 **Microsoft.Storage**를 선택 취소합니다.

몇 분 후에 스토리지 로그를 확인할 수 있습니다. 그러면 업데이트된 타임스탬프 또는 새로 만든 JSON 파일이 표시됩니다.

*옵션 2: NSG 흐름 로그 사용 안 함*

Microsoft.Storage 서비스 엔드포인트가 꼭 있어야 하는 경우 NSG 흐름 로그를 사용하지 않도록 설정해야 합니다.

### <a name="how-do-i-disable-the--firewall-on-my-storage-account"></a>저장소 계정에서 방화벽을 사용 하지 않도록 설정 어떻게 할까요??

이 문제는 "모든 네트워크"에서 저장소 계정에 액세스할 수 있도록 설정 하 여 해결 됩니다.

* [NSG 흐름 로그 개요 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)에서 NSG를 찾아 스토리지 계정의 이름을 찾습니다.
* 포털의 전역 검색에서 스토리지 계정 이름을 입력하여 스토리지 계정으로 이동합니다.
* **설정** 섹션 아래에서 **방화벽 및 가상 네트워크**를 선택합니다.
* **모든 네트워크**를 선택하고 저장합니다. 이미 선택되어 있는 경우에는 변경할 필요가 없습니다.  

### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>흐름 로그 버전 1 & 2의 차이점은 무엇 인가요?
흐름 로그 버전 2에는 전송 되는 바이트와 패킷에 대 한 정보를 저장 하 & *흐름 상태의* 개념이 도입 되었습니다. [자세히 알아보기](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>다음 단계
 - Network Watcher 시작 하는 데 도움이 되는 몇 가지 자습서는 [설명서 개요 페이지](https://docs.microsoft.com/azure/network-watcher/) 를 참조 하세요.
