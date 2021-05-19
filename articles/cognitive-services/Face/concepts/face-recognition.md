---
title: 얼굴 인식 개념
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Verify, Find Similar, Group, Identify 얼굴 인식 작업의 개념과 기본 데이터 구조를 설명합니다.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 00dadf8a91b7ed01ab9f91933d296744305a95af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92518809"
---
# <a name="face-recognition-concepts"></a>얼굴 인식 개념

이 문서에서는 Verify, Find Similar, Group, Identify 얼굴 인식 작업의 개념과 기본 데이터 구조를 설명합니다. 넓은 의미로 인식이란 두 개의 다른 얼굴을 비교해서 이들이 닮은 건지 동일한 사람에 속하는지를 확인하는 작업을 말합니다.

## <a name="recognition-related-data-structures"></a>인식 관련 데이터 구조

인식 작업에서는 주로 다음과 같은 데이터 구조가 사용됩니다. 이러한 개체를 클라우드에 저장하고 해당 ID 문자열로 참조할 수 있습니다. ID 문자열은 한 구독 내에서 항상 고유합니다. 이름 필드는 중복될 수 있습니다.

|이름|Description|
|:--|:--|
|DetectedFace| 단일 얼굴 표현은 [얼굴 감지](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) 작업에서 검색됩니다. 해당 ID는 만든 후 24시간 후에 만료됩니다.|
|PersistedFace| FaceList 또는 Person과 같은 DetectedFace 개체를 그룹에 추가하면 PersistedFace 개체가 됩니다. 언제든지 [검색할](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) 수 있으며 만료되지 않습니다.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) 또는 [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 이 데이터 구조는 PersistedFace 개체의 다양한 목록입니다. FaceList에는 고유 ID, 이름 문자열, 그리고 선택적으로 사용자 데이터 문자열이 포함됩니다.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 이 데이터 구조는 동일한 사람에 속하는 PersistedFace 개체의 목록입니다. 여기에는 고유 ID, 이름 문자열, 선택적으로 사용자 데이터 문자열이 포함됩니다.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 또는 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 이 데이터 구조는 Person 개체의 다양한 목록입니다. 여기에는 고유 ID, 이름 문자열, 선택적으로 사용자 데이터 문자열이 포함됩니다. PersonGroup을 먼저 [학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)시켜야만 인식 작업에 사용할 수 있습니다.|

## <a name="recognition-operations"></a>인식 작업

이 섹션에서는 네 가지 인식 작업이 이전에 설명한 데이터 구조를 사용하는 방법을 세부적으로 살펴봅니다. 각 인식 작업에 대한 광범위한 설명은 [개요](../Overview.md)를 참조하세요.

### <a name="verify"></a>확인

[Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 작업은 DetectedFace 또는 PersistedFace에서 얼굴 ID 하나를 가져오고 다른 얼굴 ID 또는 Person 개체 하나를 가져와서 이들이 동일한 사람에게 속하는지 여부를 확인합니다. Person 개체를 전달하는 경우 해당 Person이 속하는 PersonGroup를 선택적으로 전달하여 성능을 향상시킬 수 있습니다.

### <a name="find-similar"></a>Find Similar(유사 얼굴 찾기)

[Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 작업은 DetectedFace 또는 PersistedFace에서 얼굴 ID 하나를 가져오고 FaceList 또는 다른 얼굴 ID의 배열을 하나 가져옵니다. FaceList로는 지정된 얼굴과 유사한 얼굴로 구성된 더 작은 FaceList를 반환합니다. 얼굴 ID 배열로는 마찬가지로 더 작은 배열을 반환합니다.

### <a name="group"></a>그룹

[Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 작업은 DetectedFace 또는 PersistedFace에서 다양한 얼굴 ID 배열을 가져오고 동일한 ID를 여러 개의 더 작은 배열로 그룹화하여 반환합니다. 각 "groups" 배열에는 유사하게 나타나는 얼굴 ID가 포함됩니다. 단일 "messyGroup" 배열에는 유사성을 전혀 찾을 수 없는 얼굴 ID가 포함됩니다.

### <a name="identify"></a>Identify

[Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 작업은 DetectedFace 또는 PersistedFace 및 PersonGroup에서 하나 또는 여러 개의 얼굴 ID를 가져와 각 얼굴이 속할 수 있는 Person 개체 목록을 반환합니다. 반환된 Person 개체는 예측 신뢰도 값을 갖는 후보 개체로 래핑됩니다.

## <a name="input-data"></a>입력 데이터

다음과 같은 팁을 사용하여 입력 이미지가 가장 정확한 인식 결과를 제공하는지 확인합니다.

* 지원되는 입력 이미지 형식은 JPEG, PNG, GIF(첫 번째 프레임), BMP입니다.
* 이미지 파일 크기는 6MB를 넘지 않아야 합니다.
* Person 개체를 만들 때 여러 가지 종류의 각도와 조명을 보여 주는 사진을 사용합니다.
* 다음과 같은 기술적인 문제로 인해 일부 얼굴이 인식되지 않을 수 있습니다.
  * 극단적인 조명(예: 심한 역광)이 있는 이미지
  * 한쪽 또는 양쪽 눈을 가리는 장애물
  * 머리카락 유형 또는 수염의 차이
  * 나이로 인한 얼굴 외모의 변화
  * 극단적인 표정

## <a name="next-steps"></a>다음 단계

이제 얼굴 인식 개념에 익숙해졌으므로 학습된 PersonGroup에 대해 얼굴을 식별하는 스크립트를 작성합니다.

* [얼굴 클라이언트 라이브러리 빠른 시작](../Quickstarts/client-libraries.md)