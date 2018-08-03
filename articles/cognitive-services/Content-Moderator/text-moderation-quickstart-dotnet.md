---
title: Azure Content Moderator - .NET을 사용하여 텍스트 조정 | Microsoft Docs
description: .NET용 Azure Content Moderator SDK를 사용하여 텍스트를 조정하는 방법
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 7320286e186d7e6ba4041d3ed52f19e573b4d7e3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049884"
---
# <a name="moderate-text-using-net"></a>.NET을 사용하여 텍스트 조정

이 문서에서 제공하는 정보 및 코드 샘플을 통해 .NET용 Content Moderator SDK 사용을 시작하여 아래의 작업을 수행할 수 있습니다.
- 용어 기반 필터링을 사용하여 텍스트에서 욕설 검색
- Machine Learning 기반 모델을 사용하여 세 가지 범주로 [텍스트 분류](text-moderation-api.md#classification)
- 미국 및 영국 전화 번호, 메일 주소, 미국 우편 주소와 같은 PII(개인 식별 정보) 검색
- 텍스트 정규화 및 오타 자동 고침

이 문서에서는 Visual Studio 및 C#에 이미 익숙하다고 가정합니다.

## <a name="sign-up-for-content-moderator-services"></a>Content Moderator 서비스 등록

REST API 또는 SDK를 통해 Content Moderator 서비스를 사용하려면 먼저 구독 키가 필요합니다.
키를 획득하는 방법은 [빠른 시작](quick-start.md)을 참조하세요.

## <a name="create-your-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. 솔루션에 새 **콘솔 앱(.NET Framework)** 프로젝트를 추가합니다.

   샘플 코드에서 프로젝트의 이름을 **TextModeration**으로 지정합니다.

1. 이 프로젝트를 솔루션의 단일 시작 프로젝트로 선택합니다.

1. [Content Moderator 클라이언트 도우미 빠른 시작](content-moderator-helper-quickstart-dotnet.md)에서 만든 **ModeratorHelper** 프로젝트 어셈블리에 대한 참조를 추가합니다.

### <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

다음 NuGet 패키지를 설치합니다.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>프로그램의 using 문 업데이트

프로그램의 using 문을 수정합니다.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;


### <a name="initialize-application-specific-settings"></a>응용 프로그램 관련 설정 초기화

Program.cs의 **Program** 클래스에 다음 정적 필드를 추가합니다.

    /// <summary>
    /// The name of the file that contains the text to evaluate.
    /// </summary>
    /// <remarks>You will need to create an input file and update this path
    /// accordingly. Relative paths are ralative the execution directory.</remarks>
    private static string TextFile = "TextFile.txt";

    /// <summary>
    /// The name of the file to contain the output from the evaluation.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private static string OutputFile = "TextModerationOutput.txt";

다음 텍스트를 사용하여 이 빠른 시작의 출력을 생성했습니다.

> [!NOTE]
> 다음 샘플 텍스트에서 잘못된 사회 보장 번호는 의도적인 것입니다. 샘플 입력 및 출력 형식을 전달하기 위해 사용되었습니다.

    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
    0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).

## <a name="add-code-to-load-and-evaluate-the-input-text"></a>입력 텍스트를 로드 및 평가하는 코드 추가

**Main** 메서드에 다음 코드를 추가합니다.

    // Load the input text.
    string text = File.ReadAllText(TextFile);
    text = text.Replace(System.Environment.NewLine, " ");

    // Save the moderation results to a file.
    using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
    {
        // Create a Content Moderator client and evaluate the text.
        using (var client = Clients.NewClient())
        {
            // Screen the input text: check for profanity, classify the text into three categories
                // do autocorrect text, and check for personally identifying 
                // information (PII)
                outputWriter.WriteLine("Autocorrect typos, check for matching terms, PII, and classify.");
                var screenResult =
                    client.TextModeration.ScreenText("eng", "text/plain", text, true, true, null, true);
                outputWriter.WriteLine(
                        JsonConvert.SerializeObject(screenResult, Formatting.Indented));
        }
        outputWriter.Flush();
        outputWriter.Close();
    }

> [!NOTE]
> Content Moderator 서비스 키에는 RPS(초당 요청 수) 속도 제한이 있으며, 제한을 초과하는 경우 SDK에서 429 오류 코드로 예외를 throw합니다.
>
> 무료 계층 키를 사용하는 경우 요청 비율이 초당 하나의 요청으로 제한됩니다.

## <a name="run-the-program-and-review-the-output"></a>프로그램 실행 및 출력 검토

로그 파일에 기록되는 프로그램의 샘플 출력은 다음과 같습니다.

    Autocorrect typos, check for matching terms, PII, and classify.
    {
    "OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
    "NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
    "AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
    "Misrepresentation": null,
    
    "Classification": {
            "Category1": {
            "Score": 1.5113095059859916E-06
            },
            "Category2": {
            "Score": 0.12747249007225037
            },
            "Category3": {
            "Score": 0.98799997568130493
            },
            "ReviewRecommended": true
    },
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    },
    "PII": {
            "Email": [
                {
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 33
                }
                ],
            "IPA": [
                {
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 73
                }
                ],
            "Phone": [
                {
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 57
                },
                {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                },
                {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                },
                {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                },
                {
            "   CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
                }
                ],
             "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                 "Index": 89
                    }]
        },
    "Language": "eng",
    "Terms": [
        {
            "Index": 22,
            "OriginalIndex": 22,
            "ListId": 0,
            "Term": "crap"
        }
    ],
    "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
    }

## <a name="next-steps"></a>다음 단계

이 빠른 시작과 기타 .NET용 Content Moderator 빠른 시작을 위한 [Visual Studio 솔루션을 다운로드](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)하고 통합을 시작합니다.
