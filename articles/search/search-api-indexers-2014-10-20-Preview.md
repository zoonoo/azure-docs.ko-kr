<properties title="" pageTitle="인덱서 작업(Azure 검색 서비스 REST API: 2014-10-20-Preview)" description="인덱서 작업(Azure 검색 서비스 REST API: 2014-10-20-Preview)" services="search" documentationCenter="" authors="HeidiSteen" manager="mblythe" />

<tags ms.service="search" ms.devlang="rest-api" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="na" ms.date="07/08/2015" ms.author="heidist" />

#인덱서 작업(Azure 검색 서비스 REST API: 2014-10-20-Preview)

> [AZURE.NOTE]이 문서에서는 릴리스된 버전의 API에 없는 새 기능의 프로토타입을 설명합니다. 버전 및 지원 가능성에 대한 자세한 내용은 MSDN의 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)에서 확인할 수 있습니다. 이 미리 보기 API의 다른 기능에 대한 자세한 내용은 [Azure 검색 서비스 REST API 버전: 2014-10-20-Preview](search-api-2014-10-20-preview.md)를 참조하세요.

## 개요

코드를 작성하여 데이터를 인덱싱하지 않고도 일부 일반적인 데이터 원본과 Azure 검색을 직접 통합할 수 있습니다. 이를 설정하기 위해 Azure 검색 API를 호출하여 **인덱서** 및 **데이터 원본**을 만들고 관리할 수 있습니다.

**데이터 원본**은 인덱싱해야 하는 데이터, 데이터에 액세스할 수 있는 자격 증명, Azure 검색에서 데이터 변경 내용(예: 데이터베이스 테이블에서 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다.

**인덱서**는 데이터 원본에서 검색 인덱스로 데이터가 이동하는 방식을 설명합니다. 인덱서는 다음을 수행할 수 있습니다.

- 데이터의 일회성 복사 수행
- 일정에 따라 Azure 검색 인덱스를 데이터 원본의 변경 내용과 동기화
- 언제든지 요청 시 호출하여 복사 수행

현재 지원되는 데이터 원본은 다음과 같습니다.

- Azure SQL 데이터베이스
- DocumentDB 

향후 추가 데이터 원본에 대한 지원을 추가할 예정입니다. 이러한 의사 결정의 우선 순위를 지정하는 데 도움이 되도록 [Azure 검색 사용자 의견 포럼](http://feedback.azure.com/forums/263029-azure-search)에서 사용자 의견을 제공해 주시기 바랍니다.

**참고** 여기에 설명된 기능은 Azure 검색 API 버전 `2014-10-20-Preview`부터 지원됩니다.

## 일반적인 사용 흐름

자동 인덱싱을 설정하는 일반적인 단계는 다음과 같습니다.

1. 인덱싱해야 하는 데이터가 포함된 데이터 원본을 식별합니다. Azure 검색에서는 데이터 원본에 있는 일부 데이터 형식을 지원하지 않을 수 있습니다.

2. 스키마가 데이터 원본과 호환되는 Azure 검색 인덱스를 만듭니다.
  
3. 아래의 데이터 원본 작업 섹션에 설명된 대로 Azure 검색 데이터 원본을 만듭니다.
  
4. 아래의 인덱서 작업 섹션에 설명된 대로 Azure 검색 인덱서를 만들고 해당 실행을 모니터링합니다.

## 이름 지정 규칙

- 데이터 원본 이름은 소문자, 숫자 또는 대시만 포함할 수 있고 대시로 시작하거나 끝날 수 없으며 128자로 제한됩니다.
- 인덱서 이름은 소문자, 숫자 또는 대시만 포함할 수 있고 대시로 시작하거나 끝날 수 없으며 128자로 제한됩니다.

## 제한 및 제약 조건

자세한 내용은 [제한 및 제약 조건 페이지](http://msdn.microsoft.com/library/azure/dn798934.aspx)를 참조하세요.

## 데이터 원본 작업
Azure 검색 서비스에서 간단한 HTTP 요청(POST, GET, PUT, DELETE)을 통해 지정된 데이터 원본 리소스에 대해 데이터 원본을 만들고 관리할 수 있습니다.

### 데이터 원본 만들기

HTTP POST 요청을 사용하여 Azure 검색 서비스 내에 새 데이터 원본을 만들 수 있습니다.
	
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

또는 PUT을 사용하여 URI에서 데이터 원본 이름을 지정할 수 있습니다. 데이터 원본이 없으면 만들어집니다.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**참고**: 허용되는 최대 데이터 원본 수는 가격 책정 계층에 따라 다릅니다. 무료 서비스에서는 데이터 원본을 3개까지 사용할 수 있으며 표준 서비스에서는 50개까지 사용할 수 있습니다.

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. POST 또는 PUT 메서드를 사용하여 **데이터 원본 만들기** 요청을 생성할 수 있습니다. POST를 사용할 때는 데이터 원본 정의와 함께 요청 본문에 데이터 원본 이름을 제공해야 합니다. PUT을 사용하는 경우 이름이 URL에 포함됩니다. 데이터 원본이 없으면 만들어지고 이미 있으면 새 정의로 업데이트됩니다.

데이터 원본 이름은 소문자여야 하고 문자나 숫자로 시작되어야 합니다. 또한 슬래시나 마침표를 포함할 수 없으며 128자 미만이어야 합니다. 문자나 숫자로 시작하는 데이터 원본 이름의 뒤에는 원하는 모든 문자, 숫자, 대시(여러 대시를 연속적으로 포함할 수는 없음)를 사용할 수 있습니다.

`api-version`은 필수 사항입니다. 유효한 값에는 `2014-10-20-Preview` 이상 버전이 포함됩니다.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `Content-Type`: 필수 사항입니다. `application/json`으로 설정합니다.
- `api-key`: 필수 사항입니다. `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **데이터 원본 만들기** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key` 헤더를 포함해야 합니다. 
 
요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

<a name="CreateDataSourceRequestSyntax"></a> **요청 본문 구문**

요청 본문에는 데이터 원본 정의가 포함됩니다. 데이터 원본 정의에는 데이터 원본의 형식, 데이터를 읽는 데 필요한 자격 증명 그리고 선택적 데이터 변경/삭제 검색 정책이 들어 있으며, 이러한 정책은 정기적으로 예약되는 인덱서와 함께 사용할 때 데이터 원본에서 변경되거나 삭제된 데이터를 효율적으로 식별하는 데 사용됩니다.


요청 페이로드 구조를 지정하는 구문은 다음과 같습니다. 이 항목에서 샘플 요청이 추가로 제공됩니다.

    { 
		"name" : "Required for POST, optional for PUT. The name of the data source",
    	"description" : "Optional. Anything you want, or nothing at all",
    	"type" : "Required. Must be 'azuresql' or 'docdb'",
    	"credentials" : { "connectionString" : "Required. Connection string for your Azure SQL database" },
    	"container" : { "name" : "Required. The name of the table or collection you wish to index" },
    	"dataChangeDetectionPolicy" : { Optional. See below for details }, 
    	"dataDeletionDetectionPolicy" : { Optional. See below for details }
	}

요청에는 다음 속성이 포함될 수 있습니다.

- `name`: 필수 사항입니다. 데이터 소스의 이름입니다. 데이터 원본 이름은 소문자, 숫자 또는 대시만 포함할 수 있고 대시로 시작하거나 끝날 수 없으며 128자로 제한됩니다.
- `description`: 선택적 설명입니다. 
- `type`: 필수 사항입니다. Azure SQL 데이터베이스의 경우 `azuresql`을 사용하고, DocumentDB의 경우 `docdb`를 사용합니다.
- `container`: 
	- 필수 `name` 속성은 인덱싱할 테이블/보기(Azure SQL 데이터 원본의 경우) 또는 컬렉션(DocumentDB 데이터 원본의 경우)을 지정합니다. 
	- DocumentDB 데이터 원본은 선택적 `query` 속성도 지원합니다. 이 속성을 사용하면 Azure 검색에서 인덱싱할 수 있는 플랫 스키마로 임의 JSON 문서 레이아웃을 평면화하는 쿼리를 지정할 수 있습니다.   
- 아래에 선택적 `dataChangeDetectionPolicy` 및 `dataDeletionDetectionPolicy`에 대한 설명이 나와 있습니다.

<a name="DataChangeDetectionPolicies"></a> **데이터 변경 검색 정책**

데이터 변경 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 지원되는 정책은 데이터 원본 형식에 따라 다릅니다. 아래 섹션에서 각 정책에 대해 설명합니다.

***상위 워터마크 변경 검색 정책***

다음 기준을 충족하는 열이나 속성이 데이터 원본에 포함되어 있으면 이 정책을 사용합니다.
 
- 모든 삽입 시 열의 값을 지정합니다. 
- 항목에 대한 모든 업데이트는 열의 값도 변경합니다. 
- 각 변경 시에 열의 값이 증가합니다.
- `WHERE [High Water Mark Column] > [Current High Water Mark Value]`와 같은 필터 절을 사용하는 쿼리를 효율적으로 실행할 수 있습니다.

예를 들어 Azure SQL 데이터 원본을 사용하는 경우 상위 워터마크 정책을 사용하기에 적합한 열은 인덱싱된 `rowversion` 열입니다.

DocumentDB 데이터 원본을 사용할 때는 DocumentDB에서 제공되는 `_ts` 속성을 사용해야 합니다.
 
이 정책은 다음과 같이 지정할 수 있습니다.

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
		"highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	} 

***SQL 통합 변경 검색 정책***

SQL 데이터베이스에서 [SQL 통합 변경 내용 추적](http://technet.microsoft.com/library/cc280462.aspx)을 지원하는 경우, SQL 통합 변경 내용 추적 정책을 사용하는 것이 좋습니다. 이 정책을 사용하면 변경 내용을 가장 효율적으로 추적할 수 있으며, 스키마에서 "soft delete" 열을 명시적으로 지정하지 않아도 Azure 검색에서 삭제된 행을 식별할 수 있습니다.

SQL 통합 변경 내용 추적 기능은 다음 SQL 데이터베이스 버전부터 지원됩니다. - SQL Server 2008 R2(SQL IaaS VM을 사용하는 경우) - Azure SQL 데이터베이스 V12(Azure SQL을 사용하는 경우)

**참고:** SQL 통합 변경 내용 추적 정책을 사용할 때는 별도의 데이터 삭제 검색 정책을 지정하지 마세요. 이 정책은 삭제된 행 식별을 기본적으로 지원합니다.

**참고:** 이 정책은 테이블에만 사용할 수 있으며 보기에는 사용할 수 없습니다. 이 정책을 사용하려면 사용 중인 테이블에 대해 변경 내용 추적을 사용하도록 설정해야 합니다.
 
SQL 통합 변경 내용 추적 정책을 다음과 같이 지정할 수 있습니다.

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	}

<a name="DataDeletionDetectionPolicies"></a> **데이터 삭제 검색 정책**

데이터 삭제 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 현재 지원되는 정책은 다음과 같이 지정되는 `Soft Delete` 정책뿐입니다.

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
		"softDeleteColumnName" : "the column that specifies whether a row was deleted", 
		"softDeleteMarkerValue" : "the value that identifies a row as deleted" 
	}

<a name="CreateDataSourceRequestExamples"></a> **요청 본문 예제**

일정에 따라 실행되는 인덱서에서 데이터 원본을 사용하려는 경우 이 예제에 나와 있는 정책 힌트 지정 방법을 참조할 수 있습니다.

    { 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : true }
	}

일회성 데이터 복사에만 데이터 원본을 사용하려는 경우에는 정책 힌트를 생략해도 됩니다.

    { 
		"name" : "asqldatasource",
    	"description" : "anything you want, or nothing at all",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" }
	} 

**응답**

요청이 성공적으로 실행되면 '201 생성됨'이 반환됩니다.

### 데이터 원본 업데이트

HTTP PUT 요청을 사용하여 기존 데이터 원본을 업데이트할 수 있습니다. 요청 URI에서 업데이트할 데이터 원본의 이름을 지정합니다.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**요청** 요청 본문 구문은 [데이터 원본 만들기 요청](#CreateDataSourceRequestSyntax)의 본문 구문과 같습니다.

**응답** 요청이 성공적으로 실행되면 새 데이터 원본이 생성된 경우 ‘201 생성됨’이 반환되고 기존 데이터 원본이 업데이트된 경우 ‘204 콘텐츠 없음’이 반환됩니다.

**참고:** 기존 데이터 원본의 경우 일부 속성을 업데이트할 수 없습니다. 예를 들어 기존 데이터 원본의 형식은 변경할 수 없습니다.

### 데이터 원본 나열

**데이터 원본 나열** 작업은 Azure 검색 서비스에 있는 데이터 원본의 목록을 반환합니다.

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

**응답**

요청이 성공적으로 실행되면 '200 OK'가 반환됩니다.

아래에는 예제 응답 본문이 나와 있습니다.

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
		  ... other data source properties
        }]
    }

원하는 속성만 포함하도록 응답을 필터링할 수 있습니다. 예를 들어 데이터 원본 이름 목록만 포함하려면 다음과 같이 OData `$select` 쿼리 옵션을 사용합니다.

    GET /datasources?api-version=2014-10-20-Preview&$select=name

이 경우 위 예제의 응답은 다음과 같이 표시됩니다.

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

이 기술은 검색 서비스에 많은 데이터 원본이 있는 경우 대역폭을 절감하는 데 유용합니다.

### 데이터 원본 가져오기

**데이터 원본 가져오기** 작업은 Azure 검색에서 데이터 원본 정의를 가져옵니다.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답은 [데이터 원본 만들기 예제 요청](#CreateDataSourceRequestExamples)의 예제와 유사합니다.

	{ 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
			"highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
			"softDeleteColumnName" : "IsDeleted", 
			"softDeleteMarkerValue" : true }
	}

**참고** 이 API를 호출할 때는 `Accept` 요청 헤더를 `application/json;odata.metadata=none`으로 설정하지 마세요. 이렇게 설정하면 `@odata.type` 특성이 응답에서 생략되며, 서로 다른 유형의 데이터 변경 및 데이터 삭제 검색 정책을 구분할 수 없습니다.

### 데이터 원본 삭제

**데이터 원본 삭제** 작업은 Azure 검색 서비스에서 데이터 원본을 제거합니다.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

**참고** 삭제하는 데이터 원본을 참조하는 인덱서가 있어도 삭제 작업은 진행됩니다. 그러나 이러한 인덱서는 다음에 실행할 때 오류 상태로 전환됩니다.

**응답**

상태 코드: 응답에 성공하면 ‘204 콘텐츠 없음’이 반환됩니다.

## 인덱서 작업

인덱서는 데이터 원본을 대상 검색 인덱스에 연결하는 리소스입니다. 모든 대상 인덱스 및 데이터 소스 조합에 대해 하나의 인덱스를 생성하도록 계획해야 합니다. 동일한 인덱스에 쓰는 여러 인덱서를 만들 수 있습니다. 그러나 하나의 인덱스를 단일 인덱스에만 쓸 수 있습니다.

지정된 인덱서 리소스에 대한 간단한 HTTP 요청(POST, GET, PUT, DELETE)을 통해 인덱서를 만들고 관리할 수 있습니다.

인덱서를 만든 후에는 [인덱서 상태 가져오기](#GetIndexerStatus) 작업을 사용하여 해당 실행 상태를 검색할 수 있습니다. 또한 [인덱서 실행](#RunIndexer) 작업을 사용하여 언제든지 인덱서를 실행할 수 있습니다(일정에 따라 주기적으로 실행하는 대신 또는 이러한 방법 외에).

### 인덱서 만들기

HTTP POST 요청을 사용하여 Azure 검색 서비스 내에서 새 인덱서를 만들 수 있습니다.
	
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

또는 PUT을 사용하여 URI에서 데이터 원본 이름을 지정할 수 있습니다. 데이터 원본이 없으면 만들어집니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

**참고**: 허용되는 최대 인덱서 수는 가격 책정 계층에 따라 다릅니다. 무료 서비스에서는 인덱서를 3개까지 사용할 수 있으며 표준 서비스에서는 50개까지 사용할 수 있습니다.

<a name="CreateIndexerRequestSyntax"></a> **요청 본문 구문**

요청 본문에는 인덱서 정의가 포함됩니다. 이 정의는 인덱싱의 데이터 원본 및 대상 인덱스와 선택적 인덱싱 일정 및 매개 변수를 지정합니다.


요청 페이로드 구조를 지정하는 구문은 다음과 같습니다. 이 항목에서 샘플 요청이 추가로 제공됩니다.

    { 
		"name" : "Required for POST, optional for PUT. The name of the indexer",
    	"description" : "Optional. Anything you want, or null",
    	"dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. }
	}

**인덱싱 일정**

인덱서는 선택적으로 일정을 지정할 수 있습니다. 일정이 제공된 경우, 인덱서가 일정에 따라 주기적으로 실행됩니다. 일정은 다음과 같은 특성을 갖습니다.

- `interval`: 필수 사항입니다. 인덱서 실행 간격 또는 기간을 지정하는 기간 값입니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값([ISO 8601 기간](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. 예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다. 

- `startTime`: 필수 사항입니다. 인덱서 실행을 시작해야 하는 UTC 날짜/시간입니다.

**인덱싱 매개 변수**

인덱서는 필요에 따라 동작에 영향을 주는 여러 매개 변수를 지정할 수 있습니다. 모든 매개 변수는 선택 사항입니다.

- `maxFailedItems`: 인덱서 실행을 실패한 것으로 간주하기 전까지 허용되는 인덱싱 실패 가능 항목의 수입니다. 기본값은 `0`입니다. 실패한 항목에 대한 정보는 [인덱서 상태 가져오기](#GetIndexerStatus) 작업에서 반환됩니다. 

- `maxFailedItemsPerBatch`: 인덱서 실행을 실패한 것으로 간주하기 전까지 각 일괄 처리에서 허용되는 인덱싱 실패 가능 항목의 수입니다. 기본값은 `0`입니다.

- `base64EncodeKeys`: 문서 키를 base-64로 인코딩할지 여부를 지정합니다. Azure 검색에서는 문서 키에 포함할 수 있는 문자에 제한이 적용됩니다. 그러나 원본 데이터의 값은 유효하지 않은 문자를 포함할 수 있습니다. 이러한 값을 문서 키로 인덱싱해야 하는 경우에는 이 플래그를 true로 설정할 수 있습니다. 기본값은 `false`입니다.

<a name="CreateIndexerRequestExamples"></a> **요청 본문 예제**

다음 예제에서는 `ordersds` 데이터 원본에서 참조하는 테이블의 데이터를 2015년 1월 1일 UTC에 시작하여 매 시간 실행되는 일정으로 `orders` 인덱스에 복사하는 인덱서를 만듭니다. 각 일괄 처리에서 인덱싱에 실패하는 항목이 5개 이하이며 인덱싱에 실패하는 총 항목 수가 10개 이하이면 각 인덱서 호출은 성공합니다.

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}

**응답**

요청이 성공적으로 실행되면 '201 생성됨'이 반환됩니다.

### 인덱서 업데이트

HTTP PUT 요청을 사용하여 기존 인덱서를 업데이트할 수 있습니다. 요청 URI에서 업데이트할 인덱서의 이름을 지정합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**요청**

요청 본문 구문은 [인덱서 만들기 요청](#CreateIndexerRequestSyntax)의 본문 구문과 같습니다.

**응답**

요청이 성공적으로 실행되면 새 인덱서가 생성된 경우 ‘201 생성됨’이 반환되고 기존 인덱서가 업데이트된 경우 ‘204 콘텐츠 없음’이 반환됩니다.

### 인덱서 나열

**인덱서 나열** 작업은 Azure 검색 서비스의 인덱서 목록을 반환합니다.

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]

**응답**

요청이 성공적으로 실행되면 '200 OK'가 반환됩니다.

아래에는 예제 응답 본문이 나와 있습니다.

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
	  }]
    }

원하는 속성만 포함하도록 응답을 필터링할 수 있습니다. 예를 들어 인덱서 이름 목록만 포함하려면 다음과 같이 OData `$select` 쿼리 옵션을 사용합니다.

    GET /indexers?api-version=2014-10-20-Preview&$select=name

이 경우 위 예제의 응답은 다음과 같이 표시됩니다.

    {
      "value" : [ { "name": "myindexer" } ]
    }

이 기술은 검색 서비스에 많은 인덱서가 있는 경우 대역폭을 절감하는 데 유용합니다.

### 인덱서 가져오기

**인덱서 가져오기** 작업은 Azure 검색에서 인덱서 정의를 가져옵니다.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답은 [인덱서 만들기 예제 요청](#CreateIndexerRequestExamples)의 예제와 유사합니다.

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}

### 인덱서 삭제

**인덱서 삭제** 작업은 Azure 검색 서비스에서 인덱서를 제거합니다.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

인덱서를 삭제하면 해당 시점에 진행 중이던 인덱서 실행은 완료될 때까지 실행되지만 추가 실행은 예약되지 않습니다. 없는 인덱서를 사용하려고 하면 HTTP 상태 코드 ‘404 찾을 수 없음’이 반환됩니다.
 
**응답**

상태 코드: 응답에 성공하면 ‘204 콘텐츠 없음’이 반환됩니다.

<a name="RunIndexer"></a>
### 인덱서 실행

인덱서를 일정에 따라 주기적으로 실행할 수 있을 뿐 아니라 **인덱서 실행** 작업을 통해 요청 시에 호출할 수도 있습니다.

	POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

**응답**

상태 코드: 응답에 성공하면 ‘202 수락됨'이 반환됩니다.

<a name="GetIndexerStatus"></a>
### 인덱서 상태 가져오기

**인덱서 상태 가져오기** 작업은 인덱서의 현재 상태 및 실행 기록을 검색합니다.

	GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답 본문에는 전반적인 인덱서 상태, 마지막 인덱서 호출 및 최근 인덱서 호출 기록(있는 경우)에 대한 정보가 포함됩니다.

아래에 샘플 응답 본문이 나와 있습니다.

	{
		"status":"running",
		"lastResult": {
			"status":"success",
			"errorMessage":null,
			"startTime":"2014-11-26T03:37:18.853Z",
			"endTime":"2014-11-26T03:37:19.012Z",
			"errors":[],
			"itemsProcessed":11,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null
         },
		"executionHistory":[ {
			"status":"success",
         	"errorMessage":null,
			"startTime":"2014-11-26T03:37:18.853Z",
			"endTime":"2014-11-26T03:37:19.012Z",
			"errors":[],
			"itemsProcessed":11,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null
		}]
	}

**인덱서 상태**

인덱서 상태는 다음 값 중 하나일 수 있습니다.

- `running`: 인덱서가 정상적으로 실행되고 있음을 나타냅니다. 이 상태가 표시되더라도 일부 인덱서 실행은 실패할 수 있으므로 `lastResult` 속성도 확인하는 것이 좋습니다. 

- `error`: 인덱서에서 오류가 발생하여 사용자가 직접 해결해야 함을 나타냅니다. 데이터 원본 자격 증명이 만료되었거나 대상 인덱스 또는 데이터 원본의 스키마가 잘못 변경된 경우를 예로 들 수 있습니다.

**인덱서 실행 결과**

인덱서 실행 결과에는 단일 인덱서 실행에 대한 정보가 포함됩니다. 최신 결과는 인덱서 상태의 `lastResult` 속성으로 표시됩니다. 기타 최근 결과는 있는 경우 인덱서 상태의 `executionHistory` 속성으로 반환됩니다.

인덱서 실행 결과에는 다음 속성이 포함됩니다.

- `status`: 이 실행의 상태입니다. 자세한 내용은 아래의 [인덱서 실행 상태](#IndexerExecutionStatus)를 참조하세요. 

- `errorMessage`: 실패한 실행에 대한 오류 메시지입니다.

- `startTime`: 이 실행이 시작된 시간(UTC)입니다.

- `endTime`: 이 실행이 종료된 시간(UTC)입니다. 계속 실행 중인 경우에는 이 값이 설정되지 않은 것입니다.

- `errors`: 항목 수준 오류(있는 경우)의 목록입니다.

- `itemsProcessed`: 이 실행 중에 인덱서에서 인덱싱하려고 한 데이터 원본 항목(예: 테이블 행)의 수입니다.

- `itemsFailed`: 이 실행 중에 인덱싱하지 못한 항목의 수입니다.
 
- `initialTrackingState`: 첫 번째 인덱서 실행인 경우 또는 사용하는 데이터 원본에 대해 데이터 변경 내용 추적 정책을 사용하도록 설정하지 않은 경우에는 항상 `null`입니다. 해당 정책을 사용하도록 설정한 경우 후속 실행에서 이 값은 해당 실행에서 처리한 첫 번째(최소) 변경 내용 추적 값을 나타냅니다.

- `finalTrackingState`: 사용하는 데이터 원본에 대해 데이터 변경 내용 추적 정책을 사용하도록 설정하지 않은 경우 항상 `null`입니다. 그렇지 않으면 해당 실행에서 정상적으로 처리한 최신(최대) 변경 내용 추적 값을 나타냅니다.

<a name="IndexerExecutionStatus"></a> **인덱서 실행 상태**

인덱서 실행 상태에는 단일 인덱서 실행의 상태가 캡처됩니다. 이 상태의 값은 다음과 같을 수 있습니다.

- `success`: 인덱서 실행이 정상적으로 완료되었음을 나타냅니다.

- `inProgress`: 인덱서 실행이 진행 중임을 나타냅니다.

- `transientFailure`: 인덱서 호출에 실패했지만 일시적인 실패일 수 있음을 나타냅니다. 인덱서 호출은 일정(있는 경우)에 따라 계속 진행됩니다.

- `persistentFailure`: 사용자가 작업을 수행해야 방식(예: 데이터 원본과 대상 인덱스 간의 스키마 비호환성으로 인해)에서 인덱서 오류가 발생했음을 나타냅니다. 이 상태에서는 예약된 인덱서 실행이 중지됩니다. 사용자가 문제(`errorMessage` 속성에 설명)를 해결한 후 인덱서 실행을 다시 시작해야 합니다.

- `reset`: 인덱서 다시 설정 API(아래 참조) 호출을 통해 인덱서가 다시 설정되었음을 나타냅니다.

<a name="ResetIndexer"></a>
### 인덱서 다시 설정

**인덱서 다시 설정** 작업은 인덱서와 관련된 변경 내용 추적 상태를 다시 설정합니다. 그러면 데이터 원본 스키마가 변경된 등의 경우 인덱스를 처음부터 다시 트리거하거나 인덱서와 연결된 데이터 원본의 데이터 변경 검색 정책을 변경할 수 있습니다.

	POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

**응답**

상태 코드: 응답에 성공하면 ‘204 콘텐츠 없음'이 반환됩니다.

## SQL 데이터 형식과 Azure 검색 데이터 형식 사이의 매핑

<table style="font-size:12">
<tr>
<td>SQL 데이터 형식</td>	
<td>허용되는 대상 인덱스 필드 유형</td>
<td>참고 사항</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>real, float</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, money<br>decimal<br>numeric
</td>
<td>Edm.String</td>
<td>Azure 검색에서는 decimal 형식을 Edm.Double로 변환하면 정밀도가 떨어지기 때문에 이를 지원하지 않습니다.
</td>
</tr>
<tr>
<td>char, nchar, varchar, nvarchar</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>rowversion</td>
<td>해당 없음</td>
<td>행 버전 열은 변경 내용 추적에 사용할 수 있지만 검색 인덱스에 저장할 수는 없습니다.</td>
</tr>
<tr>
<td>time, timespan<br>binary, varbinary, image<br>xml<br>geometry<br> geography<br>CLR 형식</td>
<td>해당 없음</td>
<td>지원되지 않음</td>
</tr>
</table>

## JSON 데이터 형식과 Azure 검색 데이터 형식 사이의 매핑

<table style="font-size:12">
<tr>
<td>JSON 데이터 형식</td>	
<td>허용되는 대상 인덱스 필드 유형</td>
<td>참고 사항</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>정수</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td>JSON은 정수 형식일 수 없으므로 가장 넓은 64비트 정수로 가정해야 합니다.</td>
</tr>
<tr>
<td>부동 소수점 숫자</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>string</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>기본 형식의 배열, 예: ["a", "b", "c"]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>날짜처럼 보이는 문자열</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>JSON 개체</td>
<td>해당 없음</td>
<td>지원되지 않음. Azure 검색에서는 현재 기본 형식과 문자열 컬렉션만 지원합니다.</td>
</tr>
</table>

<!---HONumber=August15_HO6-->