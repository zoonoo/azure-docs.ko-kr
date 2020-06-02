---
title: C#에서 REST 호출을 사용하여 모델 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: b1bf3c0d7c902a048881b5fb75783744214b073e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655475"
---
## <a name="prerequisites"></a>필수 구성 요소

* Azure Language Understanding - 작성 리소스 32자 키 및 작성 엔드포인트 URL입니다. [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 또는 [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli)를 사용하여 만듭니다.
* `Azure-Samples/cognitive-services-sample-data-files` GitHub 리포지토리에서 [Pizza](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json) 앱을 가져옵니다.
* 가져온 Pizza 앱의 LUIS 애플리케이션 ID입니다. 애플리케이션 ID는 애플리케이션 대시보드에 표시됩니다.
* 발언을 수신하는 애플리케이션 내의 버전 ID입니다.
* [.NET Core 3.1](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>예제 발언 JSON 파일

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>프로그래밍 방식으로 모델 변경

1. `csharp-model-with-rest`의 프로젝트 및 폴더 이름을 사용하여 C# 언어를 대상으로 하는 새 콘솔 애플리케이션을 만듭니다.

    ```console
    dotnet new console -lang C# -n csharp-model-with-rest
    ```

1. 생성한 `csharp-model-with-rest` 디렉터리로 변경하고 다음 명령을 사용하여 필요한 종속성을 설치합니다.

    ```console
    cd csharp-model-with-rest
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```

1. Program.cs를 다음 코드로 덮어씁니다.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;

    // 3rd party NuGet packages
    using JsonFormatterPlus;

    namespace AddUtterances
    {
        class Program
        {
            // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
            static string appID = "YOUR-APP-ID";

            // YOUR-AUTHORING-KEY: Your LUIS authoring key, 32 character value.
            static string authoringKey = "YOUR-AUTHORING-KEY";

            // YOUR-AUTHORING-ENDPOINT: Replace this endpoint with your authoring key endpoint.
            // For example, "https://your-resource-name.api.cognitive.microsoft.com/"
            static string endpoint = "YOUR-AUTHORING-ENDPOINT";

            // NOTE: Replace this your version number.
            static string appVersion = "0.1";

            static string host = String.Format("{0}luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);

            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }

            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);

                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }

                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }

            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";

                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }

            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";

                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }

            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }

            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
                [
                    {
                        'text': 'order a pizza',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 6,
                                'endCharIndex': 12
                            }
                        ]
                    },
                    {
                        'text': 'order a large pepperoni pizza',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 6,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'FullPizzaWithModifiers',
                                'startCharIndex': 6,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'PizzaType',
                                'startCharIndex': 14,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'Size',
                                'startCharIndex': 8,
                                'endCharIndex': 12
                            }
                        ]
                    },
                    {
                        'text': 'I want two large pepperoni pizzas on thin crust',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 7,
                                'endCharIndex': 46
                            },
                            {
                                'entityName': 'FullPizzaWithModifiers',
                                'startCharIndex': 7,
                                'endCharIndex': 46
                            },
                            {
                                'entityName': 'PizzaType',
                                'startCharIndex': 17,
                                'endCharIndex': 32
                            },
                            {
                                'entityName': 'Size',
                                'startCharIndex': 11,
                                'endCharIndex': 15
                            },
                            {
                                'entityName': 'Quantity',
                                'startCharIndex': 7,
                                'endCharIndex': 9
                            },
                            {
                                'entityName': 'Crust',
                                'startCharIndex': 37,
                                'endCharIndex': 46
                            }
                        ]
                    }
                ]
                ";

                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
        }
    }
    ```

1. `YOUR-`에서 시작하는 값을 고유한 값으로 바꿉니다.

    |정보|목적|
    |--|--|
    |`YOUR-APP-ID`| LUIS 앱 ID입니다. |
    |`YOUR-AUTHORING-KEY`|32자 작성 키입니다.|
    |`YOUR-AUTHORING-ENDPOINT`| 작성 URL 엔드포인트입니다. 예들 들어 `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`입니다. 리소스를 만들 때 리소스 이름을 설정합니다.|

    할당된 키와 리소스는 **Azure Resources** 페이지의 관리 섹션에 있는 LUIS 포털에 표시됩니다. 앱 ID는 **애플리케이션 설정** 페이지의 동일한 관리 섹션에서 사용할 수 있습니다.

1. 콘솔 애플리케이션을 빌드합니다.

    ```console
    dotnet build
    ```

1. 콘솔 애플리케이션을 실행합니다. 콘솔 출력에 브라우저 창에서 앞서 본 것과 동일한 JSON이 표시됩니다.

    ```console
    dotnet run
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 완료하면 파일 시스템에서 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 모범 사례](../luis-concept-best-practices.md)
