<properties 
    pageTitle="Azure 검색을 위한 검색 트래픽 분석 | Microsoft Azure" 
    description="Microsoft Azure에서 Azure 검색을 위한 검색 트래픽 분석 및 클라우드 호스트된 검색 서비스를 사용하여 사용자와 데이터를 이해할 수 있습니다." 
    services="search" 
    documentationCenter="" 
    authors="bernitorres" 
    manager="pablocas" 
    editor=""
/>

<tags 
    ms.service="search" 
    ms.devlang="multiple" 
    ms.workload="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="09/23/2016" 
    ms.author="betorres"
/>



# <a name="enabling-and-using-search-traffic-analytics"></a>검색 트래픽 분석 설정 및 사용

검색 트래픽 분석은 검색 서비스에 대한 가시성을 확보하고 사용자와 이들의 동작을 이해할 수 있게 해주는 Azure 검색 기능입니다. 이 기능을 사용하도록 설정하면 검색 서비스 데이터가 선택한 저장소 계정에 복사됩니다. 이 데이터에는 추가 분석을 위해 처리하고 조작할 수 있는 검색 서비스 로그 및 집계된 운영 메트릭이 포함됩니다.

## <a name="how-to-enable-search-traffic-analytics"></a>검색 트래픽 분석을 사용하도록 설정하는 방법

검색 서비스와 동일한 하위 지역 및 구독의 저장소 계정이 필요합니다.

> [AZURE.IMPORTANT] 이 저장소 계정에 대해서는 표준 요금이 부과됩니다.

포털에서 또는 PowerShell을 통해 검색 트래픽 분석을 사용할 수 있습니다. 사용하도록 설정되었으면 5-10분 이내 데이터가 저장소 계정의 이 두 개의 Blob 컨테이너로 전달되기 시작합니다.

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


### <a name="a.-using-the-portal"></a>A. 포털 사용
[Azure 포털](http://portal.azure.com)에서 Azure Search 서비스를 엽니다. 설정에서 검색 트래픽 분석 옵션을 찾습니다. 

![][1]

상태를 **On**으로 변경하고 사용할 Azure Storage 계정을 선택하고 복사할 데이터(로그, 메트릭 또는 둘 다)를 선택합니다. 로그 및 메트릭을 복사하는 것이 좋습니다. 1일 ~ 365일까지 데이터에 대한 보존 정책을 설정할 수 있습니다. 데이터를 무기한으로 보존하지 않으려는 경우 보존(일)을 0으로 설정하세요.

![][2]

### <a name="b.-using-powershell"></a>B. PowerShell 사용

먼저, 최신 [Azure PowerShell cmdlets](https://github.com/Azure/azure-powershell/releases) 를 설치했는지 확인합니다.

그런 다음 검색 서비스 및 저장소 계정에 대한 리소스 ID를 가져옵니다. 설정 -> 속성 -> ResourceId로 이동하여 포털에서 찾을 수 있습니다.

![][3]

```PowerShell
Login-AzureRmAccount
$SearchServiceResourceId = "Your Search service resource id"
$StorageAccountResourceId = "Your Storage account resource id"
Set-AzureRmDiagnosticSetting -ResourceId $SearchServiceResourceId StorageAccountId $StorageAccountResourceId -Enabled $true
```

## <a name="understanding-the-data"></a>데이터 이해

데이터는 JSON 형식으로 Azure 저장소 Blob에 저장됩니다.

시간 및 컨테이너당 하나의 Blob가 있습니다.
  
경로 예: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### <a name="logs"></a>로그

로그 Blob는 검색 서비스 트래픽 로그를 포함합니다.
각 Blob는 **레코드** 라는 하나의 루트 개체를 포함하며 여기에는 로그 개체의 배열이 포함됩니다.
각 Blob에는 같은 시간 중에 발생한 모든 작업에 대한 레코드가 포함됩니다.

####<a name="log-schema"></a>로그 스키마

이름 |형식 |예 |참고 사항 
------|-----|----|-----
실시간 |datetime |"2015-12-07T00:00:43.6872559Z" |작업 타임스탬프
resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |ResourceId
operationName |string |"Query.Search" |작업 이름
operationVersion |string |"2015-02-28"|사용된 api-version
카테고리 |string |"OperationLogs" |constant 
resultType |string |"Success" |가능한 값: Success 또는 Failure 
resultSignature |int |200 |HTTP 결과 코드 
durationMS |int |50 |밀리초 단위의 작업 기간 
properties |object |다음 테이블 참조 |데이터별 작업을 포함하는 개체

####<a name="properties-schema"></a>속성 스키마

|이름 |형식 |예 |참고 사항|
|------|-----|----|-----|
|설명|string |"GET /indexes('content')/docs" |작업의 끝점 |
|쿼리 |string |"?search=AzureSearch&$count=true&api-version=2015-02-28" |쿼리 매개 변수 |
|문서 |int |42 |처리된 문서 수|
|IndexName |string |"testindex"|작업과 연결된 인덱스의 이름 |

### <a name="metrics"></a>메트릭

메트릭 Blob에는 검색 서비스에 대한 집계 값이 포함됩니다. 각 파일은 **레코드** 라는 하나의 루트 개체를 포함하며 여기에는 메트릭 개체의 배열이 포함됩니다. 이 루트 개체에 데이터를 사용할 수는 1 분마다 메트릭이 포함되어 있습니다. 

사용 가능한 메트릭:

- SearchLatency: 검색 쿼리를 처리하는 데 필요한 검색 서비스의 시간을 1분마다 집계합니다.
- SearchQueriesPerSecond: 초당 수신된 검색 쿼리 수를 1분마다 집계합니다.
- ThrottledSearchQueriesPercentage: 제한된 검색 쿼리의 비율을 1분마다 집계합니다.

> [AZURE.IMPORTANT] 제한은 너무 많은 쿼리가 송신되어 서비스의 프로비전된 리소스 용량을 다 써버린 경우에 발생합니다. 서비스에 더 많은 복제본을 추가하는 것이 좋습니다.

####<a name="metrics-schema"></a>메트릭 스키마

|이름 |형식 |예 |참고 사항|
|------|-----|----|-----|
|resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE"  |리소스 ID |
|metricName |string |"Latency" |메트릭 이름 |
|실시간|datetime |"2015-12-07T00:00:43.6872559Z" |작업의 타임스탬프 |
|average |int |64|메트릭 시간 간격에 원시 샘플의 평균 값 |
|minimum |int |37 |메트릭 시간 간격에 원시 샘플의 최소 값 |
|maximum |int |78 |메트릭 시간 간격에 원시 샘플의 최대 값 |
|total |int |258 |메트릭 시간 간격에 원시 샘플의 총 값 |
|count |int |4 |메트릭을 생성하는 데 사용되는 원시 샘플 수 |
|timegrain |string |"PT1M" |ISO 8601에서 메트릭의 시간 조직|

모든 메트릭은 1 분 간격으로 보고됩니다. 각 메트릭은 분당 최소, 최대 및 평균 값을 표시합니다.

SearchQueriesPerSecond 메트릭의 경우, 최소값은 해당 분 동안 등록된 초당 검색 쿼리 수에 대한 가장 낮은 값입니다. 최대값도 마찬가지입니다. 평균은 전체 분에 대한 집계입니다. 1분 동안 이 시나리오에 관한 생각: SearchQueriesPerSecond에 대한 최대값 만큼 부하가 매우 높은 1초가 지난 후 58초 동안 중간 부하 상태가 계속되다가 마지막으로 최소값인 쿼리 한 개만 있는 1초가 이어집니다.

ThrottledSearchQueriesPercentage의 경우, 최소값, 최대값, 평균 및 합계가 모두 같은 값이며, 이 값은 1분 동안 총 검색 쿼리 수에서 제한된 검색 쿼리의 비율입니다.

## <a name="analyzing-your-data"></a>데이터 분석

데이터는 사용자 자신의 저장소 계정에 있고 사례에 가장 적합한 방식으로 이 데이터를 탐색하는 것이 좋습니다.

먼저 [Power BI](https://powerbi.microsoft.com) 를 사용하여 데이터를 탐색하고 시각화하는 것이 좋습니다. Azure 저장소 계정에 쉽게 연결하고 데이터 분석을 신속하게 시작할 수 있습니다. 

#### <a name="power-bi-online"></a>Power BI 온라인

[Power BI 콘텐츠 팩](https://app.powerbi.com/getdata/services/azure-search): 자동으로 데이터를 표시하고 검색 서비스에 대한 시각적 통찰력을 제공하는 Power BI 대시보드 및 Power BI 보고서 집합을 만듭니다. [콘텐츠 팩 도움말 페이지](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-search/)를 참조하세요.

![][4]

#### <a name="power-bi-desktop"></a>Power BI Desktop

[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop): 데이터를 탐색하고 데이터에 대한 고유의 시각화를 만듭니다. 다음 섹션의 시작 쿼리를 참조하세요.

1. 새 PowerBI Desktop 보고서를 엽니다.
2. 데이터 가져오기 -> 자세히...를 선택합니다.

    ![][5]

3. Microsoft Azure Blob 저장소 및 연결을 선택합니다.

    ![][6]

4. 저장소 계정의 이름 및 계정 키를 입력합니다.
5. "insight-logs-operationlogs" 및 "insights-metrics-pt1m"을 선택한 다음 편집을 클릭합니다.
6. 쿼리 편집기가 열리면 왼쪽에서 "insight-logs-operationlogs"가 선택되었는지 확인합니다. 이제 보기 -> 고급 편집기를 선택하여 고급 편집기를 엽니다.

    ![][7]

7. 처음 두 줄은 유지하고 나머지를 다음 쿼리로 바꿉니다.

    >     #"insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
    >     #"Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
    >     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
    >     #"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
    >     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
    >     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
    >     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
    >     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
    >     #"Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
    >     #"Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
    >     #"Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
    >     #"Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
    >     #"Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
    >     #"Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
    >     #"Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
    >     #"Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
    >     #"Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
    >     #"Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
    >     #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
    >     #"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
    >     #"Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
    >     #"Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
    >     #"Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
    >     #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
    >     in
    >     #"Changed Type2"

8. 완료를 클릭합니다.

9. 이제 왼쪽 쿼리 목록에서 "insights-metrics-pt1m"을 선택하고 고급 편집기를 다시 엽니다. 처음 두 줄은 유지하고 나머지를 다음 쿼리로 바꿉니다. 

    >     #"insights-metrics-pt1m1" = Source{[Name="insights-metrics-pt1m"]}[Data],
    >     #"Sorted Rows" = Table.Sort(#"insights-metrics-pt1m1",{{"Date modified", Order.Descending}}),
    >     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
        #"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
    >     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
    >     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
    >     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
    >     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}, {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}),
    >     #"Filtered Rows" = Table.SelectRows(#"Expanded records1", each ([metricName] = "Latency")),
    >     #"Removed Columns1" = Table.RemoveColumns(#"Filtered Rows",{"timeGrain"}),
    >     #"Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"metricName", "MetricName"}, {"average", "Average"}, {"minimum", "Minimum"}, {"maximum", "Maximum"}, {"total", "Total"}, {"count", "Count"}}),
    >     #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"ResourceId", type text}, {"MetricName", type text}, {"Datetime", type datetimezone}, {"Average", type number}, {"Minimum", Int64.Type}, {"Maximum", Int64.Type}, {"Total", Int64.Type}, {"Count", Int64.Type}}),
    >         Rounding = Table.TransformColumns(#"Changed Type",{{"Average", each Number.Round(_, 2)}}),
    >     #"Changed Type1" = Table.TransformColumnTypes(Rounding,{{"Average", type number}}),
    >     #"Inserted Date" = Table.AddColumn(#"Changed Type1", "Date", each DateTime.Date([Datetime]), type date)
    >     in
        #"Inserted Date"

10. 완료를 클릭한 다음 홈 탭에서 닫기 및 적용을 선택합니다.

11. 이제 지난 30일 동안의 데이터가 사용할 준비가 되었습니다. 진행하여 몇 가지 [시각화](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-report-view/)를 만듭니다.

## <a name="next-steps"></a>다음 단계

검색 구문 및 쿼리 매개 변수에 대해 알아봅니다. 자세한 내용은 [문서 검색(Azure 검색 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx) 을 참조하세요.

놀라운 보고서 만들기에 대해 자세히 알아보세요. 자세한 내용은 [Power BI Desktop 시작](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/)을 참조하세요.

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/ResourceId.png
[4]: ./media/search-traffic-analytics/Dashboard.png
[5]: ./media/search-traffic-analytics/GetData.png
[6]: ./media/search-traffic-analytics/BlobStorage.png
[7]: ./media/search-traffic-analytics/QueryEditor.png




<!--HONumber=Oct16_HO2-->


