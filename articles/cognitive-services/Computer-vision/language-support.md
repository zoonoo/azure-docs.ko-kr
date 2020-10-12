---
title: 언어 지원-Computer Vision
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Computer Vision 기능에서 지 원하는 자연어 목록을 제공 합니다. OCR, 이미지 분석.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 844ef8c09f10e1df888ef4990db22be8705d5c01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530202"
---
# <a name="language-support-for-computer-vision"></a>Computer Vision에 대 한 언어 지원

Computer Vision의 일부 기능은 여러 언어를 지원 합니다. 여기서 언급 하지 않은 기능은 영어만 지원 합니다.

## <a name="optical-character-recognition-ocr"></a>OCR(광학 문자 인식)

Computer Vision의 OCR Api는 여러 언어를 지원 합니다. 언어 코드를 지정 하지 않아도 됩니다. 자세한 내용은 [OCR (광학 문자 인식)](concept-recognizing-text.md) 을 참조 하세요.

|언어| 언어 코드 | OCR API | V 3.0 읽기 | 읽기 v 3.1 공개 미리 보기 |
|:-----|:----:|:-----:|:---:|:---:|
|아랍어 | `ar`|✔ | | |
|중국어(간체) | `zh-Hans`|✔ | |✔ |
|중국어(번체) | `zh-Hant`|✔ | | |
|체코어 | `cs` |✔ | | |
|덴마크어 | `da` |✔ | | |
|네덜란드어 | `nl` |✔ |✔ |✔ |
|영어 | `en` |✔ |✔ |✔ |
|핀란드어 | `fi` |✔ | | |
|프랑스어 | `fr` |✔ |✔ |✔ |
|독일어 | `de` |✔ |✔ |✔ |
|그리스어 | `el` |✔ | | |
|헝가리어 | `hu` |✔ | | |
|이탈리아어 | `it` |✔ |✔ |✔ |
|일본어 | `ja` |✔ | |✔ |
|한국어 | `ko` |✔ | | |
|노르웨이어 | `nb` |✔ | | |
|폴란드어 | `pl` |✔ | | |
|포르투갈어 | `pt` |✔ |✔ |✔ |
|루마니아어 | `ro` |✔ | | |
|러시아어 | `ru` |✔ | | |
|세르비아어(키릴 자모) | `sr-Cyrl` |✔ | | |
|세르비아어(라틴 문자) | `sr-Latn` |✔ | | |
|슬로바키아어 | `sk` |✔ | | |
|스페인어 | `es` |✔ |✔ |✔ |
|스웨덴어 | `sw` |✔ | | |
|터키어 | `tr` |✔ | | |

## <a name="image-analysis"></a>이미지 분석

[분석-이미지](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API의 일부 작업은 쿼리 매개 변수로 지정 된 다른 언어로 결과를 반환할 수 있습니다 `language` . 다른 작업을 수행 하면 지정 된 언어에 관계 없이 영어로 결과가 반환 되 고 다른 작업은 지원 되지 않는 언어에 대 한 예외를 throw 합니다. 작업은 및 쿼리 매개 변수를 사용 하 여 지정 됩니다 `visualFeatures` `details` . 이미지 분석을 통해 수행할 수 있는 모든 작업 목록은 [개요](overview.md) 를 참조 하세요.

|언어 | 언어 코드 | 범주 | 태그 | 설명 | 성인 | 브랜드 | 색 | 얼굴 | ImageType | 개체 | 유명인 | 랜드마크 |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|중국어 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|영어 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|일본어 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|포르투갈어 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|스페인어 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>다음 단계

이 가이드에서 설명 하는 Computer Vision 기능을 사용 하 여 시작 하세요.

* [로컬 이미지 분석 (REST)](./quickstarts/csharp-analyze.md)
* [인쇄 된 텍스트 추출 (REST)](./quickstarts/csharp-print-text.md)
