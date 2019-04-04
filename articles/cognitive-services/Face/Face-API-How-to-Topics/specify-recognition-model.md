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
ms.openlocfilehash: 33348e637143b923719425b9674f99a475d848d9
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58806680"
---
# <a name="specify-a-face-recognition-model"></a>얼굴 인식 모델 지정

이 가이드에서는 얼굴 감지, 식별 및 Azure의 Face API를 사용 하 여 유사 항목 검색에 대 한 얼굴 인식 모델을 지정 하는 방법을 보여 줍니다.

Face API 기계 학습 모델을 사용 하 여 이미지에서 사람 얼굴에서 작업을 수행 합니다. 고객 의견 및 연구의 발전에 따라 모델의 정확도 개선 하기 위해 계속 및 모델 업데이트와 향상 된이 기능을 제공할 것입니다. 개발자가 사용할 수 있습니다; 원하는 얼굴 인식 모델의 버전을 지정 하는 옵션 해당 사용 사례에 가장 적합 한 모델을 선택할 수 있습니다.

새 사용자 인 경우에 최신 모델을 사용 하는 것이 좋습니다. 에 읽기 모델 충돌을 방지 하는 동안 다양 한 얼굴 작업에서 지정 하는 방법을 알아봅니다. 고급 사용자가 확실 하지 않으면 최신 모델로 전환 해야 하는 경우 이동 합니다 [다른 모델을 평가](#evaluate-different-models) 새 모델을 평가 하 고 현재 데이터 집합을 사용 하 여 결과 비교 하는 섹션입니다.

## <a name="prerequisites"></a>필수 조건

AI 얼굴 감지 및 id의 개념을 잘 알고 있어야 합니다. 가 아닌 경우 먼저 이러한 방법 가이드를 참조 합니다.

* [이미지에서 얼굴을 감지 하는 방법](HowtoDetectFacesinImage.md)
* [이미지에서 얼굴을 식별 하는 방법](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 얼굴 감지

얼굴 감지는 얼굴의 시각적 랜드마크를 식별 하 고 해당 경계 상자 위치를 찾습니다. 또한 발생 하는 기능을 추출 하 고 식별에 사용 하기 위해 저장 합니다. 이 정보를 모두 표현의 한 면을 형성 합니다.

검색 작업을 수행할 때 모델 버전을 지정할 수 있도록 인식 모델 얼굴 기능 추출 되는 경우 사용 됩니다.

사용 하는 경우는 [직면-검색] API 모델 버전을 할당 합니다 `recognitionModel` 매개 변수입니다. 사용 가능한 값은 다음과 같습니다.

* `recognition_01`
* `recognition_02`

선택적으로 지정할 수 있습니다는 _returnRecognitionModel_ 매개 변수 (기본 **false**)를 나타내는 여부를 _recognitionModel_ 응답에 반환 되어야 합니다. 따라서 요청 URL에 대 한를 [직면-검색] REST API는 다음과 같이 표시 됩니다.

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

클라이언트 라이브러리를 사용 하는 경우에 값을 할당할 수 있습니다 `recognitionModel` 버전을 나타내는 문자열을 전달 하 여 합니다.
경우 두면 할당 하지 않은 경우 기본 모델 버전 (_recognition_01_) 사용 됩니다. .NET 클라이언트 라이브러리에 대 한 다음 코드 예제를 참조 하세요.

```csharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 얼굴 식별

Face API는 이미지에서 얼굴 데이터를 추출 하 고 사용 하 여를 연결할 수는 **Person** 개체 (통해를 [얼굴을 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 예를 들어 API 호출), 및 다중 **Person** 개체 일 수 있습니다 에 함께 저장 된 **PersonGroup**합니다. 그런 다음 새 얼굴을 비교할 수는 **PersonGroup** (사용 하 여는 [직면-식별] 호출), 해당 그룹 내에서 일치 하는 사람을 식별할 수 있습니다.

**PersonGroup** 있어야 하나의 고유 인식 모델의 모든 합니다 **Person**가 지정할 수 있습니다 사용 하 여는 `recognitionModel` 그룹을 만들 때 매개 변수 ([PersonGroup - 만들기] 나 [LargePersonGroup - 만들기]). 원래가 매개이 변수를 지정 하지 않으면 경우 `recognition_01` 모델을 사용 합니다. 그룹 인식 모델을 사용 하 여 생성 된 항상 사용 하 고 적용 합니다. 추가 될 때 새 면이이 모델에 연결 된 됩니다. 이 그룹을 만든 후 변경할 수 없습니다. 어떤 모델을 봅니다를 **PersonGroup** 구성를 사용 하 여는 [PersonGroup - Get] 사용 하 여 API를 _returnRecognitionModel_ 으로 매개 변수 설정 **true**.

.NET 클라이언트 라이브러리에 대 한 다음 코드 예제를 참조 하세요.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

이 코드를 **PersonGroup** ID를 사용 하 여 `mypersongroupid` 만들어지면 사용 하도록 설정 하 고는 _recognition_02_ 얼굴 기능 추출에 모델.

얼굴을 감지 하는 경우이 비교를 사용 하는 모델을 지정 해야 하는 마찬가지로 **PersonGroup** (통해 합니다 [직면-검색] API). 사용 모델은 항상와 일관 되어야 합니다 **PersonGroup**의 구성을 호환 되지 않는 모델 인해 작업이 실패할 것이 고, 그렇지 합니다.

에 변경 사항이 없는 합니다 [직면-식별] API; 검색에서 모델 버전을 지정 해야 합니다.

## <a name="find-similar-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 유사한 얼굴 찾기

또한 유사성 검색에 대 한 인식 모델을 지정할 수 있습니다. 모델 버전을 지정할 수 있습니다 `recognitionModel` 얼굴 목록을 만들면 [FaceList-만들기] API 또는 [LargeFaceList-만들기]합니다. 원래가 매개이 변수를 지정 하지 않으면 경우 `recognition_01` 모델을 사용 합니다. 얼굴 목록을 인식 모델을 사용 하 여 생성 된 항상 사용 하 고 적용 합니다. 추가 될 때 새 면이이 모델에 연결 된 됩니다. 이 만든 후 변경할 수 없습니다. 얼굴 목록을 사용 하 여 구성 된 어떤 모델을 보려면 사용 하 여는 [FaceList - Get] 사용 하 여 API를 _returnRecognitionModel_ 으로 설정 된 매개 변수 **true**합니다.

.NET 클라이언트 라이브러리에 대 한 다음 코드 예제를 참조 하세요.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

이 코드 이라는 얼굴 목록을 만듭니다 `My face collection`를 사용 하 여 합니다 _recognition_02_ 기능 추출에 대 한 모델입니다. 새 검색 된 얼굴에 유사한 얼굴에 대 한이 얼굴 목록을 검색할 때 해당 얼굴 해야 검색 되었습니다 ([직면-검색])를 사용 하는 _recognition_02_ 모델입니다. 이전 섹션과 모델 일관성을 유지 해야 합니다.

에 변경 사항이 없는 [얼굴-비슷한 찾을] API로만 검색에서 모델 버전을 지정 합니다.

## <a name="verify-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 얼굴 확인

합니다 [직면-확인] API 여부를 두 얼굴이 같은 사람의 확인 합니다. 인식 모델의 경우와 관련 하 여 확인 API에서 변하지 않습니다 하지만 동일한 모델을 사용 하 여 검색 된 얼굴 비교할 수 있습니다. 따라서 두 얼굴 둘 다 필요를 사용 하 여 검색할 `recognition_01` 또는 `recognition_02`합니다.

## <a name="evaluate-different-models"></a>여러 모델 평가

성능을 비교 하려는 경우는 _recognition_01_ 하 고 _recognition_02_ 해야 데이터에서 모델:

1. 2 개를 만든 **PersonGroup**사용 하 여 s _recognition_01_ 하 고 _recognition_02_ 각각.
1. 이미지 데이터를 사용 하 여 얼굴을 감지 되도록 어셈블리를 등록 하 **Person**이 두 s **PersonGroup**사용 하 여 처리 및 학습 트리거 [PersonGroup - 학습] API.
1. 테스트 [직면-식별] 둘 다에 **PersonGroup**s 및 결과 비교 합니다.

일반적으로 정확도 임계값 (0과 얼마나 자신 모델 해야 결정 얼굴을 식별 하는 것 사이의 값)을 지정 하면 다른 모델에 대 한 서로 다른 임계값을 사용 하도록 해야 합니다. 하나의 모델에 대 한 임계값을 다른 공유 하기 위한 것 및 동일한 결과 반드시 생성 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다양 한 얼굴 서비스 Api 사용 하 여 사용할 인식 모델을 지정 하는 방법을 알아보았습니다. 다음으로, 얼굴 감지를 사용 하 여 시작 하려면 빠른 시작을 수행 합니다.

* [이미지에서 얼굴 감지](../quickstarts/csharp-detect-sdk.md)

[직면-검색]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[얼굴-비슷한 찾을]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[직면-식별]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[직면-확인]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - 학습]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList-만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
