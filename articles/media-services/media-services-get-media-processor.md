---
title: "미디어 프로세서를 만드는 방법 | Microsoft Docs"
description: "Azure 미디어 서비스용 미디어 콘텐츠를 인코딩하거나 형식을 변환하거나 암호화하거나 암호 해독하기 위한 미디어 프로세서 구성 요소를 만드는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 88f6e1da090eb6088e54c6f81d0f83b1737d3c2c
ms.lasthandoff: 04/12/2017


---
# <a name="how-to-get-a-media-processor-instance"></a>방법: 미디어 프로세서 인스턴스 가져오기
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST (영문)](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>개요
미디어 서비스에서 미디어 프로세서는 미디어 콘텐츠 인코딩, 형식 변환, 암호화 또는 암호 해독과 같은 특정 처리 작업을 다루는 구성 요소입니다. 일반적으로 미디어 콘텐츠 인코드, 암호화 또는 형식 변환 작업을 만들 때 미디어 프로세서를 만듭니다.

다음 표에서는 각 사용 가능한 미디어 프로세서의 이름과 설명을 제공합니다.

| 미디어 프로세서 이름 | 설명 | 추가 정보 |
| --- | --- | --- |
| 미디어 인코더 표준 |주문형 인코딩에 대한 표준 기능을 제공합니다. |[Azure 주문형 미디어 인코더 개요 및 비교](media-services-encode-asset.md) |
| 미디어 인코더 Premium 워크플로 |미디어 인코더 Premium 워크플로를 사용하여 인코딩 작업을 실행할 수 있습니다. |[Azure 주문형 미디어 인코더 개요 및 비교](media-services-encode-asset.md) |
| Azure 미디어 인덱서 |미디어 파일과 콘텐츠를 검색 가능하도록 설정할 수 있으며 선택 캡션 트랙과 키워드를 생성할 수 있습니다. |[Azure 미디어 인덱서](media-services-index-content.md) |
| Azure 미디어 Hyperlapse(미리 보기) |비디오 안정화를 통해 비디오에서 "범프"를 부드럽게 할 수 있습니다. 사용 가능한 클립으로 만들어 콘텐츠 속도를 높일 수도 있습니다. |[Azure 미디어 Hyperlapse](media-services-hyperlapse-content.md) |
| Azure 미디어 인코더 |사용되지 않음 | |
| 저장소 암호 해독 |사용되지 않음 | |
| Azure Media Packager |사용되지 않음 | |
| Azure Media Encryptor |사용되지 않음 | |

## <a name="get-media-processor"></a>미디어 프로세서 가져오기
다음 메서드는 미디어 프로세서 인스턴스를 가져오는 방법을 보여 줍니다. 이 코드 예제에서는 **_context**라는 모듈 수준 변수를 사용하여 [방법: 프로그래밍 방식으로 Media Services에 연결](media-services-dotnet-connect-programmatically.md) 섹션에 설명된 대로 서버 컨텍스트를 참조한다고 가정합니다.

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계
미디어 프로세서 인스턴스를 가져오는 방법을 알아보았으므로 이제 Media Encoder Standard를 사용하여 자산을 인코딩하는 방법을 보여 주는 [자산을 인코딩하는 방법](media-services-dotnet-encode-with-media-encoder-standard.md) 토픽으로 이동합니다.


