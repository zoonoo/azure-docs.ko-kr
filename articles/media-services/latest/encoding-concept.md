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
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: e1fc58db8f933ae122801f492fbbafdb905c7dda
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910399"
---
# <a name="encoding-with-media-services"></a>Media Services를 사용하여 인코딩

Media Services의 용어 인코딩은 디지털 비디오 및/또는 오디오를 포함 하는 파일을 한 표준 형식에서 다른 표준 형식으로 변환 하는 프로세스에 적용 됩니다. 즉, (a) 파일 크기를 줄이고/또는 (b)와 호환 되는 형식을 생성 합니다. 광범위 한 장치 및 응용 프로그램. 이 프로세스를 비디오 압축 또는 트랜스 코딩이 라고도 합니다. 개념에 대 한 자세한 내용은 [데이터 압축](https://en.wikipedia.org/wiki/Data_compression) 및 [인코딩 및 트랜스 코딩](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) (영문)을 참조 하세요.

비디오는 [점진적 다운로드](https://en.wikipedia.org/wiki/Progressive_download) 또는 [적응 비트 전송률 스트리밍을](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)통해 장치와 응용 프로그램에 일반적으로 제공 됩니다. 

* 점진적 다운로드로 제공하기 위해 Azure Media Services를 사용하여 디지털 미디어 파일(메자닌)을 [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) 파일로 변환할 수 있습니다. 여기에는 [H. 264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) 코덱으로 인코딩된 비디오와 [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) 코덱으로 인코딩된 오디오가 포함됩니다. 이 MP4 파일은 저장소 계정의 자산에 기록 됩니다. Azure Storage Api 또는 Sdk (예: [저장소 REST API](../../storage/common/storage-rest-api-auth.md) 또는 [.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md))를 사용 하 여 파일을 직접 다운로드할 수 있습니다. 저장소에서 특정 컨테이너 이름을 사용 하 여 출력 자산을 만든 경우 해당 위치를 사용 합니다. 그렇지 않으면 Media Services를 사용 하 여 [자산 컨테이너 url을 나열할](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)수 있습니다. 
* 적응 비트 전송률 스트리밍을 통해 콘텐츠를 준비 하려면 메자닌 파일을 여러 비트 전송률 (높은 수준에서 낮은 수준)으로 인코딩해야 합니다. 비트 전송률이 낮아질 때 품질의 정상 전환을 보장 하기 위해는 비디오 해상도입니다. 이로 인해이를 위한 인코딩 사다리 (해상도 및 비트 전송률이 [자동 생성 된 적응 비트 전송률 사다리](autogen-bitrate-ladder.md)참조) 라는 테이블이 생성 됩니다. Media Services를 사용 하 여 여러 비트 전송률로 메자닌 파일을 인코딩할 수 있습니다. 이렇게 하면 MP4 파일 집합 및 연결 된 스트리밍 구성 파일이 저장소 계정의 자산에 기록 됩니다. 그런 다음 Media Services의 [동적 패키징](dynamic-packaging-overview.md) 기능을 사용 하 여 [MPEG-대시](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) 및 [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)와 같은 스트리밍 프로토콜을 통해 비디오를 배달할 수 있습니다. 이렇게 하려면 [스트리밍 로케이터](streaming-locators-concept.md) 를 만들고 지원 되는 프로토콜에 해당 하는 스트리밍 url을 작성 해야 합니다 .이 url은 해당 기능에 따라 장치/응용 프로그램으로 전달 될 수 있습니다.

다음 다이어그램에서는 동적 패키징을 사용 하는 주문형 인코딩에 대 한 워크플로를 보여 줍니다.

![동적 패키징](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

이 항목에서는 Media Services v3을 사용하여 콘텐츠를 인코딩하는 방법에 대한 지침을 제공합니다.

## <a name="transforms-and-jobs"></a>변환 및 작업

Media Services v3을 사용하여 인코딩하려면 [변환](https://docs.microsoft.com/rest/api/media/transforms) 및 [작업](https://docs.microsoft.com/rest/api/media/jobs)을 만들어야 합니다. 변환은 인코딩 설정 및 출력에 대 한 조리법을 정의 합니다. 작업은 조리법의 인스턴스입니다. 자세한 내용은 [Transform 및 Job](transforms-jobs-concept.md)을 참조하세요.

Media Services를 사용하여 인코딩하는 경우 미리 설정을 사용하여 입력 미디어 파일을 처리하는 방법을 인코더에 알려줍니다. 예를 들어 인코딩된 콘텐츠에서 원하는 비디오 해상도 및/또는 오디오 채널 수를 지정할 수 있습니다. 

업계 모범 사례에 따라 권장되는 기본 제공 미리 설정 중 하나를 사용하여 빠르게 시작하거나 특정 시나리오 또는 디바이스 요구 사항을 대상으로 지정하는 사용자 지정 미리 설정을 작성할 수 있습니다. 자세한 내용은 [사용자 지정 변환을 사용하여 인코딩](customize-encoder-presets-how-to.md)을 참조하세요. 

2019년 1월부터 Media Encoder Standard로 인코딩하여 MP4 파일을 생성하는 경우 새 .mpi 파일이 생성되고 출력 자산에 추가됩니다. 이 MPI 파일은 [동적 패키징](dynamic-packaging-overview.md) 및 스트리밍 시나리오의 성능을 향상하기 위해 사용합니다.

> [!NOTE]
> MPI 파일을 수정 또는 제거하거나 해당 파일의 존재 여부에 따른 종속성을 서비스에서 사용하지 않아야 합니다.

### <a name="creating-job-input-from-an-https-url"></a>HTTPS URL에서 작업 입력 만들기

비디오를 처리 하는 작업을 제출 하는 경우 입력 비디오를 찾을 수 있는 위치 Media Services 알려 주어 야 합니다. 옵션 중 하나는 HTTPS URL을 작업 입력으로 지정 하는 것입니다. 현재 Media Services v3은 HTTPS Url을 통한 청크 분할 전송 인코딩을 지원 하지 않습니다. 

#### <a name="examples"></a>예

* [.NET을 사용 하 여 HTTPS URL에서 인코딩](stream-files-dotnet-quickstart.md)
* [REST를 사용 하 여 HTTPS URL에서 인코딩](stream-files-tutorial-with-rest.md)
* [CLI를 사용 하 여 HTTPS URL에서 인코딩](stream-files-cli-quickstart.md)
* [Node.js를 사용 하 여 HTTPS URL에서 인코딩](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>로컬 파일에서 작업 입력 만들기

입력 비디오는 Media Service 자산으로 저장할 수 있으며 이 경우 파일(로컬 또는 Azure Blob Storage에 저장됨)을 기반으로 입력 자산을 만듭니다. 

#### <a name="examples"></a>예

[기본 제공 기본 설정을 사용 하 여 로컬 파일 인코딩](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>서브 클립를 사용 하 여 작업 입력 만들기

비디오를 인코딩할 때를 지정 하 여 원본 파일을 자르거나 클리핑 하 고 입력 비디오의 원하는 부분만 포함 하는 출력을 생성할 수 있습니다. 이 기능은 [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 사전 설정 또는 [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 사전 설정을 사용 하 여 작성 된 모든 [변환](https://docs.microsoft.com/rest/api/media/transforms) 에 적용 됩니다. 

주문형 비디오 또는 라이브 보관 (기록 된 이벤트)의 단일 클립을 사용 하 여 [작업](https://docs.microsoft.com/rest/api/media/jobs/create) 을 만들도록 지정할 수 있습니다. 작업 입력은 자산이 나 HTTPS URL 일 수 있습니다.

> [!TIP]
> 비디오를 다시 인코딩하지 않고 비디오의 하위 lip를 스트리밍하려면 동적 패키지를 사용 하는 [미리 필터링 매니페스트](filters-dynamic-manifest-overview.md)를 사용 하는 것이 좋습니다.

#### <a name="examples"></a>예

예를 참조 하세요.

* [.NET을 사용 하 여 비디오 하위 클립](subclip-video-dotnet-howto.md)
* [REST를 사용 하 여 비디오 하위 클립](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>기본 제공 미리 설정

Media Services에서 현재 지원하는 기본 제공 인코딩 미리 설정은 다음과 같습니다.  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)은 표준 인코더로 입력 비디오를 인코딩하는 기본 제공 미리 설정을 지정하는 데 사용됩니다. 

현재 지원되는 미리 설정은 다음과 같습니다.

- **EncoderNamedPreset.AACGoodQualityAudio** - 192kbps로 인코딩된 스테레오 오디오만 포함된 단일 MP4 파일을 생성합니다.
- **EncoderNamedPreset.AdaptiveStreaming**(권장) - 자세한 내용은 [비트 전송률 사다리 자동 생성](autogen-bitrate-ladder.md)을 참조하세요.
- **EncoderNamedPreset ContentAwareEncodingExperimental** -콘텐츠 인식 인코딩에 대 한 실험적 사전 설정을 제공 합니다. 입력 콘텐츠가 지정 된 경우 서비스는 적응 스트리밍을 통해 최적의 계층 수, 적절 한 비트 전송률 및 해상도 설정을 자동으로 결정 하려고 합니다. 기본 알고리즘은 시간이 지남에 따라 지속적으로 발전 합니다. 출력에는 비디오 및 오디오가 인터리브 된 MP4 파일이 포함 됩니다. 자세한 내용은 [콘텐츠 인식 인코딩에 대 한 실험적 사전 설정](cae-experimental.md)을 참조 하세요.
- **EncoderNamedPreset.H264MultipleBitrate1080p** - 스테레오 AAC 오디오 및 6,000~400kbps 범위의 8GOP 정렬 MP4 파일 집합을 생성합니다. 해상도는 1,080p에서 시작하여 360p까지 낮아집니다.
- **EncoderNamedPreset.H264MultipleBitrate720p** - 스테레오 AAC 오디오 및 3,400~400kbps 범위의 6GOP 정렬 MP4 파일 집합을 생성합니다. 해상도는 720p에서 시작하여 360p까지 낮아집니다.
- **EncoderNamedPreset.H264MultipleBitrateSD** - 스테레오 AAC 오디오 및 1,600~400kbps 범위의 5GOP 정렬 MP4 파일 집합을 생성합니다. 해상도는 480p에서 시작하여 360p까지 낮아집니다.
- **EncoderNamedPreset** -6750 kbps 1080의 H264SingleBitrate1080p 코덱 및 픽셀의 그림 높이를 사용 하 여 비디오가 인코딩된 MP4 파일을 생성 하 고, 스테레오 오디오는 64 KBPS에서 AAC-LC 코덱으로 인코딩됩니다.
- **EncoderNamedPreset** -4500 kbps 720의 H264SingleBitrate720p 코덱 및 픽셀의 그림 높이를 사용 하 여 비디오가 인코딩된 MP4 파일을 생성 하 고, 스테레오 오디오는 64 KBPS에서 AAC-LC 코덱으로 인코딩됩니다.
- **EncoderNamedPreset** -2200 kbps 480의 H264SingleBitrateSD 코덱 및 픽셀의 그림 높이를 사용 하 여 비디오가 인코딩된 MP4 파일을 생성 하 고, 스테레오 오디오는 64 KBPS에서 AAC-LC 코덱으로 인코딩됩니다.

최신 기본 설정 목록을 보려면 [비디오 인코딩에 사용할 기본 설정 기본 설정](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)을 참조 하세요.

사전 설정을 사용 하는 방법을 보려면 [파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)을 확인 하세요.

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)은 표준 인코더로 입력 비디오를 인코딩할 때 사용할 설정을 설명합니다. Transform 미리 설정을 사용자 지정할 때 이 미리 설정을 사용합니다. 

#### <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 고려 사항이 적용 됩니다.

- AVC 콘텐츠의 높이 및 너비에 대 한 모든 값은 4의 배수 여야 합니다.
- Azure Media Services v3에서 모든 인코딩 비트 전송률은 초당 비트 단위입니다. 이는 킬로 비트/초를 단위로 사용한 v2 Api와는 다릅니다. 예를 들어 v 2의 비트 전송률이 128 (k b/초)로 지정 된 경우 v3에서 128000 (비트/초)로 설정 됩니다.

### <a name="customizing-presets"></a>사전 설정 사용자 지정

Media Services는 특정 인코딩 필요 및 요구 사항을 충족하기 위해 미리 설정에 포함된 모든 값을 완전히 사용자 지정할 수 있도록 지원합니다. 인코더 사전 설정을 사용자 지정 하는 방법을 보여 주는 예제는 다음을 참조 하세요.

#### <a name="examples"></a>예

- [.NET을 사용 하 여 사전 설정 사용자 지정](customize-encoder-presets-how-to.md)
- [CLI를 사용 하 여 사전 설정 사용자 지정](custom-preset-cli-howto.md)
- [REST를 사용 하 여 사전 설정 사용자 지정](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>미리 설정 된 스키마

Media Services v 3에서 사전 설정은 API 자체에서 강력한 형식의 엔터티입니다. [OPEN API 사양 (또는 Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)에서 이러한 개체에 대 한 "스키마" 정의를 찾을 수 있습니다. [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.net sdk](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) 또는 기타 Media Services v3 sdk 참조 설명서에서 미리 설정 된 정의 (예: **StandardEncoderPreset**)를 볼 수도 있습니다.

## <a name="scaling-encoding-in-v3"></a>v3에서 인코딩 크기 조정

미디어 처리 크기를 조정 하려면 [CLI를 사용 하 여 크기 조정](media-reserved-units-cli-how-to.md)을 참조 하세요.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Media Services를 사용하여 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
* [기본 제공 기본 설정을 사용 하 여 HTTPS URL에서 인코딩](job-input-from-http-how-to.md)
* [기본 제공 기본 설정을 사용 하 여 로컬 파일 인코딩](job-input-from-local-file-how-to.md)
* [특정 시나리오 또는 디바이스 요구 사항을 대상으로 사용자 지정 사전 설정 빌드](customize-encoder-presets-how-to.md)