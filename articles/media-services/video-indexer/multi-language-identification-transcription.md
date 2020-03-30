---
title: 비디오 인덱서를 통해 다국어 콘텐츠를 자동으로 식별하고 전사
titleSuffix: Azure Media Services
description: 이 항목에서는 비디오 인덱서를 사용하여 다국어 콘텐츠를 자동으로 식별하고 전사하는 방법을 보여 줍니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72968732"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>다국어 콘텐츠 자동 식별 및 전사(미리 보기)

비디오 인덱서(Video Indexer)는 다국어 콘텐츠의 자동 언어 식별 및 전사를 지원합니다. 이 프로세스에는 오디오에서 서로 다른 세그먼트의 음성 언어를 자동으로 식별하고 미디어 파일의 각 세그먼트를 전사하고 전사를 하나의 통합 전사로 다시 결합하는 작업이 포함됩니다. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>포털을 통한 인덱싱에서 다국어 식별 선택

동영상을 업로드하고 색인 을 만들 때 **다국어 검색을** 선택할 수 있습니다. 또는 비디오를 다시 인덱싱할 때 **다국어 검색을** 선택할 수 있습니다. 다음 단계에서는 다시 인덱싱하는 방법을 설명합니다.

1. [Video Indexer](https://vi.microsoft.com/) 웹 사이트로 이동하고 로그인합니다.
1. **라이브러리** 페이지로 이동하여 다시 인덱싱할 비디오 의 이름을 마우스로 가리킵니다. 
1. 오른쪽 하단 모서리에서 비디오 **색인 재생성** 버튼을 클릭합니다. 
1. 비디오 **색인 다시 색인 화수** 상자에서 **비디오 소스 언어** 드롭다운 상자에서 **다국어 검색을** 선택합니다.

    * 비디오가 다국어로 인덱싱되면 인사이트 페이지에 해당 옵션이 포함되고 추가 인사이트 유형이 표시되므로 사용자는 "음성 언어"로 기록된 세그먼트를 볼 수 있습니다.
    * 모든 언어로 번역은 다국어 성적 증명서에서 완전히 사용할 수 있습니다.
    * 다른 모든 인사이트는 감지된 마스터 언어로 표시되며, 이는 오디오에서 가장 많이 나타난 언어입니다.
    * 플레이어에 대한 자막은 다국어로도 제공됩니다.

![포털 환경](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>API를 통한 인덱싱에서 다국어 식별 선택

API를 사용하여 비디오를 인덱싱하거나 [다시 인덱싱할](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 때 `multi-language detection` `sourceLanguage` 매개 변수에서 옵션을 선택합니다.

### <a name="model-output"></a>모델 출력

모델은 하나의 목록에서 비디오에서 검색된 모든 언어를 검색합니다.

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

또한 전사 섹션의 각 인스턴스에는 전사된 언어가 포함됩니다.

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>지침 및 제한 사항

* 지원되는 언어 집합: 영어, 프랑스어, 독일어, 스페인어.
* 지원되는 최대 3개 언어로 다국어 콘텐츠를 지원합니다.
* 오디오에 위의 지원되는 목록 이외의 언어가 포함된 경우 예기치 않은 결과가 발생합니다.
* 각 언어에 대해 감지할 수 있는 최소 세그먼트 길이 – 15초.
* 언어 검색 오프셋은 평균 3초입니다.
* 연설은 지속될 것으로 예상됩니다. 언어 간 자주 번갈아 가며 모델 성능에 영향을 줄 수 있습니다.
* 원어민이 아닌 사람의 음성은 모델 성능에 영향을 줄 수 있습니다(예: 스피커가 모국어를 사용하고 다른 언어로 전환하는 경우).
* 이 모델은 합리적인 오디오 음향(음성 명령, 노래 등)을 통해 자연스러운 대화 음성을 인식하도록 설계되었습니다.
* 현재 다국어 동영상에는 프로젝트 만들기 및 편집이 제공되지 않습니다.
* 다국어 검색을 사용하는 경우 사용자 지정 언어 모델을 사용할 수 없습니다.
* 키워드 추가는 지원되지 않습니다.
* 닫힌 캡션 파일을 내보낼 때 언어 표시가 나타나지 않습니다.
* 업데이트 성적 증명서 API는 여러 언어 파일을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

[Video Indexer 개요](video-indexer-overview.md)
