---
title: Computer Vision API cURL 빠른 시작 썸네일 가져오기 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 cURL과 함께 Computer Vision을 사용하여 이미지에서 썸네일을 생성합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 4056f05f3f4bf97761f683b5f3a9053666d4ea26
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750601"
---
# <a name="quickstart-generate-a-thumbnail---rest-curl"></a>빠른 시작: 썸네일 생성 - REST, cURL

이 빠른 시작에서는 Computer Vision을 사용하여 이미지에서 썸네일을 생성합니다.

## <a name="prerequisites"></a>필수 조건

Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="get-thumbnail-request"></a>썸네일 가져오기 요청

[썸네일 가져오기 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)를 사용하여 이미지의 썸네일을 생성할 수 있습니다. 높이와 너비를 지정합니다. 입력 이미지의 가로 세로 비율과 다를 수 있습니다. Computer Vision은 스마트 자르기를 사용하여 관심 영역을 지능적으로 식별하고 해당 영역을 기반으로 자르기 좌표를 생성합니다.

샘플을 실행하려면 다음 단계를 수행합니다.

1. 다음 코드를 편집기에 복사합니다.
1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. `<File>`을 썸네일을 저장할 경로와 파일 이름으로 바꿉니다.
1. 필요한 경우 요청 URL(`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`)을 변경하여 구독 키를 가져온 위치를 사용합니다.
1. 필요에 따라 분석할 이미지(`{\"url\":\"...`)를 변경합니다.
1. cURL이 설치된 컴퓨터에서 명령 창을 엽니다.
1. 창에 코드를 붙여넣고 명령을 실행합니다.

>[!NOTE]
>구독 키를 가져오는 데 사용한 것과 동일한 위치를 REST 호출에서 사용해야 합니다. 예를 들어, westus에서 구독 키를 받은 경우 아래 URL의 "westcentralus"를 "westus"로 바꿉니다.

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -o <File> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
```

## <a name="get-thumbnail-response"></a>썸네일 가져오기 응답

성공적인 응답은 `<File>`에 썸네일 이미지를 씁니다. 요청이 실패하면 무엇이 잘못되었는지 확인할 수 있도록 응답에 오류 코드 및 메시지가 포함됩니다.

## <a name="next-steps"></a>다음 단계

이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄 및 필기 텍스트를 추출하는 데 사용되는 Computer Vision API를 탐색합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API 탐색](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
