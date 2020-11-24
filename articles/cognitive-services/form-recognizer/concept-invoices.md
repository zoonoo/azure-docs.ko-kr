---
title: 송장-양식 인식기
titleSuffix: Azure Cognitive Services
description: 인식기 API 사용 및 제한을 사용 하 여 송장 분석과 관련 된 개념을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: af3935bab5cd4d6a2aa4a9e3250f6beec19a8ea4
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95736682"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>양식 인식기 미리 빌드된 송장 모델

Azure 양식 인식기는 미리 작성 된 청구서 모델을 사용 하 여 판매 청구서에서 정보를 분석 하 고 추출할 수 있습니다. 송장 API를 통해 고객은 다양 한 형식으로 송장을 만들고 구조화 된 데이터를 반환 하 여 송장 처리를 자동화할 수 있습니다. 또한 강력한 [OCR (광학 인식)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) 기능을 송장과 결합 하 여 심층 학습 모델을 이해 하 여 영어로 된 청구서에서 핵심 정보를 추출 합니다. 이 도구는 고객, 공급 업체, 청구서 ID, 청구서 기한, 청구서 총액, 세금 금액, 배송 대상, 청구서 받는 사람 등의 텍스트, 테이블 및 정보를 추출 합니다. 미리 작성 된 청구서 API는 인식기 v 2.1 preview에서 공개적으로 사용할 수 있습니다.

## <a name="what-does-the-invoice-service-do"></a>청구서 서비스는 무엇을 하나요?

청구서 API는 청구서에서 키 필드를 추출 하 여 구성 된 구조적 JSON 응답으로 반환 합니다. 청구서는 전화를 캡처한 이미지, 스캔 한 문서 및 디지털 Pdf를 비롯 한 다양 한 형식 및 품질에서 사용할 수 있습니다. 청구서 API는 이러한 모든 청구서에서 구조화 된 출력을 추출 합니다. 

![Contoso invoice 예](./media/invoice-example.jpg)

## <a name="try-it-out"></a>사용해 보기

양식 인식기 청구서 서비스를 사용해 보려면 온라인 샘플 UI 도구로 이동 합니다.

> [!div class="nextstepaction"]
> [미리 작성 한 모델 사용해 보기](https://fott-preview.azurewebsites.net/)

양식 인식기 청구서 서비스를 사용해 보려면 Azure 구독 ([무료로 하나 만들기](https://azure.microsoft.com/free/cognitive-services))과 [Recognzier 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 끝점 및 키 양식이 필요 합니다. 

![분석 된 송장 예](./media/analyze-invoice.png)


### <a name="input-requirements"></a>입력 요구 사항 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>송장 분석 작업

[송장 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeInvoiceAsync) 작업은 송장의 이미지 또는 PDF를 입력으로 사용 하 고 원하는 값을 추출 합니다. 호출은 라는 응답 헤더 필드를 반환 합니다 `Operation-Location` . `Operation-Location`값은 다음 단계에서 사용할 결과 ID를 포함 하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>송장 분석 결과 가져오기 작업

두 번째 단계는 [분석 송장 결과 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeInvoiceResult) 작업을 호출 하는 것입니다. 이 작업은 송장 분석 작업에 의해 생성 된 결과 ID를 입력으로 사용 합니다. 이 메서드는 다음과 같은 가능한 값을 포함 하는 **상태** 필드를 포함 하는 JSON 응답을 반환 합니다. **성공** 값이 반환 될 때까지이 작업을 반복적으로 호출 합니다. 초당 요청 수 (RPS)를 초과 하지 않도록 3 ~ 5 초 간격을 사용 합니다.

|필드| Type | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | notStarted: 분석 작업이 시작 되지 않았습니다.<br /><br />실행 중: 분석 작업이 진행 중입니다.<br /><br />실패: 분석 작업이 실패 했습니다.<br /><br />성공: 분석 작업이 성공 했습니다.|

**상태** 필드에 **성공** 값이 있는 경우 JSON 응답에는 송장 이해 결과, 추출한 테이블 및 선택적 텍스트 인식 결과가 포함 됩니다 (요청한 경우). 송장 이해 결과는 명명 된 필드 값의 사전으로 구성 됩니다. 여기서 각 값은 추출 된 텍스트, 정규화 된 값, 경계 상자, 신뢰도 및 해당 단어 요소를 포함 합니다. 텍스트 인식 결과는 텍스트, 경계 상자 및 신뢰도 정보를 사용 하 여 선과 단어의 계층 구조로 구성 됩니다.

### <a name="sample-json-output"></a>샘플 JSON 출력

[송장 분석 결과 가져오기] 작업에 대 한 응답은 모든 정보가 추출 된 청구서의 구조화 된 표현이 됩니다. [샘플 청구서 파일](./media/sample-invoice.jpg) 및 해당 구조화 된 출력 [샘플 청구서 출력](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json)은 여기를 참조 하세요.

JSON 출력에는 3 가지 부분이 있습니다. 
* `"readResults"` 노드는 인식 된 모든 텍스트 및 선택 표시를 포함 합니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. 
* `"pageResults"` 노드에는 경계 상자, 신뢰도 및 "readResults"의 줄과 단어에 대 한 참조를 사용 하 여 추출 된 테이블 및 셀이 포함 되어 있습니다.
* `"documentResults"` 노드에는 모델에서 검색 된 청구서 특정 값이 포함 됩니다. 청구서 ID, 배송 대상, 청구서 받는 사람, 고객, 합계 등 청구서에서 모든 필드를 찾을 수 있습니다.

## <a name="example-output"></a>예제 출력

송장 서비스는 text, tables 및 26 개의 송장 필드를 추출 합니다. 다음은 JSON 출력 응답의 청구서에서 추출 된 필드입니다 (아래 출력은이 [샘플 송장을](./media/sample-invoice.jpg)사용).  

|Name| Type | 설명 | 텍스트 | 값 (표준화 된 출력) |
|:-----|:----|:----|:----| :----|
| CustomerName | 문자열 | 송장이 청구 되는 고객 | Microsoft Corp |  |
| CustomerId | 문자열 | 고객에 대 한 참조 ID | CID-12345 |  |
| PurchaseOrder | 문자열 | 구매 주문 참조 번호 | PO-3333 | |  |
| InvoiceId | 문자열 | 이 특정 송장의 Id (종종 "송장 번호") | INV-100 | |  |
| InvoiceDate | date | 송장이 발행 된 날짜 | 11/15/2019 | 
| DueDate | date | 이 청구서의 결제 기한 | 12/15/2019 | 2019-12-15 | 2019-11-15 |
| 이름의 | 문자열 | 이 송장을 만든 공급 업체 | CONTOSO L T D. | |
| VendorAddress | 문자열 | 공급 업체의 우편 주소 | 123 456th 세인트 뉴욕, 전, 10001 | |
| VendorAddressRecipient | 문자열 | VendorAddress와 연결 된 이름입니다. | Contoso 본사 | |
| CustomerAddress | 문자열 | 고객의 우편 주소 | 123 기타 St, Redmond WA, 98052 | |
| CustomerAddressRecipient | 문자열 | CustomerAddress와 연결 된 이름입니다. | Microsoft Corp | |
| BillingAddress | 문자열 | 고객에 대 한 명시적 청구 주소 | 123 청구서 St, Redmond WA, 98052 | |
| BillingAddressRecipient | 문자열 | BillingAddress와 연결 된 이름입니다. | Microsoft 서비스 | |
| ShippingAddress | 문자열 | 고객에 대 한 명시적 배송 주소 | 123 선적 세인트, Redmond WA, 98052 | |
| 배송 중인 받는 사람 | 문자열 | 배송 주소와 연결 된 이름 | Microsoft 제공 | |
| SubTotal | 숫자 | 이 청구서에서 식별 된 부분합 필드 | $100.00 | 100 | 
| TotalTax | 숫자 | 이 청구서에서 식별 된 총 세금 필드 | $10.00 | 10 |
| InvoiceTotal | 숫자 | 이 청구서와 관련 된 총 새 요금 | $110.00 | 110 |
| AmountDue |  숫자 | 공급 업체의 총 금액 | $610.00 | 610 |
| Serviceaddress.uri | 문자열 | 고객에 대 한 명시적 서비스 주소 또는 속성 주소 | 123 Service St, Redmond WA, 98052 | |
| ServiceAddressRecipient | 문자열 | ServiceAddress와 연결 된 이름 | Microsoft 서비스 | |
| RemittanceAddress | 문자열 | 고객에 대 한 명시적 송금 또는 지불 주소 | 123 remit St 뉴욕, 전, 10001 |  |
| RemittanceAddressRecipient | 문자열 | RemittanceAddress와 연결 된 이름입니다. | Contoso 청구 |  |
| ServiceStartDate | date | 서비스 기간에 대 한 첫 번째 날짜 (예: 유틸리티 청구 서비스 기간) | 2019/10/14 | 2019-10-14 |
| ServiceEndDate | date | 서비스 기간의 종료 날짜 (예: 유틸리티 청구 서비스 기간) | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | 숫자 | 명시적으로 이전 미지불 잔액 | $500.00 | 500 |


## <a name="next-steps"></a>다음 단계

- [양식 인식기 샘플 UI](https://fott-preview.azurewebsites.net/)에서 고유한 청구서 및 샘플을 사용해 보세요.
- 양식 인식기 [클라이언트 라이브러리 빠른](quickstarts/client-library.md) 시작을 완료 하 여 선택한 언어로 양식 인식기를 사용 하 여 송장 처리 앱을 작성 하기 시작 합니다.
- 또는 [청구서 데이터 추출](./quickstarts/python-invoices.md) 빠른 시작을 수행 하 여 Python 및 REST API를 사용 하 여 송장 데이터 추출을 구현 합니다.
## <a name="see-also"></a>참조

* [Form Recognizer란?](./overview.md)
* [REST API 참조 문서](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeInvoiceAsync)

