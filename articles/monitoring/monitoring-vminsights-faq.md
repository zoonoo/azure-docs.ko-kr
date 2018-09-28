---
title: VM용 Azure Monitor 질문과 대답 | Microsoft Docs
description: VM용 Azure Monitor는 Azure VM 운영 체제의 상태 및 성능 모니터링뿐만 아니라 응용 프로그램 구성 요소 및 종속성의 자동 검색도 다른 리소스와 결합하고, 이러한 항목 간의 통신을 매핑하는 Azure의 솔루션입니다. 이 문서에서는 일반적인 질문에 답변합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: e8723bd8e26eb66d9333c2093b5ada8bc78ca14b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989604"
---
# <a name="azure-monitor-for-vms-frequently-asked-questions"></a>VM용 Azure Monitor 질문과 대답
Microsoft FAQ는 Microsoft Azure의 VM용 Azure Monitor에 대해 자주 묻는 질문의 목록입니다. 솔루션에 대한 추가 질문이 있으면 [토론 포럼](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

## <a name="can-i-onboard-to-an-existing-workspace"></a>기존 작업 영역에 온보딩할 수 있나요?
가상 머신이 Log Analytics 작업 영역에 이미 연결된 경우 VM용 Azure Monitor에 온보딩되면 해당 작업 영역을 계속 사용하도록 [여기](monitoring-vminsights-onboard.md#prerequisites)에서 나열된 지원되는 지역 중 하나에서 제공될 수 있습니다.

온보딩하면 VM용 Azure Monitor에서 표시하고 분석할 이 정보의 수집을 시작하기 위해 모든 VM이 작업 영역에 데이터를 보고하게 되는 작업 영역에 대한 성능 카운터를 구성합니다.  결과적으로, 선택된 작업 영역에 연결된 VM의 모든 성능 데이터가 표시됩니다.  상태 및 맵 기능은 온보딩하도록 지정된 VM에 대해서만 활성화됩니다.

성능 카운터를 사용할 수 있는 경우에 대한 자세한 내용은 [온보딩](monitoring-vminsights-onboard.md) 문서를 참조하세요.

## <a name="can-i-onboard-to-a-new-workspace"></a>새 작업 영역에 온보딩할 수 있나요? 
현재 VM이 기존 Log Analytics 작업 영역에 연결되지 않은 경우 데이터를 저장할 새 작업 영역을 만들어야 합니다.  Azure Portal을 통해 VM용 Azure Monitor에 대해 단일 Azure VM을 구성한 경우 새 기본 작업 영역을 만드는 작업이 자동으로 수행됩니다.

스크립트 기반 메서드를 사용하려는 경우 [온보딩](monitoring-vminsights-onboard.md) 문서에서 이러한 단계를 다룹니다. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>내 VM에서 이미 기존 작업 영역에 보고하는 경우 어떻게 해야 하나요?
이미 가상 머신의 데이터를 수집하는 경우 기존 Log Analytics 작업 영역에 데이터를 보고하도록 이미 구성되었을 수 있습니다.  해당 작업 영역이 지원되는 지역 중 하나라면 기존 작업 영역에 VM용 Azure Monitor를 사용하도록 설정할 수 있습니다.  이미 사용 중인 작업 영역이 지원되는 지역 중 하나가 아닌 경우 이번에는 VM용 Azure Monitor에 온보딩할 수 없습니다.  본사에서는 추가 지역을 지원하기 위해 적극적으로 노력하고 있습니다.

>[!NOTE]
>VM용 Azure Monitor에 온보딩하도록 선택했는지 여부와 상관 없이 작업 영역에 보고는 모든 VM에 영향을 주는 작업 영역에 대한 성능 카운터를 구성합니다. 작업 영역에 대해 성능 카운터를 구성하는 방법에 대한 자세한 내용은 [설명서](../log-analytics/log-analytics-data-sources-performance-counters.md)를 참조하세요. VM용 Azure Monitor에 구성된 카운터에 대한 자세한 내용은 [온보딩 설명서](monitoring-vminsights-onboard.md#performance-counters-enabled)를 참조하세요.  

## <a name="why-did-my-vm-fail-to-onboard"></a>내 VM이 온보딩하지 못한 이유는?
Azure Portal에서 Azure VM을 온보딩한 경우 다음 단계가 수행됩니다.

* 해당 옵션을 선택한 경우 기본 Log Analytics 작업 영역이 만들어집니다.
* 선택한 작업 영역에 대한 성능 카운터가 구성됩니다. 이 단계가 실패하면 일부 성능 차트 및 표에서는 온보딩한 VM에 대한 데이터를 표시하지 않습니다. [여기](monitoring-vminsights-onboard.md#enable-with-powershell)에 문서화된 PowerShell 스크립트를 실행하여 이 문제를 해결할 수 있습니다.
* 필요한 경우 Log Analytics 에이전트는 VM 확장을 사용하여 설치됩니다.  
* 필요한 경우 VM용 Azure Monitor 맵 종속성 에이전트는 VM 확장을 사용하여 Azure VM에 설치됩니다.  
* 필요한 경우 Azure Monitor 구성 요소가 상태 기능을 지원하도록 구성하고, VM이 상태 데이터를 보고하도록 구성합니다.

온보딩 프로세스 중에 포털에서 알림 상태를 반환하는 위의 각 상태에 대해 확인합니다.  작업 영역 및 에이전트 설치의 구성에는 일반적으로 5~10분 정도가 걸립니다.  포털에서 모니터링 및 상태 데이터를 보려면 5~10분이 더 걸립니다.  

온보딩을 시작하고 VM이 온보딩되어야 함을 나타내는 메시지가 표시된 경우 VM이 프로세스를 완료하려면 최대 30분이 허용됩니다. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>내 VM의 성능 차트에 일부 또는 모든 데이터가 표시되지 않습니다
디스크 테이블 또는 일부 성능 차트에 성능 데이터가 표시되지 않는 경우 작업 영역에서 성능 카운터를 구성할 수 없습니다. 이 문제를 해결하려면 다음 [PowerShell 스크립트](monitoring-vminsights-onboard.md#enable-with-powershell)를 실행합니다.

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>VM용 Azure Monitor 맵 기능과 서비스 맵이 어떻게 다른가요?
VM용 Azure Monitor 맵 기능은 서비스 맵을 기반으로 하지만 다음과 같은 차이점이 있습니다.

* 맵 보기는 VM 블레이드 및 Azure Monitor의 VM용 Azure Monitor에서 액세스할 수 있습니다.
* 이제 맵에서 연결은 클릭 가능하고 선택한 연결에 대한 사이드 패널에서 연결 메트릭 데이터 보기를 표시합니다.
* 더 복잡한 맵을 지원하기 위해 맵을 만드는 데 사용되는 새 API가 있습니다.
* 이제 모니터링된 VM은 클라이언트 그룹 노드에 포함됩니다. 도넛형 차트에서는 그룹에서 모니터링된 가상 머신 및 모니터링되지 않는 가상 머신의 비율을 보여줍니다.  그룹이 확장되면 머신의 목록을 필터링하는 데 사용할 수도 있습니다.
* 이제 모니터링된 가상 머신은 서버 포트 그룹 노드에 포함됩니다. 도넛형 차트에서는 그룹에서 모니터링된 머신 및 모니터링되지 않는 머신의 비율을 보여줍니다.  그룹이 확장되면 머신의 목록을 필터링하는 데 사용할 수도 있습니다.
* 맵 스타일은 Application insights에서 앱 맵을 사용하여 더욱 일관되게 업데이트되었습니다.
* 사이드 패널이 업데이트되었지만 서비스 맵에서 지원된 통합의 전체 기능(업데이트 관리, 변경 내용 추적, 보안 및 서비스 데스크)이 포함되지 않습니다. 
* 맵에 대한 그룹 및 머신을 선택하는 옵션이 업데이트되어 이제 구독, 리소스 그룹, Azure 가상 머신 확장 집합 및 클라우드 서비스를 지원합니다.
* VM용 Azure Monitor 맵 기능에서 새 서비스 맵 머신 그룹을 만들 수 없습니다.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>내 성능 차트가 점선을 표시하는 이유는?

다음과 같은 이유로 발생할 수 있습니다.  데이터 수집에 간격이 있는 경우에 줄이 점선으로 그려집니다.  사용하도록 설정된 성능 카운터에 대한 데이터 샘플링 빈도를 수정한 경우(기본 설정은 60초마다 데이터를 수집하는 것임) 차트에 대해 좁은 시간 범위를 선택하고 샘플링 빈도가 차트에 사용된 버킷 크기 미만이면(예: 샘플링 빈도는 10분 간격이며 차트의 각 버킷은 5분 간격임) 차트에서 점선을 볼 수 있습니다.  광범위한 시간 범위를 보도록 선택하면 이 경우에 차트 선이 점선이 아닌 실선으로 표시됩니다.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>VM용 Azure Monitor에서 그룹이 지원되나요?
성능 기능은 특정 작업 영역 내에서 강조 표시된 리소스에 따라 그룹뿐만 아니라 특정 Azure 가상 머신 확장 집합 및 클라우드 서비스에 따라 그룹화를 지원합니다.

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>집계 성능 차트에서 95번째 백분위수 줄에 영향을 주는 항목에 대한 세부 정보를 표시하려면 어떻게 할까요?
기본적으로 목록은 5번째 백분위수의 최솟값을 포함한 머신을 보여주는 사용 가능한 메모리 차트를 제외하고 선택된 메트릭에 대해 95번째 백분위수에 대한 최댓값이 있는 VM을 표시하도록 정렬됩니다.  차트를 클릭하면 적절한 메트릭이 선택된 **상위 N개 목록** 보기가 열립니다.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>맵 기능이 다른 VNet 및 서브넷에서 중복 IP를 어떻게 처리하나요?
서브넷 및 VNet에서 VM 또는 Azure 가상 머신 확장 집합을 사용하여 IP 범위를 복제하는 경우 VM용 Azure Monitor에서 잘못된 정보를 표시할 수 있습니다. 알려진 문제이며 본사에서는 이 환경을 개선하는 옵션을 개발하는 중입니다.

## <a name="does-map-feature-support-ipv6"></a>맵 기능이 IPv6을 지원하나요?
맵 기능은 현재 IPv4만 지원하며 본사에서는 IPv6에 대한 지원을 개발하는 중입니다. IPv6 내에서 터널링된 IPv4도 지원합니다.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>리소스 그룹 또는 다른 대규모 그룹에 대한 맵을 로드하는 경우 맵을 보기 어렵습니다.
크고 복잡한 구성을 처리하도록 맵을 개선하는 동안 맵에서는 많은 노드, 연결 및 노드가 클러스터로 작동할 수 있습니다.  계속 확장성을 증가시키도록 지원을 강화하기 위해 최선을 다하고 있습니다.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>성능 탭의 네트워크 차트가 Azure VM 개요 페이지의 네트워크 차트와 다르게 보이는 이유는?

Azure VM의 개요 페이지에는 게스트 VM에서 작업의 호스트 측정값에 따라 차트가 표시됩니다.  Azure VM 개요에 대한 네트워크 차트의 경우 청구되는 네트워크 트래픽만 표시합니다.  여기에는 VNet 간 트래픽이 포함되지 않습니다.  VM용 Azure Monitor에 대해 표시된 데이터 및 차트는 게스트 VM의 데이터를 기반으로 하고 네트워크 차트는 VNet 간 트래픽을 비롯하여 해당 VM에 대한 인바운드 및 아웃바운드인 모든 TCP/IP 트래픽을 표시합니다.
