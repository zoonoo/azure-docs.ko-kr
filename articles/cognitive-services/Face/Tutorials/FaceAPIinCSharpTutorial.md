---
title: Face API C# 자습서 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services Emotion API를 사용하여 얼굴 프레이밍을 통해 이미지에서 얼굴을 감지하는 간단한 Windows 앱을 만듭니다.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ac4f247eb38cafc5582c1ffbb3752e049f0305ba
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374238"
---
# <a name="getting-started-with-face-api-in-c35-tutorial"></a>C&#35;에서 Face API 시작 자습서

이 자습서에서는 Face API를 사용하는 WPF Windows 응용 프로그램을 만듭니다. 응용 프로그램은 이미지에서 얼굴을 감지하고 각 얼굴 주위에 프레임을 그린 다음, 상태 표시줄에 얼굴에 대한 설명을 표시합니다.

![GettingStartCSharpScreenshot](../Images/getting-started-cs-detected.png)

## <a name="Preparation"></a>준비

자습서를 사용하려면 다음 필수 조건이 필요합니다.

- Visual Studio 2015 이상이 설치되어 있는지 확인합니다.

## <a name="step1"></a>1단계: Face API 구독 및 구독 키 가져오기

Face API를 사용하려면 먼저 Microsoft Cognitive Services 포털에서 등록하여 Face API를 구독해야 합니다. [구독](https://azure.microsoft.com/try/cognitive-services/)을 참조하세요. 이 자습서에서는 기본 구독 키 또는 보조 구독 키를 사용할 수 있습니다.

## <a name="step2"></a>2단계: Visual Studio 솔루션 만들기

이 단계에서는 Windows WPF 응용 프로그램 프로젝트를 만들어 이미지를 선택하고 표시할 기본 응용 프로그램을 만듭니다. 다음 지침을 따릅니다.

1. Visual Studio를 엽니다.
1. **파일** 메뉴에서 **새로 만들기**, **프로젝트**를 차례로 클릭합니다.
1. **새 프로젝트** 대화 상자에서 응용 프로그램의 WPF를 선택합니다.

   Visual Studio 2015에서는 **설치됨** &gt; **템플릿** &gt; **Visual C#** &gt; **Windows** &gt; **클래식 데스크톱** &gt;을 확장하고 **WPF 응용 프로그램**을 선택합니다.

   Visual Studio 2017에서는 **설치됨** &gt; **템플릿** &gt; **Visual C#** &gt; **Windows 클래식 데스크톱** &gt;을 확장하고 **WPF 앱(.NET Framework)** 을 선택합니다.
1. 응용 프로그램 이름을 **FaceTutorial**로 지정하고 **확인**을 클릭합니다.

   ![WPF 응용 프로그램이 선택된 새 프로젝트 대화 상자](../Images/vs2017-new-project.png)

1. **솔루션 탐색기**를 찾아서 프로젝트(이 경우 **FaceTutorial**)를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.
1. **NuGet 패키지 관리자** 창에서 패키지 원본으로 **nuget.org**를 선택합니다.
1. **Newtonsoft.Json**을 검색하고 **설치**를 클릭합니다. Visual Studio 2017에서는 먼저 **찾아보기** 탭을 클릭한 다음, **검색**을 클릭합니다.

   ![GettingStartCSharpPackageManager](../Images/install-nsoft-json.png)

## <a name="step3"></a>3단계: Face API 클라이언트 라이브러리 구성

Face API는 HTTPS REST 요청을 통해 호출할 수 있는 클라우드 API입니다. .NET 응용 프로그램에서 사용하기 쉽도록 .NET 클라이언트 라이브러리는 Face API REST 요청을 캡슐화합니다. 이 예제에서는 클라이언트 라이브러리를 사용하여 작업을 간소화합니다.

클라이언트 라이브러리를 구성하려면 다음 지침을 따르세요.

1. **솔루션 탐색기**에서 프로젝트(이 경우 **FaceTutorial**)를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.
1. **NuGet 패키지 관리자** 창에서 패키지 원본으로 **nuget.org**를 선택합니다.
1. **Microsoft.ProjectOxford.Face**를 검색한 다음, **설치**를 클릭합니다. Visual Studio 2017에서는 먼저 **찾아보기** 탭을 클릭한 다음, **검색**을 클릭합니다.

   ![GettingStartCSharpPackageManagerSDK](../Images/install-project-oxford-face.png)

1. **솔루션 탐색기**에서 프로젝트 참조를 확인합니다. 설치에 성공하면 **Microsoft.ProjectOxford.Common**, **Microsoft.ProjectOxford.Face** 및 **Newtonsoft.Json** 참조가 자동으로 추가됩니다.

   ![GetStartedCSharp-CheckInstallation.png](../Images/GetStartedCSharp-CheckInstallation.png)

## <a name="step3"></a>4단계: 초기 코드 복사 및 붙여넣기

1. MainWindow.xaml을 열고 기존 코드를 다음 코드로 바꿔 창 UI를 만듭니다.

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

1. MainWindow.xaml.cs를 열고 기존 코드를 다음 코드로 바꿉니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows;
    using System.Windows.Input;
    using System.Windows.Media;
    using System.Windows.Media.Imaging;
    using Microsoft.ProjectOxford.Common.Contract;
    using Microsoft.ProjectOxford.Face;
    using Microsoft.ProjectOxford.Face.Contract;

    namespace FaceTutorial
    {
        public partial class MainWindow : Window
        {
            // Replace the first parameter with your valid subscription key.
            //
            // Replace or verify the region in the second parameter.
            //
            // You must use the same region in your REST API call as you used to obtain your subscription keys.
            // For example, if you obtained your subscription keys from the westus region, replace
            // "westcentralus" in the URI below with "westus".
            //
            // NOTE: Free trial subscription keys are generated in the westcentralus region, so if you are using
            // a free trial subscription key, you should not need to change this region.
            private readonly IFaceServiceClient faceServiceClient =
                new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");

            Face[] faces;                   // The list of detected faces.
            String[] faceDescriptions;      // The list of descriptions for the detected faces.
            double resizeFactor;            // The resize factor for the displayed image.
            
            public MainWindow()
            {
                InitializeComponent();
            }
            
            // Displays the image and calls Detect Faces.

            private void BrowseButton_Click(object sender, RoutedEventArgs e)
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

1. 구독 키를 삽입하고 지역을 확인합니다.

    MainWindow.xaml.cs 파일에서 다음 줄(줄 28 및 29)을 찾습니다.

    ```csharp
    private readonly IFaceServiceClient faceServiceClient =
            new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");
    ```

    첫 번째 매개 변수의 `<Subscription Key>`를 1단계의 Face API 구독 키로 바꿉니다.

    또한 두 번째 매개 변수를 검사하여 구독 키를 획득한 위치를 사용 중인지 확인합니다. 예를 들어 westus 지역에서 구독 키를 획득한 경우 URI의 “**westcentralus**”를 “**westus**”로 바꿉니다.

    평가판을 사용하여 구독 키를 받은 경우 키의 지역이 **westcentralus**이므로 변경할 필요가 없습니다.

이제 앱이 사진을 찾아서 창에 표시할 수 있습니다.

![GettingStartCSharpUI](../Images/getting-started-cs-ui.png)

## <a name="step4"></a>5단계: 이미지를 업로드하여 얼굴 감지

얼굴을 감지하는 가장 간단한 방법은 이미지 파일을 직접 업로드하여 [Face - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API를 호출하는 것입니다.
클라이언트 라이브러리를 사용하는 경우 FaceServiceClient의 비동기 메서드 DetectAsync를 사용하여 이 작업을 수행할 수 있습니다.
반환된 각 얼굴에는 해당 위치를 표시하는 사각형이 일련의 선택적 얼굴 특성과 함께 포함되어 있습니다.

**MainWindow** 클래스에 다음 코드를 삽입합니다.

```csharp
// Uploads the image file and calls Detect Faces.

private async Task<Face[]> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IEnumerable<FaceAttributeType> faceAttributes =
        new FaceAttributeType[] { FaceAttributeType.Gender, FaceAttributeType.Age, FaceAttributeType.Smile, FaceAttributeType.Emotion, FaceAttributeType.Glasses, FaceAttributeType.Hair };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            Face[] faces = await faceServiceClient.DetectAsync(imageFileStream, returnFaceId: true, returnFaceLandmarks:false, returnFaceAttributes: faceAttributes);
            return faces;
        }
    }
    // Catch and display Face API errors.
    catch (FaceAPIException f)
    {
        MessageBox.Show(f.ErrorMessage, f.ErrorCode);
        return new Face[0];
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new Face[0];
    }
}
```

## <a name="step5"></a>6단계: 이미지에서 얼굴 표시

이 단계에서는 이전 단계를 모두 결합하고 이미지에서 감지된 얼굴을 표시합니다.

**MainWindow.xaml.cs**에서 **BrowseButton_Click** 메서드에 'async' 한정자를 추가합니다.

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

**BrowseButton_Click** 이벤트 처리기의 끝에 다음 코드를 삽입합니다.

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faces = await UploadAndDetectFaces(filePath);
Title = String.Format("Detection Finished. {0} face(s) detected", faces.Length);

if (faces.Length > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = 96 / dpi;
    faceDescriptions = new String[faces.Length];

    for (int i = 0; i < faces.Length; ++i)
    {
        Face face = faces[i];

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
    faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="step6"></a>7단계: 이미지의 얼굴 설명

이 단계에서는 Face 속성을 검사하고 얼굴을 설명하는 문자열을 생성합니다. 이 문자열은 마우스 포인터가 얼굴 사각형을 가리킬 때 표시됩니다.

**MainWindow** 클래스에 이 메서드를 추가하여 얼굴 세부 정보를 문자열로 변환합니다.

```csharp
// Returns a string that describes the given face.

private string FaceDescription(Face face)
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
    EmotionScores emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger     >= 0.1f) sb.Append(String.Format("anger {0:F1}%, ",     emotionScores.Anger * 100));
    if (emotionScores.Contempt  >= 0.1f) sb.Append(String.Format("contempt {0:F1}%, ",  emotionScores.Contempt * 100));
    if (emotionScores.Disgust   >= 0.1f) sb.Append(String.Format("disgust {0:F1}%, ",   emotionScores.Disgust * 100));
    if (emotionScores.Fear      >= 0.1f) sb.Append(String.Format("fear {0:F1}%, ",      emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f) sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral   >= 0.1f) sb.Append(String.Format("neutral {0:F1}%, ",   emotionScores.Neutral * 100));
    if (emotionScores.Sadness   >= 0.1f) sb.Append(String.Format("sadness {0:F1}%, ",   emotionScores.Sadness * 100));
    if (emotionScores.Surprise  >= 0.1f) sb.Append(String.Format("surprise {0:F1}%, ",  emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    HairColor[] hairColors = face.FaceAttributes.Hair.HairColor;
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

## <a name="step6"></a>8단계: 얼굴 설명 표시

**FacePhoto_MouseMove** 메서드를 다음 코드로 바꿉니다.

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return from this method.
    if (faces == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faces.Length; ++i)
    {
        FaceRectangle fr = faces[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description for this face if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width && mouseXY.Y >= top && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // If the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
}
```

이 응용 프로그램을 실행하고 얼굴이 포함된 이미지를 찾습니다. 클라우드 API가 응답할 수 있도록 몇 초 동안 기다립니다. 그러면 이미지의 얼굴에 빨간색 사각형이 표시됩니다. 마우스를 얼굴 사각형 위로 이동하면 얼굴에 대한 설명이 상태 표시줄에 나타납니다.

![GettingStartCSharpScreenshot](../Images/getting-started-cs-detected.png)

## <a name="summary"></a> 요약

이 자습서에서는 Face API를 사용하는 기본 프로세스를 배웠으며, 이미지에 얼굴 표식을 표시하는 응용 프로그램을 만들었습니다. Face API 세부 정보에 대한 자세한 내용은 방법 및 [API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 참조하세요.

## <a name="fullsource"></a> 전체 소스

여기에는 WPF Windows 응용 프로그램의 전체 소스가 나와 있습니다.

MainWindow.xaml:

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

MainWindow.xaml.cs:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using Microsoft.ProjectOxford.Common.Contract;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace the first parameter with your valid subscription key.
        //
        // Replace or verify the region in the second parameter.
        //
        // You must use the same region in your REST API call as you used to obtain your subscription keys.
        // For example, if you obtained your subscription keys from the westus region, replace
        // "westcentralus" in the URI below with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus region, so if you are using
        // a free trial subscription key, you should not need to change this region.
        private readonly IFaceServiceClient faceServiceClient =
            new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");

        Face[] faces;                   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();
        }

        // Displays the image and calls Detect Faces.

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

            // Detect any faces in the image.
            Title = "Detecting...";
            faces = await UploadAndDetectFaces(filePath);
            Title = String.Format("Detection Finished. {0} face(s) detected", faces.Length);

            if (faces.Length > 0)
            {
                // Prepare to draw rectangles around the faces.
                DrawingVisual visual = new DrawingVisual();
                DrawingContext drawingContext = visual.RenderOpen();
                drawingContext.DrawImage(bitmapSource,
                    new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
                double dpi = bitmapSource.DpiX;
                resizeFactor = 96 / dpi;
                faceDescriptions = new String[faces.Length];

                for (int i = 0; i < faces.Length; ++i)
                {
                    Face face = faces[i];

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
                faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
            }
        }

        // Displays the face description when the mouse is over a face rectangle.

        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
            // If the REST call has not completed, return from this method.
            if (faces == null)
                return;

            // Find the mouse position relative to the image.
            Point mouseXY = e.GetPosition(FacePhoto);

            ImageSource imageSource = FacePhoto.Source;
            BitmapSource bitmapSource = (BitmapSource)imageSource;

            // Scale adjustment between the actual size and displayed size.
            var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

            // Check if this mouse position is over a face rectangle.
            bool mouseOverFace = false;

            for (int i = 0; i < faces.Length; ++i)
            {
                FaceRectangle fr = faces[i].FaceRectangle;
                double left = fr.Left * scale;
                double top = fr.Top * scale;
                double width = fr.Width * scale;
                double height = fr.Height * scale;

                // Display the face description for this face if the mouse is over this face rectangle.
                if (mouseXY.X >= left && mouseXY.X <= left + width && mouseXY.Y >= top && mouseXY.Y <= top + height)
                {
                    faceDescriptionStatusBar.Text = faceDescriptions[i];
                    mouseOverFace = true;
                    break;
                }
            }

            // If the mouse is not over a face rectangle.
            if (!mouseOverFace)
                faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
        }

        // Uploads the image file and calls Detect Faces.

        private async Task<Face[]> UploadAndDetectFaces(string imageFilePath)
        {
            // The list of Face attributes to return.
            IEnumerable<FaceAttributeType> faceAttributes =
                new FaceAttributeType[] { FaceAttributeType.Gender, FaceAttributeType.Age, FaceAttributeType.Smile, FaceAttributeType.Emotion, FaceAttributeType.Glasses, FaceAttributeType.Hair };

            // Call the Face API.
            try
            {
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    Face[] faces = await faceServiceClient.DetectAsync(imageFileStream, returnFaceId: true, returnFaceLandmarks: false, returnFaceAttributes: faceAttributes);
                    return faces;
                }
            }
            // Catch and display Face API errors.
            catch (FaceAPIException f)
            {
                MessageBox.Show(f.ErrorMessage, f.ErrorCode);
                return new Face[0];
            }
            // Catch and display all other errors.
            catch (Exception e)
            {
                MessageBox.Show(e.Message, "Error");
                return new Face[0];
            }
        }

        // Returns a string that describes the given face.

        private string FaceDescription(Face face)
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
            EmotionScores emotionScores = face.FaceAttributes.Emotion;
            if (emotionScores.Anger >= 0.1f) sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
            if (emotionScores.Contempt >= 0.1f) sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
            if (emotionScores.Disgust >= 0.1f) sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
            if (emotionScores.Fear >= 0.1f) sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
            if (emotionScores.Happiness >= 0.1f) sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
            if (emotionScores.Neutral >= 0.1f) sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
            if (emotionScores.Sadness >= 0.1f) sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
            if (emotionScores.Surprise >= 0.1f) sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

            // Add glasses.
            sb.Append(face.FaceAttributes.Glasses);
            sb.Append(", ");

            // Add hair.
            sb.Append("Hair: ");

            // Display baldness confidence if over 1%.
            if (face.FaceAttributes.Hair.Bald >= 0.01f)
                sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

            // Display all hair color attributes over 10%.
            HairColor[] hairColors = face.FaceAttributes.Hair.HairColor;
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
    }
}
```

## <a name="related"></a> 다음 단계

- [Android용 Java에서 Face API 시작](FaceAPIinJavaForAndroidTutorial.md)
- [Python에서 Face API 시작](FaceAPIinPythonTutorial.md)
