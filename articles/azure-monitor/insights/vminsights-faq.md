---
title: VM용 Azure Monitor(미리 보기) 질문과 대답 | Microsoft Docs
description: VM용 Azure Monitor(미리 보기)는 Azure VM 운영 체제의 상태 및 성능 모니터링을 결합하는 Azure의 솔루션입니다. 애플리케이션 구성 요소 및 다른 리소스와의 종속성을 자동으로 검색하고 이들 간의 통신을 매핑합니다. 이 문서에서는 자주 묻는 질문에 대답합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: a97a7be0eaa8438a4df27b610106ec6ab9f60d30
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184394"
---
# <a name="azure-monitor-for-vms-preview-faq"></a>VM용 Azure Monitor(미리 보기) FAQ
이 문서에서는 VM용 Azure Monitor에 대해 자주 묻는 질문에 대답합니다. 솔루션에 대한 추가 질문이 있으면 [Azure 토론 포럼](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하여 질문을 게시하세요. 자주 묻는 질문인 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

## <a name="can-i-deploy-vms-to-an-existing-workspace"></a>기존 작업 영역에 VM을 배포할 수 있나요?
가상 머신이 Log Analytics 작업 영역에 이미 연결된 경우 VM용 Azure Monitor에 배포할 때 해당 작업 영역을 계속 사용할 수 있습니다. 작업 영역이 [VM용 Azure Monitor(미리 보기) 배포](vminsights-onboard.md#prerequisites) 섹션의 "전제 조건"에 나열된 지원되는 Azure 지역 중 하나에 있어야 합니다.

배포하는 과정에서 작업 영역의 성능 카운터를 구성할 것입니다. 이 작업으로 인해 작업 영역에 데이터를 보고하는 VM은 VM용 Azure Monitor에 표시하고 분석할 정보 수집을 시작합니다. 결과적으로, 선택한 작업 영역에 연결된 모든 VM의 성능 데이터가 표시됩니다. 상태 및 맵 기능은 배포하도록 지정된 VM에 대해서만 활성화됩니다.

활성화되는 성능 카운터에 대한 자세한 내용은 [VM용 Azure Monitor(미리 보기) 배포](vminsights-onboard.md)를 참조하세요.

## <a name="can-i-deploy-vms-to-a-new-workspace"></a>새 작업 영역에 VM을 배포할 수 있나요? 
현재 VM이 기존 Log Analytics 작업 영역에 연결되지 않은 경우 데이터를 저장할 새 작업 영역을 만들어야 합니다. Azure Portal에서 VM에 대한 단일 VM용 Azure Monitor를 구성하여 자동으로 작업 영역을 만들 수 있습니다.

스크립트 기반 메서드를 사용하기로 선택하는 경우 [VM용 Azure Monitor(미리 보기) 배포](vminsights-onboard.md)를 참조하세요. 

## <a name="what-can-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>내 VM이 이미 기존 작업 영역에 보고하는 경우 어떻게 해야 하나요?
이미 가상 머신의 데이터를 수집하고 있는 경우 기존 Log Analytics 작업 영역에 데이터를 보고하도록 이미 구성되었을 수 있습니다. 해당 작업 영역이 지원되는 Azure 지역 중 하나라면 기존 작업 영역에 VM용 Azure Monitor를 사용하도록 설정할 수 있습니다. 본사에서는 추가 지역을 지원하기 위해 적극적으로 노력하고 있습니다.

>[!NOTE]
>Microsoft에서는 작업 영역에 대한 성능 카운터를 구성하며, 이는 VM용 Azure Monitor에 배포하도록 선택했는지 여부에 관계없이 작업 영역에 보고하는 모든 VM에 영향을 줍니다. 작업 영역에 대한 성능 카운터를 구성하는 방법에 대 한 자세한 내용은 [Log Analytics의 Windows 및 Linux 성능 데이터 원본](../../azure-monitor/platform/data-sources-performance-counters.md)의 "성능 카운터 구성" 섹션을 참조하세요. VM용 Azure Monitor에 구성된 카운터에 대한 자세한 내용은 [VM용 Azure Monitor(미리 보기) 배포](vminsights-onboard.md)를 참조하세요. 

## <a name="why-did-my-vm-deployment-fail"></a>내 VM 배포가 실패하는 이유는 무엇인가요?
Azure Portal에서 Azure VM을 배포할 때 다음 이벤트가 발생합니다.

* 해당 옵션을 선택한 경우 기본 Log Analytics 작업 영역이 만들어집니다.
* 선택한 작업 영역에 대한 성능 카운터가 구성됩니다. 이 단계가 실패하면 일부 성능 차트 및 표에서는 사용자가 배포한 VM에 대한 데이터를 표시하지 않습니다. [VM용 Azure Monitor(미리 보기) 배포](vminsights-onboard.md#enable-with-powershell)의 "PowerShell을 통해 사용하도록 설정" 섹션에 설명된 PowerShell 스크립트를 실행하여 이 문제를 해결할 수 있습니다.
* 필요한 경우 VM 확장이 있는 Azure VM에 Log Analytics 에이전트가 설치됩니다. 
* 필요한 경우 확장이 있는 Azure VM에 VM용 Azure Monitor 맵 종속성 에이전트가 설치됩니다. 
* 필요한 경우 상태 기능을 지원하도록 Azure Monitor 구성 요소가 구성되고, 상태 데이터를 보고하도록 VM이 구성됩니다.

배포하는 동안 Microsoft는 각 이전 단계의 상태를 확인하고 포털에서 사용자에게 알림 상태를 반환합니다. 작업 영역 및 에이전트 설치의 구성에는 일반적으로 5~10분 정도가 걸립니다. Azure Portal에서 모니터링 및 상태 데이터를 보려면 5~10분이 더 걸립니다. 

배포를 시작했는데 VM을 배포해야 한다는 메시지가 표시되면 VM이 프로세스를 완료하는 시간으로 최대 30분을 허용합니다. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>내 VM의 성능 차트에 일부 또는 모든 데이터가 표시되지 않습니다
디스크 테이블 또는 성능 차트에 성능 데이터가 표시되지 않는 경우 작업 영역에서 성능 카운터가 구성되지 않은 것일 수 있습니다. [VM용 Azure Monitor(미리 보기) 배포](vminsights-onboard.md#enable-with-powershell)의 "PowerShell을 통해 사용하도록 설정" 섹션에 설명된 PowerShell 스크립트를 실행하여 이 문제를 해결할 수 있습니다.

## <a name="how-is-the-azure-monitor-for-vms-map-feature-different-from-service-map"></a>VM용 Azure Monitor 맵 기능과 서비스 맵이 어떻게 다른가요?
VM용 Azure Monitor 맵 기능은 서비스 맵을 기반으로 하지만 다음과 같은 차이점이 있습니다.

* 맵 보기는 VM 창과 Azure Monitor의 VM용 Azure Monitor에서 액세스할 수 있습니다.
* 이제 맵의 연결은 클릭 가능하며 사이드 패널에 연결 메트릭 데이터를 표시합니다.
* 복잡한 맵을 보다 잘 지원하는 맵을 만들기 위해 새로운 API가 사용됩니다.
* 이제 모니터링된 VM은 클라이언트 그룹 노드에 있으며, 도넛형 차트는 모니터링된 가상 머신과 모니터링되지 않는 가상 머신의 비율을 표시합니다. 그룹이 확장되면 머신 목록을 필터링하는 데 사용할 수도 있습니다.
* 이제 모니터링된 VM은 서버 포트 그룹 노드에 있으며, 도넛형 차트는 모니터링된 머신과 모니터링되지 않는 머신의 비율을 표시합니다. 그룹이 확장되면 머신 목록을 필터링하는 데 사용할 수도 있습니다.
* 맵 스타일은 Azure Application insights의 애플리케이션 맵과 더욱 일관되게 업데이트되었습니다.
* 사이트 패널은 업데이트되었지만 아직 서비스 맵에서 지원되는 전체 통합 세트: 업데이트 관리, 변경 내용 추적, 보안 및 서비스 데스크를 제공하지 않습니다. 
* 매핑할 그룹 및 머신을 선택하는 옵션이 업데이트되었습니다. 이제 구독, 리소스 그룹, Azure 가상 머신 확장 집합 및 클라우드 서비스를 지원합니다.
* VM용 Azure Monitor 맵 기능에서 새 서비스 맵 머신 그룹을 만들 수 없습니다. 

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>내 성능 차트가 점선을 표시하는 이유는?

성능 차트는 다음과 같은 몇 가지 이유로 실선 대신 점선으로 표시됩니다.
* 데이터 수집에 차이가 있을 수 있습니다. 

* 데이터 샘플링의 기본 설정은 60초 간격입니다. 차트의 시간 범위를 좁게 선택하고 샘플링 주기가 차트에 사용되는 버킷 크기보다 작으면 점선으로 표시될 수 있습니다. 샘플링 주기를 10분으로 선택했으며 차트의 각 버킷이 5분이라고 가정해 보겠습니다. 이 경우 광범위한 시간 범위를 보도록 선택하면 차트 선이 점선이 아닌 실선으로 표시됩니다.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>VM용 Azure Monitor에서 그룹이 지원되나요?
예, 종속성 에이전트가 설치되면 VM에서 정보를 수집하여 구독, 리소스 그룹, 가상 머신 확장 집합 및 클라우드 서비스에 기반한 그룹을 표시합니다. 서비스 맵을 사용하고 있고 머신 그룹을 만든 경우 이러한 그룹도 표시됩니다. 또한 보고 있는 작업 영역에 대한 컴퓨터 그룹을 만든 경우 컴퓨터 그룹이 그룹 필터에 표시됩니다. 

## <a name="how-can-i-display-the-details-about-whats-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>집계 성능 차트에서 95번째 백분위 수 줄에 영향을 주는 항목에 대한 세부 정보를 표시하려면 어떻게 해야 하나요?
기본적으로 선택한 메트릭에 대한 95번째 백분위 수의 값이 가장 큰 VM을 표시하도록 목록이 정렬됩니다. 5번째 백분위 수가 가장 작은 머신을 표시하는 **사용 가능한 메모리** 차트는 예외입니다. 차트를 선택하면 적절한 메트릭이 선택된 **상위 N개 목록** 보기가 열립니다.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-various-virtual-networks-and-subnets"></a>맵 기능은 다양한 가상 네트워크 및 서브넷에서 중복 IP를 어떻게 처리하나요?
서브넷 및 가상 네트워크에서 VM 또는 Azure 가상 머신 확장 집합을 사용하여 IP 범위를 복제하는 경우 VM용 Azure Monitor 맵 기능이 잘못된 정보를 표시할 수 있습니다. Microsoft는 이 문제를 알고 있으며 환경을 개선하기 위한 옵션을 조사하는 중입니다.

## <a name="does-the-map-feature-support-ipv6"></a>맵 기능이 IPv6를 지원하나요?
맵 기능은 현재 IPv4만 지원하며, IPv6를 지원하기 위한 연구가 진행 중입니다. IPv6 내에서 터널링된 IPv4도 지원됩니다.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-why-is-the-map-difficult-to-view"></a>리소스 그룹 또는 다른 대규모 그룹에 대한 맵을 로드할 때 맵을 보기 어려운 이유는 무엇인가요?
크고 복잡한 구성을 처리하도록 맵 기능이 개선되기는 했지만, 맵에서 수많은 노드, 연결 및 노드가 클러스터로 작동할 수 있습니다. 계속 확장성을 증가시키도록 지원을 강화하기 위해 최선을 다하고 있습니다.  

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-from-the-network-chart-on-the-azure-vm-overview-page"></a>성능 탭의 네트워크 차트가 Azure VM 개요 페이지의 네트워크 차트와 다르게 보이는 이유는 무엇인가요?

Azure VM의 개요 페이지에는 게스트 VM에서 작업의 호스트 측정값에 따라 차트가 표시됩니다. Azure VM 개요 페이지의 네트워크 차트는 요금이 청구되는 네트워크 트래픽만 표시합니다. 가상 네트워크 간의 트래픽은 표시되지 않습니다. VM용 Azure Monitor에 대해 표시되는 데이터 및 차트는 게스트 VM의 데이터를 기반으로 하고, 네트워크 차트는 가상 네트워크 간 트래픽을 비롯하여 해당 VM에 대한 인바운드 및 아웃바운드 TCP/IP 트래픽을 모두 표시합니다.

## <a name="what-are-the-limitations-of-the-log-analytics-free-pricing-plan"></a>Log Analytics Free 가격 책정 플랜의 제한 사항은 무엇인가요?
*Free* 가격 책정 계층을 사용하여 Log Analytics 작업 영역에 Azure Monitor를 구성한 경우 VM용 Azure Monitor 맵 기능은 작업 영역에 머신을 5대까지만 연결할 수 있도록 지원합니다. 

예를 들어 평가판 작업 영역에 VM 5대가 연결되어 있다고 가정해 보겠습니다. VM 하나를 분리하고 새 VM을 연결하면 새 VM은 맵 페이지에서 모니터링 및 반영되지 않습니다. 이 시나리오에서는 새 VM을 열면 VM에 Insights(미리 보기)가 이미 설치되어 있더라도 **지금 사용해 보기** 옵션을 사용하고 왼쪽 창에서 **Insights(미리 보기)** 를 선택하라는 메시지가 표시됩니다. 하지만 VM용 Azure Monitor에 VM이 배포되지 않은 경우에는 평소처럼 옵션이 표시되지 않습니다. 

## <a name="next-steps"></a>다음 단계
가상 머신을 모니터링하기 위한 요구 사항 및 방법을 이해하려면 [VM용 Azure Monitor(미리 보기) 배포](vminsights-onboard.md)를 검토하세요.
