---
title: OCR (광학 문자 인식)-Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용 하 여 인쇄 및 필기 된 텍스트가 있는 이미지 및 문서의 OCR (광학 문자 인식) 관련 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 1d633b1a9f5fee0a5cceb48f2b37aaec2092069f
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979538"
---
# <a name="optical-character-recognition-ocr"></a>OCR(광학 문자 인식)

Azure의 Computer Vision API는 이미지에서 인쇄 되거나 필기 된 텍스트를 추출 하는 OCR (광학 문자 인식) 기능을 포함 합니다. 문서 (청구서, 청구서, 재무 보고서, 문서 등) 뿐만 아니라 일련 번호를 포함 하는 라이선스 플레이트 또는 컨테이너와 같은 이미지에서 텍스트를 추출할 수 있습니다.

## <a name="read-api"></a>읽기 API 

Computer Vision [읽기 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) 는 이미지 및 다중 페이지 PDF 문서에서 인쇄 된 텍스트 (여러 언어로), 필기 텍스트 (영어 전용), 숫자 및 통화 기호를 추출 하는 Azure의 최신 OCR 기술 ([새로운 기능](./whats-new.md))입니다. 텍스트를 많이 사용 하는 이미지에서 텍스트를 추출 하 고 혼합 언어를 사용 하 여 다중 페이지 PDF 문서를 추출 하도록 최적화 되어 있습니다. 동일한 이미지나 문서에서 인쇄 된 텍스트와 필기 텍스트를 모두 검색 하는 것을 지원 합니다.

![OCR에서 이미지와 문서를 추출 된 텍스트로 구조화 된 출력으로 변환 하는 방법](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>입력 요구 사항
**읽기** 호출에서는 이미지와 문서를 입력으로 사용 합니다. 이러한 요구 사항은 다음과 같습니다.

* 지원 되는 파일 형식: JPEG, PNG, BMP, PDF 및 TIFF
* PDF 및 TIFF 파일의 경우 최대 2000 페이지 (무료 계층에 대 한 처음 두 페이지만 처리 됨)가 처리 됩니다.
* 파일 크기는 50 MB (무료 계층의 경우 4mb) 미만 이어야 하며, 최소 50 x 50 픽셀 및 최대 1만 x 1만 픽셀 이상 이어야 합니다. 
* PDF 차원은 legal 또는 A3 용지 크기에 해당 하는 17 x 17 인치 여야 합니다.

> [!NOTE]
> **언어 입력** 
>
> [읽기 호출](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) 에는 언어에 대 한 선택적 요청 매개 변수가 있습니다. 읽기는 자동 언어 식별 및 다국어 문서를 지원 하므로 문서를 특정 언어로 강제로 처리 하려는 경우에만 언어 코드를 제공 합니다.

## <a name="ocr-demo-examples"></a>OCR demo (예제)

![OCR 데모](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>1 단계: 읽기 작업

Read API의 [read 호출은](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) 이미지나 PDF 문서를 입력으로 사용 하 고 텍스트를 비동기적으로 추출 합니다. 호출은 라는 응답 헤더 필드를 사용 하 여를 반환 합니다 `Operation-Location` . `Operation-Location`값은 다음 단계에서 사용할 작업 ID를 포함 하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> [Computer Vision 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) 페이지에는 읽기에 대 한 가격 책정 계층이 포함 되어 있습니다. 분석 된 각 이미지 또는 페이지는 하나의 트랜잭션입니다. 100 페이지를 포함 하는 PDF 또는 TIFF 문서를 사용 하 여 작업을 호출 하면 읽기 작업에서 100 트랜잭션으로 계산 되며 100 트랜잭션에 대해 요금이 청구 됩니다. 작업에 대해 50를 호출 하 고 각 호출에서 100 페이지로 문서를 전송 하면 50 X 100 = 5000 트랜잭션에 대해 요금이 청구 됩니다.

## <a name="step-2-the-get-read-results-operation"></a>2 단계: 읽기 결과 가져오기 작업

두 번째 단계는 [Get Results Get](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) 작업을 호출 하는 것입니다. 이 작업은 읽기 작업에서 만든 작업 ID를 입력으로 사용 합니다. 이 메서드는 다음과 같은 가능한 값을 포함 하는 **상태** 필드를 포함 하는 JSON 응답을 반환 합니다. **성공** 값이 반환 될 때까지이 작업을 반복적으로 호출 합니다. 초당 요청 수 (RPS)가 초과 되는 것을 방지 하려면 1 ~ 2 초 간격을 사용 합니다.

|필드| Type | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | notStarted: 작업이 시작 되지 않았습니다. |
| |  | 실행 중: 작업이 처리 중입니다. |
| |  | 실패: 작업이 실패 했습니다. |
| |  | 성공: 작업이 성공 했습니다. |

> [!NOTE]
> 무료 계층은 요청 률을 분당 20 개의 호출로 제한 합니다. 유료 계층에서는 요청에 따라 증가할 수 있는 RPS (초당 요청 수)를 허용 합니다. Azure 지원 채널 또는 계정 팀을 사용 하 여 RPS (초당 요청 수) 요금을 요청 합니다.

**상태** 필드에 **성공** 값이 있는 경우 JSON 응답은 이미지 또는 문서에서 추출 된 텍스트 콘텐츠를 포함 합니다. JSON 응답은 인식 된 단어의 원래 줄 그룹을 유지 관리 합니다. 추출 된 텍스트 줄과 해당 경계 상자 좌표가 포함 됩니다. 각 텍스트 줄에는 추출 된 모든 단어가 해당 좌표와 신뢰도 점수가 포함 됩니다.

> [!NOTE]
> 작업에 전송 된 데이터는 `Read` 일시적으로 암호화 되어 짧은 기간 동안 저장 된 후 삭제 됩니다. 이렇게 하면 응용 프로그램이 서비스 응답의 일부로 추출 된 텍스트를 검색할 수 있습니다.

## <a name="sample-json-output"></a>샘플 JSON 출력

성공적인 JSON 응답의 다음 예를 참조 하세요.

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
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
              }
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="natural-reading-order-output"></a>자연 스러운 읽기 순서 출력
[Read 3.2 PREVIEW API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)를 사용 하 여 쿼리 매개 변수를 사용 하 여 텍스트 줄이 출력 되는 순서를 지정 합니다 `readingOrder` . `natural`다음 예제와 같이 사용자에 게 친숙 한 읽기 순서 출력에 사용 합니다.

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="OCR 읽기 순서 예":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>텍스트 줄에 대 한 필기 분류 (라틴어 전용)
[읽기 3.2 미리 보기 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) 응답은 각 텍스트 줄이 필기 스타일 인지 여부를 분류 하는 것과 신뢰도 점수를 포함 합니다. 이 기능은 라틴어 언어에 대해서만 지원 됩니다. 다음 예에서는 이미지의 텍스트에 대 한 필기 분류를 보여 줍니다.

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="OCR 필기 분류 예":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>텍스트 추출을 위한 페이지 또는 페이지 범위 선택
[읽기 3.2 미리 보기 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)를 사용 하 여 규모가 긴 다중 페이지 문서의 경우 `pages` 쿼리 매개 변수를 사용 하 여 페이지 번호 또는 페이지 범위를 지정 하 여 해당 페이지 에서만 텍스트를 추출 합니다. 다음 예제에서는 모든 페이지 (1-10) 및 선택 된 페이지 (3-6)에 대 한 텍스트를 포함 하는 10 개의 페이지가 있는 문서를 보여 줍니다.

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="선택한 페이지 출력":::

## <a name="supported-languages"></a>지원되는 언어
읽기 Api는 인쇄 스타일 텍스트에 대 한 총 73 언어를 지원 합니다. [OCR에서 지 원하는 언어](./language-support.md#optical-character-recognition-ocr)의 전체 목록을 참조 하세요. 필기 스타일 OCR은 영어 전용으로 지원 됩니다.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>클라우드 API 사용 또는 온-프레미스 배포
읽기 2.x 클라우드 Api는 대부분의 고객에 게 편리한 통합 및 빠른 생산성 때문에 대부분의 고객에 게 선호 되는 옵션입니다. Azure와 Computer Vision 서비스는 고객의 요구를 충족 하는 동안 규모, 성능, 데이터 보안 및 규정 준수 요구 사항을 처리 합니다.

온-프레미스 배포의 경우 [Docker 컨테이너 읽기 (미리 보기)](./computer-vision-how-to-install-containers.md) 를 사용 하 여 새 OCR 기능을 자체 로컬 환경에 배포할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다.

## <a name="ocr-api"></a>OCR API

[OCR API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) 는 이전 인식 모델을 사용 하 고 이미지만 지원 하며 동기적으로 실행 되어 검색 된 텍스트로 즉시 반환 됩니다. [OCR 지원 언어](./language-support.md#optical-character-recognition-ocr) 를 확인 한 후 API를 읽습니다.

> [!NOTE]
> 컴퓨터 Vison 2.0 RecognizeText 작업은이 문서에서 다루는 새로운 읽기 API를 사용 하 여 더 이상 사용 되지 않습니다. 기존 고객은 [읽기 작업을 사용 하도록 전환](upgrade-api-versions.md)해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Computer Vision REST API 또는 클라이언트 라이브러리](./quickstarts-sdk/client-library.md)빠른 시작을 시작 하세요.
- [읽기 3.1 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)에 대해 알아봅니다.
- 총 73 언어에 대 한 지원이 포함 된 [읽기 3.2 공개 미리 보기 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) 에 대해 알아봅니다.
