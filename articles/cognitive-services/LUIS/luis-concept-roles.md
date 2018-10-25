---
title: 패턴 기반 엔터티에서 역할을 사용하는 방법 이해
titleSuffix: Azure Cognitive Services
description: 역할은 패턴에서만 사용되는 엔터티의 명명된 컨텍스트 하위 형식입니다. 예를 들어, 발화에서 뉴욕에서 런던까지 티켓을 구매하고 뉴욕과 런던은 둘 다 도시이지만 각각은 문장에서 의미가 서로 다릅니다. New York은 출발 도시이고 London은 도착 도시입니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9bbbb797cd7e7d1cea52f1d5b1b491998b595db7
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638087"
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

## <a name="roles-versus-hierarchical-entities"></a>역할 및 계층 구조 엔터티
계층 구조 엔터티는 역할과 동일한 컨텍스트 정보를 제공하지만 **의도**의 발화에 대한 컨텍스트 정보만 제공합니다. 마찬가지로 역할은 계층 구조 엔터티와 동일한 컨텍스트 정보를 제공하지만 **패턴**의 컨텍스트 정보만 제공합니다.

|컨텍스트 학습|사용 위치|
|--|--|
|계층 구조 엔터티|의도|
|roles|패턴|

## <a name="roles-with-prebuilt-entities"></a>미리 작성된 엔터티와 역할

미리 작성된 엔터티와 함께 역할을 사용하여 발화 내에서 미리 만들어진 엔터티의 다른 인스턴스에 의미를 부여합니다. 

### <a name="roles-with-datetimev2"></a>datetimeV2와 역할

미리 작성된 엔터티 datetimeV2는 발화에서 날짜와 시간의 다양성을 광범위하게 이해하는 작업을 원활하게 수행합니다. 미리 작성된 엔터티의 기본 이해와 다르게 날짜와 날짜 범위를 지정하려고 할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

* [역할](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)을 추가하는 방법 알아보기
