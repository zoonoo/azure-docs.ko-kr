---
title: V2 API에 의해 생성 된 Azure Media Services Video Indexer 출력을 검사 합니다.
titleSuffix: Azure Media Services
description: 이 항목에서는 v2 API에 의해 생성 된 Azure Media Services Video Indexer 출력을 검사 합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/09/2019
ms.author: juliako
ms.openlocfilehash: c978fed1675ea80ae9b2f6fb7fbe9a4c84472638
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978307"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>API에 의해 생성 된 Video Indexer 출력 검사

**비디오 인덱스 가져오기** API를 호출 하 고 응답 상태가 양호 이면 응답 콘텐츠로 자세한 JSON 출력을 가져옵니다. JSON 콘텐츠에는 지정 된 video insights에 대 한 세부 정보가 포함 됩니다. 이 정보에는 성적 증명서, OCRs, 얼굴, 토픽, 블록 등의 차원이 포함 됩니다. 차원에는 각 차원이 비디오에 표시 되는 시간 범위 인스턴스가 있습니다.  

1. JSON 파일을 검색 하려면 [비디오 인덱스 API 가져오기](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?) 를 호출 합니다.
1. 특정 아티팩트에도 관심이 있는 경우 [비디오 아티팩트 가져오기 다운로드 URL API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?) 를 호출 합니다.

    API 호출에서 요청 된 아티팩트 형식 (OCR, 얼굴, 키 프레임 등)을 지정 합니다.

[Video Indexer](https://www.videoindexer.ai/) 웹 사이트의 비디오에서 **재생** 단추를 눌러 비디오의 요약 된 정보를 시각적으로 검사할 수도 있습니다. 자세한 내용은 [비디오 정보 보기 및 편집](video-indexer-view-edit.md)을 참조 하세요.

![Elemzés](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

이 문서에서는 **비디오 인덱스 가져오기** API에서 반환 된 JSON 콘텐츠를 검사 합니다. 

> [!NOTE]
> Video Indexer에 있는 모든 액세스 토큰의 만료는 1 시간입니다.


## <a name="root-elements"></a>루트 요소

|Név|Leírás|
|---|---|
|accountId|재생 목록의 VI 계정 ID입니다.|
|id|재생 목록의 ID입니다.|
|név|재생 목록 이름입니다.|
|leírás|재생 목록 설명입니다.|
|userName|재생 목록을 만든 사용자의 이름입니다.|
|만들어지며|재생 목록의 만든 시간입니다.|
|privacyMode|재생 목록의 개인 정보 모드 (개인/공용)입니다.|
|state|재생 목록 (업로드 됨, 처리 중, 처리 됨, 실패, 격리)|
|isOwned|현재 사용자가 재생 목록을 만들었는지 여부를 나타냅니다.|
|isEditable|현재 사용자에 게 재생 목록을 편집할 수 있는 권한이 있는지 여부를 나타냅니다.|
|isBase|재생 목록이 기본 재생 목록 (비디오) 또는 다른 비디오 (파생 된)의 재생 목록 인지 여부를 나타냅니다.|
|durationInSeconds|재생 목록의 총 재생 시간입니다.|
|summarizedInsights|하나의 [summarizedInsights](#summarizedinsights)를 포함 합니다.
|videók|재생 목록을 생성 하는 [비디오](#videos) 목록입니다.<br/>이 재생 목록에서 다른 비디오 (파생)의 시간 범위를 만든 경우이 목록의 비디오에는 포함 된 시간 범위의 데이터만 포함 됩니다.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

이 섹션에서는 정보 요약을 보여 줍니다.

|Attribútum | Leírás|
|---|---|
|név|비디오의 이름입니다. 예를 들어 Azure Monitor 합니다.|
|id|비디오의 ID입니다. 예를 들면 63c6d532ff입니다.|
|privacyMode|분석에는 **Private**, **Public**모드 중 하나를 사용할 수 있습니다. **공개** -비디오에 대 한 링크를 가진 모든 사용자에 게 비디오를 표시 합니다. **비공개** -계정의 모든 사용자가 비디오를 볼 수 있습니다.|
|duration|정보가 포함 된 시간을 설명 하는 한 가지 기간을 포함 합니다. 기간 (초)입니다.|
|thumbnailVideoId|미리 보기가 만들어진 비디오의 ID입니다.
|thumbnailId|비디오의 미리 보기 ID입니다. 실제 미리 보기를 가져오려면 [get-help](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) 를 호출 하 고 ThumbnailVideoId 및 thumbnailId를 전달 합니다.|
|표면|0 개 이상의 얼굴을 포함할 수 있습니다. 자세한 내용은 [얼굴](#faces)을 참조 하세요.|
|키워드|에는 0 개 이상의 키워드가 포함 될 수 있습니다. 자세한 내용은 [키워드](#keywords)를 참조 하세요.|
|정서|정서을 0 개 이상 포함할 수 있습니다. 자세한 내용은 [정서](#sentiments)를 참조 하세요.|
|오디오 효과| 0 개 이상의 오디오 효과를 포함할 수 있습니다. 자세한 내용은 [오디오 효과](#audioEffects)를 참조 하세요.|
|레이블에| 0 개 이상의 레이블을 포함할 수 있습니다. 자세한 내용은 [레이블](#labels)을 참조 하세요.|
|상표| 0 개 이상의 브랜드를 포함할 수 있습니다. 자세한 내용은 [브랜드](#brands)를 참조 하세요.|
|통계 | 자세한 내용은 [통계](#statistics)를 참조 하세요.|
|감정을| 감정을을 0 개 이상 포함할 수 있습니다. 자세한 내용은 [감정을](#emotions)를 참조 하세요.|
|témakör|에는 0 개 이상의 항목이 포함 될 수 있습니다. [항목](#topics) 차원입니다.|

## <a name="videos"></a>videók

|Név|Leírás|
|---|---|
|accountId|비디오의 VI 계정 ID입니다.|
|id|비디오 ID입니다.|
|név|비디오 이름입니다.
|state|비디오의 상태 (업로드 됨, 처리 중, 처리 됨, 실패, 격리)입니다.|
|processingProgress|처리 중 처리 진행률 (예: 20%)입니다.|
|failureCode|에서 처리 하지 못한 경우의 오류 코드입니다 (예: ' UnsupportedFileType ').|
|failureMessage|처리 하지 못한 경우의 오류 메시지입니다.|
|externalId|사용자가 지정한 비디오의 외부 ID입니다.|
|externalUrl|사용자가 지정한 비디오의 외부 url입니다.|
|metaadatok|사용자가 지정한 비디오의 외부 메타 데이터입니다.|
|isAdult|비디오를 수동으로 검토 하 고 성인 비디오로 식별 했는지 여부를 나타냅니다.|
|Insights|Insights 개체입니다. 자세한 내용은 [insights](#insights)를 참조 하세요.|
|thumbnailId|비디오의 미리 보기 ID입니다. 실제 미리 보기 호출을 가져오려면 [축소판 그림](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) 을 표시 하 고 비디오 ID와 thumbnailId을 전달 합니다.|
|publishedUrl|비디오를 스트림 하는 url입니다.|
|publishedUrlProxy|비디오를 스트리밍할 url입니다 (Apple 장치의 경우).|
|viewToken|비디오 스트리밍을 위한 수명이 짧은 보기 토큰입니다.|
|sourceLanguage|비디오의 소스 언어입니다.|
|language|비디오의 실제 언어 (변환)입니다.|
|indexingPreset|비디오를 인덱싱하는 데 사용 되는 기본 설정입니다.|
|streamingPreset|비디오를 게시 하는 데 사용 되는 기본 설정입니다.|
|linguisticModelId|CRIS 모델은 비디오를 높여줄 하는 데 사용 됩니다.|
|통계 | 자세한 내용은 [통계](#statistics)를 참조 하세요.|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>Insights

이 정보는 일련의 차원 (예: 성적 증명서, 면, 브랜드 등) 이며 각 차원은 고유한 요소 목록 (예: face1, face2, face3) 이며 각 요소에는 자체 메타 데이터와 해당 인스턴스 목록 (시간 범위)이 있습니다. 추가 선택적 메타 데이터).

한 면에 ID, 이름, 미리 보기, 기타 메타 데이터 및 임시 인스턴스 목록 (예: 00:00:05 – 00:00:10, 00:01:00-00:02:30 및 00:41:21 – 00:41:49)이 있을 수 있습니다. 각 임시 인스턴스에는 추가 메타 데이터가 있을 수 있습니다. 예를 들어 얼굴의 사각형 좌표 (20230, 60, 60)입니다.

|Verzió|코드 버전|
|---|---|
|sourceLanguage|비디오의 소스 언어입니다 (한 마스터 언어 라고 가정). [BCP-47](https://tools.ietf.org/html/bcp47) 문자열의 형식입니다.|
|language|Insights 언어 (소스 언어에서 변환)입니다. [BCP-47](https://tools.ietf.org/html/bcp47) 문자열의 형식입니다.|
|대 본|[성적 증명서](#transcript) 차원입니다.|
|ocr|[OCR](#ocr) 차원입니다.|
|키워드|[키워드](#keywords) 차원입니다.|
|blocks|하나 이상의 [블록](#blocks) 을 포함할 수 있습니다.|
|표면|[면](#faces) 차원입니다.|
|레이블에|[레이블](#labels) 차원입니다.|
|피해|[샷](#shots) 차원입니다.|
|상표|[브랜드](#brands) 차원입니다.|
|오디오 효과|[오디오 효과](#audioEffects) 차원입니다.|
|정서|[정서](#sentiments) 차원입니다.|
|visualContentModeration|[Visualcontentmoderation](#visualcontentmoderation) 차원입니다.|
|textualContentModeration|[TextualContentModeration](#textualcontentmoderation) 차원입니다.|
|감정을| [감정을](#emotions) 차원입니다.|
|témakör|[항목](#topics) 차원입니다.|

Példa:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blocks

Attribútum | Leírás
---|---
id|블록의 ID입니다.|
példányok|이 블록의 시간 범위 목록입니다.|

#### <a name="transcript"></a>대 본

|Név|Leírás|
|---|---|
|id|줄 ID입니다.|
|szöveg|인 증명서입니다.|
|language|성적 증명서 언어입니다. 각 줄이 서로 다른 언어를 사용할 수 있는 기록을 지원 하기 위한 것입니다.|
|példányok|이 줄이 나타난 시간 범위 목록입니다. 인스턴스가 성적 증명서 인 경우 인스턴스는 1 개만 있습니다.|

Példa:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

#### <a name="ocr"></a>ocr

|Név|Leírás|
|---|---|
|id|OCR 줄 ID입니다.|
|szöveg|OCR 텍스트입니다.|
|megbízhatóság|인식 신뢰도입니다.|
|language|OCR 언어입니다.|
|példányok|이 OCR이 표시 되는 시간 범위 목록입니다 (동일한 OCR이 여러 번 표시 될 수 있음).|
|magasság|OCR 사각형의 높이입니다.|
|top|Px의 위쪽 위치입니다.|
|왼쪽| Px의 왼쪽 위치입니다.|
|szélesség|OCR 사각형의 너비입니다.|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>키워드

|Név|Leírás|
|---|---|
|id|키워드 ID입니다.|
|szöveg|키워드 텍스트입니다.|
|megbízhatóság|키워드의 인식 신뢰도입니다.|
|language|키워드 언어 (번역 된 경우)입니다.|
|példányok|이 키워드가 표시 되는 시간 범위 목록 (키워드는 여러 번 표시 될 수 있음)|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="faces"></a>표면

|Név|Leírás|
|---|---|
|id|얼굴 ID입니다.|
|név|면의 이름입니다. ' 알 수 없는 #0, 식별 된 유명인 또는 고객이 학습 한 사용자 일 수 있습니다.|
|megbízhatóság|얼굴 식별 신뢰도입니다.|
|leírás|유명인에 대 한 설명입니다. |
|thumbnailId|해당 얼굴의 미리 보기 ID입니다.|
|knownPersonId|알려진 사용자 인 경우 내부 ID입니다.|
|referenceId|Bing 유명인 인 경우 Bing ID입니다.|
|referenceType|현재 Bing만 있으면 됩니다.|
|title|유명인 인 경우 제목 (예: "Microsoft CEO")입니다.|
|imageUrl|유명인 경우 해당 이미지 url입니다.|
|példányok|지정 된 시간 범위 내에서 얼굴을 표시 하는 인스턴스입니다. 각 인스턴스에는 thumbnailsId도 있습니다. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

#### <a name="labels"></a>레이블에

|Név|Leírás|
|---|---|
|id|레이블 ID입니다.|
|név|레이블 이름 (예: ' Computer ', ' TV ')입니다.|
|language|레이블 이름 언어 (번역 된 경우)입니다. BCP-47|
|példányok|이 레이블이 나타난 시간 범위 목록입니다. 레이블은 여러 번 나타날 수 있습니다. 각 인스턴스에는 신뢰도 필드가 있습니다. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

#### <a name="scenes"></a>내부적

|Név|Leírás|
|---|---|
|id|장면 ID입니다.|
|példányok|이 장면의 시간 범위 목록입니다 (장면에는 인스턴스가 1 개만 있을 수 있음).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>피해

|Név|Leírás|
|---|---|
|id|샷 ID입니다.|
|키프레임에서|샷 내의 키 프레임 목록 (각각 ID와 인스턴스 시간 범위의 목록이 있음). 각 키 프레임 인스턴스에는 키 프레임의 미리 보기 ID를 포함 하는 thumbnailId 필드가 있습니다.|
|példányok|이 샷의 시간 범위 목록입니다 (샷의 인스턴스는 1 개만 있을 수 있음).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>상표

음성에서 텍스트 기록 및/또는 비디오 OCR로 검색 된 비즈니스 및 제품 브랜드 이름. 브랜드 또는 로고 검색의 시각적 인식이 포함 되지 않습니다.

|Név|Leírás|
|---|---|
|id|브랜드 ID입니다.|
|név|브랜드 이름입니다.|
|referenceId | 브랜드 위키백과 url의 접미사입니다. 예를 들어 "Target_Corporation"는 [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation)의 접미사입니다.
|referenceUrl | 브랜드의 위키백과 url입니다 (있는 경우). Például: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|leírás|브랜드 설명입니다.|
|tags|이 브랜드와 연결 된 미리 정의 된 태그 목록입니다.|
|megbízhatóság|Video Indexer 브랜드 탐지기의 신뢰도 값 (0-1)입니다.|
|példányok|이 브랜드의 시간 범위 목록입니다. 각 인스턴스에는이 브랜드가 성적 증명서에 표시 되는지 또는 OCR로 표시 되는지 여부를 나타내는 brandType이 있습니다.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>통계

|Név|Leírás|
|---|---|
|CorrespondenceCount|비디오의 correspondences 수입니다.|
|SpeakerWordCount|스피커 당 단어 수입니다.|
|SpeakerNumberOfFragments|스피커가 비디오에 있는 조각의 양입니다.|
|SpeakerLongestMonolog|스피커의 가장 긴 monolog입니다. 스피커가 monolog 내에 silences 경우 포함 됩니다. Monolog의 시작과 끝에 소리가 제거 됩니다.| 
|SpeakerTalkToListenRatio|계산은 비디오의 총 시간으로 나눈 스피커의 monolog (사이에 소리가 들리지 않음)에 소요 된 시간을 기준으로 합니다. 시간이 세 번째 소수점으로 반올림 됩니다.|

#### <a name="a-idaudioeffectsaudioeffects"></a><a id="audioEffects"/>audioEffects

|Név|Leírás|
|---|---|
|id|오디오 효과 ID입니다.|
|type|오디오 효과 유형 (예: 박수, Speech, 무음)입니다.|
|példányok|이 오디오 효과가 나타나는 시간 범위 목록입니다.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>정서

정서는 sentimentType 필드로 집계 됩니다 (긍정/중립/부정). 예를 들어 0-0.1, 0.1-0.2가 있습니다.

|Név|Leírás|
|---|---|
|id|감정 ID입니다.|
|averageScore |감정 type-긍정/중립/부정의 모든 인스턴스에 대 한 모든 점수의 평균|
|példányok|이 감정 나타난 시간 범위 목록입니다.|
|sentimentType |형식은 ' 긍정 ', ' 중립 ' 또는 ' 음수 '가 될 수 있습니다.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

VisualContentModeration 블록에는 잠재적으로 성인 콘텐츠가 있을 수 Video Indexer 있는 시간 범위가 포함 되어 있습니다. VisualContentModeration가 비어 있는 경우 식별 된 성인 콘텐츠가 없습니다.

성인 또는 외설 콘텐츠를 포함 하는 비디오는 전용 보기 전용으로 사용할 수 있습니다. 사용자는 콘텐츠에 대 한 사람의 검토 요청을 제출 하는 옵션을 사용할 수 있습니다 .이 경우 IsAdult 특성에는 사람이 검토할 결과가 포함 됩니다.

|Név|Leírás|
|---|---|
|id|시각적 콘텐츠 중재 ID입니다.|
|adultScore|성인 점수 (콘텐츠 중재자의)입니다.|
|racyScore|외설 점수 (콘텐츠 조정에서)입니다.|
|példányok|이 시각적 콘텐츠 조정 기능이 표시 되는 시간 범위 목록입니다.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Név|Leírás|
|---|---|
|id|텍스트 콘텐츠 중재 ID입니다.|
|bannedWordsCount |금지 된 단어의 수입니다.|
|bannedWordsRatio |총 단어 수의 비율입니다.|

#### <a name="emotions"></a>감정을

Video Indexer는 음성 및 오디오 신호를 기반으로 감정을를 식별 합니다. 식별 되는 emotion는 분노, sadness 또는 일 수 있습니다.

|Név|Leírás|
|---|---|
|id|Emotion ID입니다.|
|type|음성 및 오디오 신호를 기반으로 식별 된 emotion 순간입니다. Emotion, sadness, 또는 그에 대 한 것입니다.|
|példányok|이 emotion 나타난 시간 범위 목록입니다.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>témakör

Video Indexer는 기록에서 주요 항목의 유추를 수행 합니다. 가능 하면 두 번째 수준 [IPTC](https://iptc.org/standards/media-topics/) 분류가 포함 됩니다. 

|Név|Leírás|
|---|---|
|id|항목 ID입니다.|
|név|항목 이름입니다 (예: "Pharmaceuticals").|
|referenceId|토픽 계층 구조를 반영 하는 이동 경로입니다. 예: "Health and 웰빙/의약품 and 의료/Pharmaceuticals".|
|megbízhatóság|[0, 1] 범위의 신뢰도 점수입니다. 더 높은 것이 더 확실 합니다.|
|language|항목에서 사용 되는 언어입니다.|
|iptcName|검색 된 경우 IPTC 미디어 코드 이름입니다.|
|példányok |현재 Video Indexer는 항목을 시간 간격으로 인덱싱하지 않으므로 전체 비디오가 간격으로 사용 됩니다.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

## <a name="next-steps"></a>Következő lépések

[Video Indexer 개발자 포털](https://api-portal.videoindexer.ai)

응용 프로그램에 위젯을 포함 하는 방법에 대 한 자세한 내용은 [응용 프로그램에 Video Indexer 위젯 포함](video-indexer-embed-widgets.md)을 참조 하세요. 

