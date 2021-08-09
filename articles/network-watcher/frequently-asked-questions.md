---
title: Azure Network Watcher에 대한 FAQ(자주 묻는 질문) | Microsoft Docs
description: 이 문서에서는 Azure Network Watcher 서비스에 대한 자주 묻는 질문과 대답을 제공합니다.
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
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: e7585880b98f62f819ff344c82846c2cfb1fd620
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019825"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Azure Network Watcher에 대한 FAQ(자주 묻는 질문)
[Azure Network Watcher](./network-watcher-monitoring-overview.md) 서비스는 Azure 가상 네트워크의 리소스에 대한 모니터링, 진단, 메트릭 보기, 로그 활성화 또는 비활성화를 위한 도구 모음을 제공합니다. 이 문서에서는 서비스에 대한 일반적인 질문과 대답을 제공합니다.

## <a name="general"></a>일반

### <a name="what-is-network-watcher"></a>Network Watcher란?
Network Watcher는 Azure 가상 네트워크의 가상 머신, 가상 네트워크, 애플리케이션 게이트웨이, 부하 분산 장치, 기타 리소스를 포함하는 IaaS(Infrastructure as a Service) 구성 요소의 네트워크 상태를 모니터링하고 복구하도록 설계되었습니다. PaaS(Platform as a Service) 인프라를 모니터링하거나 웹/모바일 분석을 위한 솔루션은 아닙니다.

### <a name="what-tools-does-network-watcher-provide"></a>Network Watcher에서 제공하는 도구는 무엇인가요?
Network Watcher는 세 가지 주요 기능 집합을 제공합니다.
* 모니터링
  * [토폴로지 보기](./view-network-topology.md)는 가상 네트워크의 리소스와 이들 간의 관계를 표시합니다.
  * [연결 모니터](./connection-monitor.md)를 사용하면 VM과 다른 네트워크 리소스 간의 연결 및 대기 시간을 모니터링할 수 있습니다.
  * [네트워크 성능 모니터](../azure-monitor/insights/network-performance-monitor.md)를 사용하면 하이브리드 네트워크 아키텍처, Expressroute 회로 및 서비스/애플리케이션 엔드포인트에서 연결 및 대기 시간을 모니터링할 수 있습니다.  
* 진단
  * [IP 흐름 확인](./network-watcher-ip-flow-verify-overview.md)을 사용하면 VM 수준에서 트래픽 필터링 문제를 탐지할 수 있습니다.
  * [다음 홉](./network-watcher-next-hop-overview.md)은 트래픽 경로를 확인하고 라우팅 문제를 탐지하는 데 도움이 됩니다.
  * [연결 문제 해결](./network-watcher-connectivity-portal.md)을 사용하면 VM과 다른 네트워크 리소스 간의 일회성 연결 및 대기 시간을 확인할 수 있습니다.
  * [패킷 캡처](./network-watcher-packet-capture-overview.md)를 사용하면 가상 네트워크의 VM에 대한 모든 트래픽을 캡처할 수 있습니다.
  * [VPN 문제 해결](./network-watcher-troubleshoot-overview.md)에서는 문제를 디버깅하는 데 도움이 되도록 VPN 게이트웨이 및 연결에 대한 여러 진단 검사를 실행합니다.
* 로깅
  * [NSG 흐름 로그](./network-watcher-nsg-flow-logging-overview.md)를 사용하면 [NSG(네트워크 보안 그룹)](../virtual-network/network-security-groups-overview.md)의 모든 트래픽을 기록할 수 있습니다.
  * [트래픽 분석](./traffic-analytics.md)은 NSG 흐름 로그 데이터를 처리하므로 네트워크 트래픽을 시각화하고 쿼리, 분석하여 이해할 수 있습니다.


자세한 내용은 [Network Watcher 개요 페이지](./network-watcher-monitoring-overview.md)를 참조하세요.


### <a name="how-does-network-watcher-pricing-work"></a>Network Watcher 가격 책정은 어떻게 작동하나요?
Network Watcher 구성 요소 및 가격 책정에 대한 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/network-watcher/)를 방문하세요.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Network Watcher는 어느 지역에서 지원/사용 가능한가요?
[Azure 서비스 가용성 페이지](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)에서 최신 지역의 가용성을 확인할 수 있습니다.

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Network Watcher를 사용하는 데 필요한 권한은 무엇인가요?
[Network Watcher를 사용하는 데 필요한 Azure RBAC 권한](./required-rbac-permissions.md) 목록을 참조하세요. 리소스를 배포하는 경우 NetworkWatcherRG에 대한 기여자 권한이 필요합니다(아래 참조).

### <a name="how-do-i-enable-network-watcher"></a>Network Watcher를 사용하도록 설정하려면 어떻게 해야 하나요?
Network Watcher 서비스는 모든 구독에 대해 [자동으로 사용하도록 설정](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/)됩니다.

### <a name="what-is-the-network-watcher-deployment-model"></a>Network Watcher 배포 모델은 무엇인가요?
Network Watcher 부모 리소스는 모든 지역에 고유한 인스턴스를 사용하여 배포됩니다. 명명 형식: NetworkWatcher_RegionName. 예제: NetworkWatcher_centralus는 “미국 중부” 지역에 대한 Network Watcher 리소스입니다.

### <a name="what-is-the-networkwatcherrg"></a>NetworkWatcherRG란 무엇인가요?
Network Watcher 리소스는 자동으로 생성되는 숨겨진 **NetworkWatcherRG** 리소스 그룹에 있습니다. 예를 들어 NSG 흐름 로그 리소스는 Network Watcher의 자식 리소스이며 NetworkWatcherRG에서 사용하도록 설정됩니다.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Network Watcher 확장을 설치해야 하는 이유는 무엇인가요? 
Network Watcher 확장은 VM에서 트래픽을 생성하거나 가로채는 데 필요한 모든 기능에 필요합니다. 

### <a name="which-features-require-the-network-watcher-extension"></a>Network Watcher 확장이 필요한 기능은 무엇인가요?
패킷 캡처, 연결 문제 해결, 연결 모니터 기능을 사용하려면 Network Watcher 확장이 필요합니다.

### <a name="what-are-resource-limits-on-network-watcher"></a>Network Watcher에 대한 리소스 제한은 무엇인가요?
모든 제한에 대해서는 [서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#network-watcher-limits) 페이지를 참조하세요.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>지역별 Network Watcher 인스턴스가 하나만 허용되는 이유는 무엇인가요? 
Network Watcher의 기능을 작동하려면 구독에 대해 한 번만 사용하도록 설정해야 하며, 이는 서비스 제한이 아닙니다.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Network Watcher 리소스를 관리하려면 어떻게 해야 하나요? 
Network Watcher 리소스는 Network Watcher에 대한 백 엔드 서비스를 나타내며 Azure를 통해 완전히 관리됩니다. 고객은 관리할 필요가 없습니다. 이동 등의 작업은 리소스에서 지원되지 않습니다. 그러나 [리소스를 삭제할 수 있습니다](./network-watcher-create.md#delete-a-network-watcher-in-the-portal). 

## <a name="service-availability-and-redundancy"></a>서비스 가용성 및 중복성 

### <a name="is-the-network-watcher-service-zone-resilient"></a>Network Watcher 서비스는 영역 복원력이 있나요? 
예. Network Watcher 서비스는 기본적으로 영역 복원력이 있습니다. 

### <a name="how-do-i-configure-the-network-watcher-service-to-be-zone-resilient"></a>영역 복원력을 갖도록 Network Watcher 서비스를 구성하려면 어떻게 해야 하나요? 
영역 복원력을 사용하도록 설정하기 위한 고객 구성은 필요 없습니다. Network Watcher 리소스의 영역 복원력은 기본적으로 제공되며 서비스 자체에서 관리합니다. 

## <a name="nsg-flow-logs"></a>NSG 흐름 로그

### <a name="what-does-nsg-flow-logs-do"></a>NSG 흐름 로그는 무엇을 하나요?
[NSG(네트워크 보안 그룹)](../virtual-network/network-security-groups-overview.md)를 통해 Azure 네트워크 리소스를 결합하고 관리할 수 있습니다. NSG 흐름 로그를 사용하면 NSG를 통해 모든 트래픽에 대한 5-튜플 흐름 정보를 기록할 수 있습니다. 원시 흐름 로그는 필요에 따라 추가로 처리, 분석, 쿼리 또는 내보낼 수 있는 Azure Storage 계정에 기록됩니다.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>방화벽이 있는 스토리지 계정에서 NSG 흐름 로그를 사용하려면 어떻게 해야 하나요?

방화벽이 있는 스토리지 계정을 사용하려면 스토리지 계정에 액세스하기 위해 신뢰할 수 있는 Microsoft 서비스에 대한 예외 설정을 해야 합니다.

* 포털의 전체 검색란 또는 [스토리지 계정 페이지](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)에 스토리지 계정 이름을 입력하여 스토리지 계정으로 이동합니다.
* **설정** 섹션 아래에서 **방화벽 및 가상 네트워크** 를 선택합니다.
* “다음의 액세스를 허용”에서 **선택한 네트워크** 를 선택합니다. 그런 다음, **예외** 에서 **“신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용합니다”** 옆의 확인란을 선택합니다. 
* 이미 선택되어 있는 경우에는 변경할 필요가 없습니다.  
* [NSG 흐름 로그 개요 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)에서 대상 NSG를 찾고, 위의 스토리지 계정을 선택하여 NSG 흐름 로그를 사용하도록 설정합니다.

몇 분 후에 스토리지 로그를 확인할 수 있습니다. 그러면 업데이트된 타임스탬프 또는 새로 만든 JSON 파일이 표시됩니다.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>서비스 엔드포인트가 있는 스토리지 계정으로 NSG 흐름 로그를 사용하려면 어떻게 해야 하나요?

NSG 흐름 로그는 별도의 추가 구성 없이 서비스 엔드포인트와 호환됩니다. 가상 네트워크에서 [서비스 엔드포인트를 사용하도록 설정하는 방법에 대한 자습서](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint)를 참조하세요.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>흐름 로그 버전 1과 버전 2의 차이는 무엇인가요?
흐름 로그 버전 2에서는 *흐름 상태* 개념이 추가되었고 전송되는 바이트와 패킷 정보를 저장합니다. [자세히 알아봅니다](./network-watcher-nsg-flow-logging-overview.md#log-format).

## <a name="next-steps"></a>다음 단계
 - Network Watcher를 시작하기 위한 몇 가지 자습서는 [설명서 개요 페이지](./index.yml)를 참조하세요.