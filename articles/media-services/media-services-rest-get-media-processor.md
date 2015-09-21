<properties 
	pageTitle="미디어 프로세서를 만드는 방법 | Microsoft Azure" 
	description="Azure 미디어 서비스용 미디어 콘텐츠를 인코딩하거나 형식을 변환하거나 암호화하거나 암호 해독하기 위한 미디어 프로세서 구성 요소를 만드는 방법에 대해 알아봅니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>


#방법: 미디어 프로세서 인스턴스 가져오기


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)

##개요

미디어 서비스에서 미디어 프로세서는 미디어 콘텐츠 인코딩, 형식 변환, 암호화 또는 암호 해독과 같은 특정 처리 작업을 다루는 구성 요소입니다. 일반적으로 미디어 콘텐츠 인코드, 암호화 또는 형식 변환 작업을 만들 때 미디어 프로세서를 만듭니다.

다음 표에서는 각 사용 가능한 미디어 프로세서의 이름과 설명을 제공합니다.

미디어 프로세서 이름|설명|추가 정보
---|---|---
Azure 미디어 인코더|Azure 미디어 인코더를 사용하여 인코딩 작업을 실행할 수 있습니다.|[Azure 미디어 인코더](media-services-encode-asset.md#azure_media_encoder)
미디어 인코더 표준|미디어 인코더 표준을 사용하여 인코딩 작업을 실행할 수 있습니다.|[Azure 미디어 인코더](media-services-encode-asset.md#media_encoder_standard)
미디어 인코더 Premium 워크플로|미디어 인코더 Premium 워크플로를 사용하여 인코딩 작업을 실행할 수 있습니다.|[미디어 인코더 Premium 워크플로](media-services-encode-asset.md#media_encoder_premium_wokrflow)
Azure 미디어 인덱서| 미디어 파일과 콘텐츠를 검색 가능하도록 설정할 수 있으며 선택 캡션 트랙과 키워드를 생성할 수 있습니다.|[Azure 미디어 인덱서를 사용하여 미디어 파일 인덱싱](media-services-index-content.md).
Azure 미디어 Hyperlapse(미리 보기)|비디오 안정화를 통해 비디오에서 "범프"를 부드럽게 할 수 있습니다. 사용 가능한 클립으로 만들어 콘텐츠 속도를 높일 수도 있습니다.|		[Azure 미디어 Hyperlapse](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)</a>
저장소 암호 해독| 저장소 암호 해독을 사용하여 암호화된 미디어 자산의 암호를 해독할 수 있습니다.|해당 없음
Microsoft Azure Media Packager|미디어 자산을 .mp4에서 부드러운 스트리밍 형식으로 변환할 수 있습니다. 또한 미디어 자산을 부드러운 스트리밍에서 Apple HLS(HTTP 라이브 스트리밍) 형식으로 변환할 수 있습니다.|[Azure Media Packager용 작업 기본 설정 문자열](http://msdn.microsoft.com/library/hh973635.aspx)
Windows Azure Media Encryptor|PlayReady Protection을 사용하여 미디어 자산을 암호화할 수 있습니다.|[Azure Media Packager용 작업 기본 설정 문자열](http://msdn.microsoft.com/library/hh973610.aspx)

##미디어 프로세서 가져오기

>[AZURE.NOTE]미디어 서비스 REST API를 사용할 때는 다음 사항을 고려해야 합니다.
>
>미디어 서비스에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [미디어 서비스 REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.

>https://media.windows.net에 연결하면 다른 미디어 서비스 URI를 지정하는 301 리디렉션을 받게 됩니다. [REST API를 사용하여 미디어 서비스에 연결](media-services-rest-connect_programmatically.md)에서 설명한 대로 새 URI에 대한 후속 호출을 만들어야 합니다.



다음 REST 호출에서는 이름으로 미디어 프로세서 인스턴스를 가져오는 방법을 보여줍니다(이 경우 **Azure 미디어 인코더**).

	
요청:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-477b-2233-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
응답:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
	request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 11 Feb 2015 00:19:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}



##미디어 서비스 학습 경로

여기서 AMS 학습 경로를 볼 수 있습니다.

- [AMS 라이브 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS 주문형 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##다음 단계
미디어 프로세서 인스턴스를 가져오는 방법을 알아보았으므로 이제 Azure Media Encoder를 사용하여 자산을 인코드하는 방법을 보여 주는 [자산을 인코드하는 방법][](영문) 항목으로 이동합니다.

[자산을 인코드하는 방법]: media-services-rest-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[How to: Connect to Media Services Programmatically]: ../media-services-rest-connect_programmatically/

<!---HONumber=Sept15_HO2-->