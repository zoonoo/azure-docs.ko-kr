---
title: Face API는 이미지에서 얼굴 감지
titleSuffix: Azure Cognitive Services
description: 얼굴 감지 기능에 의해 반환되는 다양한 데이터를 사용하는 방법에 알아봅니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124544"
---
# <a name="get-face-detection-data"></a>얼굴 감지 데이터 가져오기

이 가이드에는 특성을 추출할 성별, 연령 또는 자세와 같은 지정된 된 이미지에서 얼굴 감지를 사용 하는 방법을 보여 줍니다. 이 가이드에서 코드 조각을 작성 된 C# Azure Cognitive Services Face API 클라이언트 라이브러리를 사용 하 여 합니다. 동일한 기능을 통해 제공 합니다 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)합니다.

표시 하는이 가이드에 있습니다.

- 이미지에서 얼굴의 크기와 위치를 가져옵니다.
- 이미지에서 입 삼아, 코, 등의 다양 한 얼굴 랜드마크의 위치를 가져옵니다.
- 성별, 연령, 감정, 및 기타 특성 검색 된 얼굴을 추측 합니다.

## <a name="setup"></a>설정

이 가이드에서는 이미 생성 하는 가정 된 [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) 라는 개체 `faceClient`, 얼굴 구독 키 및 끝점 URL을 사용 하 여 합니다. 여기에서 호출 하 여 얼굴 감지 기능을 사용할 수 있습니다 [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet),이 가이드에서는 사용 되는 또는 [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)합니다. 이 기능을 설정 하는 방법에 대 한 자세한 내용은 참조는 [검색 얼굴에 대 한 빠른 시작 C# ](../quickstarts/csharp-detect-sdk.md)합니다.

이 가이드 검색 호출의 세부 사항에 중점을 두고, 전달할 수 있는 인수 등 및 반환된 된 데이터를 사용 하 여 수행할 수 있는 작업 필요한 기능만 쿼리 하는 것이 좋습니다. 각 작업에는 완료 하는 데 추가 시간이 걸립니다.

## <a name="get-basic-face-data"></a>기본 얼굴 데이터 가져오기

얼굴 찾고 이미지에 해당 위치를 가져올 메서드를 호출 합니다 _returnFaceId_ 매개 변수 설정 **true**합니다. 이 설정은 기본값입니다.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

반환 된 쿼리하면 [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) 개체가 고유한 Id 및 글꼴의 픽셀 좌표를 제공 하는 사각형입니다.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

글꼴의 크기와 위치를 구문 분석 하는 방법에 대 한 자세한 내용은 [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)합니다. 일반적으로이 사각형 눈, 눈 썹, 코, 및 입 포함 됩니다. 헤드, 귀, 및 chin 위쪽에 반드시 포함이 되지 않습니다. 전체 헤드를 자르거나 중간 샷 세로, 사진 ID 형식 이미지를 만들기 위해 아마도 가져오기 얼굴 사각형을 사용 하려면 각 방향에 사각형을 확장할 수 있습니다.

## <a name="get-face-landmarks"></a>얼굴 랜드마크 가져오기

[랜드마크 얼굴](../concepts/face-detection.md#face-landmarks) 는 삼아 코의 팁 등을 얼굴 찾기 쉽게 요소의 집합입니다. 얼굴 랜드마크 데이터를 가져오려면 다음을 설정 합니다 _returnFaceLandmarks_ 매개 변수를 **true**합니다.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

다음 코드는 코 및 동공의 위치를 검색하는 방법을 보여줍니다.

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

얼굴 랜드마크 데이터 정확 하 게 발생 하는 방향을 계산에 사용할 수도 있습니다. 예를 들어 명사 센터로 입 센터에서 벡터로 얼굴의 회전을 정의할 수 있습니다. 다음 코드에서는이 벡터를 계산합니다.

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

글꼴의 방향을 알고 있는 경우 더 적절히 맞출 사각형 얼굴 프레임을 회전할 수 있습니다. 이미지에서 얼굴을 자르려면 얼굴 항상 수직 표시 되도록 이미지를 프로그래밍 방식으로 회전할 수 있습니다.

## <a name="get-face-attributes"></a>얼굴 특성 가져오기

얼굴 감지 API는 얼굴 사각형 및 랜드마크 외에도 얼굴의 몇 가지 개념적 특성을 분석할 수 있습니다. 전체 목록을 보려면 참조는 [얼굴 특성](../concepts/face-detection.md#attributes) 개념 섹션입니다.

얼굴 특성을 분석 하려면 설정 합니다 _returnFaceAttributes_ 매개 변수 목록을 [FaceAttributeType 열거형](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) 값입니다.

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

그런 다음 반환된 된 데이터에 대 한 참조를 가져와 필요에 따라 더 많은 작업을 수행 합니다.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

각 특성에 대 한 자세한 내용은 참조는 [얼굴 감지 및 특성](../concepts/face-detection.md) 개념 가이드입니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 얼굴 감지의 다양 한 기능을 사용 하는 방법을 알아보았습니다. 다음으로 세부 자습서를 수행 하 여 앱에 이러한 기능을 통합.

- [자습서: 이미지에서 얼굴 데이터를 표시 하는 WPF 앱 만들기](../Tutorials/FaceAPIinCSharpTutorial.md)
- [자습서: 검색 하 여 이미지에서 얼굴 프레임 Android 앱 만들기](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>관련된 항목

- [참조 설명서 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [참조 설명서 (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)