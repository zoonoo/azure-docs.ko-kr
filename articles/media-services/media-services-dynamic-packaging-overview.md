<properties 
	pageTitle="동적 패키징 개요" 
	description="이 항목에서는 동적 패키징에 대해 간략하게 설명합니다." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="juliako"/>


#동적 패키징 

##개요

Microsoft Azure 미디어 서비스를 사용하여 많은 미디어 소스 파일 형식, 미디어 스트리밍 형식 및 콘텐츠 보호 형식을 다양한 클라이언트 기술(예: iOS, XBOX, Silverlight, Windows 8)에 제공할 수 있습니다. 이러한 클라이언트는 여러 가지 프로토콜을 이해합니다. 예를 들어 iOS에는 HLS(HTTP 라이브 스트리밍) V4 형식이 필요하고 Silverlight와 Xbox에는 부드러운 스트리밍이 필요합니다. MPEG DASH, HLS 또는 부드러운 스트리밍을 이해하는 클라이언트에 제공하려는 적응 비트 전송률(다중 비트 전송률) MP4(ISO 기본 미디어 14496-12) 파일 집합이나 적응 비트 전송률 부드러운 스트리밍 파일 집합이 있는 경우 미디어 서비스 동적 패키징을 이용해야 합니다.

동적 패키징을 사용하는 경우 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일의 집합이 포함된 자산을 만들기만 하면 됩니다. 이렇게 하면 매니페스트 또는 조각 요청의 지정된 형식에 따라 주문형 스트리밍 서버는 사용자가 선택한 프로토콜로 스트림을 받을 수 있도록 합니다. 따라서 사용자는 단일 저장소 형식으로 파일을 저장하고 해당 파일에 대한 요금을 지불하기만 하면 되며, 미디어 서비스에서 클라이언트의 요청에 따라 적절한 응답을 작성하고 제공합니다.

다음 다이어그램에서는 기존의 인코딩 및 정적 패키징 워크플로를 보여 줍니다.

![정적 인코딩](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

다음 다이어그램에서는 동적 패키징 워크플로를 보여 줍니다.

![동적 인코딩](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]동적 패키징을 이용하려면 먼저 콘텐츠를 배달할 계획인 스트리밍 끝점에 대한 주문형 스트리밍 단위를 하나 이상 가져와야 합니다. 자세한 내용은 [미디어 서비스 크기를 조정하는 방법](media-services-manage-origins.md#scale_streaming_endpoints)을 참조하세요.

##일반적인 시나리오

1. 입력 파일(mezzanine 파일이라고 함)을 업로드합니다. H.264, MP4 또는 WMV를 예로 들 수 있습니다(지원되는 형식의 목록은 미디어 서비스 인코더에서 지원하는 형식 참조).
 
1. mezzanine 파일을 H.264 MP4 적응 비트 전송률 집합으로 인코딩합니다.
 
1. 주문형 로케이터를 만들어 적응 비트 전송률 MP4 집합이 포함된 자산을 게시합니다.
 
1. 콘텐츠에 액세스하고 콘텐츠를 스트리밍할 스트리밍 URL을 작성합니다.
 
>[AZURE.NOTE]일부 MP4 파일 형식은 동적 패키징에서 지원되지 않습니다. 자세한 내용은 [동적 패키징에 지원되지 않는 형식](media-services-dynamic-packaging-overview.md#unsupported_formats)을 참조하세요.

##동적 스트리밍을 위한 자산 준비

동적 스트리밍을 위해 자산을 준비하려면 다음 두 가지 옵션을 사용할 수 있습니다.

- 마스터 파일을 업로드하고 Azure 미디어 인코더를 사용하여 H.264 MP4 적응 비트 전송률 집합을 생성합니다.
- 기존 적응 비트 전송률 집합을 업로드하고 Media Packager를 사용하여 유효성을 검사합니다.

###마스터 파일을 업로드하고 Azure 미디어 인코더를 사용하여 H.264 MP4 적응 비트 전송률 집합 생성

자산을 업로드하고 인코딩하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.


**Azure 관리 포털**, **.NET** 또는 **REST API**를 사용하여 파일을 업로드합니다.

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

**Azure 관리 포털**, **.NET** 또는 **REST API**를 사용하여 **Azure 미디어 인코더**로 인코딩합니다.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]


###기존 적응 비트 전송률 집합을 업로드하고 Media Packager를 사용하여 유효성 검사

미디어 서비스 인코더로 인코딩되지 않은 적응 비트 전송률 MP4 파일 집합을 업로드하는 경우 일반적으로 이 작업을 수행할 수 있습니다. [외부 인코더로 인코딩된 적응 비트 전송률 MP4 유효성 검사](https://msdn.microsoft.com/library/azure/dn750842.aspx) 항목에서 이 작업을 수행하는 방법을 보여 줍니다.

##클라이언트로 콘텐츠 스트리밍

적응 비트 전송률 집합이 있는 경우 주문형 로케이터를 만들어 자산을 게시하고 부드러운 스트리밍, MPEG DASH, HLS 및 HDS(Adobe PrimeTime/Access 정식 사용자만 해당)에 대한 스트리밍 URL을 작성할 수 있습니다.

로케이터를 만들고 동적 패키징을 사용하여 콘텐츠를 스트리밍하는 방법에 대한 자세한 내용은 다음 항목을 참조하세요.

[고객에 콘텐츠 배달 개요](media-services-deliver-content-overview.md)

**.NET** 또는 **REST API**를 사용하여 자산 배달 정책을 구성합니다.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

**Azure 관리 포털** 또는 **.NET**을 사용하여 자산을 게시합니다(로케이터를 만들어서 게시).

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##<a id="unsupported_formats"></a>동적 패키징에서 지원하지 않는 형식

다음과 같은 소스 파일 형식은 동적 패키징에서 지원하지 않습니다.

- Dolby Digital Plus mp4 파일
- Dolby Digital Plus 부드러운 파일. 

<!---HONumber=July15_HO4-->