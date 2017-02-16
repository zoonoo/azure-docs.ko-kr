---
title: "Azure 주문형 미디어 인코더 비교 | Microsoft Docs"
description: "이 항목에서는 **Media Encoder Standard** 및 **Media Encoder Premium Workflow**의 인코딩 기능을 비교합니다."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: dca8087230b261f14e876e49b9b0fb12579b96d0


---

# <a name="comparison-of-azure-on-demand-media-encoders"></a>Azure 주문형 미디어 인코더 비교

이 항목에서는 **Media Encoder Standard** 및 **Media Encoder Premium Workflow**의 인코딩 기능을 비교합니다.

### <a name="a-idbillingabilling-meter-used-by-each-encoder"></a><a id="billing"></a>각 인코더에서 사용되는 요금 청구 기준
| 미디어 프로세서 이름 | 적용 가능한 가격 | 참고 사항 |
| --- | --- | --- |
| **미디어 인코더 표준** |인코더 |인코딩 작업은 [여기][1]의 인코더 열 아래에 지정된 요율을 기준으로, 출력으로 생성된 모든 미디어 파일의 전체 재생 시간(분)에 따라 요금이 부과됩니다. |
| **Media Encoder Premium 워크플로** |프리미엄 인코더 |인코딩 작업은 [여기][1]의 프리미엄 인코더 열 아래에 지정된 요율을 기준으로, 출력으로 생성된 모든 미디어 파일의 전체 재생 시간(분)에 따라 요금이 부과됩니다. |

### <a name="input-containerfile-formats"></a>입력 컨테이너/파일 형식
| 입력 컨테이너/파일 형식 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| Adobe® Flash® F4V |예 |예 |
| MXF/SMPTE 377M |예 |예 |
| GXF |예 |예 |
| MPEG-2 전송 스트림 |예 |예 |
| MPEG-2 프로그램 스트림 |예 |예 |
| MPEG-4/MP4 |예 |예 |
| Windows Media/ASF |예 |예 |
| AVI(압축되지 않은 8비트/10비트) |예 |예 |
| 3GPP/3GPP2 |예 |아니요 |
| 부드러운 스트리밍 파일 형식(PIFF 1.3) |예 |아니요 |
| [DVR-MS(Microsoft Digital Video Recording)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |예 |아니요 |
| Matroska/WebM |예 |아니요 |
| QuickTime(.mov) |예 |아니요 |

### <a name="input-video-codecs"></a>입력 비디오 코덱
| 입력 비디오 코덱 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| AVC 8비트/10비트, 최대 4:2:2, AVCIntra 포함 |8비트 4:2:0 및 4:2:2 |예 |
| Avid DNxHD(MXF) |예 |예 |
| DVCPro/DVCProHD(MXF) |예 |예 |
| JPEG2000 |예 |예 |
| MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함) |최대 422 프로필 |예 |
| MPEG-1 |예 |예 |
| Windows Media 비디오/VC-1 |예 |예 |
| Canopus HQ/HQX |아니요 |아니요 |
| Mpeg-4 2부 |예 |아니요 |
| [Theora](https://en.wikipedia.org/wiki/Theora) |예 |아니요 |
| Apple ProRes 422 |예 |아니요 |
| Apple ProRes 422 LT |예 |아니요 |
| Apple ProRes 422 HQ |예 |아니요 |
| Apple ProRes Proxy |예 |아니요 |
| Apple ProRes 4444 |예 |아니요 |
| Apple ProRes 4444 XQ |예 |아니요 |

### <a name="input-audio-codecs"></a>입력 오디오 코덱
| 입력 오디오 코덱 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| AES(SMPTE 331M 및 302M, AES3-2003) |아니요 |예 |
| Dolby® E |아니요 |예 |
| Dolby® Digital(AC3) |아니요 |예 |
| Dolby® Digital Plus(E-AC3) |아니요 |예 |
| AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1) |예 |예 |
| MPEG Layer 2 |예 |예 |
| MP3(MPEG-1 Audio Layer 3) |예 |예 |
| Windows Media 오디오 |예 |예 |
| WAV/PCM |예 |예 |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |예 |아니요 |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |예 |아니요 |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |예 |아니요 |

### <a name="output-containerfile-formats"></a>출력 컨테이너/파일 형식
| 출력 컨테이너/파일 형식 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| Adobe® Flash® F4V |아니요 |예 |
| MXF(OP1a, XDCAM 및 AS02) |아니요 |예 |
| DPP(AS11 포함) |아니요 |예 |
| GXF |아니요 |예 |
| MPEG-4/MP4 |예 |예 |
| MPEG-TS |예 |예 |
| Windows Media/ASF |아니요 |예 |
| AVI(압축되지 않은 8비트/10비트) |아니요 |예 |
| 부드러운 스트리밍 파일 형식(PIFF 1.3) |아니요 |예 |

### <a name="output-video-codecs"></a>출력 비디오 코덱
| 출력 비디오 코덱 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| AVC(H.264, 8비트, 최대 High Profile, 수준 5.2, 4K Ultra HD, AVC Intra) |8비트 4:2:0만 |예 |
| Avid DNxHD(MXF) |아니요 |예 |
| MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함) |아니요 |예 |
| MPEG-1 |아니요 |예 |
| Windows Media 비디오/VC-1 |아니요 |예 |
| JPEG 축소판 그림 만들기 |아니요 |예 |

### <a name="output-audio-codecs"></a>출력 오디오 코덱
| 출력 오디오 코덱 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| AES(SMPTE 331M 및 302M, AES3-2003) |아니요 |예 |
| Dolby® Digital(AC3) |아니요 |예 |
| Dolby® Digital Plus(E-AC3) 최대 7.1 |아니요 |예 |
| AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1) |예 |예 |
| MPEG Layer 2 |아니요 |예 |
| MP3(MPEG-1 Audio Layer 3) |아니요 |예 |
| Windows Media 오디오 |아니요 |예 |


## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>관련 문서
* [미디어 인코더 표준 사전 설정을 사용자 지정하여 고급 인코딩 작업 수행](media-services-custom-mes-presets-with-dotnet.md)
* [할당량 및 제한 사항](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/



<!--HONumber=Jan17_HO2-->


