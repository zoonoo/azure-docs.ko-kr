<properties 
	pageTitle="DocumentDB 인덱싱 정책 | Azure" 
	description="인덱싱이 DocumentDB에서 작동하는 방식을 이해하고 인덱싱 정책을 구성하는 방법을 알아봅니다." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/13/2015" 
	ms.author="mimig"/>


DocumentDB 인덱싱 정책
============================

DocumentDB는 진정한 스키마 없는 데이터베이스입니다. 인덱싱하는 JSON 문서에 대해 스키마를 가정하거나 요구하지 않습니다. 따라서 신속하게 응용 프로그램 데이터 모델을 정의하여 응용 프로그램 데이터 모델에 대해 반복할 수 있습니다. 컬렉션에 문서를 추가하면 DocumentDB에서 자동으로 모든 문서 속성을 인덱싱하므로 쿼리에 이러한 속성을 사용할 수 있습니다. 자동 인덱싱을 통해 다른 유형의 문서를 저장할 수도 있습니다.

문서의 자동 인덱싱은 쓰기 최적화, 잠금 없는 로그 구조 인덱스 유지 관리 기술로 가능합니다. DocumentDB는 일관성 있는 쿼리를 제공하는 동시에 빠른 쓰기의 지속적인 볼륨을 지원합니다.

DocumentDB 인덱싱 하위 시스템은 다음을 지원하도록 설계되었습니다.

·         스키마 또는 인덱스 정의가 없는 효율적이고 풍부한 계층적 및 관계형 쿼리

·         지속적인 쓰기 볼륨을 처리하는 동안 일관성 있는 쿼리 결과 일관성 있는 쿼리와 더불어 높은 쓰기 처리량 작업을 위해 지속적인 쓰기 볼륨을 처리하는 동안 인덱스가 온라인에서 효율적으로 증분 업데이트됩니다.

·         효율적인 저장소 비용 효율성을 위해 인덱스의 디스크에 있는 저장소 오버헤드가 제한되고 예측 가능합니다.

·         다중 테넌트 지원 인덱스 업데이트가 DocumentDB 컬렉션당 할당된 시스템 리소스의 예산 내에서 수행됩니다.

대부분의 응용 프로그램에 대해 기본 자동 인덱싱 정책을 사용할 수 있습니다. 기본 자동 인덱싱 정책만으로도 최대 유연성이 보장되고 성능과 저장소 효율성 간의 균형을 잘 유지할 수 있기 때문입니다. 반면에 사용자 지정 인덱싱 정책을 지정하면 쿼리 성능, 쓰기 성능 및 인덱스 저장소 오버헤드 간의 균형을 좀 더 세분화하여 조정할 수 있습니다.

예를 들어 특정 문서나 문서 내의 경로를 인덱싱에서 제외하여 인덱싱에 사용되는 저장소 공간을 줄일 수 있을 뿐만 아니라 인덱스 유지 관리를 위한 삽입 시간 비용도 줄일 수 있습니다. 범위 쿼리에 더 적합하도록 인덱스 유형을 변경하거나 쿼리 성능을 향상하기 위해 인덱스 자릿수(바이트)를 늘릴 수 있습니다. 이 문서에서는 DocumentDB에서 사용할 수 있는 다양한 인덱싱 구성 옵션과 작업에 맞게 인덱싱 정책을 사용자 지정하는 방법을 설명합니다.

<a id="HowWorks"></a>DocumentDB 인덱싱 작동 방법
-----------------------------

DocumentDB의 인덱싱은 JSON 문법에서 문서를 **트리로 표현**할 수 있다는 사실을 이용합니다. JSON 문서를 트리로 표현하려면 문서 아래에 있는 나머지 실제 노드의 상위 항목이 되는 더미 루트 노드를 만들어야 합니다. JSON 문서의 배열 인덱스를 포함하는 각 레이블은 트리의 노드가 됩니다. 아래 그림은 예제 JSON 문서 및 해당 트리 표현을 보여 줍니다.

![Indexing Policies](media/documentdb-indexing-policies/image001.png)


예를 들어 위 예제의 JSON 속성 {"headquarters": "Belgium"}은 /"headquarters"/"Belgium" 경로에 해당합니다. JSON 배열 {"exports": [{"city": "Moscow"}, {"city": Athens"}]}는 /"exports"/0/"city"/"Moscow" 및 /"exports"/1/"city"/"Athens" 경로에 해당합니다.

**참고** 경로 표현은 문서의 구조/스키마와 인스턴스 값 간의 경계를 모호하게 하여 DocumentDB를 진정한 스키마 없는 데이터베이스로 만들어 줍니다.

DocumentDB에서 문서는 SQL을 사용하여 쿼리될 수 있거나 단일 트랜잭션 범위 내에서 처리될 수 있는 컬렉션으로 구성됩니다. 각 컬렉션은 경로를 기준으로 표현된 고유한 인덱싱 정책으로 구성할 수 있습니다. 다음 섹션에서는 DocumentDB 컬렉션의 인덱싱 동작 구성하는 방법을 살펴보겠습니다.

<a id="ConfigPolicy"></a>컬렉션의 인덱싱 정책 구성
-------------------------------------------

다음 샘플은 컬렉션을 만드는 동안 DocumentDB REST API를 사용하여 사용자 지정 인덱싱 정책을 설정하는 방법을 보여 줍니다. 이 샘플은 경로, 인덱스 유형 및 자릿수를 기준으로 표현된 인덱싱 정책을 나타냅니다.


	POST https://<REST URI>/colls HTTP/1.1                                                  
 	...                                                             
 	Accept: application/json 
                                                                                                                         
 	{                                                                     
	 "id":"customIndexCollection",                                     
	 "indexingPolicy":{                                                 
     "automatic":true,                                            
	 "indexingMode":"Consistent",                                     
	 "IncludedPaths":[                                       
	           {                                                             
	              "IndexType":"Hash",                                        
	              "Path":"/"                                                 
	           }                                                  
	        ],                                                               
	        "ExcludedPaths":[                                                
	           "/"nonIndexedContent"/*"                                 
	        ]                                                               
	     }                                                                 
	 }                                                                                                                                                
 	...                                                                      
                  
                                                        
	 HTTP/1.1 201 Created                                                     


**참고:** 컬렉션의 인덱싱 정책은 컬렉션을 만들 때 지정해야 합니다. 컬렉션을 만든 후에는 인덱싱 정책을 수정할 수 없지만 DocumentDB의 이후 릴리스에서는 이러한 수정 기능이 지원될 예정입니다.

**참고:** 기본적으로 DocumentDB는 해시 인덱스를 사용하여 문서 내의 모든 경로를 일관되게 인덱싱합니다. 내부 타임스탬프(_ts) 경로는 범위 인덱스와 함께 저장됩니다.

### 자동 인덱싱

컬렉션에서 모든 문서를 자동으로 인덱싱하도록 할지 여부를 선택할 수 있습니다. 기본적으로 모든 문서는 자동으로 인덱싱되지만 이 기능을 해제하도록 선택할 수 있습니다. 인덱싱을 해제하면 자체 링크를 통해서나 ID를 사용한 쿼리로만 문서에 액세스할 수 있습니다.

자동 인덱싱이 해제된 상태에서도 특정 문서만 선택적으로 인덱스에 추가할 수 있습니다. 반대로, 자동 인덱싱을 설정된 상태로 두고 특정 문서만 선택적으로 제외할 수도 있습니다. 인덱싱 설정/해제 구성은 문서의 하위 집합만 쿼리해야 할 경우에 유용합니다.

자동 속성 값을 true 또는 false로 지정하여 기본 정책을 구성할 수 있습니다. 단일 문서에 대해 재정의하려면 문서를 삽입하거나 바꿀 때 x-ms-indexingdirective 요청 헤더를 설정하면 됩니다.

예를 들어 다음 샘플은 [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) 및 [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) 속성을 사용하여 문서를 명시적으로 포함하는 방법을 보여 줍니다.

	// If you want to override the default collection behavior to either     
	// exclude (or include) a Document from indexing,                                                                                           
	// use the RequestOptions.IndexingDirective property.                                  
	                                                                         
	client.CreateDocumentAsync(defaultCollection.SelfLink,  
	    new { Id = "AndersenFamily", isRegistered = true },                            
		new RequestOptions                               
		    {                                                                    
			    IndexingDirective = IndexingDirective.Include                                                                                      
			}
		);                                                                  


### 인덱싱 모드

동기(**일관성**) 및 비동기(**지연**) 인덱스 업데이트 간에 선택할 수 있습니다. 기본적으로 컬렉션의 문서를 삽입하거나 바꾸거나 삭제할 때마다 인덱스가 동기적으로 업데이트됩니다. 따라서 쿼리가 인덱스가 따라잡을 때까지의 지연 없이 문서 읽기의 일관성 수준과 동일한 일관성 수준을 적용할 수 있습니다.

DocumentDB는 쓰기 최적화되며 동기 인덱스 유지 관리와 함께 문서 쓰기의 지속적인 볼륨을 지원하지만 인덱스를 지연 업데이트하도록 특정 컬렉션을 구성할 수 있습니다. 지연 인덱스는 데이터가 갑자기 작성되는 시나리오에 적합하며, 오랜 시간 동안 콘텐츠를 인덱싱하는 데 필요한 작업을 분할하고자 합니다. 그러면 프로비전된 처리량을 효과적으로 사용하고 최대 사용 시간에 최소 대기 시간으로 쓰기 요청을 처리할 수 있습니다. 지연 인덱스를 설정한 경우 쿼리 결과는 데이터베이스 계정에 대해 구성된 일관성 수준과 관계없이 결국 일관성을 지니게 됩니다.

다음 샘플은 모든 문서 삽입에 일관된 자동 인덱싱을 사용하여 .NET SDK로 DocumentDB 컬렉션을 만드는 방법을 보여 줍니다.


	 // Default collection creates a hash index for all string and numeric    
	 // fields. Hash indexes are compact and offer efficient                                                                                           
	 // performance for equality queries.                                     
	                                                                          
	 var defaultCollection = new DocumentCollection { Id ="defaultCollection" };                                                   
	                                                                          
	 // Optional. Override Automatic to false for opt-in indexing of documents.                                                                
	                                                                          
	 defaultCollection.IndexingPolicy.Automatic = true;                       
	                                                                          
	 // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
	 // collections. Queries might return stale results with Lazy indexing.       
	                                                                          
	 defaultCollection.IndexingPolicy.IndexingMode = IndexingMode.Consistent; 
	                                                                          
	 defaultCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,defaultCollection);                                                      


### 인덱스 유형 및 자릿수

인덱스 항목에 사용된 유형 또는 체계는 인덱스 저장소 및 성능에 직접적인 영향을 줍니다. 큰 자릿수를 사용하는 체계의 경우 쿼리가 일반적으로 더 빠릅니다. 그러나 인덱스에 대한 더 높은 저장소 오버헤드도 있습니다. 작은 자릿수를 선택하면 쿼리 실행 중에 더 많은 문서를 처리해야 할 수도 있지만 저장소 오버헤드는 낮아집니다.

모든 경로에 있는 값에 대한 인덱스 자릿수는 3~7바이트일 수 있습니다.
동일한 경로가 서로 다른 문서에서 숫자 및 문자열 값을 가질 수 있으므로 이를 별도로 제어할 수 있습니다. .NET SDK에서 이 값은 [NumericPrecision](http://msdn.microsoft.com/library/microsoft.azure.documents.indexingpath.numericprecision.aspx) 및 [StringPrecision](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpath.stringprecision.aspx) 속성에 해당합니다.

지원되는 인덱스 유형은 해시와 범위 두 종류입니다. **해시** 인덱스 유형을 선택하면 같음 쿼리를 효율적으로 수행할 수 있습니다. 대부분의 사용 사례에서는 기본값 3바이트보다 큰 자릿수의 해시 인덱스가 불필요합니다.

**범위** 인덱스 유형을 선택하면 범위 쿼리(>, <, >=, <=, != 사용)를 수행할 수 있습니다. 값의 큰 범위가 포함된 경로의 경우 6바이트와 같은 큰 자릿수를 사용하는 것이 좋습니다. 큰 자릿수 범위 인덱스가 필요한 일반적인 사용 사례는 Epoch 시간으로 저장되는 타임스탬프입니다.

해당 사용 사례에 효율적인 범위 쿼리가 불필요한 경우 해시 인덱스의 기본값은 저장소와 성능 간의 균형을 가장 잘 조정합니다. 쿼리 범위를 지원하려면 사용자 지정 인덱스 정책을 지정해야 합니다.

> [AZURE.NOTE] 범위 인덱스는 숫자 값에 대해서만 지원됩니다.
  
다음 예제에서는 .NET SDK를 사용하여 컬렉션의 범위 인덱스 자릿수를 늘리는 방법을 보여 줍니다. 이 샘플에서는 특수 경로 "/"를 사용하며, 이와 관련된 내용은 다음 섹션에 설명되어 있습니다.


	 // If your collection has numeric fields that need to be queried    
	 // against ranges (>,>=,<=,<), then you can configure the collection to 
	 // use range queries for all numeric values.                                                                                                      
 
	var rangeDefault = new DocumentCollection { Id = "rangeCollection" };                                                              
	rangeDefault.IndexingPolicy.IncludedPaths.Add(                                                             
												 new IndexingPath {   
													IndexType = IndexType.Range, Path = "/", 
													NumericPrecision = 7 }
												 ); 
	 rangeDefault = await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


### 인덱스 경로

문서 내에서 인덱싱에 포함하거나 인덱싱에서 제외할 경로를 선택할 수 있습니다. 따라서 쿼리 패턴을 사전에 알고 있는 경우 시나리오의 쓰기 성능이 향상되고 인덱스 저장소를 줄일 수 있습니다.

인덱스 경로는 루트(/)로 시작하며, 일반적으로 ? 와일드카드 연산자로 끝나 접두사에 대해 가능한 값이 여러 개 있음을 나타냅니다. 예를 들어 SELECT * FROM Families F WHERE F.familyName = "Andersen"을 처리하려면 컬렉션의 인덱스 정책에 /"familyName"/?의 인덱스 경로를 포함해야 합니다.

또한 인덱스 경로에 * 와일드카드 연산자를 사용하여 접두사 아래에 재귀적으로 경로에 대한 동작을 지정할 수 있습니다. 예를 들어 /"payload"/*를 사용하여 payload 속성 아래의 모든 항목을 인덱싱에서 제외할 수 있습니다.

인덱스 경로를 지정하는 일반적인 패턴은 다음과 같습니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>경로</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>설명/사용 사례</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /
                </p>
            </td>
            <td valign="top">
                <p>
                    컬렉션의 기본 경로입니다. 재귀적이며 전체 문서 트리에 적용됩니다.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    인덱스 경로는 다음과 같은 쿼리를 처리하는 데 필요합니다(각각 해시, 범위 유형이 포함되어 있음).
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop &gt; 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/*
                </p>
            </td>
            <td valign="top">
                <p>
                    지정된 레이블 아래의 모든 경로의 인덱스 경로입니다. 인덱싱에서 제외할 경우에만 지정됩니다.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/
                </p>
            </td>
            <td valign="top">
                <p>
                    지정된 경로가 없는 문서를 정리하기 위해 쿼리를 실행하는 중에 사용되는 인덱스 경로입니다.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    인덱스 경로는 다음과 같은 쿼리를 처리하는 데 필요합니다(각각 해시 또는 범위 유형이 포함되어 있음).
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop &gt; 5
                </p>
            </td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] 사용자 지정 인덱스 경로를 설정하는 동안 특수 경로 "/"로 지정된 전체 문서 트리에 대한 기본 인덱싱 규칙을 지정해야 합니다.

다음 예제에서는 범위 인덱싱을 사용하고 사용자 지정 자릿수 값이 바이트인 특정 경로를 구성합니다.


 	// If you only want to specify range queries against a specific field,   
 	// then use a range index against that field. Doing so reduces the   
	// amount of storage required for indexes for the collection. In this    
 	// case, the query creates an index against the JSON path                   
 	// /"CreatedTimestamp"/?    
 	// allowing queries of the form WHERE CreatedTimestamp [>] X            
	
	var pathRange = new DocumentCollection { Id = "rangeSinglePathCollection" };    
	
	pathRange.IndexingPolicy.IncludedPaths.Add(
								new IndexingPath { 
										IndexType = IndexType.Range, 
										Path = "/"CreatedTimestamp"/?",   
										NumericPrecision = 7   
							 			}
									);   
	
	pathRange.IndexingPolicy.IncludedPaths.Add(   
											 new IndexingPath {  
											  	 Path = "/"  
											 });                                                                      
                                                   
	 pathRange = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);      


쿼리에 범위 연산자가 사용되지만 쿼리한 경로에 대한 범위 인덱스가 없고 인덱스에서 처리될 수 있는 다른 필터가 없으면 DocumentDB가 오류를 반환합니다. 그러나 REST API의 x-ms-documentdb-allow-scans 헤더를 사용하거나, .NET SDK의 AllowScanInQueryrequest 옵션을 사용하여 범위 인덱스 없이 이러한 쿼리를 계속 수행할 수 있습니다.

다음 예제에서는 "*" 와일드카드를 사용하여 경로의 하위 트리를 인덱싱에서
제외합니다.

	var excluded = new DocumentCollection { Id = "excludedPathCollection" };                                                                       
  	excluded.IndexingPolicy.IncludedPaths.Add(
	newIndexingPath {  Path = "/" });  

	excluded.IndexingPolicy.ExcludedPaths.Add("/" nonIndexedContent"/*");    
	excluded = await client.CreateDocumentCollectionAsync(database.SelfLink,excluded);                                                               


성능 튜닝
------------------

여러 인덱싱 정책 구성을 평가할 때 DocumentDB API를 통해 정책의 저장소 및 처리량을 측정해야 합니다.

컬렉션의 저장소 할당량 및 사용량을 확인하려면 컬렉션 리소스에 대해 HEAD 또는 GET 요청을 실행하고 x-ms-request-quota 및 x-ms-request-usage 헤더를 검사합니다. .NET SDK에서는 [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx)의 [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) 및 [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) 속성에 이에 해당하는 값이 포함되어 있습니다.


 	// Measure the document size usage (which includes the index size) against   
 	// different policies.        
	 ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
	 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentSizeQuota, collectionInfo.DocumentSizeUsage);                                       


각 쓰기 작업(만들기, 업데이트 또는 삭제)에 대한 인덱싱의 오버헤드를 측정하려면 x-ms-request-charge 헤더(또는 .NET SDK의 [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx)에 있는 [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) 속성)를 검사하여 이러한 작업에 사용된 요청 단위 수를 측정합니다.


 	// Measure the performance (request units) of writes.     
 	ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
                                                                    
 	Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);                                                 
                                                                          
 	// Measure the performance (request units) of queries.    
	 IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                          
                                                                          
 	while (queryable.HasMoreResults)                                                                            
 	{                                                                         
 	   FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
	   Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
	}                                                                        




<!--HONumber=49-->