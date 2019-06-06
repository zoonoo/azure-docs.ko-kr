---
title: '빠른 시작: 지원되는 언어 목록 가져오기, C# - Translator Text API'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Translator Text API를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록을 가져옵니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: e5e57757fbd80ca8e90b9997440050a751914a8a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514947"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-c"></a>빠른 시작: Translator Text API를 사용하여 C#을 통해 지원되는 언어 목록 가져오기

이 빠른 시작에서는 Translator Text API를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록을 가져옵니다.

## <a name="prerequisites"></a>필수 조건

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet 패키지](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) 또는 즐겨 사용하는 텍스트 편집기

## <a name="create-a-net-core-project"></a>.NET Core 프로젝트 만들기

새 명령 프롬프트(또는 터미널 세션)를 열고 이 명령을 실행합니다.

```console
dotnet new console -o languages-sample
cd languages-sample
```

첫 번째 명령은 두 가지 작업을 수행합니다. 새 .NET 콘솔 애플리케이션을 만들고 `languages-sample`이라는 디렉터리를 만듭니다. 두 번째 명령은 프로젝트의 디렉터리로 변경합니다.

그런 다음, Json.Net을 설치해야 합니다. 프로젝트의 디렉터리에서 다음을 실행합니다.

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>프로젝트에 필요한 네임스페이스 추가

이전에 실행한 `dotnet new console` 명령은 `Program.cs`를 포함한 프로젝트를 만듭니다. 이 파일은 애플리케이션 코드가 보관되는 곳입니다. `Program.cs`를 열고 문을 사용하여 기존 항목을 바꿉니다. 이러한 문은 빌드하고 샘플 앱을 빌드하고 실행하는 데 필요한 모든 형식에 액세스할 수 있는지 확인합니다.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-get-a-list-of-languages"></a>언어 목록을 가져오는 함수 만들기

`Program` 클래스 내에서 `GetLanguages`라는 함수를 만듭니다. 이 클래스는 Languages 리소스를 호출하는 데 사용되는 코드를 캡슐화하고 콘솔에 결과를 출력합니다.

```csharp
static void GetLanguages()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-host-name-and-path"></a>호스트 이름 및 경로 설정

`GetLanguages` 함수에 이러한 줄을 추가합니다.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/languages?api-version=3.0";
```

## <a name="instantiate-the-client-and-make-a-request"></a>클라이언트를 인스턴스화하고 요청 수행

이러한 줄은 `HttpClient` 및 `HttpRequestMessage`를 인스턴스화합니다.

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>요청 만들기 및 응답 출력

`HttpRequestMessage` 내부에서 다음을 수행합니다.

* HTTP 메서드 선언
* 요청 URI 만들기
* 필수 헤더 추가
* 비동기 요청 수행
* 응답 출력

`HttpRequestMessage`에 이 코드를 추가합니다.

```csharp
// Set the method to GET
request.Method = HttpMethod.Get;
// Construct the full URI
request.RequestUri = new Uri(host + route);
// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;
// Pretty print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

“Pretty Print”(응답의 서식)로 응답을 출력하려면 이 함수를 프로그램 클래스에 추가합니다.
```
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="put-it-all-together"></a>모든 요소 결합

마지막 단계는 `Main` 함수에서 `GetLanguages()`를 호출하는 것입니다. `static void Main(string[] args)`을 찾고 이러한 줄을 추가하세요.

```csharp
GetLanguages();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>샘플 앱 실행

이제 끝났습니다. 샘플 앱을 실행할 준비가 되었습니다. 명령줄(또는 터미널 세션)에서 프로젝트 디렉터리로 이동하고 다음을 실행합니다.

```console
dotnet run
```

## <a name="sample-response"></a>샘플 응답

국가/지역 약어는 이 [언어 목록](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)에서 확인하세요.

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>리소스 정리

샘플 앱의 소스 코드에서 구독 키와 같은 기밀 정보를 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

음역 및 언어 식별을 포함하여 이 빠른 시작과 다른 빠른 시작의 샘플 코드 그리고 GitHub의 다른 샘플 Translator Text 프로젝트를 살펴봅니다.

> [!div class="nextstepaction"]
> [GitHub에서 C# 예제 살펴보기](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>참고 항목

* [텍스트 번역](quickstart-csharp-translate.md)
* [텍스트 음역](quickstart-csharp-transliterate.md)
* [입력으로 언어 식별](quickstart-csharp-detect.md)
* [대체 번역 가져오기](quickstart-csharp-dictionary.md)
* [입력으로 문장 길이 확인](quickstart-csharp-sentences.md)
