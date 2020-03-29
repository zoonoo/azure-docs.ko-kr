---
title: 이미지에서 얼굴 감지 - 얼굴
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 얼굴 감지를 사용하여 지정된 이미지에서 성별, 연령 또는 포즈와 같은 속성을 추출하는 방법을 보여 줍니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169885"
---
# <a name="get-face-detection-data"></a>얼굴 감지 데이터 얻기

이 가이드에서는 얼굴 감지를 사용하여 지정된 이미지에서 성별, 연령 또는 포즈와 같은 속성을 추출하는 방법을 보여 줍니다. 이 가이드의 코드 조각은 Azure Cognitive Services Face 클라이언트 라이브러리를 사용하여 C#로 작성됩니다. [REST API를](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)통해 동일한 기능을 사용할 수 있습니다.

이 가이드에서는 다음 방법을 보여 주며 다음과 같은 방법을 보여 주시면 됩니다.

- 이미지에서 얼굴의 위치와 치수를 가져옵니다.
- 이미지에서 동공, 코, 입 등 다양한 얼굴 랜드마크의 위치를 가져옵니다.
- 감지된 얼굴의 성별, 나이, 감정 및 기타 속성을 추측합니다.

## <a name="setup"></a>설치 프로그램

이 가이드에서는 Face 구독 키 및 끝점 URL을 통해 [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) 개체(?)를 `faceClient`이미 생성했다고 가정합니다. 여기에서 이 가이드에 사용되는 [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)또는 [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)를 호출하여 얼굴 감지 기능을 사용할 수 있습니다. 이 기능을 설정하는 방법에 대한 지침은 빠른 시작 중 하나를 따르십시오.

이 가이드에서는 전달할 수 있는 인수 및 반환된 데이터로 수행할 수 있는 작업의 등 Detect 호출의 세부 사항에 대해 중점합니다. 필요한 기능만 쿼리하는 것이 좋습니다. 각 작업을 완료하는 데 추가 시간이 걸립니다.

## <a name="get-basic-face-data"></a>기본 얼굴 데이터 얻기

얼굴을 찾고 이미지에서 해당 위치를 얻으려면 _returnFaceId_ 매개 변수가 **true로**설정된 메서드를 호출합니다. 이 설정은 기본값입니다.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

반환된 [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) 개체에 대해 고유한 아이디와 얼굴의 픽셀 좌표를 제공하는 사각형을 쿼리할 수 있습니다.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

면의 위치와 치수를 구문 분석하는 방법에 대한 자세한 내용은 [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)을 참조하십시오. 일반적으로 이 직사각형에는 눈, 눈썹, 코 및 입이 포함됩니다. 머리, 귀 및 턱의 상단이 반드시 포함되어 있지는 않습니다. 얼굴 사각형을 사용하여 전체 머리를 자르거나 사진 ID 유형 이미지의 경우 중간 샷 초상화를 얻으려면 각 방향으로 사각형을 확장할 수 있습니다.

## <a name="get-face-landmarks"></a>얼굴 랜드마크 받기

[얼굴 랜드마크는](../concepts/face-detection.md#face-landmarks) 눈동자 또는 코 끝과 같이 얼굴에서 찾기 쉬운 점 집합입니다. 면 랜드마크 데이터를 얻으려면 _returnFace랜드마크 매개변수를_ **true로**설정합니다.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

다음 코드는 코와 눈동자의 위치를 검색하는 방법을 보여 줍니다.

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

면 랜드마크 데이터를 사용하여 면의 방향을 정확하게 계산할 수도 있습니다. 예를 들어 얼굴의 회전을 입 의 중심에서 눈의 중심으로 의 벡터로 정의할 수 있습니다. 다음 코드는 이 벡터를 계산합니다.

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

면의 방향을 알고 있으면 직사각형 면 프레임을 회전하여 더 올바르게 정렬할 수 있습니다. 이미지에서 면을 자르려면 얼굴이 항상 똑바로 표시되도록 이미지를 프로그래밍 방식으로 회전할 수 있습니다.

## <a name="get-face-attributes"></a>얼굴 속성 받기

얼굴 사각형 및 랜드마크 외에도 얼굴 감지 API는 얼굴의 여러 개념적 특성을 분석할 수 있습니다. 전체 목록은 얼굴 속성 개념 섹션을 [참조하세요.](../concepts/face-detection.md#attributes)

얼굴 특성을 분석하려면 _returnFaceAttributes_ 매개 변수를 [FaceAttributeType 열거형](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) 값 목록으로 설정합니다.

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

그런 다음 반환된 데이터에 대한 참조를 얻고 필요에 따라 더 많은 작업을 수행합니다.

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

각 특성에 대한 자세한 내용은 얼굴 검색 및 특성 개념 [가이드를](../concepts/face-detection.md) 참조하세요.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 얼굴 감지의 다양한 기능을 사용하는 방법을 배웠습니다. 다음으로 자세한 자습서를 따라 이러한 기능을 앱에 통합합니다.

- [자습서: 이미지에서 얼굴 데이터를 표시하는 WPF 앱 만들기](../Tutorials/FaceAPIinCSharpTutorial.md)
- [자습서: 이미지에서 얼굴을 감지하고 포착하는 Android 앱 만들기](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>관련 항목

- [참조 문서(REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [참조 문서(.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)