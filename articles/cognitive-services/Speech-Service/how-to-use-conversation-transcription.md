---
title: 실시간 대화 녹음 (미리보기) - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 SDK를 사용하여 실시간 대화 기록을 사용하는 방법에 대해 알아봅니다. C ++, C#및 Java에서 사용할 수 있습니다.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: e2c9c0aadc8e443f07f60f3ccddb4a1b6dd661b1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520904"
---
# <a name="real-time-conversation-transcription-preview"></a>실시간 대화 녹음(미리 보기)

Speech SDK의 **ConversationTranscriber** API를 사용하면 을 `PullStream` `PushStream`사용하여 음성 서비스에 오디오를 스트리밍하여 여러 참가자를 추가, 제거 및 식별할 수 있는 기능으로 회의 및 기타 대화를 전사할 수 있습니다. 이 항목에서는 음성 SDK(버전 1.8.0 이상)에서 음성-텍스트 사용 방법을 알아야 합니다. 자세한 내용은 [음성 서비스 입니다.](overview.md)

## <a name="limitations"></a>제한 사항

- 대화전사 API는 윈도우, 리눅스, 안드로이드에서 C ++, C #, 자바에 지원됩니다.
- 현재 "en-US" 및 "zh-CN" 언어로 다음 지역에서 사용할 수 있습니다: _중앙 및_ _동아시아._
- 재생 참조 스트림이 있는 7마이크 원형 멀티 마이크 어레이가 필요합니다. 마이크 어레이는 [당사사양을](https://aka.ms/sdsdk-microphone)충족해야 합니다.
- [음성 장치 SDK는](speech-devices-sdk.md) 적절한 장치와 대화 녹음을 보여주는 샘플 응용 프로그램을 제공합니다.

## <a name="optional-sample-code-resources"></a>샘플 코드 리소스 옵션

음성 장치 SDK는 8채널을 사용하여 실시간 오디오 캡처를 위해 Java의 샘플 코드를 제공합니다.

- [ROOBO 장치 샘플 코드](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Azure Kinect 개발자 키트 샘플 코드](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>사전 요구 사항

음성 서비스 구독입니다. [스피치 평가판 구독이](https://azure.microsoft.com/try/cognitive-services/) 없는 경우 음성 평가판 구독을 받을 수 있습니다.

## <a name="create-voice-signatures"></a>음성 서명 만들기

첫 번째 단계는 효율적인 스피커 식별을 위해 대화 참가자를 위한 음성 서명을 만드는 것입니다.

### <a name="audio-input-requirements"></a>오디오 입력 요구 사항

- 음성 서명을 만들기 위한 입력 오디오 웨이브 파일은 16비트 샘플, 16kHz 샘플 속도 및 단일 채널(모노) 형식이어야 합니다.
- 각 오디오 샘플의 권장 길이는 30초에서 2분 사이입니다.

### <a name="sample-code"></a>예제 코드

다음 예제에서는 C#에서 [REST API를 사용하여](https://aka.ms/cts/signaturegenservice) 음성 서명을 만드는 두 가지 방법을 보여 주십습니다. "YourSubscriptionKey", "speakerVoice.wav"의 웨이브 파일 이름 및 "YourServiceRegion"(중앙 또는 `{region}` _동아시아)에_대한_centralus_ 실제 정보를 대체해야 합니다.

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>대화 전사

다음 샘플 코드에서는 세 명의 스피커에 대해 실시간으로 대화를 전사하는 방법을 보여 줍니다. 위와 같이 각 스피커에 대해 음성 서명을 이미 만들었다고 가정합니다. SpeechConfig 개체를 만들 때 "YourSubscriptionKey" 및 "YourServiceRegion"에 대한 실제 정보를 대체합니다.

샘플 코드 하이라이트는 다음과 같습니다.

- 다음을 `Conversation` 사용하여 `SpeechConfig` 생성된 모임 식별자를 사용하여 개체에서 개체 만들기`Guid.NewGuid()`
- 개체 `ConversationTranscriber` 만들기 및 대화에 `JoinConversationAsync()` 참여하여 전사 시작
- 관심 이벤트 등록
- 대화 개체를 사용하여 대화에 참가자 추가 또는 제거
- 오디오 스트리밍
- 음성 SDK 버전 1.9.0 및 `int` 이후 `string` 및 이후 의 값 형식은 음성 서명 버전 필드에서 지원됩니다.

전사 및 스피커 식별자는 등록된 이벤트에서 다시 돌아옵니다.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [비동기 대화 전사](how-to-async-conversation-transcription.md)
