---
title: 패턴. 모든 엔터티 형식-LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any는 엔터티가 시작되고 끝나는 위치를 표시하기 위해 패턴의 템플릿 발화에서만 사용되는 가변 길이 자리 표시자입니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: cda6c724a36a73dc34c2bf8e7158e3e3ec92d46b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563216"
---
# <a name="patternany-entity"></a>Pattern.any 엔터티 

Pattern.any는 엔터티가 시작되고 끝나는 위치를 표시하기 위해 패턴의 템플릿 발화에서만 사용되는 가변 길이 자리 표시자입니다.  

Pattern.any 엔터티는 의도 사용자 예제가 아닌 [패턴](luis-how-to-model-intent-pattern.md) 템플릿 예제에서 표시되어야 합니다.

**엔터티는 다음과 같은 경우에 적합 합니다.**

* 엔터티의 끝이 발화의 나머지 텍스트와 혼동될 수 있는 경우 

## <a name="usage"></a>사용법

클라이언트 애플리케이션이 제목을 기준으로 도서를 검색하는 경우 pattern.any는 전체 제목을 추출합니다. 이 도서 검색에서 pattern.any를 사용하는 템플릿 발화는 `Was {BookTitle} written by an American this year[?]`입니다. 

다음 표에서 각 행에는 두 가지 버전의 발화가 있습니다. Top utterance는 LUIS가 처음에 utterance를 확인 하는 방법입니다. 책 제목이 시작 되 고 끝나는 위치는 명확 하지 않습니다. Bottom utterance는 패턴을 사용 하 여 엔터티의 시작과 끝을 표시 합니다. 

|굵게 표시 된 엔터티가 있는 Utterance|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>**The Man Who Mistook His Wife for a Hat and Other Clinical Tales**는 올해 미국에서 저술되었나요?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>**Half Asleep in Frog Pajamas**는 올해 미국에서 저술되었나요?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>**The Particular Sadness of Lemon Cake: A Novel**은 올해 미국에서 저술되었나요?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**There's A Wocket In My Pocket!** 은 올해 미국에서 저술되었나요?|
||

## <a name="example-json"></a>예제 JSON

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

이 [자습서](luis-tutorial-pattern-any.md)에서는 패턴을 사용 하 여 길이 발언이 잘 포맷 되 고 데이터 끝이 utterance의 나머지 단어와 쉽게 혼동 될 수 있는 길이 발언에서 데이터를 추출 하는 모든 엔터티를 사용 합니다 **.**