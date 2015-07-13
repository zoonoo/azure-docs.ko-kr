<properties 
	pageTitle="Azure 미디어 서비스를 사용하여 주문형 미디어 제공" 
	description="이 항목에서는 Azure 미디어 서비스를 사용하여 주문형 미디어를 제공하는 일반적인 시나리오에 대해 다룹니다." 
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
	ms.date="06/08/2015" 
	ms.author="juliako"/>


#Azure 미디어 서비스를 사용하여 주문형 미디어 제공

##개요

이 항목에서는 일반적인 AMS(Azure 미디어 서비스) 주문형 비디오 워크플로 단계를 설명합니다. 각 단계는 관련 항목에 연결됩니다. 다른 기술을 사용하여 얻을 수 있는 작업의 경우, 선택한 기술(예:.NET 또는 REST)에 연결되는 단추가 있습니다.

미디어 서비스와 기존 도구 및 프로세스를 통합할 수 있습니다. 예를 들어 현장에서 콘텐츠를 인코딩한 후 여러 형식으로 트랜스코딩하고 Azure CDN 또는 타사 CDN을 통해 전달하기 위해 미디어 서비스에 업로드합니다.

다음 다이어그램에서는 주문형 워크플로 관련 비디오에 관련된 미디어 서비스 플랫폼의 주요 부분을 보여 줍니다.![VoD 워크플로][vod-overview]

##<a id="vod_scenarios"></a>일반적인 시나리오: 주문형 미디어를 제공합니다. 

###저장소에서 콘텐츠 보호 및 암호화하지 않고 스트리밍 미디어 배달(암호화되지 않음)

1. 자산에 고품질 mezzanine 파일을 업로드합니다.
	
	업로드하는 동안 및 저장소에 있는 동안 콘텐츠를 보호하기 위해 자산에 저장소 암호화 옵션을 적용하는 것이 좋습니다. 
1. 적응 비트 전송률 MP4 집합을 인코딩합니다. 

	그대로 있는 콘텐츠를 보호하기 위해 출력 자산에 저장소 암호화 옵션을 적용하는 것이 좋습니다.
	
1. 자산 배달 정책(동적 패키징에서 사용)을 구성합니다.
	
	자산이 암호화된 저장소인 경우 자산 배달 정책을 구성해야 **합니다**.

1. 주문형 로케이터를 만들어 자산을 게시합니다.

	콘텐츠를 스트림하려는 스트리밍 끝점에서 최소 1개의 스트리밍 예약 단위가 있어야 합니다.

1. 게시된 콘텐츠를 스트리밍합니다.

###저장소에서 콘텐츠를 보호하고 암호화된 스트리밍 미디어를 동적으로 배달합니다.  

동적 암호화를 사용할 수 있으려면 먼저 암호화된 콘텐츠를 스트리밍하려는 스트리밍 끝점에서 하나 이상의 스트리밍 예약 단위를 가져와야 합니다.

1. 자산에 고품질 mezzanine 파일을 업로드합니다. 저장소 암호화 옵션을 자산에 적용합니다.
1. 적응 비트 전송률 MP4 집합을 인코딩합니다. 저장소 암호화 옵션을 출력 자산에 적용합니다.
1. 재생하는 동안 동적으로 암호화하려는 경우 자산에 대한 암호화 콘텐츠 키를 만듭니다.
2. 콘텐츠 키 인증 정책을 구성합니다.
1. 자산 배달 정책(동적 패키징 및 동적 암호화에서 사용)을 구성합니다.
1. 주문형 로케이터를 만들어 자산을 게시합니다.
1. 게시된 콘텐츠를 스트리밍합니다. 

###콘텐츠 인덱싱

1. 자산에 고품질 mezzanine 파일을 업로드합니다.
1. 콘텐츠를 인덱싱합니다.

	인덱싱 작업은 비디오 재생에서 CC(선택 캡션)으로 사용할 수 있는 파일을 생성합니다. 또한 비디오 내 검색을 수행하고 비디오의 정확한 위치로 이동할 수 있는 파일을 생성합니다.

1. 인덱싱된 콘텐츠를 사용합니다.


###점진적 다운로드 제공 

1. 자산에 고품질 mezzanine 파일을 업로드합니다.
1. 적응 비트 전송률 MP4 집합 또는 단일 MP4를 인코딩합니다.
1. 주문형 또는 SAS 로케이터를 만들어 자산을 게시합니다.

	주문형 로케이터를 사용하는 경우, 점진적으로 콘텐츠를 다운로드하려는 스트리밍 끝점에 하나 이상의 스트리밍 예약 단위가 있어야 합니다.

	SAS 로케이터를 사용하는 경우 콘텐츠는 Azure blob 저장소에서 다운로드됩니다. 이 경우 스트리밍 예약 단위가 필요 없습니다.
  
1. 콘텐츠를 점진적으로 다운로드합니다.

이 문서에는 개발 환경 설정 및 위에 언급된 작업 수행 방법을 보여주는 항목에 대한 링크가 포함됩니다.


##개념

주문형 콘텐츠 배달과 관련된 개념은 [미디어 서비스 개념](media-services-concepts.md)을 참조하세요

##일반 작업: 주문형 미디어 제공

###미디어 서비스 계정 만들기

**Azure 관리 포털**을 이용하여 [Azure 미디어 서비스 계정](media-services-create-account.md)을 만듭니다.

###개발 환경 설정하기  

개발 환경에 **.NET** 또는 **REST API**를 선택합니다.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

###프로그래밍 방식으로 연결하기  

Azure 미디어 서비스에 프로그래밍 방식으로 연결할 **.NET** 또는 **REST API**를 선택합니다.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]


###스트리밍 끝점 구성

스트리밍 끝점 개요 및 관리 방법에 대한 정보는 [미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법](media-services-manage-origins.md)을 참조하세요.

###미디어 업로드 

**Azure 관리 포털**, **.NET** 또는 **REST API**를 사용하여 파일을 업로드합니다.

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

###작업 \ 태스크 만들기

작업은 태스크 집합(예: 인코딩 또는 인덱싱)에 대한 메타데이터를 포함하는 엔터티입니다. 각 태스크는 입력 자산에 대한 원자성 작업을 수행합니다. 인코딩 작업을 만드는 방법에 대한 예제는 다음을 참조하세요.

개요는 [Azure 미디어 서비스 작업](media-services-jobs.md) 사용을 참조하세요

**.NET** 또는 **REST API**로 작업에 적합한 미디어 프로세서를 가져옵니다.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

다음 예는 **Azure 관리 포털**, **.NET** 또는 **REST API**로 인코딩 작업을 만듭니다.

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####인덱싱

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

####인코딩 

**개요**:

- [동적 패키징 개요](media-services-dynamic-packaging-overview.md)
- [Azure 미디어 서비스로 주문형 콘텐츠 인코딩](media-services-encode-asset.md)

**Azure 관리 포털**, **.NET** 또는 **REST API**를 사용하여 **Azure 미디어 인코더**로 인코딩합니다.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

**.NET**를 사용하는 **미디어 인코더 Premium 워크플로**를 사용한 고급 인코딩

[AZURE.INCLUDE [media-services-selector-advanced-encoding](../../includes/media-services-selector-advanced-encoding.md)]

####작업 진행 상태 모니터링

**Azure 관리 포털**, **.NET** 또는 **REST API**를 사용하여 작업 진행 상태를 모니터링합니다.

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

###콘텐츠 보호 

**개요**:

[콘텐츠 보호 개요](media-services-content-protection-overview.md)

자산을 AES(Advanced Encryption Standard,128비트 암호화 키 사용) 또는 PlayReady DRM을 사용하여 암호화하려면 콘텐츠 키를 만들어야 합니다.

**.NET** 또는 **REST API**를 사용하여 키를 만듭니다.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

콘텐츠 키를 만든 후 **.NET** 또는 **REST API**를 사용하여 키 인증 정책을 구성할 수 있습니다.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


**.NET** 또는 **REST API**를 사용하여 자산 배달 정책을 구성합니다.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]


####파트너와 통합

[castLabs를 사용하여 Azure 미디어 서비스에 DRM 라이선스 제공](media-services-castlabs-integration.md)

###자산 게시 및 제공

동적 패키징 개요

> [AZURE.SELECTOR]
- [Overview](media-services-dynamic-packaging-overview.md)


콘텐츠 개요 제공

> [AZURE.SELECTOR]
- [Overview](media-services-deliver-content-overview.md)

**Azure 관리 포털**, **.NET** 또는 **REST API**을 사용하여 자산을 게시합니다(로케이터를 만들어서 게시).

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]

###Azure CDN 사용하기

미디어 서비스는 Azure CDN과의 통합을 지원합니다. Azure CDN을 사용하도록 설정하는 방법에 대한 자세한 내용은 [미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법](media-services-manage-origins.md#enable_cdn)을 참조하세요.

###Media Services 계정 크기 조정하기

계정에서 프로비전할 **스트리밍 예약 단위** 및 **인코딩 예약 단위**의 수를 지정하여 **미디어 서비스**를 확장할 수 있습니다.

또한 저장소 계정을 추가하여 미디어 서비스 계정을 확장할 수 있습니다. 각 저장소 계정은 500TB로 제한됩니다. 여러 저장소 계정을 단일 미디어 서비스 계정에 연결하여 기본 제한 이상으로 저장소를 확장할 수 있습니다.

[이](media-services-how-to-scale.md) 항목은 관련 항목으로 연결됩니다.

###기존 플레이어를 사용하여 콘텐츠 재생

자세한 내용은 [기존 플레이어를 사용하여 콘텐츠 재생](media-services-playback-content-with-existing-players.md)을 참조하세요.


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
 

<!---HONumber=62-->