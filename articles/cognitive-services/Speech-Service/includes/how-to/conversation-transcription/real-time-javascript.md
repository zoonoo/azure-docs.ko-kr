---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: d2fae1c08132c855f570750a4528a9a37343434e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104678"
---
## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript용 Speech SDK</a>를 설치해야 합니다. 사용하는 플랫폼에 따라 다음 중 적절한 지침을 따릅니다.

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">웹 브라우저 </a>

## <a name="create-voice-signatures"></a>음성 서명 만들기

첫 번째 단계는 고유 스피커로 식별할 수 있도록 대화 참가자의 음성 서명을 만드는 것입니다. 음성 서명을 만들기 위한 입력 `.wav` 오디오 파일은 16비트, 16Khz 샘플 속도 및 단일 채널(모노) 형식이어야 합니다. 각 오디오 샘플의 권장 길이는 30초~2분 사이입니다. `.wav` 파일은 고유한 음성 프로필이 생성되도록 **한 사람의** 음성 샘플이어야 합니다.

다음 예제에서는 JavaScript에서 [REST API를 사용](https://aka.ms/cts/signaturegenservice)하여 음성 서명을 만드는 방법을 보여 줍니다. `subscriptionKey`, `region` 및 샘플 `.wav` 파일 경로의 실제 정보를 대체해야 합니다.

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

이 스크립트를 실행하면 `voiceSignatureString` 변수의 음성 서명 문자열이 반환됩니다. 함수를 두 번 실행하면 아래 `voiceSignatureStringUser1` 및 `voiceSignatureStringUser2` 변수에 대한 입력으로 두 개의 문자열을 사용할 수 있습니다.

> [!NOTE]
> 음성 서명을 만들려면 REST API를 사용 **해야 합니다**.

## <a name="transcribe-conversations"></a>대화 기록

다음 샘플 코드에서는 두 개의 스피커에 대해 실시간으로 대화를 기록하는 방법을 보여 줍니다. 위에 표시된 대로 각 스피커에 대한 음성 서명 문자열을 이미 만들었다고 가정합니다. 기록할 오디오에 관한 `subscriptionKey`, `region` 및 경로 `filepath`의 실제 정보를 대체합니다.

이 샘플 코드에서는 다음을 수행합니다.

* 대화 내용 기록에 사용할 푸시 스트림을 만들고 여기에 샘플 `.wav` 파일을 씁니다.
* `createConversationAsync()`를 사용하여 `Conversation`을 만듭니다.
* 생성자를 사용하여 `ConversationTranscriber`를 만듭니다.
* 대화에 참가자를 추가합니다. 문자열 `voiceSignatureStringUser1` 및 `voiceSignatureStringUser2`는 위의 단계에서 출력으로 제공됩니다.
* 이벤트에 등록하고 대화 내용 기록을 시작합니다.

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```