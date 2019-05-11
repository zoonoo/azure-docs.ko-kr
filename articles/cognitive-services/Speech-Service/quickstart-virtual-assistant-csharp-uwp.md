---
title: '빠른 시작: 사용자 지정 음성 우선 가상 도우미(미리 보기), C#(UWP) - Speech Services'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Cognitive Services Speech SDK(소프트웨어 개발 키트)를 사용하여 C# UWP(유니버설 Windows 플랫폼) 애플리케이션을 만듭니다. 이전에 Direct Line Speech 채널을 사용하도록 만들고 구성한 Bot Framework에 클라이언트 애플리케이션을 연결합니다. 이 애플리케이션은 Speech SDK NuGet 패키지 및 Microsoft Visual Studio 2017로 빌드되었습니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: e2b25875a0dff12bba32b033bca0c35394d407aa
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465638"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>빠른 시작: Speech SDK UWP를 사용하여 음성 우선 가상 도우미 만들기

빠른 시작은 [음성 텍스트 변환](quickstart-csharp-uwp.md) 및 [음성 번역](quickstart-translate-speech-uwp.md)에도 사용할 수 있습니다.

이 문서에서는 [Speech SDK](speech-sdk.md)를 사용하여 C# UWP(유니버설 Windows 플랫폼) 애플리케이션을 개발할 것입니다. 이 프로그램은 이전에 클라이언트 애플리케이션의 음성 우선 가상 도우미 환경을 사용하도록 만들고 구성한 봇에 연결됩니다. 이 애플리케이션은 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017(모든 버전)로 빌드되었습니다.

> [!NOTE]
> 유니버설 Windows 플랫폼을 사용하여 PC, Xbox, Surface Hub 및 기타 디바이스를 비롯하여 Windows 10을 지원하는 모든 디바이스에서 실행되는 앱을 개발할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Speech Service에 대한 Azure 구독 키 [무료로 가져올 수 있습니다](get-started.md).
* 이전에 [Direct Line Speech 채널](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)을 사용하여 만들고 구성한 봇

    > [!NOTE]
    > 미리 보기로 제공되는 Direct Line Speech 채널은 현재 **westus2** Azure 지역만 지원합니다.

    > [!NOTE]
    > [Speech Service 평가판 사용해 보기](get-started.md)에 설명된 표준 가격 계층의 30일 평가판은 **westus**(**westus2** 아님)로 제한되며, 따라서 Direct Line Speech와 호환되지 않습니다. 무료 및 표준 계층 **westus2** 구독은 호환됩니다.

## <a name="optional-get-started-fast"></a>선택 사항: 빠른 시작

이 빠른 시작에서는 음성 지원 봇에 연결하는 간단한 클라이언트 애플리케이션을 만드는 방법을 단계별로 설명합니다. 바로 시작하려면 이 빠른 시작에 사용되는 즉시 컴파일 가능한 완전한 소스 코드를 사용하면 됩니다. 이 소스 코드는 `quickstart` 폴더의 [Speech SDK 샘플](https://aka.ms/csspeech/samples) 아래에 있습니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. 애플리케이션의 사용자 인터페이스는 XAML을 사용하여 정의됩니다. 솔루션 탐색기에서 `MainPage.xaml`을 엽니다. 디자이너의 XAML 보기에서 전체 콘텐츠를 아래 내용으로 바꿉니다.

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    
        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" Margin="0,10,10,0" Click="ListenButton_ButtonClicked" Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" RelativePanel.AlignBottomWithPanel="True" RelativePanel.AlignRightWithPanel="True" RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce the status update. -->
                            <TextBlock x:Name="StatusBlock" FontWeight="Bold" AutomationProperties.LiveSetting="Assertive"
                    MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

1. `MainPage.xaml.cs` 코드 숨김 원본 파일을 엽니다. `MainPage.xaml` 아래에 그룹화되어 있습니다. 내용을 아래 코드로 바꿉니다. 이 샘플에서 다루는 내용은 다음과 같습니다. 

    * Speech 및 Speech.Dialog 네임스페이스에 대한 using 문
    * 단추 처리기에 연결된 마이크 액세스를 보장하는 간단한 구현
    * 애플리케이션에서 메시지 및 오류를 표시하는 기본 UI 도우미
    * 나중에 채울 초기화 코드 경로에 대한 시작 지점
    * 텍스트 음성 변환을 재생할 수 있는 도우미(스트리밍 지원 없음)
    * 나중에 채울 수신 대기를 시작하는 빈 단추 처리기

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private SpeechBotConnector botConnector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for accumulates all audio associated with a given PullAudioOutputStream and then plays it to the
            // MediaElement. Long spoken audio will create extra latency and a streaming playback solution (that plays
            // audio while it continues to be received) should be used -- see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeBotConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. 다음으로, 구독 정보를 사용하여 `SpeechBotConnector`를 만듭니다. 다음을 `InitializeBotConnector`의 메서드 본문에 추가하고, `YourChannelSecret`, `YourSpeechSubscriptionKey` 및 `YourServiceRegion` 문자열을 사용자 고유의 봇, 음성 구독 및 [Azure 지역](regions.md) 값으로 바꿉니다.

    > [!NOTE]
    > 미리 보기로 제공되는 Direct Line Speech 채널은 현재 **westus2** Azure 지역만 지원합니다.

    > [!NOTE]
    > 봇을 구성하고 채널 비밀을 검색하는 방법은 [Direct Line Speech 채널](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)에 대한 Bot Framework 설명서를 참조하세요.

    ```csharp
    // create a BotConnectorConfig by providing a bot secret key and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret";
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey";
    const string region = "YourServiceRegion"; // note: this is assumed as westus2 for preview

    var botConnectorConfig = BotConnectorConfig.FromSecretKey(channelSecret, speechSubscriptionKey, region);
    botConnectorConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    botConnector = new SpeechBotConnector(botConnectorConfig);
    ```

1. `SpeechBotConnector`는 여러 이벤트를 사용하여 봇 작업, 음성 인식 결과 및 기타 정보를 전달합니다. 이러한 이벤트에 대한 처리기를 추가하고, `InitializeBotConnector`의 메서드 본문 끝에 다음을 추가합니다.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };
    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser($"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };
    // Recognizing (not 'Recognized') will provide the intermediate recognized text while an audio stream is being processed
    botConnector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };
    // Recognized (not 'Recognizing') will provide the final recognized text once audio capture is completed
    botConnector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };
    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };
    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. 구성을 설정하고 이벤트 처리기를 등록한 후에는 `SpeechBotConnector`가 수신 대기하기만 하면 됩니다. `MainPage` 클래스의 `ListenButton_ButtonClicked` 메서드 본문에 다음을 추가합니다.

    ```csharp
    private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
    {
        if (botConnector == null)
        {
            InitializeBotConnector();
            // Optional step to speed up first interaction: if not called, connection happens automatically on first use
            var connectTask = botConnector.ConnectAsync();
        }

        try
        {
            // Start sending audio to your speech-enabled bot
            var listenTask = botConnector.ListenOnceAsync();

            // You can also send activities to your bot as JSON strings -- Microsoft.Bot.Schema can simplify this
            string speakActivity = @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
            await botConnector.SendActivityAsync(speakActivity);

        }
        catch (Exception ex)
        {
            NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
        }
    }
    ```

1. 프로젝트에 대한 모든 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 애플리케이션을 빌드합니다. Visual Studio의 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 이제 오류 없이 컴파일됩니다.

    ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-csharp-uwp-08-build.png "성공적인 빌드")

1. 애플리케이션을 시작합니다. Visual Studio의 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

    ![디버깅 시작 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-csharp-uwp-09-start-debugging.png "앱 디버깅 시작")

1. 창이 팝업됩니다. 애플리케이션에서 **마이크 사용**을 선택하고, 팝업되는 권한 요청을 승인합니다.

    ![권한 요청 스크린샷](media/sdk/qs-csharp-uwp-10-access-prompt.png "앱 디버깅 시작")

1. **봇에게 말하기**를 선택하고, 디바이스의 마이크에 영어로 짧은 구나 문장을 말합니다. 음성은 Direct Line Speech 채널로 전송되어 텍스트로 전사되고 창에 표시됩니다.
quickstart-cs-uwp-bot-successful-turn

    ![성공적인 봇 턴의 스크린샷](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png "성공적인 봇 턴의 스크린샷")

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 C# 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [음성 번역](how-to-translate-speech-csharp.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
