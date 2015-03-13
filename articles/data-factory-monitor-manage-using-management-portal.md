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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Azure 미리 보기 포털을 사용하여 Azure 데이터 팩터리 모니터링

- [Azure 구독의 모든 데이터 팩터리 보기](#AllDataFactories)
- [데이터 팩터리에 대한 세부 정보 보기](#DataFactoryDetails)
- [데이터 팩터리에 대한 다이어그램 뷰 보기](#DataFactoryDiagram)
- [데이터 팩터리의 연결된 서비스 보기](#DataFactoryLinkedServices)
- [연결된 서비스에 대한 세부 정보 보기](#DataFactoryLinkedService) 
- [데이터 팩터리의 데이터 집합 보기](#DataFactoryDatasets)
- [데이터 집합에 대한 세부 정보 보기](#DataFactoryDataset)
- [조각에 대한 세부 정보 보기](#DataFactorySlice) 
- [조각에 대한 모든 작업 실행 보기](#DataFactoryActivtyRuns) 
- [작업 실행에 대한 세부 정보 보기](#DataFactoryActivtyRunDetails)
- [지난주의 작업 렌즈-이벤트](#EventsInThePastweek)  
   


## <a name="AllDataFactories"></a> Azure 구독의 모든 데이터 팩터리 보기

- [Azure 미리 보기 포털][azure-preview-portal]에 로그인합니다.
- 왼쪽의 **찾아보기** 허브를 클릭하고 **데이터 팩터리**를 클릭합니다.  

	![BROWSE hub -> Data Factories][image-data-factory-browse-datafactories]

	**데이터 팩터리**가 표시되지 않으면 **모두**를 클릭한 다음 **찾아보기** 블레이드에서 **데이터 팩터리**를 클릭합니다.

	![BROWSE hub -> Everything] [image-data-factory-browse-everything]

- **데이터 팩터리** 블레이드의 모든 데이터 팩터리가 표시됩니다.

	![Data factories blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> 데이터 팩터리에 대한 세부 정보 보기

데이터 팩터리에 대한 세부 정보를 보려면 다음 중 하나를 수행합니다. 


- 위에 표시된 **데이터 팩터리** 블레이드에서 데이터 팩터리를 클릭합니다.
- **시작 보드**에서 데이터 팩터리에 대한 링크를 클릭합니다. **시작 보드**는 Azure 미리 보기 포털에 로그인할 때 표시되는 블레이드입니다. 데이터 팩터리를 만들 때 **시작 보드에 추가**(기본 옵션)를 선택한 경우 다음 이미지에 표시된 대로 시작 보드에 데이터 팩터리 링크가 표시됩니다. 이 예제에서는 **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** 및 **LogProcessingFactory** 데이터 팩터리 링크를 **시작 보드**에서 사용할 수 있습니다.


![Data factory from the Startboard][image-data-factory-datafactory-from-startboard]

어떤 방법을 사용하든 다음 이미지에 표시된 대로 선택한 데이터 팩터리에 대한 **데이터 팩터리** 블레이드가 표시됩니다. 

 ![Data Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> 데이터 팩터리에 대한 다이어그램 뷰 보기
데이터 팩터리에 대한 **데이터 팩터리** 블레이드에서 **다이어그램** 타일을 클릭하여 데이터 팩터리의 다이어그램 뷰를 표시합니다. 

![Data Factory Diagram View][image-data-factory-diagram-view]
 

## <a name="DataFactoryLinkedServices"></a> 데이터 팩터리의 연결된 서비스 보기
데이터 팩터리에 대한 **데이터 팩터리** 블레이드에서 **연결된 서비스** 타일을 클릭하여 연결된 서비스를 모두 목록에 표시합니다. 

![Linked Services Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> 연결된 서비스에 대한 세부 정보 보기
**연결된 서비스** 블레이드의 목록에서 연결된 서비스를 클릭하여 서비스에 대한 세부 정보를 표시합니다. 

![Linked Service Blade][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> 데이터 팩터리의 데이터 집합 보기 
데이터 팩터리에 대한 **데이터 팩터리** 블레이드에서 **데이터 집합** 타일을 클릭하여 데이터 팩터리의 테이블을 모두 표시합니다.

![Data Sets Blade][image-data-factory-datasets] 

## <a name="DataFactoryDataset"></a>  데이터 집합에 대한 세부 정보 보기
데이터 집합 블레이드의 데이터 집합 목록에서 데이터 집합을 클릭하여 데이터 집합에 대한 세부 정보를 표시합니다. 테이블은 스키마가 있는 사각형 데이터 집합입니다. 현재 이 데이터 집합 유형만 지원됩니다. 

![Table Blade][image-data-factory-table]

위의 **테이블** 블레이드에는 **Recent slices(최근 조각)** 및 **Problem slices(문제 조각)**가 표시되어 있습니다. **... (줄임표)**를 클릭하면 조각이 모두 표시됩니다. 

![All Slices of a Table][image-data-factory-all-slices]

**데이터 조각** 블레이드에서 필터 단추를 클릭하면 검토하려는 특정 조각이 표시되도록 조각을 **필터링**할 수 있는 필터 블레이드가 표시됩니다.

![Filter Blade][image-data-factory-filter-blade]


**필터** 블레이드를 시작하면 **끝** 필드가 자동으로 최근 시간(반올림됨)으로 설정되어 반환되는 레코드 수를 제한합니다. **시작** 필드도 자동으로 설정됩니다.  **달력** 단추를 클릭하여 **시작** 날짜를 변경할 수 있습니다. **시작** 날짜를 변경하면 **끝** 날짜는 자동으로 변경됩니다. 

**이전**/**다음** 단추를 클릭하여 이전 기간/다음 기간의 조각을 볼 수 있습니다. **이전** 및 **다음** 단추의 시간 범위는 다음 표에 표시된 대로 조각 빈도 및 간격을 기반으로 설정됩니다.

빈도 | 간격 값 범위 | 결과 시간 청크
----------| -------------------- | --------------------
분 | 1-4 | 6시간
분 | 5-29 | 1일
분 | 30-180 | 7일
분 | 180+ | 28일(약 1개월)
시간 | 1-3 | 7일
시간 | 4-11 | 28일(약 1개월)
시간 | 12-72 | 182일(약 6개월)
시간 | 73+ | 1년
일 | 1-6 | 1년
일 | 7-20 | 5년
일 | 21+ | 10년
주 | 1-3 | 5년
주 | 4+ | 10년
월 | 임의 | 10년
 
예를 들어 **빈도**를 **시간**으로, **간격**을 **2**로 정의한 경우 **다음**/**이전** 단추를 클릭하면 시간 범위가 양쪽 방향으로 **7일** 이동합니다. 이 논리는 모든 조각/최근 조각/문제 조각 중 어느 조각을 보는지와 관계없이 필터 블레이드에 적용됩니다.

**필터** 블레이드에서는 **상태**에 따라 조각을 필터링할 수 있습니다. 다음 표에는 모든 조각 상태 및 해당 설명이 설명되어 있습니다.
 
조각 상태 | 설명
------------ | ------------
PendingExecution | 데이터 처리가 아직 시작되지 않았습니다.
InProgress | 데이터 처리가 진행 중입니다.
Ready | 데이터 처리가 완료되었고 데이터 조각이 준비되었습니다.
Failed | 조각을 생성하는 실행을 수행하지 못했습니다.
Skip | 조각 처리를 건너뜁니다.
Retry | 조각을 생성하는 실행을 다시 시도합니다.
Timed Out | 조각의 데이터 처리 시간이 초과되었습니다.
PendingValidation | 데이터 조각을 처리하기 전에 유효성 검사 정책에 따라 유효성 검사가 완료될 때까지 기다리는 중입니다.
RetryValidation | 조각의 유효성 검사를 다시 시도합니다.
FailedValidation | 조각의 유효성을 검사하지 못했습니다.
LongRetry | JSON 테이블에서 LongRetry를 지정하고 조각에 대한 일반 다시 시도가 실패한 경우 조각은 이 상태가 됩니다.
ValidationInProgress | JSON 테이블에 정의된 정책에 따라 조각에 대한 유효성 검사를 수행하는 중입니다.



## <a name="DataFactorySlice"></a> 조각에 대한 세부 정보 보기
**테이블** 블레이드 또는 **데이터 조각** 블레이드의 조각 목록에서 조각을 클릭하여 해당 조각에 대한 세부 정보를 표시합니다. 

![Data Slice][image-data-factory-dataslice]


### <a name="DataFactoryActivtyRuns"></a> 조각에 대한 모든 작업 실행 보기
조각에 대한 실행이 둘 이상 있을 수 있습니다. 예를 들어 조각이 실패하는 경우 서비스에서 몇 번 다시 시도할 수 있습니다. 다시 시도가 모두 실패한 조각을 다시 실행할 수도 있습니다. 아래쪽 목록의 **데이터 조각** 블레이드에서 작업 실행을 모두 볼 수 있습니다. 

## <a name="DataFactoryActivtyRunDetails"></a>  작업 실행에 대한 세부 정보 보기
**데이터 조각** 블레이드의 실행 목록에서 작업 실행을 클릭하여 해당 작업 실행에 대한 세부 정보를 표시합니다. 

![Activity Run Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> 지난주의 작업 렌즈-이벤트
데이터 팩터리의 **데이터 팩터리** 블레이드 또는 홈페이지에 있는 **작업** 렌즈에서 **Events in the past week(지난 주의 이벤트)**를 클릭하여 지난 주의 이벤트를 표시합니다. 그러면 지난 주에 데이터 팩터리에서 수행한 작업을 개략적으로 볼 수 있습니다. 또한 데이터 이동/처리 관련 오류를 해결하는 데에도 도움이 됩니다. 

![ Data Factory Events][image-data-factory-events]


## 참고 항목

문서 | 설명
------ | ---------------
[PowerShell을 사용하여 Azure 데이터 팩터리 모니터링 및 관리][monitor-manage-using-powershell] | 이 문서에서는 Azure PowerShell cmdlet을 사용하여 Azure 데이터 팩터리를 모니터링하는 방법에 대해 설명합니다. 
[파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources](영문) | 이 문서의 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 복사하는 방법을 보여 줍니다.
[데이터 팩터리에서 Pig 및 Hive 사용][use-pig-and-hive-with-data-factory] | 이 문서의 연습에서는 HDInsight 작업을 사용하여 hive/pig 스크립트로 입력 데이터를 처리하고 출력 데이터를 생성하는 방법을 보여 줍니다. 
[자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial](영문) | 이 문서에서는 Azure 데이터 팩터리를 사용하는 실제 시나리오를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 종단 간 연습을 제공합니다.
[데이터 팩터리에서 사용자 지정 작업 사용][use-custom-activities](영문) | 이 문서에서는 사용자 지정 작업을 만들어 파이프라인에서 사용하는 방법에 대한 단계별 지침이 포함된 연습을 제공합니다. 
[데이터 팩터리 문제 해결][troubleshoot](영문) | 이 문서에서는 Azure 데이터 팩터리 문제를 해결하는 방법에 대해 설명합니다.
[Azure 데이터 팩터리 개발자 참조][developer-reference](영문) | 개발자 참조에는 cmdlet, JSON 스크립트, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 
[Azure 데이터 팩터리 Cmdlet 참조][cmdlet-reference] | 이 참조 콘텐츠에는 모든 **데이터 팩터리 cmdlet**에 대한 내용이 자세히 나와 있습니다.


[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

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

<!--HONumber=35.2-->

<!--HONumber=46--> 
