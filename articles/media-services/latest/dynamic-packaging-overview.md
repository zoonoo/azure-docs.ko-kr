---
title: Azure Media Services 동적 패키징 개요 | Microsoft Docs
description: 이 문서에서는 Azure Media Services의 동적 패키징에 대해 간략하게 설명합니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 4836ec4bb66bbf8ced921dd1095665d004f8a28b
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542585"
---
# <a name="dynamic-packaging"></a>동적 패키징

Microsoft Azure Media Services를 사용하여 많은 미디어 소스 파일 형식, 미디어 스트리밍 형식 및 콘텐츠 보호 형식을 다양한 클라이언트 기술(예: iOS, XBOX)에 제공할 수 있습니다. 이러한 클라이언트는 여러 가지 프로토콜을 이해합니다. 예를 들어 iOS에는 HLS(HTTP 라이브 스트리밍) 형식이 필요하고, Xbox에는 부드러운 스트리밍이 필요합니다. HLS, MPEG DASH 또는 부드러운 스트리밍을 이해하는 클라이언트에 제공하려는 적응 비트 전송률(다중 비트 전송률) MP4(ISO 기본 미디어 14496-12) 파일 세트나 적응 비트 전송률 부드러운 스트리밍 파일 세트가 있는 경우 *동적 패키징*을 이용할 수 있습니다. 패키징은 비디오 해상도에 무관하게 SD/HD/UHD-4K를 지원합니다.

Media Services에서 [스트리밍 엔드포인트](streaming-endpoint-concept.md)는 일반적인 스트리밍 미디어 프로토콜(HLS 또는 DASH)을 사용하여 라이브 및 주문형 콘텐츠를 클라이언트 플레이어 애플리케이션에 직접 배달할 수 있는 원본 서비스와 동적(적시) 패키징을 나타냅니다. 동적 패키징은 모든 **스트리밍 엔드포인트**(표준 또는 프리미엄)에 표준으로 제공되는 기능입니다. 

동적 패키징을 활용하려면 Media Services 동적 패키징에 필요한 적응 비트 전송률 MP4 파일 및 스트리밍 구성 파일 세트가 포함된 **자산**이 있어야 합니다. 파일을 얻는 한 가지 방법은 Media Services를 사용하여 mezzanine(원본) 파일을 인코딩하는 것입니다. 인코딩된 자산의 비디오를 재생할 클라이언트에 사용할 수 있게 만들려면 **스트리밍 로케이터**를 만들고 스트리밍 URL을 빌드해야 합니다. 그러면 스트리밍 클라이언트 매니페스트에 지정된 형식에 따라(HLS, DASH 또는 부드러운 스트리밍) 선택한 프로토콜로 스트림을 받게 됩니다.

따라서 사용자는 단일 저장소 형식으로 파일을 저장하고 해당 파일에 대한 요금을 지불하기만 하면 되며, Media Services 서비스에서 클라이언트의 요청에 따라 적절한 응답을 작성하고 제공합니다. 

Media Services에서는 라이브 또는 주문형 스트리밍 여부에 무관하게 동적 패키징이 사용됩니다. 

> [!NOTE]
> 현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) 또는 지원되는 [SDK](media-services-apis-overview.md#sdks) 중 하나를 사용하세요.

## <a name="on-demand-streaming-workflow"></a>주문형 스트리밍 워크플로

동적 패키징을 사용한 Media Services 주문형 스트리밍의 일반적인 워크플로는 다음과 같습니다.

1. 입력 또는 원본 파일(*mezzanine* 파일이라고 함)을 업로드합니다. MP4, MOV 또는 MXF 파일을 예로 들 수 있습니다. 
1. mezzanine 파일을 H.264 MP4 적응 비트 전송률 집합으로 인코딩합니다. 
1. 적응 비트 전송률 MP4 세트가 포함된 자산을 게시합니다. 스트리밍 로케이터를 만들어 게시합니다.
1. 다양한 형식(HLS, MPEG-DASH 및 부드러운 스트리밍)을 대상으로 하는 URL을 작성합니다. 스트리밍 엔드포인트는 다양한 형식에 대한 정확한 매니페스트 및 요청의 서비스를 담당합니다.

이 다이어그램은 동적 패키징을 사용한 주문형 스트리밍의 워크플로를 보여 줍니다.

![패키징을 사용한 주문형 스트리밍의 워크플로 다이어그램](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="live-streaming-workflow"></a>라이브 스트리밍 워크플로

라이브 이벤트는 통과 또는 라이브 인코딩의 두 가지 형식 중 하나일 수 있습니다. 

동적 패키징을 사용한 라이브 스트리밍의 일반적인 워크플로는 다음과 같습니다.

1. [라이브 이벤트](live-events-outputs-concept.md)를 만듭니다.
1. 수집 URL을 가져오고, URL을 사용하여 기여 피드를 보내도록 온-프레미스 인코더를 구성합니다.
1. 미리 보기 URL을 가져와서 인코더의 입력이 수신되고 있는지 확인하는 데 사용합니다.
1. 새 자산을 만듭니다.
1. 라이브 출력을 만들고 만든 자산 이름을 사용합니다.<br />라이브 출력은 스트림을 자산에 보관합니다.
1. 스트리밍 정책 유형이 기본 제공된 스트리밍 로케이터를 만듭니다.<br />콘텐츠를 암호화하려면 [콘텐츠 보호 개요](content-protection-overview.md)를 검토하세요.
1. 스트리밍 로케이터에서 경로를 나열하여 사용할 URL을 가져옵니다.
1. 스트림을 내보낼 스트리밍 엔드포인트의 호스트 이름을 가져옵니다.
1. 다양한 형식(HLS, MPEG-DASH 및 부드러운 스트리밍)을 대상으로 하는 URL을 작성합니다. 스트리밍 엔드포인트는 다양한 형식에 대한 정확한 매니페스트 및 요청의 서비스를 담당합니다.

이 다이어그램은 동적 패키징을 사용한 라이브 스트리밍의 워크플로를 보여 줍니다.

![동적 패키징을 사용한 통과 인코딩의 워크플로 다이어그램](./media/live-streaming/pass-through.svg)

Media Services v3의 라이브 스트리밍에 대 한 정보는 [라이브 스트리밍 개요](live-streaming-overview.md)를 참조하세요.

## <a name="delivery-protocols"></a>배달 프로토콜

Media Services 동적 패키징에서 콘텐츠에 다음 배달 프로토콜을 사용할 수 있습니다.

|프로토콜|예|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|부드러운 스트리밍| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="encode-to-adaptive-bitrate-mp4s"></a>적응 비트 전송률 MP4를 인코딩합니다.

다음 문서에서는 [Media Services를 사용하여 비디오를 인코딩하는 방법](encoding-concept.md)의 예제를 보여 줍니다.

* [기본 제공 사전 설정을 사용하여 HTTPS URL에서 인코딩](job-input-from-http-how-to.md)
* [기본 제공 사전 설정을 사용하여 로컬 파일 인코딩](job-input-from-local-file-how-to.md)
* [특정 시나리오 또는 디바이스 요구 사항을 대상으로 사용자 지정 사전 설정 빌드](customize-encoder-presets-how-to.md)

Media Encoder Standard [형식 및 코덱](media-encoder-standard-formats.md) 목록을 참조하세요.

## <a name="video-codecs"></a>비디오 코덱

동적 패키징에서는 다음 비디오 코덱을 지원합니다.
* [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC)(MPEG-4 AVC 또는 AVC1) 또는 [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding)(HEVC, hev1 또는 hvc1)로 인코딩된 비디오를 포함하는 MP4 파일

## <a name="audio-codecs"></a>오디오 코덱

동적 패키징에서는 다음 오디오 프로토콜을 지원합니다.
* MP4 파일
* 다중 오디오 트랙

동적 패키징은 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital)(AC3) 오디오(레거시 코덱임)를 포함하는 파일을 지원하지 않습니다.

### <a name="mp4-files"></a>MP4 파일

동적 패키징에서는 다음 프로토콜을 사용하여 인코딩된 오디오를 포함하는 MP4 파일을 지원합니다. 

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)(AAC-LC, HE-AAC v1 또는 HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(향상된 AC-3 또는 E-AC3)
* Dolby Atmos<br />
   Dolby Atmos 콘텐츠 스트리밍은 CSF(Common Streaming Format) 또는 CMAF(Common Media Application Format) 조각화된 MP4 및 CMAF와의 HLS(HTTP Live Streaming)를 통해 MPEG-DASH 프로토콜과 같은 표준에서 지원됩니다.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DASH CSF, CMAF DASH, HLS-M2TS 및 HLS-CMAF 패키징 형식에서 지원되는 DTS 코덱은 다음과 같습니다.  

    * DTS 디지털 서라운드(dtsc)
    * DTS-HD 고해상도 및 DTS-HD 마스터 오디오(dtsh)
    * DTS Express(dtse)
    * DTS-HD 무손실(코어 없음) (dtsl)

### <a name="multiple-audio-tracks"></a>다중 오디오 트랙

동적 패키징은 여러 코덱 및 언어가 있는 여러 오디오 트랙을 갖는 스트리밍 자산에 대한 HLS 출력(버전 4 이상)에 다중 오디오 트랙을 지원합니다.

## <a name="dynamic-encryption"></a>동적 암호화

*동적 암호화*를 사용하여 AES-128 또는 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나에서 라이브 또는 주문형 콘텐츠를 동적으로 암호화할 수 있습니다. 동적 암호화된 라이브 콘텐츠 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에 AES 키 및 DRM 라이선스를 전달하는 서비스를 제공합니다. 자세한 정보는 [동적 암호화](content-protection-overview.md)를 참조하세요.

## <a name="manifest-examples"></a>매니페스트 예제 
 
Media Services 동적 패키징에서 HLS, MPEG-DASH 및 부드러운 스트리밍에 대한 스트리밍 클라이언트 매니페스트는 URL의 형식 선택기에 따라 동적으로 생성됩니다. 자세한 정보는 [배달 프로토콜](#delivery-protocols)을 참조하세요. 

매니페스트 파일에는 트랙 유형(오디오, 비디오 또는 텍스트), 트랙 이름, 시작 및 종료 시간, 비트 전송률(품질), 트랙 언어, 프레젠테이션 창(고정 기간의 슬라이딩 창), 비디오 코덱(FourCC) 등의 스트리밍 메타 데이터가 포함됩니다. 또한 다음으로 재생할 수 있는 비디오 조각 및 위치에 대한 정보를 제공하여 다음 조각을 검색하도록 플레이어에 지시합니다. 조각(또는 세그먼트)은 비디오 콘텐츠의 실제 “청크”입니다.

### <a name="hls"></a>HLS

HLS 마스터 재생 목록이라고도 하는 HLS 매니페스트 파일의 예제는 다음과 같습니다. 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="mpeg-dash"></a>MPEG-DASH

MPD(MPEG-DASH Media Presentation Description)라고도 하는 MPEG-DASH 매니페스트 파일의 예제는 다음과 같습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
### <a name="smooth-streaming"></a>부드러운 스트리밍

부드러운 스트리밍 매니페스트 파일의 예제는 다음과 같습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="dynamic-manifest"></a>동적 매니페스트

플레이어에 전송되는 트랙 수, 형식, 비트 전송률 및 프레젠테이션 창을 제어하기 위해 Media Services 동적 패키지 작성 도구에서 동적 필터링을 사용할 수 있습니다. 자세한 내용은 [동적 패키지 작성 도구를 사용하여 매니페스트 미리 필터링](filters-dynamic-manifest-overview.md)을 참조하세요.

## <a name="more-information"></a>자세한 정보

[Azure Media Services 커뮤니티](media-services-community.md)에서 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 받을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md) 방법에 대해 알아봅니다.

