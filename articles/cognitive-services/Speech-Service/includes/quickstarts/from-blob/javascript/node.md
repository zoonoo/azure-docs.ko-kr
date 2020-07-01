---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: dapine
ms.openlocfilehash: 19aa7b839b1c673451f51524a9461aba8222c0d2
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980401"
---
## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [개발 환경을 설정하고 빈 프로젝트 만들기](../../../../quickstarts/setup-platform.md?tabs=vs&pivots=programmming-language-javascript)
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [Azure Blob에 원본 파일 업로드](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="create-a-new-js-file"></a>새 JS 파일 만들기

첫 번째 단계로 선호하는 편집기에서 프로젝트를 열어야 합니다.

파일 index.js를 호출합니다.

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다.

```JavaScript
const https = require("https");

// Replace with your subscription key
SubscriptionKey = "YourSubscriptionKey";

// Update with your service region
Region = "YourServiceRegion";
Port = 443;

// Recordings and locale
Locale = "en-US";
RecordingsBlobUri = "YourFileUrl";

// Name and description
Name = "Simple transcription";
Description = "Simple transcription description";

SpeechToTextBasePath = "/api/speechtotext/v2.0/";
```

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON 래퍼

REST API는 JSON 형식으로 요청을 받아 JSON으로 결과를 반환합니다.
요청 및 응답을 더 손쉽게 이해할 수 있도록 JSON을 직렬화/역직렬화하는 데 사용할 몇 가지 클래스를 선언합니다.


```JavaScript
class ModelIdentity {
    id;
}

class Transcription {
    Name;
    Description;
    Locale;
    RecordingsUrl;
    ResultsUrls;
    Id;
    CreatedDateTime;
    LastActionDateTime;
    Status;
    StatusMessage;
}

class TranscriptionDefinition {
    Name;
    Description;
    RecordingsUrl;
    Locale;
    Models;
    Properties;
}
```

## <a name="create-an-initial-transcription-request"></a>초기 전사 요청을 만듭니다.
다음으로, 전사 요청을 생성합니다.

```JavaScript
const ts = {
    Name: Name,
    Description: Description,
    Locale: Locale,
    RecordingsUrl: RecordingsBlobUri,
    Properties: {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True"
    },
    Models: []
}

const postPayload = JSON.stringify(ts);

const startOptions = {
    hostname: Region + ".cris.ai",
    port: Port,
    path: SpeechToTextBasePath + "Transcriptions/",
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        'Content-Length': postPayload.length,
        "Ocp-Apim-Subscription-Key": SubscriptionKey
    }
}
```

## <a name="send-the-transcription-request"></a>전사 요청을 전송합니다.
이제 Speech Service에 요청을 게시하고 초기 응답 코드를 확인합니다. 이 응답 코드는 서비스에서 요청을 받았는지 여부를 표시합니다. 서비스는 전사 상태를 저장할 위치에 해당하는 Url을 응답 헤더에 반환합니다.

그런 다음, `CheckTranscriptionStatus` 메서드를 호출하여 상태를 확인하고 최종적으로 결과를 인쇄합니다. 다음으로 `CheckTranscriptionStatus`를 구현합니다.

```JavaScript
const request = https.request(startOptions, (response) => {
    if (response.statusCode != 202) {
        console.error("Error, status code " + response.statusCode);
    } else {

        const transcriptionLocation = response.headers.location;

        console.info("Created transcription at location " + transcriptionLocation);
        console.info("Checking status.");

        CheckTranscriptionStatus(transcriptionLocation);
    }
});

request.on("error", error => {
    console.error(error);
});

request.write(postPayload);
request.end();
```

## <a name="check-the-requests-status"></a>요청 상태 확인
서비스는 전사를 비동기식으로 처리하므로 상태를 폴링해야 하는 경우가 있습니다. 5초마다 확인합니다.

요청을 게시할 때 가져온 Url에서 콘텐츠를 검색하여 상태를 확인할 수 있습니다. 콘텐츠를 다시 가져오는 경우 쉽게 상호 작용할 수 있도록 도우미 클래스 중 하나로 역직렬화합니다.

다음은 성공적인 완료를 제외한 모든 상태에 대한 상태 표시가 있는 폴링 코드입니다. 다음에 수행하겠습니다. 

`CheckTranscriptionStatus`는 전사 요청에서 상태 URL을 가져와 성공 또는 오류를 나타낼 때까지 5초마다 폴링합니다. 그런 다음, `PrintResults`를 호출하여 전사의 결과를 인쇄합니다. 다음으로 `PrintResults`를 구현합니다.
```csharp
function CheckTranscriptionStatus(statusUrl) {
    transcription = null;
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(statusUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                const statusObject = JSON.parse(responseText);

                var done = false;
                switch (statusObject.status) {
                    case "Failed":
                        console.info("Transcription failed. Status: " + transcription.StatusMessage);
                        done = true;
                        break;
                    case "Succeeded":
                        done = true;
                        PrintResults(statusObject.resultsUrls["channel_0"]);
                        break;
                    case "Running":
                        console.info("Transcription is still running.");
                        break;
                    case "NotStarted":
                        console.info("Transcription has not started.");
                        break;
                }

                if (!done) {
                    setTimeout(() => {
                        CheckTranscriptionStatus(statusUrl);
                    }, (5000));
                }
            });
        }
    });

    fetchRequest.on("error", error => {
        console.error(error);
    });
}
```

## <a name="display-the-transcription-results"></a>전사 결과 표시
서비스가 전사를 성공적으로 완료하면 결과가 상태 응답에서 가져올 수 있는 다른 Url에 저장됩니다. 여기에서 이러한 결과를 읽고 역직렬화하기 전에 임시 파일에 다운로드하도록 요청합니다.
결과가 로드된 후에는 콘솔에 출력할 수 있습니다. 

```JavaScript
function PrintResults(resultUrl)
{
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(resultUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                console.info("Transcription Results:");
                console.info(responseText);
            });
        }
    });
}
```

## <a name="check-your-code"></a>코드 확인
이 시점에서 코드는 다음과 같습니다.

```JavaScript
const https = require("https");

// Replace with your subscription key
SubscriptionKey = "YourSubscriptionKey";

// Update with your service region
Region = "YourServiceRegion";
Port = 443;

// Recordings and locale
Locale = "en-US";
RecordingsBlobUri = "YourFileUrl";

// Name and description
Name = "Simple transcription";
Description = "Simple transcription description";

SpeechToTextBasePath = "/api/speechtotext/v2.0/";

class ModelIdentity {
    id;
}

class Transcription {
    Name;
    Description;
    Locale;
    RecordingsUrl;
    ResultsUrls;
    Id;
    CreatedDateTime;
    LastActionDateTime;
    Status;
    StatusMessage;
}

class TranscriptionDefinition {
    Name;
    Description;
    RecordingsUrl;
    Locale;
    Models;
    Properties;
}

const ts = {
    Name: Name,
    Description: Description,
    Locale: Locale,
    RecordingsUrl: RecordingsBlobUri,
    Properties: {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True"
    },
    Models: []
}

const postPayload = JSON.stringify(ts);

const startOptions = {
    hostname: Region + ".cris.ai",
    port: Port,
    path: SpeechToTextBasePath + "Transcriptions/",
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        'Content-Length': postPayload.length,
        "Ocp-Apim-Subscription-Key": SubscriptionKey
    }
}

function PrintResults(resultUrl)
{
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(resultUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                console.info("Transcription Results:");
                console.info(responseText);
            });
        }
    });
}

function CheckTranscriptionStatus(statusUrl) {
    transcription = null;
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(statusUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                const statusObject = JSON.parse(responseText);

                var done = false;
                switch (statusObject.status) {
                    case "Failed":
                        console.info("Transcription failed. Status: " + transcription.StatusMessage);
                        done = true;
                        break;
                    case "Succeeded":
                        done = true;
                        PrintResults(statusObject.resultsUrls["channel_0"]);
                        break;
                    case "Running":
                        console.info("Transcription is still running.");
                        break;
                    case "NotStarted":
                        console.info("Transcription has not started.");
                        break;
                }

                if (!done) {
                    setTimeout(() => {
                        CheckTranscriptionStatus(statusUrl);
                    }, (5000));
                }
            });
        }
    });

    fetchRequest.on("error", error => {
        console.error(error);
    });
}

const request = https.request(startOptions, (response) => {
    if (response.statusCode != 202) {
        console.error("Error, status code " + response.statusCode);
    } else {

        const transcriptionLocation = response.headers.location;

        console.info("Created transcription at location " + transcriptionLocation);
        console.info("Checking status.");

        CheckTranscriptionStatus(transcriptionLocation);
    }
});

request.on("error", error => {
    console.error(error);
});

request.write(postPayload);
request.end();
```

## <a name="run-your-app"></a>앱 실행

이제 앱을 빌드하고 음성 서비스를 사용하여 음성 인식을 테스트할 준비가 되었습니다.

**앱 시작** - 노드 index.js를 실행합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
