---
title: 얼굴 인식 개념
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 확인, 유사 하 게 찾기, 그룹 및 식별 얼굴 인식 작업 및 기본 데이터 구조에 대 한 개념을 설명 합니다.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 3957a9cde957c8e92806f10d39c949d73f20153e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323025"
---
# <a name="face-recognition-concepts"></a>얼굴 인식 개념

이 문서에서는 확인, 유사 하 게 찾기, 그룹 및 식별 얼굴 인식 작업 및 기본 데이터 구조에 대 한 개념을 설명 합니다. 광범위 한 인식은 동일한 사람에 게 비슷하거나 속하는지 여부를 확인 하기 위해 서로 다른 두 개의 얼굴을 비교 하는 작업을 설명 합니다.

## <a name="recognition-related-data-structures"></a>인식 관련 데이터 구조

인식 작업에서는 주로 다음과 같은 데이터 구조를 사용 합니다. 이러한 개체는 클라우드에 저장 되며 해당 ID 문자열에서 참조할 수 있습니다. ID 문자열은 항상 구독 내에서 고유 합니다. 이름 필드는 중복 될 수 있습니다.

|Name|설명|
|:--|:--|
|DetectedFace| 이 단일 얼굴 표현은 [얼굴 감지](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) 작업에서 검색 됩니다. 해당 ID는 만든 후 24 시간 후에 만료 됩니다.|
|PersistedFace| DetectedFace 개체를 그룹에 추가 하면 (예: FaceList 또는 Person) PersistedFace 개체가 됩니다. 언제 든 지 [검색할](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) 수 있으며 만료 되지 않습니다.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) 또는 [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 이 데이터 구조는 다양 한 PersistedFace 개체 목록입니다. FaceList에는 고유 ID, 이름 문자열 및 선택적으로 사용자 데이터 문자열이 있습니다.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 이 데이터 구조는 동일한 사람에 속하는 PersistedFace 개체의 목록입니다. 고유 ID, 이름 문자열 및 선택적으로 사용자 데이터 문자열을 포함 합니다.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 또는 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 이 데이터 구조는 사용자 개체를 분류 하는 목록입니다. 고유 ID, 이름 문자열 및 선택적으로 사용자 데이터 문자열을 포함 합니다. PersonGroup를 인식 작업에 사용 하려면 먼저 [학습](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) 해야 합니다.|

## <a name="recognition-operations"></a>인식 작업

이 섹션에서는 네 가지 인식 작업이 앞에서 설명한 데이터 구조를 사용 하는 방법에 대해 자세히 설명 합니다. 각 인식 작업에 대 한 광범위 한 설명은 [개요](../Overview.md)를 참조 하세요.

### <a name="verify"></a>확인

[확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 작업에서는 DetectedFace 또는 persistedface의 얼굴 id와 다른 얼굴 Id 또는 person 개체를 사용 하 여 동일한 사람에 게 속하는지 여부를 확인 합니다. Person 개체를 전달 하는 경우 성능 향상을 위해 해당 사용자가 속한 PersonGroup를 선택적으로 전달할 수 있습니다.

### <a name="find-similar"></a>Find Similar(유사 얼굴 찾기)

[유사 찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 작업은 DetectedFace 또는 persistedface에서 얼굴 id를 사용 하 고 FaceList 또는 다른 얼굴 id의 배열을 사용 합니다. FaceList를 사용 하면 지정 된 면과 비슷한 더 작은 얼굴 FaceList을 반환 합니다. 얼굴 Id 배열을 사용 하면 비슷한 배열을 반환 합니다.

### <a name="group"></a>그룹

[Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 작업은 DetectedFace 또는 persistedface에서 다양 한 얼굴 id의 배열을 사용 하 고 여러 개의 작은 배열로 그룹화 된 동일한 id를 반환 합니다. 각 "groups" 배열에는 유사 하 게 표시 되는 얼굴 Id가 포함 되어 있습니다. 단일 "messyGroup" 배열은 유사성을 찾을 수 없는 얼굴 Id를 포함 합니다.

### <a name="identify"></a>Identify

[식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 작업에서는 DetectedFace 또는 Persistedface와 PersonGroup에서 하나 또는 여러 개의 얼굴 id를 사용 하 고 각 얼굴이 속할 수 있는 Person 개체의 목록을 반환 합니다. 반환 된 Person 개체는 예측 신뢰도 값을 가진 후보 개체로 래핑됩니다.

## <a name="input-data"></a>입력 데이터

다음 팁을 사용 하 여 입력 이미지가 가장 정확한 인식 결과를 제공 하는지 확인 합니다.

* 지원 되는 입력 이미지 형식은 JPEG, PNG, GIF (첫 번째 프레임), BMP입니다.
* 이미지 파일 크기는 4mb 보다 크지 않아야 합니다.
* Person 개체를 만들 때 다양 한 종류의 각도와 조명을 지 원하는 사진을 사용 합니다.
* 일부 면은 다음과 같은 기술적인 문제로 인해 인식 되지 않을 수 있습니다.
  * 매우 조명이 있는 이미지 (예: 심각한 backlighting).
  * 하나 또는 두 개의 눈동자를 차단 하는 장애물입니다.
  * 머리카락 유형 또는 얼굴 머리의 차이입니다.
  * 나이로 인해 얼굴 모양의 변경 내용입니다.
  * 극단적인 얼굴 식.

## <a name="next-steps"></a>다음 단계

얼굴 인식 개념에 대해 잘 알고 있으므로 학습 된 PersonGroup에 대 한 얼굴을 식별 하는 스크립트를 작성 하세요.

* [얼굴 클라이언트 라이브러리 빠른 시작](../Quickstarts/client-libraries.md)