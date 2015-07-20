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
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#Azure 미디어 서비스로 주문형 콘텐츠 인코딩

이 항목은 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md)의 일부입니다.

##개요

미디어 서비스는 다음 인코더를 지원합니다.

- [Azure 미디어 인코더](#azure_media_encoder)
- [미디어 인코더 Premium 워크플로](#media_encoder_premium_workflow)(공개 미리 보기)

[다음 섹션](#compare_encoders)에서는 두 모두 인코더의 인코딩 기능을 비교합니다.

기본적으로 각 미디어 서비스 계정은 한번에 하나의 인코딩 작업을 활성화할 수 있습니다. 구입한 각 예약 인코딩에 대해 하나씩, 여러 인코딩 작업을 동시에 실행할 수 있는 인코딩 단위를 예약할 수 있습니다. 인코딩 단위 크기를 조정하는 방법에 대한 내용은 **포털** 및 **.NET** 항목을 참조하세요.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="azure_media_encoder"></a>Azure 미디어 인코더

[미디어 서비스 인코더에서 지원하는 형식](media-services-azure-media-encoder-formats.md) – **Azure 미디어 인코더**에서 지원하는 파일 및 스트림 형식에 대해 설명합니다.

**Azure 미디어 인코더**는 [여기](https://msdn.microsoft.com/library/azure/dn619392.aspx)에서 설명한 인코더 기본 설정 중 하나를 사용하여 구성됩니다. 실제 Azure 미디어 인코더 기본 설정 파일을 [여기](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder)에서 얻을 수도 있습니다.

**Azure 관리 포털**, **.NET** 또는 **REST API**를 사용하여 **Azure 미디어 인코더**로 인코딩합니다.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####다른 관련된 항목

[동적 패키징](https://msdn.microsoft.com/library/azure/jj889436.aspx) – 적응 비트 전송률 MP4로 인코딩하여 부드러운 스트리밍, Apple HLS 또는 MPEG-DASH를 동적으로 제공하는 방법을 설명합니다.

[미디어 서비스 인코더 출력 파일 이름을 제어](https://msdn.microsoft.com/library/azure/dn303341.aspx) – Azure 미디어 인코더에서 사용된 파일 명명 규칙 및 출력 파일 이름을 수정하는 방법을 설명합니다.

[Dolby Digital Plus를 사용하여 미디어 인코딩](media-services-encode-with-dolby-digital-plus.md) – Dolby Digital Plus 인코딩을 사용하여 오디오 트랙을 인코딩하는 방법을 설명합니다.


##<a id="media_encoder_premium_wokrflow"></a>미디어 인코더 Premium 워크플로(공개 미리 보기)

**참고** 중국에서는 이 항목에서 설명하는 미디어 인코더 Premium 워크플로 미디어 프로세서를 사용할 수 없습니다.

[미디어 인코더 Premium 워크플로에서 지원하는 형식](media-services-premium-workflow-encoder-formats.md) – **미디어 인코더 Premium 워크플로**에서 지원하는 파일 형식 및 코덱에 대해 설명합니다.

**미디어 인코더 Premium 워크플로**는 복잡한 워크플로를 사용하여 구성됩니다. 워크플로 파일은 [Workflow Designer](media-services-workflow-designer.md) 도구를 사용하여 만들 수 있습니다.

[여기](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)에서 기본 워크플로 파일을 가져올 수 있습니다. 폴더에는 이러한 파일에 대한 설명도 포함되어 있습니다.

**.NET**을 사용하여 **미디어 인코더 Premium 워크플로**로 인코딩합니다. 자세한 내용은 [미디어 인코더 Premium 워크플로를 사용한 고급 인코딩](media-services-encode-with-premium-workflow.md)을 참조하세요.
 

##<a id="compare_encoders"></a>인코딩 비교

이 섹션에서는 **Azure 미디어 인코더** and **미디어 인코더 Premium 워크플로**의 인코딩 기술을 비교합니다.

###입력 형식

입력 컨테이너/파일 형식

<table border="1">
<tr><th>입력 컨테이너/파일 형식</th><th>미디어 인코더 Premium 워크플로</th><th>Azure 미디어 인코더
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>예</td><td>아니요</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>예</td><td>제한</td></tr>
<tr><td>GXF</td><td>예</td><td>아니요</td></tr>
<tr><td>MPEG-2 전송 스트림</td><td>예</td><td>예</td></tr>
<tr><td>MPEG-2 프로그램 스트림</td><td>예</td><td>예</td></tr>
<tr><td>MPEG-4/MP4</td><td>예</td><td>예</td></tr>
<tr><td>Windows Media/ASF</td><td>예</td><td>예</td></tr>
<tr><td>AVI(압축되지 않은 8비트/10비트)</td><td>예</td><td>예</td></tr>
<tr><td>3GPP/3GPP2</td><td>아니요</td><td>예</td></tr>
<tr><td>부드러운 스트리밍 파일 형식(PIFF 1.3)</td><td>아니요</td><td>예</td></tr>
</table>

입력 비디오 코덱

<table border="1">
<tr><th>입력 비디오 코덱</th><th>미디어 인코더 Premium 워크플로</th><th>Azure 미디어 인코더
</th></tr>
<tr><td>AVC 8비트/10비트, 최대 4:2:2, AVCIntra 포함</td><td>예</td><td>8비트 4:2:0만</td></tr>
<tr><td>Avid DNxHD(MXF)</td><td>예</td><td>아니요</td></tr>
<tr><td>DVCPro/DVCProHD(MXF)</td><td>예</td><td>아니요</td></tr>
<tr><td>JPEG2000</td><td>예</td><td>아니요</td></tr>
<tr><td>MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함)</td><td>예</td><td>최대 422 프로필</td></tr>
<tr><td>MPEG-1</td><td>예</td><td>예</td></tr>
<tr><td>Windows Media 비디오/VC-1</td><td>예</td><td>예</td></tr>
<tr><td>Canopus HQ/HQX</td><td>아니요</td><td>예</td></tr>
</table>

입력 오디오 코덱

<table border="1">
<tr><th>입력 오디오 코덱</th><th>미디어 인코더 Premium 워크플로</th><th>Azure 미디어 인코더
</th></tr>
<tr><td>AES(SMPTE 331M 및 302M, AES3-2003)</td><td>예</td><td>아니요</td></tr>
<tr><td>Dolby® E</td><td>예</td><td>아니요</td></tr>
<tr><td>Dolby® Digital(AC3)</td><td>예</td><td>예</td></tr>
<tr><td>Dolby® Digital Plus(E-AC3)</td><td>예</td><td>아니요</td></tr>
<tr><td>AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1)</td><td>예</td><td>예</td></tr>
<tr><td>MPEG Layer 2</td><td>예</td><td>예</td></tr>
<tr><td>MP3(MPEG-1 Audio Layer 3)</td><td>예</td><td>예</td></tr>
<tr><td>Windows Media 오디오</td><td>예</td><td>예</td></tr>
<tr><td>WAV/PCM</td><td>예</td><td>예</td></tr>
</table>

###출력 형식

출력 컨테이너/파일 형식

<table border="1">
<tr><th>출력 컨테이너/파일 형식</th><th>미디어 인코더 Premium 워크플로</th><th>Azure 미디어 인코더
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>예</td><td>아니요</td></tr>
<tr><td>MXF(OP1a, XDCAM 및 AS02)</td><td>예</td><td>아니요</td></tr>
<tr><td>DPP(AS11 포함)</td><td>예</td><td>아니요</td></tr>
<tr><td>GXF</td><td>예</td><td>아니요</td></tr>
<tr><td>MPEG-4/MP4</td><td>예</td><td>예</td></tr>
<tr><td>Windows Media/ASF</td><td>예</td><td>예</td></tr>
<tr><td>AVI(압축되지 않은 8비트/10비트)</td><td>예</td><td>아니요</td></tr>
<tr><td>부드러운 스트리밍 파일 형식(PIFF 1.3)</td><td>예</td><td>예</td></tr>
</table>

출력 비디오 코덱

<table border="1">
<tr><th>출력 비디오 코덱</th><th>미디어 인코더 Premium 워크플로</th><th>Azure 미디어 인코더
</th></tr>
<tr><td>AVC(H.264, 8비트, 최대 High Profile, 수준 5.2, 4K Ultra HD, AVC Intra)</td><td>예</td><td>8비트 4:2:0만 최대 1080p</td></tr>
<tr><td>Avid DNxHD(MXF)</td><td>예</td><td>아니요</td></tr>
<tr><td>DVCPro/DVCProHD(MXF)</td><td>예</td><td>아니요</td></tr>
<tr><td>MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함)</td><td>예</td><td>아니요</td></tr>
<tr><td>MPEG-1</td><td>예</td><td>아니요</td></tr>
<tr><td>Windows Media 비디오/VC-1</td><td>예</td><td>예</td></tr>
<tr><td>JPEG 축소판 그림 만들기</td><td>예</td><td>예</td></tr>
</table>

출력 오디오 코덱

<table border="1">
<tr><th>출력 오디오 코덱</th><th>미디어 인코더 Premium 워크플로</th><th>Azure 미디어 인코더
</th></tr>
<tr><td>AES(SMPTE 331M 및 302M, AES3-2003)</td><td>예</td><td>아니요</td></tr>
<tr><td>Dolby® Digital(AC3)</td><td>예</td><td>예</td></tr>
<tr><td>Dolby® Digital Plus(E-AC3) 최대 7.1</td><td>예</td><td>최대 5.1</td></tr>
<tr><td>AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1)</td><td>예</td><td>예</td></tr>
<tr><td>MPEG Layer 2</td><td>예</td><td>아니요</td></tr>
<tr><td>MP3(MPEG-1 Audio Layer 3)</td><td>예</td><td>아니요</td></tr>
<tr><td>Windows Media 오디오</td><td>예</td><td>예</td></tr>
</table>
##관련된 문서

- [Azure 미디어 서비스의 프리미엄 인코딩 소개](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Azure 미디어 서비스의 프리미엄 인코딩 사용 방법](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [할당량 및 제한 사항](media-services-quotas-and-limitations.md)

 

<!---HONumber=July15_HO2-->