---
title: Azure Cognitive Services, 텍스트 분석 API의 C# 빠른 시작 | Microsoft Docs
description: Azure의 Microsoft Cognitive Services에서 텍스트 분석 API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 확인합니다.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/20/2017
ms.author: ashmaka
ms.openlocfilehash: d9c61a83450844461f621ff16354881a029f7ad6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266297"
---
# <a name="quickstart-for-text-analytics-api-with-c"></a>C#을 사용한 텍스트 분석 API 빠른 시작 
<a name="HOLTop"></a>

이 문서에서는 [텍스트 분석 API](//go.microsoft.com/fwlink/?LinkID=759711) 및 C#을 사용하여 언어 감지, 감정 분석 및 핵심 구 추출을 수행하는 방법을 보여 줍니다. 코드는 최소한의 외부 라이브러리 참조를 사용하여 .NET Core 응용 프로그램에서 작동하도록 작성되었으므로, Linux 또는 MacOS에서 실행할 수도 있습니다.

API 기술 문서는 [API 정의](//go.microsoft.com/fwlink/?LinkID=759346)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

**텍스트 분석 API**를 포함하는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. **매월 5,000개 트랜잭션의 체험 계층**을 사용하여 이 빠른 시작을 완료할 수 있습니다.

등록하는 동안 생성된 [끝점 및 액세스 키](../How-tos/text-analytics-how-to-access-key.md)도 있어야 합니다. 


## <a name="install-the-nuget-sdk-package"></a>Nuget SDK 패키지 설치
1. Visual Studio에서 새 콘솔 솔루션을 만듭니다.
1. 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **솔루션용 NuGet 패키지 관리**를 클릭합니다.
1. **시험판 포함** 확인란을 선택합니다.
1. **찾아보기** 탭을 선택하고 **Microsoft.Azure.CognitiveServices.Language**를 검색합니다.
1. Nuget 패키지를 선택하고 설치합니다.

> [!Tip]
>  C#에서 직접 [HTTP 끝점](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)을 호출할 수 있지만, Microsoft.Azure.CognitiveServices.Language SDK에서는 JSON 직렬화 및 역직렬화를 걱정하지 않고도 서비스를 훨씬 더 쉽게 호출할 수 있습니다.
>
> 몇 가지 유용한 링크:
> - [SDK Nuget 페이지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK 코드](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>SDK를 사용하여 텍스트 분석 API 호출
1. Program.cs를 아래에 제공된 코드로 바꿉니다. 이 프로그램은 3개 섹션(언어 추출, 키 구문 추출 및 감정 분석)에서 텍스트 분석 API의 기능을 보여줍니다.
1. `Ocp-Apim-Subscription-Key` 헤더 값을 구독에 유효한 액세스 키로 바꿉니다.
1. `client.AzureRegion`의 위치(현재 `AzureRegions.Westus`)를 등록한 지역으로 바꿉니다.
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
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsAPI client = new TextAnalyticsAPI(new ApiKeyServiceClientCredentials());
            client.AzureRegion = AzureRegions.Westus;

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
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
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
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
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>참고 항목 

 [Text Analytics 개요](../overview.md)  
 [FAQ(질문과 대답)](../text-analytics-resource-faq.md)

