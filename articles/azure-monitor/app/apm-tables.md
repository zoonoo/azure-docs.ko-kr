---
title: Azure Monitor Application Insights 작업 영역 기반 리소스 스키마
description: Azure Monitor Application Insights 작업 영역 기반 리소스에 대 한 새 테이블 구조 및 스키마에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: 8f0bee64d74cfd5b6abef5c918c023974fda3fcf
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931056"
---
# <a name="workspace-based-resource-changes-preview"></a>작업 영역 기반 리소스 변경 (미리 보기)

[작업 영역 기반 Application Insights 리소스가](create-workspace-resource.md)도입 되기 전에 Application Insights 데이터는 Azure Monitor의 다른 로그 데이터와 별도로 저장 됩니다. 둘 다 Azure 데이터 탐색기을 기반으로 하며 동일한 Kusto 쿼리 언어 (KQL)를 사용 합니다. 이는 [Azure Monitor의 로그](../platform/data-platform-logs.md)에 설명 되어 있습니다.

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

|ApplicationInsights|유형|LogAnalytics|유형|
|:---|:---|:---|:---|
|appId|문자열|\_ResourceGUID|문자열|
|application_Version|문자열|AppVersion|문자열|
|appName|문자열|\_ResourceId|문자열|
|client_Browser|문자열|ClientBrowser|문자열|
|client_City|문자열|ClientCity|문자열|
|client_CountryOrRegion|문자열|ClientCountryOrRegion|문자열|
|client_IP|문자열|ClientIP|문자열|
|client_Model|문자열|ClientModel|문자열|
|client_OS|문자열|ClientOS|문자열|
|client_StateOrProvince|문자열|ClientStateOrProvince|문자열|
|client_Type|문자열|ClientType|문자열|
|cloud_RoleInstance|문자열|AppRoleInstance|문자열|
|cloud_RoleName|문자열|AppRoleName|문자열|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|duration|real|DurationMs|real|
|`id`|문자열|`Id`|문자열|
|iKey|문자열|IKey|문자열|
|itemCount|int|ItemCount|int|
|itemId|문자열|\_ItemId|문자열|
|itemType|문자열|Type|String|
|위치|문자열|위치|문자열|
|message|문자열|메시지|문자열|
|name|문자열|이름|string|
|operation_Id|문자열|OperationId|문자열|
|operation_Name|문자열|OperationName|문자열|
|operation_ParentId|문자열|OperationParentId|문자열|
|operation_SyntheticSource|문자열|OperationSyntheticSource|문자열|
|performanceBucket|문자열|PerformanceBucket|문자열|
|sdkVersion|문자열|SdkVersion|문자열|
|session_Id|문자열|SessionId|문자열|
|크기|real|크기|real|
|성공|문자열|성공|Bool|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|문자열|UserAccountId|문자열|
|user_AuthenticatedId|문자열|UserAuthenticatedId|문자열|
|user_Id|문자열|UserId|문자열|

### <a name="appbrowsertimings"></a>AppBrowserTimings

레거시 테이블: browserTimings

|ApplicationInsights|유형|LogAnalytics|유형|
|:---|:---|:---|:---|
|appId|문자열|\_ResourceGUID|문자열|
|application_Version|문자열|AppVersion|문자열|
|appName|문자열|\_ResourceId|문자열|
|client_Browser|문자열|ClientBrowser|문자열|
|client_City|문자열|ClientCity|문자열|
|client_CountryOrRegion|문자열|ClientCountryOrRegion|문자열|
|client_IP|문자열|ClientIP|문자열|
|client_Model|문자열|ClientModel|문자열|
|client_OS|문자열|ClientOS|문자열|
|client_StateOrProvince|문자열|ClientStateOrProvince|문자열|
|client_Type|문자열|ClientType|문자열|
|cloud_RoleInstance|문자열|AppRoleInstance|문자열|
|cloud_RoleName|문자열|AppRoleName|문자열|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|iKey|문자열|IKey|문자열|
|itemCount|int|ItemCount|int|
|itemId|문자열|\_ItemId|문자열|
|itemType|문자열|Type|문자열|
|name|문자열|Name|Datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|문자열|OperationId|문자열|
|operation_Name|문자열|OperationName|문자열|
|operation_ParentId|문자열|OperationParentId|문자열|
|operation_SyntheticSource|문자열|OperationSyntheticSource|문자열|
|performanceBucket|문자열|PerformanceBucket|문자열|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|문자열|SdkVersion|문자열|
|sendDuration|real|SendDurationMs|real|
|session_Id|문자열|SessionId|문자열|
|timestamp|Datetime|TimeGenerated|Datetime|
|totalDuration|real|TotalDurationMs|real|
|url|문자열|Url|문자열|
|user_AccountId|문자열|UserAccountId|문자열|
|user_AuthenticatedId|문자열|UserAuthenticatedId|문자열|
|user_Id|문자열|UserId|문자열|

### <a name="appdependencies"></a>AppDependencies

레거시 테이블: 종속성

|ApplicationInsights|유형|LogAnalytics|유형|
|:---|:---|:---|:---|
|appId|문자열|\_ResourceGUID|문자열|
|application_Version|문자열|AppVersion|문자열|
|appName|문자열|\_ResourceId|문자열|
|client_Browser|문자열|ClientBrowser|문자열|
|client_City|문자열|ClientCity|문자열|
|client_CountryOrRegion|문자열|ClientCountryOrRegion|문자열|
|client_IP|문자열|ClientIP|문자열|
|client_Model|문자열|ClientModel|문자열|
|client_OS|문자열|ClientOS|문자열|
|client_StateOrProvince|문자열|ClientStateOrProvince|문자열|
|client_Type|문자열|ClientType|문자열|
|cloud_RoleInstance|문자열|AppRoleInstance|문자열|
|cloud_RoleName|문자열|AppRoleName|문자열|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|데이터|문자열|데이터|문자열|
|duration|real|DurationMs|real|
|`id`|문자열|`Id`|문자열|
|iKey|문자열|IKey|문자열|
|itemCount|int|ItemCount|int|
|itemId|문자열|\_ItemId|문자열|
|itemType|문자열|Type|String|
|name|문자열|이름|string|
|operation_Id|문자열|OperationId|문자열|
|operation_Name|문자열|OperationName|문자열|
|operation_ParentId|문자열|OperationParentId|문자열|
|operation_SyntheticSource|문자열|OperationSyntheticSource|문자열|
|performanceBucket|문자열|PerformanceBucket|문자열|
|resultCode|문자열|ResultCode|문자열|
|sdkVersion|문자열|SdkVersion|문자열|
|session_Id|문자열|SessionId|문자열|
|성공|문자열|성공|Bool|
|대상|문자열|대상|문자열|
|timestamp|Datetime|TimeGenerated|Datetime|
|type|문자열|DependencyType|문자열|
|user_AccountId|문자열|UserAccountId|문자열|
|user_AuthenticatedId|문자열|UserAuthenticatedId|문자열|
|user_Id|문자열|UserId|문자열|

### <a name="appevents"></a>AppEvents

레거시 테이블: customEvents

|ApplicationInsights|유형|LogAnalytics|유형|
|:---|:---|:---|:---|
|appId|문자열|\_ResourceGUID|문자열|
|application_Version|문자열|AppVersion|문자열|
|appName|문자열|\_ResourceId|문자열|
|client_Browser|문자열|ClientBrowser|문자열|
|client_City|문자열|ClientCity|문자열|
|client_CountryOrRegion|문자열|ClientCountryOrRegion|문자열|
|client_IP|문자열|ClientIP|문자열|
|client_Model|문자열|ClientModel|문자열|
|client_OS|문자열|ClientOS|문자열|
|client_StateOrProvince|문자열|ClientStateOrProvince|문자열|
|client_Type|문자열|ClientType|문자열|
|cloud_RoleInstance|문자열|AppRoleInstance|문자열|
|cloud_RoleName|문자열|AppRoleName|문자열|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|iKey|문자열|IKey|문자열|
|itemCount|int|ItemCount|int|
|itemId|문자열|\_ItemId|문자열|
|itemType|문자열|Type|문자열|
|name|문자열|이름|string|
|operation_Id|문자열|OperationId|문자열|
|operation_Name|문자열|OperationName|문자열|
|operation_ParentId|문자열|OperationParentId|문자열|
|operation_SyntheticSource|문자열|OperationSyntheticSource|문자열|
|sdkVersion|문자열|SdkVersion|문자열|
|session_Id|문자열|SessionId|문자열|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|문자열|UserAccountId|문자열|
|user_AuthenticatedId|문자열|UserAuthenticatedId|문자열|
|user_Id|문자열|UserId|문자열|

### <a name="appmetrics"></a>AppMetrics

레거시 테이블: customMetrics

|ApplicationInsights|유형|LogAnalytics|유형|
|:---|:---|:---|:---|
|appId|문자열|\_ResourceGUID|문자열|
|application_Version|문자열|AppVersion|문자열|
|appName|문자열|\_ResourceId|문자열|
|client_Browser|문자열|ClientBrowser|문자열|
|client_City|문자열|ClientCity|문자열|
|client_CountryOrRegion|문자열|ClientCountryOrRegion|문자열|
|client_IP|문자열|ClientIP|문자열|
|client_Model|문자열|ClientModel|문자열|
|client_OS|문자열|ClientOS|문자열|
|client_StateOrProvince|문자열|ClientStateOrProvince|문자열|
|client_Type|문자열|ClientType|문자열|
|cloud_RoleInstance|문자열|AppRoleInstance|문자열|
|cloud_RoleName|문자열|AppRoleName|문자열|
|customDimensions|동적|속성|동적|
|iKey|문자열|IKey|문자열|
|itemId|문자열|\_ItemId|문자열|
|itemType|문자열|Type|문자열|
|name|문자열|이름|string|
|operation_Id|문자열|OperationId|문자열|
|operation_Name|문자열|OperationName|문자열|
|operation_ParentId|문자열|OperationParentId|문자열|
|operation_SyntheticSource|문자열|OperationSyntheticSource|문자열|
|sdkVersion|문자열|SdkVersion|문자열|
|session_Id|문자열|SessionId|문자열|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|문자열|UserAccountId|문자열|
|user_AuthenticatedId|문자열|UserAuthenticatedId|문자열|
|user_Id|문자열|UserId|문자열|
|value|real|제거||
|이상|int|이상|int|
|valueMax|real|ValueMax|real|
|이상|real|이상|real|
|이상 개발|real|이상 개발|real|
|Valueum|real|Valueum|real|

### <a name="apppageviews"></a>AppPageViews

레거시 테이블: pageViews

|ApplicationInsights|유형|LogAnalytics|유형|
|:---|:---|:---|:---|
|appId|문자열|\_ResourceGUID|문자열|
|application_Version|문자열|AppVersion|문자열|
|appName|문자열|\_ResourceId|문자열|
|client_Browser|문자열|ClientBrowser|문자열|
|client_City|문자열|ClientCity|문자열|
|client_CountryOrRegion|문자열|ClientCountryOrRegion|문자열|
|client_IP|문자열|ClientIP|문자열|
|client_Model|문자열|ClientModel|문자열|
|client_OS|문자열|ClientOS|문자열|
|client_StateOrProvince|문자열|ClientStateOrProvince|문자열|
|client_Type|문자열|ClientType|문자열|
|cloud_RoleInstance|문자열|AppRoleInstance|문자열|
|cloud_RoleName|문자열|AppRoleName|문자열|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|duration|real|DurationMs|real|
|`id`|문자열|`Id`|문자열|
|iKey|문자열|IKey|문자열|
|itemCount|int|ItemCount|int|
|itemId|문자열|\_ItemId|문자열|
|itemType|문자열|Type|String|
|name|문자열|이름|string|
|operation_Id|문자열|OperationId|문자열|
|operation_Name|문자열|OperationName|문자열|
|operation_ParentId|문자열|OperationParentId|문자열|
|operation_SyntheticSource|문자열|OperationSyntheticSource|문자열|
|performanceBucket|문자열|PerformanceBucket|문자열|
|sdkVersion|문자열|SdkVersion|문자열|
|session_Id|문자열|SessionId|문자열|
|timestamp|Datetime|TimeGenerated|Datetime|
|url|문자열|Url|문자열|
|user_AccountId|문자열|UserAccountId|문자열|
|user_AuthenticatedId|문자열|UserAuthenticatedId|문자열|
|user_Id|문자열|UserId|문자열|

### <a name="appperformancecounters"></a>AppPerformanceCounters

레거시 테이블: performanceCounters

|ApplicationInsights|유형|LogAnalytics|유형|
|:---|:---|:---|:---|
|appId|문자열|\_ResourceGUID|문자열|
|application_Version|문자열|AppVersion|문자열|
|appName|문자열|\_ResourceId|문자열|
|category|문자열|범주|문자열|
|client_Browser|문자열|ClientBrowser|문자열|
|client_City|문자열|ClientCity|문자열|
|client_CountryOrRegion|문자열|ClientCountryOrRegion|문자열|
|client_IP|문자열|ClientIP|문자열|
|client_Model|문자열|ClientModel|문자열|
|client_OS|문자열|ClientOS|문자열|
|client_StateOrProvince|문자열|ClientStateOrProvince|문자열|
|client_Type|문자열|ClientType|문자열|
|cloud_RoleInstance|문자열|AppRoleInstance|문자열|
|cloud_RoleName|문자열|AppRoleName|문자열|
|counter|문자열|제거||
|customDimensions|동적|속성|동적|
|iKey|문자열|IKey|문자열|
|인스턴스|문자열|인스턴스|문자열|
|itemId|문자열|\_ItemId|문자열|
|itemType|문자열|Type|문자열|
|name|문자열|이름|string|
|operation_Id|문자열|OperationId|문자열|
|operation_Name|문자열|OperationName|문자열|
|operation_ParentId|문자열|OperationParentId|문자열|
|operation_SyntheticSource|문자열|OperationSyntheticSource|문자열|
|sdkVersion|문자열|SdkVersion|문자열|
|session_Id|문자열|SessionId|문자열|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|문자열|UserAccountId|문자열|
|user_AuthenticatedId|문자열|UserAuthenticatedId|문자열|
|user_Id|문자열|UserId|문자열|
|value|real|값|real|

### <a name="apprequests"></a>AppRequests

레거시 테이블: 요청

|ApplicationInsights|유형|LogAnalytics|유형|
|:---|:---|:---|:---|
|appId|문자열|\_ResourceGUID|문자열|
|application_Version|문자열|AppVersion|문자열|
|appName|문자열|\_ResourceId|문자열|
|client_Browser|문자열|ClientBrowser|문자열|
|client_City|문자열|ClientCity|문자열|
|client_CountryOrRegion|문자열|ClientCountryOrRegion|문자열|
|client_IP|문자열|ClientIP|문자열|
|client_Model|문자열|ClientModel|문자열|
|client_OS|문자열|ClientOS|문자열|
|client_StateOrProvince|문자열|ClientStateOrProvince|문자열|
|client_Type|문자열|ClientType|문자열|
|cloud_RoleInstance|문자열|AppRoleInstance|문자열|
|cloud_RoleName|문자열|AppRoleName|문자열|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|duration|real|DurationMs|Real|
|`id`|문자열|`Id`|String|
|iKey|문자열|IKey|문자열|
|itemCount|int|ItemCount|int|
|itemId|문자열|\_ItemId|문자열|
|itemType|문자열|Type|String|
|name|문자열|Name|String|
|operation_Id|문자열|OperationId|문자열|
|operation_Name|문자열|OperationName|문자열|
|operation_ParentId|문자열|OperationParentId|문자열|
|operation_SyntheticSource|문자열|OperationSyntheticSource|문자열|
|performanceBucket|문자열|PerformanceBucket|String|
|resultCode|문자열|ResultCode|String|
|sdkVersion|문자열|SdkVersion|문자열|
|session_Id|문자열|SessionId|문자열|
|source|문자열|원본|String|
|성공|문자열|성공|Bool|
|timestamp|Datetime|TimeGenerated|Datetime|
|url|문자열|Url|String|
|user_AccountId|문자열|UserAccountId|문자열|
|user_AuthenticatedId|문자열|UserAuthenticatedId|문자열|
|user_Id|문자열|UserId|문자열|

### <a name="appsystemevents"></a>AppSystemEvents

레거시 테이블: 예외

|ApplicationInsights|유형|LogAnalytics|유형|
|:---|:---|:---|:---|
|appId|문자열|\_ResourceGUID|문자열|
|application_Version|문자열|AppVersion|문자열|
|appName|문자열|\_ResourceId|문자열|
|어셈블리|문자열|어셈블리|문자열|
|client_Browser|문자열|ClientBrowser|문자열|
|client_City|문자열|ClientCity|문자열|
|client_CountryOrRegion|문자열|ClientCountryOrRegion|문자열|
|client_IP|문자열|ClientIP|문자열|
|client_Model|문자열|ClientModel|문자열|
|client_OS|문자열|ClientOS|문자열|
|client_StateOrProvince|문자열|ClientStateOrProvince|문자열|
|client_Type|문자열|ClientType|문자열|
|cloud_RoleInstance|문자열|AppRoleInstance|문자열|
|cloud_RoleName|문자열|AppRoleName|문자열|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|자세히|동적|세부 정보|동적|
|handledAt|문자열|HandledAt|문자열|
|iKey|문자열|IKey|문자열|
|innermostAssembly|문자열|InnermostAssembly|문자열|
|innermostMessage|문자열|InnermostMessage|문자열|
|innermostMethod|문자열|InnermostMethod|문자열|
|innermostType|문자열|InnermostType|문자열|
|itemCount|int|ItemCount|int|
|itemId|문자열|\_ItemId|문자열|
|itemType|문자열|Type|문자열|
|message|문자열|메시지|문자열|
|method|문자열|방법|문자열|
|operation_Id|문자열|OperationId|문자열|
|operation_Name|문자열|OperationName|문자열|
|operation_ParentId|문자열|OperationParentId|문자열|
|operation_SyntheticSource|문자열|OperationSyntheticSource|문자열|
|outerAssembly|문자열|OuterAssembly|문자열|
|outerMessage|문자열|OuterMessage|문자열|
|outerMethod|문자열|OuterMethod|문자열|
|outerType|문자열|OuterType|문자열|
|problemId|문자열|ProblemId|문자열|
|sdkVersion|문자열|SdkVersion|문자열|
|session_Id|문자열|SessionId|문자열|
|severityLevel|int|SeverityLevel|int|
|timestamp|Datetime|TimeGenerated|Datetime|
|type|문자열|ExceptionType|문자열|
|user_AccountId|문자열|UserAccountId|문자열|
|user_AuthenticatedId|문자열|UserAuthenticatedId|문자열|
|user_Id|문자열|UserId|문자열|

### <a name="apptraces"></a>AppTraces

레거시 테이블: 추적

|ApplicationInsights|유형|LogAnalytics|유형|
|:---|:---|:---|:---|
|appId|문자열|\_ResourceGUID|문자열|
|application_Version|문자열|AppVersion|문자열|
|appName|문자열|\_ResourceId|문자열|
|client_Browser|문자열|ClientBrowser|문자열|
|client_City|문자열|ClientCity|문자열|
|client_CountryOrRegion|문자열|ClientCountryOrRegion|문자열|
|client_IP|문자열|ClientIP|문자열|
|client_Model|문자열|ClientModel|문자열|
|client_OS|문자열|ClientOS|문자열|
|client_StateOrProvince|문자열|ClientStateOrProvince|문자열|
|client_Type|문자열|ClientType|문자열|
|cloud_RoleInstance|문자열|AppRoleInstance|문자열|
|cloud_RoleName|문자열|AppRoleName|문자열|
|customDimensions|동적|속성|동적|
|customMeasurements|동적|측정|동적|
|iKey|문자열|IKey|문자열|
|itemCount|int|ItemCount|int|
|itemId|문자열|\_ItemId|문자열|
|itemType|문자열|Type|문자열|
|message|문자열|메시지|문자열|
|operation_Id|문자열|OperationId|문자열|
|operation_Name|문자열|OperationName|문자열|
|operation_ParentId|문자열|OperationParentId|문자열|
|operation_SyntheticSource|문자열|OperationSyntheticSource|문자열|
|sdkVersion|문자열|SdkVersion|문자열|
|session_Id|문자열|SessionId|문자열|
|severityLevel|int|SeverityLevel|int|
|timestamp|Datetime|TimeGenerated|Datetime|
|user_AccountId|문자열|UserAccountId|문자열|
|user_AuthenticatedId|문자열|UserAuthenticatedId|문자열|
|user_Id|문자열|UserId|문자열|

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색](../platform/metrics-charts.md)
* [분석 쿼리 작성](../log-query/log-query-overview.md)

