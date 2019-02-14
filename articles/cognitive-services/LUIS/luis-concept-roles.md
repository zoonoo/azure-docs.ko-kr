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
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: 958194d49cd403caeaf9dd21dd90a02cab098e45
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881460"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>패턴의 엔터티 역할은 컨텍스트 하위 형식입니다.
역할은 [패턴](luis-concept-patterns.md)에서만 사용되는 엔터티의 명명된 컨텍스트 하위 형식입니다.

예를 들어, 발화 `buy a ticket from New York to London`(뉴욕에서 런던까지 티켓 구매)에서 New York과 London은 둘 다 도시이지만 각각은 문장에서 의미가 서로 다릅니다. New York은 출발 도시이고 London은 도착 도시입니다. 

역할은 이러한 차이점에 대한 이름을 지정합니다.

|엔터티|역할|목적|
|--|--|--|
|위치|원본|비행기가 출발하는 위치|
|위치|destination|비행기가 도착하는 위치|
|미리 빌드된 datetimeV2|to|종료 날짜|
|미리 빌드된 datetimeV2|from|시작 날짜|

## <a name="how-are-roles-used-in-patterns"></a>패턴에서 역할이 사용되는 방식
패턴의 템플릿 발화에서 역할은 발화 내에서 사용됩니다. 

|엔터티 역할을 사용한 패턴|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>패턴의 역할 구문
엔터티 및 역할은 괄호(`{}`)로 묶습니다. 엔터티 및 역할은 콜론으로 구분됩니다. 


[!INCLUDE [H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="example-role-for-entities"></a>엔터티에 대한 역할 예제

역할은 발화 내에서 문맥상 학습된 엔터티의 위치입니다. 발화에 포함된 엔터티 형식이 한 개를 초과하는 경우 가장 효과적입니다. 엔터티 형식에 대한 가장 쉬운 예는 시작과 끝 위치를 구분하는 것입니다. 위치는 수많은 엔터티 형식으로 표현할 수 있습니다. 

사용 사례의 예는 한 부서의 직원을 각 부서가 목록에 항목으로 있는 다른 부서로 이동하는 것입니다. 예:  

`Move [PersonName] from [Department:from] to [Department:to]`. 

반환된 예측에서 두 부서 엔터티는 JSON 응답으로 반환되고 각각 역할 이름을 포함합니다. 

## <a name="roles-with-prebuilt-entities"></a>미리 작성된 엔터티와 역할

미리 작성된 엔터티와 함께 역할을 사용하여 발화 내에서 미리 만들어진 엔터티의 다른 인스턴스에 의미를 부여합니다. 

### <a name="roles-with-datetimev2"></a>datetimeV2와 역할

미리 작성된 엔터티 datetimeV2는 발화에서 날짜와 시간의 다양성을 광범위하게 이해하는 작업을 원활하게 수행합니다. 미리 작성된 엔터티의 기본 이해와 다르게 날짜와 날짜 범위를 지정하려고 할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

* [역할](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)을 추가하는 방법 알아보기
