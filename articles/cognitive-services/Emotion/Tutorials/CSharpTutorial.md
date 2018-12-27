---
title: '자습서: 이미지에 있는 얼굴에서 감정 인식 - Emotion API, C#'
titlesuffix: Azure Cognitive Services
description: 이미지의 얼굴에 의해 표현된 감정을 인식하려면 기본 Windows 앱을 탐색합니다.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
ms.date: 01/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: da605ec4013fb11606f99f3d9a2dcfcfcab00d3b
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163330"
---
# <a name="tutorial-recognize-emotions-on-a-face-in-an-image"></a>자습서: 이미지에 있는 얼굴에서 감정을 인식합니다.

> [!IMPORTANT]
> Emotion API는 2019년 2월 15일부터 더 이상 사용되지 않습니다. 이제 감정 인식 기능은 [Face API](https://docs.microsoft.com/azure/cognitive-services/face/)의 일부로 일반 공급됩니다. 

이미지의 얼굴에 의해 표현된 감정을 인식하려면 Emotion API를 사용하는 기본 Windows 애플리케이션을 탐색합니다. 아래 예제에서는 이미지 URL 또는 로컬로 저장된 파일을 제출할 수 있습니다. .NET Framework의 일부인 WPF(Windows Presentation Foundation) 및 Emotion API를 사용하여 고유한 Windows용 앱을 빌드하기 위한 템플릿으로 이 오픈 소스 예제를 사용할 수 있습니다.

## <a name="Prerequisites">필수 구성 요소</a>
#### <a name="platform-requirements"></a>플랫폼 요구 사항  
아래 예제는 [Visual Studio 2015 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs)을 사용하여 .NET Framework용으로 개발되었습니다.  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Emotion API를 구독하고 구독 키를 받기  
예제를 만들기 전에 Microsoft Cognitive Services의 일부인 Emotion API를 구독해야 합니다. [구독](https://azure.microsoft.com/try/cognitive-services/)을 참조하세요. 이 자습서에서는 기본 키와 및 보조 키를 둘 다 사용할 수 있습니다. API 키 비밀 및 보안 유지를 위해 모범 사례를 수행해야 합니다.  

#### <a name="get-the-client-library-and-example"></a>클라이언트 라이브러리 및 예제 가져오기  
[SDK](https://www.github.com/microsoft/cognitive-emotion-windows)를 통해 Emotion API 클라이언트 라이브러리를 다운로드할 수 있습니다. 다운로드한 zip 파일은 사용자가 선택한 폴더에 추출되어야 하며, 많은 사용자가 Visual Studio 2015 폴더를 선택합니다.
## <a name="Step1">1단계: 예제 열기</a>
1.  Microsoft Visual Studio 2015를 시작하여 **파일**을 클릭하고 **열기**를 선택한 다음, **프로젝트/솔루션**을 선택합니다.
2.  다운로드한 Emotion API 파일을 저장한 폴더를 찾습니다. **Emotion**, **Windows**, **샘플- WPF** 폴더를 차례로 클릭합니다.
3.  **EmotionAPI-WPF-Samples.sln**이라는 Visual Studio 2015 솔루션(.sln) 파일을 열려면 두 번 클릭합니다. 이렇게 하면 Visual Studio에서 솔루션이 열립니다.

## <a name="Step2">2단계: 예제 빌드</a>
1. **솔루션 탐색기**에서 **참조**를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

  ![NuGet 패키지 관리자 열기](../Images/EmotionNuget.png)

2.  **NuGet 패키지 관리자 창**이 열립니다. 먼저 왼쪽 상단 구석에서 **찾아보기**를 선택한 다음, 검색 상자에 "Newtonsoft.Json"을 입력하고 **Newtonsoft.Json** 패키지를 선택하고 **설치**를 클릭합니다.  

  ![NuGet 패키지 찾기](../Images/EmotionNugetBrowse.png)  

3.  Ctrl+Shift+B를 누르거나, 리본 메뉴에서 **빌드**를 클릭한 다음, **솔루션 빌드**를 선택합니다.

## <a name="Step3">3단계: 예제 실행</a>
1.  빌드가 완료된 후 **F5** 키를 누르거나, 리본 메뉴에서 **시작**을 클릭하여 예제를 실행합니다.
2.  "**시작하려면 여기에 구독 키를 붙여넣으세요**"라는 **텍스트 상자**가 있는 Emotion API 창을 찾습니다. 아래 스크린샷에 표시된 것처럼 구독 키를 텍스트 상자에 붙여넣습니다. “키 저장” 단추를 클릭하여 PC 또는 노트북에 구독 키를 저장하도록 선택할 수 있습니다. 시스템에서 구독 키를 삭제하려면 “키 삭제”를 클릭하여 PC 또는 노트북에서 제거합니다.

  ![Emotion 기능 인터페이스](../Images/EmotionKey.png)

3.  "**시나리오 선택**" 아래에서 "**스트림을 사용하여 감정 감지**" 또는 "**URL을 사용하여 감정 감지**"의 두 가지 시나리오 중 하나를 사용하려면 클릭한 다음, 화면의 지침을 따릅니다. Microsoft는 업로드되는 이미지를 수신하며, 해당 이미지를 사용하여 Emotion API 및 관련 서비스를 개선할 수 있습니다. 이미지를 제출하면 [개발자 준수 사항](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)을 따랐음을 확인하는 것입니다.
4.  이 예제 애플리케이션에서 사용할 예제 이미지가 있습니다. **데이터 폴더** 아래 [Face API GitHub 리포지토리](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data)에서 이러한 이미지를 찾을 수 있습니다. 이러한 이미지의 사용은 이 예제를 테스트하기 위해 사용하는 것은 문제 없지만 다시 게시하는 것은 안 된다는 공정한 사용 계약에 따라 라이선스됐습니다.

## <a name="Review">검토 및 학습</a>
이제 실행 중인 응용 프로그램이 있으므로 이 예제 앱이 Microsoft Cognitive Services와 어떻게 통합되는지를 살펴보겠습니다. 이렇게 하면 계속해서 이 앱에 빌드하거나 Microsoft Emotion API를 사용하여 고유한 앱을 개발하는 작업이 더 쉬워집니다.

이 예제 앱은 Microsoft Emotion API용 씬 C# 클라이언트 래퍼인 Emotion API 클라이언트 라이브러리를 사용합니다. 위에서 설명한 대로 예제 앱을 빌드한 경우 NuGet 패키지에서 클라이언트 라이브러리를 가져왔습니다. **Emotion**, **Windows**, **클라이언트 라이브러리** 아래의 “[클라이언트 라이브러리](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)” 폴더에서, 위의 [필수 조건](#Prerequisites)에 언급된 다운로드된 파일 리포지토리의 일부인 클라이언트 라이브러리 소스 코드를 검토할 수 있습니다.

**솔루션 탐색기**에서 클라이언트 라이브러리 코드를 사용하는 방법을 찾을 수 있습니다. 즉, **EmotionAPI-WPF_Samples** 아래에서 **DetectEmotionUsingStreamPage.xaml**을 확장하여 로컬로 저장된 파일을 찾아보는 데 사용되는 **DetectEmotionUsingStreamPage.xaml.cs**를 찾거나, **DetectEmotionUsingURLPage.xaml**을 확장하여 이미지 URL을 업로드하는 경우 사용되는 **DetectEmotionUsingURLPage.xaml.cs**를 찾습니다. .xaml.cs 파일을 두 번 클릭하여 Visual Studio의 새 창에서 엽니다.

Emotion 클라이언트 라이브러리가 예제 앱에서 사용되는 방법을 검토하려면 **DetectEmotionUsingStreamPage.xaml.cs** 및 **DetectEmotionUsingURLPage.xaml.cs**의 두 코드 조각을 살펴보겠습니다. 각 파일에는 아래에 재현된 코드 조각을 찾는 데 도움이 되도록 “KEY SAMPLE CODE STARTS HERE” 및 “KEY SAMPLE CODE ENDS HERE”를 표시하는 코드 주석이 포함되어 있습니다.

Emotion API는 이미지 URL 또는 이진 이미지 데이터(8진수 스트림 형식)를 입력으로 사용할 수 있습니다. 아래의 두 옵션을 검토합니다. 두 사례에서 먼저 Emotion 클라이언트 라이브러리를 사용할 수 있게 해주는 using 지시문을 찾습니다.
```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Use the following namespace for EmotionServiceClient
            // -----------------------------------------------------------------------
            using Microsoft.ProjectOxford.Emotion;
            using Microsoft.ProjectOxford.Emotion.Contract;
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs

이 코드 조각은 등록 키 및 사진 URL을 Emotion API 서비스에 제출하려면 클라이언트 라이브러리를 사용하는 방법을 보여줍니다.

```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            window.Log("EmotionServiceClient is created");

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                //
                // Detect the emotions in the URL
                //
                Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
                return emotionResult;
            }
            catch (Exception exception)
            {
                window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs

구독 키와 로컬로 저장된 이미지를 Emotion API에 제출하는 방법이 아래에 표시됩니다.


```csharp


            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                Emotion[] emotionResult;
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    //
                    // Detect the emotions in the URL
                    //
                    emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
                    return emotionResult;
                }
            }
            catch (Exception exception)
            {
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
