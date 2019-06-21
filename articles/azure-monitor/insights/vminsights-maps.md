---
title: VM용 Azure Monitor(미리 보기)를 사용하여 앱 종속성을 보는 방법 | Microsoft Docs
description: 지도는 Vm에 대 한 Azure Monitor의 기능입니다. 자동으로 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 검색 하 고 서비스 간 통신을 매핑합니다. 이 문서에서는 다양 한 시나리오에서 지도 기능을 사용 하는 방법에 자세하게 설명 합니다.
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
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: f6273e9b6c7ed0c4685479976343497f01201b0b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206753"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>응용 프로그램 구성 요소를 이해 하려면 Vm (미리 보기)에 대 한 Azure Monitor의 지도 기능 사용
Vm에 대 한 Azure Monitor에서 Windows 및 Linux 가상 머신에서 (Vm) Azure 또는 사용자 환경에서 실행 되는 검색 된 응용 프로그램 구성 요소를 볼 수 있습니다. 두 가지 방법으로 Vm을 확인할 수 있습니다. VM에서 직접 지도 또는 지도 Vm 그룹에서 구성 요소를 확인 하려면 Azure Monitor에서. 이 문서에서는 이러한 두 보기 메서드와 맵 기능을 사용 하는 방법을 이해 하는 데 도움이 됩니다. 

VM용 Azure Monitor를 구성하는 방법에 대한 자세한 내용은 [VM용 Azure Monitor 사용하도록 설정](vminsights-enable-overview.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인
[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="introduction-to-the-map-experience"></a>지도 환경 소개
지도 환경으로 살펴보기에 소개 하며 정보를 시각화 하는 방법을 알아야 합니다. VM에서 직접 또는 Azure Monitor에서 맵 기능을 선택 하는지 여부를 맵 기능을 일관 된 환경을 제공 합니다. 유일한 차이점은 하는 Azure Monitor에서 하나의 맵을 다중 계층 응용 프로그램 또는 클러스터의 모든 멤버를 보여 줍니다.

실행 중인 프로세스를 검색 하 여 VM 종속성을 시각화 하는 지도 기능: 

- 서버 간에 활성 네트워크 연결입니다.
- 인바운드 및 아웃 바운드 연결 대기 시간입니다.
- 지정 된 시간 범위 동안 TCP 연결 아키텍처의 포트입니다.  
 
프로세스 정보 및 VM과 통신 하는 프로세스만 표시 하도록 VM을 확장 합니다. 클라이언트 그룹의 VM에 연결 하는 프런트 엔드 클라이언트의 수를 보여 줍니다. 서버 포트 그룹 VM에 연결 하는 백 엔드 서버의 수를 표시 합니다. 자세한 해당 포트를 통해 연결 되는 서버 목록은 서버 포트 그룹을 확장 합니다.  

VM을 선택 합니다 **속성** 오른쪽 창에서 VM의 속성을 보여 줍니다. 속성에는 Azure VM 및 검색 된 연결을 요약 하는 도넛형 차트의 속성을 운영 체제에서 보고 한 시스템 정보가 포함 됩니다. 

![속성 창](./media/vminsights-maps/properties-pane-01.png)

창의 오른쪽에서 선택 **로그 이벤트** VM에 Azure Monitor로 전송 된 데이터의 목록을 표시 합니다. 이 데이터는 쿼리에 사용할 수 있습니다.  열려는 레코드 종류를 선택 합니다 **로그** 페이지에서 해당 레코드 종류에 대 한 결과 표시 합니다. VM에 대해 필터링 되는 미리 구성 된 쿼리를 볼 수도 있습니다.  

![로그 이벤트 창](./media/vminsights-maps/properties-pane-logs-01.png)

닫기는 **로그** 페이지 및 반환 된 **속성** 창. 여기에서 선택 **경고** VM 상태 조건을 경고 보기에 있습니다. 선택한 시간 범위에서 선택한 서버에 대 한 경고를 표시 하는 Azure Alerts와 맵 기능 통합 됩니다. 현재 경고에 대 한 아이콘을 표시 하는 서버 및 **머신 경고** 창에 경고가 나열 합니다. 

![경고 창](./media/vminsights-maps/properties-pane-alerts-01.png)

관련 경고를 표시 합니다. 지도 기능을 하려면 특정 컴퓨터에 적용 되는 경고 규칙을 만듭니다.

- 컴퓨터에서 경고를 그룹화 하려면 절을 포함 (예를 들어 **Computer interval 1minute 여**).
- 메트릭 경고를 기반 합니다.

Azure 경고 및 경고 규칙 만들기에 대 한 자세한 내용은 참조 하세요. [Azure Monitor에서 경고를 통합](../../azure-monitor/platform/alerts-overview.md)합니다.

오른쪽 위 모서리에는 **범례** 맵에서 기호 및 역할 옵션에 설명 합니다. 자세히 보기 위해 지도 및 관련 이동, 오른쪽 아래 모퉁이에서 확대/축소 컨트롤을 사용 합니다. 확대/축소 수준을 설정 하 고 맵을 페이지의 크기에 맞추려면 수 있습니다.  

## <a name="connection-metrics"></a>연결 메트릭
합니다 **연결** 창에는 TCP 포트를 통해 VM에서 선택한 연결에 대 한 표준 메트릭을 표시 됩니다. 메트릭에는 응답 시간, 분당 요청, 트래픽 처리량 및 링크가 포함됩니다.  

![연결 창에서 네트워크 연결 차트](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>실패한 연결
Map은 프로세스 및 컴퓨터에 대 한 실패 한 연결을 보여 줍니다. 빨간색 파선 클라이언트 시스템이 프로세스 또는 포트에 도달할 수 실패 하는 것을 나타냅니다. 종속성 에이전트를 사용 하는 시스템의 경우 에이전트 실패 한 연결 시도에 보고 합니다. 연결에 실패 한 TCP 소켓을 관찰 하 여 프로세스를 모니터링 하는 지도 기능 합니다. 이 오류는 방화벽, 클라이언트 또는 서버 또는 원격 서비스를 사용할 수 없는 잘못 된 구성이 발생할 수 있습니다.

![맵에서 실패 한 연결](./media/vminsights-maps/map-group-failed-connection-01.png)

실패 한 연결은 도움이 이해 문제를 해결, 마이그레이션 유효성 검사, 보안, 분석 및 서비스의 전반적인 아키텍처를 이해 합니다. 실패 한 연결 문제가 되기도 하지만 문제가 경우가 많습니다. 예를 들어 두 응용 프로그램 계층이 클라우드 마이그레이션 후 서로 통신할 수 없는 경우 또는 장애 조치 환경에 갑자기 연결할 수 없는 경우 연결이 실패할 수 있습니다.

### <a name="client-groups"></a>클라이언트 그룹
클라이언트 그룹은 맵에서 매핑된 컴퓨터에 연결 된 클라이언트 컴퓨터를 나타냅니다. 단일 클라이언트 그룹은 개별 프로세스 및 컴퓨터에 대 한 클라이언트를 나타냅니다.

![지도에 클라이언트 그룹](./media/vminsights-maps/map-group-client-groups-01.png)

모니터링 되는 클라이언트 및 클라이언트 그룹에 있는 시스템의 IP 주소를 확인 하려면 그룹을 선택 합니다. 그룹의 내용을 아래에 나타납니다.  

![지도에 IP 주소의 클라이언트 그룹의 목록](./media/vminsights-maps/map-group-client-group-iplist-01.png)

그룹을 모니터링 하 고 모니터링 되지 않는 클라이언트에 포함 된 경우에 클라이언트를 필터링 할 그룹의 도넛형 차트의 해당 섹션을 선택할 수 있습니다.

### <a name="server-port-groups"></a>서버 포트 그룹
서버 포트 그룹에 있는 서버에서 포트를 나타내는 매핑된 컴퓨터에서 인바운드로 연결 합니다. 그룹에는 서버 포트와 해당 포트에 연결 해야 하는 서버 수가 포함 되어 있습니다. 개별 서버 및 연결을 보려는 그룹을 선택 합니다. 

![지도에 서버 포트 그룹](./media/vminsights-maps/map-group-server-port-groups-01.png)  

그룹을 모니터링 하 고 모니터링 되지 않는 서버에 포함 된 경우에 서버를 필터링 할 그룹의 도넛형 차트의 해당 섹션을 선택할 수 있습니다.

## <a name="view-a-map-from-a-vm"></a>VM에서 지도 보려면 

에 액세스 하려면 Azure Monitor는 VM에서 직접 vm:

1. Azure Portal에서 **Virtual Machines**를 선택합니다. 
2. 목록에서 VM을 선택 합니다. 에 **모니터링** 섹션을 선택 **Insights (미리 보기)** 합니다.  
3. **맵** 탭을 선택합니다.

맵을 검색 하 여 VM의 종속성을 시각화 프로세스 그룹 및 지정 된 시간 범위 동안 활성 네트워크 연결을 가진 프로세스를 실행 합니다.  

기본적으로 맵은 최근 30분을 보여줍니다. 과거에 종속성을 조회 하는 방법을 참조 하려는 경우에 최대 1 시간의 기록 시간 범위에 대해 쿼리할 수 있습니다. 쿼리를 실행 하려면 사용 합니다 **TimeRange** 왼쪽 위 모퉁이에서 선택기입니다. 예: 인시던트 중 또는 변경 하기 전에 상태를 보려면 쿼리를 실행할 수 있습니다.  

![직접 VM 맵 개요](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 지도 보려면

Azure Monitor vm에서 직접 액세스 하는 가상 머신 확장 집합:

1. Azure portal에서 선택 **가상 머신 확장 집합**합니다.
2. 목록에서 VM을 선택 합니다. 그런 다음 합니다 **모니터링** 섹션을 선택 **Insights (미리 보기)** 합니다.  
3. **맵** 탭을 선택합니다.

맵을 확장 그룹의 종속성과 함께 그룹 노드 집합의 모든 인스턴스를 시각화 합니다. 확장 된 노드는 확장 집합의 인스턴스를 나열합니다. 한 번에 10 이러한 인스턴스를 통해 스크롤할 수 있습니다. 

특정 인스턴스에 대 한 지도 로드 하려면 먼저 맵의 해당 인스턴스를 선택 합니다. 선택한 합니다 **줄임표** 오른쪽에 있는 단추 (...) 선택한 **서버 맵 로드**합니다. 표시 되는 지도 프로세스 그룹 및 지정 된 시간 범위 동안 활성 네트워크 연결을 가진 프로세스를 볼 수 있습니다. 

기본적으로 맵은 최근 30분을 보여줍니다. 과거에 종속성을 조회 하는 방법을 참조 하려는 경우에 최대 1 시간의 기록 시간 범위에 대해 쿼리할 수 있습니다. 쿼리를 실행 하려면 사용 합니다 **TimeRange** 선택기입니다. 예: 인시던트 중 또는 변경 하기 전에 상태를 보려면 쿼리를 실행할 수 있습니다.

![직접 VM 맵 개요](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>특정 인스턴스에 대 한 지도 액세스할 수도 있습니다는 **인스턴스** 가상 머신 확장 집합에 대 한 보기입니다. 에 **설정을** 섹션으로 이동 **인스턴스** > **Insights (미리 보기)** 합니다.

## <a name="view-a-map-from-azure-monitor"></a>Azure Monitor에서 지도 보려면
Azure Monitor에서 맵 기능 Vm 및 해당 종속성의 글로벌 보기를 제공합니다. 에 액세스 하려면 Azure Monitor에서 맵 기능:

1. Azure Portal에서 **모니터**를 선택합니다. 
2. 에 **Insights** 섹션을 선택 **가상 머신 (미리 보기)** 합니다.
3. **맵** 탭을 선택합니다.

   ![여러 Vm의 azure Monitor 개요 맵](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

사용 하 여 작업 영역을 선택 합니다 **작업 영역** 페이지의 맨 위에 있는 선택기입니다. Log Analytics 작업 영역이 둘 이상인 경우 솔루션으로 사용 되 고 보고 하는 Vm이 있는 작업 영역을 선택 합니다. 

합니다 **그룹** 선택기 구독, 리소스 그룹을 반환 합니다. [컴퓨터 그룹](../../azure-monitor/platform/computer-groups.md), 및 선택한 작업 영역에 관련 된 컴퓨터의 가상 머신 확장 집합입니다. 선택 맵 기능에만 적용 하 고 성능 또는 상태를 전달 하지 않습니다.

기본적으로 맵은 최근 30분을 보여줍니다. 과거에 종속성을 조회 하는 방법을 참조 하려는 경우에 최대 1 시간의 기록 시간 범위에 대해 쿼리할 수 있습니다. 쿼리를 실행 하려면 사용 합니다 **TimeRange** 선택기입니다. 예: 인시던트 중 또는 변경 하기 전에 상태를 보려면 쿼리를 실행할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
- 상태 기능을 사용 하는 방법에 알아보려면 참조 [보기 Azure VM 상태](vminsights-health.md)합니다. 
- 병목 상태를 식별 하려면 성능을 확인 하 고 Vm의 전체 사용률을 이해, 참조 [Vm에 대 한 Azure Monitor에 대 한 성능 상태를 보려면](vminsights-performance.md)합니다. 
