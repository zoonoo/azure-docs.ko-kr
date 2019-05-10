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
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588774"
---
# <a name="get-face-detection-data"></a>얼굴 감지 데이터 가져오기

이 가이드에서는 지정된 이미지에서 성별, 연령 또는 자세와 같은 특성을 추출하기 위해 얼굴 감지를 사용하는 방법을 보여줍니다. 이 가이드에서 코드 조각은 Face API 클라이언트 라이브러리를 사용하여 C#을 통해 작성되었으며, 동일한 기능이 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 통해 가능합니다.

이 가이드에 표시 됩니다 하는 방법에:

- 이미지에서 얼굴의 크기와 위치를 가져옵니다.
- 이미지에서 다양 한 얼굴 랜드마크 (삼아, 코, 말하거나, 및 등)의 위치를 가져옵니다.
- 성별, 연령과 및 emotion 및 검색 된 얼굴의 다른 특성을 추측 합니다.

## <a name="setup"></a>설정

이 가이드에서는 이미 생성 된 가정를 **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** 라는 개체 `faceClient`, 얼굴 구독 키 및 끝점 url입니다. 여기에서 호출 하 여 얼굴 감지 기능을 사용할 수 있습니다 **[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** (이 가이드에 사용) 또는 **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)**. 참조를 [얼굴 감지 빠른 시작에 대 한 C# ](../quickstarts/csharp-detect-sdk.md) 이를 설정 하는 방법에 대 한 합니다.

이 가이드는 검색 호출의 세부 정보에 중점&mdash;전달할 수 있는 인수 및 반환된 된 데이터를 사용 하 여 수행할 수 있습니다. 각 작업에는 데 추가 시간이 소요 되는 대로 필요한 기능에 대 한 쿼리 하는 것이 좋습니다.

## <a name="get-basic-face-data"></a>기본 글꼴 데이터 가져오기

얼굴 찾고 이미지에 해당 위치를 가져올 메서드를 호출 합니다 _returnFaceId_ 매개 변수 설정 **true** (기본값).

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

반환 된 **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** 해당 고유 Id 및 글꼴의 픽셀 좌표를 제공 하는 사각형에 대 한 개체를 쿼리할 수 있습니다.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

참조 **[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)** 글꼴의 크기와 위치를 구문 분석 하는 방법에 대 한 정보에 대 한 합니다. 이 사각형 눈, 눈 썹, 코, 및 입;를 포함 하는 일반적으로 헤드, 귀, 및 chin 맨 반드시 포함 되지 않습니다. 전체 헤드 또는 중간 샷 세로 (사진 ID 형식 이미지)을 자르려면 얼굴 사각형을 사용 하려는 경우에 각 방향에는 특정 여백으로 사각형을 확장 하는 것이 좋습니다.

## <a name="get-face-landmarks"></a>얼굴 랜드마크 가져오기

얼굴 랜드마크는는 삼아 같은 얼굴 찾기 쉽게 점의 집합 또는 코의 팁입니다. 설정 하 여 얼굴 랜드마크 데이터를 가져올 수 있습니다 합니다 _returnFaceLandmarks_ 매개 변수를 **true**합니다.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

기본적으로 27개의 이정표 지점이 사전 정의되어 있습니다. 다음 그림에는 모든 27 지점을 보여 줍니다.

![레이블이 지정 된 모든 27 개의 특징을 사용 하 여 얼굴 다이어그램](../Images/landmarks.1.jpg)

얼굴 사각형 프레임 마찬가지로 픽셀 단위로 지점은 반환 합니다. 다음 코드는 코 및 삼아의 위치를 검색할 수 있습니다 하는 방법을 보여 줍니다.

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

얼굴 랜드마크 데이터는 정확 하 게 발생 하는 방향을 계산 하려면 데도 사용할 수 있습니다. 예를 들어 글꼴의 회전 중심 눈에 입 센터에서 벡터로 정의할 수 있습니다. 아래 코드에서는이 벡터를 계산합니다.

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

글꼴의 방향의 알고 있으면 더 적절히 맞출 사각형 얼굴 프레임을 회전할 수 있습니다. 이미지에서 얼굴을 자르려면 하려는 경우 얼굴 항상 수직 표시 되도록 이미지를 프로그래밍 방식으로 회전할 수 있습니다.

## <a name="get-face-attributes"></a>얼굴 특성 가져오기

얼굴 감지 API는 얼굴 사각형 및 랜드마크 외에도 얼굴의 몇 가지 개념적 특성을 분석할 수 있습니다. 내용은 다음과 같습니다.

- Age
- 성별
- 웃는 정도
- 얼굴의 털
- 안경
- 3D 머리 포즈
- Emotion

> [!IMPORTANT]
> 이러한 특성을 예측 하는 통계 알고리즘을 사용 하 여 항상 정확 하지 않을 하며 특성 데이터를 기반으로 결정을 내릴 경우에 주의 해야 합니다.
>

얼굴 특성을 분석 하려면 설정 합니다 _returnFaceAttributes_ 매개 변수 목록을 **[FaceAttributeType 열거형](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** 값입니다.

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

그런 다음 반환된 된 데이터에 대 한 참조를 가져오고 추가로 수행 필요에 따라 작업 합니다.

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

각 특성에 대 한 자세한 내용은 참조는 [용어집](../Glossary.md)합니다.

## <a name="next-steps"></a>다음 단계

이 가이드의 얼굴 감지의 다양 한 기능을 사용 하는 방법을 알아보았습니다. 다음으로, 참조를 [용어집](../Glossary.md) 세부 정보를 보려면 검색 한 얼굴 데이터에 대 한 합니다.

## <a name="related-topics"></a>관련 항목

- [참조 설명서 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [참조 설명서 (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
