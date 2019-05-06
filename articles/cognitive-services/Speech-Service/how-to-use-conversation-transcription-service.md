---
title: Speech SDK-음성 서비스를 사용 하 여 다중 참가자 대화 기록
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용 하 여 대화 기록 서비스를 사용 하는 방법에 알아봅니다. 에 사용할 수 있는 C++, C#, 및 Java 합니다.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: jhakulin
ms.openlocfilehash: 73ab4cfa92a1efc49dea16ba2941cf16b7a1cf3e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026709"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Speech SDK를 사용 하 여 다중 참가자 대화 기록

Speech SDK **ConversationTranscriber** API를 사용 하면 추가, 제거 및 스트리밍 오디오 음성 서비스 사용 하 여 참가자를 식별 하는 기능을 사용 하 여 회의/대화를 촬영할 수 `PullStream` 또는`PushStream`.

## <a name="limitations"></a>제한 사항

* 대화 필기 인식기 대해서는 C++, C#, 및 Windows, Linux 및 Android에서 Java 합니다.
* ROOBO DevKit는 순환 다중 마이크 배열 화자 식별에 대 한 대화 기록 서비스에서 효율적으로 활용할 수 있는 기능을 제공 하는 대화를 만들기 위한 지원 되는 하드웨어 환경입니다. [자세한 내용은 음성 장치 SDK를 참조 하세요.](speech-devices-sdk.md)합니다. 
* Speech SDK 지원은 오디오 끌어오기를 사용 하 여 PCM 오디오 8 채널을 사용 하 여 모드 스트림을 푸시 제한 됩니다.

## <a name="prerequisites"></a>필수 조건

* [Speech SDK를 사용 하 여 음성-텍스트를 사용 하는 방법에 알아봅니다.](quickstart-csharp-dotnet-windows.md)
* [음성 평가판 구독을 가져옵니다.](https://azure.microsoft.com/try/cognitive-services/)

## <a name="create-voice-signatures-for-participants"></a>참가자에 대 한 음성 시그니처를 만듭니다

첫 번째 단계 대화 참가자에 대 한 음성 시그니처를 만드는 것입니다. 음성 서명을 만드는 것이 효율적인 화자 식별 필요 합니다.
에서는 다음 샘플에서는 [REST API를 사용 하 여 음성 시그니처를 가져옵니다.](https://aka.ms/cts/signaturegenservice)

아래 예제에서는 음성 서명을 만드는 두 가지 방법을 보여 줍니다.
```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.signature.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature can be extracted from the jsonData
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature can be extracted from the jsonData
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>대화 복사

여러 참가자와 대화를 촬영할 수 만들기는 `ConversationTranscriber` 연관 된 개체를 `AudioConfig` conversation 세션 및 스트림에 오디오를 사용 하 여 만든 개체 `PullAudioInputStream` 또는 `PushAudioInputStream`.

호출 ConversationTranscriber 클래스가 있다고 가정해 보겠습니다 `MyConversationTranscriber`합니다. 코드는이 처럼 보일 수 있습니다. 

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    private static string endpoint = "YourOwnEndpoint";

    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own endpoint and subscription key.
        var config = SpeechConfig.FromEndpoint(new Uri(endpoint), "YourSubScriptionKey");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback 
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, SpeakerID={e.Result.SpeakerId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create data for voice signatures using REST API described in the earlier section in this document.
                // How to create voice signatureA, signatureB & signatureC variables, please check the SDK API samples.

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 샘플 살펴보기](https://aka.ms/csspeech/samples)
