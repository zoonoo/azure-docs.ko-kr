---
title: 검색 모델을 지정 하는 방법-Face API
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Face API 응용 프로그램에서 사용할 얼굴 감지 모델을 선택 하는 방법을 보여 줍니다.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 4306a918d56240bfe038100124b3c2b94964cebc
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306678"
---
# <a name="specify-a-face-detection-model"></a>얼굴 감지 모델 지정

이 가이드에서는 Azure Face API에 대 한 얼굴 검색 모델을 지정 하는 방법을 보여 줍니다.

Face API는 기계 학습 모델을 사용하여 이미지의 사람 얼굴에 작업을 수행합니다. 고객 의견 및 연구의 발전에 따라 모델의 정확도를 계속 개선할 것이며 모델 업데이트로 이러한 개선사항을 전달할 것입니다. 개발자는 사용할 얼굴 검색 모델의 버전을 지정 하는 옵션을 사용할 수 있습니다. 사용 사례에 가장 잘 맞는 모델을 선택할 수 있습니다.

특정 얼굴 작업에서 얼굴 검색 모델을 지정 하는 방법에 대해 자세히 알아보세요. Face API은 얼굴의 이미지를 다른 형태의 데이터로 변환할 때마다 얼굴 감지를 사용 합니다.

최신 모델을 사용 해야 하는지 확실 하지 않은 경우 [다른 모델 평가](#evaluate-different-models) 섹션으로 건너뛰어 새 모델을 평가 하 고 현재 데이터 집합을 사용 하 여 결과를 비교 합니다.

## <a name="prerequisites"></a>필수 구성 요소

AI 얼굴 감지의 개념에 대해 잘 알고 있어야 합니다. 그렇지 않은 경우 얼굴 검색 개념 가이드 또는 방법 가이드를 참조 하세요.

* [얼굴 검색 개념](../concepts/face-detection.md)
* [이미지에서 얼굴을 감지 하는 방법](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>지정된 모델을 사용하여 얼굴 감지

얼굴 검색은 인간 얼굴의 경계 상자 위치를 찾고 해당 시각적 랜드마크 식별 합니다. 얼굴 기능을 추출 하 고 나중에 [인식](../concepts/face-recognition.md) 작업에 사용 하기 위해 저장 합니다.

[얼굴 검색] API를 사용 하는 경우 `detectionModel` 매개 변수를 사용 하 여 모델 버전을 할당할 수 있습니다. 사용 가능한 값은 다음과 같습니다.

* `detection_01`
* `detection_02`

[얼굴 검색] REST API에 대 한 요청 URL은 다음과 같습니다.

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

클라이언트 라이브러리를 사용 하는 경우 적절 한 문자열을 전달 하 여 `detectionModel` 에 대 한 값을 할당할 수 있습니다. 할당 되지 않은 상태로 두면 API는 기본 모델 버전 (`detection_01`)을 사용 합니다. .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>지정 된 모델의 사용자에 게 얼굴 추가

Face API는 이미지에서 얼굴 데이터를 추출 하 고 [PersonGroup face (Add face)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API를 통해 **person** 개체와 연결할 수 있습니다. 이 API 호출에서는 [얼굴 검색]와 동일한 방법으로 검색 모델을 지정할 수 있습니다.

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

이 코드는 ID `mypersongroupid` 를 사용 하 여 PersonGroup를 만들고 여기에 **사람** 을 추가 합니다. 그런 다음 `detection_02` 모델을 사용 하 여이 **사용자** 에 게 얼굴을 추가 합니다. *DetectionModel* 매개 변수를 지정 하지 않으면 API는 기본 모델인를 `detection_01`사용 합니다.

> [!NOTE]
> **Person** 개체의 모든 면에 대해 동일한 검색 모델을 사용할 필요는 없으며, **개인** 개체와 비교할 새 얼굴을 검색할 때 동일한 검색 모델을 사용할 필요가 없습니다 (예: [얼굴 식별] API).

## <a name="add-face-to-facelist-with-specified-model"></a>지정 된 모델을 사용 하 여 FaceList에 얼굴 추가

또한 기존 **FaceList** 개체에 얼굴을 추가할 때 검색 모델을 지정할 수 있습니다. .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

이 코드는 `My face collection` 라는 **FaceList** 를 만들고 `detection_02` 모델을 사용 하 여 얼굴을 추가 합니다. *DetectionModel* 매개 변수를 지정 하지 않으면 API는 기본 모델인를 `detection_01`사용 합니다.

> [!NOTE]
> **FaceList** 개체의 모든 면에 대해 동일한 검색 모델을 사용할 필요가 없으며 **FaceList** 개체와 비교할 새 얼굴을 검색할 때 동일한 검색 모델을 사용할 필요가 없습니다.

## <a name="evaluate-different-models"></a>다른 모델 평가

여러 가지 표면 검색 모델은 여러 작업에 최적화 되어 있습니다. 차이점에 대 한 개요는 다음 표를 참조 하세요.

|**detection_01**  |**detection_02**  |
|---------|---------|
|모든 얼굴 검색 작업에 대 한 기본 선택입니다. | 2019 년 5 월에 출시 되었으며 선택적으로 모든 얼굴 검색 작업에서 사용할 수 있습니다.
|작은, 사이드 보기 또는 흐린 면에서 최적화 되지 않았습니다.  | 작은, 사이드 보기 및 흐린 면에서 정확도가 향상 되었습니다. |
|검색 호출에 지정 된 경우 face 특성 (head 포즈, age, emotion 등)을 반환 합니다. |  는 face 특성을 반환 하지 않습니다.     |
|검색 호출에 지정 된 경우 face 랜드마크을 반환 합니다.   | 는 face 랜드마크을 반환 하지 않습니다.  |

`detection_01` 및`detection_02` 모델의 성능을 비교 하는 가장 좋은 방법은 샘플 데이터 집합에서 사용 하는 것입니다. 각 검색 모델을 사용 하 여 다양 한 이미지, 특히 표시 하기 어려운 여러 얼굴 또는 얼굴의 이미지에 대해 [얼굴 검색] API를 호출 하는 것이 좋습니다. 각 모델에서 반환 하는 얼굴 수에 주의 해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다른 얼굴 Api와 함께 사용할 검색 모델을 지정 하는 방법을 배웠습니다. 다음으로, 얼굴 감지를 사용하는 것을 시작하는 빠른 시작을 수행합니다.

* [Face .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)

[얼굴 검색]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[얼굴 식별]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
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
