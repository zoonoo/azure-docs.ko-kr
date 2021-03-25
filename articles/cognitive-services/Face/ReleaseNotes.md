---
title: Face 서비스의 새로운 기능이란?
titleSuffix: Azure Cognitive Services
description: Face 서비스의 릴리스 정보에는 다양 한 버전에 대 한 릴리스 변경 내역이 포함 됩니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: pafarley
ms.openlocfilehash: f280e1a70ab595a87789c7514d8cce4fccbe8e84
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046898"
---
# <a name="whats-new-in-face-service"></a>Face 서비스의 새로운 기능이란?

Azure Face 서비스는 지속적으로 업데이트 됩니다. 이 문서를 사용 하면 향상 된 기능, 수정 사항 및 설명서 업데이트를 최신 상태로 유지할 수 있습니다.

## <a name="february-2021"></a>2021년 2월

* 새 Face API 검색 모델: 새 검색 03 모델은 현재 사용할 수 있는 가장 정확한 검색 모델입니다. 새 고객 인 경우이 모델을 사용 하는 것이 좋습니다. 검색 03은 이미지 (64x64 픽셀)에서 발견 되는 작은 얼굴의 리콜 및 정밀도를 모두 향상 시킵니다. 추가 개선 사항으로는 거짓 긍정의 전반적인 감소 및 회전 된 얼굴 방향에 대 한 향상 된 검색이 포함 됩니다. 검색 03과 새 인식 04을 결합 하면 인식 정확도도 향상 됩니다. 자세한 내용은 [얼굴 검색 모델 지정](./face-api-how-to-topics/specify-detection-model.md) 을 참조 하세요.
* 얼굴 마스크 특성: 얼굴 마스크 특성은 `"noseAndMouthCovered"` 얼굴 마스크가 의도 한 대로 마모 되었는지 여부를 검색 하는 추가 특성과 함께 최신 검색 03 모델과 함께 사용할 수 있습니다. 최신 마스크 검색 기능을 사용 하려면 사용자가 API 요청에서 검색 모델을 지정 해야 합니다. _detectionModel_ 매개 변수를 사용 하 여 모델 버전을에 할당 `detection_03` 합니다. 자세한 내용은 [얼굴 검색 모델 지정](./face-api-how-to-topics/specify-detection-model.md) 을 참조 하세요.
* 새 Face API 인식 모델: 새 인식 04 모델은 현재 사용할 수 있는 가장 정확한 인식 모델입니다. 새 고객 인 경우 확인 및 식별을 위해이 모델을 사용 하는 것이 좋습니다. 얼굴 커버 (surgical 마스크, N95 마스크, 천을 마스크)를 포함 하는 등록 된 사용자에 대 한 인식 향상을 포함 하 여 인식 03의 정확도를 향상 시킵니다. 이제 고객은 등록 된 사용자가 최신 검색 03 모델을 사용 하 여 얼굴 커버를 작성 하 고 있는지 여부를 검색 하는 안전 하 고 원활한 사용자 환경을 구축 하 고, 최신 인식 04 모델을 사용 하는 사용자를 인식할 수 있습니다. 자세한 내용은 [얼굴 인식 모델 지정](./face-api-how-to-topics/specify-recognition-model.md) 을 참조 하세요.


## <a name="january-2021"></a>2021년 1월
* Face API를 사용 하는 경우 대기 시간 완화: 얼굴 팀은 서비스 및 가능한 완화 전략을 사용할 때 잠재적 대기 시간 원인을 자세히 설명 하는 새 문서를 게시 했습니다. [Face 서비스를 사용할 때 대기 시간 감소](./face-api-how-to-topics/how-to-mitigate-latency.md)를 참조 하세요.

## <a name="december-2020"></a>2020년 12월
* 얼굴 ID 저장소에 대 한 고객 구성: 얼굴 서비스는 고객 이미지를 저장 하지 않지만 압축 된 얼굴 기능은 서버에 저장 됩니다. 얼굴 ID는 얼굴 기능의 식별자 이며 [얼굴](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)확인, [얼굴 확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)및 [얼굴 찾기와 유사](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)하 게 사용 됩니다. 저장 된 면 기능이 만료 되어 원래 검색 통화 후 24 시간 후에 삭제 됩니다. 이제 고객은 이러한 얼굴 Id가 캐시 되는 기간을 결정할 수 있습니다. 최대 값은 24 시간 이지만 이제는 최소 값 60 초를 설정할 수 있습니다. 캐시 되는 Face Id의 새 시간 범위는 60 초에서 24 시간 사이의 값입니다. 자세한 내용은 [얼굴 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 참조 ( *faceIdTimeToLive* 매개 변수)에서 찾을 수 있습니다.

## <a name="november-2020"></a>2020년 11월
* 중요 한 동의를 설정 하 고 고품질 등록을 통해 정확도가 뛰어난 얼굴 인식 시스템을 만들기 위한 모범 사례를 보여 주는 샘플 얼굴 등록 앱을 게시 했습니다. 오픈 소스 샘플은 개발자가 배포 하거나 사용자 지정할 수 있도록 준비 된, [등록 앱 빌드](build-enrollment-app.md) 가이드 및 [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample)에서 찾을 수 있습니다. 

## <a name="august-2020"></a>2020년 8월
* 미사용 데이터의 고객이 관리 하는 암호화: Face 서비스는 데이터를 클라우드에 유지할 때 자동으로 암호화 합니다. 얼굴 서비스 암호화는 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 되도록 데이터를 보호 합니다. 기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. CMK (고객이 관리 하는 키) 라고 하는 고유한 키를 사용 하 여 구독을 관리 하는 새로운 옵션도 있습니다. 자세한 내용은 [고객이 관리 하는 키](./encrypt-data-at-rest.md)에서 찾을 수 있습니다.

## <a name="april-2020"></a>2020년 4월
* 새 Face API 인식 모델: 새 인식 03 모델은 현재 사용할 수 있는 가장 정확한 모델입니다. 새 고객의 경우이 모델을 사용 하는 것이 좋습니다. 인식 03은 유사성 비교와 사용자 일치 비교에 대해 향상 된 정확도를 제공 합니다. 자세한 내용은 [얼굴 인식 모델 지정](./face-api-how-to-topics/specify-recognition-model.md)에서 찾을 수 있습니다.

## <a name="june-2019"></a>2019년 6월

* 작은, 사이드 보기, 폐색 및 흐린 면에서 정확도가 향상 된 새 얼굴 감지 모델을 추가 했습니다. 매개 변수에서 새 얼굴 검색 모델 이름을 지정 하 여 [얼굴 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList 얼굴](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-추가 얼굴](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup 사람-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 추가 얼굴 및 [LargePersonGroup person-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) 추가 얼굴을 통해이를 사용 `detection_02` `detectionModel` 합니다. 자세한 내용은 [검색 모델을 지정 하는 방법](Face-API-How-to-Topics/specify-detection-model.md)을 참조 하세요.

## <a name="april-2019"></a>2019년 4월

* 및 특성의 전체 정확도가 향상 `age` `headPose` 되었습니다. `headPose`또한 특성은 now로 설정 된 값으로 업데이트 됩니다 `pitch` . 이러한 특성은 `returnFaceAttributes` [얼굴 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 매개 변수에서 지정 하 여 사용 `returnFaceAttributes` 합니다. 

* [얼굴 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList 얼굴](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-추가 얼굴](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup 사람-얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 및 [LargePersonGroup 개인-얼굴](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)추가의 향상 된 속도입니다.

## <a name="march-2019"></a>2019년 3월

* 정확도가 향상 된 새 얼굴 인식 모델을 추가 했습니다. 매개 변수에 새 얼굴 인식 모델 이름을 지정 하 여 [FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), Create, [PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 및 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) 를 통해이를 사용 [](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `recognition_02` `recognitionModel` 합니다. [인식 모델을 지정 하는 방법](Face-API-How-to-Topics/specify-recognition-model.md)에 대 한 자세한 내용.

## <a name="january-2019"></a>2019년 1월

* 구독 간 데이터 마이그레이션을 지 원하는 스냅숏 기능이 추가 되었습니다. [스냅숏](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). [얼굴 데이터를 다른 얼굴 구독으로 마이그레이션하는 방법](Face-API-How-to-Topics/how-to-migrate-face-data.md)에 대해 자세히 설명 합니다.

## <a name="october-2018"></a>2018년 10월

* [PersonGroup - 학습 상태 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - 학습 상태 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) 및 [LargeFaceList - 학습 상태 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)에서 `status`, `createdDateTime`, `lastActionDateTime` 및 `lastSuccessfulTrainingDateTime`의 자세한 설명이 추가되었습니다.

## <a name="may-2018"></a>2018년 5월

* `age`, `glasses`, `facialHair`, `hair`, `makeup` 특성과 함께 `gender` 특성이 크게 향상되었습니다. [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 매개 변수를 통해 사용합니다. 

* [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 및 [LargePersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)에서 입력 이미지 파일 크기 제한이 4MB에서 6MB로 증가했습니다.

## <a name="march-2018"></a>2018년 3월

* 백만 규모 컨테이너인 [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) 및 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)이 추가되었습니다. [대규모 기능을 사용하는 방법](Face-API-How-to-Topics/how-to-use-large-scale.md)의 자세한 내용입니다.

* [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` 매개 변수가 [1, 5]에서 [1, 100]으로 증가하고 기본값이 10으로 증가했습니다.

## <a name="may-2017"></a>2017년 5월

* [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 매개 변수에서 `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` 및 `noise` 특성이 추가되었습니다.

* PersonGroup 및 [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에서만 10K의 사람이 지원되었습니다.

* `start` 및 `top`이라는 선택적 매개 변수가 있는 [PersonGroup 사람 - 목록](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241)에 페이지 매김이 지원되었습니다.

* 다른 FaceLists 및 PersonGroup의 다른 사람에 대해 얼굴이 추가/삭제되는 경우 동시성이 지원되었습니다.

## <a name="march-2017"></a>2017년 3월
* [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 매개 변수에서 `emotion` 특성이 추가되었습니다.

* 얼굴이 [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) 및 [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)의 `targetFace`인 [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)에서 반환된 사각형을 사용하여 다시 검색될 수 없도록 수정했습니다.

* 36x36과 4096x4096 픽셀 간이 엄격하게 되도로 감지할 수 있는 글꼴 크기가 수정되었습니다.

## <a name="november-2016"></a>2016년 11월
* 식별이나 유사성 검사를 위해 [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 또는 [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)를 사용할 경우 추가로 유지될 얼굴을 저장하는 Face Storage 표준을 추가했습니다. 저장된 이미지는 1,000개 얼굴 기준으로 $0.5로 청구되며, 이 요금은 일별로 계산됩니다. 무료 계층 구독은 총 1,000명의 사람으로 계속 제한됩니다.

## <a name="october-2016"></a>2016년 10월
* [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) 및 [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)에서 targetFace의 얼굴이 둘 이상인 오류 메시지를 '이미지에 둘 이상의 얼굴들이 있습니다.'에서 '이미지에 둘 이상의 얼굴이 있습니다.'로 변경했습니다.

## <a name="july-2016"></a>2016년 7월
* [얼굴 - 확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)에서 사람 개체 인증에 얼굴이 지원되었습니다.

* 두 작업 모드의 선택 영역을 사용하도록 설정하는 선택적 `mode` 매개 변수가 추가되었습니다. [얼굴 - 유사 항목 찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)에서 `matchPerson` 및 `matchFace`이며 기본값은 `matchPerson`입니다.

* [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에서 한 얼굴이 사람 개체에 속하는지 여부의 임계값을 설정하도록 사용자에 대한 선택 사항 `confidenceThreshold` 매개 변수를 추가했습니다.

* 사용자가 시작점 및 목록에 대한 총 PersonGroups 수를 지정할 수 있도록 [PersonGroup - 목록](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248)에서 선택 사항 `start` 및 `top` 매개 변수를 추가했습니다.

## <a name="v10-changes-from-v0"></a>V0에서 변경된 V1.0 내용

* 서비스 루트 엔드포인트를 ```https://westus.api.cognitive.microsoft.com/face/v0/```에서 ```https://westus.api.cognitive.microsoft.com/face/v1.0/```으로 업데이트했습니다. [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [얼굴 - 유사 항목 찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 및 [얼굴 - 그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)에 적용된 변경 내용입니다.

* 36x36픽셀로 감지 가능한 최소 얼굴 크기를 업데이트했습니다. 36x36픽셀보다 작은 얼굴은 검색되지 않습니다.

* Face V0에서 PersonGroup 및 사람 데이터가 사용되지 않았습니다. Face V1.0 서비스를 사용하여 해당 데이터에 액세스할 수 없습니다.

* 2016년 6월 30 일에 Face API의 V0 엔드포인트가 사용되지 않았습니다.