---
title: OCR (광학 문자 인식)-Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용 하 여 인쇄 및 필기 된 텍스트가 있는 이미지 및 문서에서 OCR (광학 문자 인식) 관련 개념입니다.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 6bc118145bec30085c2d9fbf726c40a20b312430
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207064"
---
# <a name="optical-character-recognition-ocr"></a>OCR(광학 문자 인식)

Microsoft Computer Vision API는 이미지 및 PDF 문서에서 인쇄 되거나 필기 된 텍스트를 추출 하는 OCR (광학 문자 인식) 기능을 포함 합니다. OCR Api는 아날로그 문서 (이미지, 스캔 한 문서) 및 디지털화 된 문서에서 텍스트를 추출 합니다. 주문형 이미지, 일련 번호를 포함 하는 컨테이너, 청구서, 청구서, 재무 보고서, 문서 등의 문서에서 텍스트를 추출할 수 있습니다. 새 읽기 OCR API는 클라우드 또는 온-프레미스 (컨테이너)에서 관리 되는 서비스의 일부로 사용할 수 있습니다. 또한 기업 등급 준수 및 개인 정보 보호 요구 사항을 충족 하는 가상 네트워크 및 개인 끝점을 지원 합니다.

## <a name="read-api"></a>읽기 API 

Computer Vision의 [읽기 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) 는 이미지 및 다중 페이지 PDF 문서에서 인쇄 된 텍스트 (7 개의 언어), 필기 텍스트 (영어 전용), 숫자 및 통화 기호를 추출 하는 Microsoft의 최신 OCR 기술입니다. 이는 혼합 된 언어를 사용 하 여 텍스트 집약적 이미지 및 다중 페이지 PDF 문서에서 텍스트를 추출 하는 데 최적화 되어 있습니다. 동일한 이미지나 문서에서 인쇄 및 필기 텍스트 (영어만) 검색을 지원 합니다. [OCR 지원 언어](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) 의 전체 목록 페이지를 참조 하세요.

### <a name="how-ocr-works"></a>OCR 작동 방법

[읽기 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) 는 최대 2000 페이지까지 텍스트를 많이 차지 하는 문서를 지원 하므로 비동기적으로 실행 됩니다. 첫 번째 단계는 읽기 작업을 호출 하는 것입니다. 읽기 작업은 이미지나 PDF 문서를 입력으로 사용 하 여 작업 ID를 반환 합니다. 

두 번째 단계는 [결과 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) 작업을 호출 하는 것입니다. 이 작업은 읽기 작업에서 만든 작업 ID를 사용 합니다. 그런 다음 이미지 또는 문서에서 추출 된 텍스트 콘텐츠를 JSON 형식으로 반환 합니다. JSON 응답은 인식 된 단어의 원래 줄 그룹을 유지 관리 합니다. 추출 된 텍스트 줄과 해당 경계 상자 좌표가 포함 됩니다. 각 텍스트 줄에는 추출 된 모든 단어가 해당 좌표와 신뢰도 점수가 포함 됩니다.

필요한 경우 다음 그림에 표시 된 것 처럼 읽기는 가로 이미지 축에 대 한 회전 오프셋 (도)을 반환 하 여 인식 된 페이지의 회전을 수정 합니다.

![OCR에서 이미지와 문서를 추출 된 텍스트로 구조화 된 출력으로 변환 하는 방법](./Images/how-ocr-works.svg)

### <a name="sample-ocr-output"></a>샘플 OCR 출력

성공적인 응답은 다음 예제와 같이 JSON 형식으로 반환 됩니다.

```json
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
              }
            ]
          }
        ]
      }
    ]
  }
}
```

C # 및 REST API를 사용 하 여 OCR을 구현 하려면 [인쇄 및 필기 텍스트 추출](./QuickStarts/CSharp-hand-text.md) 빠른 시작을 따르세요.

### <a name="input-requirements"></a>입력 요구 사항

읽기 API는 다음 입력을 사용 합니다.
* 지원 되는 파일 형식: JPEG, PNG, BMP, PDF 및 TIFF
* PDF 및 TIFF의 경우 최대 2000 페이지가 처리 됩니다. 무료 계층 구독자의 경우 처음 두 페이지만 처리 됩니다.
* 파일 크기는 50 MB 미만 이어야 하 고 크기는 50 x 50 픽셀 이상, 최대 1만 x 1만 픽셀 이어야 합니다.
* PDF 차원은 legal 또는 A3 용지 크기에 해당 하는 17 x 17 인치 여야 합니다.

### <a name="text-from-images"></a>이미지의 텍스트

다음 읽기 API 출력은 이미지에서 추출 된 텍스트 선과 단어를 다른 각도, 색 및 글꼴의 텍스트와 함께 보여 줍니다.

![회전 중인 이미지와 해당 텍스트를 읽고 구분 하 고 있습니다.](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>문서의 텍스트

이미지 외에도 읽기 API는 PDF 문서를 입력으로 사용 합니다.

![회전 중인 이미지와 해당 텍스트를 읽고 구분 하 고 있습니다.](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>필기 텍스트 (영어)

현재 읽기 작업은 필기 텍스트를 영어로만 추출할 수 있도록 지원 합니다.

![회전 중인 이미지와 해당 텍스트를 읽고 구분 하 고 있습니다.](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>지원 되는 언어로 인쇄 된 텍스트

읽기 API는 영어, 스페인어, 독일어, 프랑스어, 이탈리아어, 포르투갈어 및 네덜란드어 언어로 인쇄 텍스트 추출을 지원 합니다. 시나리오에서 더 많은 언어를 지원 해야 하는 경우이 문서의 OCR API 개요를 참조 하세요. 모든 [OCR 지원 언어](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) 목록을 참조 하세요.

![회전 중인 이미지와 해당 텍스트를 읽고 구분 하 고 있습니다.](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>혼합 언어 지원

읽기 API는 널리 사용 되는 여러 언어를 포함 하는 이미지와 문서를 지원 합니다. 텍스트 콘텐츠를 추출 하기 전에 문서의 각 텍스트 줄을 검색 된 언어로 분류 하 여이를 수행 합니다.

![회전 중인 이미지와 해당 텍스트를 읽고 구분 하 고 있습니다.](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 인식 서비스와 마찬가지로, 읽기 서비스를 사용 하는 개발자는 고객 데이터에 대 한 Microsoft 정책을 인식 해야 합니다. 자세한 내용은 [Microsoft 보안 센터](https://www.microsoft.com/en-us/trust-center/product-overview) 의 Cognitive Services 페이지를 참조 하세요.

### <a name="deploy-on-premises"></a>온-프레미스 배포

읽기는 새 OCR 기능을 사용자 환경에 배포할 수 있도록 Docker 컨테이너 (미리 보기)로도 사용할 수 있습니다. 컨테이너는 특정 보안 및 데이터 관리 요구 사항에 적합 합니다. [읽기 컨테이너를 설치 하 고 실행 하는 방법을](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) 참조 하세요.


## <a name="ocr-api"></a>OCR API

[OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 는 이전 인식 모델을 사용 하 고 이미지만 지원 하며 동기적으로 실행 되어 검색 된 텍스트로 즉시 반환 됩니다. Read API 보다는 [OCR 지원 언어](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [읽기 3.0 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)에 대해 알아봅니다.
- [텍스트 추출](./QuickStarts/CSharp-hand-text.md) 빠른 시작을 따라 REST API와 함께 c #, Java, JavaScript 또는 Python을 사용 하 여 OCR을 구현 합니다.
