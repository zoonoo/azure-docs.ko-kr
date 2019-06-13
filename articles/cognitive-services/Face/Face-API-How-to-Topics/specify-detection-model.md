---
title: Face API-감지 모델을 지정 하는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure의 Face API 응용 프로그램을 사용 하는 얼굴 감지 모델을 선택 하는 방법을 보여 줍니다.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: dde5623bf5bd579a13fa7271dfba64f9df61bad1
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576719"
---
# <a name="specify-a-face-detection-model"></a>얼굴 감지 모델을 지정 합니다.

이 가이드에서는 Azure의 Face API에 대 한 얼굴 감지 모델을 지정 하는 방법을 보여 줍니다.

Face API는 기계 학습 모델을 사용하여 이미지의 사람 얼굴에 작업을 수행합니다. 고객 의견 및 연구의 발전에 따라 모델의 정확도를 계속 개선할 것이며 모델 업데이트로 이러한 개선사항을 전달할 것입니다. 개발자가 사용할 수 있습니다; 원하는 얼굴 감지 모델의 버전을 지정 하는 옵션 해당 사용 사례에 가장 적합 한 모델을 선택할 수 있습니다.

에 읽기 특정 얼굴 작업에서 얼굴 감지 모델을 지정 하는 방법을 알아봅니다. Face API 얼굴 이미지 데이터의 다른 형식으로 변환 될 때마다 얼굴 감지를 사용 합니다.

모르는 경우 최신 모델을 사용 해야 하는지 여부를 건너뜁니다 합니다 [여러 모델 평가](#evaluate-different-models) 새 모델을 평가 하 고 현재 데이터 집합을 사용 하 여 결과 비교 하는 섹션입니다.

## <a name="prerequisites"></a>필수 조건

AI 얼굴 감지의 개념에 익숙해야 합니다. 가 아닌 경우 얼굴 감지 개념 가이드 또는 방법 가이드를 참조 하세요.

* [얼굴 검색 개념](../concepts/face-detection.md)
* [이미지에서 얼굴을 감지 하는 방법](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>지정된 모델을 사용하여 얼굴 감지

얼굴 감지 얼굴의 경계 상자 위치를 찾아서 해당 visual 랜드마크를 식별 합니다. 발생 하는 기능을 추출 하 고 나중에 사용할 수를 저장 합니다 [인식](../concepts/face-recognition.md) 작업 합니다.

사용 하는 경우는 [직면-검색] API를 사용 하 여 모델 버전을 지정할 수 있습니다는 `detectionModel` 매개 변수입니다. 사용 가능한 값은 다음과 같습니다.

* `detection_01`
* `detection_02`

요청 URL에 대 한 합니다 [직면-검색] REST API는 다음과 같이 표시 됩니다.

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]
&subscription-key=<Subscription key>`

클라이언트 라이브러리를 사용 하는 경우에 값을 할당할 수 있습니다 `detectionModel` 적절 한 문자열을 전달 하 여 합니다. 담당자를 지정 하지 않으면 API 기본 모델 버전을 사용 합니다 (`detection_01`). .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>지정 된 모델을 사용 하 여 사람의 얼굴 추가

Face API는 이미지에서 얼굴 데이터를 추출 하 고 사용 하 여를 연결할 수는 **Person** 개체를 [PersonGroup 사람-추가 얼굴](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. 이 API 호출에서 감지 모델에서와 같이 동일한 방식으로 지정할 수 있습니다 [직면-검색]합니다.

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceServiceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

이 코드를 만듭니다를 **PersonGroup** ID를 사용 하 여 `mypersongroupid` 추가 **Person** 되도록 합니다. 이 얼굴을 추가 하는 다음 **Person** 사용 하는 `detection_02` 모델. 지정 하지 않으면 합니다 *detectionModel* 매개 변수를 API에는 기본 모델은 사용할지 `detection_01`합니다.

> [!NOTE]
> 모든 얼굴에 대 한 동일한 감지 모델을 사용할 필요가 없습니다를 **Person** 개체를 비교할 새 얼굴을 감지 하는 경우 동일한 감지 모델을 사용할 필요가 **Person** 개체 (에 [직면-식별] 예를 들어 API).

## <a name="add-face-to-facelist-with-specified-model"></a>지정 된 모델을 사용 하 여 얼굴 FaceList 추가

얼굴을 추가 하면 기존 감지 모델을 지정할 수도 있습니다 **FaceList** 개체입니다. .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

이 코드를 만듭니다는 **FaceList** 호출 `My face collection` 얼굴을 사용 하 여 추가 된 `detection_02` 모델. 지정 하지 않으면 합니다 *detectionModel* 매개 변수를 API에는 기본 모델은 사용할지 `detection_01`합니다.

> [!NOTE]
> 모든 얼굴에 대 한 동일한 감지 모델을 사용할 필요가 없습니다를 **FaceList** 비교할 새 얼굴을 감지 하는 경우 동일한 검색 모델을 사용 하는 것이 필요가 없습니다 개체에 **FaceList** 개체입니다.

## <a name="evaluate-different-models"></a>여러 모델 평가

다른 얼굴 검색 모델은 다양 한 작업에 최적화 됩니다. 차이점에 대 한 개요는 다음 표를 참조 하세요.

|**detection_01**  |**detection_02**  |
|---------|---------|
|모든 얼굴 감지 작업에 대해 기본 선택입니다. | 2019 년 5 월에 모든 얼굴 감지 작업에서 필요에 따라 사용 가능한 릴리스.
|소형, 측면, 또는 얼굴을 흐리게 표시에 대 한 최적화 되지 않습니다.  | 작은, 쪽-보기 및 얼굴을 흐리게 표시에 대 한 정확도 향상된 합니다. |
|반환 얼굴 검색 호출에서 지정 하는 경우 특성 (머리 포즈, 연령, 감정, 및 등). |  얼굴 특성을 반환 하지 않습니다.     |
|검색 호출에서 지정 하는 경우 반환 랜드마크에 직면 합니다.   | 얼굴 랜드마크를 반환 하지 않습니다.  |

성능을 비교 하는 가장 좋은 방법은 합니다 `detection_01` 고 `detection_02` 모델에 샘플 데이터 집합을 사용 하는 것입니다. 호출 것이 좋습니다 합니다 [직면-검색] API는 다양 한 이미지에서 특히 이미지를 보려면 어려운 얼굴 또는 많은 얼굴의 각 검색 모델을 사용 하 여 합니다. 각 모델을 반환 하는 얼굴 수에 주의 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다른 Face Api를 사용 하는 검색 모델을 지정 하는 방법을 알아보았습니다. 다음으로, 얼굴 감지를 사용하는 것을 시작하는 빠른 시작을 수행합니다.

* [(.NET SDK) 이미지에서 얼굴 감지](../quickstarts/csharp-detect-sdk.md)

[직면-검색]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[직면-식별]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
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