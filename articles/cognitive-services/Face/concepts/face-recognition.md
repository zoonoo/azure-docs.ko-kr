---
title: 얼굴 인식 개념
titleSuffix: Azure Cognitive Services
description: 얼굴 인식 개념에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 8c0bf001c2bdbedaa041dbd10b5c7edb08eec4c6
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "64416047"
---
# <a name="face-recognition-concepts"></a>얼굴 인식 개념

이 문서에서는 다양한 얼굴 인식 작업(확인, 유사성 찾기, 그룹화, 식별) 및 기본 데이터 구조의 개념을 설명합니다. 대체로 인식은 두 개의 다른 얼굴을 비교하여 비슷한지 또는 같은 사람인지 결정하는 작업을 설명합니다.

## <a name="recognition-related-data-structures"></a>인식 관련 데이터 구조

인식 작업은 주로 다음 데이터 구조를 사용합니다. 이러한 개체는 클라우드에서 저장되고 ID 문자열로 참조할 수 있습니다. ID 문자열은 항상 구독 내에서 고유하지만 이름 필드는 중복이 가능합니다.

|이름|설명|
|:--|:--|
|**DetectedFace**| [얼굴 감지](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) 작업에서 검색된 단일 얼굴 표현입니다. 해당 ID를 만든 후 24시간 뒤에 만료됩니다.|
|**PersistedFace**| **DetectedFace** 개체가 그룹(**FaceList** 또는 **Person** 같은)에 추가되는 경우, **PersistedFace** 개체가 되며 언제든지 [검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)할 수 있으며 만료되지 않습니다.|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| **PersistedFace** 개체의 여러 가지 목록입니다. **FaceList** 는 고유 ID, 이름 문자열과 필요에 따라 사용자 데이터 문자열을 포함합니다.|
|**[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| 동일한 사용자에 속하는 **PersistedFace** 개체 목록입니다. 고유 ID, 이름 문자열과 필요에 따라 사용자 데이터 문자열을 포함합니다.|
|**[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| **Person** 개체의 여러 가지 목록입니다.  고유 ID, 이름 문자열과 필요에 따라 사용자 데이터 문자열을 포함합니다. **PersonGroup**은 인식 작업에 사용되기 전에 [학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)되어야 합니다.|

## <a name="recognition-operations"></a>인식 작업

이 섹션에서는 4개의 인식 작업에서 위의 데이터 구조를 사용하는 방법을 자세히 설명합니다. 각 인식 작업의 개략적인 설명은 [개요](../Overview.md)를 참조합니다.

### <a name="verification"></a>확인

[확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 작업은 face ID(**DetectedFace**또는 **PersistedFace**)와 다른 face ID 또는 **Person** 개체를 가져와서 동일한 사람에게 속하는지 여부를 결정합니다.  **Person**을 전달하는 경우, 성능을 개선하기 위해 **Person**이 속하는 **PersonGroup**을 선택적으로 전달할 수 있습니다.

### <a name="find-similar"></a>유사성 찾기

[유사성 찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 작업은 face ID(**DetectedFace** 또는 **PersistedFace**)와 **FaceList** 또는 다른 face ID 배열을 가져옵니다.  **FaceList**를 사용하여, 주어진 얼굴과 유사한 더 작은 **FaceList**를 반환합니다. face ID의 배열을 사용하여 더 작은 배열을 마찬가지로 반환합니다. 얼굴 Id의 배열을 사용 하 여 더 작은 배열 마찬가지로 반환합니다.

### <a name="grouping"></a>그룹화

[그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 작업은 여러 가지 face ID의(**DetectedFace** 또는 **PersistedFace**) 배열을 가져오고 여러 작은 배열로 그룹화된 동일 ID를 반환합니다. 각 "groups" 배열은 유사성이 있는 face ID를 포함하며 단일 "messyGroup" 배열은 유사성이 없는 face ID를 포함합니다.

### <a name="identification"></a>식별

합니다 [식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 작업은 하나 또는 여러 개의 얼굴 Id (**DetectedFace** 또는 **PersistedFace**) 및 **PersonGroup** 목록을 반환 합니다. **Person** 각 얼굴에 속할 수 있는 개체입니다. 반환 **Person** 개체 래핑되고 **후보** 예측 신뢰도 값이 있는 개체입니다.

## <a name="input-data"></a>데이터 입력

입력된 이미지로 가장 정확한 인식 결과를 얻으려면 다음 팁을 사용합니다.

* 지원되는 입력 이미지 형식은 BMP, GIF(첫 번째 프레임), PNG, JPEG입니다.
* 이미지 파일 크기는 4MB를 넘지 않도록 해야 합니다.
* **Person** 개체를 생성하는 경우, 사진 각도 및 조명의 다양한 기능을 사용해야 합니다.
* 일부 얼굴은 기술적인 문제 때문에 인식할 수 없습니다.
  * 극단적인 조명(예: 심각한 역광)을 사용한 이미지.
  * 눈 둘 또는 하나를 차단하는 장애물.
  * 모발 스타일 또는 수염에서의 차이.
  * 나이로 인한 얼굴 모양 변화.
  * 극단적인 얼굴 표정.

## <a name="next-steps"></a>다음 단계

얼굴 인식 개념에 익숙해졌으므로 학습된 **PersonGroup**에 대해 얼굴을 식별하는 간단한 스크립트를 작성하는 방법을 알아봅니다.

* [이미지에서 얼굴을 식별 하는 방법](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)