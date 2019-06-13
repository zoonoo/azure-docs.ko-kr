---
title: 언어 지원-Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision 기능을 지원하는 자연어의 목록입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012667"
---
# <a name="language-support-for-computer-vision"></a>Computer Vision에 대한 언어 지원

Computer Vision의 일부 기능은 여러 언어를 지원합니다. 여기서 언급되지 않은 기능은 영어만 지원합니다.

## <a name="text-recognition"></a>텍스트 인식

Computer Vision은 다양한 언어의 텍스트를 인식할 수 있습니다. 특히 [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API는 다양한 언어를 지원하는 반면, [읽기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API 및 [텍스트 인식](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API는 영어만 지원합니다. 각 API의 장점과 기능에 대한 자세한 내용은 [인쇄 및 필기 텍스트 인식](concept-recognizing-text.md)를 참조합니다.

API 호출에서 언어 코드를 지정 하지 않아도 되므로 OCR 입력 내용의 언어를 자동으로 검색 합니다. 그러나 언어 코드는 항상 값으로 반환 된 `"language"` JSON 응답에서 노드.

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
|세르비아어(키릴자모) | `sr-Cyrl` |✔ |
|세르비아어(라틴 문자) | `sr-Latn` |✔ |
|슬로바키아어 | `sk` |✔ |
|스페인어 | `es` |✔ |
|스웨덴어 | `sw` |✔ |
|터키어 | `tr` |✔ |

## <a name="image-analysis"></a>이미지 분석

일부 [이미지-분석](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API의 작업은 `language` 쿼리 매개 변수로 지정된 다른 언어로 결과를 반환할 수 있습니다. 어떤 작업은 어떤 언어를 지정하는가에 관계 없이 영어로 결과를 반환하고 어떤 작업은 지원되지 않는 언어에 대해 예외를 발생시킵니다. 작업은 `visualFeatures` 및 `details` 쿼리 매개 변수를 사용하여 지정합니다. 이미지 분석을 사용하여 할 수 있는 모든 작업의 목록은 [개요](home.md) 를 참조하세요.

|언어 | 언어 코드 | Categories | 태그들 | 설명 | 성인 | 브랜드 | 색 | 얼굴 | ImageType | 개체 | 유명인 | 랜드마크 |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|중국어 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|영어 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|일본어 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|포르투갈어 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|스페인어 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>다음 단계

이 가이드에서 설명한 Computer Vision 기능을 사용하여 시작합니다.

* [로컬 이미지 (REST)를 분석 합니다.](./quickstarts/csharp-analyze.md)
* [인쇄된 텍스트(REST) 추출](./quickstarts/csharp-print-text.md)