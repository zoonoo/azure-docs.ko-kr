---
title: Computer Vision API cURL 빠른 시작 OCR | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 cURL과 함께 Computer Vision을 사용하여 이미지의 인쇄 텍스트를 추출합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 3a9ebafa5e367c4fcd7dce63c54c9fdc14eb3812
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750609"
---
# <a name="quickstart-extract-printed-text-ocr---rest-curl"></a>빠른 시작: 인쇄 텍스트(OCR) 추출 - REST, cURL

이 빠른 시작에서는 Computer Vision을 사용하여 이미지에서 인쇄 텍스트를 추출하며, 이 기능은 OCR(광학 문자 인식)이라고도 합니다.

## <a name="prerequisites"></a>필수 조건

Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="ocr-request"></a>OCR 요청

[OCR 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)를 사용하여 이미지의 인쇄 텍스트를 감지하고, 인식된 문자를 머신에서 사용 가능한 문자 스트림으로 추출할 수 있습니다.

샘플을 실행하려면 다음 단계를 수행합니다.

1. 다음 코드를 편집기에 복사합니다.
1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 요청 URL(`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`)을 변경하여 구독 키를 가져온 위치를 사용합니다.
1. 필요에 따라 분석할 이미지(`{\"url\":\"...`)를 변경합니다.
1. cURL이 설치된 컴퓨터에서 명령 창을 엽니다.
1. 창에 코드를 붙여넣고 명령을 실행합니다.

>[!NOTE]
>구독 키를 가져오는 데 사용한 것과 동일한 위치를 REST 호출에서 사용해야 합니다. 예를 들어, westus에서 구독 키를 받은 경우 아래 URL의 "westcentralus"를 "westus"로 바꿉니다.

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr?language=unk&detectOrientation=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

## <a name="ocr-response"></a>OCR 응답

성공 시 반환되는 OCR 결과에는 텍스트, 영역, 선 및 단어의 경계 상자가 포함됩니다. 예:

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄 및 필기 텍스트를 추출하는 데 사용되는 Computer Vision API를 탐색합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API 탐색](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
