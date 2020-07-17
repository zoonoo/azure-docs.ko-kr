---
title: Azure Monitor Application Insights 작업 영역 기반 리소스 스키마
description: Azure Monitor Application Insights 작업 영역 기반 리소스에 대 한 새 테이블 구조 및 스키마에 대해 알아봅니다.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/09/2020
ms.openlocfilehash: 21f387a87224615ea6afbdce620c56e3ad2cc6ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83210542"
---
# <a name="workspace-based-resource-changes-preview"></a>작업 영역 기반 리소스 변경 (미리 보기)

[작업 영역 기반 Application Insights 리소스가](create-workspace-resource.md)도입 되기 전에 Application Insights 데이터는 Azure Monitor의 다른 로그 데이터와 별도로 저장 됩니다. 둘 다 Azure 데이터 탐색기을 기반으로 하며 동일한 Kusto 쿼리 언어 (KQL)를 사용 합니다. 이는 [Azure Monitor의 로그](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)에 설명 되어 있습니다.

작업 영역 기반 Application Insights 리소스 데이터는 다른 모니터링 데이터 및 응용 프로그램 데이터와 함께 Log Analytics 작업 영역에 저장 됩니다. 이렇게 하면 여러 솔루션에서 데이터를 보다 쉽게 분석 하 고 작업 영역 기능을 활용할 수 있으므로 구성이 간단해 집니다.

## <a name="table-structure"></a>테이블 구조

| 레거시 테이블 이름 | 새 테이블 이름 | Description |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  가용성 테스트에 대한 요약 데이터입니다.|
| browserTimings | AppBrowserTimings | 들어오는 데이터를 처리하는 데 걸리는 시간 등, 클라이언트 성능에 대한 데이터입니다.|
| 종속성 | AppDependencies | 애플리케이션에서 TrackDependency()를 통해 기록된 다른 구성 요소(외부 구성 요소 포함)에 대한 호출입니다. – 예: REST API, 데이터베이스 또는 파일 시스템에 대한 호출.  |
| customEvents | AppEvents | 애플리케이션에서 생성된 사용자 지정 이벤트입니다. |
| customMetrics | AppMetrics | 애플리케이션에서 생성된 사용자 지정 메트릭입니다. |
| pageViews | AppPageViews| 브라우저 정보가 포함된 각 웹 사이트 보기에 대한 데이터입니다. |
| performanceCounters | AppPerformanceCounters | 애플리케이션을 지원하는 컴퓨팅 리소스의 성능 측정입니다(예: Windows 성능 카운터). |
| requests | AppRequests | 애플리케이션이 받은 요청입니다. 예를 들어, 웹앱이 수신하는 각 HTTP 요청에 대해 별도의 요청 레코드가 기록됩니다.  |
| 예외 | AppSystemEvents | 애플리케이션 런타임에서 throw된 예외는 서버 측 및 클라이언트 측(브라우저) 예외를 모두 캡처합니다. |
| traces | AppTraces | TrackTrace()를 통해 기록된 애플리케이션 코드/로깅 프레임워크를 통해 생성된 자세한 로그(추적)입니다. |

## <a name="table-schemas"></a>테이블 스키마

다음 섹션에서는 클래식 속성 이름과 새 작업 영역 기반 Application Insights 속성 이름 간의 매핑을 보여 줍니다.  이 정보를 사용 하 여 레거시 테이블을 사용 하는 쿼리를 변환 합니다.

대부분의 열은 대/소문자가 다른 동일한 이름을 갖습니다. KQL은 대/소문자를 구분 하므로 기존 쿼리의 테이블 이름과 함께 각 열 이름을 변경 해야 합니다. 대문자화 외에도 변경 내용이 있는 열은 강조 표시 됩니다. 작업 영역 기반 리소스인 경우에도 Application Insights 리소스의 **로그** 창 내에서 기존 Application Insights 쿼리를 계속 사용할 수 있습니다. Log Analytics 작업 영역 환경의 컨텍스트 내에서 쿼리 하는 경우에는 새 속성 이름이 필요 합니다.

### <a name="appavailabilityresults"></a>AppAvailabilityResults

레거시 테이블: 가용성

|ApplicationInsights|형식|LogAnalytics|형식|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|형식|String|
|위치|string|위치|string|
|message|문자열|메시지|string|
|name|string|이름|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|크기|real|크기|real|
|성공|string|성공|Bool|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appbrowsertimings"></a>AppBrowserTimings

레거시 테이블: browserTimings

|ApplicationInsights|형식|LogAnalytics|형식|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|문자열|이름|Datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|string|SdkVersion|string|
|sendDuration|real|SendDurationMs|real|
|session_Id|string|SessionId|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|totalDuration|real|TotalDurationMs|real|
|url|문자열|Url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appdependencies"></a>AppDependencies

레거시 테이블: 종속성

|ApplicationInsights|형식|LogAnalytics|형식|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|데이터|string|데이터|string|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|형식|String|
|name|문자열|이름|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|resultCode|string|ResultCode|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|성공|string|성공|Bool|
|대상|string|대상|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|형식|string|DependencyType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appevents"></a>AppEvents

레거시 테이블: customEvents

|ApplicationInsights|형식|LogAnalytics|형식|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|문자열|이름|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appmetrics"></a>AppMetrics

레거시 테이블: customMetrics

|ApplicationInsights|형식|LogAnalytics|형식|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|동적|속성|동적|
|iKey|string|IKey|string|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|문자열|이름|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|값|real|제거||
|이상|int|이상|int|
|valueMax|real|ValueMax|real|
|이상|real|이상|real|
|이상 개발|real|이상 개발|real|
|Valueum|real|Valueum|real|

### <a name="apppageviews"></a>AppPageViews

레거시 테이블: pageViews

|ApplicationInsights|형식|LogAnalytics|형식|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|형식|String|
|name|문자열|이름|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|url|string|Url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appperformancecounters"></a>AppPerformanceCounters

레거시 테이블: performanceCounters

|ApplicationInsights|형식|LogAnalytics|형식|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|category|문자열|범주|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|counter|string|제거||
|customDimensions|동적|속성|동적|
|iKey|string|IKey|string|
|인스턴스|string|인스턴스|string|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|문자열|이름|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|값|real|값|real|

### <a name="apprequests"></a>AppRequests

레거시 테이블: 요청

|ApplicationInsights|형식|LogAnalytics|형식|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|duration|real|DurationMs|Real|
|`id`|string|`Id`|String|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|형식|String|
|name|string|이름|String|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|String|
|resultCode|string|ResultCode|String|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|source|문자열|원본|String|
|성공|string|성공|Bool|
|timestamp|Datetime|TimeGenerated|Datetime|
|url|string|Url|String|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appsystemevents"></a>AppSystemEvents

레거시 테이블: 예외

|ApplicationInsights|형식|LogAnalytics|형식|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|어셈블리|string|어셈블리|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|자세히|동적|설명|동적|
|handledAt|string|HandledAt|string|
|iKey|string|IKey|string|
|innermostAssembly|string|InnermostAssembly|string|
|innermostMessage|string|InnermostMessage|string|
|innermostMethod|string|InnermostMethod|string|
|innermostType|string|InnermostType|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|message|문자열|메시지|string|
|method|string|메서드|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|outerAssembly|string|OuterAssembly|string|
|outerMessage|string|OuterMessage|string|
|outerMethod|string|OuterMethod|string|
|outerType|string|OuterType|string|
|problemId|string|ProblemId|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|int|SeverityLevel|int|
|timestamp|Datetime|TimeGenerated|Datetime|
|형식|string|ExceptionType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="apptraces"></a>AppTraces

레거시 테이블: 추적

|ApplicationInsights|형식|LogAnalytics|형식|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|message|문자열|메시지|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|int|SeverityLevel|int|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색](../../azure-monitor/platform/metrics-charts.md)
* [분석 쿼리 작성](../../azure-monitor/app/analytics.md)