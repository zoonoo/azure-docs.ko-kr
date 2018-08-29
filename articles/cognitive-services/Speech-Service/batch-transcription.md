---
title: Azure Batch 전사 API
description: 샘플
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 5af829ca076b39758973c28a44d918b9ba5782b1
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42351253"
---
# <a name="batch-transcription"></a>일괄 처리 기록

Batch 전사는 대용량의 오디오가 있는 경우에 적합합니다. 오디오 파일을 가리키고 전사를 비동기 모드로 다시 가져올 수 있습니다.

## <a name="batch-transcription-api"></a>Batch Transcription API

Batch 전사 API는 추가 기능과 함께 비동기 음성 텍스트 변환 전사를 제공합니다.

> [!NOTE]
> Batch 전사 API는 일반적으로 수천 시간의 오디오를 누적하는 호출 센터에 적합합니다. API는 대용량의 오디오 녹음을 쉽게 전사할 수 있게 하는 "자체 유도(fire and forget)" 철학으로 수행됩니다.

### <a name="supported-formats"></a>지원되는 형식

Batch 전사 API에서 지원하는 형식은 다음과 같습니다.

Name| 채널  |
----|----------|
mp3 |   Mono   |   
mp3 |  스테레오  | 
wav |   Mono   |
wav |  스테레오  |

스테레오 오디오 스트림의 경우 일괄 처리 전사는 전사하는 동안 왼쪽 및 오른쪽 채널을 분할합니다. 결과를 포함하는 2개의 JSON 파일이 각각 단일 채널에서 만들어집니다. 말하기 기준 타임스탬프를 사용하여 개발자는 순서가 지정된 최종 기록을 만들 수 있습니다. 다음 JSON 샘플에서는 채널의 출력을 보여 줍니다.

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> Batch Transcription API는 기록, 해당 상태 및 관련 결과를 요청하기 위해 REST 서비스를 사용합니다. 모든 언어에서 API를 사용할 수 있습니다. 다음 섹션에서는 사용 방법을 설명합니다.

## <a name="authorization-token"></a>권한 부여 토큰

통합 Speech Service의 모든 기능과 마찬가지로, [Azure Portal](https://portal.azure.com)에서 구독 키를 만듭니다. 또한 Speech 포털에서 API 키를 얻습니다. 

1. [Custom Speech](https://customspeech.ai)에 로그인합니다.

2. **구독**을 선택합니다.

3. **API 키 생성**을 선택합니다.

    ![Custom Speech 구독 페이지의 스크린샷](media/stt/Subscriptions.jpg)

4. 해당 키를 복사하여 다음 샘플의 클라이언트 코드에 붙여넣습니다.

> [!NOTE]
> 사용자 지정 모델을 사용하려면 해당 모델의 ID도 필요합니다. [엔드포인트 세부 정보] 보기에서 찾은 배포 또는 엔드포인트 ID가 아닙니다. 해당 모델의 세부 정보를 선택할 때 검색할 수 있는 모델 ID입니다.

## <a name="sample-code"></a>샘플 코드

구독 키와 API 키를 사용하여 다음 샘플 코드를 사용자 지정합니다. 이렇게 하면 전달자 토큰을 가져올 수 있습니다.

```cs
    public static async Task<CrisClient> CreateApiV1ClientAsync(string username, string key, string hostName, int port)
        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;

            var tokenProviderPath = "/oauth/ctoken";
            var clientToken = await CreateClientTokenAsync(client, hostName, port, tokenProviderPath, username, key).ConfigureAwait(false);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", clientToken.AccessToken);

            return new CrisClient(client);
        }
```

토큰을 가져온 후에는 전사가 필요한 오디오 파일을 가리키는 SAS URI를 지정해야 합니다. 코드의 나머지 부분은 상태를 반복하면서 결과를 표시합니다.

```cs
   static async Task TranscribeAsync()
        { 
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscription key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
        }
```

> [!NOTE]
> 앞의 코드에서 구독 키는 Azure Portal에서 만든 Speech(미리 보기) 리소스의 구독 키입니다. Custom Speech Service 리소스에서 가져온 키는 작동하지 않습니다.

오디오 게시 및 기록 상태 수신에 대한 비동기 설정을 확인합니다. 만든 클라이언트는 .NET Http 클라이언트입니다. 오디오 파일 세부 정보를 전송하기 위한 `PostTranscriptions` 메서드와, 결과를 수신하기 위한 `GetTranscriptions` 메서드가 있습니다. `PostTranscriptions`는 핸들을 반환하고, `GetTranscriptions`는 이 핸들을 사용하여 전사 상태를 가져오기 위한 핸들을 만듭니다.

현재 샘플 코드는 어떤 사용자 지정 모델도 지정하지 않습니다. 이 서비스는 파일을 전사하기 위해 기준 모델을 사용합니다. 모델을 지정하려면 음향 및 언어 모델에 대한 모델 ID를 동일한 메서드에 전달할 수 있습니다. 

기준 모델을 사용하지 않으려면 음향 및 언어 모델 둘 다에 대한 모델 ID를 전달해야 합니다.

> [!NOTE]
> 기준 전사의 경우 기준 모델의 엔드포인트를 선언할 필요가 없습니다. 사용자 지정 모델을 사용하려면 엔드포인트 ID를 [샘플](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)로 제공해야 합니다. 기준 언어 모델에서 음향 기준을 사용하려면 사용자 지정 모델의 엔드포인트 ID만 선언하면 됩니다. Microsoft는 파트너 기준 모델(음향 또는 언어)을 검색하고 이를 사용하여 전사 요청을 수행합니다.

### <a name="supported-storage"></a>지원되는 저장소

현재 지원되는 유일한 저장소는 Azure Blob 저장소입니다.

## <a name="downloading-the-sample"></a>샘플 다운로드

여기에 표시된 샘플은 [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)에 있습니다.

> [!NOTE]
> 일반적으로 오디오 전사에는 오디오 파일의 기간과 동일한 시간 범위와 2-3분의 오버헤드가 필요합니다.

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
