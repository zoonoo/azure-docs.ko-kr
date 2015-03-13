<properties 
	pageTitle="기계 학습 권장 사항 API의 빠른 시작 가이드 | Azure" 
	description="Azure 기계 학습 권장 사항 - 빠른 시작 가이드" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="jaymathe"/>

# 기계 학습 권장 사항 API에 대한 빠른 시작 가이드

이 문서에서는 Azure 기계 학습 권장 사항을 사용하도록 서비스나 응용 프로그램을 등록하는 방법에 대해 설명합니다. 

##목차

* 일반 개요	
* 제한 사항	        
* 통합	        
  * 인증	
  *	서비스 URI	    
  *	API 버전	    
  *	모델 만들기	
  *	카탈로그 데이터 가져오기	
  *	사용 데이터 가져오기	
     * 파일 업로드	
	 * 데이터 취득 사용	
* 권장 사항 모델 작성	
* 빌드 상태 가져오기	
* 권장 사항 가져오기	
* 모델 업데이트	
* 법적 정보	

 
##일반 개요

Azure 기계 학습 권장 사항을 사용하려면 다음 단계를 수행해야 합니다.

* 모델 만들기 - 모델은 사용 데이터, 카탈로그 데이터 및 권장 사항 모델의 컨테이너입니다.
* 카탈로그 데이터 가져오기 - 선택적 단계입니다. 카탈로그에는 항목에 대한 메타데이터 정보가 포함됩니다. 카탈로그 데이터를 업로드하지 않으면 권장 사항 서비스는 사용 데이터에서 묵시적으로 카탈로그에 대해 학습합니다.
* 사용 데이터 가져오기 - 다음 두 가지 방법의 하나로(또는 둘 다) 사용 데이터를 업로드할 수 있습니다.
	* 사용 데이터를 포함하는 파일을 업로드합니다.
	* 데이터 취득 이벤트를 전송합니다.
	일반적으로 초기 권장 사항 모델(부트스트랩)을 만들고 시스템에서 데이터 취득 형식을 사용하여 충분한 데이터를 수집할 때까지 초기 모델을 사용할 수 있도록 사용 파일을 업로드합니다.
* 권장 사항 모델 작성 - 이 단계는 권장 사항 시스템이 모든 사용 데이터를 이용하여 권장 사항 모델을 만드는 비동기 작업입니다. 이 작업은 데이터 크기 및 빌드 구성 매개 변수에 따라 몇 분이나 몇 시간이 걸릴 수 있습니다. 빌드를 트리거할 때 빌드 ID를 얻고 이를 사용하여 권장 사항 소비를 시작하기 전에 빌드 프로세스가 종료된 시간을 확인합니다. 
* 권장 사항 소비 - 특정 항목 또는 항목 목록에 대한 권장 사항을 가져옵니다.

위의 모든 단계는 Azure 기계 학습 권장 사항 API를 통해 수행됩니다.

##제한 사항

* 구독당 최대 모델 수: 10
* 카탈로그에 포함할 수 있는 최대 항목 수: 100,000
* 유지되는 최대 사용 지점 수는 최대 5,000,000개입니다. 새 지점이 업로드되거나 보고될 경우 가장 오래된 지점이 삭제됩니다.
* POST로 전송할 수 있는 최대 데이터 크기(예: 카탈로그 데이터 가져오기, 사용 데이터 가져오기)는 200MB입니다.
* 활성화되지 않은 권장 사항 모델에 대한 초당 트랜잭션 수는 최대 2TPS이고, 활성화된 권장 사항 모델 빌드에만 최대 20TPS를 포함할 수 있습니다.

##통합

###인증
인증과 관련된 Microsoft Azure 마켓플레이스 지침을 따르세요. 마켓플레이스에서는 기본 또는 OAuth 인증 방법을 지원합니다.

###서비스 URI 
각 Azure 기계 학습 권장 사항 API에 대한 서비스 루트 URI는 [여기](https://api.datamarket.azure.com/amla/recommendations/v1/)에 있습니다.

전체 서비스 URI는 OData 사양의 요소를 사용하여 표현됩니다.  

###API 버전
각 API 호출은 1.0으로 설정해야 하는 apiVersion이라는 쿼리 매개 변수 종료 시 포함됩니다.

###모델 만들기
"모델 만들기" 요청 만들기:

![Table1][1]

응답:

HTTP 상태 코드: 200

OData XML
	
	feed/entry/content/properties/id - contains the model id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'</id>
	  <rights type="text" />	
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
	  </entry>
	</feed>


###카탈로그 데이터 가져오기

여러 번 호출하여 여러 카탈로그 파일을 같은 모델에 업로드하면 새 카탈로그 항목만 삽입됩니다. 기존 항목은 원래 값으로 유지됩니다.

![Table2][2]

응답:

HTTP 상태 코드: 200

OData XML	

	Feed\entry\content\properties\LineCount - number of lines accepted
	Feed\entry\content\properties\ErrorCount - number of lines that were not inserted due to an error

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
  		<subtitle type="text">Import catalog file</subtitle>
  		<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T06:58:04Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
 	 </entry>
	</feed>


###사용 데이터 가져오기

####파일 업로드
이 섹션에서는 파일을 사용하여 사용 데이터를 업로드하는 방법을 보여 줍니다. 사용 데이터를 사용하여 이 API를 여러 번 호출할 수 있습니다. 모든 호출에 대한 모든 사용 데이터가 저장됩니다.

![Table3a][3]
![Table3b][4]

응답:

HTTP 상태 코드: 200

OData XML	

	Feed\entry\content\properties\LineCount - number of lines accepted
	Feed\entry\content\properties\ErrorCount - number of lines that were not inserted due to an error
	Feed\entry\content\properties\FileId - the file identifier


	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####데이터 취득 사용
이 섹션에서는 일반적으로 웹 사이트에서 Azure 기계 학습 권장 사항에 실시간으로 이벤트를 전송하는 방법을 보여 줍니다.

![Table4][5]

요청 본문

	전송할 각 이벤트에 대한 이벤트 데이터 항목입니다. SessionId 필드에서 같은 사용자 또는 브라우저 세션에 대해 같은 ID를 전송해야 합니다.


	Example of Click:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>Click</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event> 


	Example of Recommendation Click:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>RecommendationClick</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>


	Example of Adding to shop cart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>AddShopCart</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>

	Example of Removing from shop cart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>RemoveShopCart</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>

	Example of Purchase:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">   
	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>   
	<SessionId>11112222</SessionId>   
	<EventData>     
	<EventData>       
		<Name>Purchase</Name> 
		<PurchaseItems>
			<PurchaseItems>
				<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>       
				<Count>3</Count>
			</PurchaseItems>
		</PurchaseItems>
	</EventData>     
	</EventData> 
	</Event>

	Example of sending 2 events "Click" and "AddShopCart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>Click</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
      <ItemName>itemName</ItemName>
      <ItemDescription>item description</ItemDescription>
      <ItemCategory>category</ItemCategory>
    </EventData>
    <EventData>
      <Name>AddShopCart</Name>
      <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
    </EventData>
  	</EventData>
	</Event>

응답:
HTTP 상태 코드: 200

###권장 사항 모델 작성

![Table5][6]

응답:

HTTP 상태 코드: 200

	OData XML	
	This is an asynchronous API. You will get a Build Id as a response. To know when the build has ended, you should call the "Get Builds Status of a Model" API and locate this build Id in the response. Note that a build can take from minutes to hours depending on the size of the data.
	You cannot consume recommendations till the build ends.

	Valid build status:

	* Create - model was created
	* Queued - model build was triggered and it is queued
	* Building - the model is being build
	* Success - the build ended successfully
	* Error - the build ended with a failure
	* Cancelled - build was canceled
	* Cancelling - build is being cancelled

	Feed\entry\content\properties\Id - contains the build id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First build'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First%20build'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First build'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First%20build'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>

###모델의 빌드 상태 가져오기
![Table6][7]

응답:

HTTP 상태 코드: 200

	OData XML	Valid build status:
	* Create - model was created
	* Queued - model build was triggered and it is queued
	* Building - the model is being build
	* Success - the build ended successfully
	* Error - the build ended with a failure
	* Cancelled - build was canceled
	* Cancelling - build is being cancelled

	Feed\entry\content\properties\Status -contains the build status

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get builds status of a model</subtitle>
	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T17:51:10Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'" />
	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###권장 사항 가져오기
![Table7][8]

HTTP 상태 코드: 200

	OData XML	The response includes one entry per recommended item, each entry has the following data:
	* Feed\entry\content\properties\Id - The recommended item id
	* Feed\entry\content\properties\Name - The name of the item 
	* Feed\entry\content\properties\Rating - The rating of the recommendation, higher number means higher confidence
	* Feed\entry\content\properties\Reasoning - the recommendation reasoning
	The example response below includes 10 recommended items:

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=1&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=1&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=2&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=2&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=3&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=3&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=4&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=4&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=5&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=5&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=6&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=6&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=7&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=7&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=8&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=8&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=9&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=9&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###모델 업데이트
모델 설명 또는 활성 빌드 ID를 업데이트할 수 있습니다.
활성 빌드 ID - 모든 모델에 대한 모든 빌드에는 "빌드 ID"가 있습니다. 활성 "빌드 ID"는 모든 새 모델 중 처음 성공한 빌드입니다. 활성 빌드 ID가 있는데 같은 모델에 대한 추가 빌드를 수행하려면 이 활성 빌드 ID를 기본 빌드 ID로 명시적으로 설정해야 합니다. 권장 사항을 소비할 때 사용할 빌드 ID를 지정하지 않으면 자동으로 기본 빌드 ID가 사용됩니다.

이 메커니즘을 사용하면 프로덕션에 권장 사항 모델을 포함하고 나서 새 모델을 빌드하고 프로덕션으로 수준을 올리기 전에 테스트할 수 있습니다.

![Table8][9]

응답:

HTTP 상태 코드: 200

	OData XML
	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Update an Existing Model</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&apiVersion='1.0'</id>
  	<rights type="text" />
 	 <updated>2014-10-05T10:27:17Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&apiVersion='1.0'" />
	</feed>

##법적 정보
이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 뷰는 통지 없이 변경될 수 있습니다. 
여기에서 설명하는 일부 예는 설명 목적으로만 제공되는 가상의 예이며, 어떠한 실제 사례와도 연관시킬 의도가 없으며 그렇게 유추해서도 안 됩니다. 
이 문서는 Microsoft 제품의 지적 소유권에 대한 법적 권한을 사용자에게 제공하지 않습니다. 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다. 
(c) 2014 Microsoft. All rights reserved. 


[1]: ./media/machine-learning-recommendation-api-quick-start-guide/Table01.png
[2]: ./media/machine-learning-recommendation-api-quick-start-guide/Table02.png
[3]: ./media/machine-learning-recommendation-api-quick-start-guide/Table03a.png
[4]: ./media/machine-learning-recommendation-api-quick-start-guide/Table03b.png
[5]: ./media/machine-learning-recommendation-api-quick-start-guide/Table04.png
[6]: ./media/machine-learning-recommendation-api-quick-start-guide/Table05.png
[7]: ./media/machine-learning-recommendation-api-quick-start-guide/Table06.png
[8]: ./media/machine-learning-recommendation-api-quick-start-guide/Table07.png
[9]: ./media/machine-learning-recommendation-api-quick-start-guide/Table08.png


<!--HONumber=46--> 
