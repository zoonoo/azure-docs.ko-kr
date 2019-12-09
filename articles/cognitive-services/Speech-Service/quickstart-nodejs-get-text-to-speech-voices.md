---
title: '빠른 시작: 텍스트 음성 변환 음성 나열, Node.js - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Node.js를 사용하여 지역/엔드포인트에 대한 표준 음성 및 인공신경망 음성의 전체 목록을 가져오는 방법을 알아봅니다. 목록은 JSON으로 반환되며 음성 가용성은 지역에 따라 다릅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bd063102a4c0cbdc4b62df027e643b117b412278
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812863"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>빠른 시작: Node.js를 사용하여 텍스트-음성 변환 음성 목록 가져오기

이 빠른 시작에서는 Node.js를 사용하여 지역/엔드포인트에 대한 표준 음성 및 인공신경망 음성의 전체 목록을 가져오는 방법을 알아봅니다. 목록은 JSON으로 반환되며 음성 가용성은 지역에 따라 다릅니다. 지원되는 지역의 전체 목록은 [ 지역](regions.md)을 참조하세요.

이 빠른 시작에는 Speech Service 리소스와 함께 [Azure Cognitive Services 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 필요합니다. 계정이 없는 경우 [평가판](get-started.md)을 사용하여 구독 키를 가져올 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [Node 8.12.x 이상](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) 또는 즐겨 사용하는 텍스트 편집기
* Speech Service에 대한 Azure 구독 키입니다. [평가판을 가져올 수 있습니다](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>프로젝트 만들기 및 종속성 요구

즐겨 찾는 IDE 또는 편집기를 사용하여 새 Node.js 프로젝트를 만듭니다. 그런 다음, 아래 코드 조각을 `get-voices.js`라는 파일의 프로젝트에 복사합니다.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> 이러한 모듈을 사용하지 않았다면 프로그램을 실행하기 전에 설치해야 합니다. 이러한 패키지를 설치하려면 `npm install request request-promise`를 실행합니다.

## <a name="get-an-access-token"></a>액세스 토큰 가져오기

Text-to-Speech REST API에는 인증을 위한 액세스 토큰이 필요합니다. 액세스 토큰을 가져오려면 교환이 필요합니다. 이 함수에서는 `issueToken` 엔드포인트를 사용하여 액세스 토큰의 Speech Service 구독 키를 교환합니다.

이 샘플에서는 Speech Service 구독이 미국 서부 지역에 있다고 가정합니다. 다른 지역을 사용하는 경우 `uri` 값을 업데이트합니다. 전체 목록은 [지역](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)을 참조하세요.

이 코드를 프로젝트에 복사합니다.

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> 인증에 대한 자세한 내용은 [액세스 토큰으로 인증](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)을 참조하세요.

다음 섹션에서는 음성 목록을 가져오고 JSON 출력을 파일에 저장하는 함수를 만듭니다.

## <a name="make-a-request-and-save-the-response"></a>요청을 작성하고 응답을 저장합니다.

여기서 요청을 작성하고 반환되는 음성 목록을 저장합니다. 이 샘플에서는 사용자가 미국 서부 엔드포인트를 사용하고 있다고 가정합니다. 리소스가 다른 지역에 등록된 경우에는 `uri`을 업데이트해야 합니다. 자세한 내용은 [Speech Service 지역](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)을 참조하세요.

그런 다음, 요청에 필요한 헤더를 추가합니다. 마지막으로 서비스에 대한 요청을 만듭니다. 요청이 성공하고 200 상태 코드가 반환되면 응답이 파일에 기록됩니다.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>모든 요소 결합

이제 거의 완료되었습니다. 마지막 단계는 비동기 함수를 만드는 것입니다. 이 함수는 환경 변수에서 구독 키를 읽고, 토큰을 가져오고, 요청이 완료될 때까지 기다린 다음, 파일에 JSON 응답을 작성합니다.

환경 변수를 사용하는 데 친숙하지 않거나 문자열로 하드 코딩된 구독 키를 사용하여 테스트하려는 경우 `process.env.SPEECH_SERVICE_KEY`를 문자열로 구독 키로 바꿉니다.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>샘플 앱 실행

이제 끝났습니다. 샘플 앱을 실행할 준비가 되었습니다. 명령줄(또는 터미널 세션)에서 프로젝트 디렉터리로 이동하고 다음을 실행합니다.

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>리소스 정리

샘플 앱의 소스 코드에서 구독 키와 같은 기밀 정보를 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 Node.js 샘플 살펴보기](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>참고 항목

* [텍스트를 음성으로 변환 API 참조](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [사용자 지정 음성 글꼴 만들기](how-to-customize-voice-font.md)
* [사용자 지정 음성을 만들기 위한 음성 샘플 녹음](record-custom-voice-samples.md)
