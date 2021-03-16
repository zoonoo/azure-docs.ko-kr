---
title: 레이아웃-폼 인식기
titleSuffix: Azure Cognitive Services
description: 인식기 API 사용 및 제한을 사용 하 여 레이아웃 분석과 관련 된 개념을 알아봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 73bef21f430bde1c6c2c95d7c3f685cccbbd9179
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467020"
---
# <a name="form-recognizer-layout-service"></a>폼 인식기 레이아웃 서비스

Azure 양식 인식기는 레이아웃 서비스를 사용 하 여 문서에서 텍스트, 테이블, 선택 표시 및 구조 정보를 추출할 수 있습니다. 사용자는 레이아웃 API를 사용 하 여 다양 한 형식으로 문서를 가져와서 문서의 구조화 된 데이터 표현을 반환할 수 있습니다. 강력한 [OCR (광학 문자 인식)](../computer-vision/concept-recognizing-text.md) 기능을 심층 학습 모델과 결합 하 여 텍스트, 테이블, 선택 표시 및 문서 구조를 추출 합니다. 

## <a name="what-does-the-layout-service-do"></a>레이아웃 서비스는 어떤 역할을 하나요?

레이아웃 API는 예외 정확도가 있는 문서에서 텍스트, 테이블, 선택 표시 및 구조 정보를 추출 하 고 구조화 된 구조적 JSON 응답을 반환 합니다. 문서는 휴대폰으로 캡처된 이미지, 스캔 한 문서, 디지털 Pdf 등의 다양 한 형식과 품질을 사용할 수 있습니다. Layout API는 이러한 모든 문서에서 구조적 출력을 정확 하 게 추출 합니다.

![레이아웃 예제](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>사용해 보기

양식 인식기 레이아웃 서비스를 사용해 보려면 온라인 샘플 UI 도구로 이동 합니다.

> [!div class="nextstepaction"]
> [FOTT (양식 OCR 테스트 도구)](https://fott-preview.azurewebsites.net)

양식 인식기 레이아웃 API를 사용해 보려면 Azure 구독 ([무료로 하나 만들기](https://azure.microsoft.com/free/cognitive-services))과 [양식 인식기 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 끝점 및 키가 필요 합니다. 

![샘플 UI 스크린샷 문서의 텍스트, 테이블 및 선택 표시가 분석 됩니다.](./media/analyze-layout.png)

### <a name="input-requirements"></a>입력 요구 사항 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>레이아웃 분석 작업

먼저 [레이아웃 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync) 작업을 호출 합니다. 레이아웃 분석은 문서 (이미지, TIFF 또는 PDF 파일)를 입력으로 사용 하 고 텍스트, 테이블, 선택 표시 및 문서의 구조를 추출 합니다. 호출은 라는 응답 헤더 필드를 반환 합니다 `Operation-Location` . `Operation-Location`값은 다음 단계에서 사용할 결과 ID를 포함 하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>자연 읽기 순서 출력 (라틴어 전용)

쿼리 매개 변수를 사용 하 여 텍스트 줄이 출력 되는 순서를 지정할 수 있습니다 `readingOrder` . `natural`다음 예제와 같이 사용자에 게 친숙 한 읽기 순서 출력에 사용 합니다. 이 기능은 라틴어 언어에 대해서만 지원 됩니다.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="레이아웃 읽기 순서 예" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>텍스트 추출을 위한 페이지 번호 또는 범위 선택

다중 페이지 문서의 경우 `pages` 쿼리 매개 변수를 사용 하 여 텍스트 추출을 위한 특정 페이지 번호 또는 페이지 범위를 지정 합니다. 다음 예제에서는 모든 페이지 (1-10) 및 선택 된 페이지 (3-6)에 대 한 텍스트를 포함 하는 10 개의 페이지가 있는 문서를 보여 줍니다.

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="선택한 페이지 출력 레이아웃":::

## <a name="the-get-analyze-layout-result-operation"></a>분석 레이아웃 결과 가져오기 작업

두 번째 단계는 [분석 레이아웃 결과 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult) 작업을 호출 하는 것입니다. 이 작업은 레이아웃 분석 작업에 의해 생성 된 결과 ID를 입력으로 사용 합니다. 이 메서드는 다음과 같은 가능한 값을 포함 하는 **상태** 필드를 포함 하는 JSON 응답을 반환 합니다. 

|필드| Type | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | `notStarted`: 분석 작업이 시작 되지 않았습니다.<br /><br />`running`: 분석 작업이 진행 중입니다.<br /><br />`failed`: 분석 작업이 실패 했습니다.<br /><br />`succeeded`: 분석 작업에 성공 했습니다.|

값을 반환할 때까지이 작업을 반복적으로 호출 `succeeded` 합니다. 초당 요청 수 (RPS)를 초과 하지 않도록 3 ~ 5 초 간격을 사용 합니다.

**상태** 필드에 값이 있는 경우 `succeeded` JSON 응답은 추출 된 레이아웃, 텍스트, 표 및 선택 표시를 포함 합니다. 추출 된 데이터에는 추출 된 텍스트 줄과 단어, 경계 상자, 필기 표시를 사용 하 여 텍스트 모양, 선택/선택이 표시 된 선택 표시가 포함 됩니다. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>텍스트 줄에 대 한 필기 분류 (라틴어 전용)

응답에는 신뢰 점수와 함께 각 텍스트 줄이 필기 스타일 인지 여부를 분류 하는 작업이 포함 됩니다. 이 기능은 라틴어 언어에 대해서만 지원 됩니다. 다음 예에서는 이미지의 텍스트에 대 한 필기 분류를 보여 줍니다.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="필기 분류 예":::

### <a name="sample-json-output"></a>샘플 JSON 출력

*분석 레이아웃 결과 가져오기* 작업에 대 한 응답은 압축 된 모든 정보를 포함 하는 문서에 대 한 구조적 표현입니다. [샘플 문서 파일](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) 및 해당 구조화 된 출력 [샘플 레이아웃 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)은 여기를 참조 하세요.

JSON 출력에는 두 부분이 있습니다.

* `readResults` 노드는 인식 된 모든 텍스트 및 선택 표시를 포함 합니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. 
* `pageResults` 노드에는 경계 상자, 신뢰도 및 "readResults"의 줄과 단어에 대 한 참조를 사용 하 여 추출 된 테이블 및 셀이 포함 되어 있습니다.

## <a name="example-output"></a>예제 출력

### <a name="text"></a>텍스트

Layout API는 여러 텍스트 각도 및 색을 사용 하 여 문서 (PDF, TIFF) 및 이미지 (JPG, PNG, BMP)에서 텍스트를 추출 합니다. 문서, 팩스, 인쇄 및/또는 필기 (영어 전용) 텍스트 및 혼합 모드의 사진을 허용 합니다. 텍스트는 줄, 단어, 경계 상자, 신뢰도 점수 및 스타일 (필기 또는 기타)에 제공 된 정보로 추출 됩니다. 모든 텍스트 정보는 `readResults` JSON 출력의 섹션에 포함 됩니다. 

### <a name="tables"></a>테이블

Layout API는 문서 (PDF, TIFF) 및 이미지 (JPG, PNG, BMP)에서 테이블을 추출 합니다. 문서는 검색, photographed 또는 디지털화 할 수 있습니다. 테이블은 병합 된 셀 또는 열을 사용 하 여 복잡할 수 있으며 테두리를 포함 하거나 포함 하지 않고 홀수를 사용할 수 있습니다. 추출 된 테이블 정보에는 열과 행, 행 범위 및 열 범위 수가 포함 됩니다. 각 셀은 경계 상자와 섹션에서 추출 된 텍스트에 대 한 참조를 사용 하 여 추출 됩니다 `readResults` . 테이블 정보는 `pageResults` JSON 출력의 섹션에 있습니다. 

![테이블 예](./media/tables-example.jpg)

### <a name="selection-marks"></a>선택 표시

또한 레이아웃 API는 문서에서 선택 표시를 추출 합니다. 추출 된 선택 표시에는 경계 상자, 신뢰도 및 상태 (선택/선택 하지 않음)가 포함 됩니다. 선택 표시 정보는 `readResults` JSON 출력의 섹션에서 추출 됩니다. 

## <a name="next-steps"></a>다음 단계

* [양식 인식기 샘플 UI 도구](https://fott-preview.azurewebsites.net/) 를 사용 하 여 사용자 고유의 레이아웃 추출 시도
* 선택한 개발 언어로 레이아웃 압축 풀기를 시작 하려면 [폼 인식기 퀵 스타트](quickstarts/client-library.md) 를 완료 하세요.

## <a name="see-also"></a>참조

* [Form Recognizer란?](./overview.md)
* [REST API 참조 문서](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
