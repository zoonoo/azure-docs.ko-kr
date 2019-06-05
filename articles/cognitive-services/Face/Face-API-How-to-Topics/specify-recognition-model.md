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

[Face-Detect]API를 사용하는 경우, `recognitionModel` 매개 변수를 사용하여 모델 버전을 할당합니다. 사용 가능한 값은 다음과 같습니다. 사용 가능한 값은 다음과 같습니다.

* `recognition_01`
* `recognition_02`

선택적으로, _recognitionModel_이 응답에서 반환되어야 할지 여부를 나타내는 _returnRecognitionModel_ 매개 변수(기본 **false**)를 지정할 수 있습니다 따라서 [Face-Detect] REST API에 대한 요청 URL은 다음과 같이 표시됩니다.

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

클라이언트 라이브러리를 사용하는 경우, 버전을 나타내는 문자열을 전달하여 `recognitionModel` 값을 할당할 수 있습니다.
만약 할당하지 않은 경우, 기본 모델 버전(_recognition_01_)이 사용됩니다. .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>지정된 모델을 사용하여 얼굴 식별

Face API는 이미지에서 얼굴 데이터를 추출하고 **Person** 개체(예를 들어, [Add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 호출을 통해)와 연결하고, 여러 **Person** 개체는 **PersonGroup**에 함께 저장할 수 있습니다. 그런 다음 새 얼굴을 **PersonGroup**([Face-Identify] 호출을 사용하여)과 비교할 수 있으며, 해당 그룹 내에서 일치하는 사람을 식별할 수 있습니다.

**PersonGroup**은 모든 **Person**을 위해 하나의 고유 인식 모델을 가져야 하며,  그룹을 만들 때([PersonGroup - Create] 나 [LargePersonGroup - Create]) `recognitionModel` 매개 변수를 사용하여 지정할 수 있습니다. 이 매개 변수를 지정하지 않으면 원래의 `recognition_01` 모델이 사용됩니다. 그룹은 항상 생성된 인식 모델을 사용하며 추가된 새 얼굴은 이 모델과 연관됩니다. 그룹을 만든 후에는 변경할 수 없습니다. **PersonGroup**이 구성된 모델을 보려면, [PersonGroup - Get] API를 사용하여 _returnRecognitionModel_ 매개 변수를 **true**로 설정합니다.

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

이 코드에서, `mypersongroupid` ID를 가진 **PersonGroup** 을 생성하고, 얼굴 특징을 추출하기 위해  _recognition_02_ 모델을 사용하도록 설정합니다.

따라서, 얼굴을 감지할 때 **PersonGroup**([Face-Detect] API를 통해)과 비교하기 위해  사용할 모델을 지정할 필요가 있습니다. 모델은 항상 **PersonGroup**의 구성에 일관되어야 합니다. 그렇지 않으면, 호환되지 않는 모델로 인해 작업이 실패하게 됩니다.

[Face-Identify] API에서는 변경 사항이 없습니다. 감지에서만 모델 버전을 지정하면 됩니다.

## <a name="find-similar-faces-with-specified-model"></a>지정된 모델을 사용하여 유사한 얼굴 찾기

또한 유사성 검색에 대해 인식 모델을 지정할 수 있습니다. [FaceList - Create] 또는 [LargeFaceList-Create] API를 사용하여 얼굴 목록을 만들 때 `recognitionModel`을 사용하여 모델 버전을 지정할 수 있습니다. 이 매개 변수를 지정하지 않으면 원래의 `recognition_01` 모델이 사용됩니다. 얼굴 목록은 항상 생성된 인식 모델을 사용합니다. 새 얼굴이 추가될 때 모델에 연결됩니다. 만든 후에는 변경할 수 없습니다. 얼굴 목록이 구성된 모델을 보려면 [FaceList - Get] API를 사용하여 _returnRecognitionModel_ 매개 변수를 **true** 로 설정합니다.

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

이 코드에서, `My face collection`이라는 얼굴 목록을 생성하며, 특징 추출을 위해 _recognition_02_ 모델을 사용합니다. 새 감지된 얼굴에 유사한 얼굴을 찾기 위해 얼굴 목록을 검색할 때 해당 얼굴은_recognition_02_ 모델을 사용하여 감지([Face-Detect])됩니다. 이전 섹션과 같이 모델 일관성을 유지해야 합니다. 이전 섹션과 모델 일관성을 유지 해야 합니다.

[Face-Find Similar] API에서는 변경 사항이 없습니다. 감지에서 모델 버전만 지정하면 됩니다.

## <a name="verify-faces-with-specified-model"></a>지정된 모델을 사용하여 얼굴 확인

[Face-Verify] API는 두 얼굴이 같은 사람에 속하는지 여부를 확인합니다. 인식 모델과 관련하여 Verify API에서 변경사항은 없지만 동일한 모델을 사용하여 감지된 얼굴만 비교할 수 있습니다. 따라서 두 얼굴은 모두 `recognition_01` 또는 `recognition_02` 모델을 사용하여 감지되어야 합니다.

## <a name="evaluate-different-models"></a>여러 모델 평가

데이터에서 _recognition_01_와 _recognition_02_ 모델의 성능을 비교하려는 경우, 다음과 같이 비교할 수 있습니다.

1. _recognition_01_과 _recognition_02_를 사용하여 각각 **PersonGroup**을 생성합니다.
1. 이미지 데이터를 사용하여 얼굴을 감지하고 두 **PersonGroup**의 **Person**에 얼굴을 등록하고, [PersonGroup - Train] API를 사용하여 학습 프로세스를 시작합니다.
1. [Face-Identify]를 사용하여 두 **PersonGroup**을 테스트하고 결과를 비교합니다.

일반적으로 신뢰도 임계값(얼굴을 식별하는데 모델이 얼마나 신뢰성이 있어야 하는지 결정하는 0과 1 사이의 값)을 지정하면 다른 모델에 대해서는 다른 임계값을 사용해야 할 수 있습니다. 한 모델의 임계값은 다른 모델과 공유되지 않으며 반드시 동일한 결과가 도출되지는 않습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다양한 Face service API를 사용하기 위해 인식 모델을 지정하는 방법을 알아보았습니다. 다음으로, 얼굴 감지를 사용하는 것을 시작하는 빠른 시작을 수행합니다.

* [이미지에서 얼굴 감지](../quickstarts/csharp-detect-sdk.md)

[Face-Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face-Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face-Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face-Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
