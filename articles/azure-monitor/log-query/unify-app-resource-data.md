---
title: 여러 Azure Monitor Application Insights 리소스 통합 | Microsoft Docs
description: 이 문서에서는 Azure Monitor Logs의 함수를 사용해 여러 Application Insights 리소스를 쿼리하고 해당 데이터를 표시하는 방법을 자세히 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: magoedte
ms.openlocfilehash: f9138ec06900f4a7f856cc90362d16496b7b4fed
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766015"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>여러 Azure Monitor Application Insights 리소스 통합 
이 문서에서는 모든 Application Insights 애플리케이션 로그 데이터가 각기 다른 Azure 구독에 있더라도 한 곳에서 쿼리하고 확인하는 방법을 설명합니다. 사용이 중단된 Application Insights 커넥터 대신 이 방법을 사용할 수 있습니다. 단일 쿼리에 포함할 수 있는 Application Insights 리소스 수는 100개로 제한됩니다.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>여러 Application Insights 리소스를 쿼리하는 권장 방식 
여러 Application Insights 리소스의 목록을 쿼리에 표시하는 방식은 번거로우며 유지 관리하기가 어려울 수 있습니다. 이러한 방식 대신 함수를 사용해 애플리케이션 범위에서 쿼리 논리를 분리할 수 있습니다.  

이 예제에서는 여러 Application Insights 리소스를 모니터링하고 애플리케이션 이름을 기준으로 실패한 요청의 수를 시각화하는 방법을 보여 줍니다. 시작하기 전에 Application Insights 리소스에 연결된 작업 영역에서 이 쿼리를 실행하여 연결된 애플리케이션 목록을 가져옵니다. 

```
ApplicationInsights
| summarize by ApplicationName
```

애플리케이션 목록에 합집합 연산자를 사용하는 함수를 만든 다음 별칭을 *applicationsScoping*으로 지정하여 쿼리를 함수로 저장합니다.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>Logs 포털의 쿼리 탐색기로 이동한 다음 함수를 편집하거나 `SavedSearch` PowerShell cmdlet을 사용하여 언제든지 목록에 포함된 애플리케이션을 수정할 수 있습니다. `withsource= SourceApp` 명령은 로그를 전송한 애플리케이션을 지정하는 열을 결과에 추가합니다. 
>
>ApplicationsScoping 함수는 Application Insights 데이터 구조를 반환하므로 쿼리는 작업 영역에서 실행되지만 Application Insights 스키마를 사용합니다. 
>
>이 예제의 선택적 연산자인 parse는 SourceApp 속성에서 애플리케이션 이름을 추출합니다. 

이제 리소스 간 쿼리에 applicationsScoping 함수를 사용할 수 있습니다.  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

함수 별칭은 정의된 모든 애플리케이션에서 요청의 합집합을 반환합니다. 그런 다음, 쿼리는 실패한 요청에 대해 필터링하고 애플리케이션별로 추세를 시각화합니다.

![쿼리 간 결과 예제](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>여러 Application Insights 리소스 및 작업 영역 데이터 쿼리 
Connector를 중지하고 Application Insights 데이터 보존 기간(90일)에 따라 잘린 시간 범위에 대해 쿼리를 수행해야 하는 경우 작업 영역과 Application Insights 리소스에서 중간 기간에 대해 [리소스 간 쿼리](../../azure-monitor/log-query/cross-workspace-query.md)를 수행해야 합니다. 위에서 설명한 새 Application Insights 데이터 보존 기간에 따라 애플리케이션 데이터가 누적될 때까지 이 쿼리를 수행합니다. Application Insights의 스키마와 작업 영역의 스키마는 서로 다르기 때문에 이 쿼리는 다소 조작해야 합니다. 스키마 차이점이 강조 표시되어 있는 이 섹션 뒷부분의 표를 참조하세요. 

예를 들어 2018년 11월 1일에 Connector 작동이 중지된 경우 작업 영역의 애플리케이션 데이터와 Application Insights 리소스 간에 로그를 쿼리할 때는 다음 예제와 같이 쿼리를 생성합니다.

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights 및 Log Analytics 작업 영역 스키마의 차이점
다음 표에는 Log Analytics 및 Application Insights의 스키마 간 차이점이 나와 있습니다.  

| Log Analytics 작업 영역 속성| Application Insights 리소스 속성|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | Message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | 이름 |
| AvailabilityTimestamp | timestamp |
| 브라우저 | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| 국가 | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | 이름 | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | Message | 
| ExceptionType | 형식 |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | 이름 | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | 이름 | 
| RequestSuccess | 성공 | 
| ResponseCode | resultCode | 
| 역할 | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | 형식 |
| URL | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>다음 단계

Application Insights 앱에 대한 자세한 정보를 보려면 [로그 검색](../../azure-monitor/log-query/log-query-overview.md)을 사용합니다.
