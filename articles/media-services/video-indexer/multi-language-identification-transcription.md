---
title: Video Indexer를 사용 하 여 자동으로 다국어 콘텐츠 식별 및 높여줄
titleSuffix: Azure Media Services
description: 이 항목에서는 Video Indexer를 사용 하 여 다국어 콘텐츠를 자동으로 식별 하 고 높여줄 하는 방법을 보여 줍니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 983b66be6b51c9af5987e539ea1175a65c9269b3
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862045"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>자동으로 다국어 콘텐츠 식별 및 높여줄 (미리 보기)

Video Indexer은 다중 언어 콘텐츠에서 자동 언어 식별 및 기록을 지원 합니다. 이 프로세스는 오디오의 여러 세그먼트에서 음성 언어를 자동으로 식별 하 여 미디어 파일의 각 세그먼트를 transcribed으로 보내고 기록을 다시 하나의 통합 된 기록으로 결합 합니다. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>포털을 사용 하 여 인덱싱에서 다국어 id 선택

비디오를 업로드 하 고 인덱싱할 때 **다국어 검색** 을 선택할 수 있습니다. 또는 비디오를 다시 인덱싱할 때 **다국어 검색** 을 선택할 수 있습니다. 다음 단계에서는 인덱스를 수행 하는 방법을 설명 합니다.

1. [Video Indexer](https://vi.microsoft.com/) 웹 사이트로 이동하고 로그인합니다.
1. **라이브러리** 페이지로 이동 하 여 인덱스를 다시 만들려는 비디오의 이름을 마우스로 가리킵니다. 
1. 오른쪽 아래 모서리에서 **인덱스 다시 설정 비디오** 단추를 클릭 합니다. 
1. **인덱스 다시** 입력 대화 상자의 **비디오 원본 언어** 드롭다운 상자에서 **다국어 검색** 을 선택 합니다.

    * 비디오가 다중 언어로 인덱싱되는 경우 정보 제공 페이지에 해당 옵션이 포함 되 고 추가 정보 형식이 표시 되므로 사용자는 어떤 transcribed 어떤 언어로 "음성 언어"를 사용 하는지 확인할 수 있습니다.
    * 모든 언어로 번역은 다중 언어 성적 증명서에서 완전히 사용할 수 있습니다.
    * 다른 모든 정보는 검색 된 마스터 언어로 표시 됩니다. 즉, 오디오에서 가장 많이 표시 되는 언어입니다.
    * 플레이어의 폐쇄 캡션을 다국어 에서도 사용할 수 있습니다.

![포털 환경](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>API를 사용 하 여 인덱싱에서 다국어 id 선택

API를 사용 하 여 [비디오를 인덱싱하거나 인덱스를](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 인덱싱할 때 `multi-language detection` `sourceLanguage` 매개 변수에서 옵션을 선택 합니다.

### <a name="model-output"></a>모델 출력

이 모델은 비디오에서 검색 된 모든 언어를 한 목록에 검색 합니다.

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

또한 기록 섹션의 각 인스턴스에는 transcribed 된 언어가 포함 됩니다.

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

* 지원 되는 언어 집합: 영어, 프랑스어, 독일어, 스페인어
* 지원 되는 최대 3 개의 언어를 갖춘 다국어 콘텐츠 지원
* 오디오가 위의 지원 되는 목록 이외의 언어를 포함 하는 경우 예기치 않은 결과가 발생 합니다.
* 각 언어를 검색할 최소 세그먼트 길이 – 15 초
* 언어 검색 오프셋은 평균 3 초입니다.
* 음성은 연속 된 것으로 예상 됩니다. 언어 간 잦은 구분할은 모델 성능에 영향을 줄 수 있습니다.
* 기본이 아닌 스피커의 음성은 모델 성능에 영향을 줄 수 있습니다. 예를 들어 스피커가 자신의 기본 메롱를 사용 하 고 다른 언어로 전환 하는 경우입니다.
* 모델은 적절 한 오디오 acoustics (음성 명령, 노래 등)를 사용 하 여 갑작스런 대화형 음성을 인식 하도록 설계 되었습니다.
* 프로젝트 만들기 및 편집은 현재 다국어 비디오에 사용할 수 없습니다.
* 다국어 검색을 사용 하는 경우 사용자 지정 언어 모델을 사용할 수 없습니다.
* 키워드 추가는 지원 되지 않습니다.
* "업데이트 기록" API는 지원 되지 않습니다.
* 닫힌 캡션 파일을 내보낼 때 언어 표시가 나타나지 않습니다.


## <a name="next-steps"></a>다음 단계

[Video Indexer 개요](video-indexer-overview.md)
