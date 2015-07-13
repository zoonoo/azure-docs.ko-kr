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
	ms.date="06/18/2015" 
	ms.author="juliako"/>


#Azure 미디어 인코더를 사용하여 자산을 인코딩하는 방법

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 시리즈의 일부입니다.

##개요
인터넷을 통해 디지털 비디오를 배달하려면 미디어를 압축해야 합니다. 디지털 비디오 파일은 크기가 상당히 크기 때문에 인터넷을 통해 전달하거나 고객의 장치에서 제대로 표시하지 못할 수 있습니다. 인코딩은 고객이 미디어를 볼 수 있도록 비디오 및 오디오를 압축하는 과정입니다.

인코딩 작업은 미디어 서비스에서 가장 일반적인 처리 작업 중 하나입니다. 인코딩 작업을 만들어 한 인코딩에서 다른 인코딩으로 미디어 파일을 변환합니다. 인코딩할 때는 미디어 서비스 기본 제공 미디어 인코더를 사용할 수 있습니다. 또한 미디어 서비스 파트너가 제공하는 인코더를 사용할 수도 있습니다. 타사 인코더는 Azure 마켓플레이스를 통해 사용할 수 있습니다. 인코더에 정의된 미리 설정 문자열을 사용하여 또는 미리 설정 구성 파일을 사용하여 인코딩 작업의 세부 정보를 지정할 수 있습니다. 사용할 수 있는 미리 설정 유형을 보려면 [Azure Media Services에 대한 작업 미리 설정](https://msdn.microsoft.com/library/azure/dn619392.aspx)을 참조하세요. 타사 인코더를 사용하는 경우 [파일 유효성을 검사](https://msdn.microsoft.com/library/azure/dn750842.aspx)해야 합니다.

각 작업을 수행하려는 처리 유형에 따라 하나 이상의 작업을 가질 수 있습니다. REST API를 통해 두 방법 중 하나로 작업 및 관련된 작업을 만들 수 있습니다. 작업은 작업 엔터티에 대한 작업 탐색 속성 또는 OData 배치를 통해 인라인으로 정의될 수 있습니다. 미디어 서비스 SDK 는 일괄 처리를 사용하지만 이 항목에 있는 코드 예제 가독성의 경우 작업은 인라인으로 정의됩니다. 배치에 대한 정보는 [Open Data Protocol(OData) 배치](http://www.odata.org/documentation/odata-version-3-0/batch-processing/)를 참조하세요. [작업](https://msdn.microsoft.com/library/azure/hh974289.aspx) 항목에서 배치의 예를 찾을 수도 있습니다.

항상 mezzanine 파일을 적응 비트 전송률 MP4 집합으로 인코딩한 다음 [동적 패키징](https://msdn.microsoft.com/library/azure/jj889436.aspx)을 사용하여 원하는 형식으로 집합을 변환하는 것이 좋습니다. 동적 패키징을 이용하려면 먼저 콘텐츠를 배달할 계획인 스트리밍 끝점에 대한 주문형 스트리밍 단위를 하나 이상 가져와야 합니다. 자세한 내용은 [미디어 서비스 크기를 조정하는 방법](media-services-manage-origins.md#scale_streaming_endpoints)을 참조하세요.

출력 자산이 암호화된 저장소인 경우 자산 배달 정책을 구성해야 합니다. 자세한 내용은 [자산 배달 정책 구성](media-services-rest-configure-asset-delivery-policy.md)을 참조하세요.



##작업을 단일 인코딩 작업으로 만들기 

>[AZURE.NOTE]미디어 서비스 REST API를 사용할 때는 다음 사항을 고려해야 합니다.
>
>미디어 서비스에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [미디어 서비스 REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.

>https://media.windows.net에 연결하면 다른 미디어 서비스 URI를 지정하는 301 리디렉션을 받게 됩니다. [REST API를 사용하여 미디어 서비스에 연결](media-services-rest-connect_programmatically.md)에서 설명한 대로 새 URI에 대한 후속 호출을 만들어야 합니다.


다음 예제에서는 특정 해상도와 품질로 비디오를 인코딩하기 위해 하나의 작업 집합으로 작업을 만들어 게시하는 방법을 보여 줍니다. Azure 미디어 인코더로 인코딩할 때 [여기](https://msdn.microsoft.com/library/azure/dn619389.aspx)에 지정된 작업 구성 기본 설정을 사용할 수 있습니다.
	
요청:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

응답:
	
	HTTP/1.1 201 Created

	. . . 

##고려 사항

- TaskBody 속성은 리터럴 XML을 사용하여 작업에서 사용될 입력이나 출력 수를 정의해야 합니다. 작업 항목은 해당 XML에 대한 XML 스키마 정의를 포함합니다.
- TaskBody 정의에서 <inputAsset> 및 <outputAsset>에 대한 각각의 내부 값은 JobInputAsset(value) 또는 JobOutputAsset(value)으로 설정되어야 합니다.
- 작업 출력 자산은 여러 개일 수 있습니다. 작업에서 하나의 JobOutputAsset(x)을 작업 출력으로 한 번만 사용할 수 있습니다.
- JobInputAsset 또는 JobOutputAsset을 작업의 입력 자산으로 지정할 수 있습니다.
- 작업은 주기를 형성해서는 안됩니다.
- JobInputAsset 또는 JobOutputAsset에 전달하는 값 매개변수는 자산에 대한 인덱스 값을 나타냅니다. 실제 자산은 작업 엔터티 정의에 있는 InputMediaAssets 및 OutputMediaAssets 탐색 속성에 정의됩니다. 
- 미디어 서비스는 OData v 3를 기반으로 하기 때문에 InputMediaAssets 및 OutputMediaAssets 탐색 속성 컬렉션에 있는 개별 자산은 "__metadata : uri" 이름 값 쌍으로 참조됩니다.
- InputMediaAsset은 미디어 서비스에서 만든 하나 이상의 자산에 매핑됩니다. OutputMediaAsset은 시스템에 의해 생성됩니다. 기존 자산을 참조하지 않습니다.
- OutputMediaAsset은 assetName 특성을 사용하여 명명할 수 있습니다. 이 특성이 없을 경우 OutputMediaAsset의 이름은 <outputAsset> 요소의 내부 텍스트 값이 작업 이름 값 또는 작업 Id 값(이름 속성이 정의되어 있지 않은 경우)의 접미사를 갖는 어떤 것이든 가능합니다. 예를 들어, assetName에 대한 값을 "Sample"로 설정하는 경우 OutputMediaAsset 이름 속성은 "Sample"로 설정됩니다. 하지만 assetName에 대한 값은 설정하지 않았지만 작업 이름을 "NewJob"으로 설정한 경우 OutputMediaAsset 이름은 "JobOutputAsset(값)_NewJob"이 됩니다. 

다음 예제에서는 assetName 특성을 설정하는 방법을 보여줍니다.

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}



##연결된 작업으로 작업 만들기

대부분의 응용 프로그램 시나리오에서는 개발자가 일련의 처리 작업을 만들려고 합니다. 미디어 서비스에서 일련의 연결된 작업을 만들 수 있습니다. 각 작업은 서로 다른 처리 단계를 수행하고 다양한 미디어 프로세서를 사용할 수 있습니다. 연결된 작업은 한 작업에서 다른 작업으로 자산을 전달하여 자산에 대한 선형 시퀀스 작업을 수행할 수 있습니다. 그러나 작업에서 수행된 작업은 시퀀스에 있을 필요가 없습니다. 연결된 작업을 만들면 연결된 **ITask** 개체는 단일 **IJob** 개체에 만들어집니다.

>[AZURE.NOTE]현재 작업은 작업당 30개로 제한됩니다. 30개 이상의 작업을 연결해야 하는 경우 둘 이상의 작업을 만들어 작업을 연결합니다.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

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
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}

###고려 사항

작업 체인을 사용하려면,

- 작업에 작업이 2개 이상 있어야 합니다.
- 작업에서 입력이 다른 작업의 출력인 작업이 하나 이상 있어야 합니다.


## JobTemplate을 사용하여 작업 만들기


작업의 공통 집합을 사용하여 여러 자산을 처리할 때 JobTemplates는 기본 작업 기본 설정, 작업의 순서 등을 지정하는 데 유용합니다.

다음 예제는 TaskTemplate 정의된 인라인이 있는 JobTemplate을 만드는 방법을 보여줍니다. TaskTemplate는 Azure Media Encoder를 MediaProcessor로 사용하여 자산 파일을 인코딩합니다. 그러나 다른 Mediaprocessor도 사용할 수 있습니다.


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]다른 미디어 서비스 엔터티와 달리, 각 TaskTemplate에 대한 새 GUID 식별자를 정의하고 요청 본문의 taskTemplateId 및 Id 속성에 배치해야 합니다. 콘텐츠 식별 구성표는 Azure 미디어 서비스 엔터티 식별에 설명된 구성표를 따라야 합니다. 또한 JobTemplates는 업데이트할 수 없습니다. 대신 업데이트된 변경 사항으로 새로운 템플릿을 만들어야 합니다.
 

성공하면 다음 응답이 반환됩니다.
	
	HTTP/1.1 201 Created
	
	. . .


다음 예제에서는 JobTemplate Id를 참조하는 작업을 만드는 방법을 보여줍니다.

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
	 

성공하면 다음 응답이 반환됩니다.
	
	HTTP/1.1 201 Created
	
	. . . 


##미디어 서비스 인코더 출력 파일 이름 제어 

기본적으로 Azure Media Encoder는 입력 자산 및 인코딩 프로세스의 다양 한 특성을 결합하여 출력 파일 이름을 만듭니다. 각 특성은 아래 설명과 같이 매크로를 사용하여 식별됩니다.

다음은 출력 파일 이름 지정에 사용할 수 있는 매크로의 전체 목록입니다. 오디오 비트 전송률은 오디오 인코딩 시 사용된 비트 전송률로 kbps로 지정됩니다.

- 오디오 코덱 오디오 인코딩에 사용된 코덱으로, 유효한 값은 AAC, WMA 및 DDP입니다.
- 유효한 값은 채널 수로 인코딩된 오디오 채널 수(1, 2 또는 6)입니다.
- 기본 확장명 – 기본 파일 확장명 
- 언어 오디오에 사용되는 언어를 나타내는 BCP-47 언어 코드입니다. 현재 기본값은 "und"입니다. 
- 원래 파일 이름 Azure 저장소에 업로드된 파일의 이름
- StreamId - 미리 설정된 파일의 <StreamInfo> 요소의 streamID 속성으로 정의되는 스트림 ID 
- 비디오 코덱 인코딩에 사용된 코덱으로, 유효한 값은 H264 및 VC1입니다.
- 비디오 비트 전송률 비디오 인코딩 시 사용된 비트 전송률 kbps 지정됩니다.

미디어 서비스 인코더에서 생성된 파일의 이름을 제어하도록 이 매크로를 임의 순열로 결합할 수 있습니다. 예를 들어, 기본 명명 규칙은 다음과 같습니다.

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

파일 명명 규칙은 [미리 설정](https://msdn.microsoft.com/library/azure/dn554334.aspx)의 DefaultMediaOutputFileName 특성을 사용하여 지정됩니다. 예:

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

인코더는 각 매크로 사이로 밑줄을 삽입합니다. 예를 들어, 위의 구성에서는 다음과 같은 파일 이름이 작성됩니다. MyVideo_H264_4500kpbs_AAC_und_ch2_128kbps.mp4.

##다음 단계
자산을 인코드하는 작업을 만드는 방법을 알아보았습니다. 이제 [미디어 서비스 작업 진행 상태를 확인하는 방법](media-services-rest-check-job-progress.md) 항목으로 이동하세요.

[Azure Marketplace]: https://datamarket.azure.com/
[Encoder Preset]: http://msdn.microsoft.com/library/dn619392.aspx
[How to: Get a Media Processor Instance]: http://go.microsoft.com/fwlink/?LinkId=301732
[How to: Upload an Encrypted Asset]: http://go.microsoft.com/fwlink/?LinkId=301733
[How to: Deliver an Asset by Download]: http://go.microsoft.com/fwlink/?LinkId=301734
[How to Check Job Progress]: http://go.microsoft.com/fwlink/?LinkId=301737
[Task Preset for Azure Media Packager]: http://msdn.microsoft.com/library/windowsazure/hh973635.aspx
 

<!---HONumber=62-->