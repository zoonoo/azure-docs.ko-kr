---
title: Id-폼 인식기
titleSuffix: Azure Cognitive Services
description: 양식 인식기 미리 작성 된 Id API를 사용 하 여 id 문서에서 데이터 추출과 관련 된 개념을 알아봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 01a73e7940f88a3eb6e040f26d255448294cab18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467841"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>양식 인식기의 미리 빌드된 id (식별 카드) 모델

Azure 양식 인식기는 미리 빌드된 Id 모델을 사용 하 여 Id (정부 식별 카드)에서 정보를 분석 하 고 추출할 수 있습니다. 또한 강력한 [OCR (광학 문자 인식)](../computer-vision/concept-recognizing-text.md) 기능을 ID 인식 기능과 결합 하 여 전 세계 여권 및 미국 운전 라이선스 (모든 50 상태 및 d.c)에서 핵심 정보를 추출 합니다. Id API는 이름, 성, 생년월일, 문서 번호 등의 이러한 id 문서에서 키 정보를 추출 합니다. 이 API는 인식기 v 2.1 preview에서 클라우드 서비스 및 온-프레미스 컨테이너로 사용할 수 있습니다.

## <a name="what-does-the-id-service-do"></a>ID 서비스는 어떤 역할을 하나요? 

미리 작성 된 Id 서비스는 전 세계 여권 및 미국 운전 면허증에서 키 값을 추출 하 여 구성 된 구조적 JSON 응답으로 반환 합니다. 

![샘플 드라이버의 라이선스](./media/id-example-drivers-license.JPG)

![샘플 Passport](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>추출 된 필드

|Name| Type | Description | 값 | 
|:-----|:----|:----|:----|
|  국가 | country | ISO 3166 표준을 준수 하는 국가 코드 | 인도네시아 | 
|  DateOfBirth | date | YYYY-MM-DD 형식의 DOB | "1980-01-01" | 
|  DateOfExpiration | date | YYYY-MM-DD 형식의 만료 날짜 | "2019-05-05" |  
|  문서 번호 | 문자열 | 관련 passport 번호, 운전 면허 번호 등 | "340020013" |  
|  FirstName | 문자열 | 지정 된 이름 및 중간 이니셜 (해당 하는 경우)을 추출 했습니다. | JENNIFER | 
|  LastName | 문자열 | 추출 된 성 | "BROOKS" |   
|  Nationality | country | ISO 3166 표준을 준수 하는 국가 코드 | 인도네시아 |
|  성 | gender | 추출 가능한 값에는 "M", "F" 및 "X"가 포함 됩니다. | "F" | 
|  MachineReadableZone | 개체 | 두 줄의 44 문자를 포함 하 여 Passport MRZ을 추출 했습니다. | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType | 문자열 | 문서 유형 (예: Passport, 드라이버 라이선스) | passport |  
|  주소 | 문자열 | 추출 된 주소 (드라이버의 라이선스에만 해당) | "123 주소에서 도시 WA 99999-1234"|
|  지역 | 문자열 | 추출 된 지역, 시/도, 구/군/시 (드라이버의 라이선스에만 해당) | 워싱턴 | 

### <a name="additional-features"></a>추가 기능

또한 Id API는 다음 정보를 반환 합니다.

* 필드 신뢰 수준 (각 필드는 연결 된 신뢰도 값을 반환)
* OCR 원시 텍스트 (전체 수신에 대 한 OCR 추출 된 텍스트 출력)
* 미국 드라이버 라이선스의 추출 된 각 필드에 대 한 경계 상자
* 여권의 MRZ (컴퓨터 판독 가능 영역)에 대 한 경계 상자

  > [!NOTE]
  > 미리 작성 된 Id가 ID 신뢰성을 검색 하지 않음
  >
  > 양식 인식기 미리 작성 된 Id는 ID 데이터에서 키 데이터를 추출 합니다. 그러나 원래 id 문서의 유효성 또는 신뢰성을 검색 하지는 않습니다. 

## <a name="try-it-out"></a>사용해 보기

양식 인식기 Id 서비스를 사용해 보려면 온라인 샘플 UI 도구로 이동 합니다.

> [!div class="nextstepaction"]
> [미리 빌드된 모델 사용해보기](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>입력 요구 사항

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>지원 되는 ID 유형  

* **미리 작성 된 id v 2.1-preview. 3** 전 세계 여권 및 미국 운전 라이선스에서 키 값을 추출 합니다. 

  > [!NOTE]
  > ID 유형 지원 
  >
  > 현재 지원 되는 ID 형식은 전 세계 passport 및 미국 드라이버의 라이선스를 포함 합니다. Microsoft는 현재 ID 지원을 전 세계의 다른 id 문서로 확장 하 고 있습니다.

## <a name="post-analyze-id-document"></a>분석 Id 문서 게시

[Id 분석](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) 작업은 입력으로 id의 이미지나 PDF를 사용 하 고 관심 있는 값을 추출 합니다. 호출은 라는 응답 헤더 필드를 반환 합니다 `Operation-Location` . `Operation-Location`값은 다음 단계에서 사용할 결과 ID를 포함 하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>분석 Id 문서 결과 가져오기

<!---
Need to update this with updated APIM links when available
-->

두 번째 단계는 [**idDocument Result 분석 작업 가져오기**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) 작업을 호출 하는 것입니다. 이 작업은 ID 분석 작업에 의해 생성 된 결과 ID를 입력으로 사용 합니다. 이 메서드는 다음과 같은 가능한 값을 포함 하는 **상태** 필드를 포함 하는 JSON 응답을 반환 합니다. **성공** 값이 반환 될 때까지이 작업을 반복적으로 호출 합니다. 초당 요청 수 (RPS)를 초과 하지 않도록 3 ~ 5 초 간격을 사용 합니다.

|필드| Type | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | notStarted: 분석 작업이 시작 되지 않았습니다. |
| |  | 실행 중: 분석 작업이 진행 중입니다. |
| |  | 실패: 분석 작업이 실패 했습니다. |
| |  | 성공: 분석 작업이 성공 했습니다. |

**상태** 필드의 **성공** 값이 이면 JSON 응답에는 수신 이해 및 텍스트 인식 결과가 포함 됩니다. Id 결과는 명명 된 필드 값의 사전으로 구성 됩니다. 여기서 각 값은 추출 된 텍스트, 정규화 된 값, 경계 상자, 신뢰도 및 해당 단어 요소를 포함 합니다. 텍스트 인식 결과는 텍스트, 경계 상자 및 신뢰도 정보를 사용 하 여 선과 단어의 계층 구조로 구성 됩니다.

![샘플 수신 결과](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>샘플 JSON 출력

성공적인 JSON 응답의 다음 예를 참조 하세요. `readResults` 노드에는 인식 된 모든 텍스트가 포함 됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. `documentResults`노드에는 모델에서 검색 된 ID 값이 포함 됩니다. 이 노드는 이름, 성, 문서 번호 등과 같은 유용한 키/값 쌍을 찾을 수도 있습니다.

```json
{ 
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],
    
     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```


## <a name="next-steps"></a>다음 단계

- 사용자 고유의 Id와 샘플을 [양식 인식기 샘플 UI](https://fott-preview.azurewebsites.net/)에서 사용해 보세요.
- 선택한 개발 언어로 폼 인식기를 사용 하 여 ID 처리 앱을 작성 하기 시작 하려면 [폼 인식기 퀵 스타트](quickstarts/client-library.md) 를 완료 하세요.

## <a name="see-also"></a>참고 항목

* [**Form Recognizer란?**](./overview.md)
* [**REST API 참조 문서**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
