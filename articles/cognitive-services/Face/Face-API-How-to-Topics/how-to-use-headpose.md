---
title: HeadPose 특성 사용
titleSuffix: Azure Cognitive Services
description: 자동으로 얼굴 사각형을 회전 하거나 비디오 피드 헤드 제스처를 감지 하려면 HeadPose 특성을 사용 하는 방법에 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 168b4fce873206e39a32a83da3dc5509b431d6a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058584"
---
# <a name="use-the-headpose-attribute"></a>HeadPose 특성 사용

이 가이드에서는 검색 된 얼굴 HeadPose 특성을 사용 하 여 몇 가지 주요 시나리오를 사용 하는 방법을 볼 수 있습니다.

## <a name="rotate-the-face-rectangle"></a>얼굴 사각형을 회전

각 감지된 얼굴과 함께 반환된 얼굴 사각형은 이미지에서 얼굴의 크기와 위치를 표시합니다. 기본적으로 사각형을 항상 맞춥니다 (의 측면은 가로 및 세로) 이미지 이 비효율적일 수 있습니다 각 진된 얼굴을 프레이밍 하는 것에 대 한 합니다. 프로그래밍 방식으로 이미지에서 얼굴을 자르려면 하려는 경우를 자르는 사각형을 회전 하는 일을 할 수는 것이 좋습니다.

합니다 [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 샘플 앱은 해당 검색 된 얼굴 사각형을 회전 HeadPose 특성을 사용 합니다.

### <a name="explore-the-sample-code"></a>샘플 코드 탐색

프로그래밍 방식으로 HeadPose 특성을 사용하여 얼굴 사각형을 회전할 수 있습니다. 얼굴을 감지([얼굴을 감지하는 방법](HowtoDetectFacesinImage.md) 참조)하는 경우 이 특성을 지정하게 되면, 나중에 쿼리할 수 있습니다. [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 앱에서 다음 메서드는 **DetectedFace** 개체의 목록을 가져오고 **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** 개체의 목록을 반환합니다. 여기서 **Face** 는 업데이트된 사각형 좌표를 포함하여 얼굴 데이터를 저장하는 사용자 지정 클래스입니다. 에 대 한 새 값은 계산 **위쪽**, **왼쪽**를 **너비**, 및 **높이**, 새 필드와 **FaceAngle**회전을 지정 합니다.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>업데이트된 사각형 표시

여기에서 반환된 **Face** 개체를 사용하여 표시할 수 있습니다. [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) 의 다음 줄은 이 데이터로부터 새 사각형이 렌더링되는 방법을 보여줍니다.

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>헤드 제스처를 감지 합니다.

Nodding 실시간에서 HeadPose 변경을 추적 하 여 흔들기를 head 등 헤드 제스처를 검색할 수 있습니다. 사용자 지정 선거의 탐지기도이 기능을 사용할 수 있습니다.

선거의 검색은 주제는 실제 사용자는 이미지 또는 비디오 표현 되지 결정 하는 작업입니다. 헤드 제스처 탐지기 사용자에 대 한 이미지 표현이 아닌 특히 선거의 확인 하기 위해 한 가지 방법으로 사용 될 수 있습니다.

> [!CAUTION]
> 헤드 제스처를 실시간으로 검색 하려면 (초당 한 번 이상)는 높은 속도로 Face API를 호출 해야 합니다. 무료 계층 (f0) 구독이 있는 경우이 불가능 합니다. 유료 계층 구독을 보유 하는 경우 했는지 신속 하 게 api의 비용 계산에 대 한 호출 제스처 검색 합니다.

참조를 [Face API HeadPose 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) 헤드의 작업 예제는 GitHub의 동작을 검색 합니다.

## <a name="next-steps"></a>다음 단계

회전된 얼굴 사각형의 작업 예제는 GitHub의 [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF)앱을 참조합니다. 참조를 [Face API HeadPose 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) 헤드 이동 검색할 실시간에서 HeadPose 특성을 추적 하는 앱입니다.