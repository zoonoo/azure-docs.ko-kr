---
title: 텍스트 조정 API를 사용하여 텍스트 조정 - Content Moderator
titlesuffix: Azure Cognitive Services
description: 온라인 콘솔에서 텍스트 조정 API를 사용하여 텍스트 조정을 시험 사용해 보세요.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 21209548d1cfe7b6eebb1757e817a12c797e78a9
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688832"
---
# <a name="moderate-text-from-the-api-console"></a>API 콘솔에서 텍스트 조정

사용 합니다 [텍스트 조정 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) 의 Azure Content Moderator 불경 한 언어에 대 한 텍스트 콘텐츠를 검색 하 여 사용자 지정 및 공유 목록을 비교 합니다.

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

## <a name="analyze-the-response"></a>응답 분석

다음과 같은 응답이 API에서 다양한 인사이트를 보여줍니다. 잠재적인 불경 한, 개인 데이터를 분류 (미리 보기) 및 자동 수정 버전을 포함합니다.

> [!NOTE]
> 컴퓨터 지원 ‘분류’ 기능은 현재 미리 보기로 제공되며 영어만 지원합니다.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"6657789887",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1 Microsoft Way, Redmond, WA 98052",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

에 대 한 자세한 설명은 JSON 응답에서 모든 섹션을 참조 합니다 [텍스트 조정](text-moderation-api.md) 개념 가이드입니다.

## <a name="next-steps"></a>다음 단계

코드에서 REST API를 사용 하거나 사용 하 여 시작 합니다 [텍스트 조정.NET 빠른 시작](text-moderation-quickstart-dotnet.md) 응용 프로그램을 통합할 수입니다.
