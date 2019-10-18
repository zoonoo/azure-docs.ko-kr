---
title: VM용 Azure Monitor(미리 보기)를 사용하여 앱 종속성을 보는 방법 | Microsoft Docs
description: Map은 VM용 Azure Monitor의 기능입니다. Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색 하 고 서비스 간 통신을 매핑합니다. 이 문서에서는 다양 한 시나리오에서 맵 기능을 사용 하는 방법에 대 한 세부 정보를 제공 합니다.
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
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: 456ed0a48db015d3c95827942a576e6916095131
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515467"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>VM용 Azure Monitor (미리 보기)의 Map 기능을 사용 하 여 응용 프로그램 구성 요소 이해
VM용 Azure Monitor에서 Azure 또는 사용자 환경에서 실행 되는 Windows 및 Linux Vm (가상 머신)에서 검색 된 응용 프로그램 구성 요소를 볼 수 있습니다. 두 가지 방법으로 Vm을 관찰할 수 있습니다. VM에서 직접 맵을 보거나 Azure Monitor에서 맵을 확인 하 여 Vm 그룹에서 구성 요소를 볼 수 있습니다. 이 문서는 이러한 두 가지 보기 방법과 지도 기능을 사용 하는 방법을 이해 하는 데 도움이 됩니다. 

VM용 Azure Monitor를 구성하는 방법에 대한 자세한 내용은 [VM용 Azure Monitor 사용하도록 설정](vminsights-enable-overview.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인
[Azure portal](https://portal.azure.com)에 로그인합니다.

## <a name="introduction-to-the-map-experience"></a>지도 환경 소개
지도 환경에 대해 알아보기 전에 정보를 표시 하 고 시각화 하는 방법을 이해 해야 합니다. VM에서 직접 또는 Azure Monitor에서 맵 기능을 선택 하는 경우 맵 기능은 일관 된 환경을 제공 합니다. 유일한 차이점은 Azure Monitor에서 맵 하나는 다중 계층 응용 프로그램 또는 클러스터의 모든 멤버를 표시 한다는 것입니다.

지도 기능은 다음을 포함 하는 실행 중인 프로세스를 검색 하 여 VM 종속성을 시각화 합니다. 

- 서버 간 활성 네트워크 연결입니다.
- 인바운드 및 아웃 바운드 연결 대기 시간입니다.
- 지정 된 시간 범위 동안 TCP 연결 아키텍처에서 발생 하는 포트입니다.  
 
VM을 확장 하 여 프로세스 세부 정보와 VM과 통신 하는 프로세스만 표시 합니다. 클라이언트 그룹에는 VM에 연결 하는 프런트 엔드 클라이언트 수가 표시 됩니다. 서버 포트 그룹에는 VM이 연결 되는 백 엔드 서버 수가 표시 됩니다. 서버-포트 그룹을 확장 하 여 해당 포트를 통해 연결 되는 서버에 대 한 자세한 목록을 표시 합니다.  

VM을 선택 하면 오른쪽의 **속성** 창에 vm의 속성이 표시 됩니다. 속성에는 운영 체제에서 보고 한 시스템 정보, Azure VM의 속성, 검색 된 연결을 요약 하는 도넛형 차트가 포함 됩니다. 

![속성 창](./media/vminsights-maps/properties-pane-01.png)

창 오른쪽에서 **로그 이벤트** 를 선택 하 여 VM Azure Monitor 전송 된 데이터 목록을 표시 합니다. 이 데이터는 쿼리에 사용할 수 있습니다.  레코드 유형을 선택 하 여 해당 레코드 유형에 대 한 결과가 표시 되는 **로그** 페이지를 엽니다. 또한 VM에 대해 필터링 되는 미리 구성 된 쿼리가 표시 됩니다.  

![이벤트 로그 창](./media/vminsights-maps/properties-pane-logs-01.png)

**로그** 페이지를 닫고 **속성** 창으로 돌아갑니다. 여기에서 **경고** 를 선택 하 여 VM 상태 조건 경고를 확인 합니다. 지도 기능은 Azure 경고와 통합 되어 선택한 시간 범위에서 선택한 서버에 대 한 경고를 표시 합니다. 서버에 현재 경고의 아이콘이 표시 되 고 **컴퓨터 경고** 창에 경고가 나열 됩니다. 

![경고 창](./media/vminsights-maps/properties-pane-alerts-01.png)

지도 기능에 관련 경고가 표시 되도록 하려면 특정 컴퓨터에 적용 되는 경고 규칙을 만듭니다.

- 컴퓨터 별로 경고를 그룹화 하는 절을 포함 합니다 (예: **컴퓨터 간격 1 분**).
- 메트릭에 대 한 경고를 기반으로 합니다.

Azure 경고 및 경고 규칙을 만드는 방법에 대 한 자세한 내용은 [Azure Monitor의 통합 경고](../../azure-monitor/platform/alerts-overview.md)를 참조 하세요.

오른쪽 위 모서리에서 **범례** 옵션은 지도의 기호와 역할을 설명 합니다. 지도를 좀 더 자세히 살펴보고 이동 하려면 오른쪽 아래 모서리에 있는 확대/축소 컨트롤을 사용 합니다. 확대/축소 수준을 설정 하 고 지도를 페이지 크기에 맞출 수 있습니다.  

## <a name="connection-metrics"></a>연결 메트릭
**연결** 창에는 TCP 포트를 통해 VM에서 선택한 연결에 대 한 표준 메트릭이 표시 됩니다. 메트릭에는 응답 시간, 분당 요청, 트래픽 처리량 및 링크가 포함됩니다.  

![연결 창의 네트워크 연결 차트](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>실패한 연결
지도는 프로세스 및 컴퓨터에 대해 실패 한 연결을 보여 줍니다. 빨간색 파선은 클라이언트 시스템이 프로세스 또는 포트에 연결 하지 못했음을 나타냅니다. 종속성 에이전트를 사용 하는 시스템의 경우 에이전트는 실패 한 연결 시도에 대해 보고 합니다. 지도 기능은 연결 설정에 실패 한 TCP 소켓을 관찰 하 여 프로세스를 모니터링 합니다. 이 오류는 방화벽, 클라이언트 또는 서버의 잘못 된 구성 또는 사용할 수 없는 원격 서비스에서 발생할 수 있습니다.

![맵에 실패 한 연결](./media/vminsights-maps/map-group-failed-connection-01.png)

실패 한 연결을 이해 하면 문제를 해결 하 고, 마이그레이션 유효성을 검사 하 고, 보안을 분석 하 고, 서비스의 전반적인 아키텍처를 이해할 수 있습니다. 실패 한 연결이 무해 한 경우도 있지만 종종 문제를 가리킵니다. 예를 들어 장애 조치 (failover) 환경을 갑자기 연결할 수 없거나 클라우드 마이그레이션 후 두 응용 프로그램 계층이 서로 통신할 수 없는 경우 연결이 실패할 수 있습니다.

### <a name="client-groups"></a>클라이언트 그룹
맵에서 클라이언트 그룹은 매핑된 컴퓨터에 연결 하는 클라이언트 컴퓨터를 나타냅니다. 단일 클라이언트 그룹은 개별 프로세스 또는 컴퓨터에 대 한 클라이언트를 나타냅니다.

![맵의 클라이언트 그룹](./media/vminsights-maps/map-group-client-groups-01.png)

클라이언트 그룹에 있는 시스템의 모니터링 되는 클라이언트와 IP 주소를 확인 하려면 해당 그룹을 선택 합니다. 그룹의 내용이 아래에 나타납니다.  

![클라이언트 그룹의 IP 주소 목록입니다.](./media/vminsights-maps/map-group-client-group-iplist-01.png)

그룹에 모니터링 및 모니터링 되지 않는 클라이언트가 포함 된 경우 그룹의 도넛형 차트에서 적절 한 섹션을 선택 하 여 클라이언트를 필터링 할 수 있습니다.

### <a name="server-port-groups"></a>서버-포트 그룹
서버 포트 그룹은 매핑된 컴퓨터에서 인바운드 연결이 있는 서버의 포트를 나타냅니다. 그룹에는 서버 포트와 해당 포트에 연결 된 서버 수의 수가 포함 됩니다. 개별 서버 및 연결을 보려면 그룹을 선택 합니다. 

![맵의 서버 포트 그룹](./media/vminsights-maps/map-group-server-port-groups-01.png)  

그룹에 모니터링 및 모니터링 되지 않는 서버가 포함 된 경우 그룹의 도넛형 차트에서 적절 한 섹션을 선택 하 여 서버를 필터링 할 수 있습니다.

## <a name="view-a-map-from-a-vm"></a>VM에서 맵 보기 

VM에서 직접 VM용 Azure Monitor에 액세스 하려면 다음을 수행 합니다.

1. Azure Portal에서 **Virtual Machines**를 선택합니다. 
2. 목록에서 VM을 선택 합니다. **모니터링** 섹션에서 **Insights (미리 보기)** 를 선택 합니다.  
3. **맵** 탭을 선택합니다.

맵은 지정 된 시간 범위 동안 활성 네트워크 연결을 포함 하는 실행 중인 프로세스 그룹과 프로세스를 검색 하 여 VM의 종속성을 시각화 합니다.  

기본적으로 맵은 최근 30분을 보여줍니다. 이전에 종속성이 표시 되는 방식을 확인 하려는 경우 최대 1 시간의 기록 시간 범위를 쿼리할 수 있습니다. 쿼리를 실행 하려면 왼쪽 위 모퉁이에 있는 **TimeRange** 선택기를 사용 합니다. 예를 들어 인시던트 중 또는 변경 전 상태를 확인 하는 등의 쿼리를 실행할 수 있습니다.  

![직접 VM 맵 개요](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 맵 보기

가상 머신 확장 집합에서 직접 VM용 Azure Monitor에 액세스 하려면 다음을 수행 합니다.

1. Azure Portal에서 **가상 머신 확장 집합**을 선택 합니다.
2. 목록에서 VM을 선택 합니다. 그런 다음 **모니터링** 섹션에서 **Insights (미리 보기)** 를 선택 합니다.  
3. **맵** 탭을 선택합니다.

맵은 확장 집합의 모든 인스턴스를 그룹의 종속성과 함께 그룹 노드로 시각화 합니다. 확장 된 노드에 확장 집합의 인스턴스가 나열 됩니다. 이러한 인스턴스를 한 번에 10 개까지 스크롤할 수 있습니다. 

특정 인스턴스에 대 한 맵을 로드 하려면 먼저 맵에서 해당 인스턴스를 선택 합니다. 그런 다음 오른쪽에 있는 **줄임표** 단추 (...)를 선택 하 고 **서버 맵 로드**를 선택 합니다. 표시 되는 맵에는 지정 된 시간 범위 동안 활성 네트워크 연결을 포함 하는 프로세스 그룹 및 프로세스가 표시 됩니다. 

기본적으로 맵은 최근 30분을 보여줍니다. 이전에 종속성이 표시 되는 방식을 확인 하려는 경우 최대 1 시간의 기록 시간 범위를 쿼리할 수 있습니다. 쿼리를 실행 하려면 **TimeRange** 선택기를 사용 합니다. 예를 들어 인시던트 중 또는 변경 전 상태를 확인 하는 등의 쿼리를 실행할 수 있습니다.

![직접 VM 맵 개요](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>가상 머신 확장 집합에 대 한 **인스턴스** 보기에서 특정 인스턴스에 대 한 맵에 액세스할 수도 있습니다. **설정** 섹션에서 **인스턴스**  > **Insights (미리 보기)** 로 이동 합니다.

## <a name="view-a-map-from-azure-monitor"></a>Azure Monitor에서 맵 보기

Azure Monitor 맵 기능은 Vm 및 해당 종속성에 대 한 글로벌 보기를 제공 합니다. Azure Monitor에서 맵 기능에 액세스 하려면 다음을 수행 합니다.

1. Azure Portal에서 **모니터**를 선택합니다. 
2. **Insights** 섹션에서 **Virtual Machines (미리 보기)** 를 선택 합니다.
3. **맵** 탭을 선택합니다.

   ![여러 Vm의 Azure Monitor 개요 맵](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

페이지 맨 위에 있는 **작업 영역** 선택기를 사용 하 여 작업 영역을 선택 합니다. 둘 이상의 Log Analytics 작업 영역이 있는 경우 솔루션과 함께 사용 하도록 설정 된 작업 영역을 선택 하 고 Vm에 보고 하는 작업 영역을 선택 합니다. 

**그룹** 선택기는 선택한 작업 영역과 관련 된 컴퓨터의 구독, 리소스 그룹, [컴퓨터 그룹](../../azure-monitor/platform/computer-groups.md)및 가상 컴퓨터 크기 집합을 반환 합니다. 선택 항목은 지도 기능에만 적용 되며 성능 또는 상태에 대해 전달 되지 않습니다.

기본적으로 맵은 최근 30분을 보여줍니다. 이전에 종속성이 표시 되는 방식을 확인 하려는 경우 최대 1 시간의 기록 시간 범위를 쿼리할 수 있습니다. 쿼리를 실행 하려면 **TimeRange** 선택기를 사용 합니다. 예를 들어 인시던트 중 또는 변경 전 상태를 확인 하는 등의 쿼리를 실행할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

병목 상태를 식별 하 고 성능을 확인 하 고 Vm의 전반적인 사용률을 이해 하려면 [VM용 Azure Monitor에 대 한 성능 상태 보기](vminsights-performance.md)를 참조 하세요. 
