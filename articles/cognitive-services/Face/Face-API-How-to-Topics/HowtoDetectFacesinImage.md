---
title: '예: 이미지에서 얼굴 감지 - Face API'
titleSuffix: Azure Cognitive Services
description: Face API를 사용하여 이미지에서 얼굴을 감지합니다.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: a4c74ff70a4426abf97562bf997479a91afbf17a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124051"
---
# <a name="example-how-to-detect-faces-in-image"></a>예: 이미지에서 얼굴을 감지하는 방법

이 가이드에서는 추출된 성별, 나이 또는 포즈와 같은 얼굴 특성이 포함된 이미지에서 얼굴을 감지하는 방법을 설명합니다. 샘플은 Face API 클라이언트 라이브러리를 사용하여 C#에서 작성되었습니다. 

## <a name="concepts"></a>개념

이 가이드에서 다음 개념에 대해 익숙치 않다면 언제든지 [용어집](../Glossary.md)에 나와 있는 정의를 참조하세요. 

- 얼굴 감지
- 얼굴 이정표
- 머리 포즈
- 얼굴 특성

## <a name="preparation"></a>준비

이 샘플에서는 다음과 같은 기능을 설명합니다. 

- 이미지에서 얼굴을 감지하고 사각형 프레이밍을 사용하도록 설정
- 눈동자, 코나 입의 위치를 분석한 다음, 이미지에서 표시
- 얼굴의 머리 포즈, 성별 및 나이를 분석

이러한 기능을 실행하려면 선명한 얼굴이 하나 이상 포함된 이미지를 준비해야 합니다. 

## <a name="step-1-authorize-the-api-call"></a>1단계: API 호출 권한 부여

Face API를 호출할 때마다 구독 키가 필요합니다. 이 키는 쿼리 문자열 매개 변수를 통해 전달되거나 요청 헤더에서 지정되어야 합니다. 쿼리 문자열을 통해 구독 키를 전달하려면 [얼굴 - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)에 대한 요청 URL을 참조하세요. 다음은 예제입니다.

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

또는 구독 키를 HTTP 요청 헤더에서 지정할 수도 있습니다. **ocp-apim-subscription-key: &lt;Subscription Key&gt;** 클라이언트 라이브러리를 사용할 경우 구독 키가 FaceServiceClient 클래스의 생성자를 통해 전달됩니다. 예: 
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step-2-upload-an-image-to-the-service-and-execute-face-detection"></a>2단계: 서비스에 이미지를 업로드하고 얼굴 감지를 실행

얼굴 감지를 수행하는 가장 기본적인 방법은 직접 이미지를 업로드하는 것입니다. JPEG 이미지에서 읽은 데이터와 함께 응용 프로그램/옥텟 스트림 콘텐츠 형식으로 “POST” 요청을 보내 수행합니다. 이미지의 최대 크기는 4MB입니다.

클라이언트 라이브러리를 사용하면 업로드를 통한 얼굴 감지는 스트리밍 개체를 전달하여 수행됩니다. 아래 예제를 참조하세요.

```CSharp
using (Stream s = File.OpenRead(@"D:\MyPictures\image1.jpg"))
{
    var faces = await faceServiceClient.DetectAsync(s, true, true);
 
    foreach (var face in faces)
    {
        var rect = face.FaceRectangle;
        var landmarks = face.FaceLandmarks;
    }
}
```

FaceServiceClient의 DetectAsync 메서드가 비동기임을 확인하세요. await 절을 사용하려면 호출 메서드도 비동기로 표시되어야 합니다.
이미지가 이미 웹에 있고 URL이 있는 경우 URL을 제공하여 얼굴 감지를 실행할 수 있습니다. 이 예제에서 요청 본문은 URL이 포함된 JSON 문자열입니다.
클라이언트 라이브러리를 사용하면 URL를 통한 얼굴 감지를 DetectAsync 메서드의 다른 오버로드를 사용하여 쉽게 실행할 수 있습니다.

```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

감지된 얼굴과 함께 반환되는 FaceRectangle 속성은 기본적으로 얼굴에서의 위치(픽셀)입니다. 보통 이 사각형에는 눈, 눈썹, 코 및 입이 포함됩니다. 머리의 맨 윗부분, 귀, 턱은 포함되지 않습니다. 전체 머리 또는 중간 인물 사진을 자르는 경우(사진 ID 유형 이미지) 일부 응용 프로그램에 사용하기에 얼굴 영역이 너무 작을 수 있으므로 사각형의 얼굴 프레임 영역을 확대할 수 있습니다. 얼굴의 위치를 더 정확하게 찾기 위해서는 다음 섹션에 설명되어 있는 얼굴 이정표를 사용(얼굴 특징 또는 얼굴 방향 찾기 메커니즘)하는 것이 유용합니다.

## <a name="step-3-understanding-and-using-face-landmarks"></a>3단계: 얼굴 이정표에 대한 이해 및 사용

얼굴 이정표는 얼굴에서의 일련의 세부 지점입니다(일반적으로 얼굴의 지점은 눈동자, 안각 또는 코). 얼굴 이정표는 얼굴을 감지하는 동안 분석할 수 있는 선택적 특성입니다. 감지 결과에 얼굴 이정표를 포함시키려면 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 호출할 때 ‘true’를 returnFaceLandmarks 쿼리 매개 변수에 부울 값으로 전달하거나, FaceServiceClient 클래스 DetectAsync 메서드에 대해 returnFaceLandmarks 선택적 매개 변수를 사용합니다.

기본적으로 27개의 이정표 지점이 사전 정의되어 있습니다. 정의된 27개의 모든 지점이 다음 그림에 나와 있습니다.

![HowToDetectFace](../Images/landmarks.1.jpg)

얼굴 사각형 프레임과 마찬가지로 반환되는 지점은 픽셀 단위입니다. 따라서 이미지에서 특정한 관심 지점을 더 손쉽게 표시할 수 있습니다. 다음 코드는 코와 눈동자의 위치를 검색하는 방법을 보여 줍니다.

```CSharp
var faces = await faceServiceClient.DetectAsync(imageUrl, returnFaceLandmarks:true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
 
    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;
 
    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;
 
    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
``` 

얼굴 이정표는 이미지의 얼굴 특성을 표시할 뿐만 아니라, 얼굴의 방향을 정확히 계산하는 데도 사용할 수 있습니다. 예를 들어 얼굴의 방향을 입의 가운데부터 눈동자의 가운데까지 벡터로 정의할 수 있습니다. 아래 코드에서는 이를 자세히 설명합니다.

```CSharp
var landmarks = face.FaceLandmarks;
 
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

얼굴이 있는 방향을 알고 있으면 얼굴에 맞춰 사각형 얼굴 프레임을 회전할 수 있습니다. 얼굴 이정표를 사용하면 확실히 더 많은 세부 정보 및 유용성을 제공할 수 있습니다.

## <a name="step-4-using-other-face-attributes"></a>4단계: 다른 얼굴 특성 사용

얼굴 이정표 외에, Face – Detect API는 얼굴에서 다른 여러 특성을 분석할 수도 있습니다. 이러한 특성에는 다음이 포함됩니다.

- Age
- 성별
- 웃는 정도
- 얼굴의 털
- 3D 머리 포즈

이러한 특성은 통계 알고리즘을 사용하여 예측할 수 있으나, 항상 100% 정확하지 않을 수도 있습니다. 그러나 이러한 특성으로 얼굴을 분류하려는 경우에는 여전히 유용합니다. 각 특성에 대한 자세한 내용은 [용어집](../Glossary.md)을 참조하세요.

다음은 얼굴을 감지하는 동안 얼굴 특성을 추출하는 간단한 예제입니다.

```CSharp
var requiredFaceAttributes = new FaceAttributeType[] {
                FaceAttributeType.Age,
                FaceAttributeType.Gender,
                FaceAttributeType.Smile,
                FaceAttributeType.FacialHair,
                FaceAttributeType.HeadPose,
                FaceAttributeType.Glasses
            };
var faces = await faceServiceClient.DetectAsync(imageUrl,
    returnFaceLandmarks: true,
    returnFaceAttributes: requiredFaceAttributes);

foreach (var face in faces)
{
    var id = face.FaceId;
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
}
``` 

## <a name="summary"></a>요약

이 가이드에서는 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API의 기능, 로컬 업로드된 이미지 또는 웹에 있는 이미지 URL에 대한 얼굴을 감지하는 방법, 사각형 얼굴 프레임을 반환하여 얼굴을 감지하는 방법, 얼굴 이정표, 3D 머리 포즈 및 다른 얼굴 특성을 분석하는 방법에 대해 알아보았습니다.

API 세부 사항에 대한 자세한 내용은 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)에 대한 API 참조 가이드를 참조하세요.

## <a name="related-topics"></a>관련 항목

[이미지에서 얼굴을 식별하는 방법](HowtoIdentifyFacesinImage.md)
