---
title: 감지 모델을 지정하는 방법 - 얼굴
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Face 응용 프로그램에서 사용할 얼굴 감지 모델을 선택하는 방법을 보여 줍니다.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76934569"
---
# <a name="specify-a-face-detection-model"></a>얼굴 감지 모델 지정

이 가이드에서는 Azure Face 서비스에 대한 얼굴 감지 모델을 지정하는 방법을 보여 주며, 이 가이드에서는

Face 서비스는 기계 학습 모델을 사용하여 이미지에서 사람의 얼굴에 대한 작업을 수행합니다. 우리는 고객 피드백과 연구의 진보를 기반으로 모델의 정확성을 지속적으로 개선하고, 우리는 모델 업데이트로 이러한 개선을 제공합니다. 개발자는 사용할 얼굴 감지 모델의 버전을 지정할 수 있습니다. 사용 사례에 가장 적합한 모델을 선택할 수 있습니다.

특정 얼굴 작업에서 얼굴 감지 모델을 지정하는 방법을 알아보려면 계속 읽어보십시오. Face 서비스는 얼굴 이미지를 다른 형태의 데이터로 변환할 때마다 얼굴 감지를 사용합니다.

최신 모델을 사용해야 하는지 확실하지 않은 경우 [다른 모델 평가](#evaluate-different-models) 섹션으로 건너뛰서 새 모델을 평가하고 현재 데이터 집합을 사용하여 결과를 비교합니다.

## <a name="prerequisites"></a>사전 요구 사항

AI 얼굴 감지의 개념에 대해 잘 알고 있어야 합니다. 그렇지 않은 경우 얼굴 감지 개념 가이드 또는 방법 가이드를 참조하십시오.

* [얼굴 감지 개념](../concepts/face-detection.md)
* [이미지에서 얼굴을 감지하는 방법](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>지정된 모델로 면 감지

얼굴 감지는 사람의 얼굴의 경계 상자 위치를 찾아 시각적 랜드마크를 식별합니다. 얼굴의 기능을 추출하여 [나중에 인식](../concepts/face-recognition.md) 작업에 사용할 수 있도록 저장합니다.

Face - API [를 검색하는] 경우 매개 변수를 `detectionModel` 사용하여 모델 버전을 할당할 수 있습니다. 사용 가능한 값은

* `detection_01`
* `detection_02`

얼굴에 대한 요청 URL - REST API [감지는] 다음과 같습니다.

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

클라이언트 라이브러리를 사용하는 경우 적절한 문자열을 `detectionModel` 전달하여 값을 할당할 수 있습니다. 할당되지 않은 상태로 두면 API는 기본 모델`detection_01`버전()을 사용합니다. .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하십시오.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>지정된 모델을 사용하여 사람에 얼굴 추가

Face 서비스는 이미지에서 얼굴 데이터를 추출하고 사람 그룹 사람 - 얼굴 API [추가를](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 통해 **사람** 개체와 연결할 수 있습니다. 이 API 호출에서 Face - Detect에서와 동일한 방식으로 검색 모델을 지정할 수 [있습니다.]

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하십시오.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

이 코드는 ID가 `mypersongroupid` 있는 **PersonGroup을** 만들고 해당 코드에 **사람을** 추가합니다. 그런 다음 모델을 사용하여 **Person** 이 `detection_02` 사람에게 면을 추가합니다. *detectionModel* 매개 변수를 지정하지 않으면 API는 기본 모델을 `detection_01`사용합니다.

> [!NOTE]
> **Person** 개체의 모든 얼굴에 대해 동일한 감지 모델을 사용할 필요가 없으며 새 얼굴을 감지할 때 동일한 감지 모델을 사용하여 **사람** 개체와 비교할 필요가 없습니다(예: [Face - IDENTIFY] API).

## <a name="add-face-to-facelist-with-specified-model"></a>지정된 모델로 면 리스트에 면 추가

기존 **FaceList** 오브젝트에 면을 추가할 때 감지 모델을 지정할 수도 있습니다. .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하십시오.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

이 코드는 라는 `My face collection` **FaceList를** 만들고 `detection_02` 모델과 함께 얼굴을 추가 합니다. *detectionModel* 매개 변수를 지정하지 않으면 API는 기본 모델을 `detection_01`사용합니다.

> [!NOTE]
> **FaceList** 개체의 모든 얼굴에 대해 동일한 감지 모델을 사용할 필요가 없으며 **FaceList** 개체와 비교할 새 얼굴을 감지할 때 동일한 감지 모델을 사용할 필요가 없습니다.

## <a name="evaluate-different-models"></a>다양한 모델 평가

다양한 얼굴 감지 모델은 다양한 작업에 최적화되어 있습니다. 차이점에 대한 개요는 다음 표를 참조하십시오.

|**detection_01**  |**detection_02**  |
|---------|---------|
|모든 얼굴 감지 작업에 대한 기본 선택입니다. | 2019년 5월에 출시되며 모든 얼굴 감지 작업에서 선택적으로 사용할 수 있습니다.
|작은, 측면 보기 또는 흐릿한 얼굴에 최적화되지 않았습니다.  | 작고 측면이 크고 흐릿한 얼굴의 정확도가 향상되었습니다. |
|감지 호출에 지정된 경우 얼굴 속성(머리 포즈, 나이, 감정 등)을 반환합니다. |  얼굴 속성을 반환하지 않습니다.     |
|감지 호출에 지정된 경우 얼굴 랜드마크를 반환합니다.   | 얼굴 랜드마크를 반환하지 않습니다.  |

`detection_01` 모델과 `detection_02` 모델의 성능을 비교하는 가장 좋은 방법은 샘플 데이터 집합에서 사용하는 것입니다. Face - 각 검색 모델을 사용하여 다양한 이미지, 특히 보기 어려운 많은 얼굴이나 얼굴의 이미지에서 API를 [감지하는] 것이 좋습니다. 각 모델이 반환하는 면 수에 주의하십시오.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다른 Face API와 함께 사용할 검색 모델을 지정하는 방법을 배웠습니다. 다음으로 빠른 시작을 따라 얼굴 감지를 사용하여 시작합니다.

* [얼굴 .NET SDK](../Quickstarts/csharp-sdk.md)
* [페이스 파이썬 SDK](../Quickstarts/python-sdk.md)
* [페이스 고 SDK](../Quickstarts/go-sdk.md)

[얼굴 - 감지]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[얼굴 - 식별]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
