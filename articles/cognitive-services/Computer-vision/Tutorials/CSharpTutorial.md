---
title: Computer Vision API C# 자습서 | Microsoft Docs
description: Microsoft Cognitive Services의 Computer Vision API를 사용하는 기본 Windows 앱을 탐색합니다. OCR를 수행하고, 썸네일을 만들고, 이미지의 시각적 기능으로 작업합니다.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/22/2017
ms.author: kefre
ms.openlocfilehash: f2aeb1734f8858ed8b80e5cdf48ef7e558703919
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374751"
---
# <a name="computer-vision-api-c35-tutorial"></a>Computer Vision API C&#35; 자습서

Computer Vision API를 사용하여 OCR(광학 문자 인식)을 수행하고, 는 기본 Windows 응용 프로그램을 탐색하여 광학 문자 인식(OCR)을 수행하고, 스마트하게 잘리는 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 기능을 감지, 분류, 태그 지정 및 설명하는 기본 Windows 응용 프로그램을 탐색합니다. 아래 예제에서는 이미지 URL 또는 로컬로 저장된 파일을 제출할 수 있습니다. .NET Framework의 일부인 WPF(Windows Presentation Foundation) 및 Vision API를 사용하여 고유한 Windows용 앱을 빌드하기 위한 템플릿으로 이 오픈 소스 예제를 사용할 수 있습니다.

### <a name="prerequisites"></a>필수 조건

#### <a name="platform-requirements"></a>플랫폼 요구 사항

아래 예제는 [Visual Studio 2015 Community Edition](https://www.visualstudio.com/downloads/)을 사용하여 .NET Framework용으로 개발되었습니다.

#### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Computer Vision API를 구독하고 구독 키를 받습니다. 

예제를 만들기 전에 Microsoft Cognitive Services(이전의 Project Oxford)의 일부인 Computer Vision API를 구독해야 합니다. 구독 및 키 관리 세부 정보는 [구독](https://azure.microsoft.com/try/cognitive-services/)을 참조하세요. 이 자습서에서는 기본 키와 및 보조 키를 둘 다 사용할 수 있습니다. 

> [!NOTE]
> 자습서는 **westcentralus** 지역의 구독 키를 사용하도록 작성되었습니다. Computer Vision 평가판에 생성된 구독 키는 **westcentralus** 지역을 사용하므로 올바르게 작동합니다. [https://azure.microsoft.com/](https://azure.microsoft.com/)을 통해 Azure 계정을 사용하여 구독 키를 생성한 경우 **westcentralus** 지역을 지정해야 합니다. **westcentralus** 지역 외부에서 생성된 키는 작동하지 않습니다.

#### <a name="get-the-client-library-and-example"></a>클라이언트 라이브러리 및 예제 가져오기

[SDK](https://www.github.com/microsoft/cognitive-vision-windows)를 통해 Computer Vision API 클라이언트 라이브러리 및 예제 응용 프로그램을 컴퓨터에 복제할 수 있습니다. ZIP으로 다운로드하면 안 됩니다.

### <a name="Step1">1단계: 예제 설치</a>

GitHub 데스크톱에서 Sample-WPF\VisionAPI-WPF-Samples.sln을 엽니다.

### <a name="Step2">2단계: 예제 빌드</a>

* Ctrl+Shift+B를 누르거나, 리본 메뉴에서 빌드를 클릭하고 솔루션 빌드를 선택합니다.

### <a name="Step3">3단계: 예제 실행</a>

1. 빌드가 완료된 후 **F5** 키를 누르거나, 리본 메뉴에서 **시작**을 클릭하여 예제를 실행합니다.
2. “시작하려면 여기에 구독 키를 붙여넣으세요.” 텍스트 편집 상자가 있는 Computer Vision API 사용자 인터페이스 창을 찾습니다.
“키 저장” 단추를 클릭하여 PC 또는 노트북에 구독 키를 유지하도록 선택할 수 있습니다. 시스템에서 구독 키를 삭제하려면 “키 삭제”를 클릭하여 PC 또는 노트북에서 제거합니다.

    ![Vision 구독 키](../Images/Vision_UI_Subscription.PNG)

3. “시나리오 선택” 아래에서 6가지 시나리오 중 하나를 클릭하여 사용하고 화면에 표시되는 지침을 따르세요. Microsoft는 업로드되는 이미지를 수신하며, 해당 이미지를 사용하여 Computer Vision API 및 관련 서비스를 개선할 수 있습니다. 이미지를 제출하면 [개발자 준수 사항](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)을 따랐음을 확인하는 것입니다.

    ![이미지 분석 인터페이스](../Images/Analyze_Image_Example.PNG)

4. 이 예제 응용 프로그램에서 사용할 예제 이미지가 있습니다. Face API Windows Github 리포지토리의 [데이터 폴더](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data)에서 이러한 이미지를 찾을 수 있습니다. 이러한 이미지는 계약 [LICENSE-IMAGE](https://github.com/Microsoft/Cognitive-Face-Windows/blob/master/LICENSE-IMAGE.md)에 의거하여 사용이 허가되었습니다.

### <a name="Review">검토 및 학습</a>

이제 실행 중인 응용 프로그램이 있으므로 이 예제 앱이 Cognitive Services 기술과 어떻게 통합되는지를 살펴보겠습니다. 이렇게 하면 계속해서 이 앱에 빌드하거나 Microsoft Computer Vision API를 사용하여 고유한 앱을 개발하는 작업이 더 쉬워집니다.

이 예제 앱은 Microsoft Computer Vision API용 씬 C# 클라이언트 래퍼인 Computer Vision API 클라이언트 라이브러리를 사용합니다. 위에서 설명한 대로 예제 앱을 빌드한 경우 NuGet 패키지에서 클라이언트 라이브러리를 가져왔습니다. **Vision**, **Windows**, **클라이언트 라이브러리** 아래의 “ **클라이언트 라이브러리**” 폴더에서, 위의 필수 조건에 언급된 다운로드된 파일 리포지토리의 일부인 클라이언트 라이브러리 소스 코드를 검토할 수 있습니다.

솔루션 탐색기에서 클라이언트 라이브러리 코드를 사용하는 방법을 확인할 수도 있습니다. **VisionAPI-WPF_Samples** 아래의 **AnalyzePage.xaml**을 확장하여 이미지를 이미지 분석 끝점에 제출하는 데 사용되는 **AnalyzePage.xaml.cs**를 찾습니다. .xaml.cs 파일을 두 번 클릭하여 Visual Studio의 새 창에서 엽니다.

예제 앱에서 Vision 클라이언트 라이브러리가 어떻게 사용되는지를 검토하면서 **AnalyzePage.xaml.cs**의 두 코드 조각을 살펴보겠습니다. 이 파일에는 아래에 재현된 코드 조각을 찾는 데 도움이 되도록 “KEY SAMPLE CODE STARTS HERE” 및 “KEY SAMPLE CODE ENDS HERE”를 표시하는 코드 주석이 포함되어 있습니다.

분석 끝점은 이미지 URL 또는 이진 이미지 데이터(8진수 스트림 형식)를 입력으로 사용할 수 있습니다. 먼저, Vision 클라이언트 라이브러리를 사용할 수 있게 해주는 using 지시문을 찾습니다.

```
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE STARTS HERE
                // Use the following namespace for VisionServiceClient 
                // ---------------------------------------------------------------------- 
                using Microsoft.ProjectOxford.Vision; 
                using Microsoft.ProjectOxford.Vision.Contract; 
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE ENDS HERE 
                // ----------------------------------------------------------------------

```
**UploadAndAnalyzeImage(…)** 이 코드 조각은 클라이언트 라이브러리를 사용하여 구독 키와 로컬로 저장된 이미지를 Computer Vision API 서비스의 분석 끝점에 제출하는 방법을 보여 줍니다.

```
    private async Task<AnalysisResult> UploadAndAnalyzeImage(string imageFilePath)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------  
        //
        // Create Project Oxford Computer Vision API Service client
        //
        VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features
            //
            Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
         VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
            AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageFileStream, visualFeatures);
            return analysisResult;
        }
    
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
        }
```
**AnalyzeUrl(…)** 이 코드 조각은 클라이언트 라이브러리를 사용하여 구독 키와 사진 URL을 Computer Vision API 서비스의 분석 끝점에 제출하는 방법을 보여 줍니다.

```
    private async Task<AnalysisResult> AnalyzeUrl(string imageUrl)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------
    
        //
        // Create Project Oxford Computer Vision API Service client
        //
     VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        //
        // Analyze the url for all visual features
        //
        Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
        VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
        AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageUrl, visualFeatures);
     return analysisResult;
    }
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
```
**기타 페이지 및 끝점** Computer Vision API 서비스에서 노출하는 다른 끝점을 조작하는 방법은 샘플의 기타 페이지에서 확인할 수 있습니다. 예를 들어 OCR 끝점은 OCRPage.xaml.cs에 포함된 코드의 일부로 표시됩니다. 

### <a name="Related">관련 항목</a>
 * [Face API 시작](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
 
 


