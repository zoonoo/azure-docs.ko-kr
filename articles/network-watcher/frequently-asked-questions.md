---
title: Azure 네트워크 감시자에 대한 자주 묻는 질문(FAQ) | 마이크로 소프트 문서
description: 이 문서에서는 Azure 네트워크 감시자 서비스에 대해 자주 묻는 질문에 대한 답변을 답변합니다.
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
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471859"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Azure 네트워크 감시자에 대한 자주 묻는 질문(FAQ)
[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 서비스는 Azure 가상 네트워크의 리소스에 대한 로그를 모니터링, 진단, 보고, 로그를 활성화 또는 비활성화하는 도구 모음을 제공합니다. 이 문서에서는 서비스에 대한 일반적인 질문에 대한 답변을 답변합니다.

## <a name="general"></a>일반

### <a name="what-is-network-watcher"></a>Network Watcher란?
네트워크 감시자는 Azure 가상 네트워크의 가상 컴퓨터, 가상 네트워크, 응용 프로그램 게이트웨이, 로드 밸런서 및 기타 리소스를 포함하는 IaaS(서비스로서의 인프라) 구성 요소의 네트워크 상태를 모니터링하고 복구하도록 설계되었습니다. PaaS(서비스형 플랫폼) 인프라를 모니터링하거나 웹/모바일 분석을 가져오는 솔루션이 아닙니다.

### <a name="what-tools-does-network-watcher-provide"></a>네트워크 감시자가 제공하는 도구는 무엇입니까?
네트워크 감시자는 세 가지 주요 기능 세트를 제공합니다.
* 모니터링
  * [토폴로지 보기는](https://docs.microsoft.com/azure/network-watcher/view-network-topology) 가상 네트워크의 리소스와 가상 네트워크의 관계를 보여 줍니다.
  * [연결 모니터를](https://docs.microsoft.com/azure/network-watcher/connection-monitor) 사용하면 VM과 다른 네트워크 리소스 간의 연결 및 대기 시간을 모니터링할 수 있습니다.
  * [네트워크 성능 모니터를](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) 사용하면 하이브리드 네트워크 아키텍처, Expressroute 회로 및 서비스/애플리케이션 엔드포인트 전반에서 연결 및 대기 시간을 모니터링할 수 있습니다.  
* 진단
  * [IP 흐름 확인을](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) 사용하면 VM 수준에서 트래픽 필터링 문제를 감지할 수 있습니다.
  * [Next Hop을](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) 사용하면 트래픽 경로를 확인하고 라우팅 문제를 감지할 수 있습니다.
  * [연결 문제 해결을](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) 사용하면 VM과 다른 네트워크 리소스 간에 일회성 연결 및 대기 시간 검사를 수행할 수 있습니다.
  * [패킷 캡처를](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) 사용하면 가상 네트워크의 VM에서 모든 트래픽을 캡처할 수 있습니다.
  * [VPN 문제 해결은](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) VPN 게이트웨이 및 연결에서 여러 진단 검사를 실행하여 디버그 문제를 해결합니다.
* 로깅
  * [NSG 흐름 로그를](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) 사용하면 [NSG(네트워크 보안 그룹)의](https://docs.microsoft.com/azure/virtual-network/security-overview) 모든 트래픽을 기록할 수 있습니다.
  * [트래픽 분석은](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) NSG 흐름 로그 데이터를 처리하므로 네트워크 트래픽을 시각화, 쿼리, 분석 및 이해할 수 있습니다.


자세한 내용은 [네트워크 감시자 개요 페이지를](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)참조하십시오.


### <a name="how-does-network-watcher-pricing-work"></a>네트워크 감시자 가격은 어떻게 작동합니까?
네트워크 감시자 구성 요소 및 해당 가격에 대한 [가격 책정 페이지를](https://azure.microsoft.com/pricing/details/network-watcher/) 방문하십시오.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>네트워크 감시자가 지원/사용할 수 있는 지역은 무엇입니까?
[Azure 서비스](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher) 가용성 페이지에서 최신 지역 별 가용성을 볼 수 있습니다.

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>네트워크 감시기를 사용하려면 어떤 권한이 필요합니까?
네트워크 감시자 를 [사용하는 데 필요한 RBAC 권한](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions)목록을 참조하십시오. 리소스를 배포하려면 NetworkWatcherRG에 대한 기여자 권한이 필요합니다(아래 참조).

### <a name="how-do-i-enable-network-watcher"></a>Network Watcher를 사용하도록 설정하려면 어떻게 해야 하나요?
모든 구독에 대해 네트워크 감시자 서비스가 [자동으로 활성화됩니다.](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/)

### <a name="what-is-the-network-watcher-deployment-model"></a>네트워크 감시자 배포 모델은 무엇입니까?
네트워크 감시자 상위 리소스는 모든 리전에서 고유한 인스턴스로 배포됩니다. 명명 형식: NetworkWatcher_RegionName. 예: NetworkWatcher_centralus "미국 중부" 리전의 네트워크 감시자 리소스입니다.

### <a name="what-is-the-networkwatcherrg"></a>네트워크감시기RG란?
네트워크 감시자 리소스는 자동으로 생성되는 숨겨진 **NetworkWatcherRG** 리소스 그룹에 있습니다. 예를 들어 NSG 흐름 로그 리소스는 네트워크 감시자의 자식 리소스이며 NetworkWatcherRG에서 활성화됩니다.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>네트워크 감시자 확장을 설치해야 하는 이유는 무엇입니까? 
VM에서 트래픽을 생성하거나 가로채야 하는 모든 기능에는 네트워크 감시자 확장이 필요합니다. 

### <a name="which-features-require-the-network-watcher-extension"></a>네트워크 감시자 확장이 필요한 기능은 무엇입니까?
패킷 캡처, 연결 문제 해결 및 연결 모니터 기능을 사용할 수 있도록 네트워크 감시자 확장이 필요합니다.

### <a name="what-are-resource-limits-on-network-watcher"></a>네트워크 감시자의 리소스 제한은 무엇입니까?
모든 [제한에](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) 대한 서비스 제한 페이지를 참조하십시오.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>지역당 네트워크 감시자의 인스턴스가 하나만 허용되는 이유는 무엇입니까? 
네트워크 감시기는 기능의 작동을 위해 구독을 한 번 활성화하면됩니다.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>네트워크 감시자 리소스를 관리하어떻게 하나요? 
네트워크 감시자 리소스는 네트워크 감시자의 백 엔드 서비스를 나타내며 Azure에서 완전히 관리합니다. 고객은 이를 관리할 필요가 없습니다. 이동과 같은 작업은 리소스에서 지원되지 않습니다. 그러나 [리소스를 삭제할 수 있습니다.](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal) 

## <a name="nsg-flow-logs"></a>NSG 흐름 로그

### <a name="what-does-nsg-flow-logs-do"></a>NSG 흐름 로그는 무엇을 합니까?
Azure 네트워크 리소스는 [NSG(네트워크 보안 그룹)를](https://docs.microsoft.com/azure/virtual-network/security-overview)통해 결합및 관리할 수 있습니다. NSG 흐름 로그를 사용하면 NSG를 통해 모든 트래픽에 대한 5튜플 흐름 정보를 기록할 수 있습니다. 원시 흐름 로그는 필요에 따라 추가 처리, 분석, 쿼리 또는 내보낼 수 있는 Azure Storage 계정에 기록됩니다.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>방화벽 뒤에 있는 저장소 계정과 함께 NSG 흐름 로그를 사용하려면 어떻게 해야 합니까?

방화벽 뒤에 있는 저장소 계정을 사용하려면 신뢰할 수 있는 Microsoft 서비스가 저장소 계정에 액세스하려면 예외를 제공해야 합니다.

* 포털의 글로벌 검색 또는 저장소 계정 페이지에서 저장소 계정 이름을 입력하여 [저장소 계정으로](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) 이동합니다.
* **설정** 섹션 아래에서 **방화벽 및 가상 네트워크**를 선택합니다.
* "에서 액세스 허용"에서 **선택한 네트워크를**선택합니다. 그런 다음 **예외에서** **"신뢰할 수 있는 Microsoft 서비스가 이 저장소 계정에 액세스할 수 있도록 허용"** 옆의 체크박스를 선택합니다. 
* 이미 선택되어 있는 경우에는 변경할 필요가 없습니다.  
* [NSG 흐름 로그 개요 페이지에서](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) 대상 NSG를 찾아 위의 저장소 계정을 선택한 NSG 흐름 로그를 사용하도록 설정합니다.

몇 분 후에 스토리지 로그를 확인할 수 있습니다. 그러면 업데이트된 타임스탬프 또는 새로 만든 JSON 파일이 표시됩니다.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>서비스 엔드포인트 뒤에 있는 저장소 계정과 함께 NSG 흐름 로그를 사용하려면 어떻게 해야 합니까?

NSG 흐름 로그는 추가 구성없이 서비스 끝점과 호환됩니다. 가상 네트워크에서 [서비스 끝점 사용에 대한 자습서를](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) 참조하십시오.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>흐름 로그 버전 1 & 2의 차이점은 무엇입니까?
플로우 로그 버전 2는 전송된 바이트 및 패킷에 대한 정보를 저장하기 & *Flow State의* 개념을 소개합니다. [자세한 내용을 읽어보십시오.](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)

## <a name="next-steps"></a>다음 단계
 - 네트워크 감시자로 시작하는 몇 가지 자습서에 대한 [설명서 개요 페이지로](https://docs.microsoft.com/azure/network-watcher/) 이동하십시오.
