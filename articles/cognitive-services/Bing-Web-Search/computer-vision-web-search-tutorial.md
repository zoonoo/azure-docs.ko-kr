---
title: Visual Search 모바일 앱 자습서
description: Cognitive Services Computer Vision API, Bing Web Search API 및 Xamarin.Forms 플랫폼 간 프레임워크를 사용하여 시각적 검색을 구현하는 오픈 소스 C# 응용 프로그램입니다.
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374247"
---
# <a name="visual-search-mobile-app-tutorial"></a>Visual Search 모바일 앱 자습서

## <a name="introduction"></a>소개  
이 자습서에서는 [Computer Vision API](https://azure.microsoft.com/services/cognitive-services/computer-vision/) 및 [Bing Web Search API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 엔드포인트를 탐색하고 [Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/)를 사용하여 기본 시각적 검색 응용 프로그램을 빌드하는 데 이들을 사용할 수 있는 방법을 보여줍니다.  전반적으로 이 자습서에서 다루는 항목은 다음과 같습니다. 

> [!div class="checklist"]
> * Xamarin.Forms 응용 프로그램을 개발하려면 Visual Studio 설정
> * [Xamarin Media Plugin](https://github.com/jamesmontemagno/MediaPlugin)을 사용하여 이미지 데이터 캡처 및 가져오기
> * Computer Vision API를 사용하여 이미지에서 텍스트 구문 분석
> * Bing Web Search API에 검색 요청 전송
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)을 사용하여(LINQ 및 모델 개체 역직렬화를 사용하여) 두 API에서 JSON 응답 구문 분석
> * 시각적 검색에 대한 플랫폼 간 Xamarin.Forms 사용자 인터페이스 만들기 

## <a name="prerequisites"></a>필수 조건

이 예제는 [Visual Studio 2017](https://www.visualstudio.com/downloads/)로 Xamarin.Forms를 사용하여 개발됐습니다. 커뮤니티 버전의 Visual Studio 2017을 사용할 수 있습니다. Visual Studio로 Xamarin을 사용하는 방법에 대한 자세한 내용은 [Xamarin 설명서](https://developer.xamarin.com/guides/cross-platform/getting_started/)를 참조합니다.

이 샘플에서는 다음과 같은 NuGet 패키지를 활용합니다.

> [!div class="checklist"]
> * [Xamarin Media Plugin](https://github.com/jamesmontemagno/MediaPlugin)
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

응용 프로그램은 다음 Cognitive Services API를 사용합니다.

> [!div class="checklist"]
> * [Bing Web Search API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 
> * [Computer Vision API](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

이러한 API에 대한 30일 평가판 키를 얻으려면 [Cognitive Services 체험하기](https://azure.microsoft.com/try/cognitive-services/)를 참조합니다. 상업적 용도로 키를 얻는 방법에 대한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/calculator/)을 참조합니다.

## <a name="setup-for-development"></a>개발을 위한 설정  

### <a name="installing-xamarin-on-windows"></a>Windows에 Xamarin 설치
Visual Studio 2017의 모든 버전을 설치한 상태에서 Visual Studio 설치 관리자를 열고 Visual Studio 설치와 연결된 햄버거 메뉴 및 **수정**을 차례로 선택합니다.  

![Visual Studio 설치 관리자](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

아래로 스크롤하여 모바일 및 게임으로 이동하고 이미 활성화되어 있지 않으면 **.NET을 사용하여 모바일 개발**을 활성화합니다.

![선택된 Xamarin.Forms를 사용한 Visual Studio 설치 관리자](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

마지막으로, 창의 오른쪽 아래 구석에서 **수정**을 클릭하고 설치할 Xamarin을 대기합니다.

### <a name="installing-xamarin-on-macos"></a>macOS에 Xamarin 설치
Xamarin은 Mac용 Visual Studio와 함께 사전에 패키징되어 제공됩니다. 설치가 필요하지 않습니다.

## <a name="building-and-running-the-app"></a>앱 빌드 및 실행

Visual Search 응용 프로그램에 대한 Visual Studio 솔루션 파일 *(.sln)* 은 [Cognitive Services를 사용한 Visual Search 앱](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/)에서 다운로드할 수 있습니다. 웹 브라우저를 사용하여 ZIP 보관 파일을 다운로드하여 GitHub의 워크스테이션에 복제하거나 Visual Studio를 사용하여 다운로드할 수 있습니다.

샘플 작업을 시작하려면 Visual Studio에서 `VisualSearchApp.sln`을 엽니다.  필수 구성 요소를 초기화하는 데는 시간이 걸릴 수 있습니다.

응용 프로그램에는 **Json.NET** 및 **Xamarin Media Plugin**의 두 개의 타사 라이브러리가 필요합니다. NuGet 패키지 관리자를 사용하여 이러한 라이브러리를 Visual Studio에 바로 설치할 수 있습니다. **도구 > NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리**를 선택하거나 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **NuGet 패키지 관리**를 선택합니다.

NuGet 창에서 **Xamarin Media plugin**(Xam.Plugin.Media) 버전 2.6.2 및 **Json.NET**(Newtonsoft.Json) 버전 10.0.3을 검색하고 설치합니다. 설치할 때 모든 프로젝트를 선택해야 합니다.

사용 가능한 모든 플랫폼에 대해 응용 프로그램을 빌드하려면 **Ctrl + Shift + B**를 누르거나 리본 메뉴에서 **빌드**를 클릭하고 **솔루션 빌드**를 선택합니다.  Windows 시스템에서 iOS 용 코드를 컴파일하고 테스트하려면 [이 가이드](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/)에서 도움말을 참조합니다.

응용 프로그램을 실행하기 전에 대상 구성, 플랫폼 및 프로젝트를 선택해야 합니다.  Xamarin.Forms 응용 프로그램은 Windows, Android 및 iOS에 대한 네이티브 코드로 컴파일됩니다. 이 자습서에는 샘플의 Windows 버전 스크린샷이 포함되지만 모든 버전은 기능적으로 동일합니다. 이 가이드에 사용된 배포 설정은 여기에 표시됩니다.  

![Android 휴대폰에 대해 컴파일하기 위해 구성된 Visual Studio](./media/computer-vision-web-search-tutorial/configuration-selection.png)

빌드 프로세스가 성공하고 대상 플랫폼을 선택했으면 도구 모음에서 **시작** 단추를 클릭하거나 **F5** 키를 누릅니다. Visual Studio는 대상 플랫폼에 솔루션을 배포하고 실행합니다.

키 추가 페이지가 표시됩니다. (이 페이지는 `AddKeysPage.xaml`에서 정의됐습니다.)  

![키 추가 페이지의 이미지](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

Computer Vision 및 Bing Web Search API 키에 붙여넣습니다. Computer Vision API는 또한 사용자에게 엔드포인트를 호스팅하는 서버를 선택하도록 요구합니다.

> [!TIP]
> 이 페이지를 건너뛰려면 `App.xaml.cs`의 적절한 위치에 이 정보를 입력합니다. 

응용 프로그램은 테스트 쿼리를 수행하여 키의 유효성을 확인한 다음, OCR 선택 페이지(`OcrSelectPage.xaml`에서 정의된)로 이동합니다.
   
![OCR 선택 페이지 이미지](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

이 화면 맨 위에서 인식하려는 텍스트를 인쇄하거나 필기하는지 여부를 선택할 수 있습니다.

> [!TIP]
> 가능한 수준으로 텍스트를 인식하고 반사 없이 균일하게 켜져있는지 확인하려는 항목을 보유합니다. 필기 OCR이 때로는 스크립트 글꼴 또는 기타 "멋진" 텍스트에 대해 더 잘 작동합니다.

다음으로, 장치의 카메라를 사용하여 사진을 찍거나 장치에 저장된 사진을 선택하려면 **사진 찍기** 또는 **사진 가져오기**를 클릭합니다.

사진을 찍거나 선택한 후 이미지를 Computer Vision API에 전달합니다. 찾은 단어 페이지(`OcrResultsPage.xaml`에서 정의된)는 이미지에서 인식된 모든 단어를 표시합니다.

![OCR 결과 페이지의 이미지](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> 이 결과 대해 사용한 이미지는 `SamplePhotos\TestImage.jpg`의 원본 리포지토리에 있습니다.

찾은 단어 페이지에서 항목을 클릭하는 경우 웹 결과 페이지(`WebResultsPage.xaml`)가 표시되고 해당 검색에 대한 Bing 상위 결과를 보여줍니다.

![웹 결과 페이지의 이미지](./media/computer-vision-web-search-tutorial/web-results-page.png)  
마지막으로, 포함된 WebView에서 링크를 열려면 웹 결과 페이지에서 항목을 선택합니다. (또는 다른 결과를 선택하려면 뒤로 이동합니다.)

![웹 보기 페이지의 이미지](./media/computer-vision-web-search-tutorial/web-view-page.png)  

웹 결과 페이지로 다시 이동하거나 모든 웹 브라우저에서 해당 페이지와 상호 작용할 수 있습니다. 

## <a name="review-and-learn"></a>검토 및 학습

스핀에 대해 Visual Search 앱을 사용했으므로 두 개의 Microsoft Cognitive Services API를 어떻게 사용하고 있는지 정확히 살펴보겠습니다.  이 샘플을 응용 프로그램에 대한 시작점으로 사용하든 API에 대한 방법으로 사용하든 작동 방법을 정확하게 검사하려면 응용 프로그램을 단계적으로 설명하는 것이 중요합니다.

### <a name="add-keys-page"></a>키 추가 페이지
키 추가 페이지 UI는 `AddKeysPage.xaml`에서 정의되고 해당 기본 논리는 `AddKeysPage.xaml.cs`에서 정의됩니다. 키는 테스트를 요청하여 유효성이 검사되므로 C#에서 Cognitive Services 엔드포인트를 사용하는 방법을 설정할 때가 됐습니다.  

이 상호 작용의 기본 구조는 다음과 같습니다. 

1. *System.Net.Http*에서 *HttpResponseMessage* 및 *HttpClient* 개체 인스턴스화
2. *HttpClient* 개체에 원하는 모든 헤더(각 엔드포인트의 API 참조에서 정의된) 연결
3. 엔드포인트 URI에 모든 필요한 매개 변수를 추가하면서 데이터를 사용하여 GET 또는 POST 요청 보내기
4. 응답 성공적으로 수행되었는지 확인
5. 추가 처리를 위해 응답 전달

Bing Search API 키의 유효성을 검사하는 함수는 여기에 표시된 `CheckBingSearchKey()`입니다.

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

비슷한 기능인 `CheckComputerVisionKey()`는 Computer Vision 키의 유효성을 검사하는 데 사용됩니다.

### <a name="ocr-select-page"></a>OCR 선택 페이지

OCR 선택 페이지(`OcrSelectPage.xaml`)에는 두 가지 중요한 역할이 있습니다. 첫째, 대상 사진에 대해 어떤 유형의 OCR이 수행되는지를 결정합니다. 둘째, 처리하려는 이미지를 사용자가 캡처하거나 열게 합니다.

각 플랫폼은 다른 코드를 필요로 하기 때문에 두 번째 작업은 보통 플랫폼 간 응용 프로그램에서 복잡합니다. Xamarin Media Plugin은 단지 코드 몇 줄을 사용하여 처리합니다.

다음 함수는 사진 캡처를 위해 Xamarin Media Plugin을 사용하는 예제를 통합합니다.  여기서 다음을 수행합니다.

1. 카메라를 현재 장치에서 사용할 수 있는지 확인
2. 캡처된 이미지 저장할 위치를 지정하려면 `StoreCameraMediaOptions` 개체 인스턴스화
3. 이미지를 캡처하고 이미지 데이터가 포함된 `MediaFile` 개체를 얻음
4. 바이트 배열로 `MediaFile` 압축 풀기
5. 추가 처리를 위해 바이트 배열 반환

사진 캡처를 위해 Xamarin Media Plugin을 사용하는 함수 `TakePhoto()`입니다.  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
사진 가져오기 유틸리티는 비슷한 방식으로 작동합니다. 두 가지 기능 모두 `OcrSelectPage.xaml.cs`에서 확인할 수 있습니다. 
 
> [!NOTE]
> 필기 OCR 엔드포인트는 크기가 4MB보다 작은 사진만 처리할 수 있습니다. 파일 크기 문제를 방지하려면 `StoreCameraMediaOptions` 개체에 옵션 `PhotoSize = PhotoSize.Medium`을 설정하여 사진을 원래 크기의 50%로 줄입니다.  장치가 매우 고품질의 사진을 찍지만 오류가 발생하는 경우 대신 `PhotoSize = PhotoSize.Small`을 시도해볼 수 있습니다.

*MediaFile*을 바이트 배열로 변환하는 데 사용된 유틸리티 함수입니다.

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>OCR 결과 페이지

OCR 결과 페이지는 **Json.NET** [SelectToken 메서드](http://www.newtonsoft.com/json/help/html/SelectToken.htm)를 사용하여 이미지에서 추출된 텍스트를 표시합니다.  두 개의 OCR 엔드포인트는 약간 다르게 작동하므로 둘 다 설명하는 것이 중요합니다.  

Computer Vision API는 몇몇 서버 위치에서만 호스팅되므로 해당 엔드포인트는 런타임 시 동적으로 생성돼야 합니다. 함수 `SetOcrLocation`(`AppConstants.cs`에 있는 정적 *AppConstants* 클래스의 일부)은 URI 엔드포인트의 위치를 설정합니다. 키 추가 페이지에서 사용자의 선택에 해당하거나 `App.xaml.cs`에 설정된 값을 사용합니다. 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

이러한 API 요청을 좀 더 자세히 살펴보겠습니다. Computer Vision OCR API는 미확인 언어에서 텍스트를 구문 분석할 수 있지만 결과 개선하려면 영어 텍스트를 검색하도록 합니다. 또한 API가 우리를 위해 텍스트 방향을 검색하게 합니다. 고정 방향을 사용하면 구문 분석 결과를 향상시킬 수 있겠지만 방향 검색은 모바일 응용 프로그램에서 유용할 수 있습니다.

[광학 문자 인식 API 참조 인쇄](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc)에서 이 엔드포인트에서 사용할 수 있는 매개 변수에 대해 자세히 알아볼 수 있습니다.  

필기 OCR API는 아직 미리 보기 상태에 있으며 현재는 영문 텍스트에서만 작동합니다.  이러한 이유로 해당 유일한 매개 변수는 현재 필기 텍스트를 전혀 구문 분석하지 않는지 여부를 나타내는 플래그입니다. 필기 OCR API는 컴퓨터 인쇄 및 필기 텍스트 모두를 구문 분석할 수 있지만 `handwriting=false`면 인쇄 텍스트에서 더 나은 결과를 얻습니다. 

이 응용 프로그램은 영어로 돼 있으므로 이 샘플에 대해 필기 OCR만 사용할 수 있었으며 사용자가 인식하라고 알려주는 것에 따라 `handwriting` 플래그를 설정할 수 있었습니다.  설명을 위해 두 엔드포인트 모두 사용했습니다.  

[필기 광학 문자 인식 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200)에서 이 엔드포인트에서 사용할 수 있는 매개 변수에 대해 자세히 알아볼 수 있습니다.

이제 이러한 API를 호출하는 함수를 살펴보도록 하겠습니다.

`FetchPrintedWordList()`는 인쇄 OCR 엔드포인트를 사용하여 이미지에서 인쇄된 텍스트를 구문 분석합니다. HTTP 요청은 키의 유효성을 검사하기 위해 키 추가 페이지에서 사용한 호출과 비슷한 구조를 따르지만 사진을 전송해야 합니다. 사진이 너무 커서 쿼리 문자열을 전달할 수 없으므로 GET 요청 대신 HTTP POST 요청을 사용해야 합니다. 사진(바이트 배열로 메모리에 존재하는)을 `ByteArrayContent` 개체로 인코딩하고 전송하는 데이터 형식을 정의하는 이 개체에 헤더를 추가해야 합니다. 

[Computer Vision API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200)에서 허용 가능한 콘텐츠 형식 정보를 확인할 수 있습니다.  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> 응답 개체에서 텍스트를 추출하려면 **Json.NET** [SelectToken 메서드](http://www.newtonsoft.com/json/help/html/SelectToken.htm)를 사용하는 방법을 참고합니다. `SelectToken`은 다음 함수로 전달할 수 있는 JSON 응답의 특정한 일부만 필요한 경우 이상적입니다. 코드의 다른 부분에서 JSON 응답을 `ModelObjects.cs`에서 정의된 모델 개체로 deserialize합니다.

인쇄 OCR 및 필기 OCR 요청 간 주요 차이점은 인쇄 OCR 서비스가 인식된 텍스트를 HTTP 응답의 일부로 반환하는 한편, 필기 OCR 서비스는 정보를 가져오기 위해 추가 정보를 요구하는 점입니다. 초기 필기 OCR 요청은 처리를 시작했다는 것만 표시하는 HTTP 202 상태를 반환합니다. 이 응답은 클라이언트가 사용할 수 있는 경우 완료된 응답을 얻기 위해 확인해야 하는 엔드포인트를 포함합니다. 작동 방식을 보려면 `FetchHandwrittenWordList()`를 참조합니다.

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()`는 필기 OCR 프로세스의 두 번째 부분입니다. 결과를 얻거나 함수가 시간 초과될 때까지 초기 응답의 메타데이터에서 추출된 URI를 ping합니다.  이 함수는 자체 스레드에서 비동기적으로 호출된다는 것이 중요합니다. 그렇지 않으면 이 메서드는 처리가 완료될 때까지 사용자 인터페이스를 잠급니다.

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>웹 결과 페이지
사용자가 OCR 결과 페이지에 표시된 검색 용어를 선택한 후 웹 결과 페이지로 이동합니다.  여기서 [Bing Web Search API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 요청을 생성해 이를 서비스의 엔드포인트로 보내고 **Json.NET** [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm) 메서드를 사용하여 JSON 응답을 deserialize합니다.  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

Bing Web Search API는 사용자가 원할 수 있는 것에 대해 가능한 한 많은 정보를 제공하는 경우 가장 잘 작동합니다. 특히 사용자의 위치와 선호하는 언어를 식별하려면 거의 항상 `mkt` 및 `setLang` 매개 변수(미국 영어로 설정됨)를 사용해야 합니다.

> [!NOTE]
> 소스 코드를 이해하기 쉽게 하기 위해 Bing Web Search 쿼리를 단순하게 만들었습니다.  실제 응용 프로그램에서 향상된 결과를 위해서는 HTTP 요청에 다음 헤더를 추가해야 합니다. 
> * User-Agent  
> * X-MSEdge-ClientID  
> * X-Search-ClientIP  
> * X-Search-Location  
>
> [Bing Web Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers)에서 이러한 헤더 값에 대해 자세히 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
Microsoft Cognitive Services는 이 응용 프로그램에 쉽게 통합될 수 있는 많은 추가 서비스를 제공합니다.  예를 들어 다음과 같이 할 수 있습니다.

* 웹 검색 결과를 보강하려면 [Bing 엔터티 검색](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) 추가
* Bing Web Search를 대신해 [Bing Custom Search](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/)로 교환
* 캡처된 이미지에 대해 자세히 알아보고 웹에서 비슷한 이미지를 찾으려면 [Bing Image Search](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 이미지 인사이트 기능 사용
* 구문 분석된 텍스트의 품질을 더욱 개선하려면 [Bing Spell Check](https://azure.microsoft.com/services/cognitive-services/spell-check/) 사용
* 추출된 텍스트를 다른 언어로 보려면 [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) 통합
* [Cognitive Services 포털](https://azure.microsoft.com/services/cognitive-services/)에서 다른 서비스를 혼합 및 일치시키면 무한한 가능성이 있습니다!
