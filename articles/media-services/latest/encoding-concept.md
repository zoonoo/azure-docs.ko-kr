---
title: Media Services를 사용하여 클라우드에서 인코딩 - Azure | Microsoft Docs
description: 이 항목에서는 Azure Media Services를 사용할 때의 인코딩 프로세스에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 25b3209bed98ea217db9e414caa6f08cee6d8c89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761903"
---
# <a name="encoding-with-media-services"></a>Media Services를 사용하여 인코딩

디지털 비디오 및/또는 다른 파일의 크기를 줄이면 하는 (a) 및/또는 (b)와 호환 되는 형식을 생성 하기 위한 목적으로 하나의 표준 형식에서 오디오를 포함 하는 파일을 변환 하는 프로세스를 적용할 Media Services의 encoding 용어는 다양 한 장치 및 응용 프로그램입니다. 이 프로세스는 비디오 압축 또는 트랜스 코딩 라고도 합니다. 참조를 [데이터 압축](https://en.wikipedia.org/wiki/Data_compression) 하며 [인코딩 및 트랜스 코딩 란?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) 개념에 대 한 자세한 내용은 합니다.

비디오 장치 및 응용 프로그램에 일반적으로 전달할지 [점진적 다운로드](https://en.wikipedia.org/wiki/Progressive_download) 를 통해 [적응 비트 전송률 스트리밍을](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)합니다. 

* 점진적 다운로드를 통해 전달할 변환할 Azure Media Services를 사용할 수는 디지털 미디어 파일 (mezzanine)에 [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) 사용 하 여 인코딩된 비디오를 포함 하는 파일을 [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) 코덱, 및 사용 하 여 인코딩된 오디오 합니다 [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) 코덱 합니다. 이 MP4 파일 자산 저장소 계정에 기록 됩니다. Azure Storage Api 또는 Sdk를 사용할 수 있습니다 (예를 들어 [Storage REST API](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), 또는 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) 파일을 직접 다운로드. 출력을 만들 경우 해당 위치를 사용 하는 저장소에서 관련 컨테이너 이름의 자산입니다. 그렇지 않은 경우 Media Services를 사용할 수 있습니다 [자산 컨테이너 Url을 나열할](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)합니다. 
* 콘텐츠 배달에 대 한 적응 비트 전송률 스트리밍을 통해를 준비 하려면 mezzanine 파일 (높음-낮음) 여러 전송률로 인코딩할 수 해야 합니다. 비트 전송률을 줄일 수으로 품질의 정상적인 전환 되도록 따라서 비디오의 해상도가입니다. 소위 인코딩 사다리 – 해상도 및 비트 전송률의 테이블에에서이 인해 (참조 [자동으로 생성 된 적응 비트 전송률 사다리](autogen-bitrate-ladder.md)). Media Services를 사용 하 여이 과정에서 – 여러 전송률로 mezzanine 파일을, 저장소 계정에 자산을 쓸 스트리밍 관련된 구성 파일, 및 MP4 파일 집합을 얻게 됩니다. 사용할 수 있습니다는 [동적 패키징](dynamic-packaging-overview.md) 같은 스트리밍 프로토콜을 통해 비디오를 배달 하는 Media Services의 기능 [MPEG DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) 하 고 [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)합니다. 작성 해야이 [스트리밍 로케이터](streaming-locators-concept.md) 빌드하고 스트리밍 Url을 다음 전달 될 수 있습니다 해제 장치/응용 프로그램의 기능을 기반으로 하는 지원 되는 프로토콜에 해당 합니다.

다음 다이어그램에서는 주문형으로 동적 패키징과 인코딩 워크플로 보여 줍니다.

![동적 패키징](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

이 항목에서는 Media Services v3을 사용하여 콘텐츠를 인코딩하는 방법에 대한 지침을 제공합니다.

## <a name="transforms-and-jobs"></a>변환 및 작업

Media Services v3을 사용하여 인코딩하려면 [변환](https://docs.microsoft.com/rest/api/media/transforms) 및 [작업](https://docs.microsoft.com/rest/api/media/jobs)을 만들어야 합니다. 인코딩 설정 및 출력;에 대 한는 레시피를 정의 하는 변환 작업에는 레시피의 인스턴스입니다. 자세한 내용은 [Transform 및 Job](transforms-jobs-concept.md)을 참조하세요.

Media Services를 사용하여 인코딩하는 경우 미리 설정을 사용하여 입력 미디어 파일을 처리하는 방법을 인코더에 알려줍니다. 예를 들어 인코딩된 콘텐츠에서 원하는 비디오 해상도 및/또는 오디오 채널 수를 지정할 수 있습니다. 

업계 모범 사례에 따라 권장되는 기본 제공 미리 설정 중 하나를 사용하여 빠르게 시작하거나 특정 시나리오 또는 디바이스 요구 사항을 대상으로 지정하는 사용자 지정 미리 설정을 작성할 수 있습니다. 자세한 내용은 [사용자 지정 변환을 사용하여 인코딩](customize-encoder-presets-how-to.md)을 참조하세요. 

2019년 1월부터 Media Encoder Standard로 인코딩하여 MP4 파일을 생성하는 경우 새 .mpi 파일이 생성되고 출력 자산에 추가됩니다. 이 MPI 파일은 [동적 패키징](dynamic-packaging-overview.md) 및 스트리밍 시나리오의 성능을 향상하기 위해 사용합니다.

> [!NOTE]
> MPI 파일을 수정 또는 제거하거나 해당 파일의 존재 여부에 따른 종속성을 서비스에서 사용하지 않아야 합니다.

## <a name="built-in-presets"></a>기본 제공 미리 설정

Media Services에서 현재 지원하는 기본 제공 인코딩 미리 설정은 다음과 같습니다.  

### <a name="builtinstandardencoderpreset-preset"></a>BuiltInStandardEncoderPreset 미리 설정

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)은 표준 인코더로 입력 비디오를 인코딩하는 기본 제공 미리 설정을 지정하는 데 사용됩니다. 

현재 지원되는 미리 설정은 다음과 같습니다.

- **EncoderNamedPreset.AACGoodQualityAudio** - 192kbps로 인코딩된 스테레오 오디오만 포함된 단일 MP4 파일을 생성합니다.
- **EncoderNamedPreset.AdaptiveStreaming**(권장) - 자세한 내용은 [비트 전송률 사다리 자동 생성](autogen-bitrate-ladder.md)을 참조하세요.
- **EncoderNamedPreset.ContentAwareEncodingExperimental** -콘텐츠 인식 인코딩에 실험적 미리 설정을 노출 합니다. 서비스 입력된 내용을 지정 되 면 자동으로 적응 스트리밍을 통해 계층, 적절 한 비트 전송률 및 해상도 설정을 배달에 대 한 최적 수를 확인 하려고 합니다. 기본 알고리즘은 시간이 지나면서 계속 됩니다. 출력은 비디오 및 인터리브 오디오로 MP4 파일에 포함 됩니다. 자세한 내용은 [콘텐츠를 인식 하는 인코딩 사전 설정을 실험적](cae-experimental.md)합니다.
- **EncoderNamedPreset.H264MultipleBitrate1080p** - 스테레오 AAC 오디오 및 6,000~400kbps 범위의 8GOP 정렬 MP4 파일 집합을 생성합니다. 해상도는 1,080p에서 시작하여 360p까지 낮아집니다.
- **EncoderNamedPreset.H264MultipleBitrate720p** - 스테레오 AAC 오디오 및 3,400~400kbps 범위의 6GOP 정렬 MP4 파일 집합을 생성합니다. 해상도는 720p에서 시작하여 360p까지 낮아집니다.
- **EncoderNamedPreset.H264MultipleBitrateSD** - 스테레오 AAC 오디오 및 1,600~400kbps 범위의 5GOP 정렬 MP4 파일 집합을 생성합니다. 해상도는 480p에서 시작하여 360p까지 낮아집니다.
- **EncoderNamedPreset.H264SingleBitrate1080p** -비디오 6750kbps 및 1080 픽셀의 그림 높이 H.264 코덱으로 인코딩된 않았고 64kbps AAC-LC 코덱으로 인코딩된 스테레오 오디오 MP4 파일을 생성 합니다.
- **EncoderNamedPreset.H264SingleBitrate720p** -비디오 4500kbps, 그림 높이 720 픽셀에서 H.264 코덱으로 인코딩된 않았고 64kbps AAC-LC 코덱으로 인코딩된 스테레오 오디오 MP4 파일을 생성 합니다.
- **EncoderNamedPreset.H264SingleBitrateSD** -비디오 2200kbps 및 그림 높이는 480 픽셀 H.264 코덱으로 인코딩된 않았고 64kbps AAC-LC 코덱으로 인코딩된 스테레오 오디오 MP4 파일을 생성 합니다.

최신 사전 설정 목록은 참조 하세요 [비디오를 encoding에 사용 되는 기본 제공 사전 설정](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)합니다.

기본 설정을 사용 하는 방법을 보려면, 체크 아웃 [업로드, 인코딩 및 스트리밍 파일](stream-files-tutorial-with-api.md)합니다.

### <a name="standardencoderpreset-preset"></a>StandardEncoderPreset 미리 설정

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)은 표준 인코더로 입력 비디오를 인코딩할 때 사용할 설정을 설명합니다. Transform 미리 설정을 사용자 지정할 때 이 미리 설정을 사용합니다. 

#### <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 사항을 고려해 야 합니다.

- 높이 너비 AVC 내용에 대 한 모든 값은 4의 배수 여야 합니다.
- Azure Media Services v3에서는 인코딩 비트 전송률 모두 비트 / 초입니다. 이 킬로 비트/초 단위로 v2 Api 사용 하 여 사전 설정에서 다릅니다. 예를 들어 v2의 비트 전송률 (k b/초) 128로 지정 된, 경우 v3에서 것은에 설정할 128000 (비트/초)입니다.

#### <a name="examples"></a>예

Media Services는 특정 인코딩 필요 및 요구 사항을 충족하기 위해 미리 설정에 포함된 모든 값을 완전히 사용자 지정할 수 있도록 지원합니다. 인코더 사전 설정을 사용자 지정 하는 방법을 보여 주는 예제를 참조 하세요.

- [.NET을 사용 하 여 사전 설정 사용자 지정](customize-encoder-presets-how-to.md)
- [CLI 사용 하 여 사전 설정 사용자 지정](custom-preset-cli-howto.md)
- [REST 사용 하 여 사전 설정 사용자 지정](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>기본 설정된 스키마

Media Services v3에서는 사전 설정에는 API 자체의 강력한 형식의 엔터티. 이러한 개체에 대 한 "schema" 정의 찾을 수 있습니다 [Open API Specification (또는 Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)합니다. 미리 설정 된 정의 볼 수도 있습니다 (같은 **StandardEncoderPreset**)에 [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)에 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (또는 다른 Media Services v3 SDK 참조 설명서).

## <a name="scaling-encoding-in-v3"></a>v3에서 인코딩 크기 조정

미디어 처리 크기를 조정 하려면 참조 [CLI 사용 하 여 Scale](media-reserved-units-cli-how-to.md)합니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [기본 제공 사전 설정을 사용 하는 HTTPS URL에서 인코딩](job-input-from-http-how-to.md)
* [기본 제공 사전 설정을 사용 하 여 로컬 파일 인코딩](job-input-from-local-file-how-to.md)
* [특정 시나리오 또는 장치 요구 사항을 대상으로 사전 설정 사용자 지정 빌드](customize-encoder-presets-how-to.md)
* [Media Services를 사용하여 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
