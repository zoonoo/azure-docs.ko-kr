---
title: '자습서: C#을 사용하여 Translator Text에 대한 WPF 응용 프로그램 작성'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Translator Text API를 사용하여 C#을 통해 WPF 응용 프로그램을 빌드하여 텍스트를 번역하고, 지원되는 언어의 지역화된 목록을 가져오는 등의 방법을 알아봅니다.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: nolachar
ms.openlocfilehash: 97660985b275bbe4384acb3fc92be8aaa0b57881
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123966"
---
# <a name="tutorial-write-a-wpf-application-for-translator-text-using-c35"></a>자습서: C&#35;을 사용하여 Translator Text에 대한 WPF 응용 프로그램 작성

이 자습서에서는 Azure의 Microsoft Cognitive Services에 포함된 Translator Text API(V3)를 사용하여 대화형 텍스트 번역 도구를 빌드합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 서비스에서 지원되는 언어 목록 가져오기
> * 사용자가 입력한 텍스트를 다른 언어로 번역 실행

이 응용 프로그램은 두 가지 다른 Microsoft Cognitive Services와의 통합 기능도 제공합니다.

|||
|-|-|
|[텍스트 분석](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|필요에 따라 번역할 텍스트의 원본 언어를 자동으로 검색하는 데 사용됩니다.|
|[Bing Spell Check](https://azure.microsoft.com/services/cognitive-services/spell-check/)|영어 원본 텍스트의 경우 번역이 더 정확하도록 철자 오류를 수정하는 데 사용됩니다.

![[자습서 프로그램 실행 중]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>필수 조건

Windows에서 이 코드를 실행하려면 [Visual Studio 2017](https://www.visualstudio.com/downloads/)이 필요합니다. 체험 Community Edition을 사용해도 됩니다.

프로그램에서 사용되는 세 가지 Azure 서비스에 대한 구독 키도 필요합니다. Azure 대시보드에서 Translator Text 서비스에 대한 키를 가져올 수 있습니다. 체험 가격 책정 계층에서는 매월 최대 2백만 자를 무료로 번역할 수 있습니다.

텍스트 분석 및 Bing Spell Check 서비스는 둘 다 평가판을 제공하며, [Cognitive Services 체험하기](https://azure.microsoft.com/try/cognitive-services/)에서 등록할 수 있습니다. Azure 대시보드를 통해 두 서비스에 대한 구독을 만들 수도 있습니다. 텍스트 분석에는 체험 계층이 있습니다.

이 자습서의 소스 코드는 아래에서 확인할 수 있습니다. 구독 키는 `MainWindow.xaml.cs`의 소스 코드에 `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY` 등의 변수로 복사해야 합니다.

> [!IMPORTANT]
> 텍스트 분석 서비스는 여러 지역에서 사용할 수 있습니다. 이 자습서 소스 코드의 URI는 평가판에 사용되는 지역인 `westus` 지역에 있습니다. 다른 지역에 구독이 있는 경우 이 URI를 적절하게 업데이트합니다.

## <a name="source-code"></a>소스 코드

Microsoft Translator Text API의 소스 코드입니다. 앱을 실행하려면 Visual Studio에서 새 WPF 프로젝트의 해당 파일에 소스 코드를 복사합니다.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

응용 프로그램의 기능을 제공하는 코드 숨김 파일입니다.

```csharp
using System;
using System.Windows;
using System.Net;
using System.Net.Http;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using System.Text;

// NOTE: Add assembly references to System.Runtime.Serialization, System.Web, and System.Web.Extensions.

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace MSTranslatorTextDemo
{
    /// <summary>
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string from
    /// one language to another. The languages are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected. English text is spell-checked.
    /// </summary>
    public partial class MainWindow : Window
    {
        // Translator text subscription key from Microsoft Azure dashboard
        const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

        public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
        const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
        const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

        private string[] languageCodes;     // array of language codes

        // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
        private SortedDictionary<string, string> languageCodesAndTitles =
            new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

        public MainWindow()
        {
            // at least show an error dialog if there's an unexpected error
            AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

            if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
                || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
                || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
            {
                MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                    "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                    "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
                System.Windows.Application.Current.Shutdown();
            }
            else
            {
                InitializeComponent();          // start the GUI
                GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
                PopulateLanguageMenus();        // fill the drop-down language lists
            }
        }

        // Global exception handler to display error message and exit
        private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception)args.ExceptionObject;
            MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }

        // ***** POPULATE LANGUAGE MENUS
        private void PopulateLanguageMenus()
        {
            // Add option to automatically detect the source language
            FromLanguageComboBox.Items.Add("Detect");

            int count = languageCodesAndTitles.Count;
            foreach (string menuItem in languageCodesAndTitles.Keys)
            {
                FromLanguageComboBox.Items.Add(menuItem);
                ToLanguageComboBox.Items.Add(menuItem);
            }

            // set default languages
            FromLanguageComboBox.SelectedItem = "Detect";
            ToLanguageComboBox.SelectedItem = "English";
        }

        // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
        private string DetectLanguage(string text)
        {
            string uri = TEXT_ANALYTICS_API_ENDPOINT + "languages?numberOfLanguagesToDetect=1";

            // create request to Text Analytics API
            HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_ANALYTICS_API_SUBSCRIPTION_KEY);
            detectLanguageWebRequest.Method = "POST";

            // create and send body of request
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            string jsonText = serializer.Serialize(text);

            string body = "{ \"documents\": [ { \"id\": \"0\", \"text\": " + jsonText + "} ] }";
            byte[] data = Encoding.UTF8.GetBytes(body);
            detectLanguageWebRequest.ContentLength = data.Length;

            using (var requestStream = detectLanguageWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);

            HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

            // read and parse JSON response
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);

            // fish out the detected language code
            var languageInfo = jsonResponse["documents"][0]["detectedLanguages"][0];
            if (languageInfo["score"] > (decimal)0.5)
                return languageInfo["iso6391Name"];
            else
                return "";
        }

        // ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
        private string CorrectSpelling(string text)
        {
            string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

            // create request to Bing Spell Check API
            HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", BING_SPELL_CHECK_API_SUBSCRIPTION_KEY);
            spellCheckWebRequest.Method = "POST";
            spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

            // create and send body of request
            string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
            byte[] data = Encoding.UTF8.GetBytes(body);
            spellCheckWebRequest.ContentLength = data.Length;
            using (var requestStream = spellCheckWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);
            HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

            // read and parse JSON response and get spelling corrections
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);
            var flaggedTokens = jsonResponse["flaggedTokens"];

            // construct sorted dictionary of corrections in reverse order in string (right to left)
            // so that making a correction can't affect later indexes
            var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
            for (int i = 0; i < flaggedTokens.Length; i++)
            {
                var correction = flaggedTokens[i];
                var suggestion = correction["suggestions"][0];  // consider only first suggestion
                if (suggestion["score"] > (decimal)0.7)         // take it only if highly confident
                    corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                        { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
            }

            // apply the corrections in order from right to left
            foreach (int i in corrections.Keys)
            {
                var oldtext = corrections[i][0];
                var newtext = corrections[i][1];

                // apply capitalization from original text to correction - all caps or initial caps
                if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
                else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

                text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
            }

            return text;
        }

        // ***** GET TRANSLATABLE LANGUAGE CODES
        private void GetLanguagesForTranslate()
        {
            // send request to get supported language codes
            string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
            WebRequest WebRequest = WebRequest.Create(uri);
            WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
            WebRequest.Headers.Add("Accept-Language", "en");
            WebResponse response = null;
            // read and parse the JSON response
            response = WebRequest.GetResponse();
            using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
            {
                var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
                var languages = result["translation"];

                languageCodes = languages.Keys.ToArray();
                foreach (var kv in languages)
                {
                    languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
                }
            }
        }

        // ***** PERFORM TRANSLATION ON BUTTON CLICK
        private async void TranslateButton_Click(object sender, EventArgs e)
        {
            string textToTranslate = TextToTranslate.Text.Trim();

            string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
            string fromLanguageCode;

            // auto-detect source language if requested
            if (fromLanguage == "Detect")
            {
                fromLanguageCode = DetectLanguage(textToTranslate);
                if (!languageCodes.Contains(fromLanguageCode))
                {
                    MessageBox.Show("The source language could not be detected automatically " +
                        "or is not supported for translation.", "Language detection failed",
                        MessageBoxButton.OK, MessageBoxImage.Error);
                    return;
                }
            }
            else
                fromLanguageCode = languageCodesAndTitles[fromLanguage];

            string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

            // spell-check the source text if the source language is English
            if (fromLanguageCode == "en")
            {
                if (textToTranslate.StartsWith("-"))    // don't spell check in this case
                    textToTranslate = textToTranslate.Substring(1);
                else
                {
                    textToTranslate = CorrectSpelling(textToTranslate);
                    TextToTranslate.Text = textToTranslate;     // put corrected text into input field
                }
            }

            // handle null operations: no text or same source/target languages
            if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
            {
                TranslatedTextLabel.Content = textToTranslate;
                return;
            }

            // send HTTP request to perform the translation
            string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
            string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

            System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
                request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
                var translations = result[0]["translations"];
                var translation = translations[0]["text"];

                // Update the translation field
                TranslatedTextLabel.Content = translation;
            }
        }
    }
}
```

### <a name="mainwindowxaml"></a>MainWindow.xaml

이 파일은 WPF 폼인 응용 프로그램의 사용자 인터페이스를 정의합니다. 고유 버전의 폼을 디자인하려는 경우 이 XAML이 필요하지 않습니다.

```xml
<Window x:Class="MSTranslatorTextDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:MSTranslatorTextDemo"
        mc:Ignorable="d"
        Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
    <Grid>
        <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
        <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
        <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
        <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

        <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
        <ComboBox x:Name="ToLanguageComboBox"
                HorizontalAlignment="Left"
                Margin="306,88,0,0"
                VerticalAlignment="Top"
                Width="175" FontSize="14" TabIndex="2">

        </ComboBox>
        <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
        <ComboBox x:Name="FromLanguageComboBox"
            HorizontalAlignment="Left"
            Margin="42,88,0,0"
            VerticalAlignment="Top"
            Width="175" FontSize="14" TabIndex="1"/>
        <Label x:Name="TranslatedTextLabel" Content="Translation appears here" HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
    </Grid>
</Window>
```

## <a name="service-endpoints"></a>서비스 엔드포인트

Microsoft Translator 서비스에는 다양한 번역 기능을 제공하는 많은 엔드포인트가 있습니다. 이 자습서에서 사용하는 엔드포인트는 다음과 같습니다.

|||
|-|-|
|`Languages`|현재 Translator Text API의 다른 작업에서 지원하는 언어 집합을 반환합니다.|
|`Translate`|원본 텍스트, 원본 언어 코드 및 대상 언어 코드가 지정되면 원본 텍스트를 대상 언어로 번역한 텍스트를 반환합니다.|

## <a name="the-translation-app"></a>번역 앱

번역 응용 프로그램의 사용자 인터페이스는 WPF(Windows Presentation Foundation)를 사용하여 빌드되었습니다. 다음 단계에 따라 Visual Studio에서 새 WPF 프로젝트를 만듭니다.

* **파일** 메뉴에서 **새로 만들기 > 프로젝트**를 선택합니다.
* 새 프로젝트 창에서 **설치됨 > 템플릿 > Visual C#** 을 엽니다. 사용 가능한 프로젝트 템플릿 목록이 대화 상자 가운데에 표시됩니다.
* 프로젝트 템플릿 목록 위의 드롭다운 메뉴에서 **.NET Framework 4.5.2**가 선택되었는지 확인합니다.
* 프로젝트 템플릿 목록에서 **WPF 앱(.NET Framework)** 을 클릭합니다.
* 대화 상자의 맨 아래에 있는 필드를 사용하여 새 프로젝트 및 이 프로젝트를 포함하는 솔루션의 이름을 지정합니다.
* **확인**을 클릭하여 새 프로젝트와 솔루션을 만듭니다.

![[Visual Studio에서 새 WPF 앱을 만드는 중]](media/translator-text-csharp-new-project.png)

다음 .NET Framework 어셈블리에 대한 참조를 프로젝트에 추가합니다.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

또한 NuGet 패키지 `Newtonsoft.Json`을 프로젝트에 설치합니다.

이제 솔루션 탐색기에서 `MainWindow.xaml` 파일을 찾아 엽니다. 처음에는 파일이 비어 있습니다. 파란색 컨트롤 이름이 주석으로 달린, 완성된 사용자 인터페이스의 모양은 다음과 같습니다. 코드에도 표시되므로 사용자 인터페이스의 컨트롤에 대해 동일한 이름을 사용합니다.

![[Visual Studio 디자이너에서 주 창의 주석 보기]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> 이 자습서의 소스 코드에는 이 폼의 XAML 소스가 포함되어 있습니다. Visual Studio에서 폼을 작성하는 대신 프로젝트에 붙여넣을 수 있습니다.

* `FromLanguageComboBox`*(콤보 상자)* - 텍스트 번역에 대해 Microsoft Translator에서 지원하는 언어 목록을 표시합니다. 사용자가 번역할 원본 언어를 선택합니다.
* `ToLanguageComboBox`*(콤보 상자)* - `FromComboBox`와 동일한 언어 목록을 표시하지만 사용자가 번역할 대상 언어를 선택하는 데 사용됩니다.
* `TextToTranslate`*(텍스트 상자)* - 사용자가 번역할 텍스트를 여기에 입력합니다.
* `TranslateButton`*(단추)* - 사용자가 이 단추를 클릭하거나 Enter 키를 눌러 텍스트를 번역합니다.
* `TranslatedTextLabel`*(레이블)* - 사용자 텍스트에 대한 번역이 여기에 표시됩니다.

이 폼의 고유한 버전을 만드는 경우 여기에 사용된 것과 *똑같이* 만들 필요는 없습니다. 그러나 언어 이름이 잘리지 않도록 언어 드롭다운이 충분히 넓은지 확인합니다.

## <a name="the-mainwindow-class"></a>MainWindow 클래스

코드 숨김 파일 `MainWindow.xaml.cs`에는 프로그램이 작업을 수행하게 하는 코드가 포함됩니다. 작업은 다음 두 가지 경우에 수행됩니다.

* 프로그램이 시작되고 `MainWindow`가 인스턴스화되면 Translator의  및 API를 사용하여 언어 목록을 검색하고 드롭다운 메뉴를 채웁니다. 이 작업은 각 세션을 시작할 때 한 번 수행됩니다.

* 사용자가 **번역** 단추를 클릭하면 사용자의 언어 선택 항목이 검색되고 입력한 텍스트 및 `Translate` API가 변역을 수행하기 위해 차례로 호출됩니다. 텍스트의 언어를 확인하고 번역 전에 맞춤법을 수정하기 위해 다른 함수도 호출될 수 있습니다.

클래스의 시작 부분을 살펴보십시오.

```csharp
public partial class MainWindow : Window
{
    // Translator text subscription key from Microsoft Azure dashboard
    const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

    public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
    const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
    const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    private string[] languageCodes;     // array of language codes

    // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
    private SortedDictionary<string, string> languageCodesAndTitles =
        new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

    public MainWindow()
    {
        // at least show an error dialog if there's an unexpected error
        AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

        if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
            || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
            || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
        {
            MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }
        else
        {
            InitializeComponent();          // start the GUI
            GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
            PopulateLanguageMenus();        // fill the drop-down language lists
        }
    }
// more to come
}
```

여기서 선언한 두 개의 멤버 변수는 언어에 대한 정보를 포함합니다.

|||
|-|-|
|`languageCodes`<br>문자열의 배열|언어 코드를 캐시합니다. Translator 서비스는 영어를 나타내는 `en`과 같은 짧은 코드를 사용하여 언어를 식별합니다.|
|`languageCodesAndTitles`<br>SortedDictionary|사용자 인터페이스의 “친숙한” 이름을 API에 사용된 짧은 코드로 다시 매핑합니다. 대/소문자와 관계없이 사전순으로 정렬됩니다.|

응용 프로그램에서 실행하는 첫 번째 코드는 `MainWindow` 생성자입니다. 먼저 `HandleExceptions` 메서드를 전역 오류 처리기로 설정합니다. 이렇게 하면 예외가 처리되지 않을 경우 최소한 오류 메시지가 표시됩니다.

다음에는 API 구독 키의 길이가 모두 정확히 32자인지 확인합니다. 32자가 아닌 경우 가장 가능성이 높은 이유는 *누군가*가 API 키를 붙여넣지 않은 것입니다. 이 경우 오류 메시지를 표시하고 구제합니다. 이 테스트를 통과해도 키가 유효하다는 것은 아닙니다.

최소한 키 길이가 올바른 경우 `InitializeComponent()` 호출에서 주 응용 프로그램 창에 대한 XAML 설명을 찾아 로드하고 인스턴스화하여 사용자 인터페이스가 롤링되도록 합니다.

마지막으로, 언어 드롭다운 메뉴를 설정합니다. 이 작업에는 다음 섹션에서 자세히 설명되어 있는 세 개의 별도 메서드 호출이 필요합니다.

## <a name="get-supported-languages"></a>지원되는 언어 가져오기

Microsoft Translator 서비스는 이 문서를 작성할 당시 총 61개 언어를 지원하며, 수시로 더 많은 언어가 추가될 수 있습니다. 따라서 프로그램에서 지원되는 언어를 하드 코드하지 않는 것이 좋습니다. Translator 서비스에 지원하는 언어를 확인하세요. 지원되는 모든 언어를 지원되는 다른 언어로 번역할 수 있습니다.

`Languages` API를 호출하여 지원되는 언어 목록을 가져옵니다.

`Languages` API는 선택적 GET 쿼리 매개 변수인 *scope*를 사용합니다. *scope*에는 `translation`, `transliteration` 및 `dictionary`의 세 값 중 하나가 포함될 수 있습니다. 이 코드는 `translation` 값을 사용합니다.

`Languages` API는 선택적 HTTP 헤더인 `Accept-Language`도 사용합니다. 이 헤더의 값은 지원되는 언어의 이름이 반환되는 언어를 결정합니다. 값은 올바른 형식의 BCP 47 언어 태그여야 합니다. 이 코드는 `en` 값을 사용하여 언어 이름을 영어로 가져옵니다.

`Languages` API는 다음과 같이 표시되는 JSON 응답을 반환합니다.

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
}
```

언어 코드(예: `af`) 및 언어 이름(예: `Afrikaans`)을 추출할 수 있도록 이 코드는 NewtonSoft.Json 메서드 [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)를 사용합니다.

이 배경 정보를 사용하여 언어 코드와 해당 이름을 검색하는 다음 메서드를 만듭니다.

```csharp
private void GetLanguagesForTranslate()
{
    // send request to get supported language codes
    string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
    WebRequest WebRequest = WebRequest.Create(uri);
    WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
    WebRequest.Headers.Add("Accept-Language", "en");
    WebResponse response = null;
    // read and parse the JSON response
    response = WebRequest.GetResponse();
    using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
    {
        var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
        var languages = result["translation"];

        languageCodes = languages.Keys.ToArray();
        foreach (var kv in languages)
        {
            languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
        }
    }
}
```

`GetLanguagesForTranslate()`는 먼저 HTTP 요청을 만듭니다. `scope=translation` 쿼리 문자열 매개 변수는 텍스트 번역을 지원하는 언어만을 요청합니다. Text Translation API 구독 키는 요청 헤더에 추가됩니다. 지원되는 언어가 영어로 반환되도록 값이 `en`인 `Accept-Language` 헤더가 추가됩니다.

요청이 완료된 후 JSON 응답은 구문 분석되고 사전으로 변환된 다음, 언어 코드는 `languageCodes` 멤버 변수에 추가됩니다. 언어 코드와 친숙한 언어 이름을 포함하는 키/값 쌍이 반복되고 `languageCodesAndTitles` 멤버 변수에 추가됩니다. (폼의 드롭다운 메뉴에는 이름이 표시되지만 번역을 요청하려면 코드가 필요합니다.)

## <a name="populate-the-language-menus"></a>언어 메뉴 채우기

대부분의 사용자 인터페이스는 XAML로 정의되므로 `InitializeComponent()` 호출 외에는 설정하는 데 많은 작업이 필요하지 않습니다. **원본** 및 **대상** 드롭다운에 친숙한 언어 이름을 추가하기만 하면 되며, 이 작업은 `PopulateLanguageMenus()`에서 수행됩니다.

```csharp
private void PopulateLanguageMenus()
{
    // Add option to automatically detect the source language
    FromLanguageComboBox.Items.Add("Detect");

    int count = languageCodesAndTitles.Count;
    foreach (string menuItem in languageCodesAndTitles.Keys)
    {
        FromLanguageComboBox.Items.Add(menuItem);
        ToLanguageComboBox.Items.Add(menuItem);
    }

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

메뉴 채우기는 `languageCodesAndTitles` 사전을 반복하고 “친숙한” 이름인 각 키를 두 메뉴에 모두 추가하면 되는 간단한 작업입니다. 메뉴를 채운 후 기본 "대상" 및 "원본" 언어는 **감지**(언어 자동 감지) 및 **영어**로 설정됩니다.

> [!TIP]
> 메뉴에 대해 기본 선택 사항을 설정하지 않을 경우 사용자는 "대상" 또는 "원본" 언어를 선택하지 않고 **번역**을 클릭할 수 있습니다. 기본값을 사용하면 이 문제를 처리할 필요가 없습니다.

이제 `MainWindow`가 초기화되고 사용자 인터페이스가 생성되었으므로 코드는 사용자가**변역** 단추를 클릭할 때까지 대기합니다.

## <a name="perform-translation"></a>번역 수행

사용자가 **번역**을 클릭하면 WPF에서 여기에 표시된 `TranslateButton_Click()` 이벤트 처리기를 호출합니다.

```csharp
private async void TranslateButton_Click(object sender, EventArgs e)
{
    string textToTranslate = TextToTranslate.Text.Trim();

    string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
    string fromLanguageCode;

    // auto-detect source language if requested
    if (fromLanguage == "Detect")
    {
        fromLanguageCode = DetectLanguage(textToTranslate);
        if (!languageCodes.Contains(fromLanguageCode))
        {
            MessageBox.Show("The source language could not be detected automatically " +
                "or is not supported for translation.", "Language detection failed",
                MessageBoxButton.OK, MessageBoxImage.Error);
            return;
        }
    }
    else
        fromLanguageCode = languageCodesAndTitles[fromLanguage];

    string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

    // spell-check the source text if the source language is English
    if (fromLanguageCode == "en")
    {
        if (textToTranslate.StartsWith("-"))    // don't spell check in this case
            textToTranslate = textToTranslate.Substring(1);
        else
        {
            textToTranslate = CorrectSpelling(textToTranslate);
            TextToTranslate.Text = textToTranslate;     // put corrected text into input field
        }
    }

    // handle null operations: no text or same source/target languages
    if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
    {
        TranslatedTextLabel.Content = textToTranslate;
        return;
    }

    // send HTTP request to perform the translation
    string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
    string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

    System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
    var requestBody = JsonConvert.SerializeObject(body);

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
        request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();

        var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
        var translations = result[0]["translations"];
        var translation = translations[0]["text"];

        // Update the translation field
        TranslatedTextLabel.Content = translation;
    }
}
```

첫 번째 단계는 사용자가 입력한 텍스트와 함께 "대상" 및 "원본" 언어를 폼에서 검색하는 것입니다.

원본 언어가 **감지**로 설정된 경우 `DetectLanguage()`를 호출하여 텍스트 언어를 확인합니다. 텍스트 언어가 Translator API에서 지원하지 않는 언어이거나(번역할 수 있는 언어보다 훨씬 더 많은 언어가 감지될 수 있음) 텍스트 분석 API가 감지하지 못할 수 있습니다. 이 경우 사용자에게 알리는 메시지를 표시하고, 번역하지 않고 돌아갑니다.

지정 또는 감지되었는지에 관계없이 원본 언어가 영어인 경우 `CorrectSpelling()`을 사용하여 텍스트 맞춤법을 검사하고 수정 사항을 적용합니다. 수정한 텍스트가 입력 필드에 다시 채워지므로 사용자가 수정된 것을 알 수 있습니다. 사용자는 번역 중인 텍스트 앞에 하이픈을 추가하여 맞춤법 수정을 표시하지 않을 수 있습니다.

사용자가 텍스트를 입력하지 않았거나 "대상" 및 "원본" 언어가 동일한 경우에는 번역이 필요하지 않고 요청을 방지할 수 있습니다.

변역 요청을 수행하는 코드는 URI 작성, 요청 만들기, 보내기 및 응답 구문 분석과 친숙해 보여야 합니다. 텍스트를 표시하려면 `TranslatedTextLabel` 컨트롤에 보냅니다.

다음으로 POST 요청 본문에 있는 직렬화된 JSON 배열을 통해 `Translate` API에 텍스트를 전달합니다. JSON 배열에는 번역할 텍스트가 여러 개 포함될 수 있지만 여기서는 하나만 필요합니다.

`X-ClientTraceId`라는 HTTP 헤더는 선택 사항입니다. 값은 GUID여야 합니다. 클라이언트가 제공하는 추적 ID는 요청이 예상대로 작동하지 않을 경우 요청을 추적하는 데 유용합니다. 그러나 유용하려면 클라이언트가 X-ClientTraceID 값을 기록해야 합니다. 클라이언트 추적 ID와 요청 날짜는 발생할 수 있는 문제를 Microsoft에서 진단하는 데 도움이 됩니다.

> [!NOTE]
> 이 자습서에서는 Microsoft Translator 서비스에 대해 중점적으로 설명하므로 `DetectLanguage()` 및 `CorrectSpelling()` 메서드는 자세히 다루지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Microsoft Translator Text API 참조](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
