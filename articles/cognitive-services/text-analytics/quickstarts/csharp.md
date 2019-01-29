---
title: '빠른 시작: C#을 사용하여 텍스트 분석 API 호출'
titleSuffix: Azure Cognitive Services
description: 텍스트 분석 API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 구합니다.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: assafi
ms.openlocfilehash: 8cfc826e76e937df416e88528e1ae2339d4d079d
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390696"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>빠른 시작: C#을 사용하여 텍스트 분석 Cognitive Service 호출
<a name="HOLTop"></a>

이 문서에서는  [Text Analytics API](//go.microsoft.com/fwlink/?LinkID=759711) 및 C#을 사용하여 언어 감지, 감정 분석 및 핵심 구 추출을 수행하는 방법을 보여 줍니다. 코드는 최소한의 외부 라이브러리 참조를 사용하여 .NET Core 애플리케이션에서 작동하도록 작성되었으므로, Linux 또는 MacOS에서 실행할 수도 있습니다.

API 기술 문서는 [API 정의](//go.microsoft.com/fwlink/?LinkID=759346)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

등록하는 동안 생성된 [엔드포인트 및 액세스 키](../How-tos/text-analytics-how-to-access-key.md)도 있어야 합니다.


## <a name="install-the-nuget-sdk-package"></a>NuGet SDK 패키지 설치
1. Visual Studio에서 새 콘솔 솔루션을 만듭니다.
1. 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **솔루션용 NuGet 패키지 관리**를 클릭합니다.
1. **시험판 포함** 확인란을 선택합니다.
1. **찾아보기** 탭을 선택하고 **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**를 검색합니다.
1. NuGet 패키지를 선택하고 설치합니다.

> [!Tip]
>  C#에서 직접 [HTTP 엔드포인트](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)를 호출할 수 있지만, Microsoft.Azure.CognitiveServices.Language SDK에서는 JSON 직렬화 및 역직렬화를 걱정하지 않고도 서비스를 훨씬 더 쉽게 호출할 수 있습니다.
>
> 몇 가지 유용한 링크:
> - [SDK Nuget 페이지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK 코드](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>SDK를 사용하여 텍스트 분석 API 호출
1. Program.cs를 아래에 제공된 코드로 바꿉니다. 이 프로그램은 3개 섹션(언어 추출, 키 구문 추출 및 감정 분석)에서 텍스트 분석 API의 기능을 보여줍니다.
1. `Ocp-Apim-Subscription-Key` 헤더 값을 구독에 유효한 액세스 키로 바꿉니다.
1. `Endpoint`의 위치를 등록한 끝점으로 바꿉니다. Azure Portal 리소스에서 엔드포인트를 찾을 수 있습니다. 엔드포인트는 일반적으로 "https://[region].api.cognitive.microsoft.com"으로 시작되며, 여기서는 프로토콜과 호스트 이름만 포함하세요.
1. 프로그램을 실행합니다.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        private const string SubscriptionKey = ""; //Insert your Text Anaytics subscription key

        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            }; //Replace 'westus' with the correct region for your Text Analytics subscription

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result = client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine($"\t\t{keyphrase}");
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
            }


            // Identify entities
            Console.WriteLine("\n\n===== ENTITIES ======");

            EntitiesBatchResultV2dot1 result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.")
                        })).Result;

            // Printing entities results
            foreach (var document in result4.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Entities:");

                foreach (EntityRecordV2dot1 entity in document.Entities)
                {
                    Console.WriteLine($"\t\t{entity.Name}\t\t{entity.WikipediaUrl}\t\t{entity.Type}\t\t{entity.SubType}");
                }
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="application-output"></a>애플리케이션 출력

애플리케이션에는 다음 정보가 표시됩니다.

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified


===== KEY-PHRASE EXTRACTION ======
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol

===== SENTIMENT ANALYSIS ======
Document ID: 0 , Sentiment Score: 0.87
Document ID: 1 , Sentiment Score: 0.11
Document ID: 2 , Sentiment Score: 0.44
Document ID: 3 , Sentiment Score: 1.00
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>참고 항목

 [Text Analytics 개요](../overview.md) [FAQ(질문과 대답)](../text-analytics-resource-faq.md)

