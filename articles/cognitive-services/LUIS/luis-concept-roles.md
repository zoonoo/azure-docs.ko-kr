---
title: 엔터티에 대한 역할
titleSuffix: Azure Cognitive Services
description: 역할은 패턴에서만 사용되는 엔터티의 명명된 컨텍스트 하위 형식입니다. 예를 들어, 발화 `buy a ticket from New York to London`(뉴욕에서 런던까지 티켓 구매)에서 New York과 London은 둘 다 도시이지만 각각은 문장에서 의미가 서로 다릅니다. New York은 출발 도시이고 London은 도착 도시입니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 318e71b68bbabeeef34c75a412f9fdd5b6db754a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65073020"
---
# <a name="entity-roles-for-contextual-subtypes"></a>상황에 맞는 하위 형식에 대 한 엔터티 역할

역할 엔터티 하위 형식에는 명명 된를 허용 합니다. 역할은 미리 작성된 엔터티 유형 또는 사용자 지정 엔터티 유형에 사용할 수 있으며, 두 예제 발화 및 패턴 모두에서 사용할 수 있습니다. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>역할의 컴퓨터에서 학습 엔터티 예

utterance에서 "에서 비행기표를 구입 **뉴욕** 하 **런던**런던 및 뉴욕은 도시 되지만 문장에서 각은 의미가 달라 집니다. New York은 출발 도시이고 London은 도착 도시입니다. 

```
buy a ticket from New York to London
```

역할은 이러한 차이점에 대한 이름을 지정합니다.

|엔터티 유형|엔터티 이름|역할|목적|
|--|--|--|--|
|단순|Location|원본|비행기가 출발하는 위치|
|단순|Location|대상|비행기가 도착하는 위치|

## <a name="non-machine-learned-entity-example-of-roles"></a>역할의 컴퓨터 학습 엔터티 예

utterance에서 "9 8에서 회의 예약" 모두 숫자 번 나타내지만 때마다를 utterance에서 다른 의미를 갖습니다. 역할에는 차이점에 대 한 이름을 제공합니다. 

```
Schedule the meeting from 8 to 9
```

|엔터티 유형|역할 이름|Value|
|--|--|--|
|미리 빌드된 datetimeV2|starttime|8|
|미리 빌드된 datetimeV2|endtime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>여러 엔터티를 utterance에서 역할로 마찬가지 인가요? 

여러 엔터티를 utterance에 존재할 수 있으며 역할을 사용 하지 않고를 추출할 수 있습니다. 문장의 컨텍스트를 사용 하 여 나타냅니다 엔터티의 버전 값을 가진 경우 역할을 사용 해야 합니다. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>의미 없는 중복 역할을 사용 하지 마세요

utterance 위치 목록을 포함 하는 경우 `I want to travel to Seattle, Cairo, and London.`, 여기서 각 항목에 추가적인 의미 없는 목록입니다. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>중복 의미를 표시 하는 경우 역할을 사용 합니다.

utterance 의미를 사용 하 여 위치 목록을 포함 하는 경우 `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, 역할과 돌아오자 원점과 대상의이 의미를 캡처해야 합니다.

### <a name="roles-can-indicate-order"></a>역할에서 순서를 나타낼 수 있습니다.

utterance을 추출 하려고 하는 순서를 나타내도록 변경 하는 경우 `I want to first start with Seattle, second London, then third Cairo`에 여러 가지 방법으로 추출할 수 있습니다. 역할을 나타내는 토큰을 태그로 지정할 수 있습니다 `first start with`, `second`, `third`합니다. 또한 미리 작성 된 엔터티를 사용할 수 있습니다 **서 수** 하며 **GeographyV2** 순서와 위치 개념을 캡처하려면 복합 엔터티에서 미리 작성 된 엔터티. 

## <a name="how-are-roles-used-in-example-utterances"></a>예제 표현에서 역할을 사용 하는 방법

엔터티는 역할을 하 고 엔터티는 예제 utterance 표시 됩니다, 경우만 엔터티를 선택 또는 선택 된 엔터티 및 역할 중에서 선택을 해야 합니다. 

다음 예제에서는 길이 발언 엔터티 및 역할을 사용합니다.

|토큰 보기|엔터티 보기|
|--|--|
|에 대 한 자세한 내용은 학습 과정 **시애틀**|{Location}에 대 한 자세한 내용은 관심이|
|뉴욕에 시애틀에서 비행기표를 구입|{0} 위치: 대상} {위치: 원본}에서 비행기표를 구입|

## <a name="how-are-roles-used-in-patterns"></a>패턴에서 역할이 사용되는 방식
패턴의 템플릿 발화에서 역할은 발화 내에서 사용됩니다. 

|엔터티 역할을 사용한 패턴|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>패턴의 역할 구문
엔터티 및 역할은 괄호(`{}`)로 묶습니다. 엔터티 및 역할은 콜론으로 구분됩니다. 

## <a name="entity-roles-versus-collaborator-roles"></a>공동 작업자 역할 및 엔터티 역할

LUIS 앱의 데이터 모델 엔터티 역할 적용 됩니다. [협력자](luis-concept-collaborator.md) 역할 제작 액세스 수준에 적용 합니다. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>다음 단계

* 사용 된 [실습 자습서](tutorial-entity-roles.md) 엔터티에 역할을 사용 하 여 컴퓨터 학습 되지 않은 엔터티를 사용 하 여
* [역할](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)을 추가하는 방법 알아보기
