---
title: 얼굴 인식 개념
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 얼굴 인식 작업 및 기본 데이터 구조의 확인, 유사 찾기, 그룹 및 식별의 개념을 설명합니다.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743068"
---
# <a name="face-recognition-concepts"></a>얼굴 인식 개념

이 문서에서는 얼굴 인식 작업 및 기본 데이터 구조의 확인, 유사 찾기, 그룹 및 식별의 개념을 설명합니다. 대체로 인식은 두 개의 서로 다른 얼굴을 비교하여 동일한 사람이 비슷한지 또는 같은 사람에 속하는지 결정하는 작업을 설명합니다.

## <a name="recognition-related-data-structures"></a>인식 관련 데이터 구조

인식 작업은 주로 다음과 같은 데이터 구조를 사용합니다. 이러한 개체는 클라우드에 저장되며 ID 문자열에서 참조할 수 있습니다. ID 문자열은 구독 내에서 항상 고유합니다. 이름 필드를 복제할 수 있습니다.

|이름|설명|
|:--|:--|
|DetectedFace| 이 단일 얼굴 표현은 [얼굴 감지](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) 작업에 의해 검색됩니다. ID가 생성된 후 24시간 후에 만료됩니다.|
|PersistedFace| 감지된Face 개체가 FaceList 또는 Person와 같은 그룹에 추가되면 지속얼굴 개체가 됩니다. 언제든지 [검색할](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) 수 있으며 만료되지 않습니다.|
|[페이스리스트](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) 또는 [대형 페이스리스트](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 이 데이터 구조는 PersistedFace 개체의 모듬된 목록입니다. FaceList에는 고유 ID, 이름 문자열 및 선택적으로 사용자 데이터 문자열이 있습니다.|
|[사람](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 이 데이터 구조는 동일한 사람에 속하는 PersistedFace 개체의 목록입니다. 고유 ID, 이름 문자열 및 선택적으로 사용자 데이터 문자열이 있습니다.|
|[사람 그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 또는 [대형 인물 그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 이 데이터 구조는 사람 개체의 모듬된 목록입니다. 고유 ID, 이름 문자열 및 선택적으로 사용자 데이터 문자열이 있습니다. PersonGroup은 인식 작업에 사용하려면 먼저 [교육을](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) 받아야 합니다.|

## <a name="recognition-operations"></a>인식 작업

이 섹션에서는 네 가지 인식 작업이 앞서 설명한 데이터 구조를 사용하는 방법을 자세히 설명합니다. 각 인식 작업에 대한 광범위한 설명은 [개요 를](../Overview.md)참조하십시오.

### <a name="verify"></a>확인

[확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 작업은 감지된 얼굴 또는 PersistedFace에서 얼굴 ID를 가져와 다른 얼굴 ID 또는 사람 개체를 가져와 동일한 사람에 속하는지 여부를 결정합니다. Person 개체를 전달하는 경우 성능 향상을 위해 해당 사람이 속한 PersonGroup을 선택적으로 전달할 수 있습니다.

### <a name="find-similar"></a>Find Similar(유사 얼굴 찾기)

[유사 찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 작업은 감지된 얼굴 또는 지속얼굴에서 얼굴 ID를 취하고 FaceList 또는 다른 얼굴 ID의 배열을 합니다. FaceList를 사용하면 지정된 면과 유사한 작은 얼굴 리스트를 반환합니다. 얼굴 아이디 배열을 사용하면 마찬가지로 더 작은 배열을 반환합니다.

### <a name="group"></a>그룹

[그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 작업은 DetectedFace 또는 PersistedFace에서 모듬된 얼굴 아이디 배열을 취하고 여러 개의 작은 배열로 그룹화된 동일한 ID를 반환합니다. 각 "그룹" 배열에는 유사하게 보이는 얼굴 ID가 포함되어 있습니다. 단일 "지저분한 그룹" 배열에는 유사점이 발견되지 않은 얼굴 ID가 포함되어 있습니다.

### <a name="identify"></a>Identify

[식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 작업은 DetectedFace 또는 PersistedFace 및 PersonGroup에서 하나 또는 여러 개의 얼굴 식별을 가져와 각 얼굴이 속할 수 있는 사람 개체 목록을 반환합니다. 반환된 사람 개체는 예측 신뢰도 값이 있는 후보 개체로 래핑됩니다.

## <a name="input-data"></a>데이터 입력

다음 팁을 사용하여 입력 이미지가 가장 정확한 인식 결과를 제공할 수 있도록 합니다.

* 지원되는 입력 이미지 형식은 JPEG, PNG, GIF(첫 번째 프레임), BMP입니다.
* 이미지 파일 크기는 4MB를 초과해서는 안 됩니다.
* 사람 개체를 만들 때 다양한 각도와 조명이 특징인 사진을 사용합니다.
* 다음과 같은 기술적 문제로 인해 일부 얼굴이 인식되지 않을 수 있습니다.
  * 예를 들어, 심한 백라이트와 같은 극단적인 조명이 있는 이미지.
  * 한쪽 또는 양쪽 눈을 차단하는 장애물.
  * 모발 유형 또는 얼굴 털의 차이.
  * 나이 때문에 얼굴 외관의 변화.
  * 극단적 인 얼굴 표정.

## <a name="next-steps"></a>다음 단계

이제 얼굴 인식 개념에 익숙해졌으니 숙련된 PersonGroup에 대한 얼굴을 식별하는 스크립트를 작성하는 방법을 알아봅니다.

* [이미지에서 얼굴 식별](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)