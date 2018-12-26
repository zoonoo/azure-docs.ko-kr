---
title: 텍스트 조정 API를 사용하여 텍스트 조정 - Content Moderator
titlesuffix: Azure Cognitive Services
description: 온라인 콘솔에서 텍스트 조정 API를 사용하여 텍스트 조정을 시험 사용해 보세요.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 95fd5507287a9294f4fca6af9cc5f01f0ea9fe1c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219790"
---
# <a name="moderate-text-from-the-api-console"></a>API 콘솔에서 텍스트 조정

Azure Content Moderator에서 [텍스트 조정 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)를 사용하여 텍스트 콘텐츠를 검색합니다. 작업은 콘텐츠에서 욕설을 검색하고 사용자 지정 및 공유 블랙리스트에 대해 콘텐츠를 비교합니다.


## <a name="get-your-api-key"></a>API key 가져오기
온라인 콘솔에서 API를 시험 사용할 수 있으려면 먼저 구독 키가 필요합니다. 구독 키는 **Ocp-Apim-Subscription-Key** 상자의 **설정** 탭에 있습니다. 자세한 내용은 [개요](overview.md)를 참조하세요.

## <a name="navigate-to-the-api-reference"></a>API 참조로 이동
[텍스트 조정 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)로 이동합니다. 

  **텍스트-화면** 페이지가 열립니다.

## <a name="open-the-api-console"></a>API 콘솔 열기
**개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 

  ![텍스트 - 화면 페이지 영역 선택](images/test-drive-region.png)

  **텍스트 - 화면** API 콘솔이 열립니다.

## <a name="select-the-inputs"></a>입력 선택

### <a name="parameters"></a>매개 변수
텍스트 화면에서 사용하려는 쿼리 매개 변수를 선택합니다. 이 예제의 경우 **언어**에 대한 기본값을 사용합니다. 작업에서 실행의 일부로 가능성이 있는 언어를 자동으로 검색하므로 비워둘 수도 있습니다.

> [!NOTE]
> **language** 매개 변수에 대해 `eng`를 할당하거나, 컴퓨터 지원 **분류** 응답(미리 보기 기능)을 확인하려면 비워둡니다. **이 기능은 영어만 지원합니다**.
>
> **욕설** 감지의 경우 이 문서에 나열된 지원되는 언어의 [ISO 639-3 코드](http://www-01.sil.org/iso639-3/codes.asp)를 사용하거나 비워둡니다.

**자동 수정**, **PII** 및 **분류(미리 보기)** 의 경우 **true**를 선택합니다. **ListId** 필드는 비워둡니다.

  ![텍스트 - 화면 콘솔 쿼리 매개 변수](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>콘텐츠 형식
**Content-type**의 경우 차단하려는 콘텐츠 유형을 선택합니다. 이 예제의 경우 기본 **텍스트/일반** 콘텐츠 형식을 사용합니다. **Ocp-Apim-Subscription-Key** 상자에 구독 키를 입력합니다.

### <a name="sample-text-to-scan"></a>검색할 샘플 텍스트
**요청 본문** 상자에 일부 텍스트를 입력합니다. 다음 예제에서는 텍스트의 의도적인 오타를 보여줍니다.

> [!NOTE]
> 다음 샘플 텍스트에서 잘못된 사회 보장 번호는 의도적인 것입니다. 샘플 입력 및 출력 형식을 전달하기 위해 사용되었습니다.

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>텍스트 분류 기능

다음 예제에서는 Content Moderator의 컴퓨터 기반 텍스트 분류 응답을 확인합니다. 잠재적으로 원하지 않는 콘텐츠를 검색할 수 있습니다. 플래그가 지정된 콘텐츠는 컨텍스트에 따라 부적절한 콘텐츠로 간주될 수 있습니다. 각 범주의 가능성을 전달할 뿐 아니라 콘텐츠에 대한 사용자 검토를 권장할 수 있습니다. 이 기능은 학습된 모델을 사용하여 상스럽거나 경멸적이거나 차별적인 언어를 식별합니다. 여기에는 은어, 약어, 불쾌한 단어, 의도적으로 철자가 틀린 단어의 검토가 포함됩니다. 

#### <a name="explanation"></a>설명

- `Category1`은 잠재적으로 특정 상황에서 성적으로 노골적이거나 음란한 것으로 간주될 수 있는 언어가 있음을 나타냅니다.
- `Category2`는 잠재적으로 특정 상황에서 성적으로 외설적이거나 도발적인 것으로 간주될 수 있는 언어가 있음을 나타냅니다.
- `Category3`는 잠재적으로 특정 상황에서 모욕적인 것으로 간주될 수 있는 언어가 있음을 나타냅니다.
- `Score`는 0에서 1 사이입니다. 점수가 높을수록 모델이 예측하는 범주에 해당할 가능성이 높아집니다. 이 미리 보기는 수동으로 코딩된 결과가 아닌 통계 모델을 사용합니다. 고유한 콘텐츠로 테스트하여 각 범주가 요구 사항과 얼마나 일치하는지 확인하는 것이 좋습니다.
- `ReviewRecommended`는 내부 점수 임계값에 따라 true 또는 false입니다. 고객은 이 값을 사용할지 또는 콘텐츠 정책에 따라 사용자 지정 임계값을 결정할지를 평가해야 합니다.

### <a name="analyze-the-response"></a>응답 분석
다음과 같은 응답이 API에서 다양한 인사이트를 보여줍니다. 잠재적인 욕설, PII, 분류(미리 보기) 및 자동 수정 버전이 포함됩니다.

> [!NOTE]
> 컴퓨터 지원 ‘분류’ 기능은 현재 미리 보기로 제공되며 영어만 지원합니다.

```
{
    "OriginalText": "Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
    "NormalizedText": "Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation": null,
    "PII": {
            "Email": [{
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 32
                }],
            "IPA": [{
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 72
                }],
            "Phone": [{
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 56
                }, {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                }, {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                }, {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                }, {
                "CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
            }],
         "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                "Index": 89
            }],
            "SSN": [{
                "Text": "999999999",
                "Index": 56
            }, {
                "Text": "999-99-9999",
                "Index": 266
            }]
        },
    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
        },
        "Category2": {
            "Score": 0.12747249007225037
        },
        "Category3": {
            "Score": 0.98799997568130493
        }
        },
    "Language": "eng",
    "Terms": [{
            "Index": 21,
            "OriginalIndex": 21,
            "ListId": 0,
         "Term": "crap"
        }],
    "Status": {
            "Code": 3000,
            "Description": "OK",
            "Exception": null
        },
     "TrackingId": "2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

JSON 응답의 모든 섹션에 대한 자세한 내용은 [텍스트 조정 API 개요](text-moderation-api.md)를 참조합니다.

## <a name="next-steps"></a>다음 단계

코드에서 REST API를 사용하거나 [텍스트 조정 .NET 빠른 시작](text-moderation-quickstart-dotnet.md)을 시작하여 응용 프로그램을 통합합니다.
