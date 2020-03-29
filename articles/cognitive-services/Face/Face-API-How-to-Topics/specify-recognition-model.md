---
title: 인식 모델을 지정하는 방법 - 얼굴
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Face 응용 프로그램에서 사용할 인식 모델을 선택하는 방법을 보여 줍니다.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76938836"
---
# <a name="specify-a-face-recognition-model"></a>얼굴 인식 모델 지정

이 가이드에서는 Azure Face 서비스를 사용하여 얼굴 감지, 식별 및 유사성 검색을 위한 얼굴 인식 모델을 지정하는 방법을 보여 주며, 이 가이드에서는 얼굴 인식 모델을 지정합니다.

Face 서비스는 기계 학습 모델을 사용하여 이미지에서 사람의 얼굴에 대한 작업을 수행합니다. 우리는 고객 피드백과 연구의 진보를 기반으로 모델의 정확성을 지속적으로 개선하고, 우리는 모델 업데이트로 이러한 개선을 제공합니다. 개발자는 사용할 얼굴 인식 모델의 버전을 지정할 수 있습니다. 사용 사례에 가장 적합한 모델을 선택할 수 있습니다.

새 사용자인 경우 최신 모델을 사용하는 것이 좋습니다. 모델 충돌을 피하면서 다른 Face 연산에서 지정하는 방법을 알아보려면 계속 읽어보십시오. 고급 사용자이고 최신 모델로 전환해야 하는지 확실하지 않은 경우 다른 [모델 평가](#evaluate-different-models) 섹션으로 건너뛰서 새 모델을 평가하고 현재 데이터 집합을 사용하여 결과를 비교합니다.

## <a name="prerequisites"></a>사전 요구 사항

AI 얼굴 감지 및 식별의 개념에 대해 잘 알고 있어야 합니다. 그렇지 않은 경우 먼저 다음 방법 가이드를 참조하십시오.

* [이미지에서 얼굴을 감지하는 방법](HowtoDetectFacesinImage.md)
* [이미지에서 얼굴을 식별하는 방법](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>지정된 모델로 면 감지

얼굴 감지는 사람의 얼굴의 시각적 랜드마크를 식별하고 경계 상자 위치를 찾습니다. 또한 얼굴의 특징을 추출하여 식별에 사용할 수 있도록 저장합니다. 이 모든 정보는 한 면의 표현을 형성합니다.

인식 모델은 면 피쳐를 추출할 때 사용되므로 감지 작업을 수행할 때 모델 버전을 지정할 수 있습니다.

[Face - API를 검색하는] 경우 매개 변수를 사용하여 모델 버전을 할당합니다. `recognitionModel` 사용 가능한 값은

* `recognition_01`
* `recognition_02`

선택적으로 _returnRecognitionModel_ 매개 변수(기본 **false)를**지정하여 _인식 모델이_ 응답으로 반환되어야 하는지 여부를 나타낼 수 있습니다. 따라서 Face - REST API [감지에] 대한 요청 URL은 다음과 같습니다.

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

클라이언트 라이브러리를 사용하는 경우 버전을 나타내는 문자열을 `recognitionModel` 전달하여 값을 할당할 수 있습니다.
할당되지 않은 상태로 두면 기본 모델_버전(recognition_01)이_사용됩니다. .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하십시오.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>지정된 모델로 면 식별

Face 서비스는 이미지에서 얼굴 데이터를 추출하여 **Person** 개체(예: [face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 추가 호출)와 연결할 수 있으며 여러 **사람** 개체를 **PersonGroup에**함께 저장할 수 있습니다. 그런 다음 새 얼굴을 **PersonGroup(얼굴** - [식별] 호출)과 비교할 수 있으며 해당 그룹 내의 일치하는 사람을 식별할 수 있습니다.

**PersonGroup은** 모든 **Person**s에 대해 하나의 고유한 인식 모델을 가져야 `recognitionModel` 하며 그룹을 만들 때 매개 변수를 사용하여 지정할 수 있습니다(PersonGroup[- 만들기] 또는 [LargePersonGroup - 만들기).] 이 매개변수를 지정하지 않으면 `recognition_01` 원래 모델이 사용됩니다. 그룹은 항상 생성된 인식 모델을 사용하며 새 면이 이 모델에 추가될 때 이 모델과 연결됩니다. 그룹을 만든 후에는 변경할 수 없습니다. **PersonGroup이** 구성된 모델을 보려면 [PersonGroup -] _returnRecognitionModel_ 매개 변수를 **true로**설정한 API 를 사용합니다.

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하십시오.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

이 코드에서는 ID가 `mypersongroupid` 있는 **PersonGroup이** 만들어지고 _recognition_02_ 모델을 사용하여 얼굴 피처를 추출하도록 설정됩니다.

따라서 얼굴을 감지할 때 사용할 모델을 지정하여 이 **PersonGroup과** 비교할 수 [있습니다(Face - 감지] API를 통해). 사용하는 모델은 항상 **PersonGroup의**구성과 일치해야 합니다. 그렇지 않으면 호환되지 않는 모델로 인해 작업이 실패합니다.

얼굴 - API [식별에] 변화가 없습니다. 검색에서 모델 버전을 지정하기만 하면 됩니다.

## <a name="find-similar-faces-with-specified-model"></a>지정된 모델로 유사한 면 찾기

유사성 검색을 위해 인식 모델을 지정할 수도 있습니다. [FaceList -] API 만들기 `recognitionModel` 또는 [LargeFaceList - 만들기를]사용하여 얼굴 목록을 만들 때 모델 버전을 할당할 수 있습니다. 이 매개변수를 지정하지 않으면 `recognition_01` 원래 모델이 사용됩니다. 면 목록은 항상 작성된 인식 모델을 사용하며 새 면이 이 모델에 추가될 때 이 모델과 연결됩니다. 생성 한 후에는 변경할 수 없습니다. 얼굴 목록이 구성되는 모델을 보려면 [FaceList -] _returnRecognitionModel_ 매개 변수를 **true로**설정한 API 를 사용합니다.

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하십시오.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

이 코드는 피처 `My face collection`추출을 위해 _recognition_02_ 모델을 사용하여 "라는 면 목록을 만듭니다. 이 얼굴 목록에서 새 감지된 면과 유사한 면을 검색할 때 _해당_ 얼굴이 감지되어야[합니다(얼굴 - 감지)recognition_02]모델을 사용하여. 이전 섹션에서와 같이 모델의 일관성이 있어야 합니다.

얼굴에 는 변경 이 없습니다 [- 유사한 API찾기;] 검색에서 모델 버전만 지정합니다.

## <a name="verify-faces-with-specified-model"></a>지정된 모델로 면 확인

Face - API [확인은] 두 얼굴이 같은 사람에 속하는지 여부를 확인합니다. 인식 모델과 관련하여 Verify API에는 변경이 없지만 동일한 모델로 검색된 얼굴만 비교할 수 있습니다. 따라서 두 면을 모두 사용하여 `recognition_01` 감지해야 `recognition_02`합니다.

## <a name="evaluate-different-models"></a>다양한 모델 평가

데이터에 recognition_01 _모델과_ _recognition_02_ 모델의 성능을 비교하려면 다음을 수행해야 합니다.

1. _각각 recognition_01_ _recognition_02_ 두 **PersonGroup**s를 만듭니다.
1. 이미지 데이터를 사용하여 얼굴을 감지하고 이 두 **PersonGroup**s에 대해 **Person**s에 등록하고 [PersonGroup - Train] API를 사용하여 교육 프로세스를 트리거합니다.
1. [얼굴로] 테스트 - 두 **PersonGroup**s에서 식별하고 결과를 비교합니다.

일반적으로 신뢰 임계값(면 식별을 위해 모델이 얼마나 확신하는지 결정하는 0과 1 사이의 값)을 지정하는 경우 다른 모델에 대해 서로 다른 임계값을 사용해야 할 수 있습니다. 한 모델의 임계값은 다른 모델과 공유하기 위한 것이 아니며 반드시 동일한 결과를 생성할 필요는 없습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다른 Face 서비스 API와 함께 사용할 인식 모델을 지정하는 방법을 배웠습니다. 다음으로 빠른 시작을 따라 얼굴 감지를 사용하여 시작합니다.

* [얼굴 .NET SDK](../Quickstarts/csharp-sdk.md)
* [페이스 파이썬 SDK](../Quickstarts/python-sdk.md)
* [페이스 고 SDK](../Quickstarts/go-sdk.md)

[얼굴 - 감지]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[얼굴 - 유사 찾기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[얼굴 - 식별]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[얼굴 - 확인]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[인물 그룹 - 받기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - 학습]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[페이스리스트 - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[페이스리스트 - 받기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[대형 페이스리스트 - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
