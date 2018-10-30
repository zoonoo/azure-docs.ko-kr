---
title: '자습서: C# 및 .NET SDK를 사용하여 이미지에서 얼굴 감지 및 포착'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Face API를 사용하여 이미지에서 얼굴을 감지하고 포착하는 Windows 앱을 만듭니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: pafarley
ms.openlocfilehash: df5e46270275ca08a8499607fbad4c1161382fab
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956497"
---
# <a name="tutorial-create-a-wpf-app-to-detect-and-frame-faces-in-an-image-using-the-net-sdk"></a>자습서: NET SDK를 사용하여 이미지에서 얼굴을 감지하고 포착하는 WPF 앱 만들기

이 자습서에서는 .NET 클라이언트 라이브러리를 통해 Face 서비스를 사용하는 WPF(Windows Presentation Framework) 응용 프로그램을 만듭니다. 앱은 이미지에서 얼굴을 감지하고 각 얼굴 주위에 프레임을 그린 다음, 상태 표시줄에 얼굴에 대한 설명을 표시합니다. 전체 샘플 코드는 [Windows의 이미지에서 얼굴 감지 및 포착](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample)의 GitHub에서 사용할 수 있습니다.

![사각형으로 포착되어 감지된 얼굴을 보여주는 스크린샷](../Images/getting-started-cs-detected.png)

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> - WPF 응용 프로그램 만들기
> - Face 서비스 클라이언트 라이브러리 설치
> - 클라이언트 라이브러리를 사용하여 이미지에서 얼굴 감지
> - 감지된 각 얼굴 주위에 프레임 그리기
> - 상태 표시줄에 얼굴에 대한 설명 표시

## <a name="prerequisites"></a>필수 조건

- 샘플을 실행하려면 구독 키가 있어야 합니다. [Cognitive Services 시도](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다.
- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전. Visual Studio 2017의 경우 .NET 데스크톱 응용 프로그램 개발 워크로드가 필요합니다. 이 자습서에서는 Visual Studio 2017 Community Edition을 사용합니다.
- [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview) 클라이언트 라이브러리 NuGet 패키지. 패키지를 다운로드할 필요는 없습니다. 설치 지침은 아래에 제공됩니다.

## <a name="create-the-visual-studio-solution"></a>Visual Studio 솔루션 만들기

다음 단계에 따라 Windows WPF 응용 프로그램 프로젝트를 만듭니다.

1. Visual Studio를 열고 **파일** 메뉴에서 **새로 만들기**, **프로젝트**를 차례로 클릭합니다.
   - Visual Studio 2017에서 **설치됨**, **다른 언어**를 차례로 확장합니다. **Visual C#**, **WPF 앱(.NET Framework)** 을 차례로 선택합니다.
   - Visual Studio 2015에서 **설치됨**, **템플릿**을 차례로 확장합니다. **Visual C#**, **WPF 응용 프로그램**을 차례로 선택합니다.
1. 응용 프로그램 이름을 **FaceTutorial**로 지정하고 **확인**을 클릭합니다.

## <a name="install-the-face-service-client-library"></a>Face 서비스 클라이언트 라이브러리 설치

클라이언트 라이브러리를 설치하려면 다음 지침을 따르세요.

1. **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다.
1. **패키지 관리자 콘솔**에서 다음을 붙여 넣은 다음, **Enter**를 누릅니다.

    `Install-Package Microsoft.Azure.CognitiveServices.Vision.Face -Version 2.2.0-preview`

## <a name="add-the-initial-code"></a>초기 코드를 추가합니다.

### <a name="mainwindowxaml"></a>MainWindow.xaml

*MainWindow.xaml*을 열고(팁:  **위/아래 화살표 아이콘**을 사용하여 창 교환) 콘텐츠를 다음 코드로 바꿉니다. 이 xaml 코드는 UI 창을 만드는 데 사용됩니다. 이벤트 처리기, `FacePhoto_MouseMove` 및 `BrowseButton_Click`입니다.

```xml
<Window x:Class="FaceTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="700" Width="960">
    <Grid x:Name="BackPanel">
        <Image x:Name="FacePhoto" Stretch="Uniform" Margin="0,0,0,50" MouseMove="FacePhoto_MouseMove" />
        <DockPanel DockPanel.Dock="Bottom">
            <Button x:Name="BrowseButton" Width="72" Height="20" VerticalAlignment="Bottom" HorizontalAlignment="Left"
                    Content="Browse..."
                    Click="BrowseButton_Click" />
            <StatusBar VerticalAlignment="Bottom">
                <StatusBarItem>
                    <TextBlock Name="faceDescriptionStatusBar" />
                </StatusBarItem>
            </StatusBar>
        </DockPanel>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

*MainWindow.xaml*을 확장한 다음, *MainWindow.xaml.cs*를 열고 콘텐츠를 다음 코드로 바꿉니다. 물결 모양의 빨간색 밑줄은 무시하세요. 첫 번째 빌드 후 사라집니다.

처음 두 줄은 클라이언트 라이브러리 네임스페이스를 가져옵니다. 그 다음, `MainWindow` 생성자에서 Azure 영역을 설정되는 동안 `FaceClient`가 생성되고 구독 키가 전달됩니다. 두 메서드 `BrowseButton_Click`과 `FacePhoto_MouseMove`는 *MainWindow.xaml*에 선언된 이벤트 처리기에 해당합니다.

`BrowseButton_Click`은 `OpenFileDialog`를 만들어, 사용자가 jpg 이미지를 선택할 수 있도록 합니다. 이미지는 주 창에서 읽히고 표시됩니다. `BrowseButton_Click`의 나머지 코드와 `FacePhoto_MouseMove`의 코드는 이후 단계에 삽입됩니다.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace <SubscriptionKey> with your valid subscription key.
        // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // Replace or verify the region.
        //
        // You must use the same region as you used to obtain your subscription
        // keys. For example, if you obtained your subscription keys from the
        // westus region, replace "westcentralus" with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus
        // region, so if you are using a free trial subscription key, you should
        // not need to change this region.
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });

        IList<DetectedFace> faceList;   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();

            if (Uri.IsWellFormedUriString(faceEndpoint, UriKind.Absolute))
            {
                faceClient.Endpoint = faceEndpoint;
            }
            else
            {
                MessageBox.Show(faceEndpoint,
                    "Invalid URI", MessageBoxButton.OK, MessageBoxImage.Error);
                Environment.Exit(0);
            }
        }

        // Displays the image and calls UploadAndDetectFaces.
        private async void BrowseButton_Click(object sender, RoutedEventArgs e)
        {
            // Get the image file to scan from the user.
            var openDlg = new Microsoft.Win32.OpenFileDialog();

            openDlg.Filter = "JPEG Image(*.jpg)|*.jpg";
            bool? result = openDlg.ShowDialog(this);

            // Return if canceled.
            if (!(bool)result)
            {
                return;
            }

            // Display the image file.
            string filePath = openDlg.FileName;

            Uri fileUri = new Uri(filePath);
            BitmapImage bitmapSource = new BitmapImage();

            bitmapSource.BeginInit();
            bitmapSource.CacheOption = BitmapCacheOption.None;
            bitmapSource.UriSource = fileUri;
            bitmapSource.EndInit();

            FacePhoto.Source = bitmapSource;
        }

        // Displays the face description when the mouse is over a face rectangle.
        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
        }
    }
}
```

### <a name="insert-your-subscription-key-and-verify-or-change-the-region"></a>구독 키를 삽입하고 지역을 확인하거나 변경합니다.

- *MainWindow.xaml.cs*에서 다음 줄을 찾아 `<Subscription Key>`를 Face API 구독 키로 바꿉니다.

    ```csharp
    private const string subscriptionKey = "<SubscriptionKey>";
    ```

- *MainWindow.xaml.cs*에서 다음 줄을 찾아 구독 키와 연결된 Azure 지역을 바꾸거나 확인합니다.

    ```csharp
    private const string Endpoint =
        "https://westcentralus.api.cognitive.microsoft.com";
    ```

    위치가 구독 키를 획득한 위치와 동일한지 확인합니다. 예를 들어 **westus** 지역에서 구독 키를 획득한 경우 `Westcentralus`를 `Westus`로 바꿉니다.

    평가판을 사용하여 구독 키를 받은 경우 키의 지역이 **westcentralus**이므로 변경할 필요가 없습니다.

### <a name="test-the-app"></a>앱 테스트

메뉴에서 **시작**을 눌러 앱을 테스트합니다. 창이 열리면 왼쪽 아래에 있는 **찾아보기**를 클릭합니다. **파일 열기** 대화 상자가 나타나고 여기에서 창에 표시된 사진을 찾아보고 선택할 수 있습니다.

![수정되지 않은 얼굴 이미지를 보여주는 스크린샷](../Images/getting-started-cs-ui.png)

## <a name="upload-an-image-to-detect-faces"></a>얼굴을 감지할 이미지 업로드

얼굴을 감지하는 가장 간단한 방법은 로컬 이미지를 업로드하기 위해 API [감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 메서드를 래핑하는 `FaceClient.Face.DetectWithStreamAsync` 메서드를 호출하는 것입니다.

`FacePhoto_MouseMove` 메서드 아래의 `MainWindow` 클래스에 다음 코드를 삽입합니다.

분석할 얼굴 속성 목록이 만들어지고 제출된 이미지 파일은 `Stream`으로 읽혀집니다. 둘 다 `DetectWithStreamAsync` 호출에 전달됩니다.

```csharp
// Uploads the image file and calls DetectWithStreamAsync.
private async Task<IList<DetectedFace>> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IList<FaceAttributeType> faceAttributes =
        new FaceAttributeType[]
        {
            FaceAttributeType.Gender, FaceAttributeType.Age,
            FaceAttributeType.Smile, FaceAttributeType.Emotion,
            FaceAttributeType.Glasses, FaceAttributeType.Hair
        };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            // The second argument specifies to return the faceId, while
            // the third argument specifies not to return face landmarks.
            IList<DetectedFace> faceList =
                await faceClient.Face.DetectWithStreamAsync(
                    imageFileStream, true, false, faceAttributes);
            return faceList;
        }
    }
    // Catch and display Face API errors.
    catch (APIErrorException f)
    {
        MessageBox.Show(f.Message);
        return new List<DetectedFace>();
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new List<DetectedFace>();
    }
}
```

## <a name="draw-rectangles-around-each-face"></a>얼굴 주위에 사각형 그리기

이미지에서 감지된 각 얼굴 주위에 사각형을 그리는 코드를 추가합니다.

*MainWindow.xaml.cs*에서 `async` 한정자를 `BrowseButton_Click` 메서드에 추가합니다.

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

`FacePhoto.Source = bitmapSource` 줄 뒤 `BrowseButton_Click` 메서드의 끝에 다음 코드를 삽입합니다.

감지된 얼굴 목록은 `UploadAndDetectFaces`에 대한 호출로 채워집니다. 그 다음, 각 얼굴 주위에 사각형이 그려지고 수정된 이미지가 주 창에 표시됩니다.

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faceList = await UploadAndDetectFaces(filePath);
Title = String.Format(
    "Detection Finished. {0} face(s) detected", faceList.Count);

if (faceList.Count > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = (dpi > 0) ? 96 / dpi : 1;
    faceDescriptions = new String[faceList.Count];

    for (int i = 0; i < faceList.Count; ++i)
    {
        DetectedFace face = faceList[i];

        // Draw a rectangle on the face.
        drawingContext.DrawRectangle(
            Brushes.Transparent,
            new Pen(Brushes.Red, 2),
            new Rect(
                face.FaceRectangle.Left * resizeFactor,
                face.FaceRectangle.Top * resizeFactor,
                face.FaceRectangle.Width * resizeFactor,
                face.FaceRectangle.Height * resizeFactor
                )
        );

        // Store the face description.
        faceDescriptions[i] = FaceDescription(face);
    }

    drawingContext.Close();

    // Display the image with the rectangle around the face.
    RenderTargetBitmap faceWithRectBitmap = new RenderTargetBitmap(
        (int)(bitmapSource.PixelWidth * resizeFactor),
        (int)(bitmapSource.PixelHeight * resizeFactor),
        96,
        96,
        PixelFormats.Pbgra32);

    faceWithRectBitmap.Render(visual);
    FacePhoto.Source = faceWithRectBitmap;

    // Set the status bar text.
    faceDescriptionStatusBar.Text =
        "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="describe-the-faces-in-the-image"></a>이미지의 얼굴 설명

`UploadAndDetectFaces` 메서드 아래의 `MainWindow` 클래스에 다음 코드를 추가합니다.

메서드는 얼굴 특성을 얼굴을 설명하는 문자열로 변환합니다. 이 문자열은 마우스 포인터가 얼굴 사각형을 가리킬 때 표시됩니다.

```csharp
// Creates a string out of the attributes describing the face.
private string FaceDescription(DetectedFace face)
{
    StringBuilder sb = new StringBuilder();

    sb.Append("Face: ");

    // Add the gender, age, and smile.
    sb.Append(face.FaceAttributes.Gender);
    sb.Append(", ");
    sb.Append(face.FaceAttributes.Age);
    sb.Append(", ");
    sb.Append(String.Format("smile {0:F1}%, ", face.FaceAttributes.Smile * 100));

    // Add the emotions. Display all emotions over 10%.
    sb.Append("Emotion: ");
    Emotion emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger >= 0.1f)
        sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
    if (emotionScores.Contempt >= 0.1f)
        sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
    if (emotionScores.Disgust >= 0.1f)
        sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
    if (emotionScores.Fear >= 0.1f)
        sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f)
        sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral >= 0.1f)
        sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
    if (emotionScores.Sadness >= 0.1f)
        sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
    if (emotionScores.Surprise >= 0.1f)
        sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    IList<HairColor> hairColors = face.FaceAttributes.Hair.HairColor;
    foreach (HairColor hairColor in hairColors)
    {
        if (hairColor.Confidence >= 0.1f)
        {
            sb.Append(hairColor.Color.ToString());
            sb.Append(String.Format(" {0:F1}% ", hairColor.Confidence * 100));
        }
    }

    // Return the built string.
    return sb.ToString();
}
```

## <a name="display-the-face-description"></a>얼굴 설명 표시

`FacePhoto_MouseMove` 메서드를 다음 코드로 바꿉니다.

이 이벤트 처리기는 마우스 포인터가 얼굴 사각형을 가리킬 때 얼굴 설명 문자열을 표시합니다.

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return.
    if (faceList == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faceList.Count; ++i)
    {
        FaceRectangle fr = faceList[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width &&
            mouseXY.Y >= top  && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // String to display when the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text =
            "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="run-the-app"></a>앱 실행

이 응용 프로그램을 실행하고 얼굴이 포함된 이미지를 찾습니다. Face 서비스가 응답할 수 있도록 몇 초 동안 기다립니다. 그러면 이미지의 얼굴에 빨간색 사각형이 표시됩니다. 마우스를 얼굴 사각형 위로 이동하면 얼굴에 대한 설명이 상태 표시줄에 나타납니다.

![사각형으로 포착되어 감지된 얼굴을 보여주는 스크린샷](../Images/getting-started-cs-detected.png)

## <a name="summary"></a>요약

이 자습서에서는 Face 서비스 클라이언트 라이브러리를 사용하는 기본 프로세스를 배웠으며, 이미지에서 얼굴을 표시하고 포착하는 응용 프로그램을 만들었습니다.

## <a name="next-steps"></a>다음 단계

얼굴 이정표를 탐지하고 사용하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [이미지에서 얼굴을 감지하는 방법](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
