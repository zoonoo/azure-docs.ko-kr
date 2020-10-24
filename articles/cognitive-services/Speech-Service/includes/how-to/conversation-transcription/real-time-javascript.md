---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: 7ab4d7708dd75ff2e07e77f3be4f996068704797
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486792"
---
## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript용 Speech SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>를 설치해야 합니다. 사용하는 플랫폼에 따라 다음 중 적절한 지침을 따릅니다.

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">웹 브라우저 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-voice-signatures"></a>음성 서명 만들기

첫 번째 단계는 대화 참가자에 대 한 음성 서명을 만들어 고유 스피커로 식별할 수 있도록 하는 것입니다. `.wav`음성 서명을 만들기 위한 입력 오디오 파일은 16 비트, 16Khz 샘플 요금 및 단일 채널 (mono) 형식 이어야 합니다. 각 오디오 샘플의 권장 길이는 30 초에서 2 분 사이입니다. `.wav`이 파일은 고유한 음성 프로필을 만들도록 **한 사람의** 음성 샘플 이어야 합니다.

다음 예제에서는 JavaScript에서 [REST API를 사용](https://aka.ms/cts/signaturegenservice) 하 여 음성 서명을 만드는 방법을 보여 줍니다. `subscriptionKey`, 및 샘플 파일의 경로에 대 한 실제 정보를 대체 해야 `region` `.wav` 합니다.

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

이 스크립트를 실행 하면 변수에 음성 서명 문자열이 반환 `voiceSignatureString` 됩니다. 함수를 두 번 실행 하 여 변수 및 아래에 대 한 입력으로 사용할 문자열 두 개를 갖도록 `voiceSignatureStringUser1` `voiceSignatureStringUser2` 합니다.

> [!NOTE]
> 음성 서명은 REST API를 사용 **하 여 만들 수 있습니다.**

## <a name="transcribe-conversations"></a>높여줄 대화

다음 샘플 코드에서는 두 개의 스피커에 대해 실시간으로 대화를 높여줄 하는 방법을 보여 줍니다. 위에서 설명한 것 처럼 각 스피커에 대해 음성 서명 문자열을 이미 만들었다고 가정 합니다. `subscriptionKey`, 및 높여줄 오디오의 경로에 대 한 실제 정보를 대체 `region` `filepath` 합니다.

이 샘플 코드는 다음을 수행 합니다.

* 기록에 사용할 푸시 스트림을 만들고 `.wav` 여기에 샘플 파일을 씁니다.
* 을 `Conversation` 사용 하 여를 만듭니다 `createConversationAsync()` .
* `ConversationTranscriber`생성자를 사용 하 여를 만듭니다.
* 대화에 참가자를 추가 합니다. 문자열과는 `voiceSignatureStringUser1` `voiceSignatureStringUser2` 위의 단계에서 출력으로 제공 됩니다.
* 이벤트에 등록 하 고 기록을 시작 합니다.

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