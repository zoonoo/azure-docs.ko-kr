---
title: 사용자 검토를 사용한 비디오 조정 - Content Moderator
titlesuffix: Azure Cognitive Services
description: 컴퓨터 지원 비디오 조정 및 사용자 검토 도구를 사용하여 부적절한 콘텐츠 조정
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: 43a43ddcbfc656a3eb5a274e1bb63a473b7c89a2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867718"
---
# <a name="video-moderation-with-human-review"></a>사용자 검토를 사용한 비디오 조정

Content Moderator의 컴퓨터 지원 [비디오 조정](video-moderation-api.md) 및 [사용자 검토 도구](Review-Tool-User-Guide/human-in-the-loop.md)를 사용하여 성인물(노골적) 및 선정적(외설적) 콘텐츠에 대한 비디오 및 대본을 조정하면 비즈니스를 위한 최상의 결과를 얻을 수 있습니다.

## <a name="video-trained-classifier-preview"></a>비디오 학습 분류자(미리 보기)

컴퓨터 지원 비디오 분류는 이미지 학습 모델 또는 비디오 학습 모델을 통해 이루어집니다. 이미지 학습 비디오 분류자와는 달리, Microsoft의 성인물 및 선정적 비디오 분류자는 비디오를 통해 학습합니다. 이 방법의 일치 품질이 더 높습니다.

## <a name="shot-detection"></a>샷 검색

분류 세부 정보를 출력할 때 추가 비디오 인텔리전스는 비디오를 좀 더 유연하게 분석할 수 있게 도와줍니다. Microsoft의 비디오 조정 서비스는 단순히 프레임만 출력하지 않고 샷 수준 정보를 제공합니다. 이제 샷 수준 및 프레임 수준에서 비디오를 분석하는 옵션이 생겼습니다.
 
## <a name="key-frame-detection"></a>키 프레임 검색

비디오 조정 서비스는 정기적으로 프레임을 출력하는 대신 잠재적으로 완전한(양호한) 프레임만 식별하여 출력합니다. 이 기능을 통해 프레임 수준 성인물 및 음란물 분석을 위한 효율적인 프레임을 생성할 수 있습니다.

다음 추출물은 잠재적 샷, 키 프레임, 성인물 및 음란물 점수를 이용한 부분 응답을 보여줍니다.

    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]


## <a name="visualization-for-human-reviews"></a>사용자 검토에 대한 시각화

좀 더 미묘한 상황인 경우 기업은 비디오, 프레임 및 컴퓨터 할당 태그를 렌더링하기 위한 사용자 검토 솔루션이 필요합니다. 비디오 및 프레임을 검토하는 사용자 중재자는 인사이트 전체를 살펴보고, 태그를 변경하고, 결정 사항을 제출합니다.

![비디오 검토 도구 기본 보기](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>비디오 수준 검토를 위한 플레이어 보기

비디오 수준 이진 의사 결정은 잠재적인 성인물 및 음란물 프레임을 보여주는 비디오 플레이어 보기를 통해 가능합니다. 사용자 검토자는 다양한 속도 옵션을 사용하여 비디오를 탐색하면서 장면을 검사합니다. 이들은 태그를 설정/해제하여 자신의 결정 사항을 확인합니다.

![비디오 검토 도구 플레이어 보기](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>자세한 검토를 위한 프레임 보기

프레임 단위 분석을 위한 자세한 비디오 검토는 프레임 기반 보기를 통해 가능합니다. 사용자 검토자는 하나 이상의 프레임을 검토 및 선택하고 태그를 설정/해제하여 결정 사항을 확인합니다. 그 다음으로 수행할 선택적 단계는 모욕적인 프레임 또는 콘텐츠를 편집하는 것입니다.

![비디오 검토 도구 프레임 보기](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>대본 조정

비디오에는 일반적으로 공격적인 표현에 대한 조정이 필요한 목소리도 포함되어 있습니다. 검토 도구 내에서 Azure Media Indexer 서비스를 사용하여 음성을 텍스트로 변환하고 Content Moderator의 review API를 사용하여 텍스트를 조정할 대본을 제출합니다.

![비디오 검토 도구 대본 보기](images/video-review-transcript-view.png)

## <a name="next-steps"></a>다음 단계

[비디오 조정 빠른 시작](video-moderation-api.md)을 시작합니다. 

조정된 출력에서 사용자 검토자의 [비디오 검토](video-reviews-quickstart-dotnet.md)를 생성하는 방법을 알아봅니다.

[비디오 대본 검토](video-transcript-reviews-quickstart-dotnet.md)를 비디오 검토에 추가합니다.

[완전한 비디오 조정 솔루션](video-transcript-moderation-review-tutorial-dotnet.md)을 개발하는 방법에 대한 자세한 자습서를 살펴봅니다. 
