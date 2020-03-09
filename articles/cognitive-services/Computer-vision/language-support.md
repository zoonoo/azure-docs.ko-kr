---
title: 언어 지원-Computer Vision
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Computer Vision 기능에서 지 원하는 자연어 목록을 제공 합니다. OCR, 텍스트 인식 및 읽기
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365478"
---
# <a name="language-support-for-computer-vision"></a>Computer Vision에 대한 언어 지원

Computer Vision의 일부 기능은 여러 언어를 지원합니다. 여기서 언급되지 않은 기능은 영어만 지원합니다.

## <a name="text-recognition"></a>텍스트 인식

Computer Vision은 다양한 언어의 텍스트를 인식할 수 있습니다. 특히, [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) api는 다양 한 언어를 지원 하지만, [읽기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) api 및 [텍스트 인식](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) api는 영어만 지원 합니다. 이 기능과 각 API의 이점에 대 한 자세한 내용은 [인쇄 및 필기 텍스트 인식](concept-recognizing-text.md) 을 참조 하세요.

OCR은 입력 자료의 언어를 자동으로 검색 하므로 API 호출에서 언어 코드를 지정할 필요가 없습니다. 그러나 언어 코드는 항상 JSON 응답에서 `"language"` 노드의 값으로 반환 됩니다.

|언어| 언어 코드 | OCR API |
|:-----|:----:|:-----:|
|아랍어 | `ar`|✔ |
|중국어 (간체) | `zh-Hans`|✔ |
|중국어 (번체) | `zh-Hant`|✔ |
|체코어 | `cs` |✔ |
|덴마크어 | `da` |✔ |
|네덜란드어 | `nl` |✔ |
|영어 | `en` |✔ |
|핀란드어 | `fi` |✔ |
|프랑스어 | `fr` |✔ |
|독일어 | `de` |✔ |
|그리스어 | `el` |✔ |
|헝가리어 | `hu` |✔ |
|이탈리아어 | `it` |✔ |
|일본어 | `ja` |✔ |
|한국어 | `ko` |✔ |
|노르웨이어 | `nb` |✔ |
|폴란드어 | `pl` |✔ |
|포르투갈어 | `pt` |✔ |
|루마니아어 | `ro` |✔ |
|러시아어 | `ru` |✔ |
|세르비아어(키릴 자모) | `sr-Cyrl` |✔ |
|세르비아어(라틴 문자) | `sr-Latn` |✔ |
|슬로바키아어 | `sk` |✔ |
|스페인어 | `es` |✔ |
|스웨덴어 | `sw` |✔ |
|터키어 | `tr` |✔ |

## <a name="image-analysis"></a>이미지 분석

[분석-이미지](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API의 일부 작업은 `language` 쿼리 매개 변수로 지정 된 다른 언어로 결과를 반환할 수 있습니다. 어떤 작업은 어떤 언어를 지정하는가에 관계 없이 영어로 결과를 반환하고 어떤 작업은 지원되지 않는 언어에 대해 예외를 발생시킵니다. 작업은 `visualFeatures` 및 `details` 쿼리 매개 변수로 지정 됩니다. 이미지 분석으로 수행할 수 있는 모든 작업 목록은 [개요](home.md) 를 참조 하세요.

|언어 | 언어 코드 | 범주 | 태그들 | Description | 성인 | 브랜드 | 색 | 얼굴 | ImageType | 개체 | 유명인 | 랜드마크 |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|중국어 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|영어 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|일본어 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|포르투갈어 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|스페인어 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>다음 단계

이 가이드에서 설명한 Computer Vision 기능을 사용하여 시작합니다.

* [로컬 이미지 분석 (REST)](./quickstarts/csharp-analyze.md)
* [인쇄 된 텍스트 추출 (REST)](./quickstarts/csharp-print-text.md)