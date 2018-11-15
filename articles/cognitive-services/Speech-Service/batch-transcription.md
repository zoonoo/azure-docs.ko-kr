---
title: Azure Batch Transcription API 사용
titlesuffix: Azure Cognitive Services
description: 대규모 오디오 콘텐츠를 기록하는 방법에 대한 샘플입니다.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: cd57e9a90b07447392fbff48017bb29f002ad29e
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035954"
---
# <a name="use-batch-transcription"></a>일괄 처리 전사 사용

일괄 처리 전사는 저장소에 대용량의 오디오가 있는 경우에 적합합니다. Rest API를 사용하면 SAS(공유 액세스 서명) URI를 통해 오디오 파일을 가리키고 비동기적으로 전사를 받을 수 있습니다.

## <a name="the-batch-transcription-api"></a>Batch Transcription API

Batch Transcription API는 추가 기능과 함께 비동기 음성 텍스트 변환 전사를 제공합니다. 다음 용도로 사용되는 메서드를 노출하는 REST API입니다.

1. 일괄 처리 요청 만들기
1. 상태 쿼리 
1. 전사 다운로드

> [!NOTE]
> Batch Transcription API는 일반적으로 수천 시간의 오디오를 누적하는 호출 센터에 적합합니다. API는 대용량의 오디오 녹음을 쉽게 전사할 수 있게 하는 "자체 유도(fire and forget)" 철학에 따라 안내됩니다.

### <a name="supported-formats"></a>지원되는 형식

Batch Transcription API에서 지원하는 형식은 다음과 같습니다.

이름| 채널  |
----|----------|
mp3 |   Mono   |   
mp3 |  스테레오  | 
wav |   Mono   |
wav |  스테레오  |
Opus|   Mono   |
Opus|  스테레오  |

스테레오 오디오 스트림의 경우 일괄 처리 전사는 전사하는 동안 왼쪽 및 오른쪽 채널을 분할합니다. 결과를 포함하는 2개의 JSON 파일이 각각 단일 채널에서 만들어집니다. 말하기 기준 타임스탬프를 사용하여 개발자는 순서가 지정된 최종 기록을 만들 수 있습니다. 욕설 필터 및 문장 부호 모델을 설정하기 위한 속성을 포함하는 채널의 출력은 다음과 같이 JSON 샘플에 표시됩니다.

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic"
  },
```

> [!NOTE]
> Batch Transcription API는 기록, 해당 상태 및 관련 결과를 요청하기 위해 REST 서비스를 사용합니다. 모든 언어에서 API를 사용할 수 있습니다. 다음 섹션에서는 API 사용 방법을 설명합니다.

## <a name="authorization-token"></a>권한 부여 토큰

Speech Service의 모든 기능과 마찬가지로, [시작 가이드](get-started.md)에 따라 [Azure Portal](https://portal.azure.com)에서 구독 키를 만듭니다. 기준 모델에서 전사를 가져오려는 경우 키 만들기만 수행하면 됩니다. 

사용자 지정 모델을 사용자 지정하고 사용하려는 경우 다음에 따라 구독 키를 사용자 지정 음성 포털에 추가합니다.

1. [Custom Speech](https://customspeech.ai)에 로그인합니다.

2. 오른쪽 위에서 **구독**을 선택합니다.

3. **기존 구독 연결**을 선택합니다.

4. 팝업 창에서 구독 키 및 별칭을 추가합니다.

    ![구독 추가 창](media/stt/Subscriptions.jpg)

5. 해당 키를 복사하여 다음 샘플의 클라이언트 코드에 붙여넣습니다.

> [!NOTE]
> 사용자 지정 모델을 사용하려면 해당 모델의 ID도 필요합니다. 이 ID는 엔드포인트 세부 정보 보기에서 찾은 엔드포인트 ID가 아닙니다. 해당 모델의 세부 정보를 선택할 때 검색할 수 있는 모델 ID입니다.

## <a name="sample-code"></a>샘플 코드

구독 키와 API 키를 사용하여 다음 샘플 코드를 사용자 지정합니다. 이 작업을 사용하면 전달자 토큰을 가져올 수 있습니다.

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
         
            return new CrisClient(client);
        }
```

토큰을 가져온 후 전사가 필요한 오디오 파일을 가리키는 SAS URI를 지정합니다. 코드의 나머지 부분은 상태를 통해 반복되고 결과를 표시합니다. 먼저, 다음 코드 조각에 표시된 대로 키, 지역, 사용할 모델 및 SA를 설정합니다. 다음으로, 클라이언트 및 POST 요청을 인스턴스화합니다. 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

이제 요청을 했으니 다음 코드 조각에 표시된 대로 전사 결과를 쿼리하고 다운로드할 수 있습니다.

```cs
  
            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            // for each transcription in the list we check the status
            foreach (var transcription in transcriptions)
            {
                switch(transcription.Status)
                {
                    case "Failed":
                    case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                        if (!createdTranscriptions.Contains(transcription.Id))
                        {
                            // not created from here, continue
                            continue;
                        }
                            
                        completed++;
                            
                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
                            Console.WriteLine(results);
                        }
                    
                    break;
                    case "Running":
                    running++;
                     break;
                    case "NotStarted":
                    notStarted++;
                    break;
                    
                    }
                }
            }
        }
```

이전 호출에 대한 전체 세부 정보는 [swagger 문서](https://westus.cris.ai/swagger/ui/index)를 참조하세요. 여기에 표시된 완전한 샘플을 참조하려면 [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)로 이동합니다.

> [!NOTE]
> 이전 코드에서 구독 키는 Azure Portal에서 만든 Speech 리소스의 구독 키입니다. Custom Speech Service 리소스에서 가져온 키는 작동하지 않습니다.

오디오를 게시하고 전사 상태를 수신하려면 비동기 설정을 기록해 둡니다. 만든 클라이언트는 .NET HTTP 클라이언트입니다. 오디오 파일 세부 정보를 전송하기 위한 `PostTranscriptions` 메서드 및 결과를 수신하기 위한 `GetTranscriptions` 메서드가 있습니다. `PostTranscriptions`는 핸들을 반환하고, `GetTranscriptions`는 이 핸들을 사용하여 전사 상태를 가져오기 위한 핸들을 만듭니다.

현재 샘플 코드는 사용자 지정 모델을 지정하지 않습니다. 이 서비스는 파일을 전사하기 위해 기준 모델을 사용합니다. 모델을 지정하려면 음향 및 언어 모델에 대해 모델 ID와 동일한 메서드를 전달할 수 있습니다. 

기준 모델을 사용하지 않으려면 음향 모델 및 언어 모델 모두에 대해 모델 ID를 전달합니다.

> [!NOTE]
> 기준 전사의 경우 기준 모델의 엔드포인트를 선언할 필요가 없습니다. 사용자 지정 모델을 사용하려면 엔드포인트 ID를 [샘플](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)로 제공해야 합니다. 기준 언어 모델에서 음향 기준을 사용하려면 사용자 지정 모델의 엔드포인트 ID만 선언하면 됩니다. Microsoft에서는 파트너 기준 모델&mdash;음향 또는 언어 여부에 관계 없이&mdash;을 검색하고 이를 사용하여 전사 요청을 수행합니다.

### <a name="supported-storage"></a>지원되는 저장소

현재 지원되는 유일한 스토리지는 Azure Blob 스토리지입니다.

## <a name="download-the-sample"></a>샘플 다운로드

이 문서의 샘플은 [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)에서 찾을 수 있습니다.

> [!NOTE]
> 일반적으로 오디오 전사에는 오디오 파일의 기간과 동일한 시간 범위에 더해 2-3분의 오버헤드가 필요합니다.

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
