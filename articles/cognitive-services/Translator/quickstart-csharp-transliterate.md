---
title: '빠른 시작: 텍스트 스크립트 변환 - Translator Text, C#'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 C#과 함께 Translator Text API를 사용하여 특정 언어의 텍스트를 한 스크립트에서 다른 스크립트로 변환합니다.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: 7fa8c5dc76e59ec236d6b9662bd2ba41db0add0f
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320410"
---
# <a name="quickstart-transliterate-text-with-c35"></a>빠른 시작: C&#35;을 사용하여 텍스트 음역

이 빠른 시작에서는 Translator Text API를 사용하여 한 언어의 텍스트를 다른 언어로 변환합니다.

## <a name="prerequisites"></a>필수 조건

Windows에서 이 코드를 실행하려면 [Visual Studio 2017](https://www.visualstudio.com/downloads/)이 필요합니다. 체험 Community Edition을 사용해도 됩니다.

Translator Text API를 사용하려면 구독 키도 필요합니다. [Translator Text API에 등록하는 방법](translator-text-how-to-signup.md)을 참조하세요.

## <a name="transliterate-request"></a>음역 요청

> [!TIP]
> [Github](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp)에서 최신 코드를 받습니다.

다음은 [Transliterate](./reference/v3-0-transliterate.md) 메서드를 사용하여 특정 언어의 텍스트를 한 스크립트에서 다른 스크립트로 변환합니다.

1. 즐겨찾는 IDE에서 새 C# 프로젝트를 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `key` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. 프로그램을 실행합니다.

```csharp
using System;
using System.Net.Http;
using System.Text;
// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace TranslatorTextQuickStart
{
    class Program
    {
        static string host = "https://api.cognitive.microsofttranslator.com";
        static string path = "/transliterate?api-version=3.0";
        // Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
        static string params_ = "&language=ja&fromScript=jpan&toScript=latn";

        static string uri = host + path + params_;

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // Transliterate "good afternoon".
        static string text = "こんにちは";

        async static void Transliterate()
        {
            System.Object[] body = new System.Object[] { new { Text = text } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                var result = JsonConvert.SerializeObject(JsonConvert.DeserializeObject(responseBody), Formatting.Indented);

                Console.OutputEncoding = UnicodeEncoding.UTF8;
                Console.WriteLine(result);
            }
        }

        static void Main(string[] args)
        {
            Transliterate();
            Console.ReadLine();
        }
    }
}
```

## <a name="transliterate-response"></a>음역 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>다음 단계

번역 및 언어 식별을 포함하여 이 빠른 시작과 다른 빠른 시작의 샘플 코드 그리고 GitHub의 다른 샘플 Translator Text 프로젝트를 살펴봅니다.

> [!div class="nextstepaction"]
> [GitHub에서 C# 예제 살펴보기](https://aka.ms/TranslatorGitHub?type=&language=c%23)
