---
title: 비동기 대화 기록 (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 서비스를 사용 하 여 비동기 대화 기록을 사용 하는 방법을 알아봅니다. Java에만 사용할 수 있습니다.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 9a2a00a8c76c7b5c02cde623bf93f536b27cf074
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621961"
---
# <a name="asynchronous-conversation-transcription-preview"></a>비동기 대화 기록 (미리 보기)

이 문서에서는 **RemoteConversationTranscriptionClient** API를 사용 하 여 비동기 대화 기록을 보여 줍니다. 비동기 기록을 수행 하 고 `conversationId`있는 대화 기록을 구성한 경우 **RemoteConversationTranscriptionClient** API를 사용 하 여 해당 `conversationId`와 관련 된 기록을 가져올 수 있습니다.

## <a name="asynchronous-vs-real-time--asynchronous"></a>비동기 및 실시간 + 비동기

비동기 기록을 사용 하면 대화 오디오를 스트리밍하 고 실시간으로 반환 되는 기록을 필요로 하지 않습니다. 대신, 오디오를 보낸 후 `Conversation` `conversationId`을 사용 하 여 비동기 기록의 상태를 쿼리 합니다. 비동기 기록을 준비 하면 `RemoteConversationTranscriptionResult`을 받게 됩니다.

실시간 및 비동기를 사용 하면 실시간으로 기록을 가져올 수 있지만, `conversationId` (비동기 시나리오와 유사)를 쿼리하여 기록을 가져올 수도 있습니다.

비동기 기록을 수행 하려면 두 단계가 필요 합니다. 첫 번째 단계는 비동기 전용 또는 실시간 및 비동기를 선택 하 여 오디오를 업로드 하는 것입니다. 두 번째 단계는 기록 결과를 가져오는 것입니다.

## <a name="upload-the-audio"></a>오디오 업로드

비동기 기록을 수행 하려면 먼저 Microsoft 인식 음성 클라이언트 SDK (버전 1.8.0 이상)를 사용 하 여 대화 기록 서비스로 오디오를 전송 해야 합니다.

이 예제 코드는 비동기 전용 모드에 대해 대화 transcriber을 만드는 방법을 보여 줍니다. 오디오를 transcriber로 스트리밍하려면 [음성 SDK를 사용 하 여 높여줄 대화](how-to-use-conversation-transcription-service.md)에서 파생 된 오디오 스트리밍 코드를 실시간으로 추가 해야 합니다. 지원 되는 플랫폼 및 언어 Api를 보려면 해당 항목의 **제한 사항** 섹션을 참조 하세요.

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
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16Khz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16Khz, 16 bits and 8 channel pcm wav file
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

실시간 _및_ 비동기를 원하는 경우 다음과 같이 적절 한 코드 줄을 주석 처리 하 고 주석 처리를 제거 합니다.

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>기록 결과 가져오기

이 단계는 비동기 기록 결과를 가져오지만 필요한 모든 실시간 처리를 다른 곳에서 수행 한다고 가정 합니다. 자세한 내용은 [음성 SDK를 사용 하 여 실시간으로 대화 높여줄](how-to-use-conversation-transcription-service.md)를 참조 하세요.

여기에 표시 된 코드의 경우 Windows, Linux 및 Android에서 Java (1.8.0 이상)에 대해서만 지원 되는 **원격 대화 버전 1.8.0**필요 합니다 (API 레벨 26 이상에만 해당).

### <a name="obtaining-the-client-sdk"></a>클라이언트 SDK 가져오기

다음과 같이 pom .xml 파일을 편집 하 여 **원격 대화** 를 가져올 수 있습니다.

1. 파일의 끝에서 닫는 태그 `</project>`전에 Speech SDK의 Maven 리포지토리에 대 한 참조를 사용 하 여 `repositories` 요소를 만듭니다.

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. 또한 remoteconversation 1.8.0을 종속성으로 사용 하 여 `dependencies` 요소를 추가 합니다.

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

### <a name="sample-transcription-code"></a>샘플 기록 코드

`conversationId`된 후 클라이언트 응용 프로그램에서 원격 대화 기록 클라이언트 **RemoteConversationTranscriptionClient** 를 만들어 비동기 기록의 상태를 쿼리 합니다. RemoteConversationTranscriptionClient에서 **Getge Tionoperation** 메서드 를 사용 하 여 [PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) 개체를 가져옵니다. PollerFlux 개체에는 원격 작업 상태 **RemoteConversationTranscriptionOperation** 및 최종 결과 **RemoteConversationTranscriptionResult**에 대 한 정보가 포함 됩니다. 작업이 완료 되 면 [Syncpoller 러 러](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java)에 **getRemoteConversationTranscriptionResult result** 를 호출 하 여 get을 가져옵니다. 이 코드에서는 결과 내용을 시스템 출력으로 출력 합니다.

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
