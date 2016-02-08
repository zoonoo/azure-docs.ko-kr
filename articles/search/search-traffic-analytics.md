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
	ms.date="12/17/2015" 
	ms.author="betorres"
/>


# 검색 트래픽 분석 설정 및 사용

검색 트래픽 분석은 검색 서비스에 대한 가시성을 확보하고 사용자와 이들의 동작을 이해할 수 있게 해주는 Azure 검색 기능입니다. 이 기능을 사용하도록 설정하면 검색 서비스 데이터가 선택한 저장소 계정에 복사됩니다. 이 데이터는 검색 서비스 로그 및 집계된 운영 메트릭을 포함합니다. 여기에서 어떤 방식으로든 사용 현황 데이터를 처리 및 조작할 수 있습니다.


## 검색 트래픽 분석을 사용하도록 설정하는 방법

### 1\. 포털 사용
[Azure 포털](https://portal.azure.com)에서 Azure 검색 서비스를 엽니다. 설정에서 검색 트래픽 분석 옵션을 찾습니다.

![][1]

이 옵션을 선택하면 새 블레이드가 열립니다. 상태를 **On**으로 변경하고 데이터를 복사할 Azure 저장소 계정을 선택하고 복사할 데이터(로그, 메트릭 또는 둘 다)를 선택합니다. 로그 및 메트릭을 복사하는 것이 좋습니다.

![][2]


> [AZURE.IMPORTANT] 저장소 계정이 검색 서비스와 동일한 지역 및 구독에 있어야 합니다.
> 
> 이 저장소 계정에 대해서는 표준 요금이 부과됩니다.

### 2\. PowerShell 사용

또한 다음 PowerShell cmdlet을 실행하여 이 기능을 사용할 수 있습니다.

```PowerShell
Login-AzureRmAccount
Set-AzureRmDiagnosticSetting -ResourceId <SearchService ResourceId> StorageAccountId <StorageAccount ResourceId> -Enabled $true
```

-   **SearchService ResourceId**: ```
/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Search/searchServices/<searchServiceName>
```

 
-  **StorageAccount ResourceId**: 포털의 설정 -> 속성 -> 리소스 ID ```
New: /subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>
OR
Classic: /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ClassicStorage/storageAccounts/<storageAccountName>
```에서 찾을 수 있습니다.

----------

사용하도록 설정되었으면 5-10분 이내 데이터가 저장소 계정으로 전달되기 시작합니다. Blob 저장소에서 2개의 새 컨테이너를 찾을 수 있습니다.

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


## 데이터 이해

데이터는 JSON 형식으로 Azure 저장소 Blob에 저장됩니다.

시간 및 컨테이너당 하나의 Blob가 있습니다.
  
경로 예: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### 로그

로그 Blob는 검색 서비스 트래픽 로그를 포함합니다.

각 Blob는 **레코드**라는 하나의 루트 개체를 포함하며 여기에는 로그 개체의 배열이 포함됩니다.

####로그 스키마

이름 |형식 |예 |참고 사항 
------|-----|----|-----
실시간 |datetime |"2015-12-07T00:00:43.6872559Z" |작업 타임스탬프
resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |ResourceId
operationName |string |"Query.Search" |작업 이름
operationVersion |string |"2015-02-28"|사용된 api-version
카테고리 |string |"OperationLogs" |constant 
resultType |string |"Success" |가능한 값: Success 또는 Failure 
resultSignature |int |200 |HTTP 결과 코드 
durationMS |int |50 |밀리초 단위의 작업 기간 
properties |object |아래 참조 |데이터별 작업을 포함하는 개체

####속성 스키마

|이름 |형식 |예 |참고 사항|
|------|-----|----|-----|
|설명|string |"GET /indexes('content')/docs" |작업의 끝점 |
|쿼리 |string |"?search=AzureSearch&$count=true&api-version=2015-02-28" |쿼리 매개 변수 |
|문서 |int |42 |처리된 문서 수|
|IndexName |string |"testindex"|작업과 연결된 인덱스의 이름 |

### 메트릭

메트릭 Blob에는 검색 서비스에 대한 집계 값이 포함됩니다. 각 파일은 **레코드**라는 하나의 루트 개체를 포함하며 여기에는 메트릭 개체의 배열이 포함됩니다.

사용 가능한 메트릭:

- 대기 시간

####메트릭 스키마

|이름 |형식 |예 |참고 사항|
|------|-----|----|-----|
|resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |리소스 ID |
|metricName |string |"Latency" |메트릭 이름 |
|실시간|datetime |"2015-12-07T00:00:43.6872559Z" |작업의 타임스탬프 |
|average |int |64|메트릭 시간 간격에 원시 샘플의 평균 값 |
|minimum |int |37 |메트릭 시간 간격에 원시 샘플의 최소 값 |
|maximum |int |78 |메트릭 시간 간격에 원시 샘플의 최대 값 |
|total |int |258 |메트릭 시간 간격에 원시 샘플의 총 값 |
|count |int |4 |메트릭을 생성하는 데 사용되는 원시 샘플 수 |
|timegrain |string |"PT1M" |ISO 8601에서 메트릭의 시간 조직|

## 데이터 분석

데이터는 사용자 자신의 저장소 계정에 있고 사례에 가장 적합한 방식으로 이 데이터를 탐색하는 것이 좋습니다.

시작점으로 [Power BI Desktop](https://powerbi.microsoft.com/ko-KR/desktop)을 사용하여 데이터를 탐색하고 시각화하는 것이 좋습니다. Azure 저장소 계정에 쉽게 연결하고 데이터 분석을 신속하게 시작할 수 있습니다.

Power BI Desktop에서 직접 보고서를 만들 수 있는 다음 샘플 쿼리를 확인하세요.

### 지침

1. 새 PowerBI Desktop 보고서를 엽니다.
2. 데이터 가져오기 -> 자세히...를 선택합니다.

	![][3]

3. Microsoft Azure Blob 저장소 및 연결을 선택합니다.

	![][4]

4. 저장소 계정의 이름 및 계정 키를 입력합니다.
5. "insight-logs-operationlogs" 및 "insights-metrics-pt1m"을 선택한 다음 편집을 클릭합니다.
6. 쿼리 편집기가 열리면 왼쪽에서 "insight-logs-operationlogs"가 선택되었는지 확인합니다. 이제 보기 -> 고급 편집기를 선택하여 고급 편집기를 엽니다.

	![][5]

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

11. 이제 지난 30일 동안의 데이터가 사용할 준비가 되었습니다. 진행하여 몇 가지 [시각화](https://powerbi.microsoft.com/ko-KR/documentation/powerbi-desktop-report-view/)를 만듭니다.

## 다음 단계

검색 구문 및 쿼리 매개 변수에 대해 알아봅니다. 자세한 내용은 [문서 검색(Azure 검색 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)을 참조하세요.

놀라운 보고서 만들기에 대해 자세히 알아보세요. 자세한 내용은 [Power BI Desktop 시작](https://powerbi.microsoft.com/ko-KR/documentation/powerbi-desktop-getting-started/)을 참조하세요.

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/GetData.png
[4]: ./media/search-traffic-analytics/BlobStorage.png
[5]: ./media/search-traffic-analytics/QueryEditor.png

<!---HONumber=AcomDC_0128_2016-->