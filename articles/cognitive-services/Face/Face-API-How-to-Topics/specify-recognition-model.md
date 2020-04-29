---
title: 인식 모델을 지정 하는 방법-얼굴
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Face 응용 프로그램에 사용할 인식 모델을 선택 하는 방법을 보여 줍니다.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "76938836"
---
# <a name="specify-a-face-recognition-model"></a>얼굴 인식 모델 지정

이 가이드에서는 Azure Face 서비스를 사용 하 여 얼굴 감지, 식별 및 유사성 검색을 위한 얼굴 인식 모델을 지정 하는 방법을 보여 줍니다.

얼굴 서비스는 기계 학습 모델을 사용 하 여 이미지에서 인간 얼굴에 대 한 작업을 수행 합니다. 사용자 의견에 따라 모델의 정확도를 지속적으로 향상 시키고 연구를 진행 하면서 이러한 향상 된 기능을 모델 업데이트로 제공 합니다. 개발자는 사용 하려는 얼굴 인식 모델의 버전을 지정 하는 옵션을 사용할 수 있습니다. 사용 사례에 가장 잘 맞는 모델을 선택할 수 있습니다.

새 사용자 인 경우 최신 모델을 사용 하는 것이 좋습니다. 모델 충돌을 방지 하면서 다른 얼굴 작업에서 지정 하는 방법에 대해 알아보세요. 고급 사용자 이며 최신 모델로 전환 해야 하는지 확실 하지 않은 경우 [다른 모델 평가](#evaluate-different-models) 섹션으로 건너뛰어 새 모델을 평가 하 고 현재 데이터 집합을 사용 하 여 결과를 비교할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

AI 얼굴 감지 및 식별의 개념에 대해 잘 알고 있어야 합니다. 그렇지 않은 경우 먼저 다음 방법 가이드를 참조 하세요.

* [이미지에서 얼굴을 감지 하는 방법](HowtoDetectFacesinImage.md)
* [이미지에서 얼굴을 식별 하는 방법](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 얼굴 감지

얼굴 검색은 인간 얼굴의 시각적 랜드마크 식별 하 고 경계 상자 위치를 찾습니다. 또한 얼굴 기능을 추출 하 여 식별에 사용할 수 있도록 저장 합니다. 이 모든 정보는 한 면의 표현을 형성 합니다.

인식 모델은 얼굴 기능이 추출 될 때 사용 되므로 검색 작업을 수행할 때 모델 버전을 지정할 수 있습니다.

[얼굴 검색] API를 사용 하는 경우 `recognitionModel` 매개 변수를 사용 하 여 모델 버전을 할당 합니다. 사용 가능한 값은

* `recognition_01`
* `recognition_02`

필요에 따라 _returnRecognitionModel_ 매개 변수 (기본값 **false**)를 지정 하 여 응답에서 _recognitionModel_ 을 반환할지 여부를 나타낼 수 있습니다. 따라서 [얼굴 검색] REST API에 대 한 요청 URL은 다음과 같이 표시 됩니다.

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

클라이언트 라이브러리를 사용 하는 경우 버전을 나타내는 문자열을 전달 하 `recognitionModel` 여에 대 한 값을 할당할 수 있습니다.
할당 되지 않은 상태로 두면 기본 모델 버전 (_recognition_01_)이 사용 됩니다. .NET 클라이언트 라이브러리에 대 한 다음 코드 예제를 참조 하세요.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 얼굴 식별

얼굴 서비스는 이미지에서 얼굴 데이터를 추출 하 고이를 **Person** 개체와 연결 (예: [face API 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 호출을 통해) 할 수 있으며, 여러 **person** 개체를 **PersonGroup**에 함께 저장할 수 있습니다. 그런 다음 **PersonGroup** 를 사용 [하 여 새] 얼굴을 비교 하 고 해당 그룹 내의 일치 하는 사용자를 식별할 수 있습니다.

**PersonGroup** 에는 모든 **사용자**에 대 한 고유한 인식 모델이 하나씩 있어야 하 고 그룹을 만들 때 매개 변수를 `recognitionModel` 사용 하 여이를 지정할 수 있습니다 ([PersonGroup-create] 또는 [LargePersonGroup-create]). 이 매개 변수를 지정 하지 않으면 원래 `recognition_01` 모델이 사용 됩니다. 그룹은 항상 생성 된 인식 모델을 사용 하 고 새 얼굴은이 모델에 추가 될 때이 모델에 연결 됩니다. 그룹을 만든 후에는 변경할 수 없습니다. **PersonGroup** 구성 된 모델을 확인 하려면 _returnRecognitionModel_ 매개 변수를 **True**로 설정 하 여 [PersonGroup-Get] API를 사용 합니다.

.NET 클라이언트 라이브러리에 대 한 다음 코드 예제를 참조 하세요.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

이 코드에서는 ID `mypersongroupid` 가 인 **PersonGroup** 을 만들고, _recognition_02_ 모델을 사용 하 여 얼굴 기능을 추출 하도록 설정 합니다.

마찬가지로 [얼굴 검색] API를 통해이 **PersonGroup** 비교할 얼굴을 검색할 때 사용할 모델을 지정 해야 합니다. 사용 하는 모델은 항상 **PersonGroup**의 구성과 일치 해야 합니다. 그렇지 않으면 호환 되지 않는 모델로 인해 작업이 실패 합니다.

[얼굴 식별] API에는 변경 내용이 없습니다. 검색 에서만 모델 버전을 지정 해야 합니다.

## <a name="find-similar-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 유사한 얼굴 찾기

유사성 검색을 위한 인식 모델을 지정할 수도 있습니다. [FaceList] API 또는 [LargeFaceList-create]를 `recognitionModel` 사용 하 여 얼굴 목록을 만들 때를 사용 하 여 모델 버전을 할당할 수 있습니다. 이 매개 변수를 지정 하지 않으면 원래 `recognition_01` 모델이 사용 됩니다. 얼굴 목록에는 항상 생성 된 인식 모델이 사용 되며 새 얼굴은이 모델에 추가 될 때이 모델에 연결 됩니다. 이를 만든 후에는 변경할 수 없습니다. 얼굴 목록이로 구성 된 모델을 확인 하려면 _returnRecognitionModel_ 매개 변수를 **true**로 설정 하 여 [FaceList] API를 사용 합니다.

.NET 클라이언트 라이브러리에 대 한 다음 코드 예제를 참조 하세요.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

이 코드는 기능 추출에 대 `My face collection`한 _recognition_02_ 모델을 사용 하 여 라는 얼굴 목록을 만듭니다. 이 얼굴 목록을 검색 하 여 새롭게 검색 된 면에서 유사한 얼굴을 검색 하는 경우에는 _recognition_02_ 모델을 사용 하 여 해당 얼굴을 감지 해야 합니다 ([얼굴 감지]). 이전 섹션과 마찬가지로 모델은 일관적 이어야 합니다.

[유사한 API 찾기] 에는 변경 내용이 없습니다. 검색 에서만 모델 버전을 지정 합니다.

## <a name="verify-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 얼굴 확인

[얼굴 확인] API는 두 얼굴이 동일한 사람에 게 속하는지 여부를 확인 합니다. 인식 모델과 관련 하 여 확인 API에는 변경 내용이 없지만 동일한 모델을 사용 하 여 검색 된 면만 비교할 수 있습니다. 따라서 두 얼굴은 모두 또는 `recognition_01` `recognition_02`를 사용 하 여 검색 해야 합니다.

## <a name="evaluate-different-models"></a>다른 모델 평가

데이터의 _recognition_01_ 및 _recognition_02_ 모델의 성능을 비교 하려는 경우 다음을 수행 해야 합니다.

1. 각각 _recognition_01_ 및 _recognition_02_ 를 사용 하 여 두 개의 **PersonGroup**s를 만듭니다.
1. 이미지 데이터를 사용 하 여 얼굴을 감지 하 고 이러한 두 **PersonGroup**s에 대 한 **사용자**에 게 등록 하 고 [PersonGroup] API를 사용 하 여 학습 프로세스를 트리거합니다.
1. **PersonGroup**s [를 사용 하 여 테스트 하 고] 결과를 비교 합니다.

일반적으로 신뢰도 임계값을 지정 하는 경우 (0에서 1 사이의 값과 얼굴을 식별 하는 모델의 확신 정도를 결정 하는 값) 모델 마다 다른 임계값을 사용 해야 할 수 있습니다. 한 모델에 대 한 임계값은 다른 모델에 대 한 임계값을 공유 하는 것이 아니며 반드시 동일한 결과를 생성 하는 것은 아닙니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다른 얼굴 서비스 Api와 함께 사용할 인식 모델을 지정 하는 방법을 배웠습니다. 그런 다음 빠른 시작을 따라 얼굴 감지를 사용 하 여 시작 합니다.

* [Face .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [얼굴 이동 SDK](../Quickstarts/go-sdk.md)

[얼굴 - 감지]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face-유사 찾기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[얼굴 - 식별]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[얼굴-확인]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - 학습]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList-만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
