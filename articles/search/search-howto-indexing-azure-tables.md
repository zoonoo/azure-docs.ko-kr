<properties
pageTitle="Azure 검색을 사용하여 Azure 테이블 저장소 인덱싱"
description="Azure 검색을 사용하여 Azure 테이블에 저장된 데이터를 인덱싱하는 방법을 알아봅니다"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="05/12/2016"
ms.author="eugenesh" />

# Azure 검색을 사용하여 Azure 테이블 저장소 인덱싱

이 문서에서는 Azure 검색을 사용하여 Azure 테이블 저장소에 저장된 데이터를 인덱싱하는 방법을 보여 줍니다. 새로운 Azure 검색 테이블 인덱서로 이 과정을 신속하게 원활하게 수행할 수 있습니다.

> [AZURE.IMPORTANT] 이 기능은 현재 미리 보기 상태입니다. **2015-02-28-Preview** 버전을 사용하여 REST API로만 제공됩니다. 미리 보기 API는 테스트 및 평가 용도로 제공되며 프로덕션 환경에는 사용되지 않는다는 점을 유념하세요.

## 테이블 인덱싱 설정

Azure 테이블 인덱서를 설정 및 구성하려면 [인덱서 작업](https://msdn.microsoft.com/library/azure/dn946891.aspx)에 설명된 대로 Azure 검색 REST API를 사용하여 **인덱서** 및 **데이터 원본**을 만들고 관리할 수 있습니다. 향후에는 테이블 인덱싱에 대한 지원이 Azure 검색 .NET SDK 및 Azure 포털에 추가될 예정입니다.

데이터 원본은 인덱싱할 데이터, 데이터에 액세스하는 데 필요한 자격 증명, Azure 검색에서 데이터 변경 내용(예: 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다.

인덱서는 데이터 원본에서 데이터를 읽어 대상 검색 인덱스에 로드합니다.

테이블 인덱싱을 설정하려면

1. Azure 저장소 계정에서 테이블(및 필요에 따라, 쿼리)을 참조하는 `azuretable` 유형의 데이터 원본을 만듭니다.
	- 저장소 계정 연결 문자열을 `credentials.connectionString` 매개 변수로 전달합니다.
	- `container.name` 매개 변수를 사용하여 테이블 이름을 지정합니다.
	- 필요에 따라 `container.query` 매개 변수를 사용하여 쿼리를 지정합니다. 가능한 경우 최상의 성능을 위해 PartitionKey에서 필터를 사용합니다. 다른 쿼리를 사용하면 전체 테이블이 검색되므로 테이블이 큰 경우 성능이 저하될 수 있습니다.
2. 인덱싱하려는 테이블의 열에 해당하는 스키마로 검색 인덱스를 만듭니다. 
3. 검색 인덱스에 데이터 원본을 연결하여 인덱서를 만듭니다.

### 데이터 원본 만들기

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "table-datasource",
	    "type" : "azuretable",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
	}   

데이터 원본 만들기 API에 대한 자세한 내용은 [데이터 원본 만들기](search-api-indexers-2015-02-28-preview.md#create-data-source)를 참조하세요.

### 인덱스 만들기 

	POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/json
	api-key: [admin key]

	{
  		"name" : "my-target-index",
  		"fields": [
    		{ "name": "key", "type": "Edm.String", "key": true, "searchable": false },
    		{ "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
  		]
	}

인덱스 만들기 API에 대한 자세한 내용은 [인덱스 만들기](https://msdn.microsoft.com/library/dn798941.aspx)를 참조하세요.

### 인덱서 만들기 

마지막으로 데이터 원본과 대상 인덱스를 참조하는 인덱서를 만듭니다. 예:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "table-indexer",
	  "dataSourceName" : "table-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](search-api-indexers-2015-02-28-preview.md#create-indexer)를 확인하세요.

이제 모든 인덱싱 작업을 마쳤습니다.

## 다른 필드 이름 처리

기존 인덱스의 필드 이름이 테이블의 속성 이름과 달라지는 경우가 종종 있습니다. **필드 매핑**을 사용하여 테이블의 속성 이름을 검색 인덱스의 필드 이름에 매핑할 수 있습니다. 필드 매핑에 대해 자세히 알아 보려면 [데이터 원본 및 검색 인덱스의 차이를 극복하는 Azure 검색 인덱서 필드 매핑](search-indexer-field-mappings.md)을 참조하세요.

## 문서 키 처리

Azure 검색에서는 문서 키가 문서를 고유하게 식별합니다. 모든 검색 인덱스는 `Edm.String` 형식의 키 필드를 정확히 하나만 포함해야 합니다. 인덱스에 추가할 각 문서에는 키 필드(실제로 유일한 필수 필드)가 필요합니다.

테이블 행에 복합 키가 있으므로 Azure 검색에서 파티션 키와 행 키 값을 연결한 `Key`라는 합성 필드를 생성합니다. 예를 들어 행의 PartitionKey가 `PK1`이고 RowKey가 `RK1`인 경우 `Key` 필드의 값은 `PK1RK1`이 됩니다.

> [AZURE.NOTE] `Key` 값은 문서 키의 유효하지 않은 문자(예: 대시)를 포함할 수 있습니다. `base64Encode` [필드 매핑 함수](search-indexer-field-mappings.md#base64EncodeFunction)를 사용하여 잘못된 문자를 처리할 수 있습니다. 또한 이 작업을 수행하는 경우 조회 같은 API 호출에서 문서 키 전달 시 URL 안전 Base64 인코딩을 사용해야 합니다.

## 증분 인덱싱 및 삭제 감지
 
일정에 따라 실행되는 테이블 인덱서를 설정한 경우 이 인덱서는 행의 `Timestamp` 값에 따라 새 행 또는 업데이트된 행만 다시 인덱싱합니다. 변경 감지 정책을 지정하지 않아도 됩니다. 증분 인덱싱이 자동으로 사용됩니다.

인덱스에서 특정 문서를 제거해야 함을 나타내려면 소프트 삭제 전략을 사용합니다. 행을 삭제하는 대신, 삭제됨을 나타내는 속성을 추가하고 데이터 원본에 대한 소프트 삭제 감지 정책을 설정합니다. 예를 들어 아래에 표시된 정책은 `"true"` 값의 속성 `IsDeleted`가 있는 경우 행이 삭제됨을 고려합니다.

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]
	
	{
	    "name" : "my-table-datasource",
	    "type" : "azuretable",
	    "credentials" : { "connectionString" : "<your storage connection string>" },
	    "container" : { "name" : "table name", "query" : "query" },
	    "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
	}   


## Azure 검색 개선 지원

기능 요청 또는 개선에 대한 아이디어가 있는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search/)를 통해 연락해 주세요.

<!---HONumber=AcomDC_0518_2016-->