<properties 
	pageTitle="미디어 서비스 주문형 비디오 워크플로" 
	description="이 항목에서는 일반적인 미디어 서비스 주문형 비디오 워크플로 단계를 설명합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#미디어 서비스 주문형 비디오 워크플로

##개요

이 항목에서는 일반적인 AMS(Azure 미디어 서비스) 주문형 비디오 워크플로 단계를 설명합니다. 각 단계는 관련 항목에 연결됩니다. 다른 기술을 사용하여 얻을 수 있는 작업의 경우, 선택한 기술(예:.NET 또는 REST)에 연결되는 단추가 있습니다.   

미디어 서비스와 기존 도구 및 프로세스를 통합할 수 있습니다. 예를 들어 현장에서 콘텐츠를 인코딩한 후 여러 형식으로 트랜스코딩하고 Azure CDN 또는 타사 CDN을 통해 전달하기 위해 미디어 서비스에 업로드합니다. 

다음 다이어그램에서는 주문형 워크플로 관련 비디오에 관련된 미디어 서비스 플랫폼의 주요 부분을 보여 줍니다.
![VoD workflow][vod-overview]

자세한 내용은 [미디어 서비스 개요]를 참조하세요(media-services-overview.md).

##미디어 서비스 계정 만들기

**Azure 관리 포털**을 이용하여 [Azure Media Services 계정]을 만듭니다(media-services-create-account.md). 

##스트리밍 끝점 구성

[진행 중인 작업]

##개발 환경 설정하기  

개발 환경에 **.NET** 또는 **REST API**를 선택합니다.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##프로그래밍 방식으로 연결하기  

Azure Media Services에 프로그래밍 방식으로 연결할 **.NET** 또는 **REST API**를 선택합니다.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##미디어 업로드 

**Azure 관리 포털**, **.NET** 또는 **REST API**를 사용하여 파일을 업로드합니다.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

##미디어 처리: 인코딩, 인덱싱, 작업 모니터링

###미디어 프로세서 가져오기

**.NET** 또는 **REST API**를 사용하여 미디어 프로세서를 가져옵니다.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

###작업 만들기 

작업은 태스크 집합(예: 인코딩 또는 인덱싱)에 대한 메타데이터를 포함하는 엔터티입니다. 각 태스크는 입력 자산에 대한 원자성 작업을 수행합니다. 인코딩 작업을 만드는 방법에 대한 예제는 다음을 참조하세요.

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###작업 진행 상태 모니터링

**Azure 관리 포털**, **.NET** 또는 **REST API**를 사용하여 작업 진행 상태를 모니터링합니다.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

###인덱싱

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

###인코딩 

[Azure 미디어 서비스로 인코딩]을 참조하세요(media-services-encode-asset.md).

##콘텐츠 키 인증 정책 구성 

**.NET** 또는 **REST API**를 사용하여 콘텐츠 보호 및 키 인증 정책을 구성합니다.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##자산 배달 정책 구성

**.NET** 또는 **REST API**를 사용하여 자산 배달 정책을 구성합니다.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##자산 게시하기

**Azure 관리 포털** 또는**.NET**을 사용하여 자산을 게시합니다(로케이터를 만들어서 게시).

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]

##Media Services 계정 크기 조정하기

계정에서 프로비전할 **스트리밍 예약 단위** 및 **인코딩 예약 단위**의 수를 지정하여 **미디어 서비스**를 확장할 수 있습니다. 

또한 저장소 계정을 추가하여 미디어 서비스 계정을 확장할 수 있습니다. 각 저장소 계정은 500TB로 제한됩니다. 여러 저장소 계정을 단일 미디어 서비스 계정에 연결하여 기본 제한 이상으로 저장소를 확장할 수 있습니다.

[이](media-services-how-to-scale.md) 항목은 관련 항목으로 연결됩니다.


##콘텐츠 재생

자세한 내용은 [기존 플레이어를 사용하여 콘텐츠 재생]을 참조하세요(media-services-playback-content.md).

[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png
<!--HONumber=47-->
