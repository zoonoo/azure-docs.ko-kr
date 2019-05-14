---
title: 얼굴 감지 및 특성 개념
titleSuffix: Azure Cognitive Services
description: 얼굴 감지 및 얼굴 특성에 대한 개념을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572542"
---
# <a name="face-detection-and-attributes"></a>얼굴 감지 및 특성

이 문서는 얼굴 감지 및 얼굴 특성 데이터의 개념을 설명합니다. 얼굴 감지는 이미지에서 사람 얼굴을 찾고 필요에 따라 다양한 얼굴 관련 데이터를 반환하는 작업입니다.

이미지에서 얼굴을 감지하기 위해 [얼굴-감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 작업을 사용합니다. 최소한, 검색된 각 얼굴은 응답에서 **faceRectangle** 필드에 해당합니다. 이는 해당 얼굴 위치를 표시하는 픽셀 좌표(왼쪽, 위쪽, 너비, 높이) 집합입니다. 이러한 좌표를 사용하여, 해당 크기 뿐만 아니라 얼굴의 위치를 가져올 수 있습니다. API 응답에서 얼굴은 가장 큰 크기에서 가장 작은 크기 순서대로 나열됩니다.

## <a name="face-id"></a>Face ID(얼굴 ID)

face ID는 이미지에서 감지된 각 얼굴에 대한 고유 식별자 문자열입니다. [얼굴-감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 호출에서 face ID를 요청할 수 있습니다.

## <a name="face-landmarks"></a>얼굴 랜드마크

얼굴 랜드마크는 동공이나 코 끝 같은 얼굴에서 찾기 쉬운 포인트들의 집합입니다. 기본적으로 27개의 랜드마크 포인트가 사전 정의되어 있습니다. 다음 그림은 모든 27개의 포인트을 보여 줍니다.

![레이블이 지정 된 모든 27 개의 특징을 사용 하 여 얼굴 다이어그램](../Images/landmarks.1.jpg)

포인트의 좌표는 픽셀 단위로 반환됩니다.

## <a name="attributes"></a>특성

특성은 [얼굴-감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API에서 선택적으로 감지되는 추가적인 얼굴 특징의 집합입니다. 다음은 감지되는 특성입니다.

* **Age** 특정 얼굴의 예상 나이, 세로 제공됩니다.
* **Blur** 이미지에서 얼굴의 흐릿함. 비공식적인 등급("low", "medium", "high") 뿐만 아니라 0 및 1 사이의 값을 반환합니다.
* **노출** 이미지에서 얼굴의 노출. 비공식적인 등급(예: "underExposure", "goodExposure", "overExposure") 뿐만 아니라 0 및 1 사이의 값을 반환합니다.
* **감정** 특정 얼굴에 대한 감지 신뢰도를 사용한 감정의 목록. 신뢰성 점수는 정규화되어 모든 감정 점수의 합은 1이 됩니다. 반환된 감정은 행복, 슬픔, 중립, 분노, 경멸, 혐오, 놀람 및 두려움입니다.
* **수염** 주어진 얼굴의 수염 유무 및 길이 추정치.
* **성별** 주어진 얼굴의 예상 성별. 가능한 값은 "male", "female", "genderless"입니다.
* **안경** 주어진 얼굴에 안경이 있는지 여부. 가능한 값은 "NoGlasses", "ReadingGlasses", "Sunglasses" 및 "Swimming Goggles" 입니다.
* **모발** 얼굴의 모발 스타일. 모발이 표시되는지 여부, 대머리로 감지되는지 여부, 어떤 모발 색이 감지되는지 보여 줍니다.
* **머리 포즈** 3D 공간에서 발생 하는 방향입니다. 피치, 롤 및 요 각도로 설명합니다. 값 범위는 각각 [-90, 90] [-180, 180] 및 [-90, 90] 입니다. 각도 매핑은 다음 다이어그램을 참조 하세요.

    ![피치를 사용 하 여 헤드, 롤백 및 축 레이블이 yaw](../Images/headpose.1.jpg)
* **화장** 화장했는지 여부. "eyeMakeup" 및 "lipMakeup"에 대해 부울 값을 반환합니다.
* **노이즈** 얼굴 이미지에서 감지된 시각적 노이즈. 비공식적인 등급 ("low", "medium", "high") 뿐만 아니라 0 및 1 사이의 값을 반환합니다.
* **폐색** 얼굴의 부분을 차단하는 개체가 있는지 여부. "eyeOccluded", "foreheadOccluded" 및 "mouthOccluded"에 대해 부울 값을 반환합니다.
* **미소** 주어진 얼굴의 미소 표현. 0(미소 없음)과 1(미소) 사이의 값입니다.

> [!IMPORTANT]
> 얼굴 특성은 통계 알고리즘을 사용하여 예측하며 항상 정확하지는 않을 수 있습니다. 특성 데이터를 기반으로 결정을 내릴 경우 주의해야 합니다.

## <a name="input-data"></a>데이터 입력

입력된 이미지로 가장 정확한 감지 결과를 얻으려면 다음 팁을 사용합니다.

* 지원되는 입력 이미지 형식은 BMP, GIF(첫 번째 프레임), PNG, JPEG입니다.
* 이미지 파일 크기는 4MB를 넘지 않도록 해야 합니다.
* 감지 가능한 얼굴 크기 범위는 36x36 픽셀부터 4096x4096 픽셀 사이입니다. 이 범위 밖의 얼굴은 감지할 수 없습니다.
* 일부 얼굴은 기술적인 문제 때문에 감지되지 않을 수 있습니다. 극단적인 얼굴 각도(머리 포즈) 또는 얼굴 폐색(손이나 선글라스 같은 개체로 얼굴 일부분 차단)은 감지에 영향을 줄 수 있습니다. 정면 및 정면에 가까운 얼굴이 최상의 결과를 제공합니다.

## <a name="next-steps"></a>다음 단계

얼굴 감지 개념에 익숙해졌으므로, 지정된 이미지에서 얼굴을 감지하는 간단한 스크립트를 작성하는 방법에 대해 알아봅니다.

* [이미지에서 얼굴을 감지 하는 방법](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
