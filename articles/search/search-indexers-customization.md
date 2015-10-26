<properties 
	pageTitle="Azure 검색 인덱서 사용자 지정" 
	description="Azure 검색 인덱서의 설정 및 정책을 사용자 지정하는 방법에 대해 알아보세요." 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/29/2015" 
	ms.author="eugenesh"/>

#Azure 검색 인덱서 사용자 지정

Azure 검색에서 인덱서를 구성하면 데이터 원본과 대상 인덱스 사이에 필드 이름을 바꾸고, 문자열을 데이터베이스 테이블에서 문자열 컬렉션으로 변환하고, 데이터 원본에 대한 데이터 감지 정책을 전환하고, URL 안전하지 않은 문자를 포함하고 있는 문서 키를 URL 인코딩하고, 일부 문서의 인덱싱 실패를 허용할 수 있습니다.

Azure 검색 인덱서에 익숙하지 않은 경우 다음 문서를 먼저 살펴보세요.

- [인덱서를 사용하여 Azure 검색에 Azure SQL 데이터베이스 연결](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [인덱서를 사용해서 DocumentDB를 Azure 검색에 연결](../documentdb/documentdb-search-indexer.md)
- [인덱서를 지원하는 .NET SDK](https://msdn.microsoft.com/library/dn951165.aspx) 또는 
- [인덱서 Azure API 참조](https://msdn.microsoft.com/library/azure/dn946891.aspx)

##데이터 원본과 대상 인덱스 사이의 필드 이름 바꾸기##

**Field mappings**는 필드 정의 간의 차이점을 조정하는 속성입니다. 가장 일반적인 예는 Azure 검색 명명 규칙을 위반하는 필드 이름에서 찾을 수 있습니다. 하나 이상의 필드 이름이 밑줄로 시작하는 원본 테이블을 떠올려 보세요(예: `_id`). Azure 검색에서는 밑줄로 시작하는 필드 이름을 사용할 수 없으므로 필드 이름을 바꿔야 합니다.

다음 예제에서는 데이터 원본의 `_id` 필드를 대상 인덱스에서 `id` 필드로 "이름을 변경"하는 필드 매핑을 포함하도록 인덱서를 업데이트합니다.

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
    } 

**참고:** 필드 매핑에 API 미리 보기 버전 2015-02-28-Preview를 사용해야 합니다.

여러 필드 매핑을 지정할 수 있습니다.

	"fieldMappings" : [ 
		{ "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
	 ]

원본 필드 이름과 대상 필드 이름 모두 대/소문자를 구분하지 않습니다.

##문자열을 데이터베이스 테이블에서 문자열 컬렉션으로 변환##

필드 매핑을 사용하는 경우 *매핑 함수*를 통해 원본 필드 값을 변환할 수도 있습니다.

이러한 함수 중 하나인 `jsonArrayToStringCollection` 함수는 JSON 배열 형식의 문자열이 포함된 필드를 대상 인덱스에서 Collection(Edm.String) 필드로 구문 분석합니다. SQL에는 네이티브 컬렉션 데이터 형식이 없기 때문에 이 함수는 특히 Azure SQL 인덱서와 함께 사용됩니다. 다음과 같이 사용할 수 있습니다.

	"fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

예를 들어 원본 필드에 `["red", "white", "blue"]` 문자열이 포함된 경우 `Collection(Edm.String)` 형식의 대상 필드는 세 개의 값 `"red"`, `"white"` 및 `"blue"`로 채워집니다.

`targetFieldName` 속성은 선택 사항입니다. 비워 둘 경우 `sourceFieldName` 값이 사용됩니다.

##데이터 원본에서 변경 감지 정책 전환##
  
Azure 검색에서 어떤 변경 감지 정책을 적용할 것인지는 데이터 원본 및 데이터 스키마에서 지원되는 유형에 크게 좌우됩니다. 특히 시간이 지나면서 데이터베이스를 업그레이드하거나 마이그레이션하는 경우 변경 감지 정책 다른 유형으로 변경할 수 있습니다. 예를 들어 최근에 통합 변경 추적을 지원하는 최신 버전으로 Azure SQL 데이터베이스를 업데이트했다면 상위 워터 마크 정책에서 통합 변경 추적 정책으로 전환할 수 있습니다. 또는 상위 워터 마크로 다른 열을 사용하도록 결정할 수도 있습니다.

단순히 PUT 데이터 원본 REST API를 호출하여 데이터 원본을 업데이트하면 다음과 비슷한 오류 메시지와 함께 400 응답을 받게 될 것입니다.


	"Change detection policy cannot be changed for data source '…' because indexer '…' references this data source and has a non-empty change tracking state. You can use Reset API to reset the indexer's change tracking state, and retry this call."

 이것이 어떤 의미이며 어떻게 해결해야 하는지 궁금하실 것입니다. 이 오류는 Azure 검색에서 변경 감지 정책과 연결된 내부 상태를 유지하기 때문에 발생합니다. 정책이 변경되면 기존 상태는 새 정책에 적용되지 않기 때문에 무효화됩니다. 즉, 인덱서가 새 정책을 사용하여 데이터 원본 인덱싱을 처음부터 시작해야 합니다. 이는 사용자에게 잠재적으로 영향을 미칩니다(예: 데이터베이스에 추가 부하 또는 추가 네트워킹 대역폭 요금). 그래서 Azure 검색에서는 사용자에게 [인덱서 재설정 API](https://msdn.microsoft.com/library/azure/dn946897.aspx)를 호출하여 현재 변경 감지 정책과 연결된 상태를 다시 설정하라고 요청합니다. 그 후에는 일반 PUT datasource 호출을 사용하여 정책을 변경할 수 있습니다. 물론 Azure 검색에서 재설정 작업을 자동으로 처리할 수도 있지만 저희는 사용자가 재설정 API 호출의 의미를 정확하게 이해하는 것이 중요하다고 생각했습니다.

##URL-안전하지 않은 문자가 포함된 문서 키를 URL 인코딩##

Azure 검색에서 문서 키 필드 내부의 문자는 URL 안전 문자로 제한됩니다. 사용자가 문서 키로 문서를 조회할 수 있어야 하기 때문입니다. 그렇다면 인덱싱할 문서의 키 필드에 이러한 문자가 들어 있다면 어떻게 될까요? 사용자가 클라이언트 SDK 또는 REST API를 사용하여 직접 문서를 인덱싱하는 경우 키를 URL 인코딩할 수 있습니다. 인덱서를 사용하면 인덱서를 만들거나 업데이트할 때 **base64EncodeKeys** 매개 변수를 `true`로 설정하여 키를 URL 인코딩하라고 Azure 검색에 전달할 수 있습니다.

    PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "base64EncodeKeys": true }
    }

인코딩에 대한 자세한 내용은 [MSDN 문서](http://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)를 참조하세요.

참고: 키 값을 검색 또는 필터링해야 하는 경우 요청이 검색 인덱스에 저장된 인코딩된 값과 일치하도록 요청에 사용된 키를 비슷한 방식으로 인코딩해야 합니다.


##일부 문서를 인덱싱할 수 있도록 오류 허용##

기본적으로 Azure 검색 인덱서는 한 문서만 인덱싱에 실패해도 인덱싱을 즉시 중지 합니다. 시나리오에 따라 일부 실패를 허용할 수 있습니다. 전체 데이터 원본을 반복적으로 다시 인덱싱하는 경우를 예로 들 수 있습니다. Azure 검색에서는 이 동작을 미세 조정할 수 있는 두 가지 인덱서 매개 변수를 제공합니다.

- **maxFailedItems**: 인덱서 실행을 실패한 것으로 간주하기 전까지 허용되는 인덱싱 실패 가능 항목의 수입니다. 기본값은 0입니다.
- **maxFailedItemsPerBatch**: 인덱서 실행을 실패한 것으로 간주하기 전까지 단일 배치에서 허용되는 인덱싱 실패 가능 항목의 수입니다. 기본값은 0입니다.

인덱서를 업데이트할 때 이러한 매개 변수 중 하나 또는 둘 모두를 지정하여 언제든지 이러한 값을 변경할 수 있습니다.

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
	Content-Type: application/json
	api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }
    }

일부 실패를 허용하더라도 [인덱서 상태 가져오기 API](https://msdn.microsoft.com/library/azure/dn946884.aspx)에서 실패한 문서에 대한 정보를 반환합니다.

오늘은 여기까지 하겠습니다. 향후 콘텐츠 아이디어에 대해 의견이나 제안이 있다면 #AzureSearch 해시 태그를 사용하여 트윗하거나 [UserVoice 페이지](http://feedback.azure.com/forums/263029-azure-search)에 아이디어를 제출하세요.
 

<!---HONumber=Oct15_HO3-->