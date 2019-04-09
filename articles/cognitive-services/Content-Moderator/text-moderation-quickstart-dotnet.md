---
title: '빠른 시작: C#에서 텍스트 콘텐츠 분석 - Content Moderator'
titlesuffix: Azure Cognitive Services
description: .NET용 Content Moderator SDK를 사용하여 다양하고 불쾌한 자료에 대한 텍스트 콘텐츠를 분석하는 방법
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: sajagtap
ms.openlocfilehash: 09fd58fa33873c06ac5dab4970af199bc3030479
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756540"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>빠른 시작: C#에서 불쾌한 자료에 대한 텍스트 콘텐츠 분석

이 문서에서는 [.NET용 Content Moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)를 사용하여 시작할 수 있도록 지원하는 정보 및 코드 샘플을 제공합니다. 잠재적으로 불쾌한 자료를 조정할 목적으로 텍스트 콘텐츠의 용어 기반 필터링 및 분류를 실행하는 방법을 배웁니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="prerequisites"></a>필수 조건
- Content Moderator 구독 키. [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Content Moderator를 구독하고 키를 가져옵니다.
- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전

> [!NOTE]
> 이 가이드에서는 체험 계층 Content Moderator 구독을 사용합니다. 각 구독 계층과 함께 제공되는 것에 대한 내용은 [가격 책정 및 제한](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/) 페이지를 참조하세요.

## <a name="create-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio에서 새 **콘솔 앱(.NET Framework)** 프로젝트를 만들고 **TextModeration**으로 이름을 지정합니다. 
1. 솔루션에 다른 프로젝트가 있는 경우 이것을 단일 시작 프로젝트로 선택합니다.
1. 필요한 NuGet 패키지를 가져옵니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 누르고 **NuGet 패키지 관리**를 선택한 후 다음 패키지를 찾아 설치합니다.
    - `Microsoft.Azure.CognitiveServices.ContentModerator`
    - `Microsoft.Rest.ClientRuntime`
    - `Newtonsoft.Json`

## <a name="add-text-moderation-code"></a>텍스트 조정 코드 추가

다음으로 이 가이드에서 프로젝트로 코드를 복사하여 붙여넣고 기본 콘텐츠 조정 시나리오를 구현합니다.

### <a name="include-namespaces"></a>네임스페이스 포함

*Program.cs* 파일 위에 다음 `using` 문을 추가합니다.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Content Moderator 클라이언트 만들기

다음 코드를 *Program.cs* 파일에 추가하여 구독에 대한 Content Moderator 클라이언트 공급자를 만듭니다. 동일한 네임스페이스에서 **Program** 클래스와 함께 코드를 추가합니다. **AzureRegion** 및 **CMSubscriptionKey** 필드를 해당 지역 식별자 및 구독 키 값으로 업데이트해야 합니다.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>입력 및 출력 대상 설정

_Program.cs_의 **Program** 클래스에 다음 정적 필드를 추가합니다. 이러한 필드는 입력 텍스트 콘텐츠 및 출력 JSON 콘텐츠에 대한 파일을 지정합니다.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

*TextFile.txt* 입력 파일을 만들고 해당 경로를 적절하게 업데이트해야 합니다(상대 경로는 실행 디렉터리에 상대적). _TextFile.txt_를 열고 조정할 텍스트를 추가합니다. 이 빠른 시작에서는 다음 샘플 텍스트를 사용합니다.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>입력 텍스트 로드

**Main** 메서드에 다음 코드를 추가합니다. **ScreenText** 메서드는 필수 작업입니다. 해당 매개 변수는 어떤 콘텐츠 조정 작업을 수행할지 지정합니다. 이 예제에서는 다음을 수행할 메서드를 구성합니다.
- 텍스트에서 잠재적으로 불경한 언어를 검색합니다.
- 텍스트를 정규화하고 오타를 자동으로 고칩니다.
- 미국 및 영국 전화 번호, 이메일 주소, 미국 우편 주소와 같은 개인 데이터를 검색합니다.
- 머신 러닝 기반 모델을 사용하여 세 가지 범주로 텍스트를 분류합니다.

수행할 작업에 대해 자세히 알아보려면 [다음 단계](#next-steps) 섹션에 있는 링크를 따라가세요.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>프로그램 실행

프로그램이 JSON 문자열 데이터를 _TextModerationOutput.txt_ 파일에 작성합니다. 이 빠른 시작에 사용된 샘플 텍스트는 다음 출력을 제공합니다.

```json
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
        "CountryCode": "UK",
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
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 지정된 텍스트 샘플에 대한 관련 정보를 반환하는 Content Moderator 서비스를 사용하는 간단한 .NET 애플리케이션을 개발했습니다. 다음으로, 다양한 플래그 및 분류가 의미하는 바를 자세히 알아보므로 어떤 데이터가 필요하고 앱에서 데이터를 어떻게 처리해야 하는지 결정할 수 있습니다.

> [!div class="nextstepaction"]
> [텍스트 조정 가이드](text-moderation-api.md)
