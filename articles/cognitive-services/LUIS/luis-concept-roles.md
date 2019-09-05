---
title: 엔터티에 대 한 역할-LUIS
titleSuffix: Azure Cognitive Services
description: 역할은 패턴에서만 사용되는 엔터티의 명명된 컨텍스트 하위 형식입니다. 예를 들어, 발화 `buy a ticket from New York to London`(뉴욕에서 런던까지 티켓 구매)에서 New York과 London은 둘 다 도시이지만 각각은 문장에서 의미가 서로 다릅니다. New York은 출발 도시이고 London은 도착 도시입니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: eead9e0fec8ac4322bc7816de4a4774f8be8129c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257962"
---
# <a name="entity-roles-for-contextual-subtypes"></a>상황별 하위 형식에 대 한 엔터티 역할

역할은 엔터티의 이름이 지정 된 하위 유형을 가질 수 있습니다. 역할은 미리 작성된 엔터티 유형 또는 사용자 지정 엔터티 유형에 사용할 수 있으며, 두 예제 발화 및 패턴 모두에서 사용할 수 있습니다. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>컴퓨터에서 학습 된 엔터티 예제 역할 예제

Utterance "뉴욕에서 **런던** **으로 티켓** 구입" 뉴욕 및 런던은 모두 도시 이지만 각 문장에는 서로 다른 의미가 있습니다. New York은 출발 도시이고 London은 도착 도시입니다. 

```
buy a ticket from New York to London
```

역할은 이러한 차이점에 대한 이름을 지정합니다.

|엔터티 유형|엔터티 이름|역할|용도|
|--|--|--|--|
|단순|위치|원본|비행기가 출발하는 위치|
|단순|위치|대상|비행기가 도착하는 위치|

## <a name="non-machine-learned-entity-example-of-roles"></a>비 기계어 학습 엔터티의 역할 예제

Utterance "모임을 8에서 9로 예약"에서 두 숫자는 시간을 나타내지만 각 시간은 utterance에서 서로 다른 의미를 갖습니다. 역할은 차이점에 대 한 이름을 제공 합니다. 

```
Schedule the meeting from 8 to 9
```

|엔터티 유형|역할 이름|값|
|--|--|--|
|미리 빌드된 datetimeV2|Starttime|8|
|미리 빌드된 datetimeV2|Endtime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Utterance의 여러 엔터티가 역할과 동일 합니까? 

여러 엔터티가 utterance에 존재할 수 있으며 역할을 사용 하지 않고 추출할 수 있습니다. 문장의 컨텍스트에서 값이 있는 엔터티의 버전을 나타내는 경우에는 역할을 사용 해야 합니다. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>의미 없는 중복에 대해 역할을 사용 하지 않음

Utterance에 위치 `I want to travel to Seattle, Cairo, and London.`목록이 포함 된 경우이 목록은 각 항목에 추가 의미가 없는 목록입니다. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>중복이 의미를 나타내는 경우 역할 사용

Utterance에 의미가 `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`있는 위치 목록이 포함 되어 있으면 원본, layover 및 대상의 이러한 의미를 역할과 함께 캡처해야 합니다.

### <a name="roles-can-indicate-order"></a>역할은 순서를 나타낼 수 있습니다.

추출 `I want to first start with Seattle, second London, then third Cairo`하려는 주문을 나타내도록 utterance이 변경 되 면 몇 가지 방법으로 추출할 수 있습니다. 역할, `first start with` `second`, ,`third`을 나타내는 토큰에 태그를 지정할 수 있습니다. 또한 미리 작성 된 엔터티 **서 수** 를 사용 하 고 복합 엔터티에서 **GeographyV2** 미리 작성 된 엔터티를 사용 하 여 순서와 위치를 파악할 수 있습니다. 

## <a name="how-are-roles-used-in-example-utterances"></a>예 길이 발언 역할은 어떻게 사용 되나요?

엔터티에 역할이 있고 엔터티가 예 utterance로 표시 되는 경우 엔터티를 선택 하거나 엔터티 및 역할을 선택 하는 것을 선택할 수 있습니다. 

다음 예제에서는 엔터티 및 역할을 사용 길이 발언 합니다.

|토큰 보기|엔터티 뷰|
|--|--|
|**시애틀** 에 대해 자세히 알아보는 것이 흥미롭습니다.|{Location}에 대해 자세히 알아보세요.|
|시애틀에서 뉴욕으로 티켓 구입|{Location: 원점}에서 {Location: Destination} (으)로 티켓 구입|

## <a name="how-are-roles-used-in-patterns"></a>패턴에서 역할이 사용되는 방식
패턴의 템플릿 발화에서 역할은 발화 내에서 사용됩니다. 

|엔터티 역할을 사용한 패턴|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>패턴의 역할 구문
엔터티 및 역할은 괄호(`{}`)로 묶습니다. 엔터티 및 역할은 콜론으로 구분됩니다. 

## <a name="entity-roles-versus-collaborator-roles"></a>엔터티 역할과 협력자 역할 비교

엔터티 역할은 LUIS 앱의 데이터 모델에 적용 됩니다. [협력자 또는 참가자](luis-concept-keys.md#contributions-from-other-authors) 역할은 제작 액세스 수준에 적용 됩니다. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>다음 단계

* 컴퓨터에서 학습 하지 않은 엔터티가 포함 된 엔터티 역할을 사용 하 여 [실습 자습서](tutorial-entity-roles.md) 사용
* [역할](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)을 추가하는 방법 알아보기
