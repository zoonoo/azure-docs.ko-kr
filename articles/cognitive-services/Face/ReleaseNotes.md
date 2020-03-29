---
title: 릴리스 정보 - 얼굴 서비스
titleSuffix: Azure Cognitive Services
description: Face 서비스에 대한 릴리스 에는 다양한 버전에 대한 릴리스 변경 내역이 포함되어 있습니다.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165863"
---
# <a name="face-release-notes"></a>얼굴 릴리즈 노트

이 문서는 Face 서비스 버전 1.0과 관련이 있습니다.

### <a name="release-changes-in-june-2019"></a>2019년 6월에 릴리스 변경 사항

* 작은 측면 보기, 가려진 얼굴 및 흐릿한 면에서 향상된 정확도로 새로운 얼굴 감지 모델을 추가했습니다. 얼굴을 통해 사용 [- 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), 얼굴 목록 - [얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), 사람 그룹 사람 - `detectionModel` [얼굴](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 추가 및 큰 사람 그룹 사람 - 매개 변수에 새 얼굴 감지 모델 이름을 `detection_02` 지정하여 [얼굴을 추가합니다.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) 자세한 내용은 [검색 모델을 지정하는 방법에 대한](Face-API-How-to-Topics/specify-detection-model.md)자세한 내용입니다.

### <a name="release-changes-in-april-2019"></a>2019년 4월에 릴리스 변경 사항

* 및 `headPose` 속성의 `age` 전반적인 정확도가 향상되었습니다. 특성도 `headPose` 지금 활성화된 `pitch` 값으로 업데이트됩니다. [Face - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 매개 변수의 `returnFaceAttributes` 매개 변수에 지정하여 이러한 특성을 사용합니다. 

* [얼굴의](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)향상 된 속도 - 감지 , [얼굴 목록 - 얼굴 추가,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)큰 얼굴 목록 - 얼굴 [추가,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3) [사람 그룹 사람 - 얼굴](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 과 큰 사람 그룹 사람 [추가 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>2019년 3월에 릴리스 변경 사항

* 향상된 정확도로 새로운 얼굴 인식 모델을 추가했습니다. 얼굴을 통해 사용 [- 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), 얼굴 목록 [- 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), 사람 그룹 - `recognitionModel` [만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 및 [LargePersonGroup -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) 매개 변수에 새 얼굴 인식 모델 이름을 `recognition_02` 지정하여 만듭니다. 자세한 내용은 [인식 모델을 지정하는 방법에 있습니다.](Face-API-How-to-Topics/specify-recognition-model.md)

### <a name="release-changes-in-january-2019"></a>2019년 1월 릴리스 변경 내용

* 구독 간에 데이터 마이그레이션을 지원하기 [Snapshot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get)위해 스냅숏 기능을 추가했습니다. [얼굴 데이터를 다른 Face 구독으로 마이그레이션하는 방법에](Face-API-How-to-Topics/how-to-migrate-face-data.md)대한 자세한 내용입니다.

### <a name="release-changes-in-october-2018"></a>2018년 10월 릴리스 변경 내용

* [PersonGroup - 학습 상태 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - 학습 상태 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) 및 [LargeFaceList - 학습 상태 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)에서 `status`, `createdDateTime`, `lastActionDateTime` 및 `lastSuccessfulTrainingDateTime`의 자세한 설명이 추가되었습니다.

### <a name="release-changes-in-may-2018"></a>2018년 5월 릴리스 변경 내용

* `age`, `glasses`, `facialHair`, `hair`, `makeup` 특성과 함께 `gender` 특성이 크게 향상되었습니다. [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 매개 변수를 통해 사용합니다. 

* [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 및 [LargePersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)에서 입력 이미지 파일 크기 제한이 4MB에서 6MB로 증가했습니다.

### <a name="release-changes-in-march-2018"></a>2018년 3월 릴리스 변경 내용

* 백만 규모 컨테이너인 [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) 및 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)이 추가되었습니다. [대규모 기능을 사용하는 방법](Face-API-How-to-Topics/how-to-use-large-scale.md)의 자세한 내용입니다.

* [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` 매개 변수가 [1, 5]에서 [1, 100]으로 증가하고 기본값이 10으로 증가했습니다.

### <a name="release-changes-in-may-2017"></a>2017년 5월 릴리스 변경 내용

* [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 매개 변수에서 `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` 및 `noise` 특성이 추가되었습니다.

* PersonGroup 및 [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에서만 10K의 사람이 지원되었습니다.

* `start` 및 `top`이라는 선택적 매개 변수가 있는 [PersonGroup 사람 - 목록](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241)에 페이지 매김이 지원되었습니다.

* 다른 FaceLists 및 PersonGroup의 다른 사람에 대해 얼굴이 추가/삭제되는 경우 동시성이 지원되었습니다.

### <a name="release-changes-in-march-2017"></a>2017년 3월 릴리스 변경 내용
* [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 매개 변수에서 `emotion` 특성이 추가되었습니다.

* 얼굴이 [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) 및 [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)의 `targetFace`인 [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)에서 반환된 사각형을 사용하여 다시 검색될 수 없도록 수정했습니다.

* 36x36과 4096x4096 픽셀 간이 엄격하게 되도로 감지할 수 있는 글꼴 크기가 수정되었습니다.

### <a name="release-changes-in-november-2016"></a>2016년 11월 릴리스 변경 내용
* 식별이나 유사성 검사를 위해 [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 또는 [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)를 사용할 경우 추가로 유지될 얼굴을 저장하는 Face Storage 표준을 추가했습니다. 저장된 이미지는 1,000개 얼굴 기준으로 $0.5로 청구되며, 이 요금은 일별로 계산됩니다. 무료 계층 구독은 총 1,000명의 사람으로 계속 제한됩니다.

### <a name="release-changes-in-october-2016"></a>2016년 10월 릴리스 변경 내용
* [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) 및 [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)에서 targetFace의 얼굴이 둘 이상인 오류 메시지를 '이미지에 둘 이상의 얼굴들이 있습니다.'에서 '이미지에 둘 이상의 얼굴이 있습니다.'로 변경했습니다.

### <a name="release-changes-in-july-2016"></a>2016년 7월 릴리스 변경 내용
* [얼굴 - 확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)에서 사람 개체 인증에 얼굴이 지원되었습니다.

* 두 작업 모드의 선택 영역을 사용하도록 설정하는 선택적 `mode` 매개 변수가 추가되었습니다. [얼굴 - 유사 항목 찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)에서 `matchPerson` 및 `matchFace`이며 기본값은 `matchPerson`입니다.

* [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에서 한 얼굴이 사람 개체에 속하는지 여부의 임계값을 설정하도록 사용자에 대한 선택 사항 `confidenceThreshold` 매개 변수를 추가했습니다.

* 사용자가 시작점 및 목록에 대한 총 PersonGroups 수를 지정할 수 있도록 [PersonGroup - 목록](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248)에서 선택 사항 `start` 및 `top` 매개 변수를 추가했습니다.

### <a name="v10-changes-from-v0"></a>V0에서 변경된 V1.0 내용
* 서비스 루트 엔드포인트를 ```https://westus.api.cognitive.microsoft.com/face/v0/```에서 ```https://westus.api.cognitive.microsoft.com/face/v1.0/```으로 업데이트했습니다. [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [얼굴 - 유사 항목 찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 및 [얼굴 - 그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)에 적용된 변경 내용입니다.

* 36x36픽셀로 감지 가능한 최소 얼굴 크기를 업데이트했습니다. 36x36픽셀보다 작은 얼굴은 검색되지 않습니다.

* Face V0에서 PersonGroup 및 사람 데이터가 사용되지 않았습니다. Face V1.0 서비스를 사용하여 해당 데이터에 액세스할 수 없습니다.

* 2016년 6월 30 일에 Face API의 V0 엔드포인트가 사용되지 않았습니다.
