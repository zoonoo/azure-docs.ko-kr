---
title: Video Indexer를 사용하여 다국어 콘텐츠를 자동으로 식별하고 전사
titleSuffix: Azure Media Services
description: 이 항목에서는 Video Indexer를 사용하여 다국어 콘텐츠를 자동으로 식별하고 전사하는 방법을 설명합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 319bd408943c560622dc3208a6701417b8ca010c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532915"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>자동으로 다국어 콘텐츠 식별 및 전사

Video Indexer는 다국어 콘텐츠에서 자동 언어 식별 및 전사를 지원합니다. 이 프로세스는 오디오의 여러 세그먼트에서 음성 언어를 자동으로 식별하고, 전사할 미디어 파일의 각 세그먼트를 보내고 전사를 하나의 통합 전사로 다시 결합합니다. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>포털을 사용하여 인덱싱에서 다국어 식별 선택

비디오를 업로드하고 인덱싱할 때 **다국어 검색** 을 선택할 수 있습니다. 또는 비디오를 다시 인덱싱할 때 **다국어 검색** 을 선택할 수 있습니다. 다음 단계에서는 다시 인덱싱하는 방법을 설명합니다.

1. [Video Indexer](https://vi.microsoft.com/) 웹 사이트로 이동하고 로그인합니다.
1. **라이브러리** 페이지로 이동하여 인덱스를 다시 만들려는 비디오의 이름을 마우스로 가리킵니다. 
1. 오른쪽 아래 모서리에서 **비디오 다시 인덱싱** 단추를 클릭합니다. 
1. **비디오 다시 인덱싱** 대화 상자의 **비디오 원본 언어** 드롭다운 상자에서 **다국어 검색** 을 선택합니다.

    * 비디오가 다국어로 인덱싱되면 인사이트 페이지에 해당 옵션이 포함되고 추가 인사이트 유형이 표시되어 사용자가 "음성 언어"로 어떤 세그먼트가 전사되었는지 볼 수 있습니다.
    * 다국어 음성 텍스트에서 모든 언어로의 번역이 가능합니다.
    * 다른 모든 인사이트는 검색된 마스터 언어로 표시됩니다. 즉, 오디오에서 가장 많이 표시되는 언어입니다.
    * 플레이어의 선택 자막도 다국어로 사용할 수 있습니다.

![포털 환경](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>API를 사용하여 인덱싱에서 다국어 식별 선택

API를 사용하여 비디오를 인덱싱하거나 [다시 인덱싱](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)할 때 `sourceLanguage` 매개 변수에서`multi-language detection` 옵션을 선택합니다.

### <a name="model-output"></a>모델 출력

이 모델은 비디오에서 검색된 모든 언어를 하나의 목록으로 검색합니다.

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

* 지원되는 언어 집합: 영어, 프랑스어, 독일어, 스페인어
* 최대 3개 언어로 다국어 콘텐츠를 지원합니다.
* 오디오에 위의 지원되는 목록 이외의 언어가 포함된 경우 예기치 못한 결과가 발생합니다.
* 각 언어를 검색할 최소 세그먼트 길이는 15초입니다.
* 언어 검색 오프셋은 평균 3초입니다.
* 음성은 연속되어야 합니다. 언어 간 잦은 교체는 모델 성능에 영향을 줄 수 있습니다.
* 기본이 아닌 스피커의 음성은 모델 성능에 영향을 줄 수 있습니다(예: 스피커가 자신의 기본 언어를 사용하고 다른 언어로 전환하는 경우).
* 이 모델은 합리적인 오디오 음향(음성 명령, 노래 등이 아님)으로 자연스러운 대화 음성을 인식하도록 설계되었습니다.
* 프로젝트 만들기 및 편집은 현재 다국어 비디오에 사용할 수 없습니다.
* 다국어 검색을 사용하는 경우 사용자 지정 언어 모델을 사용할 수 없습니다.
* 키워드 추가는 지원되지 않습니다.
* 선택 자막 파일을 내보낼 때 언어 표시가 나타나지 않습니다.
* 업데이트 음성 텍스트 API는 다국어 파일을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

[Video Indexer 개요](video-indexer-overview.md)
