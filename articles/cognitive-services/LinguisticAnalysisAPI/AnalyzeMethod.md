---
title: Linguistic Analysis API의 분석 메서드 | Microsoft Docs
description: Linguistic Analysis API의 분석 메서드를 사용하여 특정 자연어 입력을 분석하는 방법입니다.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 12/13/2016
ms.author: lesun
ms.openlocfilehash: b17a00f31845bfa05572dff7ca94e9a1ffd69586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373207"
---
# <a name="analyze-method"></a>분석 메서드

**분석** REST API는 주어진 자연어 입력을 분석하는 데 사용됩니다.
여기에는 해당 입력 내 [문장과 토큰](Sentences-and-Tokens.md) 찾기, [음성 부분 태그](POS-tagging.md) 찾기 또는 [구성성 트리](Constituency-Parsing.md) 찾기만 포함될 수도 있습니다.
관련 분석기를 선택하여 원하는 결과를 지정할 수 있습니다.
모든 사용 가능한 분석기를 나열하려면 **[분석기](AnalyzersMethod.md)** 를 검토합니다.

입력 문자열의 언어를 지정해야 합니다.

**REST 엔드포인트:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>요청 매개 변수

Name | type | 필수 | 설명
-----|-------|----------|------------
**language**    | string | 예 | 분석에 사용되는 두 자로 된 ISO 언어 코드입니다. 예를 들어, 영어는 “en”입니다.
**analyzerIds** | 문자열 목록 | 예 | 적용할 분석기의 GUID 목록입니다. 자세한 내용은 분석기 설명서를 참조하세요.
**text**        | string | 예 | 분석할 원시 입력입니다. 이는 단어나 구처럼 짧은 문장, 전체 문장 또는 전체 단락이나 담론일 수 있습니다.

<br>
## <a name="response-json"></a>응답(JSON)
요청에 지정된 각 특성에 대한 분석 출력의 배열입니다.

결과는 다음과 같이 표시됩니다.

Name | type | 설명
-----|------|--------------
analyzerId | string | 지정된 분석기의 GUID
result | object | 분석기 결과

결과의 형식은 입력된 분석기 형식에 따라 달라 집니다.

### <a name="tokens-response-json"></a>토큰 응답(JSON)

Name | type | 설명
-----|------|-------------
result | 문장 개체의 목록 | 텍스트 내에서 식별된 문장 경계 |
result[x].Offset | int | 각 문장의 시작 문자 오프셋 |
result[x].Len | int | 각 문장의 문자 길이 |
result[x].Tokens | 토큰 개체의 목록 | 문장 내에서 식별된 토큰 경계 |
result[x].Tokens[y].Offset | int | 토큰의 시작 문자 오프셋 |
result[x].Tokens[y].Len | int | 각 토큰의 문자 길이 |
result[x].Tokens[y].RawToken | string | 정규화하기 전의 해당 토큰 내 문자 |
result[x].Tokens[y].NormalizedToken | string | [구문 분석 트리](Constituency-Parsing.md)에서 사용하도록 보호되는 문자의 정규화된 형식(예: 여는 괄호 문자 ‘(’는 ‘-LRB-’가 됨) |

입력 예제: ‘This is a test(이것은 테스트입니다). Hello(안녕하세요).’
예제 JSON 응답:
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>POS 태그 응답(JSON)

결과는 문자열 목록의 목록입니다.
각 문장에서 각 토큰에 대한 하나의 POS 태그인 POS 태그의 목록이 있습니다.
각 POS 태그에 해당하는 토큰을 찾기 위해 토큰화 개체도 요구할 수 있습니다.

### <a name="constituency-tree-response-json"></a>팀원 트리 응답(JSON)

결과는 입력에서 찾은 각 문장에 대한 하나의 구문 분석 트리인 문자열의 목록입니다.
구문 분석 트리는 괄호로 묶은 형식으로 표현됩니다.

<br>

## <a name="example"></a>예

`POST /analyze`

요청 본문: JSON 페이로드
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?" 
}
```

응답: JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04", 
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2", 
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```

