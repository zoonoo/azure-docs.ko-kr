---
title: '자습서: Translator Speech API C#'
titleSuffix: Azure Cognitive Services
description: Translator Speech API를 사용하여 실시간으로 텍스트를 번역합니다.
services: cognitive-services
author: v-jerkin
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: v-jerkin
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 68457367987305b6926f8f885a226cfcedd23873
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65860329"
---
# <a name="tutorial-translator-speech-application-in-c"></a>자습서: C#의 Translator Speech 애플리케이션

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

이 자습서에서는 Azure Cognitive Services의 일부인 Translator Speech API를 사용하는 대화형 음성 번역 도구를 간략히 살펴봅니다. 다음 방법을 알게 됩니다.

> [!div class="checklist"]
> * 서비스에서 지원되는 언어 목록 요청
> * 오디오 캡처 및 서비스로 전송
> * 음성 수신 및 텍스트로 변환
> * 필요에 따라 발화(텍스트 음성 변환) 버전의 변환 재생

이 애플리케이션에 대한 Visual Studio 솔루션 파일은 [GitHub에서 사용](https://github.com/MicrosoftTranslator/SpeechTranslator)할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 Community Edition을 포함하여 Visual Studio 2019의 모든 버전이 필요합니다.

Visual Studio 솔루션은 애플리케이션에 대한 설치 관리자도 빌드합니다. 이 기능을 지원하려면 [WiX 도구 집합](http://wixtoolset.org/) 및 [WiX 도구 집합 Visual Studio 확장](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension)이 필요합니다.

Microsoft Azure 대시보드에서 얻을 수 있는 Translator Speech 서비스에 대한 구독 키도 필요합니다. 체험 가격 책정 계층에서는 음성을 매월 최대 10시간까지 무료로 변환할 수 있습니다. 이 자습서에서는 이 계층이면 충분합니다.

타사 [JSON.NET 라이브러리](https://www.newtonsoft.com/json)(Newtonsoft)도 필요합니다. Visual Studio 옵션에서 두 패키지 복원 확인란을 모두 사용하도록 설정되어 있으면 NuGet에서 이 어셈블리를 자동으로 설치합니다.

## <a name="trying-the-translation-app"></a>변환 응용 프로그램 사용해 보기

Visual Studio에서 Speech Translator 솔루션(`SpeechTranslator.sln`)을 연 후에 F5 키를 눌러 애플리케이션을 빌드하고 시작합니다.  프로그램의 주 창이 나타납니다.

![[Speech Translator 주 창]](media/speech-translator-main-window.png)

첫 번째 실행의 **설정** 메뉴에서 **계정 설정**을 선택하여 여기에 표시된 창을 엽니다.

![[Speech Translator 주 창]](media/speech-translator-settings-window.png)

이 창에 Translator Speech 구독 키를 붙여넣은 다음, **저장**을 클릭합니다. 실행 사이에서 키가 저장됩니다.

주 창으로 돌아가서 사용하려는 오디오 입력 및 출력 디바이스와 [소스] 및 [대상] 언어를 선택합니다. 변환된 오디오를 들으려면 **TTS**(텍스트 음성 변환) 옵션이 선택되어 있는지 확인합니다. 말하는 대로 불확실한 부분 변환을 확인하려면 **부분 결과** 옵션을 사용하도록 설정합니다.

마지막으로 **시작**을 클릭하여 변환을 시작합니다. 변환하려는 내용을 말하고, 창에 표시된 인식된 텍스트와 변환을 조사합니다. TTS 옵션을 사용하도록 설정하면 변환 내용도 들을 수 있습니다.

## <a name="obtaining-supported-languages"></a>지원되는 언어 가져오기

이 문서를 작성하는 시점에서 Translator Speech 서비스는 텍스트 변환에 60개가 넘는 언어를 지원합니다. 음성 변환에는 더 적은 수의 언어가 지원됩니다. 이러한 언어는 전사(음성 인식) 및 합성(텍스트 음성 변환 출력의 경우)을 모두 지원해야 합니다.

즉 음성 변환의 경우 소스 언어는 전사에 지원되는 언어여야 합니다. 텍스트 결과를 원한다고 가정하는 경우 출력 언어는 텍스트 변환에 지원되는 언어 중 하나일 수 있습니다. 음성 출력을 원하는 경우 텍스트 음성 변환에 지원되는 언어로만 변환할 수 있습니다.

Microsoft는 수시로 새 언어에 대한 지원을 추가할 수 있습니다. 이러한 이유로 애플리케이션에서 지원되는 언어에 대한 지식을 하드 코드하면 안됩니다. 대신, Translator Speech API는 런타임에서 지원되는 언어를 검색할 수 있는 언어 엔드포인트를 제공합니다. 다음 언어 목록 중에서 하나 이상을 받도록 선택할 수 있습니다.

| | |
|-|-|
|`speech`|음성 전사에 지원되는 언어입니다. 음성 변환에 대한 소스 언어가 될 수 있습니다.|
|`text`|텍스트-텍스트 변환에 지원되는 언어입니다. 텍스트 출력을 사용할 때 음성 변환에 대한 대상 언어가 될 수 있습니다.|
|`tts`|각각 특정 언어와 각각 연결되어 음성 합성에 지원되는 음성입니다. 텍스트 음성 변환을 사용할 때 음성 변환에 대한 대상 언어가 될 수 있습니다. 지정된 언어는 둘 이상의 음성으로 지원될 수 있습니다.|

Languages(언어) 엔드포인트에는 구독 키가 필요하지 않으며 해당 사용량은 할당량에 계산되지 않습니다. 해당 URI는 `https://dev.microsofttranslator.com/languages`이며 결과는 JSON 형식으로 반환됩니다.

아래에 표시된 `MainWindow.xaml.cs`의 `UpdateLanguageSettingsAsync()` 메서드는 Languages 엔드포인트를 호출하여 지원되는 언어 목록을 가져옵니다.

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

이 메서드는 먼저 3개의 언어 목록(`text`, `speech` 및 `tts`)을 모두 요청하여 Languages 엔드포인트에 대한 HTTP 요청을 구성합니다.

Languages 엔드포인트는 요청의 `Accept-Languages` 헤더를 사용하여 언어 이름이 표현되는 언어를 결정합니다. 예를 들어 영어 화자에게 "독일어"로 알려진 언어는 독일어로 "Deutsch", 스페인어로 "Alemán"이라고 하며, 언어 목록에서는 이러한 차이를 반영합니다. 이 헤더에는 시스템의 기본 언어가 사용됩니다.

요청을 보내고 JSON 응답을 받으면 응답이 내부 데이터 구조로 구문 분석됩니다. 그런 다음, 이러한 구조는 [소스 언어] 및 [대상 언어] 메뉴를 구성하는 데 사용됩니다.

사용자가 선택한 [대상 언어]에 따라 사용할 수 있는 음성이 달라지기 때문에 아직은 [음성] 메뉴를 설정할 수 없습니다. 대신, 각 언어에 사용할 수 있는 음성은 나중에 사용하기 위해 저장됩니다. 나중에 사용자가 [대상 언어]를 선택할 때 `ToLanguage_SelectionChanged` 처리기(동일한 소스 파일에 있음)에서 `UpdateVoiceComboBox()`를 호출하여 [음성] 메뉴를 업데이트합니다.

단순히 재미를 위해, 사용자가 앞에서 애플리케이션을 실행하지 않은 경우 [대상 언어]가 임의로 선택됩니다. (메뉴 설정은 세션 간에 저장됩니다.)

## <a name="authenticating-requests"></a>요청 인증

Microsoft Translator Speech 서비스를 인증하려면 Azure 구독 키를 연결 요청의 `Ocp-Apim-Subscription-Key` 값으로 헤더에 보내야 합니다.

## <a name="translation-overview"></a>변환 개요

Translate API(`wss://dev.microsofttranslator.com/speech/translate` WebSockets 엔드포인트)는 오디오를 모노포닉, 16kHz, 부호 있는 16비트 WAVE 형식으로 변환할 수 있도록 합니다. 서비스에서 인식된 텍스트와 변환된 텍스트가 모두 포함된 하나 이상의 JSON 응답을 반환합니다. 텍스트 음성 변환이 요청되면 오디오 파일이 보내집니다.

사용자는 [마이크/파일 입력] 메뉴를 사용하여 오디오 소스를 선택합니다. 오디오는 오디오 디바이스(예: 마이크) 또는 `.WAV` 파일에서 나올 수 있습니다.

사용자가 [시작] 단추를 클릭하면 `StartListening_Click` 메서드가 호출됩니다. 그러면 이 이벤트 처리기에서 `Connect()`를 호출하여 오디오를 서비스 API 엔드포인트로 보내는 프로세스를 시작합니다. `Connect()` 메서드에서 수행하는 작업은 다음과 같습니다.


> [!div class="checklist"]
> * 주 창에서 사용자 설정 가져오기 및 유효성 검사
> * 오디오 입력 및 출력 스트림 초기화
> * `ConnectAsync()`를 호출하여 나머지 작업을 처리

다음으로, `ConnectAsync()`에서 수행하는 사소한 작업은 다음과 같습니다.

> [!div class="checklist"]
> * `Ocp-Apim-Subscription-Key` 헤더의 Azure 구독 키로 인증
> * 서비스와 통신하기 위한 `SpeechClient` 인스턴스 만들기(`SpeechClient.cs`에 있음)
> * 응답을 처리하기 위한 `TextMessageDecoder` 및 `BinaryMessageDecoder` 인스턴스 초기화(`SpeechResponseDecoder.cs` 참조)
> * `SpeechClient` 인스턴스를 통해 오디오를 Translator Speech 서비스로 보내기
> * 변환 결과 수신 및 처리

부담이 더 적은 `SpeechClient`의 임무는 다음과 같습니다.

> [!div class="checklist"]
> * Translator Speech 서비스에 대한 WebSocket 연결 설정
> * 소켓을 통해 오디오 데이터 보내기 및 응답 받기

## <a name="a-closer-look"></a>자세히 보기

이제 애플리케이션의 일부가 연동하여 변환 요청을 수행하는 방법을 명확히 해야 합니다. 관련 부분에 집중하여 몇 가지 코드를 살펴보겠습니다.

다음은 오디오 스트림 설정을 보여 주는 `Connect()`의 일부 버전입니다.

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };

    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

`Connect()`의 상당한 부분은 변환 옵션을 유지하는 `SpeechClientOptions` 인스턴스(`SpeechClientOptions.cs` 참조)를 만드는 것과 관련되어 있습니다. 옵션에는 서비스에 연결하는 데 필요한 정보(예: 인증 키 및 호스트 이름)와 변환에 사용되는 기능이 포함됩니다. 여기서는 필드가 [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)로 노출되는 헤더 필드와 HTTP 매개 변수에 매핑됩니다.

또한 `Connect()`는 변환할 음성 소스로 사용되는 오디오 입력 디바이스(`sampleProvider` 변수)를 만들고 초기화합니다. 이 디바이스는 마이크와 같은 하드웨어 입력 디바이스 또는 WAVE 오디오 데이터가 포함된 파일입니다.

다음은 `speechClient` 클래스를 인스턴스화하고, 익명 함수를 후크하여 서비스의 텍스트 및 이진 응답을 처리하는 `ConnectAsync()` 메서드입니다.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);

    TextMessageDecoder textDecoder;

    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);

    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected.
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

인증되면 메서드에서 `SpeechClient` 인스턴스를 만듭니다. `SpeechClient` 클래스(`SpeechClient.cs`에 있음)는 수신 이진 및 텍스트 데이터에 대한 이벤트 처리기를 호출합니다. 연결이 실패하거나 연결이 끊어지면 추가 처리기가 호출됩니다.

이진 데이터는 TTS가 활성화되었을 때 서비스에서 보낸 오디오(텍스트 음성 변환 출력)입니다. 텍스트 데이터는 음성 텍스트의 부분 또는 전체 변환입니다. 따라서 인스턴스화되면 이러한 메시지를 처리하는 함수(나중에 재생할 수 있도록 오디오를 저장하고 텍스트를 창에 표시)를 후크합니다.

## <a name="next-steps"></a>다음 단계

이 코드 샘플은 Translator Speech API를 사용하는 방법을 보여 주는 다양한 기능을 갖춘 애플리케이션입니다. 따라서 이해하기 위해 이동해야 하는 부분이 상당히 많습니다. 여기서는 가장 중요한 부분을 안내했습니다. 나머지 부분의 경우 Visual Studio에서 몇 가지 중단점을 설정하고 변환 프로세스를 진행하는 것이 좋습니다. 샘플 애플리케이션을 이해하면 자신의 애플리케이션에서 Translator Speech 서비스를 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Microsoft Translator Speech API 참조](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
