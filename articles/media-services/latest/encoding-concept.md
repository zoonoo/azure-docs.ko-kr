---
title: 미디어 서비스를 통해 비디오 및 오디오 인코딩
titleSuffix: Azure Media Services
description: 이 문서에서는 Azure Media Services로 비디오 및 오디오인코딩에 대해 설명합니다.
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
ms.openlocfilehash: 6e1c9aa5c2e049d5fc1ebd8bf745417f56d232ec
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366573"
---
# <a name="encoding-video-and-audio-with-media-services"></a>미디어 서비스를 통해 비디오 및 오디오 인코딩

Media Services의 인코딩이라는 용어는 (a) 파일 크기를 줄이고/또는 (b) 광범위한 장치 및 앱과 호환되는 형식을 생성하는 목적으로 디지털 비디오 및/또는 오디오가 포함된 파일을 하나의 표준 형식으로 변환하는 프로세스에 적용됩니다. 이 프로세스를 비디오 압축 또는 트랜스코딩이라고도 합니다. [개념에](https://en.wikipedia.org/wiki/Data_compression) 대한 자세한 내용은 데이터 압축 및 [인코딩 및 트랜스코딩이란 무엇입니까?를](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) 참조하십시오.

동영상은 일반적으로 [점진적 다운로드](https://en.wikipedia.org/wiki/Progressive_download) 또는 [적응형 비트레이트 스트리밍을](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)통해 장치 및 앱에 전달됩니다.

> [!IMPORTANT]
> 미디어 서비스는 취소되거나 오류가 있는 작업에 대해 요금을 청구하지 않습니다. 예를 들어 진행률 50%에 도달하고 취소된 작업은 작업 분의 50%로 청구되지 않습니다. 완료된 작업에 대해서만 요금이 부과됩니다.

* 점진적 다운로드를 통해 Azure Media Services를 사용하여 디지털 미디어 파일(메자닌)을 [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) 코덱으로 인코딩된 비디오와 [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) 코덱으로 인코딩된 오디오가 포함된 [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) 파일로 변환할 수 있습니다. 이 MP4 파일은 저장소 계정의 자산에 기록됩니다. Azure 저장소 API 또는 SDK(예: 저장소 [REST API](../../storage/common/storage-rest-api-auth.md) 또는 [.NET SDK)를](../../storage/blobs/storage-quickstart-blobs-dotnet.md)사용하여 파일을 직접 다운로드할 수 있습니다. 저장소에 특정 컨테이너 이름이 있는 출력 자산을 만든 경우 해당 위치를 사용합니다. 그렇지 않으면 미디어 서비스를 사용하여 [자산 컨테이너 URL을 나열할](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)수 있습니다. 
* 적응형 비트 전송률 스트리밍을 통해 전송을 위해 콘텐츠를 준비하려면 메자닌 파일을 여러 비트 레이트(높음에서 낮음)로 인코딩해야 합니다. 품질이 정상적으로 전환되도록 비트레이트 속도가 낮아짐에 따라 비디오 해상도가 낮아집니다. 이렇게 하면 소위 인코딩 사다리-해상도 및 비트 레이트 [테이블(자동 생성 된 적응 비트 레이트 래더](autogen-bitrate-ladder.md)참조)이 발생합니다. 미디어 서비스를 사용하여 여러 비트 레이트에서 메자닌 파일을 인코딩할 수 있습니다. 이렇게 하면 저장소 계정의 자산에 기록된 MP4 파일 및 관련 스트리밍 구성 파일 집합을 받게 됩니다. 그런 다음 미디어 서비스에서 [동적 패키징](dynamic-packaging-overview.md) 기능을 사용하여 [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) 및 [HLS와](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)같은 스트리밍 프로토콜을 통해 비디오를 전달할 수 있습니다. 이렇게 하려면 스트리밍 [로케이터를](streaming-locators-concept.md) 만들고 지원되는 프로토콜에 해당하는 스트리밍 URL을 빌드해야 하며, 이 URL은 해당 기능에 따라 장치/앱에 전달될 수 있습니다.

다음 다이어그램은 동적 패키징을 사용한 온디맨드 인코딩에 대한 워크플로우를 보여 주며,

![동적 패키징을 사용한 온디맨드 인코딩워크](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

이 항목에서는 Media Services v3을 사용하여 콘텐츠를 인코딩하는 방법에 대한 지침을 제공합니다.

## <a name="transforms-and-jobs"></a>변환 및 작업

미디어 서비스 v3로 인코딩하려면 [변환](https://docs.microsoft.com/rest/api/media/transforms) 및 [작업](https://docs.microsoft.com/rest/api/media/jobs). 변환은 인코딩 설정 및 출력에 대한 레시피를 정의합니다. 작업은 레시피의 인스턴스입니다. 자세한 내용은 [변환 및 작업](transforms-jobs-concept.md)을 참조하십시오.

Media Services를 사용하여 인코딩하는 경우 미리 설정을 사용하여 입력 미디어 파일을 처리하는 방법을 인코더에 알려줍니다. 미디어 서비스 v3에서는 표준 인코더를 사용하여 파일을 인코딩합니다. 예를 들어 인코딩된 콘텐츠에서 원하는 비디오 해상도 및/또는 오디오 채널 수를 지정할 수 있습니다.

업계 모범 사례에 따라 권장되는 기본 제공 미리 설정 중 하나를 사용하여 빠르게 시작하거나 특정 시나리오 또는 디바이스 요구 사항을 대상으로 지정하는 사용자 지정 미리 설정을 작성할 수 있습니다. 자세한 내용은 [사용자 지정 변환을 사용하여 인코딩](customize-encoder-presets-how-to.md)을 참조하세요.

MP4 파일을 생성하기 위해 표준 인코더로 인코딩할 때 2019년 1월부터 새 .mpi 파일이 생성되어 출력 자산에 추가됩니다. 이 MPI 파일은 [동적 패키징](dynamic-packaging-overview.md) 및 스트리밍 시나리오의 성능을 향상하기 위해 사용합니다.

> [!NOTE]
> MPI 파일을 수정하거나 제거하거나 이러한 파일의 존재 여부에 대한 서비스에 종속성을 가져서는 안됩니다.

### <a name="creating-job-input-from-an-https-url"></a>HTTPS URL에서 작업 입력 만들기

비디오를 처리하기 위해 채용 정보를 제출할 때 미디어 서비스에 입력 비디오를 찾을 위치를 알려주어야 합니다. 옵션 중 하나는 HTTPS URL을 작업 입력으로 지정하는 것입니다. 현재 미디어 서비스 v3는 HTTPS URL을 통해 청크 전송 인코딩을 지원하지 않습니다.

#### <a name="examples"></a>예

* [.NET을 통해 HTTPS URL에서 인코딩](stream-files-dotnet-quickstart.md)
* [REST가 있는 HTTPS URL에서 인코딩](stream-files-tutorial-with-rest.md)
* [CLI를 통해 HTTPS URL에서 인코딩](stream-files-cli-quickstart.md)
* [Node.js를 통해 HTTPS URL에서 인코딩](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>로컬 파일에서 작업 입력 만들기

입력 비디오는 Media Service 자산으로 저장할 수 있으며 이 경우 파일(로컬 또는 Azure Blob Storage에 저장됨)을 기반으로 입력 자산을 만듭니다.

#### <a name="examples"></a>예

[기본 제공 사전 설정을 사용하여 로컬 파일 인코딩](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>하위 클리핑을 통해 작업 입력 만들기

비디오를 인코딩할 때 소스 파일을 잘라내거나 클립화하도록 지정하고 입력 비디오의 원하는 부분만 있는 출력을 생성할 수 있습니다. 이 기능은 [BuiltInStandardEncoder 사전](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 설정 사전 설정 또는 [StandardEncoder 사전](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 설정 사전 설정을 사용하여 빌드된 모든 [변환에서](https://docs.microsoft.com/rest/api/media/transforms) 작동합니다.

주문형 비디오 또는 라이브 아카이브(녹화된 이벤트)의 단일 클립으로 [작업을](https://docs.microsoft.com/rest/api/media/jobs/create) 만들도록 지정할 수 있습니다. 작업 입력은 자산 또는 HTTPS URL일 수 있습니다.

> [!TIP]
> 비디오를 다시 인코딩하지 않고 비디오의 하위 립을 스트리밍하려면 [동적 패키지와 함께 사전 필터링 매니페스트를](filters-dynamic-manifest-overview.md)사용하는 것이 좋습니다.

#### <a name="examples"></a>예

예제를 참조하십시오.

* [.NET으로 비디오 서브클립](subclip-video-dotnet-howto.md)
* [REST로 비디오 서브클립](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>기본 제공 미리 설정

미디어 서비스는 다음과 같은 기본 제공 인코딩 사전 설정을 지원합니다.  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)은 표준 인코더로 입력 비디오를 인코딩하는 기본 제공 미리 설정을 지정하는 데 사용됩니다.

현재 지원되는 미리 설정은 다음과 같습니다.

- **인코더명프리셋.AAC굿퀄리티오디오**: 192kbps로 인코딩된 스테레오 오디오만 포함하는 단일 MP4 파일을 생성합니다.
- **인코더명명프리셋.적응스트리밍(권장):** 자세한 내용은 [비트레이트 래더 자동 생성을](autogen-bitrate-ladder.md)참조하세요.
- **인코더명명프리셋.콘텐츠인식엔코딩실험**: 콘텐츠 인식 인코딩을 위한 실험적 사전 설정을 노출한다. 입력 콘텐츠가 주어지면 서비스는 적응형 스트리밍을 통해 최적의 레이어 수와 적절한 비트 전송률 및 해상도 설정을 자동으로 결정하려고 시도합니다. 기본 알고리즘은 시간이 지남에 따라 계속 진화할 것입니다. 출력에는 비디오 및 오디오 인터리브가 있는 MP4 파일이 포함됩니다. 자세한 내용은 [콘텐츠 인식 인코딩에 대한 실험 사전 설정을](content-aware-encoding.md)참조하십시오.
- **인코더NamedPreset.H264MultipleBitrate1080p**: 6000 kbps에서 400 kbps에 이르기까지 8 개의 GOP 정렬 MP4 파일 세트와 스테레오 AAC 오디오를 생성합니다. 해상도는 1,080p에서 시작하여 360p까지 낮아집니다.
- **인코더NamedPreset.H264MultipleBitrate720p**: 3400 kbps에서 400 kbps에 이르기까지 여섯 GOP 정렬 MP4 파일의 세트를 생성하고, 스테레오 AAC 오디오. 해상도는 720p에서 시작하여 360p까지 낮아집니다.
- **인코더NamedPreset.H264MultipleBitrateSD**: 1600 kbps에서 400 kbps에 이르기까지 다섯 GOP 정렬 MP4 파일의 세트를 생성하고, 스테레오 AAC 오디오. 해상도는 480p에서 시작하여 360p까지 낮아집니다.
- **인코더NamedPreset.H264SingleBitrate1080p**: 비디오가 6750 kbps에서 H.264 코덱으로 인코딩되고 1080 픽셀의 사진 높이가 있는 MP4 파일을 생성하며 스테레오 오디오는 64kbps의 AAC-LC 코덱으로 인코딩됩니다.
- **인코더NamedPreset.H264SingleBitrate720p**: 비디오가 4500 kbps에서 H.264 코덱으로 인코딩되고 720 픽셀의 사진 높이가 있는 MP4 파일을 생성하며 스테레오 오디오는 64kbps의 AAC-LC 코덱으로 인코딩됩니다.
- **인코더NamedPreset.H264SingleBitrateSD**: 비디오가 2200 kbps에서 H.264 코덱으로 인코딩되고 480 픽셀의 사진 높이가 있는 MP4 파일을 생성하고 스테레오 오디오는 64kbps의 AAC-LC 코덱으로 인코딩됩니다.

최신 사전 설정 목록을 보려면 [비디오 인코딩에 사용할 기본 제공 사전 설정을](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)참조하십시오.

사전 설정이 사용되는 방법을 보려면 [파일 업로드, 인코딩 및 스트리밍 을](stream-files-tutorial-with-api.md)참조하십시오.

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)은 표준 인코더로 입력 비디오를 인코딩할 때 사용할 설정을 설명합니다. Transform 미리 설정을 사용자 지정할 때 이 미리 설정을 사용합니다.

#### <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 고려 사항이 적용됩니다.

- AVC 콘텐츠의 높이 및 너비에 대한 모든 값은 4배여야 합니다.
- Azure Media Services v3에서 모든 인코딩 비트 레이트(bitrates)는 초당 비트입니다. 이것은 단위로 킬로비트 / 초를 사용하는 v2 API의 사전 설정과 다릅니다. 예를 들어 v2의 비트 레이트(128(킬로비트/초)로 지정된 경우 v3에서는 128000(비트/초)으로 설정됩니다.

### <a name="customizing-presets"></a>사전 설정 사용자 지정

Media Services는 특정 인코딩 필요 및 요구 사항을 충족하기 위해 미리 설정에 포함된 모든 값을 완전히 사용자 지정할 수 있도록 지원합니다. 인코더 사전 설정을 사용자 지정하는 방법을 보여 주는 예제는 아래 목록을 참조하십시오.

#### <a name="examples"></a>예

- [.NET으로 사전 설정을 사용자 지정](customize-encoder-presets-how-to.md)
- [CLI로 사전 설정 사용자 지정](custom-preset-cli-howto.md)
- [REST로 사전 설정 사용자 지정](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>사전 설정 스키마

Media Services v3에서 사전 설정은 API 자체에서 강력하게 입력된 엔터티입니다. 이러한 개체에 대한 "스키마" 정의는 [개방형 API 사양(또는 Swagger)에서](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)찾을 수 있습니다. 또한 [REST API,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [.NET SDK(또는](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) 기타 미디어 서비스 v3 SDK 참조 설명서)에서 사전 설정 된 정의 (예 : **StandardEncoderPreset)를**볼 수 있습니다.

## <a name="scaling-encoding-in-v3"></a>v3에서 인코딩 크기 조정

미디어 처리의 크기를 조정하려면 [CLI를 사용하여 배율을](media-reserved-units-cli-how-to.md)참조하십시오.

## <a name="billing"></a>결제

미디어 서비스는 취소되거나 오류가 있는 작업에 대해 요금을 청구하지 않습니다. 예를 들어 진행률 50%에 도달하고 취소된 작업은 작업 분의 50%로 청구되지 않습니다. 완료된 작업에 대해서만 요금이 부과됩니다.

자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/media-services/)을 참조하십시오.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [미디어 서비스를 사용하여 업로드, 인코딩 및 스트리밍.](stream-files-tutorial-with-api.md)
* [기본 제공 사전 설정을 사용하여 HTTPS URL에서 인코딩합니다.](job-input-from-http-how-to.md)
* [기본 제공 사전 설정을 사용하여 로컬 파일을 인코딩합니다.](job-input-from-local-file-how-to.md)
* [특정 시나리오 또는 장치 요구 사항을 대상으로 하는 사용자 지정 사전 설정을 빌드합니다.](customize-encoder-presets-how-to.md)
