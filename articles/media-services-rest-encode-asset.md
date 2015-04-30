<properties 
	pageTitle="Azure 미디어 인코더를 사용하여 자산을 인코딩하는 방법" 
	description="Azure 미디어 인코더를 사용하여 미디어 서비스에서 미디어 콘텐츠를 인코딩하는 방법에 대해 알아봅니다. REST API를 사용하는 코드 샘플입니다." 
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
	ms.date="02/10/2015" 
	ms.author="juliako"/>


# Azure 미디어 인코더를 사용하여 자산을 인코딩하는 방법

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 시리즈의 일부입니다. 

## 개요
인터넷을 통해 디지털 비디오를 배달하려면 미디어를 압축해야 합니다. 디지털 비디오 파일은 크기가 상당히 크기 때문에 인터넷을 통해 전달하거나 고객의 장치에서 제대로 표시하지 못할 수 있습니다. 인코딩은 고객이 미디어를 볼 수 있도록 비디오 및 오디오를 압축하는 과정입니다.

인코딩 작업은 미디어 서비스에서 가장 일반적인 처리 작업 중 하나입니다. 인코딩 작업을 만들어 한 인코딩에서 다른 인코딩으로 미디어 파일을 변환합니다. 인코딩할 때는 미디어 서비스 기본 제공 미디어 인코더를 사용할 수 있습니다. 또한 미디어 서비스 파트너가 제공하는 인코더를 사용할 수도 있습니다. 타사 인코더는 Azure 마켓플레이스를 통해 사용할 수 있습니다. 인코더에 정의된 미리 설정 문자열을 사용하여 또는 미리 설정 구성 파일을 사용하여 인코딩 작업의 세부 정보를 지정할 수 있습니다. 사용할 수 있는 미리 설정 유형을 보려면 Azure Media Services에 대한 작업 미리 설정을 참조하세요. 타사 인코더를 사용하는 경우 [파일 유효성을 검사](https://msdn.microsoft.com/library/azure/dn750842.aspx)해야 합니다.

항상 mezzanine 파일을 적응 비트 전송률 MP4 집합으로 인코딩한 다음 [동적 패키징](https://msdn.microsoft.com/library/azure/jj889436.aspx)을 사용하여 원하는 형식으로 집합을 변환하는 것이 좋습니다.

출력 자산이 암호화된 저장소인 경우 자산 배달 정책을 구성해야 합니다. 자세한 내용은 [자산 배달 정책 구성](media-services-rest-configure-asset-delivery-policy.md)을 참조하세요.

## 작업을 단일 인코딩 작업으로 만들기 

>[AZURE.NOTE] 미디어 서비스 REST API를 사용할 때는 다음 사항을 고려해야 합니다.
>
>미디어 서비스에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [미디어 서비스 REST API 개발을 위한 설치](media-services-rest-how-to-use.md)를 참조하세요.

>https://media.windows.net에 연결하면 다른 미디어 서비스 URI를 지정하는 301 리디렉션을 받게 됩니다. [REST API를 사용하여 미디어 서비스에 연결](media-services-rest-connect_programmatically.md)에서 설명한 대로 새 URI에 대한 후속 호출을 만들어야 합니다. 


다음 예제에서는 특정 해상도와 품질로 비디오를 인코딩하기 위해 하나의 작업 집합으로 작업을 만들어 게시하는 방법을 보여 줍니다. Azure 미디어 인코더로 인코딩할 때 [여기](https://msdn.microsoft.com/library/azure/dn619389.aspx)에 지정된 작업 구성 기본 설정을 사용할 수 있습니다.
	
요청:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d
	Host: media.windows.net
	Content-Length: 476
	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

응답:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1017
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: d2052a71-95b1-4a52-9420-ccca1202a5fb
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Fri, 11 May 2012 21:32:40 GMT
	
	{"d":{"__metadata":{"id":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"},"Tasks":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/Tasks"}},"OutputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/OutputMediaAssets"}},"InputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/InputMediaAssets"}},"Id":"nb:jid:UUID:40dc7bef-6bd9-2247-9f3d-d80bc257d715","Name":"NewTestJob","Created":"\/Date(1336771959431)\/","LastModified":"\/Date(1336771959431)\/","EndTime":null,"Priority":0,"RunningDuration":0,"StartTime":null,"State":0,"TemplateId":null}}

## 연결된 작업으로 작업 만들기

대부분의 응용 프로그램 시나리오에서는 개발자가 일련의 처리 작업을 만들려고 합니다. 미디어 서비스에서 일련의 연결된 작업을 만들 수 있습니다. 각 작업은 서로 다른 처리 단계를 수행하고 다양한 미디어 프로세서를 사용할 수 있습니다. 연결된 작업은 한 작업에서 다른 작업으로 자산을 전달하여 자산에 대한 선형 시퀀스 작업을 수행할 수 있습니다. 그러나 작업에서 수행된 작업은 시퀀스에 있을 필요가 없습니다. 연결된 작업을 만들면 연결된 **ITask** 개체는 단일 **IJob** 개체에 만들어집니다.

>[AZURE.NOTE] 현재 작업은 작업당 30개로 제한됩니다. 30개 이상의 작업을 연결해야 하는 경우 둘 이상의 작업을 만들어 작업을 연결합니다.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-4e75-2233-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


## 다음 단계
자산을 인코드하는 작업을 만드는 방법을 알아보았습니다. 이제 [미디어 서비스 작업 진행 상태를 확인하는 방법](media-services-rest-check-job-progress.md) 항목으로 이동하세요.

[Azure 마켓플레이스]: https://datamarket.azure.com/
[인코더 기본 설정]: http://msdn.microsoft.com/library/dn619392.aspx
[방법: 미디어 프로세서 인스턴스 가져오기]:http://go.microsoft.com/fwlink/?LinkId=301732
[방법: 암호화된 자산 업로드]::http://go.microsoft.com/fwlink/?LinkId=301733
[방법: 다운로드를 통해 자산 제공]:http://go.microsoft.com/fwlink/?LinkId=301734
[작업 진행 상태를 확인하는 방법]:http://go.microsoft.com/fwlink/?LinkId=301737
[Azure Media Packager의 작업 미리 설정]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx

<!--HONumber=52-->