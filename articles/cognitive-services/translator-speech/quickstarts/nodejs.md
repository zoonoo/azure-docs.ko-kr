---
title: Azure Cognitive Services용 Node.js 빠른 시작, Microsoft Translator Speech API | Microsoft Docs
description: Azure의 Microsoft Cognitive Services에서 Microsoft Translator Speech API를 사용하여 빠르게 시작할 수 있도록 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: d469fa008ba8acaf505fa09596dd739d5cc7744c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373926"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-nodejs"></a>Node.js를 사용한 Microsoft Translator Speech API 빠른 시작 
<a name="HOLTop"></a>

이 문서에서는 Microsoft Translator Speech API를 사용하여 .wav 파일에서 발화된 단어를 변환하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Node.js 6](https://nodejs.org/en/download/)이 필요합니다.

Node.js용 [Websocket 패키지](https://www.npmjs.com/package/websocket)를 설치해야 합니다.

아래 코드에서 컴파일하는 실행 파일과 동일한 폴더에 "speak.wav"라는 .wav 파일이 필요합니다. 이 .wav 파일은 표준 PCM, 16비트, 16kHz, 모노 형식이어야 합니다. 이러한 .wav 파일은 [Translator Text Speak API](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak)에서 가져올 수 있습니다.

**Microsoft Translator Speech API**와 함께 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. [Azure 대시보드](https://portal.azure.com/#create/Microsoft.CognitiveServices)에서 유료 구독 키가 필요합니다.

## <a name="translate-speech"></a>음성 변환

다음 코드에서는 음성을 한 언어에서 다른 언어로 변환합니다.

1. 즐겨찾는 IDE에 새 Node.js 프로젝트를 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `key` 값을 구독에 대해 유효한 액세스 키로 바꿉니다.
4. 프로그램을 실행합니다.

```nodejs
/* To install this dependency, run:
npm install websocket
*/
var wsClient = require('websocket').client;
var fs = require('fs');
/* To install this dependency, run:
npm install stream-buffers
*/
var streamBuffers = require('stream-buffers');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let key = 'ENTER KEY HERE';

let host = 'wss://dev.microsofttranslator.com';
let path = '/speech/translate';
let params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa';
let uri = host + path + params;

/* The input .wav file is in PCM 16bit, 16kHz, mono format.
You can obtain such a .wav file using the Translator Text Speak API. See:
http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak
*/
let input_path = 'speak.wav';

let output_path = 'speak2.wav';

function receive(message) {
    console.log ("Received message of type: " + message.type + ".");

    if (message.type == 'utf8') {
        var result = JSON.parse(message.utf8Data)
        console.log("Response type: " + result.type + ".");
        console.log("Recognized input as: " + result.recognition);
        console.log("Translation: " + result.translation);
    }
    else if (message.type == 'binary') {
/* This is needed to make sure message.binaryData is defined. See:
https://stackoverflow.com/questions/17546953/cant-access-object-property-even-though-it-exists-returns-undefined
*/
        let binary_data = JSON.parse (JSON.stringify (message.binaryData));

        if (binary_data.type == 'Buffer') {
/* Put the binary data in a Buffer - do not write it directly to the file. */
            let buffer = new Buffer(binary_data.data, 'binary');
/* Write the contents of the Buffer to the file. */
            fs.writeFile (output_path, buffer, 'binary', function (error) {
/* fs.writeFile calls the error-handling function even if there is no error, in which case error === null. See:
https://stackoverflow.com/questions/44473868/node-js-fs-writefile-err-returns-null
*/
                if (error !== null) {
                    console.log ("Error: " + error);
                }
            });
        }
    }
}

function send(connection, filename) {
    
    var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
        frequency: 100,
        chunkSize: 32000
    });

/* Make sure the audio file is followed by silence.
This lets the service know that the audio input is finished. */
    myReadableStreamBuffer.put(fs.readFileSync(filename));
    myReadableStreamBuffer.put(new Buffer(3200000));
    myReadableStreamBuffer.stop();

    myReadableStreamBuffer.on('data', function (data) {
        connection.sendBytes(data);
    });

    myReadableStreamBuffer.on('end', function () {
        console.log('Closing connection.');
        connection.close(1000);
    });
}

function connect() {
    var ws = new wsClient();

    ws.on('connectFailed', function (error) {
        console.log('Connection error: ' + error.toString());
    });
                            
    ws.on('connect', function (connection) {
        console.log('Connected.');

        connection.on('message', receive);
        
        connection.on('close', function (reasonCode, description) {
            console.log('Connection closed: ' + reasonCode);
        });

        connection.on('error', function (error) {
            console.log('Connection error: ' + error.toString());
        });
        
        send(connection, input_path);
    });

    ws.connect(uri, null, null, { 'Ocp-Apim-Subscription-Key' : key });
}

connect();
```

**음성 응답 변환**

성공적인 결과는 "speak2.wav"라는 파일을 만드는 것입니다. 이 파일에는 "speak.wav"에서 발화된 단어에 대한 변환이 포함됩니다.

[맨 위로 이동](#HOLTop)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Translator Speech 자습서](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>참고 항목 

[Translator Speech 개요](../overview.md)
[API 참조](http://docs.microsofttranslator.com/speech-translate.html)
