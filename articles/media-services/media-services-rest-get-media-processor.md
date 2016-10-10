<properties 
	pageTitle="미디어 프로세서를 만드는 방법 | Microsoft Azure" 
	description="Azure 미디어 서비스용 미디어 콘텐츠를 인코딩하거나 형식을 변환하거나 암호화하거나 암호 해독하기 위한 미디어 프로세서 구성 요소를 만드는 방법에 대해 알아봅니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/26/2016" 
	ms.author="juliako"/>


#방법: 미디어 프로세서 인스턴스 가져오기


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST (영문)](media-services-rest-get-media-processor.md)

##개요

미디어 서비스에서 미디어 프로세서는 미디어 콘텐츠 인코딩, 형식 변환, 암호화 또는 암호 해독과 같은 특정 처리 작업을 다루는 구성 요소입니다. 일반적으로 미디어 콘텐츠 인코드, 암호화 또는 형식 변환 작업을 만들 때 미디어 프로세서를 만듭니다.

다음 표에서는 각 사용 가능한 미디어 프로세서의 이름과 설명을 제공합니다.

미디어 프로세서 이름|설명|추가 정보
---|---|---
미디어 인코더 표준|주문형 인코딩에 대한 표준 기능을 제공합니다. |[Azure 주문형 미디어 인코더 개요 및 비교](media-services-encode-asset.md)
미디어 인코더 Premium 워크플로|미디어 인코더 Premium 워크플로를 사용하여 인코딩 작업을 실행할 수 있습니다.|[Azure 주문형 미디어 인코더 개요 및 비교](media-services-encode-asset.md)
Azure 미디어 인덱서| 미디어 파일과 콘텐츠를 검색 가능하도록 설정할 수 있으며 선택 캡션 트랙과 키워드를 생성할 수 있습니다.|[Azure 미디어 인덱서](media-services-index-content.md)
Azure 미디어 Hyperlapse(미리 보기)|비디오 안정화를 통해 비디오에서 "범프"를 부드럽게 할 수 있습니다. 사용 가능한 클립으로 만들어 콘텐츠 속도를 높일 수도 있습니다.|[Azure 미디어 Hyperlapse](media-services-hyperlapse-content.md)
Azure 미디어 인코더|사용 중단됨
저장소 암호 해독| 사용 중단됨|
Azure Media Packager|사용 중단됨|
Azure Media Encryptor|사용 중단됨|

##미디어 프로세서 가져오기

>[AZURE.NOTE] 미디어 서비스 REST API를 사용할 때는 다음 사항을 고려해야 합니다.
>
>미디어 서비스에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [미디어 서비스 REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.

>https://media.windows.net에 연결하면 다른 미디어 서비스 URI를 지정하는 301 리디렉션을 받게 됩니다. [REST API를 사용하여 미디어 서비스에 연결](media-services-rest-connect-programmatically.md)에서 설명한 대로 새 URI에 대한 후속 호출을 만들어야 합니다.


다음 REST 호출에서는 이름으로 미디어 프로세서 인스턴스를 가져오는 방법을 보여 줍니다(이 경우 **미디어 인코더 표준**).



	
요청:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer <token>
	x-ms-version: 2.11
	Host: media.windows.net
	
응답:
		
	. . .
	
	{  
	   "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
	   "value":[  
	      {  
	         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "Description":"Media Encoder Standard",
	         "Name":"Media Encoder Standard",
	         "Sku":"",
	         "Vendor":"Microsoft",
	         "Version":"1.1"
	      }
	   ]
	}


##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##다음 단계

미디어 프로세서 인스턴스를 가져오는 방법을 알아보았으므로 이제 Media Encoder Standard를 사용하여 자산을 인코딩하는 방법을 보여 주는 [자산을 인코딩하는 방법](media-services-rest-get-started.md) 토픽으로 이동합니다.

<!---HONumber=AcomDC_0928_2016-->