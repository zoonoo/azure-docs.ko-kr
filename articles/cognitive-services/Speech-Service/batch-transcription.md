---
title: Azure Batch Transcription API | Azure Microsoft Docs
description: 샘플
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 01bbf4ca19b0fb702aa76d5149fb0e38389fe455
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054826"
---
# <a name="batch-transcription"></a>일괄 처리 기록

일괄 처리 기록은 많은 양의 오디오가 있는 사용 사례에 적합합니다. 개발자는 오디오 파일을 가리킨 후 비동기 모드로 기록을 다시 가져올 수 있습니다.

## <a name="batch-transcription-api"></a>Batch Transcription API

Batch Transcription API는 위의 시나리오를 지원합니다. 추가 기능과 함께 비동기 음성 텍스트 변환 기록을 제공합니다.

> [!NOTE]
> Batch Transcription API는 일반적으로 수천 시간의 오디오를 누적하는 호출 센터에 적합합니다. 이 API의 Fire & Forget(발사 후 잊어버리기) 철학은 대량의 오디오 녹음을 쉽게 기록하도록 합니다.

### <a name="supported-formats"></a>지원되는 형식

Batch Transcription API는 모든 오프라인 호출 센터 관련 시나리오에 실제로 사용되고 모든 관련 형식을 지원하는 것을 목표로 합니다. 현재 지원되는 형식:

Name| 채널  |
----|----------|
mp3 |   Mono   |   
mp3 |  스테레오  | 
wav |   Mono   |
wav |  스테레오  |

스테레오 오디오 스트림의 경우 일괄 처리 기록은 기록 동안 왼쪽 및 오른쪽 채널을 분할합니다. 결과를 포함하는 2개의 JSON 파일이 각각 단일 채널에서 만들어집니다. 말하기 기준 타임스탬프를 사용하여 개발자는 순서가 지정된 최종 기록을 만들 수 있습니다. 다음 JSON 샘플에서는 채널의 출력을 보여 줍니다.

    ```
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
> Batch Transcription API는 기록, 해당 상태 및 관련 결과를 요청하기 위해 REST 서비스를 사용합니다. 이 API는 .NET을 기준으로 하며 외부 종속성이 없습니다. 다음 섹션에서는 사용 방법을 설명합니다.

## <a name="authorization-token"></a>권한 부여 토큰

통합 Speech Service의 모든 기능과 마찬가지로, [Azure Portal](https://portal.azure.com)에서 구독 키를 만들어야 합니다. 또한 API 키는 음성 포털에서 획득해야 합니다. API 키를 생성하는 단계:

1. https://customspeech.ai에 로그인합니다.

2. 구독을 클릭합니다.

3. 옵션 `Generate API Key`을 클릭합니다.

    ![업로드 보기](media/stt/Subscriptions.jpg)

4. 해당 키를 복사한 후 아래 샘플의 클라이언트 코드에 붙여 넣습니다.

> [!NOTE]
> 사용자 지정 모델을 사용하려는 경우 해당 모델의 ID도 필요합니다. 이 ID는 끝점 세부 정보 보기에서 찾을 수 있는 배포 또는 끝점 ID가 아니라 해당 모델의 세부 정보를 클릭하여 검색할 수 있는 모델 ID입니다.

## <a name="sample-code"></a>샘플 코드

API를 사용하는 것은 매우 간단합니다. 아래 샘플 코드는 구독 키 및 API 키로 사용자 지정해야 합니다.

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
                    "<your subscripition key>", // Subscription Key
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
> 위의 코드 조각에서 언급된 등록 키는 Azure Portal에서 만드는 Speech(Preview) 리소스의 키입니다. 사용자 지정 Custom Speech 리소스에서 가져온 키는 작동하지 않습니다.


오디오 게시 및 기록 상태 수신에 대한 비동기 설정을 확인합니다. 만든 클라이언트는 NET Http 클라이언트입니다. 오디오 파일 세부 정보를 전송하기 위한 `PostTranscriptions` 메서드와, 결과를 수신하기 위한 `GetTranscriptions` 메서드가 있습니다. `PostTranscriptions`는 핸들을 반환하며 `GetTranscriptions` 메서드는 이 핸들을 사용하여 기록 상태를 가져오기 위한 핸들을 만듭니다.

현재 샘플 코드는 어떤 사용자 지정 모델도 지정하지 않습니다. 이 서비스는 파일을 기록하기 위해 기준 모델을 사용합니다. 사용자는 모델을 지정하려는 경우 음향 및 언어 모델에 대해 동일한 방법으로 modelID를 제공할 수 있습니다. 

기준 모델을 사용하지 않으려는 경우 음향 및 언어 모델 둘 다에 대해 모델 ID를 제공해야 합니다.

> [!NOTE]
> 기준 기록의 경우, 사용자는 기준 모델의 끝점을 선언할 필요가 없습니다. 사용자는 사용자 지정 모델을 사용하려는 경우 끝점 ID를 [샘플](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)로 제공해야 합니다. 사용자가 기준 언어 모델에서 음향 기준을 사용하려는 경우 사용자 지정 모델의 끝점 ID만 선언하면 됩니다. 내부적으로 시스템은 파트너 기준 모델(음향 또는 언어)을 파악하고 기록 요청을 이행하는 데 사용합니다.

### <a name="supported-storage"></a>지원되는 저장소

현재 지원되는 유일한 저장소는 Azure Blob입니다.

## <a name="downloading-the-sample"></a>샘플 다운로드

여기에 표시된 예제는 [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)에 있습니다.

> [!NOTE]
> 일반적으로 오디오 기록에는 오디오 파일의 기간에 2~3분의 오버헤드를 더한 시간 범위가 필요합니다.

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
