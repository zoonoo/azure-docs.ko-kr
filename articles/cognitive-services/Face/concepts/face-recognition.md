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
ms.openlocfilehash: fa38c492530cb8938e49bc15e13fdd39ed5b6f1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65890891"
---
# <a name="face-recognition-concepts"></a>얼굴 인식 개념

이 문서에서는 기본 데이터 구조를 확인, 유사한 찾기, 그룹 및 식별 얼굴 인식 작업을 개념을 설명합니다. 넓은 의미로 볼 인식 비슷하지만 또는 동일한 사람에 게 속한 여부를 확인 하려면 두 개의 다른 면 비교 작업을 설명 합니다.

## <a name="recognition-related-data-structures"></a>인식 관련 데이터 구조

인식 작업은 주로 다음 데이터 구조를 사용합니다. 이러한 개체는 클라우드에서 저장되고 ID 문자열로 참조할 수 있습니다. ID 문자열은 항상 구독 내에서 고유 합니다. 이름 필드를 복제할 수 있습니다.

|이름|설명|
|:--|:--|
|DetectedFace| 이 단일 얼굴 표현을 검색 합니다 [얼굴 감지](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) 작업 합니다. 해당 ID를 만든 후 24 시간에 만료 됩니다.|
|PersistedFace| DetectedFace 개체 FaceList 등 사용자 그룹에 추가 되 면 해지기 PersistedFace 개체입니다. 수 [검색할](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) 언제 든 시간 및 만료 되지 않습니다.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) 또는 [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 이 데이터 구조는 PersistedFace 개체는 선별 된 목록입니다. FaceList 고유 ID, 이름 문자열 및 필요에 따라 사용자 데이터 문자열에 있습니다.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 이 데이터 구조는 동일한 사람에 게 속한 PersistedFace 개체의 목록입니다. 고유 ID, 이름 문자열과 필요에 따라 사용자 데이터 문자열을 포함합니다.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 또는 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 이 데이터 구조는 사용자 개체를 선별 된 목록. 고유 ID, 이름 문자열과 필요에 따라 사용자 데이터 문자열을 포함합니다. PersonGroup 여야 [학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) 인식 작업에 사용할 수 있습니다.|

## <a name="recognition-operations"></a>인식 작업

이 섹션에서는 4 개의 인식 작업 앞에서 설명한 데이터 구조를 사용 하는 방법을 자세히 설명 합니다. 각 인식 작업의 광범위 한 설명을 참조 하세요 [개요](../Overview.md)합니다.

### <a name="verify"></a>확인

합니다 [확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 작업 DetectedFace 또는 PersistedFace 다른 얼굴 ID 또는 사용자 개체에서 face ID를 가져와 같은 사람에 속하는지 여부를 결정 합니다. 사용자 개체에 전달 하는 경우에 성능 향상을 위해 해당 사용자가 속해 있는 PersonGroup에서 선택적으로 전달할 수 있습니다.

### <a name="find-similar"></a>Find Similar(유사 얼굴 찾기)

합니다 [유사한 찾을](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 작업 DetectedFace 또는 PersistedFace는 FaceList 또는 다른 얼굴 Id의 배열에서 face ID를 사용 합니다. FaceList를 사용 하 여 해당된 얼굴 유사한 얼굴의 작은 FaceList 반환 합니다. 얼굴 Id의 배열을 사용 하 여 더 작은 배열 마찬가지로 반환합니다.

### <a name="group"></a>그룹

합니다 [그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 작업 DetectedFace 또는 PersistedFace에서 선별 된 얼굴 Id의 배열을 사용 하 고 여러 작은 배열이 그룹화 된 동일한 Id를 반환 합니다. 얼굴와 비슷하게 표시 되는 Id를 포함 하는 각 "groups" 배열입니다. 얼굴 Id를 포함 하는 단일 "messyGroup" 배열에 없는 유사성을 찾았습니다.

### <a name="identify"></a>식별

합니다 [식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 작업 DetectedFace 있고 PersistedFace를 PersonGroup에서 하나 또는 여러 개의 얼굴 Id를 가져와 각 얼굴에 속할 수 있는 사용자 개체의 목록을 반환 합니다. 예측 신뢰도 값이 있는 후보 개체로 래핑된 개체는 사용자를 반환 합니다.

## <a name="input-data"></a>데이터 입력

입력된 이미지로 가장 정확한 인식 결과를 얻으려면 다음 팁을 사용합니다.

* 지원되는 입력 이미지 형식은 BMP, GIF(첫 번째 프레임), PNG, JPEG입니다.
* 이미지 파일 크기는 4MB를 넘지 않도록 해야 합니다.
* 사용자 개체를 만든 경우 사진 다른 종류의 각도 및 조명 기능 들을 사용 합니다.
* 일부 얼굴 기술적인 문제 때문 같은 인식 되지 않을 수 있습니다.
  * 극단적인 조명, 예를 들어, 심각한를 사용 하 여 이미지 역광 합니다.
  * 하나 또는 둘 다 눈을 차단 하는 장애물입니다.
  * 십자선 형식 또는 얼굴 머리카락에서 차이점입니다.
  * 보존 기간으로 인해 얼굴 모양이 변경 됩니다.
  * 극단적인 얼굴 표정.

## <a name="next-steps"></a>다음 단계

얼굴 인식 개념에 익숙한 했으므로 학습된 PersonGroup에 대 한 얼굴을 식별 하는 스크립트를 작성 하는 방법에 알아봅니다.

* [이미지에서 얼굴 식별](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)