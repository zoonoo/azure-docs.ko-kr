---
title: 비디오 인덱서를 사용하여 음성 언어 자동 식별 - Azure
titleSuffix: Azure Media Services
description: 이 문서에서는 비디오에서 음성 언어를 자동으로 식별하는 데 비디오 인덱서 언어 식별 모델을 사용하는 방법을 설명합니다.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: 7a2e03b8dacbf6c3ff20e02c804804b671e86d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513884"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>언어 식별 모델로 음성 언어를 자동으로 식별

비디오 인덱서 (VIDEO Indexer)는 오디오에서 음성 언어 콘텐츠를 자동으로 식별하고 미디어 파일을 주요 식별 언어로 전사하는 프로세스인 자동 언어 식별(LID)을 지원합니다. 현재 LID는 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 중국어(간체), 일본어, 러시아어, 포르투갈어(브라질어)를 지원합니다. 

## <a name="choosing-auto-language-identification-on-indexing"></a>인덱싱에서 자동 언어 식별 선택

API를 사용하여 비디오를 인덱싱하거나 [다시 인덱싱할](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) `sourceLanguage` 때 매개 변수에서 `auto detect` 옵션을 선택합니다.

포털을 사용하는 경우 [비디오 인덱서](https://www.videoindexer.ai/) 홈 페이지의 **계정 동영상으로** 이동하여 다시 색인을 지정하려는 동영상의 이름을 마우스로 가리킵니다. 오른쪽 하단 모서리에서 인덱스 다시 단추를 클릭합니다. 비디오 **색인 다시 색인 화수** 상자에서 *자동 감지를* **선택합니다.**

![자동 감지](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>모델 출력

비디오 인덱서가 해당 언어에 대한 신뢰도가 있는 경우 `> 0.6`가장 가능성이 높은 언어에 따라 비디오를 전사합니다. 확신을 가지고 언어를 식별할 수 없는 경우, 음성 언어가 영어라고 가정합니다. 

모델 지배적 인 언어는 `sourceLanguage` 특성 (루트 / 비디오 / 통찰력 아래)로 인사이트 JSON에서 사용할 수 있습니다. 해당 신뢰도 점수는 `sourceLanguageConfidence` 특성 아래에서도 사용할 수 있습니다.

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

* 지원되는 언어에는 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 중국어(간체), 일본어, 러시아어 및 브라질 포르투갈어가 포함됩니다.
* 오디오에 위의 지원되는 목록 이외의 언어가 포함된 경우 예기치 않은 결과가 발생합니다.
* 비디오 인덱서가 충분한 신뢰도()로`>0.6`언어를 식별할 수 없는 경우 대체 언어는 영어입니다.
* 혼합 언어 오디오가 있는 파일에 대한 현재 지원은 없습니다. 오디오에 혼합 언어가 포함되어 있으면 예기치 않은 결과가 발생합니다. 
* 음량이 낮은 오디오는 모델 결과에 영향을 미칠 수 있습니다.
* 모델은 오디오에서 1분 이상의 음성이 필요합니다.
* 이 모델은 음성 명령, 노래 등이 아닌 자발적인 대화 음성을 인식하도록 설계되었습니다.

## <a name="next-steps"></a>다음 단계

* [개요](video-indexer-overview.md)
* [다국어 콘텐츠를 자동으로 식별하고 전사](multi-language-identification-transcription.md)
