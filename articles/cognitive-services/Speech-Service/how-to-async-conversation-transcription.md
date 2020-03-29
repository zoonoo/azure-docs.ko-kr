---
title: 비동기 대화 녹음 (미리보기) - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 서비스를 사용하여 비동기 대화 기록을 사용하는 방법에 대해 알아봅니다. 자바에서만 사용할 수 있습니다.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: c1f0110c83eb42aaedbd36736946ae3faff58699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80366615"
---
# <a name="asynchronous-conversation-transcription-preview"></a>비동기 대화 녹음(미리 보기)

이 문서에서는 비동기 대화 전사는 **RemoteConversationTranscriptionClient** API를 사용하여 보여 주었습니다. 비동기 전사를 수행하도록 대화 전사를 구성하고 `conversationId`있는 경우 , **RemoteConversationTranscriptionClient** API를 `conversationId` 사용하여 해당와 관련된 전사를 얻을 수 있습니다.

## <a name="asynchronous-vs-real-time--asynchronous"></a>비동기 대 실시간 + 비동기

비동기 전사를 사용하면 대화 오디오를 스트리밍할 수 있지만 실시간으로 반환되는 전사가 필요하지 않습니다. 대신 오디오를 보낸 후 를 `conversationId` `Conversation` 사용하여 비동기 전사 의 상태를 쿼리합니다. 비동기 전사가 준비되면 `RemoteConversationTranscriptionResult`을 받게 됩니다.

실시간 플러스 비동기를 사용하면 실시간으로 전사를 얻을 수 있을 뿐만 아니라 `conversationId` (비동기 시나리오와 유사)로 쿼리하여 전사를 얻을 수도 있습니다.

비동기 전사를 수행하려면 두 단계가 필요합니다. 첫 번째 단계는 오디오를 업로드하여 비동기 전용 또는 실시간 플러스 비동기를 선택하는 것입니다. 두 번째 단계는 전사 결과를 얻는 것입니다.

## <a name="upload-the-audio"></a>오디오 업로드

비동기 전사를 수행하기 전에 Microsoft 인지 음성 클라이언트 SDK(버전 1.8.0 이상)를 사용하여 오디오를 대화 전사 서비스로 보내야 합니다.

이 예제 코드는 비동기 전용 모드에 대한 대화 전사를 만드는 방법을 보여 주었습니다. 오디오를 전사로 스트리밍하려면 [Speech SDK를 사용하여 실시간으로 Transcribe 대화에서](how-to-use-conversation-transcription-service.md)파생된 오디오 스트리밍 코드를 추가해야 합니다. 지원되는 플랫폼 및 언어 API를 보려면 해당 항목의 **제한** 섹션을 참조하십시오.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

실시간 _플러스_ 비동기를 원하는 경우 다음과 같이 적절한 코드 줄에 주석을 달고 주석을 달고 주석을 달지 않습니다.

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>전사 결과 얻기

이 단계는 비동기 전사 결과를 얻지만 필요한 실시간 처리가 다른 곳에서 수행된다고 가정합니다. 자세한 내용은 [음성 SDK를 통해 실시간으로 대화를 전사하는](how-to-use-conversation-transcription-service.md)것을 참조하십시오.

여기에 표시된 코드의 경우 **원격 대화 버전 1.8.0이**필요하며 Windows, Linux 및 Android에서 Java(1.8.0 이상)에서만 지원됩니다(API 수준 26 이상만).

### <a name="obtaining-the-client-sdk"></a>클라이언트 SDK 획득

다음과 같이 pom.xml 파일을 편집하여 **원격 대화를** 얻을 수 있습니다.

1. 파일의 끝에서 닫는 태그 `</project>`앞에 Speech `repositories` SDK에 대한 Maven 리포지토리를 참조하는 요소를 만듭니다.

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. 또한 리모컨-클라이언트-sdk 1.8.0을 종속성으로 사용하여 `dependencies` 요소를 추가합니다.

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. 변경 내용 저장

### <a name="sample-transcription-code"></a>샘플 전사 코드

`conversationId`을 사용 하면 원격 대화 전사 클라이언트 **RemoteConversationTranscription** 클라이언트 클라이언트 응용 프로그램에서 비동기 전사의 상태를 쿼리 합니다. **RemoteConversationTranscriptionClient에서** **getTranscription운영** 방법을 사용하여 [PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) 개체를 가져옵니다. PollerFlux 개체에는 원격 작업 상태 **원격대화 전사작업** 및 최종 **결과**결과에 대한 정보가 있습니다. 작업이 완료되면, 얻을 **원격대화TranscriptiongetgetFinalResult** [에 동기화 폴러](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). **getFinalResult** 이 코드에서는 결과 내용을 시스템 출력에 인쇄하기만 하면 됩니다.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 샘플 살펴보기](https://aka.ms/csspeech/samples)
