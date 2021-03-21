---
title: Azure Portal에서 단일 가상 머신 또는 가상 머신 확장 집합에 대한 Azure Monitor 사용
description: Azure Portal를 사용 하 여 단일 Azure 가상 머신 또는 가상 머신 확장 집합에서 VM 정보를 사용 하도록 설정 하는 방법을 알아봅니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 076fcab7b0747a7993407edd65f9d08efc27309f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035588"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Azure Portal에서 단일 가상 머신 또는 가상 머신 확장 집합에 대한 Azure Monitor 사용
이 문서에서는 Azure Portal를 사용 하 여 가상 머신 또는 가상 머신 확장 집합에 대해 VM insights를 사용 하도록 설정 하는 방법을 설명 합니다. 이 절차는 다음과 같은 경우에 사용할 수 있습니다.

- Azure 가상 머신
- Azure 가상 머신 확장 집합
- Azure Arc와 연결 된 하이브리드 가상 컴퓨터

## <a name="prerequisites"></a>필수 조건

- [Log Analytics 작업 영역을 만들고 구성](./vminsights-configure-workspace.md)합니다. 또는이 프로세스 중에 새 작업 영역을 만들 수 있습니다.
- 지원 되는 [운영 체제](./vminsights-enable-overview.md#supported-operating-systems) 를 참조 하 여 활성화 하는 가상 머신 또는 가상 머신 확장 집합의 운영 체제가 지원 되는지 확인 합니다. 

## <a name="enable-vm-insights"></a>VM 인사이트 사용

Azure Portal에서 **가상** 머신, **가상 머신 확장 집합** 또는 **서버-Azure Arc** 를 선택 하 고 목록에서 리소스를 선택 합니다. 메뉴의 **모니터링** 섹션에서 **Insights** 를 선택 하 고 **사용** 을 선택 합니다. 다음 예제에서는 azure 가상 컴퓨터를 보여 주지만 azure virtual machine scale set 또는 Azure Arc의 메뉴는 유사 합니다.

![Vm에 대 한 VM insights 사용](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

가상 컴퓨터가 Log Analytics 작업 영역에 이미 연결 되어 있지 않은 경우 하나를 선택 하 라는 메시지가 표시 됩니다. 이전에 [작업 영역을 만들지](../logs/quick-create-workspace.md)않은 경우에는 구독에 가상 머신 또는 가상 머신 확장 집합이 배포 된 위치에 대 한 기본값을 선택할 수 있습니다. 이 작업 영역은 아직 없는 경우 만들어지고 구성 됩니다. 기존 작업 영역을 선택 하는 경우 VM insights에 대해 아직 구성 되지 않은 것입니다.

> [!NOTE]
> 이전에 VM insights에 대해 구성 되지 않은 작업 영역을 선택 하는 경우 *VMInsights* 관리 팩이이 작업 영역에 추가 됩니다. 이는 VM insights에 대해 사용 하도록 설정 되었는지 여부에 관계 없이 작업 영역에 이미 연결 된 모든 에이전트에 적용 됩니다. 성능 데이터는 이러한 가상 컴퓨터에서 수집 되 고 *InsightsMetrics* 테이블에 저장 됩니다.

![작업 영역 선택](media/vminsights-enable-portal/select-workspace.png)

구성이 수행 되 면 상태 메시지가 표시 됩니다.

>[!NOTE]
>가상 머신 확장 집합에 수동 업그레이드 모델을 사용 하는 경우 인스턴스를 업그레이드 하 여 설치를 완료 합니다. **인스턴스** 페이지의 **설정** 섹션에서 업그레이드를 시작할 수 있습니다.

![VM insights 모니터링 배포 처리 사용](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>다음 단계

* 검색 된 응용 프로그램 종속성을 보려면 [VM Insights 맵 사용](vminsights-maps.md) 을 참조 하세요. 
* [AZURE vm 성능 보기](vminsights-performance.md) 를 참조 하 여 병목 상태, 전반적인 사용률 및 VM의 성능을 식별 하세요.
