---
title: API 콘솔을 사용하여 이미지 조정 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Azure Content Moderator의 Image Moderation API를 사용하여 이미지 콘텐츠에 대한 검사-검토 조정 워크플로를 시작합니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: f4c74e668e3f0b96f8350447424b7d6d06e34f16
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218736"
---
# <a name="moderate-images-from-the-api-console"></a>API 콘솔에서 이미지 조정

Azure Content Moderator의 [Image Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)를 사용하여 이미지 콘텐츠에 대한 검사-검토 조정 워크플로를 시작합니다. 조정 작업은 콘텐츠에서 비속어를 검색하여 사용자 지정 및 공유 블랙리스트와 비교합니다.

## <a name="use-the-api-console"></a>API 콘솔 사용
온라인 콘솔에서 API를 테스트하려면 구독 키가 필요합니다. 구독 키는 **Ocp-Apim-Subscription-Key** 상자의 **설정** 탭에 있습니다. 자세한 내용은 [개요](overview.md)를 참조하세요.

1.  [Image Moderation API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)로 이동합니다.

  **이미지 - 평가** 이미지 조정 페이지가 열립니다.

2. **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 

  ![이미지 시도 - 평가 페이지 지역 선택](images/test-drive-region.png)
  
  **이미지 - 평가** API 콘솔이 열립니다.

3. **Ocp-Apim-Subscription-Key** 상자에 구독 키를 입력합니다.

  ![이미지 시도 - 평가 콘솔 구독 키](images/try-image-api-1.PNG)

4. **요청 본문** 상자에서 기본 샘플 이미지를 사용하거나 검사할 이미지를 지정합니다. 이미지 자체를 이진 비트 데이터로 제출할 수도 있고, 공개적으로 액세스 가능한 이미지 URL을 지정할 수도 있습니다. 

  이 예제에서는 **요청 본문** 상자에 제공된 경로를 사용한 다음, **보내기**를 선택합니다. 

   ![이미지 시도 - 평가 콘솔 요청 본문](images/try-image-api-2.PNG)

  다음은 해당 URL의 이미지입니다.

  ![이미지 시도 - 평가 콘솔 샘플 이미지](images/sample-image.jpg) 

5. **보내기**를 선택합니다.

6. API는 각 분류의 확률 점수를 반환합니다. 또한 이미지가 조건을 충족하는지 확인 결과를 반환합니다(**true** 또는 **false**). 

  ![이미지 시도 - 평가 콘솔 확률 점수 및 조건 확인](images/try-image-api-3.PNG)

## <a name="face-detection"></a>얼굴 감지

Image Moderation API를 사용하여 이미지에서 얼굴을 찾을 수 있습니다. 개인 정보 보호 문제가 있고 특정 얼굴이 플랫폼에 게시되지 않게 차단하려는 경우 이 옵션을 유용하게 사용할 수 있습니다. 

1.  [Image Moderation API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)의 왼쪽 메뉴에 있는 **이미지** 아래에서 **얼굴 찾기**를 선택합니다. 

  **이미지 - 얼굴 찾기** 페이지가 열립니다.

2.  **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 

  ![이미지 시도 - 얼굴 찾기 페이지 지역 선택](images/test-drive-region.png)

  **이미지 - 얼굴 찾기** API 콘솔이 열립니다.

3. 검사할 이미지를 지정합니다. 이미지 자체를 이진 비트 데이터로 제출해도 되고, 공개적으로 액세스 가능한 이미지 URL을 지정해도 됩니다. 이 예제에서는 CNN 스토리에 사용되는 이미지에 연결합니다.

  ![이미지 시도 - 얼굴 찾기 샘플 이미지](images/try-image-api-face-image.jpg)

  ![이미지 시도 - 얼굴 찾기 샘플 요청](images/try-image-api-face-request.png)

4. **보내기**를 선택합니다. 이 예제에서는 API가 두 얼굴을 찾아서 이미지의 해당 좌표를 반환합니다.

   ![이미지 시도 - 얼굴 찾기 샘플 응답 콘텐츠 상자](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>OCR 기능을 통한 텍스트 검색

Content Moderator OCR 기능을 사용하여 이미지의 텍스트를 검색할 수 있습니다.

1. [Image Moderation API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)의 왼쪽 메뉴에 있는 **이미지** 아래에서 **OCR**을 선택합니다. 

  **이미지 - OCR** 페이지가 열립니다.

2. **개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 

  ![이미지 - OCR 페이지 지역 선택](images/test-drive-region.png)

  **이미지 - OCR** API 콘솔이 열립니다.

3. **Ocp-Apim-Subscription-Key** 상자에 구독 키를 입력합니다.

4. **요청 본문** 상자에서 기본 샘플 이미지를 사용합니다. 이전 섹션에서 사용한 것과 동일한 이미지입니다.

5. **보내기**를 선택합니다. 추출된 텍스트는 JSON으로 표시됩니다.

  ![이미지 - OCR 샘플 응답 콘텐츠 상자](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>다음 단계

코드에서 REST API를 사용하거나 [이미지 조정 .NET 빠른 시작](image-moderation-quickstart-dotnet.md)을 시작하여 애플리케이션을 통합합니다.
