---
title: 인덱서 v1 및 v2에서 Azure 미디어 서비스 비디오 인덱서로 마이그레이션 | 마이크로 소프트 문서
description: 이 항목에서는 Azure 미디어 인덱서 v1 및 v2에서 Azure 미디어 서비스 비디오 인덱서로 마이그레이션하는 방법에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513238"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>미디어 인덱서 및 미디어 인덱서 2에서 비디오 인덱서로 마이그레이션

[Azure 미디어 인덱서](media-services-index-content.md) 미디어 프로세서 및 [Azure 미디어 인덱서 2 미리 보기](media-services-process-content-with-indexer2.md) 미디어 프로세서가 사용 중지됩니다. 사용 중지 날짜에 대 한이 [레거시 구성 요소](legacy-components.md) 항목을 참조 합니다. [Azure 미디어 서비스 비디오 인덱서가](https://docs.microsoft.com/azure/media-services/video-indexer/) 이러한 레거시 미디어 프로세서를 대체합니다.

Azure 미디어 서비스 비디오 인덱서에는 Azure Media 분석, Azure 인지 검색, 인지 서비스(예: Face API, Microsoft 번역기, 컴퓨터 비전 API 및 사용자 지정 음성 서비스)를 기반으로 합니다. 이 애플리케이션에서 Video Indexer 비디오 및 오디오 모델을 사용하여 비디오의 인사이트를 추출할 수 있습니다. 비디오 인덱서에서 사용할 수 있는 시나리오, 제공하는 기능 및 시작하는 방법을 보려면 [비디오 인덱서 비디오 및 오디오 모델을](../video-indexer/video-indexer-overview.md)참조하십시오. 

[Azure Media Services v3 분석기 사전 설정을](../latest/analyzing-video-audio-files-concept.md) 사용하거나 비디오 [인덱서 API를](https://api-portal.videoindexer.ai/)사용하여 직접 비디오 및 오디오 파일에서 통찰력을 추출할 수 있습니다. 현재 비디오 인덱서 API와 미디어 서비스 v3 API에서 제공하는 기능 사이에는 중복이 있습니다.

> [!NOTE]
> 비디오 인덱서 와 미디어 서비스 분석기 사전 설정을 사용할 시기를 이해하려면 [비교 문서를](../video-indexer/compare-video-indexer-with-media-services-presets.md)확인하십시오. 

이 문서에서는 Azure 미디어 인덱서 및 Azure 미디어 인덱서 2에서 Azure 미디어 서비스 비디오 인덱서로 마이그레이션하는 단계에 대해 설명합니다.  

## <a name="migration-options"></a>마이그레이션 옵션 

|필요한 경우  |다음으로 |
|---|---|
|VTT, SRT 또는 TTML과 같은 모든 미디어 파일 형식에 대해 음성-텍스트 전사를 제공하는 솔루션<br/>키워드, 주제 추론, 음향 이벤트, 스피커 디아리화, 엔터티 추출 및 번역과 같은 추가 오디오 인사이트| 비디오 인덱서 v2 REST API 또는 Azure 미디어 서비스 v3 오디오 분석기 사전 설정을 통해 Azure 비디오 인덱서 기능을 사용하도록 응용 프로그램을 업데이트합니다.|
|음성-텍스트 기능| 코그너티브 서비스 음성 API를 직접 사용합니다.|  

## <a name="getting-started-with-video-indexer"></a>비디오 인덱서 시작

다음 섹션에서는 관련 링크를 가리킵니다: [비디오 인덱서를 시작하려면 어떻게](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 해야 하나요? 

## <a name="getting-started-with-media-services-v3-apis"></a>미디어 서비스 v3 API 시작

Azure 미디어 서비스 v3 API를 사용하면 [Azure Media Services v3 분석기 사전 설정을](../latest/analyzing-video-audio-files-concept.md)통해 비디오 및 오디오 파일에서 통찰력을 추출할 수 있습니다. 

**AudioAnalyzerPreset**를 사용하면 오디오 또는 비디오 파일에서 여러 오디오 인사이트를 추출할 수 있습니다. 출력에는 오디오 대본에 대한 VTT 또는 TTML 파일과 JSON 파일(모든 추가 오디오 인사이트 포함)이 포함됩니다. 오디오 인사이트에는 키워드, 스피커 인덱싱 및 음성 감정 분석이 포함됩니다. AudioAnalyzerPreset은 특정 언어에 대한 언어 검색도 지원합니다. 자세한 내용은 [변환 을](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)참조하십시오.

### <a name="get-started"></a>시작

시작하려면

* [자습서](../latest/analyze-videos-tutorial-with-api.md)
* 오디오 분석기사전 샘플: [자바 SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) 또는 [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* 비디오 분석기사전 설정 샘플: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) 또는 [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>코그너티브 서비스 음성 서비스 시작

[Azure Cognitive Services는](https://docs.microsoft.com/azure/cognitive-services/) 응용 프로그램, 도구 또는 장치가 사용하거나 표시할 수 있는 오디오 스트림을 텍스트로 실시간으로 전사하는 음성-텍스트 서비스를 제공합니다. 음성-텍스트를 사용하여 사용자 [고유의 어쿠스틱 모델, 언어 모델 또는 발음 모델을 사용자 지정할](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)수 있습니다. 자세한 내용은 [코그너티브 서비스 음성-텍스트](../../cognitive-services/speech-service/speech-to-text.md)를 참조하십시오. 

> [!NOTE] 
> 음성-텍스트 서비스는 비디오 파일 형식을 사용하지 않으며 [특정 오디오 형식만 사용합니다.](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats) 

텍스트 음성 변환 서비스 및 시작하는 방법에 대한 자세한 내용은 [음성-텍스트란 무엇입니까?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>더 이상 사용되지 않은 서비스와의 알려진 차이점 

비디오 인덱서, Azure 미디어 서비스 v3 AudioAnalyzerPreset 및 인지 서비스 음성 서비스 서비스가 더 안정적이며 은퇴한 Azure 미디어 인덱서 1 및 Azure Media Indexer 2 프로세서보다 더 나은 품질의 출력을 생성한다는 것을 알게 될 것입니다.  

몇 가지 알려진 차이점은 다음과 같습니다. 

* 코그너티브 서비스 음성 서비스는 키워드 추출을 지원하지 않습니다. 그러나 비디오 인덱서 및 미디어 서비스 v3 AudioAnalyzerPreset은 모두 JSON 파일 형식의 보다 강력한 키워드 집합을 제공합니다. 

## <a name="need-help"></a>도움 필요 시

[새 지원 요청으로](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 이동하여 지원 티켓을 열 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [레거시 구성 요소](legacy-components.md)
* [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/#encoding)


