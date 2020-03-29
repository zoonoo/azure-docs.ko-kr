---
title: 언어 지원 - 컴퓨터 비전
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 컴퓨터 비전 기능에서 지원하는 자연어 목록을 제공합니다. OCR, 텍스트 인식 및 읽기.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221356"
---
# <a name="language-support-for-computer-vision"></a>컴퓨터 비전을 위한 언어 지원

컴퓨터 비전의 일부 기능은 여러 언어를 지원합니다. 여기에 언급되지 않은 모든 기능은 영어만 지원합니다.

## <a name="text-recognition"></a>텍스트 인식

컴퓨터 비전은 여러 언어로 텍스트를 인식할 수 있습니다. 특히 [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API는 다양한 언어를 지원하는 반면 [읽기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API 및 [인식 텍스트](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API는 영어만 지원합니다. 이 기능및 각 API의 이점에 대한 자세한 내용은 [인쇄 및 필기 텍스트 인식을](concept-recognizing-text.md) 참조하십시오.

OCR은 입력 자료의 언어를 자동으로 감지하므로 API 호출에서 언어 코드를 지정할 필요가 없습니다. 그러나 언어 코드는 항상 JSON `"language"` 응답에서 노드의 값으로 반환됩니다.

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

Analyze - [이미지](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API의 일부 작업은 쿼리 매개 변수로 `language` 지정된 다른 언어로 결과를 반환할 수 있습니다. 다른 작업은 지정된 언어에 관계없이 영어로 결과를 반환하고 다른 작업은 지원되지 않는 언어에 대한 예외를 throw합니다. 작업은 `visualFeatures` 및 `details` 쿼리 매개 변수로 지정됩니다. 이미지 분석을 통해 수행할 수 있는 모든 작업 목록은 [개요를](home.md) 참조하십시오.

|언어 | 언어 코드 | 범주 | 태그들 | 설명 | 성인 | 브랜드 | 색 | 얼굴 | ImageType | 개체 | 유명인 | 랜드마크 |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|중국어 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|영어 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|일본어 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|포르투갈어 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|스페인어 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>다음 단계

이 가이드에 언급된 컴퓨터 비전 기능을 사용하기 시작하십시오.

* [로컬 이미지 분석(REST)](./quickstarts/csharp-analyze.md)
* [인쇄된 텍스트 추출(REST)](./quickstarts/csharp-print-text.md)