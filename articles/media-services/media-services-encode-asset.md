<properties 
	pageTitle="Azure 주문형 미디어 인코더 개요 및 비교" 
	description="이 항목에서는 Azure 주문형 미디어 인코더 개요 및 비교를 제공합니다." 
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
	ms.date="09/23/2015"  
	ms.author="juliako"/>

#Azure 주문형 미디어 인코더 개요 및 비교

##인코딩 개요

인코더는 코덱을 사용하여 디지털 미디어를 압축합니다. 일반적으로 인코더에는 생성된 미디어의 속성(예: 사용된 코덱, 파일 형식, 해상도 및 비트 전송률)을 지정할 수 있는 다양한 설정이 있습니다. 파일 형식은 비디오를 압축하는 데 사용된 코덱에 대한 정보뿐만 아니라 압축된 비디오를 보관하는 컨테이너입니다.

코덱에는 두 개의 구성 요소가 있으며, 전송을 위해 디지털 미디어 파일을 압축하는 구성 요소와 재생을 위해 디지털 미디어 파일의 압축을 푸는 구성 요소입니다. 코덱에는 오디오를 압축하고 압축을 푸는 오디오 코덱과 비디오를 압축하고 압축을 푸는 비디오 코덱이 있습니다. 코덱은 무손실 압축이나 손실 허용 압축을 사용할 수 있습니다. 무손실 코덱은 압축이 수행될 때 모든 정보를 유지합니다. 파일의 압축을 푸는 경우 결과 파일이 입력 미디어와 동일하므로 무손실 코덱은 보관 및 저장에 매우 적합합니다. 손실 허용 코덱은 인코딩할 때 일부 정보가 손실되고 원본보다 더 작은 파일을 생성하여 비디오 품질이 손상되므로 인터넷을 통한 스트리밍에 적합합니다.

코덱과 파일 형식 간의 차이점을 이해하는 것은 중요합니다. 코덱은 압축/압축 해제 알고리즘을 구현하는 소프트웨어이고 파일 형식은 압축된 비디오를 보관하는 컨테이너입니다. 자세한 내용은 [인코딩 및 패키징](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/)(영문)을 참조하세요.

미디어 서비스는 적응 비트 전송률 MP4 또는 부드러운 스트리밍 인코딩 콘텐츠를 미디어 서비스에서 지원되는 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍, HDS)으로 다시 패키지하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다.

[동적 패키징](media-services-dynamic-packaging-overview.md)을 이용하려면 다음을 수행해야 합니다.

- mezzanine(원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩합니다(인코딩 단계는 이 자습서의 뒷부분에서 설명).
- 콘텐츠를 배달하는 출발점이 될 스트리밍 끝점에 하나 이상의 주문형 스트리밍 단위를 구성합니다. 자세한 내용은 [주문형 스트리밍 예약 단위를 확장하는 방법](media-services-manage-origins.md#scale_streaming_endpoints/)을 참조하세요

미디어 서비스는 이 문서에서 설명하는 다음 주문형 인코더를 지원합니다.

- **미디어 인코더 표준**
- **Azure 미디어 인코더** 
- **미디어 인코더 Premium 워크플로**

이 문서에서는 주문형 미디어 인코더에 대한 간략한 개요와 보다 자세한 정보를 제공하는 문서에 대한 링크를 제공합니다. 또한 항목에서는 인코더에 대한 비교를 제공합니다.

기본적으로 각 미디어 서비스 계정은 한번에 하나의 인코딩 작업을 활성화할 수 있습니다. 구입한 각 예약 인코딩에 대해 하나씩, 여러 인코딩 작업을 동시에 실행할 수 있는 인코딩 단위를 예약할 수 있습니다. 자세한 내용은 [인코딩 단위 크기 조정](media-services-portal-encoding-units.md)을 참조하세요.

##미디어 인코더 표준

###개요

미디어 인코더 표준 인코더를 사용하는 것이 좋습니다. 그러나 Azure 포털에는 현재 노출되지 않습니다.

Azure 미디어 인코더에 비해 더 입력 및 출력 형식과 코덱을 지원합니다. 기타 이점은 다음과 같습니다.

- 입력 파일을 생성하는 방법이 보다 허용적
- Azure 미디어 인코더보다 H.264 코덱 품질 우수
- 최신의 보다 유연한 파이프라인을 기반으로 함
- 보다 견고하고 복원적

###사용 방법

[미디어 인코더 표준으로 인코딩하는 방법](media-services-dotnet-encode-with-media-encoder-standard.md)

###형식

[형식 및 코덱](media-services-media-encoder-standard-formats.md)

###기본 설정

미디어 인코더 표준은 [여기](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)에서 설명한 인코더 기본 설정 중 하나를 사용하여 구성됩니다.

###입력 및 출력 메타데이터

인코더 입력 메타데이터는 [여기](http://msdn.microsoft.com/library/azure/dn783120.aspx)에서 설명합니다.

인코더 출력 메타데이터는 [여기](http://msdn.microsoft.com/library/azure/dn783217.aspx)에서 설명합니다.

###미리 보기

미리 보기를 생성하는 방법에 대한 자세한 내용은 [미디어 인코더 표준을 사용하여 미리 보기를 생성하는 방법](media-services-dotnet-generate-thumbnail-with-mes.md)을 참조하세요.

###오디오 및/또는 비디오 오버레이

현재 지원되지 않습니다.

###참고 항목

[미디어 서비스 블로그](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Azure 미디어 인코더

###개요

Azure 미디어 인코더는 미디어 서비스에서 지원하는 인코더 중 하나입니다. 2015년 7월부터 [미디어 인코더 표준](media-services-encode-asset.md#media_encoder_standard)을 사용하는 것이 좋습니다.

###사용 방법

[Azure 미디어 인코더로 인코딩하는 방법](media-services-dotnet-encode-asset.md)

###형식

[형식 및 코덱](media-services-azure-media-encoder-formats.md)

###기본 설정

Azure 미디어 인코더는 [여기](https://msdn.microsoft.com/library/azure/dn619392.aspx)서 설명하는 인코더 기본 설정 중 하나를 사용하여 구성됩니다. [여기](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder)서 실제 Azure 미디어 인코더 기본 설정 파일을 가져올 수도 있습니다.

###입력 및 출력 메타데이터

인코더 입력 메타데이터는 [여기](http://msdn.microsoft.com/library/azure/dn783120.aspx)에서 설명합니다.

인코더 출력 메타데이터는 [여기](http://msdn.microsoft.com/library/azure/dn783217.aspx)에서 설명합니다.

###미리 보기

[미리 보기 만들기](https://msdn.microsoft.com/library/hh973624.aspx)

###오디오 및/또는 비디오 오버레이

[오버레이 만들기](media-services-azure-media-customize-ame-presets.md#creating-overlays)

###명명 규칙

[출력 파일 이름을 수정하는 방법](media-services-azure-media-customize-ame-presets.md#controlling-azure-media-encoder-output-file-names)

###참고 항목

[Dolby Digital Plus를 사용하여 미디어 인코딩](media-services-encode-with-dolby-digital-plus.md)

##미디어 인코더 Premium 워크플로

###개요

[Azure 미디어 서비스의 프리미엄 인코딩 소개](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

###사용 방법

미디어 인코더 Premium 워크플로는 복잡한 워크플로를 사용하여 구성됩니다. 워크플로 파일은 [Workflow Designer](media-services-workflow-designer.md) 도구를 사용하여 만들고 업데이트할 수 있습니다.

[Azure 미디어 서비스의 프리미엄 인코딩 사용 방법](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

##<a id="compare_encoders"></a>인코더 비교

###<a id="billing"></a>각 인코더에서 사용되는 요금 청구 기준

미디어 프로세서 이름|적용 가능한 가격|참고 사항
---|---|---
**미디어 인코더 표준** |인코더|인코딩 작업은 출력 자산의 크기(기가바이트 단위)에 따라 [여기][1](인코더 열 아래)에 지정된 비율로 청구됩니다.
**Azure 미디어 인코더** |인코더|인코딩 작업은 출력 자산의 크기(기가바이트 단위)에 따라 [여기][1](인코더 열 아래)에 지정된 비율로 청구됩니다.
**미디어 인코더 Premium 워크플로** |프리미엄 인코더|인코딩 작업은 출력 자산의 크기(기가바이트 단위)에 따라 [여기][1](프리미엄 인코더 열 아래)에 지정된 비율로 청구됩니다.


이 섹션에서는 **미디어 인코더 표준**, **Azure 미디어 인코더**, **미디어 인코더 Premium 워크플로**의 인코딩 기능을 비교합니다.


###입력 컨테이너/파일 형식

입력 컨테이너/파일 형식|미디어 인코더 표준|Azure 미디어 인코더|미디어 인코더 Premium 워크플로
---|---|---|---
Adobe® Flash® F4V |예|아니요 |예
MXF/SMPTE 377M |예|제한|예
GXF |예|아니요 |예
MPEG-2 전송 스트림 |예|예 |예
MPEG-2 프로그램 스트림 |예|예 |예
MPEG-4/MP4 |예|예 |예
Windows Media/ASF |예|예 |예
AVI(압축되지 않은 8비트/10비트)|예|예 |예
3GPP/3GPP2 |예|예 |아니요
부드러운 스트리밍 파일 형식(PIFF 1.3)|예|예|아니요
[DVR-MS(Microsoft Digital Video Recording)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|예|아니요|아니요
Matroska/WebM |예|아니요|아니요

###입력 비디오 코덱

입력 비디오 코덱|미디어 인코더 표준|Azure 미디어 인코더|미디어 인코더 Premium 워크플로
---|---|---|---
AVC 8비트/10비트, 최대 4:2:2, AVCIntra 포함 |8비트 4:2:0 및 4:2:2|8비트 4:2:0만|예
Avid DNxHD(MXF) |예|아니요|예
DVCPro/DVCProHD(MXF) |예|아니요|예
JPEG2000 |예|아니요|예
MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함)|최대 422 프로필|최대 422 프로필|예
MPEG-1 |예|예|예
Windows Media 비디오/VC-1 |예|예|예
Canopus HQ/HQX |아니요|예|아니요
Mpeg-4 2부 |예|아니요|아니요
[Theora](https://en.wikipedia.org/wiki/Theora) |예|아니요|아니요

###입력 오디오 코덱

입력 오디오 코덱|미디어 인코더 표준|Azure 미디어 인코더|미디어 인코더 Premium 워크플로
---|---|---|---
AES(SMPTE 331M 및 302M, AES3-2003) |아니요|아니요|예
Dolby® E |아니요|아니요|예
Dolby® Digital(AC3) |아니요|예|예
Dolby® Digital Plus(E-AC3) |아니요|아니요|예
AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1)|예|예|예
MPEG Layer 2|예|예|예
MP3(MPEG-1 Audio Layer 3)|예|예|예
Windows Media 오디오|예|예|예
WAV/PCM|예|예|예
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|예|아니요|아니요
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |예|아니요|아니요
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|예|아니요|아니요


###출력 컨테이너/파일 형식

출력 컨테이너/파일 형식|미디어 인코더 표준|Azure 미디어 인코더|미디어 인코더 Premium 워크플로
---|---|---|---
Adobe® Flash® F4V|아니요|아니요|예
MXF(OP1a, XDCAM 및 AS02)|아니요|아니요|예
DPP(AS11 포함)|아니요|아니요|예
GXF|아니요|아니요|예
MPEG-4/MP4|예|예|예
MPEG-TS|예|아니요|예
Windows Media/ASF|아니요|예|예
AVI(압축되지 않은 8비트/10비트)|아니요|아니요|예
부드러운 스트리밍 파일 형식(PIFF 1.3)|아니요|예|예

###출력 비디오 코덱

출력 비디오 코덱|미디어 인코더 표준|Azure 미디어 인코더|미디어 인코더 Premium 워크플로
---|---|---|---
AVC(H.264, 8비트, 최대 High Profile, 수준 5.2, 4K Ultra HD, AVC Intra)|8비트 4:2:0만|8비트 4:2:0만 최대 1080p|예
Avid DNxHD(MXF)|아니요|아니요|예
DVCPro/DVCProHD(MXF)|아니요|아니요|예
MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함)|아니요|아니요|예
MPEG-1|아니요|아니요|예
Windows Media 비디오/VC-1|아니요|예|예
JPEG 축소판 그림 만들기|아니요|예|예

###출력 오디오 코덱

출력 오디오 코덱|미디어 인코더 표준|Azure 미디어 인코더|미디어 인코더 Premium 워크플로
---|---|---|---
AES(SMPTE 331M 및 302M, AES3-2003)|아니요|아니요|예
Dolby® Digital(AC3)|아니요|예|예
Dolby® Digital Plus(E-AC3) 최대 7.1|아니요|최대 5.1|예
AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1)|예|예|예
MPEG Layer 2|아니요|아니요|예
MP3(MPEG-1 Audio Layer 3)|아니요|아니요|예
Windows Media 오디오|아니요|예|예


##미디어 서비스 학습 경로

여기서 AMS 학습 경로를 볼 수 있습니다.

- [AMS 라이브 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS 주문형 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##관련된 문서

- [할당량 및 제한 사항](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=Oct15_HO1-->