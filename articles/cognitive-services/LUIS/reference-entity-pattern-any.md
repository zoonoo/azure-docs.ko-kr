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
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "75979171"
---
# <a name="patternany-entity"></a>Pattern.any 엔터티

Pattern.any는 엔터티가 시작되고 끝나는 위치를 표시하기 위해 패턴의 템플릿 발화에서만 사용되는 가변 길이 자리 표시자입니다.  

Pattern.any 엔터티는 의도 사용자 예제가 아닌 [패턴](luis-how-to-model-intent-pattern.md) 템플릿 예제에서 표시되어야 합니다.

**다음과 같은 경우 이 엔터티가 적합합니다.**

* 엔터티의 끝이 발화의 나머지 텍스트와 혼동될 수 있는 경우

## <a name="usage"></a>사용

클라이언트 애플리케이션이 제목을 기준으로 도서를 검색하는 경우 pattern.any는 전체 제목을 추출합니다. 이 도서 검색에서 pattern.any를 사용하는 템플릿 발화는 `Was {BookTitle} written by an American this year[?]`입니다.

다음 표에서 각 행에는 두 가지 버전의 발화가 있습니다. Top utterance는 LUIS가 처음에 utterance를 확인 하는 방법입니다. 책 제목이 시작 되 고 끝나는 위치는 명확 하지 않습니다. Bottom utterance는 패턴을 사용 하 여 엔터티의 시작과 끝을 표시 합니다.

|굵게 표시 된 엔터티가 있는 Utterance|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>**The Man Who Mistook His Wife for a Hat and Other Clinical Tales**는 올해 미국에서 저술되었나요?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>**Half Asleep in Frog Pajamas**는 올해 미국에서 저술되었나요?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>**레몬 케이크의 특정 작업** 인 경우 미국 서 면에서 작성 한 Novel?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**내 주머니에 Wocket가 있습니다.** 은 올해 미국에서 저술되었나요?|
||



## <a name="example-json"></a>예제 JSON

다음과 같은 쿼리를 고려해 보세요.

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

패턴으로 추출할 포함 된 폼 이름 사용:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 예측 엔드포인트 응답](#tab/V3)

이는 쿼리 문자열에 `verbose=false` 가 설정 된 경우 JSON입니다.

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

이는 쿼리 문자열에 `verbose=true` 가 설정 된 경우 JSON입니다.

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>다음 단계

이 [자습서](luis-tutorial-pattern.md)에서는 패턴을 사용 하 여 길이 발언이 잘 포맷 되 고 데이터 끝이 utterance의 나머지 단어와 쉽게 혼동 될 수 있는 길이 발언에서 데이터를 추출 하는 모든 엔터티를 사용 합니다 **.**
