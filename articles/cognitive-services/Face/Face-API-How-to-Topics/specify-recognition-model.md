---
title: Face API-인식 모델을 지정 하는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Face API 응용 프로그램을 사용 하는 인식 모델을 선택 하는 방법을 보여 줍니다.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: 88b0ac853c64e1e32a2d1c429bdf8655158f030d
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411463"
---
# <a name="specify-a-face-recognition-model"></a>얼굴 인식 모델 지정

이 가이드에서는 Azure의 Face API를 사용하여 얼굴 감지, 식별 및 유사 얼굴 찾기에 대한 얼굴 인식 모델을 지정하는 방법을 보여줍니다.

Face API는 기계 학습 모델을 사용하여 이미지의 사람 얼굴에 작업을 수행합니다. 고객 의견 및 연구의 발전에 따라 모델의 정확도를 계속 개선할 것이며 모델 업데이트로 이러한 개선사항을 전달할 것입니다. 개발자는 사용할 얼굴 인식 모델의 버전을 지정하는 옵션을 가지고 있으므로 해당 사용 사례에 가장 적합한 모델을 선택할 수 있습니다.

새 사용자인 경우 최신 모델을 사용하는 것이 좋습니다. 모델 충돌을 방지하면서 다양한 얼굴 작업에서 그것을 지정하는 방법을 알아봅니다. 고급 사용자이면서 최신 모델로의 전환에 대해 확실하지 않으면, 새 모델을 평가하고 현재 데이터 집합을 사용하여 결과를 비교하는 [다른 모델 평가](#evaluate-different-models) 섹션으로 이동합니다.

## <a name="prerequisites"></a>필수 조건

AI 얼굴 감지 및 식별의 개념을 잘 알고 있어야 합니다. 그렇치 않은 경우 먼저 다음의 방법 가이드를 참조합니다.

* [이미지에서 얼굴을 감지 하는 방법](HowtoDetectFacesinImage.md)
* [이미지에서 얼굴을 식별 하는 방법](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>지정된 모델을 사용하여 얼굴 감지

얼굴 감지는 얼굴의 시각적 랜드마크를 식별하고 해당 경계 상자 위치를 찾습니다. 또한 얼굴의 특징을 추출하고 식별에 사용하기 위해 저장합니다. 이 모든 정보는 한 얼굴을 나타냅니다.

인식 모델은 얼굴 특징을 추출할 때 사용되며, 감지 작업을 수행할때 모델 버전을 지정할 수 있습니다.

사용 하는 경우는 [직면-검색] API 모델 버전을 할당 합니다 `recognitionModel` 매개 변수입니다. 사용 가능한 값은 다음과 같습니다.

* `recognition_01`
* `recognition_02`

선택적으로 지정할 수 있습니다는 _returnRecognitionModel_ 매개 변수 (기본 **false**)를 나타내는 여부를 _recognitionModel_ 응답에 반환 되어야 합니다. 따라서 요청 URL에 대 한를 [직면-검색] REST API는 다음과 같이 표시 됩니다.

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

클라이언트 라이브러리를 사용하는 경우, 버전을 나타내는 문자열을 전달하여 `recognitionModel` 값을 할당할 수 있습니다.
만약 할당하지 않은 경우, 기본 모델 버전(_recognition_01_)이 사용됩니다. .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>지정된 모델을 사용하여 얼굴 식별

Face API는 이미지에서 얼굴 데이터를 추출 하 고 사용 하 여를 연결할 수는 **Person** 개체 (통해를 [얼굴을 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 예를 들어 API 호출), 및 다중 **Person** 개체 일 수 있습니다 에 함께 저장 된 **PersonGroup**합니다. 그런 다음 새 얼굴을 비교할 수는 **PersonGroup** (사용 하 여는 [직면-식별] 호출), 해당 그룹 내에서 일치 하는 사람을 식별할 수 있습니다.

**PersonGroup** 있어야 하나의 고유 인식 모델의 모든 합니다 **Person**가 지정할 수 있습니다 사용 하 여는 `recognitionModel` 그룹을 만들 때 매개 변수 ([PersonGroup - Create] 나 [LargePersonGroup - Create]). 원래가 매개이 변수를 지정 하지 않으면 경우 `recognition_01` 모델을 사용 합니다. 그룹 인식 모델을 사용 하 여 생성 된 항상 사용 하 고 적용 합니다. 추가 될 때 새 면이이 모델에 연결 된 됩니다. 이 그룹을 만든 후 변경할 수 없습니다. 어떤 모델을 봅니다를 **PersonGroup** 구성를 사용 하 여는 [PersonGroup - Get] 사용 하 여 API를 _returnRecognitionModel_ 으로 매개 변수 설정 **true**.

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

이 코드에서, `mypersongroupid` ID를 가진 **PersonGroup** 을 생성하고, 얼굴 특징을 추출하기 위해  _recognition_02_ 모델을 사용하도록 설정합니다.

얼굴을 감지 하는 경우이 비교를 사용 하는 모델을 지정 해야 하는 마찬가지로 **PersonGroup** (통해 합니다 [직면-검색] API). 사용 모델은 항상와 일관 되어야 합니다 **PersonGroup**의 구성을 호환 되지 않는 모델 인해 작업이 실패할 것이 고, 그렇지 합니다.

에 변경 사항이 없는 합니다 [직면-식별] API; 검색에서 모델 버전을 지정 해야 합니다.

## <a name="find-similar-faces-with-specified-model"></a>지정된 모델을 사용하여 유사한 얼굴 찾기

또한 유사성 검색에 대 한 인식 모델을 지정할 수 있습니다. 모델 버전을 지정할 수 있습니다 `recognitionModel` 얼굴 목록을 만들면 [FaceList - Create] API 또는 [LargeFaceList-Create]합니다. 원래가 매개이 변수를 지정 하지 않으면 경우 `recognition_01` 모델을 사용 합니다. 얼굴 목록을 인식 모델을 사용 하 여 생성 된 항상 사용 하 고 적용 합니다. 추가 될 때 새 면이이 모델에 연결 된 됩니다. 이 만든 후 변경할 수 없습니다. 얼굴 목록을 사용 하 여 구성 된 어떤 모델을 보려면 사용 하 여는 [FaceList - Get] 사용 하 여 API를 _returnRecognitionModel_ 으로 설정 된 매개 변수 **true**합니다.

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

이 코드 이라는 얼굴 목록을 만듭니다 `My face collection`를 사용 하 여 합니다 _recognition_02_ 기능 추출에 대 한 모델입니다. 새 검색 된 얼굴에 유사한 얼굴에 대 한이 얼굴 목록을 검색할 때 해당 얼굴 해야 검색 되었습니다 ([직면-검색])를 사용 하는 _recognition_02_ 모델입니다. 이전 섹션과 모델 일관성을 유지 해야 합니다.

에 변경 사항이 없는 [얼굴-비슷한 찾을] API로만 검색에서 모델 버전을 지정 합니다.

## <a name="verify-faces-with-specified-model"></a>지정된 모델을 사용하여 얼굴 확인

합니다 [직면-확인] API 여부를 두 얼굴이 같은 사람의 확인 합니다. 인식 모델의 경우와 관련 하 여 확인 API에서 변하지 않습니다 하지만 동일한 모델을 사용 하 여 검색 된 얼굴 비교할 수 있습니다. 따라서 두 얼굴 둘 다 필요를 사용 하 여 검색할 `recognition_01` 또는 `recognition_02`합니다.

## <a name="evaluate-different-models"></a>여러 모델 평가

데이터에서 _recognition_01_와 _recognition_02_ 모델의 성능을 비교하려는 경우, 다음과 같이 비교할 수 있습니다.

1. _recognition_01_과 _recognition_02_를 사용하여 각각 **PersonGroup**을 생성합니다.
1. 이미지 데이터를 사용하여 얼굴을 감지하고 두 **PersonGroup**의 **Person**에 얼굴을 등록하고, [PersonGroup - 학습] API를 사용하여 학습 프로세스를 시작합니다.
1. [직면-식별]를 사용하여 두 **PersonGroup**을 테스트하고 결과를 비교합니다.

일반적으로 신뢰도 임계값(얼굴을 식별하는데 모델이 얼마나 신뢰성이 있어야 하는지 결정하는 0과 1 사이의 값)을 지정하면 다른 모델에 대해서는 다른 임계값을 사용해야 할 수 있습니다. 한 모델의 임계값은 다른 모델과 공유되지 않으며 반드시 동일한 결과가 도출되지는 않습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다양한 Face service API를 사용하기 위해 인식 모델을 지정하는 방법을 알아보았습니다. 다음으로, 얼굴 감지를 사용하는 것을 시작하는 빠른 시작을 수행합니다.

* [이미지에서 얼굴 감지](../quickstarts/csharp-detect-sdk.md)

[직면-검색]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[얼굴-비슷한 찾을]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[직면-식별]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[직면-확인]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - 학습]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
