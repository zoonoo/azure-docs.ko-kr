---
title: 얼굴 인식 개념
titleSuffix: Azure Cognitive Services
description: Face recognition 얼굴 인식에 대 한 개념에 알아봅니다.
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

이 문서에서는 다양 한 얼굴 인식 작업 (확인, 마찬가지로 그룹화를 식별 하는 찾기) 및 기본 데이터 구조의 개념을 설명 합니다. 넓은 의미로 볼 인식 비슷한 경우를 확인 하려면 두 개의 다른 면 비교 작업을 설명 되거나 같은 사람에 속해야 합니다.

## <a name="recognition-related-data-structures"></a>인식 관련 데이터 구조

인식 작업에는 다음 데이터 구조에 주로 사용합니다. 이러한 개체는 클라우드에서 저장 되 고 해당 ID 문자열에서 참조할 수 있습니다. ID 문자열 되므로 항상 구독 내에서 고유 하지만 이름 필드를 복제할 수 있습니다.

|이름|설명|
|:--|:--|
|**DetectedFace**| 이 단일 표면 표현 하 여 검색할 합니다 [얼굴 감지](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) 작업 합니다. 해당 ID를 만든 후 24 시간에 만료 됩니다.|
|**PersistedFace**| 때 **DetectedFace** 개체를 그룹에 추가 됩니다 (같은 **FaceList** 또는 **Person**), 해지기 **PersistedFace** 수 있는 개체 수 [검색할](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) 언제 든 시간 및 만료 되지 않습니다.|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| 이 되는 선별 된 목록이 며 **PersistedFace** 개체입니다. A **FaceList** 고유 ID, 이름 문자열을 및 필요에 따라 사용자 데이터 문자열을 포함 합니다.|
|**[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| 이 목록을 **PersistedFace** 동일한 사용자에 속하는 개체입니다. 고유 ID, 이름 문자열 및 필요에 따라 사용자 데이터 문자열을 있습니다.|
|**[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| 이 되는 선별 된 목록이 며 **Person** 개체입니다. 고유 ID, 이름 문자열 및 필요에 따라 사용자 데이터 문자열을 있습니다. A **PersonGroup** 있어야 [학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) 인식 작업에 사용할 수 있습니다.|

## <a name="recognition-operations"></a>인식 작업

이 섹션에서는 4 개의 인식 작업에서 위의 데이터 구조를 사용 하는 방법을 자세히 설명 합니다. 참조 된 [개요](../Overview.md) 광범위 한 설명은 각 인식 작업에 대 한 합니다.

### <a name="verification"></a>확인

[확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) face ID를 사용 하는 작업 (**DetectedFace** 하거나 **PersistedFace**) 및 다른 face ID 중 하나 또는 **Person** 개체 및 동일한 사람에 게 속하는지 여부를 결정 합니다. 에 전달 하는 경우는 **Person**, 선택적으로 전달할 수 있습니다를 **PersonGroup** 를 하 고 있는 **사용자** 성능을 개선 하기 위해 속한.

### <a name="find-similar"></a>비슷한

[비슷한](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) face ID를 사용 하는 작업 (**DetectedFace** 또는 **PersistedFace**) 및 중 하나는 **FaceList** 또는 다른 얼굴 배열을 Id입니다. 사용 하 여는 **FaceList**, 작은 반환 **FaceList** 주어진된 얼굴 유사한 얼굴입니다. 얼굴 Id의 배열을 사용 하 여 더 작은 배열 마찬가지로 반환합니다.

### <a name="grouping"></a>Grouping(그룹화)

[그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 작업 사용 분류 된 얼굴 Id의 배열 (**DetectedFace** 하거나 **PersistedFace**) 여러 작은 배열이 그룹화 된 동일한 Id를 반환 합니다. 얼굴 마찬가지로 표시 되는 Id를 포함 하는 각 "groups" 배열 및 face Id를 포함 하는 단일 "messyGroup" 배열에 없는 유사성을 찾았습니다.

### <a name="identification"></a>Identification(식별)

합니다 [식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 작업은 하나 또는 여러 개의 얼굴 Id (**DetectedFace** 또는 **PersistedFace**) 및 **PersonGroup** 목록을 반환 합니다. **Person** 각 얼굴에 속할 수 있는 개체입니다. 반환 **Person** 개체 래핑되고 **후보** 예측 신뢰도 값이 있는 개체입니다.

## <a name="input-data"></a>데이터 입력

입력된 이미지는 가장 정확 하 게 인식 결과 얻을 수 있도록 다음 팁을 사용 합니다.

* 지원 되는 입력된 이미지 형식은 BMP, GIF (첫 번째 프레임), PNG, JPEG 없기 때문입니다.
* 이미지 파일 크기는 4MB를 넘지 않도록 해야 합니다.
* 만들면 **Person** 개체 사진 각도 및 조명의 다양 한 기능을 사용 해야 합니다.
* 일부 얼굴 기술적인 문제 때문에 인식할 수 없습니다.
  * 극단적인 조명 (예: 심각한 역광)를 사용 하 여 이미지를 제공 합니다.
  * 하나 또는 둘 다 눈을 차단 하는 장애물입니다.
  * 십자선 스타일 또는 얼굴 머리카락에서 차이점입니다.
  * 보존 기간으로 인해 글꼴 모양이 변경 됩니다.
  * 극단적인 얼굴 식입니다.

## <a name="next-steps"></a>다음 단계

얼굴 인식 개념에 익숙한 했으므로 학습에 대 한 얼굴을 식별 하는 간단한 스크립트를 작성 하는 방법에 알아봅니다 **PersonGroup**합니다.

* [이미지에서 얼굴을 식별 하는 방법](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)