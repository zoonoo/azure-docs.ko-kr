---
title: Computer Vision API의 Read v3.0으로 업그레이드
titleSuffix: Azure Cognitive Services
description: v2.0/v2.1에서 Computer Vision v3.0 읽기 API로 업그레이드하는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 5910c40729d07d5a759b2e5cc7b7a4272524c150
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91253856"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Read v2.x에서 Read v3.x로 업그레이드

이 가이드에서는 기존 컨테이너 또는 클라우드 API 코드를 Read v2.x에서 Read v3.0 및 v3.1 미리 보기로 업그레이드하는 방법을 보여 줍니다.

## <a name="determine-your-api-path"></a>API 경로 결정
다음 표를 사용하여 마이그레이션하는 Read 3.x 버전에 따라 API 경로에서 **버전 문자열**을 확인할 수 있습니다.

|제품 유형| 버전 | 3\.x API 경로의 버전 문자열 |
|:-----|:----|:----|
|서비스 | Read 3.0 | **v3.0** |
|컨테이너 | Read 3.0 미리 보기 | **v3.0** |
|서비스/컨테이너 | Read 3.1 미리 보기 | **v3.1-preview.2** |

다음으로 다음 섹션을 사용하여 작업 범위를 좁히고 API 경로의 **버전 문자열**을 테이블의 값으로 바꿉니다. 예를 들어 **Read v3.1 미리 보기** 클라우드 및 컨테이너 버전의 경우 API 경로를 **https://{endpoint}/vision/v3.1-preview.2/read/analyze[?language]** 로 업데이트합니다.

## <a name="servicecontainer"></a>서비스/컨테이너

### `Batch Read File`

|Read 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/<**version string**>/read/analyze[?language]|
    
새로운 선택적 매개 변수 _language_를 사용할 수 있습니다. 문서 언어를 모르는 경우 또는 문서 언어가 다국어일 수도 있는 경우에는 이 매개 변수를 포함하지 마세요. 

### `Get Read Results`

|Read 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/operations**/{operationId}     |https://{endpoint}/vision/<**version string**>/read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` 상태 플래그

`Get Read Operation Result` 호출이 성공하면 JSON 본문에 상태 문자열 필드가 반환됩니다.
 
|Read 2.x |Read 3.x  |
|----------|-----------|
|`"NotStarted"` |   `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>API 응답(JSON) 

Json이 다음과 같이 변경되는 것에 주의하세요.
* v2.x에서 `Get Read Operation Result`는 상태가 `Succeeded"`일 때 OCR 인식 json을 반환합니다. v3.0에서는 이 필드가 `succeeded`입니다.
* 페이지 배열의 루트를 가져오려면 json 계층을 `recognitionResults`에서 `analyzeResult`/`readResults`로 변경합니다. 페이지 단위 줄 및 단어 json 계층은 변경되지 않으므로 코드를 변경할 필요가 없습니다.
* 페이지 각도 `clockwiseOrientation`의 이름이 `angle`로 변경되었으며, 범위는 0~360도에서 -180~180도로 변경되었습니다. 대부분의 수학 함수에서 두 범위를 처리할 수 있으므로 코드에 따라 변경해야 할 수도 있고 그렇지 않을 수도 있습니다.

또한 v3.0 API에는 선택적으로 활용할 수 있는 다음과 같은 향상된 기능이 도입되었습니다.
* 처리 기간을 추적할 수 있도록 `createdDateTime` 및 `lastUpdatedDateTime`이 추가됩니다. 자세한 내용은 설명서를 참조하세요. 
* `version`은 결과를 생성하는 데 사용된 API 버전을 알려줍니다.
* 단어별 `confidence`가 추가되었습니다. 값 0.95가 인식 정확도 95%를 의미하도록 이 값이 보정됩니다. 신뢰도 점수를 사용하여 사람 검토 담당자에게 보낼 텍스트를 선택할 수 있습니다. 
    
    
2\.X에서 출력 형식은 다음과 같습니다. 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
v3.0에서는 다음과 같이 조정되었습니다.
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>서비스 전용

### `Recognize Text`
`Recognize Text`는 *모든 버전의 Computer Vision API에서 사용이 중지되는* *미리 보기* 작업입니다. `Recognize Text`에서 `Read`(v3.0)로 또는 `Batch Read File`(v2.0, v2.1)로 마이그레이션해야 합니다. 개선된 새 텍스트 인식 모델과 추가 기능이 포함되어 있는 v3.0의 `Read`를 사용하는 것이 좋습니다. `Recognize Text`에서 `Read`로 업그레이드하는 방법은 다음과 같습니다.

|Recognize Text 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/<**version string**>/read/analyze[?language]|
    
_mode_ 매개 변수는 `Read`에서 지원되지 않습니다. 필기 텍스트와 인쇄된 텍스트가 모두 자동으로 지원됩니다.
    
v3.0에서는 새로운 선택적 매개 변수 _language_를 사용할 수 있습니다. 문서 언어를 모르는 경우 또는 문서 언어가 다국어일 수도 있는 경우에는 이 매개 변수를 포함하지 마세요. 

### `Get Recognize Text Operation Result`

|Recognize Text 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/textOperations/** {operationId}|https://{endpoint}/vision/<**version string**>/read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` 상태 플래그
`Get Recognize Text Operation Result` 호출이 성공하면 JSON 본문에 상태 문자열 필드가 반환됩니다. 
 
|Recognize Text 2.x |Read 3.x  |
|----------|-----------|
|`"NotStarted"` |   `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>API 응답(JSON)

Json이 다음과 같이 변경되는 것에 주의하세요.    
* v2.x에서 `Get Read Operation Result`는 상태가 `Succeeded`일 때 OCR 인식 json을 반환합니다. v3.x에서 이 필드는 `succeeded`입니다.
* 페이지 배열의 루트를 가져오려면 json 계층을 `recognitionResult`에서 `analyzeResult`/`readResults`로 변경합니다. 페이지 단위 줄 및 단어 json 계층은 변경되지 않으므로 코드를 변경할 필요가 없습니다.

또한 v3.0 API에는 선택적으로 활용할 수 있는 다음과 같은 향상된 기능이 도입되었습니다. 자세한 내용은 API 참조를 참조하세요.
* 처리 기간을 추적할 수 있도록 `createdDateTime` 및 `lastUpdatedDateTime`이 추가됩니다. 자세한 내용은 설명서를 참조하세요. 
* `version`은 결과를 생성하는 데 사용된 API 버전을 알려줍니다.
* 단어별 `confidence`가 추가되었습니다. 값 0.95가 인식 정확도 95%를 의미하도록 이 값이 보정됩니다. 신뢰도 점수를 사용하여 사람 검토 담당자에게 보낼 텍스트를 선택할 수 있습니다. 
* 시계 방향 텍스트의 `angle` 일반 방향은 (-180, 180]도 사이에서 측정됩니다.
* `width` 및 `"height"`는 문서 크기를 제공하고, `"unit"`은 이러한 크기(문서 종류에 따라 픽셀 또는 인치)의 단위를 제공합니다.
* `page` 다중 페이지 문서가 지원됩니다.
* `language` 선택적 _language_ 매개 변수의 문서 입력 언어입니다.


2\.X에서 출력 형식은 다음과 같습니다. 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
v3.x에서는 다음과 같이 조정되었습니다.
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>컨테이너만

### `Synchronous Read`

|Read 2.0 |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/Analyze**     |https://{endpoint}/vision/<**version string**>/read/syncAnalyze[?language]|
