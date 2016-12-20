---
title: "Azure Data Factory 파이프라인 모니터링 및 관리"
description: "모니터링 및 관리 앱을 사용하여 Azure 데이터 팩터리 및 파이프라인을 모니터링하고 관리하는 방법을 알아봅니다."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 0bb9269d20c157221faa5604e68acc3410a5247b
ms.openlocfilehash: d8e5e8e99d40af959f8fa1dd4bf7b636a9f2343b


---
# <a name="monitor-and-manage-azure-data-factory-pipelines-using-new-monitoring-and-management-app"></a>새 모니터링 및 관리 앱을 사용하여 Azure 데이터 팩터리 파이프라인 모니터링 및 관리
> [!div class="op_single_selector"]
> * [Azure 포털/Azure PowerShell 사용](data-factory-monitor-manage-pipelines.md)
> * [모니터링 및 관리 앱 사용](data-factory-monitor-manage-app.md)
> 
> 

이 문서는 **모니터링 및 관리 앱**을 사용하여 파이프라인을 모니터링하고 관리하고 디버그하며, 오류에 대한 알림을 받도록 경고를 만드는 방법을 설명합니다. 모니터링 및 관리 앱에 대해 자세히 알아보려면 다음 동영상을 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
> 
> 

## <a name="launching-the-monitoring-and-management-app-a"></a>모니터링 및 관리 앱 시작
모니터 및 관리 앱을 시작하려면 데이터 팩터리에 대한 **데이터 팩터리** 블레이드에서 **모니터링 및 관리** 타일을 클릭합니다.

![데이터 팩터리 홈 페이지에서 모니터링 타일](./media/data-factory-monitor-manage-app/MonitoringAppTile.png) 

별도 탭/창에서 시작된 모니터링 및 관리 앱이 표시됩니다.  

![모니터링 및 관리 앱](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> 웹 브라우저가 "권한 부여..." 상태로 중지된 것을 확인하면 **타사 쿠키 및 사이트 데이터 차단** 설정을 사용 안 함/선택 취소하고 (또는) 계속 사용하지 않습니다. 그리고 **login.microsoftonline.com**에 대한 예외를 만든 다음 앱을 다시 시작해봅니다.
> 
> 

맨 아래 목록에 활동 창이 표시되지 않으면 도구 모음에서 **새로 고침** 단추를 클릭하여 목록을 새로 고칩니다. 또한 **시작 시간** 및 **종료 시간** 필터에 대한 올바른 값을 설정합니다.  

## <a name="understanding-the-monitoring-and-management-app"></a>모니터링 및 관리 앱 이해
왼쪽에 **리소스 탐색기**, **모니터링 보기** 및 **경고** 등 세 개의 탭이 있으며 첫 번째 탭(리소스 탐색기)이 기본적으로 선택됩니다. 

### <a name="resource-explorer"></a>리소스 탐색기
다음이 표시됩니다. 

* 왼쪽 창에 리소스 탐색기 **트리 뷰** .
* **다이어그램 뷰** .
* **작업 창** 목록.
* **속성**/**작업 창 탐색기** 탭. 

리소스 탐색기에서 데이터 팩터리의 모든 리소스(파이프라인, 데이터 집합, 연결된 서비스)를 트리 뷰로 볼 수 있습니다. 리소스 탐색기에서 개체를 선택하는 경우 다음을 알려줍니다. 

* 연결된 데이터 팩터리 엔터티는 다이어그램 뷰에 강조 표시됩니다.
* 연관된 작업 창( [여기](data-factory-scheduling-and-execution.md) 를 클릭하여 작업 창에 대해 알아봅니다)은 맨 아래에 있는 작업 창 목록에서 강조 표시됩니다.  
* 오른쪽 창의 속성 창에서 선택한 개체의 속성입니다. 
* 해당하는 경우 선택된 개체의 JSON 정의. 예: 연결된 서비스나 데이터 집합 또는 파이프라인 

![리소스 탐색기](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

작업 창에 대한 자세한 개념 정보는 [예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요. 

### <a name="diagram-view"></a>다이어그램 뷰
데이터 팩터리의 다이어그램 뷰에는 데이터 팩터리와 그 자산을 모니터링하고 관리하기 위한 단일 돋보기 창이 제공됩니다. 다이어그램 뷰에서 Data Factory 엔터티(데이터 집합/파이프라인)를 선택하면 다음을 알려줍니다.

* 데이터 팩터리 엔터티를 트리 보기에서 선택합니다
* 연관된 작업 창이 작업 창 목록에 강조 표시됩니다.
* 속성 창에서 선택한 개체의 속성입니다.

파이프라인을 사용하도록 설정한 경우(일시 중지된 상태가 아님) 녹색 선으로 표시됩니다. 

![파이프라인 실행](./media/data-factory-monitor-manage-app/PipelineRunning.png)

다이어그램 뷰에서 파이프라인에 대한 세 개의 명령 단추가 있다고 알려줍니다. 두 번째 단추를 사용하여 파이프라인을 일시 중지할 수 있습니다. 일시 중지할 경우 현재 실행 중인 활동을 종료하지 않고 완료될 때까지 계속할 수 있습니다. 세 번째 단추는 파이프라인을 일시 중지하고 실행 중인 기존의 활동을 종료합니다. 첫 번째 단추는 파이프라인을 다시 시작합니다. 파이프라인을 일시 중지하면 다음과 같이 파이프라인 타일에 대한 색상 변경을 확인할 수 있습니다.

![타일에서 일시 중지/다시 시작](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

두 개 이상의 파이프라인을 다중 선택(CTRL 사용)하고 명령 모음 단추를 사용하여 한 번에 여러 파이프라인을 일시 중지/계속할 수 있습니다.

![명령 모음에서 일시 중지/다시 시작](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

파이프라인 타일을 마우스 오른쪽 단추로 클릭하고 **파이프라인 열기**를 클릭하여 파이프라인의 모든 작업을 볼 수 있습니다.

![파이프라인 열기 메뉴](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

열린 파이프라인 뷰에서 파이프라인의 모든 작업이 표시됩니다. 이 예제에서는 하나의 작업, 복사 작업만이 있습니다. 이전 보기로 돌아가려면 맨 위에서 breadcrumb 메뉴의 데이터 팩터리 이름을 클릭합니다.

![열린 파이프라인](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

파이프라인 뷰에서 출력 데이터 집합을 클릭할 경우, 출력 데이터 집합 위로 마우스를 이동하는 경우, 해당 데이터 집합에 작업 창 팝업이 표시됩니다.

![작업 창 팝업](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

오른쪽 창의 **속성** 창에서 이에 대한 자세한 내용을 보려면 작업 창을 클릭할 수 있습니다. 

![작업 창 속성](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

오른쪽 창에서 **작업 창 탐색기** 탭으로 전환하여 자세한 내용을 봅니다.

![작업 창 탐색기](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png) 

**시도** 섹션에서 각 활동 실행 시도에 대한 **확인된 변수**도 볼 수 있습니다. 

![확인된 변수](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

**스크립트** 탭으로 전환하여 선택한 개체에 대한 JSON 스크립트 정의를 참조하세요.   

![스크립트 탭](./media/data-factory-monitor-manage-app/ScriptTab.png)

세 위치에서 작업 창을 확인할 수 있습니다.

* 다이어그램 뷰에서 작업 창 팝업(가운데 창).
* 오른쪽 창에서 작업 창 탐색기.
* 아래쪽 창에서 작업 창 목록.

작업 창 팝업 및 작업 창 탐색기에서 왼쪽 및 오른쪽 화살표를 사용하여 이전 주 및 다음 주로 스크롤할 수 있습니다.

![작업 창 탐색기 왼쪽/오른쪽 화살표](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

다이어그램 뷰의 아래쪽 단추에서 확대, 축소, 크기에 맞게, 100% 확대, 레이아웃 잠금 단추가 표시됩니다. 레이아웃 잠금 단추는 다이어그램 뷰에서 테이블 및 파이프라인을 실수로 이동하지 않도록 방지하며 기본적으로 켜기 상태입니다. 기능을 해제하고 다이어그램에서 엔터티를 이동할 수 있습니다. 해제를 설정한 경우 마지막 단추를 사용하여 테이블 및 파이프라인을 자동으로 배치할 수 있습니다. 또한 마우스 휠을 사용하여 확대/축소할 수 있습니다.

![다이어그램 뷰 확대/축소 명령](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>작업 창 목록
가운데 창의 맨 아래에서 작업 창 목록은 리소스 탐색기 또는 다이어그램 뷰에서 선택한 데이터 집합에 대한 모든 작업 창을 표시합니다. 기본적으로 목록은 내림차순으로 즉, 창 위쪽에 최신 작업을 표시합니다. 

![작업 창 목록](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

이 목록은 자동으로 새로 고치지 않으므로 도구 모음에서 새로 고침 단추를 사용하여 수동으로 새로 고칩니다.  

작업 창은 다음 상태 중 하나일 수 있습니다.

<table>
<tr>
    <th align="left">가동 상태</th><th align="left">하위 상태</th><th align="left">설명</th>
</tr>
<tr>
    <td rowspan="8">대기</td><td>ScheduleTime</td><td>아직 작업 창을 실행할 시간이 되지 않습니다.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>업스트림 종속성이 준비되지 않습니다.</td>
</tr>
<tr>
<td>ComputeResources</td><td>계산 리소스를 사용할 수 없습니다.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>모든 활동 인스턴스는 다른 작업 창을 실행하고 있습니다.</td>
</tr>
<tr>
<td>ActivityResume</td><td>작업은 일시 중지되어 재개될 때까지 작업 창을 실행할 수 없습니다.</td>
</tr>
<tr>
<td>Retry</td><td>작업 실행을 다시 시도합니다.</td>
</tr>
<tr>
<td>유효성 검사</td><td>유효성 검사를 아직 시작되지 않았습니다.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>유효성 검사를 재시도하기를 기다리고 있습니다.</td>
</tr>
<tr>
<tr
<td rowspan="2">InProgress</td><td>유효성 검사 중</td><td>유효성 검사가 진행 중입니다.</td>
</tr>
<td>-</td>
<td>작업 창을 처리 중입니다.</td>
</tr>
<tr>
<td rowspan="4">Failed</td><td>TimedOut</td><td>실행이 작업에서 허용하는 것보다 오래 걸렸습니다.</td>
</tr>
<tr>
<td>Canceled</td><td>사용자 동작으로 취소합니다.</td>
</tr>
<tr>
<td>유효성 검사</td><td>유효성 검사가 실패했습니다.</td>
</tr>
<tr>
<td>-</td><td>작업 창을 생성 및/또는 유효성을 검사하지 못했습니다.</td>
</tr>
<td>Ready</td><td>-</td><td>작업 창을 사용할 준비가 되었습니다.</td>
</tr>
<tr>
<td>생략</td><td>-</td><td>작업 창을 처리하지 않습니다.</td>
</tr>
<tr>
<td>없음</td><td>-</td><td>다른 상태와 함께 존재하기 위해 사용되는 작업 창이지만, 재설정되지 않았습니다.</td>
</tr>
</table>


목록에서 작업 창을 클릭하면 오른쪽에 있는 **작업 창 탐색기** 또는 **속성** 창에서 자세한 내용이 표시됩니다.

![작업 창 탐색기](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>작업 창 새로 고침
세부 정보는 자동으로 새로 고쳐지지 않으므로 명령 모음에서 **새로 고침** 단추(두 번째 단추)를 사용하여 수동으로 작업 창 목록을 새로 고칩니다.  

### <a name="properties-window"></a>속성 창
속성 창은 모니터링 및 관리 앱의 가장 오른쪽 창에 있습니다. 

![속성 창](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

리소스 탐색기(트리 뷰)(또는) 다이어그램 뷰(또는) 작업 창 목록에서 선택한 항목에 대한 속성을 표시합니다. 

### <a name="activity-window-explorer"></a>작업 창 탐색기
**작업 창 탐색기** 창은 모니터링 및 관리 앱의 가장 오른쪽 창에 있습니다. 작업 창 팝업 또는 작업 창 목록에서 선택한 작업 창에 대한 세부 정보를 표시합니다. 

![작업 창 탐색기](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

위쪽의 일정 보기에서 클릭하여 다른 작업 창으로 전환할 수 있습니다. 또한 위쪽에서 **왼쪽 화살표**/**오른쪽 화살표** 단추를 사용하여 이전/다음 주의 작업 창을 볼 수 있습니다.

아래쪽 창에서 도구 모음 단추를 사용하여 작업 창을 **다시 실행**하거나 창에서 세부 정보를 **새로 고칠** 수 있습니다. 

### <a name="script"></a>스크립트
**스크립트** 탭을 사용하여 선택한 Data Factory 엔터티(연결된 서비스, 데이터 집합 및 파이프라인)의 JSON 정의를 볼 수 있습니다. 

![스크립트 탭](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="using-system-views"></a>시스템 뷰 사용
모니터링 및 관리 앱은 데이터 팩터리에 대해 최근/실패한/진행 중인 작업 창을 보여주는 미리 작성된 시스템 뷰를 포함합니다(**최근 작업 창**, **실패한 작업 창**, **진행 중인 작업 창**). 

왼쪽에서 **모니터링 뷰** 탭을 클릭하여 전환합니다. 

![모니터링 뷰 탭](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

현재는 지원되는 세 가지 시스템 뷰가 있습니다. (가운데 창의 맨 위에 있는)작업 창 목록에서 옵션을 선택하여 최근 작업 창 (또는) 실패한 작업 창 (또는) 진행 중인 작업 창을 봅니다. 

**최근 작업 창** 옵션을 선택하면 **마지막 시도 시간**의 내림차순으로 최근 작업 창이 모두 표시됩니다. 

**실패한 작업 창** 뷰를 사용하여 목록에서 실패한 작업 창을 모두 볼 수 있습니다. **속성** 창 또는 **작업 창 탐색기**에서 이에 대한 자세한 내용을 보려면 목록에서 실패한 작업 창을 선택하세요. 또한 실패한 작업 창에 대한 로그를 다운로드할 수 있습니다. 

## <a name="sorting-and-filtering-activity-windows"></a>작업 창 정렬 및 필터링
명령 모음에서 **시작 시간** 및 **종료 시간** 설정을 변경하여 작업 창을 필터링합니다. 시작 시간 및 종료 시간을 변경한 후에 작업 창 목록을 새로 고치려면 종료 시간 옆에 있는 단추를 클릭합니다.

![시작 및 종료 시간](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> 현재는 모니터링 및 관리 앱에서 UTC 형식에 모든 시간이 있습니다. 
> 
> 

**작업 창 목록**에서 열의 이름을 클릭합니다(예: 상태). 

![작업 창 목록 열 메뉴](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

다음을 수행할 수 있습니다.

* 오름차순으로 정렬합니다.
* 내림차순으로 정렬합니다.
* 하나 이상의 값으로 필터링(준비, 대기 등)

열에 필터를 지정할 때 해당 열에 대해 사용하도록 설정된 필터 단추가 표시되어 열의 값이 필터링된 값임을 나타냅니다. 

![작업 창 목록의 열에서 필터링](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

동일한 팝업 창을 사용하여 필터를 지울 수 있습니다. 작업 창 목록에 대한 모든 필터를 지우려면 명령 모음에서 필터 지우기 단추를 클릭합니다. 

![작업 창 목록의 모든 필터 지우기](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="performing-batch-actions"></a>배치 작업 수행
### <a name="rerun-selected-activity-windows"></a>선택한 작업 창 다시 실행
작업 창을 선택하고 첫 번째 명령 모음 단추에 아래쪽 화살표를 클릭한 다음 **다시 실행** / **파이프라인에서 업스트림으로 다시 실행**을 선택합니다. **파이프라인에서 업스트림으로 다시 실행** 옵션을 선택하면 모든 업스트림 작업 창이 다시 실행됩니다. 
    ![작업 창 다시 실행](./media/data-factory-monitor-manage-app/ReRunSlice.png)

또한 목록에서 여러 개의 작업 창을 선택하고 동시에 다시 실행할 수 있습니다. 상태를 기준으로 작업 창을 필터링(예: **실패**)한 다음, 작업 창에 실패를 일으키는 문제를 해결한 후에 실패한 작업 창을 다시 실행할 수 있습니다. 목록에서 작업 창을 필터링하는 자세한 내용은 다음 섹션을 참조합니다.  

### <a name="pauseresume-multiple-pipelines"></a>여러 파이프라인 일시 중지/다시 시작
두 개 이상의 파이프라인을 다중 선택(CTRL 사용)하고 명령 모음 단추(다음 그림에서 빨간색 사각형으로 강조 표시)를 사용하여 한 번에 여러 파이프라인을 일시 중지/계속할 수 있습니다.

![명령 모음에서 일시 중단/다시 시작](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## <a name="creating-alerts"></a>경고 생성
경고 페이지에서 경고를 만들고 기존 경고를 보기/편집/삭제할 수 있습니다. 또한 경고를 사용하지 않거나/사용할 수 있습니다. 경고 페이지를 보려면 경고 탭을 클릭합니다.

![경고 탭](./media/data-factory-monitor-manage-app/AlertsTab.png)

### <a name="to-create-an-alert"></a>경고를 만들려면
1. **경고 추가** 를 클릭하여 경고를 추가합니다. 세부 정보 페이지가 표시됩니다. 
   
    ![경고 만들기 - 세부 정보 페이지](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
2. 경고에 대한 **이름** 및 **설명**을 지정하고 **다음**을 클릭합니다. **필터** 페이지를 참조해야 합니다.
   
    ![경고 만들기 - 필터 페이지](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)
3. Data Factory 서비스에서 경고를 보낼 **이벤트**, **상태** 및 **하위 상태**(선택 사항)를 선택하고 **다음**을 클릭합니다. **받는 사람** 페이지가 표시됩니다.
   
    ![경고 만들기 - 받는 사람 페이지](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png) 
4. **구독 관리자 전자 메일** 옵션을 선택하거나/하고 **추가 관리자 전자 메일**을 입력한 다음 **마침**을 클릭합니다. 목록에서 경고가 표시되어야 합니다. 
   
    ![경고 목록](./media/data-factory-monitor-manage-app/AlertsList.png)

경고 목록에서 경고와 관련된 단추를 사용하여 경고를 편집/삭제/비활성화/활성화합니다. 

### <a name="eventstatussubstatus"></a>이벤트/상태/하위 상태
다음 테이블은 사용 가능한 이벤트 및 상태(및 하위 상태) 목록을 제공합니다.

| 이벤트 이름 | 가동 상태 | 하위 상태 |
| --- | --- | --- |
| 작업 실행 시작 |Started |시작 중 |
| 작업 실행 완료 |Succeeded |Succeeded |
| 작업 실행 완료 |실패 |실패한 리소스 할당<br/><br/>실패한 실행<br/><br/>Timed Out<br/><br/>Failed Validation<br/><br/>Abandoned |
| 주문형 HDI 클러스터 만들기 시작 |Started |-|
| 주문형 HDI 클러스터 성공적으로 생성 |Succeeded |-|
| 주문형 HDI 클러스터 삭제 |Succeeded |-|

### <a name="to-editdeletedisable-an-alert"></a>경고를 편집/삭제/사용 안 함
![경고 단추](./media/data-factory-monitor-manage-app/AlertButtons.png)




<!--HONumber=Nov16_HO3-->


