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
이 문서에서는 Azure 미리 보기 포털을 사용 하 여 모니터링 하 고 Azure 데이터 팩터리를 관리 하는 것에 대 한 다양 한 시나리오를 설명 합니다.

## <a name="AllDataFactories"></a> Azure 구독에서 모든 데이터 팩터리를 보려면

- 에 로그인 된 [Azure 미리 보기 포털][azure-preview-portal].
- 클릭 하 여 **찾아보기** 를클릭하고 왼쪽에는 허브 **데이터 팩터리**.  

	![찾아보기 허브-데이터 팩터리 >][image-data-factory-browse-datafactories]

	표시 되지 않으면 **데이터 팩터리**, 를 클릭 하 여 **모든** 클릭 하 고 **데이터 factorries** 에 **찾아보기** 블레이드.

	![찾아보기 허브를-> 모든 것][image-data-factory-browse-everything]

- 모든 데이터 팩터리 표시는 **데이터 팩터리** 블레이드입니다.

	![데이터 팩터리 블레이드][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> 데이터 팩터리에 대 한 세부 정보 보기

데이터 팩터리에 대한 세부 정보를 보려면 다음 중 하나를 수행합니다.


- 데이터 팩터리를 클릭 하 여는 **데이터 팩터리** 위에 표시 된 블레이드입니다.
- 데이터 팩터리에 대 한 링크를 클릭할는 **시작 보드**. **시작 보드** 블레이드에서 Azure 미리 보기 포털에 로그인 하면 표시 됩니다. 선택한 경우 **시작 보드에 추가** 데이터 팩터리 (기본 옵션)을 만드는 동안 데이터 팩터리 표시 되어야 다음 그림에 표시 된 것 처럼 시작 보드에 연결 합니다. 이 예에서는 **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** 및 **LogProcessingFactory** 데이터 팩터리 연결에서 사용할 수 있는 **시작 보드**.


![시작 보드에서 데이터 팩터리][image-data-factory-datafactory-from-startboard]

그러면 어떤 방법을 사용 하는 **데이터 팩터리** 블레이드 다음 그림에 표시 된 것 처럼 선택한 데이터 팩터리에 대 한 합니다.

 ![데이터 팩터리 홈페이지][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> 데이터 팩터리 보기 다이어그램 보기
에 **데이터 팩터리** 데이터 팩터리에 대 한 블레이드를 클릭 **다이어그램** 타일 데이터 팩터리의 다이어그램 보기를 볼 수 있습니다.

![데이터 팩터리 다이어그램 보기][image-data-factory-diagram-view]
 
### 다이어그램 보기에는 파이프라인을 엽니다.
다이어그램 보기에서 파이프라인을 마우스 오른쪽 단추로 클릭 하 고 클릭 하 여 파이프라인의 모든 활동을 볼 수 있습니다 **개방 된 파이프라인이**. 활동의 활동에 대 한 입력 및 출력 데이터 집합을 함께 파이프라인에 표시 되어야 합니다. ![개방 된 파이프라인이](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

클릭 하 여 **데이터 팩터리** 다이어그램 보기에 복귀할 왼쪽 위 모서리의 이동 경로에 있습니다. 다이어그램 보기는 모든 파이프라인을 표시합니다. 이 예제에서는 하나의 파이프라인을 만든만 있습니다.

## <a name="DataFactoryLinkedServices"></a> 데이터 팩터리에 연결 된 서비스 보기
에 **데이터 팩터리** 데이터 팩터리에 대 한 블레이드를 클릭 **연결 된 서비스** 타일을 목록에 연결 된 모든 서비스를 참조 하십시오.

![연결 된 서비스 블레이드][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> 연결 된 서비스에 대 한 세부 정보 보기
에 **연결 된 서비스** 블레이드를 클릭 하 여 그에 대 한 참조를 목록에서 연결 된 서비스에 자세히 설명 합니다.

![연결 된 서비스 블레이드][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> 데이터 공장에서 데이터 집합 보기 
에 **데이터 팩터리** 데이터 팩터리에 대 한 블레이드를 클릭 **데이터 집합** 타일 데이터 팩터리에 있는 모든 테이블을 볼 수 있습니다.

![데이터 집합 블레이드][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> 데이터 집합에 대 한 세부 정보 보기
데이터 집합 블레이드의 데이터 집합 목록에서 데이터 집합을 클릭하여 데이터 집합에 대한 세부 정보를 표시합니다. 테이블은 스키마가 있는 사각형 데이터 집합입니다. 현재 이 데이터 집합 유형만 지원됩니다.

![테이블 블레이드][image-data-factory-table]

에 **테이블** 모두 위에 블레이드 **조각 최근에 업데이트** 및 **최근에 조각 실패 한** 목록을 기준으로 정렬 됩니다는 **마지막 업데이트 시간**. 다음과 같은 상황에서 조각의 업데이트 시간이 변경 됩니다.

-  수동으로 업데이트할 있습니다 조각의 상태, 예를 사용 하 여는 **집합 AzureDataFactorySliceStatus** (또는)를 클릭 하 여 **실행** 에 **조각** 블레이드는 조각에 대 한 합니다.
-  실행으로 인해 상태를 변경 하는 조각 (예: 시작을 실행 하는, 종료 하 고 실패 한 실행, 실행을 종료 하 고 성공, 등).
 
	
기준으로 정렬 된 조각 시작/종료 시간 대신 데이터 조각이 보려면 클릭 합니다. **(시간별 slice) 데이터 조각이** 바둑판식으로 배열 합니다.
 
![Slice 시간별 데이터 조각][DataSlicesBySliceTime]

목록 또는 의 제목을 클릭 **... (타원)** 더 큰 조각 목록을 확인 합니다.

![테이블의 모든 조각][image-data-factory-all-slices]

에 **데이터 조각이** 블레이드를 클릭 하 여는 **필터** 단추를는 **필터** 수 있는 블레이드 **필터** 검토 하려는 특정 부분은 참조에 조각입니다. 다음 예와 비슷한 블레이드를 클릭할 때 표시 됩니다 **필터** 에 **데이터 조각이** 슬라이스로 블레이드 **업데이트 시간 별로 정렬 된**.

![블레이드를 필터링 합니다.][image-data-factory-filter-blade]

 **필터** 블레이드 필터링 할 수에 따라 **마지막 시간으로 업데이트 된** 및 **조각화 상태**. 다음 표에서 모든 조각 상태와 그 설명을 보고에 대해 설명합니다.
 
조각화 상태 | 설명
------------ | ------------
PendingExecution | 데이터 처리가 아직 시작되지 않았습니다.
InProgress | 데이터 처리가 진행 중입니다.
Ready | 데이터 처리가 완료되었고 데이터 조각이 준비되었습니다.
Failed | 조각을 생성하는 실행을 수행하지 못했습니다.
Skip | 조각의 처리를 건너뜁니다.
Retry | 조각을 생성하는 실행을 다시 시도합니다.
Timed Out | 조각의 데이터 처리 시간이 초과되었습니다.
PendingValidation | 데이터 조각을 처리하기 전에 유효성 검사 정책에 따라 유효성 검사가 완료될 때까지 기다리는 중입니다.
RetryValidation | 조각의 유효성 검사를 다시 시도합니다.
FailedValidation | 조각의 유효성 검사에 실패했습니다.
LongRetry | JSON 테이블에서 LongRetry를 지정하고 조각에 대한 일반 재시도가 실패한 경우 조각은 이 상태가 됩니다.
ValidationInProgress | JSON 테이블에 정의된 정책에 따라 조각에 대한 유효성 검사를 수행하는 중입니다.

클릭할 때 **필터** 에는 **데이터 조각이** 슬라이스로 블레이드 **조각 시간순으로 정렬 된**, 다른 유형의 나타납니다 **필터** 블레이드.

![필터 블레이드 2][image-data-factory-filter-blade-2]


시작 하기는 **필터** 블레이드에서 **를** 필드 (반올림) 반환 된 레코드의 수를 제한 하는 가장 최근의 시간으로 자동으로 설정 됩니다.  **에서** 필드가 자동으로 설정 합니다. 변경할 수는 **에서** 클릭 하 여 날짜는 **달력** 단추입니다.  **를** 변경 하면 날짜가 변경 된 자동으로 **에서** 날짜입니다.

클릭할 수 있는 **이전**/ * * 다음 * * 단추를 보려면 조각 이전 기간/다음 기간입니다. 시간 범위를 **이전** 및 **다음** 단추 슬라이스 빈도 및 다음 표와에서 같이 간격에 따라 설정 됩니다.

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
 
예: 정의 하는 경우 **주파수** 로 **시간** 및 **간격** 의 **2**, 는 **다음**/ * * 이전 * * 단추 이동 시간 범위 **7 일** 어느 방향으로든에서 합니다. 이 논리는 모든 조각/최근 조각/문제 조각 중 어느 조각을 보는지와 관계없이 필터 블레이드에 적용됩니다.




## <a name="DataFactorySlice"></a> 조각에 대 한 세부 정보 보기
목록에서 조각 클릭 조각 중 하나에 **테이블** 블레이드 또는 **데이터 조각이** 블레이드 해당 조각에 대 한 세부 정보를 확인 합니다.

![데이터 조각][image-data-factory-dataslice]

조각에 없는 경우는 **준비** 상태를 준비 하지 않으며는 현재 조각에서 실행할 수 없도록 차단 여부를 지정 하는 업스트림 조각을 볼 수 있습니다는 **준비 하지 않은 업스트림 조각** 목록입니다.

### <a name="DataFactoryActivtyRuns"></a> 조각에 대 한 모든 활동을 실행 하는 보기
조각에 대한 실행이 둘 이상 있을 수 있습니다. 예를 들어 조각이 실패하는 경우 서비스에서 몇 번 다시 시도할 수 있습니다. 다시 시도가 모두 실패한 조각을 다시 실행할 수도 있습니다. 실행 되는 활동 모두 볼 수 있습니다는 * * 데이터 조각을 * * 블레이드 맨 아래에 목록에 있습니다.

## <a name="DataFactoryActivtyRunDetails"></a> 실행 하는 활동에 대 한 세부 정보 보기
활동의 실행 목록에서 실행을 클릭는 **데이터 조각을** 블레이드 실행 작업에 대 한 세부 정보를 확인 합니다.

![활동 실행 정보][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> 작업 렌즈-지난주의 이벤트
에 **데이터 팩터리** 데이터 팩터리에 대 한 블레이드 (또는 홈 페이지)를 클릭 **지난주의 이벤트** 에서 **작업** 렌즈 지난주에 이벤트를 볼 수 있습니다. 그러면 지난 주에 데이터 팩터리에서 수행한 작업을 개략적으로 볼 수 있습니다. 또한 데이터 이동/처리 관련 오류를 해결하는 데에도 도움이 됩니다.

![데이터 팩터리 이벤트][image-data-factory-events]


## 참고 항목

문서 | 설명
------ | ---------------
[모니터 및 PowerShell을 사용 하 여 관리 Azure 데이터 팩터리][monitor-manage-using-powershell] | 이 문서에서는 Azure PowerShell cmdlet을 사용 하는 Azure 데이터 팩터리를 모니터링 하는 방법을 설명 합니다. 


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

<!---HONumber=GIT-SubDir--> 