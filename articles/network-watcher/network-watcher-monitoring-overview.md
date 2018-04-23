---
title: Azure Network Watcher 소개 | Microsoft Docs
description: Azure에서 네트워크에 연결된 리소스를 모니터링하고 시각화하는 Network Watcher 서비스에 대한 개요를 간략히 설명합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: a546296749ba9373355cfe2b857b83d8af94d5a1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-network-monitoring-overview"></a>Azure 네트워크 모니터링 개요

고객은 VNet, ExpressRoute, Application Gateway, 부하 분산 장치 등과 같은 다양한 개별 네트워크 리소스를 오케스트레이션하고 구성하여 Azure에서 종단 간 네트워크를 구축합니다. 모니터링은 각 네트워크 리소스에서 사용할 수 있습니다. 이 모니터링을 리소스 수준 모니터링이라고 합니다.

종단 간 네트워크는 복잡하게 구성되고 리소스 간에 상호 작용하므로 Network Watcher를 통해 시나리오 기반 모니터링이 필요한 복잡한 시나리오를 만듭니다.

이 문서에서는 시나리오 및 리소스 수준 모니터링에 대해 설명합니다. Azure의 네트워크 모니터링은 포괄적이며 다음 두 가지 범주를 포함합니다.

* [**Network Watcher**](#network-watcher) - Network Watcher 기능과 함께 시나리오 기반 모니터링이 제공됩니다. 이 서비스에는 패킷 캡처, 다음 홉, IP 흐름 확인, 보안 그룹 보기, NSG 흐름 로그가 포함되어 있습니다. 시나리오 수준 모니터링에서는 개별 네트워크 리소스 모니터링과 달리 네트워크 리소스의 종단 간 보기를 제공합니다.
* [**리소스 모니터링**](#network-resource-level-monitoring) - 리소스 수준 모니터링은 진단 로그, 메트릭, 문제 해결 및 리소스 상태의 네 가지 기능으로 구성됩니다. 이러한 모든 기능은 네트워크 리소스 수준에서 구현됩니다.

## <a name="network-watcher"></a>Network Watcher

Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. Network Watcher에서 제공하는 네트워크 진단 및 시각화 도구를 사용하면 Azure에서 네트워크를 파악하고, 진단하고, 정보를 얻을 수 있습니다.

Network Watcher는 현재 다음과 같은 기능을 제공합니다.

* **[토폴로지](network-watcher-topology-overview.md)** - 리소스 그룹의 네트워크 리소스 간 다양한 연결 및 상호 연결을 보여 주는 네트워크 수준 보기를 제공합니다.
* **[변수 패킷 캡처](network-watcher-packet-capture-overview.md)** - 가상 컴퓨터의 내부 및 외부 패킷 데이터를 캡처합니다. 고급 필터링 옵션과 세밀한 조정 컨트롤(예: 시간 및 크기 제한 설정)이 다양하게 제공됩니다. 패킷 데이터는 Blob 저장소 또는 .cap 형식의 로컬 디스크에 저장할 수 있습니다.
* **[IP 흐름 확인](network-watcher-ip-flow-verify-overview.md)** - 흐름 정보의 5개 튜플 패킷 매개 변수(대상 IP, 원본 IP, 대상 포트, 원본 포트 및 프로토콜)에 따라 패킷을 허용하거나 거부하는지 확인합니다. 보안 그룹에서 패킷을 거부하면 해당 패킷을 거부한 규칙과 그룹이 반환됩니다.
* **[다음 홉](network-watcher-next-hop-overview.md)** - Azure 네트워크 패브릭에서 라우팅되는 패킷의 다음 홉을 결정하여 잘못 구성된 사용자 정의 경로를 진단할 수 있습니다.
* **[보안 그룹 보기](network-watcher-security-group-view-overview.md)** - VM에 적용되는 효과적이고 실용적인 보안 규칙을 가져옵니다.
* **[NSG 흐름 로깅](network-watcher-nsg-flow-logging-overview.md)** - 네트워크 보안 그룹의 흐름 로그를 사용하면 그룹의 보안 규칙으로 허용되거나 거부되는 트래픽과 관련된 로그를 캡처할 수 있습니다. 흐름은 원본 IP, 대상 IP, 원본 포트, 대상 포트 및 프로토콜의 5개 튜플 정보로 정의됩니다.
* **[Virtual Network 게이트웨이 및 연결 문제 해결](network-watcher-troubleshoot-manage-rest.md)** - Virtual Network 게이트웨이 및 연결에 발생한 문제를 해결하는 기능을 제공합니다.
* **[네트워크 구독 제한](#network-subscription-limits)** - 제한과 대조한 네트워크 리소스 사용량을 볼 수 있습니다.
* **[진단 로그 구성](#diagnostic-logs)** – 리소스 그룹의 네트워크 리소스에 대해 진단 로그를 사용하거나 사용하지 않도록 설정할 수 있는 단일 창을 제공합니다.
* **[연결 문제 해결](network-watcher-connectivity-overview.md)** - 가상 머신에서 Azure 컨텍스트를 사용해 보강된 지정된 끝점으로의 직접 TCP 연결을 설정할 수 있는지 확인합니다.
* **[연결 모니터](connection-monitor.md)** - 원본 및 대상 IP 주소와 포트를 사용하여 Azure 가상 머신과 IP 주소 사이의 대기 시간 및 구성 문제를 모니터링합니다.

### <a name="role-based-access-control-rbac-in-network-watcher"></a>Network Watcher의 RBAC(역할 기반 Access Control)

Network Watcher는 [Azure 역할 기반 Access Control(RBAC) 모델 ](../role-based-access-control/overview.md)을 사용합니다. Network Watcher에 필요한 권한은 다음과 같습니다. Network Watcher API를 시작하거나 포털에서 Network Watcher를 사용하기 위한 역할에 필요한 액세스 권한이 있는지 확인하는 것이 중요합니다.

|리소스| 사용 권한|
|---|---| 
|Microsoft.Storage/ |읽기|
|Microsoft.Authorization/| 읽기| 
|Microsoft.Resources/subscriptions/resourceGroups/| 읽기|
|Microsoft.Storage/storageAccounts/listServiceSas/ | 조치|
|Microsoft.Storage/storageAccounts/listAccountSas/ |조치|
|Microsoft.Storage/storageAccounts/listKeys/ | 조치|
|Microsoft.Compute/virtualMachines/ |읽기|
|Microsoft.Compute/virtualMachines/ |쓰기|
|Microsoft.Compute/virtualMachineScaleSets/ |읽기|
|Microsoft.Compute/virtualMachineScaleSets/ |쓰기|
|Microsoft.Network/networkWatchers/packetCaptures/ |읽기|
|Microsoft.Network/networkWatchers/packetCaptures/| 쓰기|
|Microsoft.Network/networkWatchers/packetCaptures/| 삭제|
|Microsoft.Network/networkWatchers/ |쓰기 |
|Microsoft.Network/networkWatchers/| 읽기 |
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/ | *|

### <a name="network-subscription-limits"></a>네트워크 구독 제한

네트워크 구독 제한은 사용 가능한 최대 리소스 수와 개별 지역에 속한 개별 구독의 각 네트워크 리소스 사용량을 대조한 세부 정보를 제공합니다.

![네트워크 구독 제한][nsl]

## <a name="network-resource-level-monitoring"></a>네트워크 리소스 수준 모니터링

리소스 수준 모니터링에서 사용할 수 있는 기능은 다음과 같습니다.

### <a name="audit-log"></a>감사 로그

네트워크 구성의 일부로 수행되는 작업을 기록합니다. 이러한 로그는 Azure Portal에서 보거나 Power BI 또는 타사 도구와 같은 Microsoft 도구를 사용하여 검색할 수 있습니다. 감사 로그는 포털, PowerShell, CLI 및 Rest API를 통해 사용할 수 있습니다. 감사 로그에 대한 자세한 내용은 [Resource Manager를 사용하는 감사 작업](../resource-group-audit.md)을 참조하세요.

감사 로그는 모든 네트워크 리소스에서 수행된 작업에 사용할 수 있습니다.

### <a name="metrics"></a>메트릭

메트릭은 일정 기간 동안 수집된 성능 측정 및 카운터입니다. 메트릭은 현재 Application Gateway에서 사용할 수 있습니다. 메트릭을 사용하여 임계값에 기반한 경고를 트리거할 수 있습니다. 메트릭을 사용하여 경고를 만드는 방법을 알아보려면 [Application Gateway 진단](../application-gateway/application-gateway-diagnostics.md)을 참조하세요.

![메트릭 보기][metrics]

### <a name="diagnostic-logs"></a>진단 로그

정기적이고 자동적인 이벤트가 네트워크 리소스에서 만들어지고 저장소 계정에 기록되어 이벤트 허브 또는 Log Analytics로 전송됩니다. 이러한 로그는 리소스 상태에 대한 정보를 제공하며, Power BI 및 Log Analytics와 같은 도구에서 볼 수 있습니다. 진단 로그를 보는 방법을 알아보려면 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)를 방문하세요.

진단 로그는 [부하 분산 장치](../load-balancer/load-balancer-monitor-log.md), [네트워크 보안 그룹](../virtual-network/virtual-network-nsg-manage-log.md), 경로 및 [Application Gateway](../application-gateway/application-gateway-diagnostics.md)에서 사용할 수 있습니다.

Network Watcher는 진단 로그 보기를 제공합니다. 이 보기에는 진단 로깅을 지원하는 모든 네트워킹 리소스가 포함됩니다. 이 보기에서 네트워킹 리소스를 빠르고 편리하게 사용하거나 사용하지 않도록 설정할 수 있습니다.

![로그][logs]

### <a name="troubleshooting"></a>문제 해결

포털의 환경 중 하나인 문제 해결 블레이드는 현재 개별 리소스와 관련된 일반적인 문제를 진단하기 위해 네트워크 리소스에 제공됩니다. 이 환경은 ExpressRoute, VPN Gateway, Application Gateway, 네트워크 보안 로그, 경로, DNS, 부하 분산 장치 및 Traffic Manager와 같은 네트워크 리소스에서 사용할 수 있습니다. 리소스 수준 문제 해결에 대해 자세히 알아보려면 [Azure 문제 해결로 문제 진단 및 해결](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/)을 참조하세요.

![문제 해결 정보][TS]

### <a name="resource-health"></a>리소스 상태

네트워크 리소스의 상태는 정기적으로 제공됩니다. 이러한 리소스에는 VPN Gateway 및 VPN 터널이 포함됩니다. 리소스 상태는 Azure Portal에서 액세스할 수 있습니다. 리소스 상태에 대해 자세히 알아보려면 [리소스 상태 개요](../resource-health/resource-health-overview.md)를 방문하세요.

## <a name="next-steps"></a>다음 단계

Network Watcher에 대해 알아보았으면 다음을 익힐 수 있습니다.

[Azure Portal에서 변수 패킷 캡처](network-watcher-packet-capture-manage-portal.md)를 방문하여 VM에서 패킷 캡처를 수행합니다.

[경고로 인해 발생한 패킷 캡처](network-watcher-alert-triggered-packet-capture.md)를 사용하여 자동 관리 모니터링 및 진단을 수행합니다.

오픈 소스 도구를 사용하는 [Wireshark로 패킷 캡처 분석](network-watcher-deep-packet-inspection.md)을 통해 보안 취약점을 감지합니다.

Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/networking-overview.md)을 알아봅니다.

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png











