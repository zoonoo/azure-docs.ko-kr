---
title: Azure Media Services를 사용하여 클라우드에서 인코딩 | Microsoft Docs
description: 이 항목에서는 Azure Media Services를 사용할 때의 인코딩 프로세스에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/05/2018
ms.author: juliako
ms.openlocfilehash: 0df13e3364cebe7cb5804b840889bca971b36be2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235057"
---
# <a name="encoding-with-media-services"></a>Media Services를 사용하여 인코딩

Azure Media Services를 사용하면 고품질 디지털 미디어 파일을 다양한 브라우저와 장치에서 재생할 수 있는 형식으로 인코딩할 수 있습니다. 예를 들어 콘텐츠를 Apple의 HLS 또는 MPEG DASH 형식으로 스트림할 수 있습니다. 이 항목에서는 Media Services v3을 사용하여 콘텐츠를 인코딩하는 방법에 대한 지침을 제공합니다.

Media Services v3을 사용하여 인코딩하려면 변환 및 작업을 만들어야 합니다. 변환은 인코딩 설정 및 출력에 대한 방법을 정의하며 작업은 방법의 인스턴스입니다. 자세한 내용은 [Transform 및 Job](transform-concept.md)을 참조하세요.

Media Services를 사용하여 인코딩하는 경우 미리 설정을 사용하여 입력 미디어 파일을 처리하는 방법을 인코더에 알려줍니다. 예를 들어 인코딩된 콘텐츠에서 원하는 비디오 해상도 및/또는 오디오 채널 수를 지정할 수 있습니다. 

업계 모범 사례에 따라 권장되는 기본 제공 미리 설정 중 하나를 사용하여 빠르게 시작하거나 특정 시나리오 또는 장치 요구 사항을 대상으로 지정하는 사용자 지정 미리 설정을 작성할 수 있습니다. 자세한 내용은 [사용자 지정 변환을 사용하여 인코딩](customize-encoder-presets-how-to.md)을 참조하세요. 

## <a name="built-in-presets"></a>기본 제공 미리 설정

Media Services에서 현재 지원하는 기본 제공 인코딩 미리 설정은 다음과 같습니다.  

|**미리 설정 이름**|**시나리오**|**세부 정보**|
|---|---|---|
|**BuiltInStandardEncoderPreset**|스트리밍|표준 인코더를 사용하여 입력 비디오를 인코딩하는 기본 제공 미리 설정을 설정하는 데 사용됩니다. <br/>현재 지원되는 미리 설정은 다음과 같습니다.<br/>**EncoderNamedPreset.AdaptiveStreaming**(권장) - 자세한 내용은 [비트 전송률 사다리 자동 생성](autogen-bitrate-ladder.md)을 참조하세요.<br/>**EncoderNamedPreset.AACGoodQualityAudio** - 192kbps로 인코딩된 스테레오 오디오만 포함된 단일 MP4 파일을 생성합니다.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** - 스테레오 AAC 오디오 및 6,000~400kbps 범위의 8GOP 정렬 MP4 파일 집합을 생성합니다. 해상도는 1,080p에서 시작하여 360p까지 낮아집니다.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** - 스테레오 AAC 오디오 및 3,400~400kbps 범위의 6GOP 정렬 MP4 파일 집합을 생성합니다. 해상도는 720p에서 시작하여 360p까지 낮아집니다.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** - 스테레오 AAC 오디오 및 1,600~400kbps 범위의 5GOP 정렬 MP4 파일 집합을 생성합니다. 해상도는 480p에서 시작하여 360p까지 낮아집니다.<br/><br/>자세한 내용은 [파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)을 참조하세요.|
|**StandardEncoderPreset**|스트리밍|표준 인코더를 사용하여 입력 비디오를 인코딩할 때 사용할 설정을 설명합니다. <br/>Transform 미리 설정을 사용자 지정할 때 이 미리 설정을 사용합니다. 자세한 내용은 [Transform 미리 설정을 사용자 지정하는 방법](customize-encoder-presets-how-to.md)을 참조하세요.|

## <a name="custom-presets"></a>사용자 지정 미리 설정

Media Services는 특정 인코딩 필요 및 요구 사항을 충족하기 위해 미리 설정에 포함된 모든 값을 완전히 사용자 지정할 수 있도록 지원합니다. Transform 미리 설정을 사용자 지정할 때 **StandardEncoderPreset** 미리 설정을 사용합니다. 자세한 설명과 예제는 [인코더 미리 설정을 사용자 지정하는 방법](customize-encoder-presets-how-to.md)을 참조하세요.

## <a name="scaling-encoding-in-v3"></a>v3에서 인코딩 크기 조정

현재 고객이 Azure Portal 또는 Media Services v2 API를 사용하여 [미디어 처리 크기 조정](../previous/media-services-scale-media-processing-overview.md)에 설명된 대로 RU를 설정해야 합니다. 

## <a name="next-steps"></a>다음 단계

### <a name="tutorials"></a>자습서

다음 자습서에서는 Media Services를 사용하여 콘텐츠를 인코딩하는 방법을 보여 줍니다.

* [Media Services를 사용하여 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)

### <a name="code-samples"></a>코드 샘플

다음 코드 샘플에는 Media Services를 사용하여 인코딩하는 방법을 보여 주는 코드가 포함되어 있습니다.

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [Azure CLI](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>SDK

지원되는 다음 Media Services v3 SDK 중 하나를 사용하여 콘텐츠를 인코딩할 수 있습니다.

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST (영문)](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

