<properties 
	pageTitle="Azure 미디어 서비스로 주문형 콘텐츠 인코딩" 
	description="이 항목에서는 미디어 서비스를 사용하는 주문형 콘텐츠 인코딩의 개요를 제공 합니다." 
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
	ms.date="08/11/2015"  
	ms.author="juliako"/>

#Azure 미디어 서비스로 주문형 콘텐츠 인코딩

이 항목은 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md)의 일부입니다.

##개요

미디어 서비스는 다음 인코더를 지원합니다.

- [미디어 인코더 표준](#media_encoder_standard)
- [Azure 미디어 인코더](#azure_media_encoder)
- [미디어 인코더 Premium 워크플로](#media_encoder_premium_workflow)

[다음 섹션](#compare_encoders)에서는 지원되는 인코더의 인코딩 기능을 비교합니다.

기본적으로 각 미디어 서비스 계정은 한번에 하나의 인코딩 작업을 활성화할 수 있습니다. 구입한 각 예약 인코딩에 대해 하나씩, 여러 인코딩 작업을 동시에 실행할 수 있는 인코딩 단위를 예약할 수 있습니다. 인코딩 단위 크기를 조정하는 방법에 대한 내용은 **포털** 및 **.NET** 항목을 참조하세요.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="media_encoder_standard"></a>미디어 인코더 표준

[미디어 인코더 표준에서 지원되는 형식](media-services-media-encoder-standard-formats.md) – **미디어 인코더 표준**에서 지원되는 파일 및 스트림 형식에 대해 설명합니다.

**미디어 인코더 표준**은 [여기](http://go.microsoft.com/fwlink/?LinkId=618336)에 설명된 인코더 기본 설정 중 하나를 사용하거나 [이러한](http://go.microsoft.com/fwlink/?LinkId=618336) 기본 설정을 기반으로 하는 사용자 지정 기본 설정을 사용하여 구성됩니다.

자세한 내용은 [이 게시물](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)을 참조하세요.

##<a id="azure_media_encoder"></a>Azure 미디어 인코더

[미디어 서비스 인코더에서 지원하는 형식](media-services-azure-media-encoder-formats.md) – **Azure 미디어 인코더**에서 지원하는 파일 및 스트림 형식에 대해 설명합니다.

**Azure 미디어 인코더**는 [여기](https://msdn.microsoft.com/library/azure/dn619392.aspx)에서 설명한 인코더 기본 설정 문자열 중 하나를 사용하여 구성됩니다. [여기](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder)에서 실제 Azure 미디어 인코더 기본 설정 파일을 가져올 수도 있습니다.

###예

**Azure 관리 포털**, **.NET** 또는 **REST API**를 사용하여 **Azure 미디어 인코더**로 인코딩합니다.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####다른 관련된 항목

[동적 패키징](https://msdn.microsoft.com/library/azure/jj889436.aspx) – 적응 비트 전송률 MP4로 인코딩하는 방법과 부드러운 스트리밍, Apple HLS 또는 MPEG-DASH를 동적으로 제공하는 방법을 설명합니다.

[미디어 서비스 인코더 출력 파일 이름 제어](https://msdn.microsoft.com/library/azure/dn303341.aspx) – Azure 미디어 인코더에서 사용된 파일 명명 규칙 및 출력 파일 이름을 수정하는 방법을 설명합니다.

[Dolby Digital Plus로 미디어 인코딩](media-services-encode-with-dolby-digital-plus.md) – Dolby Digital Plus 인코딩을 사용하여 오디오 트랙을 인코딩하는 방법을 설명합니다.


##<a id="media_encoder_premium_wokrflow"></a>미디어 인코더 Premium 워크플로 

**참고** 중국에서는 이 항목에서 설명하는 미디어 인코더 Premium 워크플로 미디어 프로세서를 사용할 수 없습니다.

[미디어 인코더 Premium 워크플로에서 지원하는 형식](media-services-premium-workflow-encoder-formats.md) – **미디어 인코더 Premium 워크플로**에서 지원하는 파일 형식 및 코덱에 대해 설명합니다.

### Workflow Designer

**미디어 인코더 Premium 워크플로**는 복잡한 워크플로를 사용하여 구성됩니다. 워크플로 파일은 [Workflow Designer](media-services-workflow-designer.md) 도구를 사용하여 만들 수 있습니다.

[여기](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)에서 기본 워크플로 파일을 가져올 수 있습니다. 폴더에는 이러한 파일에 대한 설명도 포함되어 있습니다.

###예
**.NET**을 사용하는 **미디어 인코더 Premium 워크플로**로 인코딩합니다. 자세한 내용은 [미디어 인코더 Premium 워크플로를 사용한 고급 인코딩](media-services-encode-with-premium-workflow.md)을 참조하세요.
 

##<a id="compare_encoders"></a>인코더 비교

###<a id="billing"></a>각 인코더에서 사용되는 요금 청구 기준

미디어 프로세서 이름|적용 가능한 가격|참고 사항
---|---|---
**Microsoft Azure 미디어 인코더** |기존 인코더|인코딩 작업은 입력 자산 및 출력 자산의 크기 합계(기가바이트 단위)에 따라 [여기][1](기존 인코더 열 아래)에 지정된 비율로 청구됩니다.
**Azure 미디어 인코더** |인코더|인코딩 작업은 출력 자산의 크기(기가바이트 단위)에 따라 [여기][1](인코더 열 아래)에 지정된 비율로 청구됩니다.
**미디어 인코더 표준** |인코더|인코딩 작업은 출력 자산의 크기(기가바이트 단위)에 따라 [여기][1](인코더 열 아래)에 지정된 비율로 청구됩니다.
**미디어 인코더 Premium 워크플로** |프리미엄 인코더|인코딩 작업은 출력 자산의 크기(기가바이트 단위)에 따라 [여기][1](프리미엄 인코더 열 아래)에 지정된 비율로 청구됩니다.



이 섹션에서는 **Azure 미디어 인코더**, **미디어 인코더 Premium 워크플로**, **미디어 인코더 표준**의 인코딩 기능을 비교합니다.


###입력 형식

입력 컨테이너/파일 형식

입력 컨테이너/파일 형식|미디어 인코더 Premium 워크플로|Azure 미디어 인코더|미디어 인코더 표준
---|---|---|---
Adobe® Flash® F4V|예|아니요|예
MXF/SMPTE 377M|예|제한|예
GXF|예|아니요|예
MPEG-2 전송 스트림|예|예|예
MPEG-2 프로그램 스트림|예|예|예
MPEG-4/MP4|예|예|예
Windows Media/ASF|예|예|예
AVI(압축되지 않은 8비트/10비트)|예|예|예
3GPP/3GPP2|아니요|예|예
부드러운 스트리밍 파일 형식(PIFF 1.3)|아니요|예|예
[DVR-MS(Microsoft Digital Video Recording)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|아니요|아니요|예
Matroska/WebM|아니요|아니요|예

입력 비디오 코덱

입력 비디오 코덱|미디어 인코더 Premium 워크플로|Azure 미디어 인코더|미디어 인코더 표준
---|---|---|---
AVC 8비트/10비트, 최대 4:2:2, AVCIntra 포함|예|8비트 4:2:0만|8비트 4:2:0 및 4:2:2
Avid DNxHD(MXF)|예|아니요|예
DVCPro/DVCProHD(MXF)|예|아니요|예
JPEG2000|예|아니요|예
MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함)|예|최대 422 프로필|최대 422 프로필
MPEG-1|예|예|예
Windows Media 비디오/VC-1|예|예|예
Canopus HQ/HQX|아니요|예|아니요
Mpeg-4 2부|아니요|아니요|예
[Theora](https://en.wikipedia.org/wiki/Theora)|아니요|아니요|예

입력 오디오 코덱

입력 오디오 코덱|미디어 인코더 Premium 워크플로|Azure 미디어 인코더|미디어 인코더 표준
---|---|---|---
AES(SMPTE 331M 및 302M, AES3-2003)|예|아니요|아니요
Dolby® E|예|아니요|아니요
Dolby® Digital(AC3)|예|예|아니요
Dolby® Digital Plus(E-AC3)|예|아니요|아니요
AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1)|예|예|예
MPEG Layer 2|예|예|예
MP3(MPEG-1 Audio Layer 3)|예|예|예
Windows Media 오디오|예|예|예
WAV/PCM|예|예|예
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|아니요|아니요|예
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |아니요|아니요|예
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|아니요|아니요|예

###출력 형식

출력 컨테이너/파일 형식

출력 컨테이너/파일 형식|미디어 인코더 Premium 워크플로|Azure 미디어 인코더|미디어 인코더 표준
---|---|---|---
Adobe® Flash® F4V|예|아니요|아니요
MXF(OP1a, XDCAM 및 AS02)|예|아니요|아니요
DPP(AS11 포함)|예|아니요|아니요
GXF|예|아니요|아니요
MPEG-4/MP4|예|예|예
MPEG-TS|예|아니요|예
Windows Media/ASF|예|예|아니요
AVI(압축되지 않은 8비트/10비트)|예|아니요|아니요
부드러운 스트리밍 파일 형식(PIFF 1.3)|예|예|아니요

출력 비디오 코덱

출력 비디오 코덱|미디어 인코더 Premium 워크플로|Azure 미디어 인코더|미디어 인코더 표준
---|---|---|---
AVC(H.264, 8비트, 최대 High Profile, 수준 5.2, 4K Ultra HD, AVC Intra)|예|8비트 4:2:0만 최대 1080p|8비트 4:2:0만
Avid DNxHD(MXF)|예|아니요|아니요
DVCPro/DVCProHD(MXF)|예|아니요|아니요
MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함)|예|아니요|아니요
MPEG-1|예|아니요|아니요
Windows Media 비디오/VC-1|예|예|아니요
JPEG 축소판 그림 만들기|예|예|아니요

출력 오디오 코덱

출력 오디오 코덱|미디어 인코더 Premium 워크플로|Azure 미디어 인코더|미디어 인코더 표준
---|---|---|---
AES(SMPTE 331M 및 302M, AES3-2003)|예|아니요|아니요
Dolby® Digital(AC3)|예|예|아니요
Dolby® Digital Plus(E-AC3) 최대 7.1|예|최대 5.1|아니요
AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1)|예|예|예
MPEG Layer 2|예|아니요|아니요
MP3(MPEG-1 Audio Layer 3)|예|아니요|아니요
Windows Media 오디오|예|예|아니요

##관련된 문서

- [Azure 미디어 서비스의 프리미엄 인코딩 소개](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Azure 미디어 서비스의 프리미엄 인코딩 사용 방법](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [할당량 및 제한 사항](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=August15_HO7-->