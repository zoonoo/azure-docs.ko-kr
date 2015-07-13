<properties 
	pageTitle="Azure 미리 보기 포털을 사용하여 Azure 데이터 팩터리 모니터링 및 관리" 
	description="Azure 관리 포털을 사용하여 사용자가 만든 Azure 데이터 팩터리를 모니터링하고 관리하는 방법에 대해 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Azure 미리 보기 포털을 사용하여 Azure 데이터 팩터리 모니터링
이 문서에서는 Azure Preview 포털을 사용하여 Azure 데이터 팩터리를 모니터링 및 관리하는 다양한 시나리오를 설명합니다.

## <a name="AllDataFactories"></a> Azure 구독에서 모든 데이터 팩터리 보기

- [Azure 미리 보기 포털][azure-preview-portal]에 로그인합니다.
- 왼쪽의 **찾아보기** 허브를 클릭하고 **데이터 팩터리**를 클릭합니다.  

	![찾아보기 허브 -> 데이터 팩터리][image-data-factory-browse-datafactories]

	**데이터 팩터리**가 표시되지 않으면 **모두**를 클릭한 다음 **찾아보기** 블레이드에서 **데이터 팩터리**를 클릭합니다.

	![찾아보기 허브 -> 모두][image-data-factory-browse-everything]

- **데이터 팩터리** 블레이드에 모든 데이터 팩터리가 표시됩니다.

	![데이터 팩터리 블레이드][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> 데이터 팩터리에 대한 세부 정보 보기

데이터 팩터리에 대한 세부 정보를 보려면 다음 중 하나를 수행합니다.


- 위에 표시된 **데이터 팩터리** 블레이드에서 데이터 팩터리를 클릭합니다.
- **시작 보드**에서 데이터 팩터리에 대한 링크를 클릭합니다. **시작 보드**는 Azure Preview 포털에 로그인할 때 표시되는 블레이드입니다. 데이터 팩터리를 만들 때 **시작 보드에 추가**(기본 옵션)를 선택한 경우 다음 이미지에 표시된 대로 시작 보드에 데이터 팩터리 링크가 표시됩니다. 이 예제에서는 **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** 및 **LogProcessingFactory** 데이터 팩터리 링크를 **시작 보드**에서 사용할 수 있습니다.


![시작 보드의 데이터 팩터리][image-data-factory-datafactory-from-startboard]

어떤 방법을 사용하든 다음 이미지에 표시된 대로 선택한 데이터 팩터리의 **데이터 팩터리** 블레이드가 표시됩니다.

 ![데이터 팩터리 홈페이지][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> 데이터 팩터리에 대한 다이어그램 뷰 보기
데이터 팩터리의 **데이터 팩터리** 블레이드에서 **다이어그램** 타일을 클릭하여 데이터 팩터리의 다이어그램 뷰를 표시합니다.

![데이터 팩터리 다이어그램 뷰][image-data-factory-diagram-view]
 
### 다이어그램 뷰에서 파이프라인 열기
다이어그램 뷰에서 파이프라인을 마우스 오른쪽 단추로 클릭하고 **파이프라인 열기**를 클릭하여 파이프라인의 모든 작업을 볼 수 있습니다. 작업에 대한 입력 및 출력 데이터 집합과 함께 파이프라인의 작업이 표시됩니다. ![파이프라인 열기](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

왼쪽 위의 이동 경로에서 **데이터 팩터리**를 클릭하여 다이어그램 뷰로 돌아갑니다. 다이어그램 뷰에 모든 파이프라인이 표시됩니다. 이 예제에서는 하나의 파이프라인만 만들었습니다.

## <a name="DataFactoryLinkedServices"></a> 데이터 팩터리의 연결된 서비스 보기
데이터 팩터리의 **데이터 팩터리** 블레이드에서 **연결된 서비스** 타일을 클릭하여 연결된 서비스를 모두 목록에 표시합니다.

![연결된 서비스 블레이드][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> 연결된 서비스에 대한 세부 정보 보기
**연결된 서비스** 블레이드의 목록에서 연결된 서비스를 클릭하여 서비스에 대한 세부 정보를 표시합니다.

![연결된 서비스 블레이드][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> 데이터 팩터리의 데이터 집합 보기 
데이터 팩터리에 대한 **데이터 팩터리** 블레이드에서 **데이터 집합** 타일을 클릭하여 데이터 팩터리의 테이블을 모두 표시합니다.

![데이터 집합 블레이드][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> 데이터 집합에 대한 세부 정보 보기
데이터 집합 블레이드의 데이터 집합 목록에서 데이터 집합을 클릭하여 데이터 집합에 대한 세부 정보를 표시합니다. 테이블은 스키마가 있는 사각형 데이터 집합입니다. 현재 이 데이터 집합 유형만 지원됩니다.

![테이블 블레이드][image-data-factory-table]

위의 **테이블** 블레이드에서 **Recently updated slices(최근에 업데이트된 조각)** 및 **Recently failed slices(최근에 실패한 조각)** 목록은 둘 다 **마지막 업데이트 시간**을 기준으로 정렬됩니다. 조각의 업데이트 시간은 다음과 같은 상황에서 변경됩니다.

-  **Set-AzureDataFactorySliceStatus**를 사용하거나 조각의 **조각** 블레이드에서 **실행**을 클릭하여 수동으로 조각 상태를 업데이트합니다.
-  실행(예: 실행 시작, 실행 종료 및 실패, 실행 종료 및 성공 등)으로 인해 조각 상태가 변경됩니다.
 
	
조각 시작/종료 시간을 기준으로 정렬된 데이터 조각을 보려면 **데이터 조각(조각 시간별)** 타일을 클릭합니다.
 
![조각 시간별 데이터 조각][DataSlicesBySliceTime]

목록의 제목 또는 **...(줄임표)**을 클릭하여 더 큰 조각 목록을 표시합니다.

![테이블의 모든 조각][image-data-factory-all-slices]

**데이터 조각** 블레이드에서 **필터** 단추를 클릭하면 검토하려는 특정 조각이 표시되도록 조각을 **필터링**할 수 있는 **필터** 블레이드가 표시됩니다. 조각이 **업데이트 시간을 기준으로 정렬된** **데이터 조각** 블레이드에서 **필터**를 클릭하면 다음 예와 비슷한 블레이드가 표시됩니다.

![필터 블레이드][image-data-factory-filter-blade]

**필터** 블레이드에서는 **마지막 업데이트 시간** 및 **조각 상태**를 기준으로 필터링할 수 있습니다. 다음 표에는 모든 조각 상태 및 해당 설명이 설명되어 있습니다.
 
조각 상태 | 설명
------------ | ------------
PendingExecution | 데이터 처리가 아직 시작되지 않았습니다.
InProgress | 데이터 처리가 진행 중입니다.
Ready | 데이터 처리가 완료되었고 데이터 조각이 준비되었습니다.
Failed | 조각을 생성하는 실행을 수행하지 못했습니다.
Skip | 조각의 처리를 건너뜁니다.
Retry | 조각을 생성하는 실행을 다시 시도합니다.
Timed Out | 조각의 데이터 처리 시간이 초과되었습니다.
PendingValidation | 데이터 조각을 처리하기 전에 유효성 검사 정책에 따라 유효성 검사가 완료될 때까지 기다리는 중입니다.
RetryValidation | 조각의 유효성 검사를 다시 시도합니다..
FailedValidation | 조각의 유효성 검사에 실패했습니다.
LongRetry | JSON 테이블에서 LongRetry를 지정하고 조각에 대한 일반 재시도가 실패한 경우 조각은 이 상태가 됩니다.
ValidationInProgress | JSON 테이블에 정의된 정책에 따라 조각에 대한 유효성 검사를 수행하는 중입니다.

조각이 **조각 시간을 기준으로 정렬된** **데이터 조각** 블레이드에서 **필터**를 클릭하면 다른 유형의 **필터** 블레이드가 표시됩니다.

![필터 블레이드 2][image-data-factory-filter-blade-2]


**필터** 블레이드를 시작하면 **끝** 필드가 자동으로 최근 시간(반올림됨)으로 설정되어 반환되는 레코드 수를 제한합니다. **시작** 필드도 자동으로 설정됩니다. **달력** 단추를 클릭하여 **시작** 날짜를 변경할 수 있습니다. **시작** 날짜를 변경하면 **끝** 날짜는 자동으로 변경됩니다.

**이전**/**다음** 단추를 클릭하여 이전 기간/다음 기간의 조각을 볼 수 있습니다. **이전** 및 **다음** 단추의 시간 범위는 다음 표에 표시된 대로 조각 빈도 및 간격을 기반으로 설정됩니다.

Frequency(빈도) | 간격 값 범위 | 결과 시간 청크
----------| -------------------- | --------------------
1 분 | 1-4 | 6 시간
1 분 | 5-29 | 1일
1 분 | 30-180 | 7 일
1 분 | 180 + | 28 일 (대략적인. 달력의 월)
시간 | 1-3 | 7 일
시간 | 4-11 | 28 일 (대략적인. 달력의 월)
시간 | 12-72 | 182 일 (근사치입니다. 6개월)
시간 | 73 + | 1 년
일 | 1-6 | 1 년
일 | 7-20 | 5 년
일 | 21 + | 10 년
주 | 1-3 | 5 년
주 | 4 + | 10 년
월 | 모든 | 10 년
 
예를 들어 **빈도**를 **시간**으로 정의하고 **간격**을 **2**로 정의한 경우 **다음**/**이전** 단추를 클릭하면 시간 범위가 양쪽 방향으로 **7일** 이동합니다. 이 논리는 모든 조각/최근 조각/문제 조각 중 어느 조각을 보는지와 관계없이 필터 블레이드에 적용됩니다.




## <a name="DataFactorySlice"></a> 조각에 대한 세부 정보 보기
**테이블** 블레이드 또는 **데이터 조각** 블레이드의 조각 목록에서 조각을 클릭하여 해당 조각에 대한 세부 정보를 표시합니다.

![데이터 조각][image-data-factory-dataslice]

조각이 **Ready** 상태가 아닌 경우 **Upstream slices that are not ready(준비되지 않은 업스트림 조각)** 목록에서 Ready 상태가 아니고 현재 조각의 실행을 차단하는 업스트림 조각을 확인할 수 있습니다.

### <a name="DataFactoryActivtyRuns"></a> 조각에 대한 모든 작업 실행 보기
조각에 대한 실행이 둘 이상 있을 수 있습니다. 예를 들어 조각이 실패하는 경우 서비스에서 몇 번 다시 시도할 수 있습니다. 다시 시도가 모두 실패한 조각을 다시 실행할 수도 있습니다. **데이터 조각** 블레이드의 맨 아래 목록에서 작업 실행을 모두 볼 수 있습니다.

## <a name="DataFactoryActivtyRunDetails"></a> 실행 하는 활동에 대 한 세부 정보 보기
**데이터 조각** 블레이드의 실행 목록에서 작업 실행을 클릭하여 해당 작업 실행에 대한 세부 정보를 표시합니다.

![작업 실행 세부 정보][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> 작업 렌즈 - 지난주의 이벤트
데이터 팩터리의 **데이터 팩터리** 블레이드 또는 홈페이지에 있는 **작업** 렌즈에서 **Events in the past week(지난 주의 이벤트)**를 클릭하여 지난 주의 이벤트를 표시합니다. 그러면 지난 주에 데이터 팩터리에서 수행한 작업을 개략적으로 볼 수 있습니다. 또한 데이터 이동/처리 관련 오류를 해결하는 데에도 도움이 됩니다.

![데이터 팩터리 이벤트][image-data-factory-events]


## 참고 항목

문서 | 설명
------ | ---------------
[PowerShell을 사용하여 Azure 데이터 팩터리 모니터링 및 관리][monitor-manage-using-powershell] | 이 문서에서는 Azure PowerShell cmdlet을 사용하여 Azure 데이터 팩터리를 모니터링하는 방법을 설명합니다. 


[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-filter-blade-2]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade2.png


[image-data-factory-browse-everything]: ./media/data-factory-monitor-manage-using-management-portal/BrowseEverything.png

[image-data-factory-browse-datafactories]: ./media/data-factory-monitor-manage-using-management-portal/BrowseDataFactories.png

[image-data-factory-datafactories-blade]: ./media/data-factory-monitor-manage-using-management-portal/DataFactories.png

[image-data-factory-datafactory-from-startboard]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryFromStartboard.png

[image-data-factory-datafactory-home-page]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryHomePage.png

[image-data-factory-diagram-view]: ./media/data-factory-monitor-manage-using-management-portal/DiagramView.png

[image-data-factory-linked-services]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServicesBlade.png

[image-data-factory-linked-service]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServiceBlade.png

[image-data-factory-datasets]: ./media/data-factory-monitor-manage-using-management-portal/Datasets.png

[image-data-factory-table]: ./media/data-factory-monitor-manage-using-management-portal/Table.png

[image-data-factory-all-slices]: ./media/data-factory-monitor-manage-using-management-portal/AllSlices.png

[image-data-factory-filter]: ./media/data-factory-monitor-manage-using-management-portal/Filter.png

[image-data-factory-dataslice]: ./media/data-factory-monitor-manage-using-management-portal/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-monitor-manage-using-management-portal/ActivityRunDetails.png

[image-data-factory-events]: ./media/data-factory-monitor-manage-using-management-portal/Events.png
[DataSlicesBySliceTime]: ./media/data-factory-monitor-manage-using-management-portal/DataSlicesBySliceTime.png

<!---HONumber=62-->