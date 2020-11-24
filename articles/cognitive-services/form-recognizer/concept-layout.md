---
title: 레이아웃-폼 인식기
titleSuffix: Azure Cognitive Services
description: 인식기 API 사용 및 제한을 사용 하 여 레이아웃 분석과 관련 된 개념을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: b489964e5ef8ea483cbe203be1ff665078a6d66a
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95741732"
---
# <a name="form-recognizer-layout-service"></a>폼 인식기 레이아웃 서비스

Azure 양식 인식기는 레이아웃 서비스를 사용 하 여 문서에서 텍스트, 테이블, 선택 표시 및 구조 정보를 추출할 수 있습니다. 사용자는 레이아웃 API를 사용 하 여 다양 한 형식으로 문서를 가져와서 구조화 된 데이터 및 문서 표현을 반환할 수 있습니다. 강력한 [OCR (광학 인식)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) 기능을 문서와 결합 하 여 심층 학습 모델을 이해 하 여 텍스트, 테이블, 선택 표시 및 문서의 구조를 추출 합니다. 

## <a name="what-does-the-layout-service-do"></a>레이아웃 서비스는 어떤 역할을 하나요?

레이아웃 API는 예외 정확도가 있는 문서에서 텍스트, 테이블, 선택 표시 및 구조 정보를 추출 하 여 구성 된 구조적 JSON 응답으로 반환 합니다. 문서는 휴대폰 캡처된 이미지, 스캔 한 문서 및 디지털 Pdf를 비롯 한 다양 한 형식과 품질에서 비롯 될 수 있습니다. Layout API는 이러한 모든 문서에서 구조적 출력을 추출 합니다.

![레이아웃 예제](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>사용해 보기

양식 인식기 레이아웃 서비스를 사용해 보려면 온라인 샘플 UI 도구로 이동 합니다.

> [!div class="nextstepaction"]
> [샘플 UI](https://fott-preview.azurewebsites.net/)

양식 인식기 레이아웃 API를 사용해 보려면 Azure 구독 ([무료로 하나 만들기](https://azure.microsoft.com/free/cognitive-services))과 [양식 인식기 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 끝점 및 키가 필요 합니다. 

![샘플 UI 스크린샷 문서의 텍스트, 테이블 및 선택 표시가 분석 됩니다.](./media/analyze-layout.png)

### <a name="input-requirements"></a>입력 요구 사항 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>레이아웃 분석 작업

[레이아웃 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) 작업은 문서 (이미지, TIFF 또는 PDF 파일)를 입력으로 사용 하 고 텍스트, 테이블, 선택 표시 및 문서의 구조를 추출 합니다. 호출은 라는 응답 헤더 필드를 반환 합니다 `Operation-Location` . `Operation-Location`값은 다음 단계에서 사용할 결과 ID를 포함 하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>분석 레이아웃 결과 가져오기 작업

두 번째 단계는 [분석 레이아웃 결과 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) 작업을 호출 하는 것입니다. 이 작업은 레이아웃 분석 작업에 의해 생성 된 결과 ID를 입력으로 사용 합니다. 이 메서드는 다음과 같은 가능한 값을 포함 하는 **상태** 필드를 포함 하는 JSON 응답을 반환 합니다. 

|필드| Type | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | `notStarted`: 분석 작업이 시작 되지 않았습니다.<br /><br />`running`: 분석 작업이 진행 중입니다.<br /><br />`failed`: 분석 작업이 실패 했습니다.<br /><br />`succeeded`: 분석 작업에 성공 했습니다.|

이 작업은 값을 사용 하 여 반환 될 때까지 반복적으로 호출 `succeeded` 됩니다. 초당 요청 수 (RPS)를 초과 하지 않도록 3 ~ 5 초 간격을 사용 합니다.

**상태** 필드의 값이 인 경우 `succeeded` JSON 응답에는 추출 된 레이아웃 추출 결과, 텍스트, 표 및 선택 표시가 포함 됩니다. 추출 된 데이터에는 추출 된 텍스트 줄과 단어, 경계 상자, 텍스트 모양, 필기 표시, 표 및 선택 표시가 표시 되 고 선택/선택이 취소 되어 있습니다. 

### <a name="sample-json-output"></a>샘플 JSON 출력

분석 레이아웃 결과 가져오기 작업에 대 한 응답은 모든 정보가 추출 된 문서를 구조화 하 여 표현한 것입니다. [샘플 문서 파일](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) 및 해당 구조화 된 출력 [샘플 레이아웃 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)은 여기를 참조 하세요.

JSON 출력에는 두 부분이 있습니다. 
* `"readResults"` 노드는 인식 된 모든 텍스트 및 선택 표시를 포함 합니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. 
* `"pageResults"` 노드에는 경계 상자, 신뢰도 및 "readResults"의 줄과 단어에 대 한 참조를 사용 하 여 추출 된 테이블 및 셀이 포함 되어 있습니다.

## <a name="example-output"></a>예제 출력

### <a name="text"></a>텍스트

레이아웃은 문서 (PDF, TIFF) 및 이미지 (jpg, png, bmp)에서 다양 한 텍스트 각도, 색, 각도, 문서 사진, 팩스, 인쇄, 필기 (영어 전용) 및 혼합 모드를 사용 하 여 텍스트를 추출 합니다. 텍스트는 줄, 단어, 경계 상자, 신뢰도 점수 및 스타일 (필기 또는 기타)에 대 한 정보로 추출 됩니다. 모든 텍스트 정보는 `"readResults"` JSON 출력의 섹션에 포함 됩니다. 

### <a name="tables"></a>테이블

레이아웃은 문서 (PDF, TIFF) 및 이미지 (jpg, png, bmp)에서 테이블을 추출 합니다. 문서는 검색, photographed 또는 디지털화 할 수 있습니다. 테이블은 병합 된 셀 또는 열이 있는 복잡 한 테이블 일 수 있습니다. 즉, 테두리를 포함 하거나 포함 하지 않고 홀수입니다. 추출 된 테이블에는 열과 행, 행 범위 및 열 범위 수가 포함 됩니다. 각 셀은 경계 상자와 섹션에서 추출 된 텍스트에 대 한 참조를 사용 하 여 추출 됩니다 `"readResults"` . 테이블 정보는 `"pageResults"` JSON 출력의 섹션에 있습니다. 

![테이블 예](./media/tables-example.jpg)

### <a name="selection-marks"></a>선택 표시

또한 레이아웃은 문서에서 선택 표시를 추출 합니다. 추출 된 선택 표시에는 경계 상자, 신뢰도 및 상태 (선택/선택 되지 않음)가 포함 됩니다. 선택 표시 정보는 `"readResults"` JSON 출력의 섹션에서 추출 됩니다. 

## <a name="next-steps"></a>다음 단계

- [양식 인식기 샘플 UI](https://fott-preview.azurewebsites.net/) 를 사용 하 여 사용자 고유의 레이아웃 추출 시도
- [양식 인식기 클라이언트 라이브러리 빠른](quickstarts/client-library.md) 시작을 완료 하 여 선택한 언어로 레이아웃 압축을 시작 합니다.
- 또는 [레이아웃 데이터 추출](./QuickStarts/python-layout.md) 빠른 시작을 따라 Python 및 REST API를 사용 하 여 레이아웃 데이터 추출을 구현 합니다.

## <a name="see-also"></a>참조

* [Form Recognizer란?](./overview.md)
* [REST API 참조 문서](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)




