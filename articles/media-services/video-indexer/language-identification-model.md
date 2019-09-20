---
title: Video Indexer에서 음성 언어를 자동으로 식별-Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer 언어 식별 모델을 사용 하 여 비디오에서 음성 언어를 자동으로 식별 하는 방법을 설명 합니다.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: 729ac9fc7c8ec6a85b3497c51888d3b5a6fc8558
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147184"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>언어 식별 모델을 사용 하 여 음성 언어 자동 식별

Video Indexer는 오디오에서 음성 언어 콘텐츠를 자동으로 식별 하 고 식별 된 주요 언어로 transcribed 미디어 파일을 보내는 프로세스 인 자동 언어 식별 (LID)을 지원 합니다. 현재 LID는 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 중국어 (간체), 일본어, 러시아어 및 포르투갈어 (브라질)를 지원 합니다. 

## <a name="choosing-auto-language-identification-on-indexing"></a>인덱싱에서 자동 언어 식별 선택

API를 사용 하 여 [비디오를 인덱싱하거나 인덱싱할 때](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) `auto detect` `sourceLanguage` 매개 변수에서 옵션을 선택 합니다.

포털을 사용 하는 경우 [Video Indexer](https://www.videoindexer.ai/) 홈 페이지의 **계정 비디오** 로 이동 하 여 다시 인덱싱할 비디오의 이름을 마우스로 가리킵니다. 오른쪽 아래 모서리에서 인덱스 다시 설정 단추를 클릭 합니다. 비디오 **다시 인덱싱** 대화 상자의 **비디오 원본 언어** 드롭다운 상자에서 *자동 검색* 을 선택 합니다.

![자동 검색](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>모델 출력

해당 언어에 대 한 신뢰도가 `> 0.6`speech 면 가장 가능성이 높은 언어에 따라 비디오를 합니다. Video Indexer 언어가 자신을 식별할 수 없는 경우 음성 언어는 영어 라고 가정 합니다. 

모델 기준 언어는 insights JSON에서 `sourceLanguage` 특성으로 사용할 수 있습니다 (root/비디오/정보에서). `sourceLanguageConfidence` 특성에서 해당 신뢰 점수를 사용할 수도 있습니다.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>지침 및 제한 사항

* 지원 되는 언어는 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 중국어 (간체), 일본어, 러시아어 및 포르투갈어 (브라질)입니다.
* 오디오가 위의 지원 되는 목록 이외의 언어를 포함 하는 경우 예기치 않은 결과가 발생 합니다.
* Video Indexer 높은 신뢰도 (`>0.6`)로 언어를 식별할 수 없는 경우 대체 언어는 영어입니다.
* 혼합 된 언어가 있는 파일은 현재 지원 되지 않습니다. 오디오에 혼합 된 언어가 포함 되어 있으면 예기치 않은 결과가 발생 합니다. 
* 저품질 오디오가 모델 결과에 영향을 줄 수 있습니다.
* 모델을 사용 하려면 오디오에 적어도 1 분 이상의 음성이 필요 합니다.
* 모델은 갑작스런 대화형 음성 (음성 명령, 노래 등)을 인식 하도록 설계 되었습니다.

## <a name="next-steps"></a>다음 단계

* [개요](video-indexer-overview.md)
* [자동으로 다국어 콘텐츠 식별 및 높여줄](multi-language-identification-transcription.md)
