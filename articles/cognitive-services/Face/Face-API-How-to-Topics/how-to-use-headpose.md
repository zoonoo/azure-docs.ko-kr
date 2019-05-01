---
title: HeadPose 얼굴 사각형에 맞게 사용
titleSuffix: Azure Cognitive Services
description: 자동으로 얼굴 사각형을 회전 HeadPose 특성을 사용 하는 방법에 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: ddc5bc522c0d3ac258581f2a48a5c3b755302f01
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576524"
---
# <a name="use-the-headpose-attribute-to-adjust-the-face-rectangle"></a>얼굴 사각형에 맞게 HeadPose 특성을 사용 합니다.

이 가이드에서는 얼굴 개체의 사각형을 회전 하려면 HeadPose, 검색 된 얼굴 특성을 사용 합니다. 샘플에서이 가이드에서는 코드를 [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 샘플 앱에서.NET SDK를 사용 합니다.

모든 검색 된 얼굴을 사용 하 여 반환 하는 얼굴 사각형, 이미지에서 얼굴의 크기와 위치를 표시 합니다. 기본적으로 사각형을 항상 맞춥니다 (의 측면은 완벽 하 게 가로 및 세로) 이미지 이 비효율적일 수 있습니다 각 진된 얼굴을 프레이밍 하는 것에 대 한 합니다. 프로그래밍 방식으로 이미지에서 얼굴을 자르려면 하려는 경우를 자르는 사각형을 회전 하려면 유용 합니다.

## <a name="explore-the-sample-code"></a>샘플 코드 탐색

프로그래밍 방식으로 HeadPose 특성을 사용 하 여 얼굴 사각형을 회전할 수 있습니다. 얼굴을 감지 하는 경우이 특성을 지정 하는 경우 (참조 [얼굴을 감지 하는 방법을](HowtoDetectFacesinImage.md)), 나중에 쿼리할 수 있습니다. 다음 메서드를 [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 앱의 목록을 가져오고 **DetectedFace** 개체와의 목록을 반환 합니다 **[얼굴](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** 개체입니다. **얼굴** 저장소 데이터를 업데이트 된 사각형 좌표를 포함 하 여 직면 하는 사용자 지정 클래스는 다음과 같습니다. 에 대 한 새 값은 계산 **위쪽**, **왼쪽**를 **너비**, 및 **높이**, 새 필드와 **FaceAngle**회전을 지정 합니다.

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

## <a name="display-the-updated-rectangle"></a>업데이트 된 사각형을 표시 합니다.

여기에서 반환 된 따르면 **얼굴** 디스플레이 있는 개체입니다. 다음 줄 [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) 새 사각형을이 데이터에서 렌더링 되는 방법을 보여 줍니다.

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="next-steps"></a>다음 단계

참조 된 [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 회전된 얼굴 사각형의 작업 예제는 GitHub의 앱입니다. 참조를 [Face API HeadPose 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) HeadPose 특성 다양 한 헤드 이동 (nodding, 흔들기) 검색에 실시간으로 추적 하는 앱입니다.