---
title: '빠른 시작: 텍스트 음성 변환, Node.js - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Node.js 및 Text-to-Speech REST API를 사용하여 텍스트를 음성으로 변환하는 방법을 알아봅니다. 이 가이드에 포함된 샘플 텍스트는 SSML(Speech Synthesis Markup Language)로 구조화되어 있습니다. 이를 통해 음성 응답의 음성 및 언어를 선택할 수 있습니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 808466e9d2546472a4f86dd0d7eacd48e75cb94a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224482"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>빠른 시작: Node.js를 사용하여 텍스트 음성 변환

이 빠른 시작에서는 Node.js 및 Text-to-Speech REST API를 사용하여 텍스트를 음성으로 변환하는 방법을 알아봅니다. 이 가이드의 요청 본문은 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)로 구조화되어 있으므로 응답의 음성 및 언어를 선택할 수 있습니다.

이 빠른 시작에는 Speech Service 리소스와 함께 [Azure Cognitive Services 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 필요합니다. 계정이 없는 경우 [평가판](get-started.md)을 사용하여 구독 키를 가져올 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [Node 8.12.x 이상](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) 또는 즐겨 사용하는 텍스트 편집기
* Speech Service에 대한 Azure 구독 키 [평가판을 가져올 수 있습니다](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>프로젝트 만들기 및 종속성 요구

즐겨 찾는 IDE 또는 편집기를 사용하여 새 Node.js 프로젝트를 만듭니다. 그런 다음, 아래 코드 조각을 `tts.js`라는 파일의 프로젝트에 복사합니다.

```javascript
// Requires request for HTTP requests
const request = require('request');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> 이러한 모듈을 사용하지 않았다면 프로그램을 실행하기 전에 설치해야 합니다. 이러한 패키지를 설치하려면 `npm install request readline-sync`를 실행합니다.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>구독 키를 설정하고 TTS 프롬프트를 만듭니다.

다음 몇 개 섹션에서는 권한 부여를 처리하고, Text-to-Speech API를 호출하고, 응답의 유효성을 검사하는 함수를 만듭니다. 구독 키를 추가하고 텍스트 입력을 위한 프롬프트를 만들어 시작해 보겠습니다.

```javascript
/*
 * These lines will attempt to read your subscription key from an environment
 * variable. If you prefer to hardcode the subscription key for ease of use,
 * replace process.env.SUBSCRIPTION_KEY with your subscription key as a string.  
 */
const subscriptionKey = process.env.SUBSCRIPTION_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};

// Prompts the user to input text.
let text = readline.question('What would you like to convert to speech? ');
```

## <a name="get-an-access-token"></a>액세스 토큰 가져오기

Text-to-Speech REST API에는 인증을 위한 액세스 토큰이 필요합니다. 액세스 토큰을 가져오려면 교환이 필요합니다. 이 샘플에서는 `issueToken` 엔드포인트를 사용하여 액세스 토큰의 Speech Service 구독 키를 교환합니다.

이 함수는 두 개의 인수, Speech Services 구독 키 및 콜백 함수를 사용합니다. 함수에서 액세스 토큰을 획득했으면 값을 콜백 함수에 전달합니다. 다음 섹션에서는 Text-to-Speech API를 호출하고 합성된 음성 응답을 저장합니다.

이 샘플에서는 Speech Service 구독이 미국 서부 지역에 있다고 가정합니다. 다른 지역을 사용하는 경우 `uri` 값을 업데이트합니다. 전체 목록은 [지역](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)을 참조하세요.

이 코드를 프로젝트에 복사합니다.

```javascript
function textToSpeech(subscriptionKey, saveAudio) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };
    // This function retrieve the access token and is passed as callback
    // to request below.
    function getToken(error, response, body) {
        console.log("Getting your token...\n")
        if (!error && response.statusCode == 200) {
            //This is the callback to our saveAudio function.
            // It takes a single argument, which is the returned accessToken.
            saveAudio(body)
        }
        else {
          throw new Error(error);
        }
    }
    request(options, getToken)
}
```

> [!NOTE]
> 인증에 대한 자세한 내용은 [액세스 토큰으로 인증](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)을 참조하세요.

## <a name="make-a-request-and-save-the-response"></a>요청을 작성하고 응답을 저장합니다.

여기서 Text-to-Speech API에 대한 요청을 작성하고 음성 응답을 저장합니다. 이 샘플에서는 사용자가 미국 서부 엔드포인트를 사용하고 있다고 가정합니다. 리소스가 다른 지역에 등록된 경우에는 `uri`을 업데이트해야 합니다. 자세한 내용은 [Speech Service 지역](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)을 참조하세요.

그런 다음, 요청에 필요한 헤더를 추가해야 합니다. Azure Portal에 있는 리소스 이름을 사용하여 `User-Agent`를 업데이트하고 `X-Microsoft-OutputFormat`을 선호하는 오디오 출력으로 설정해야 합니다. 출력 형식의 전체 목록은 [오디오 출력](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs)을 참조하세요.

그런 다음, SSML(Speech Synthesis Markup Language)을 사용하여 요청 본문을 구성합니다. 이 샘플에서는 구조체를 정의하고 이전에 만든 `text` 입력을 사용합니다.

>[!NOTE]
> 이 샘플에서는 `JessaRUS` 음성 글꼴을 사용합니다. Microsoft 제공 음성/언어의 전체 목록은 [언어 지원](language-support.md)을 참조하세요.
> 브랜드의 고유하고 인식 가능한 음성을 만들려면 [사용자 지정 음성 글꼴 만들기](how-to-customize-voice-font.md)를 참조하세요.

마지막으로 서비스에 대한 요청을 만듭니다. 요청이 성공하고 200 상태 코드가 반환되면 음성 응답이 `sample.wav`로 기록됩니다.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function saveAudio(accessToken) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
      .att('version', '1.0')
      .att('xml:lang', 'en-us')
      .ele('voice')
      .att('xml:lang', 'en-us')
      .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
      .txt(text)
      .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    };
    // This function makes the request to convert speech to text.
    // The speech is returned as the response.
    function convertText(error, response, body){
      if (!error && response.statusCode == 200) {
        console.log("Converting text-to-speech. Please hold...\n")
      }
      else {
        throw new Error(error);
      }
      console.log("Your file is ready.\n")
    }
    // Pipe the response to file.
    request(options, convertText).pipe(fs.createWriteStream('sample.wav'));
}
```

## <a name="put-it-all-together"></a>모든 요소 결합

이제 거의 완료되었습니다. 마지막 단계는 `textToSpeech` 함수를 호출하는 것입니다.

```javascript
// Start the sample app.
textToSpeech(subscriptionKey, saveAudio);
```

## <a name="run-the-sample-app"></a>샘플 앱 실행

이제 텍스트 음성 변환 샘플 앱을 실행할 준비가 되었습니다. 명령줄(또는 터미널 세션)에서 프로젝트 디렉터리로 이동하고 다음을 실행합니다.

```console
node tts.js
```

프롬프트가 표시되면 텍스트를 음성으로 변환하려는 항목을 입력합니다. 성공하면 음성 파일이 프로젝트 폴더에 있습니다. 선호하는 미디어 플레이어를 사용하여 재생합니다.

## <a name="clean-up-resources"></a>리소스 정리

샘플 앱의 소스 코드에서 구독 키와 같은 기밀 정보를 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 Node.js 샘플 살펴보기](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>참고 항목

* [텍스트를 음성으로 변환 API 참조](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [사용자 지정 음성 글꼴 만들기](how-to-customize-voice-font.md)
* [사용자 지정 음성을 만들기 위한 음성 샘플 녹음](record-custom-voice-samples.md)
