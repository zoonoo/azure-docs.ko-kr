---
title: 패턴 기반 엔터티에서 역할을 사용하는 방법 이해 - Azure| Microsoft Docs
description: 패턴 기반 엔터티에서 역할을 사용하여 컨텍스트 엔터티 하위 형식에 이름을 지정하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: ab6100e33fb767528b87c6afde4c5ef275fc7c81
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35383483"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>패턴의 엔터티 역할은 컨텍스트 하위 형식입니다.
역할은 [패턴](luis-concept-patterns.md)에서만 사용되는 엔터티의 명명된 컨텍스트 하위 형식입니다.

예를 들어, 발화 `buy a ticket from New York to London`(뉴욕에서 런던까지 티켓 구매)에서 New York과 London은 둘 다 도시이지만 각각은 문장에서 의미가 서로 다릅니다. New York은 출발 도시이고 London은 도착 도시입니다. 

역할은 이러한 차이점에 대한 이름을 지정합니다.

|엔터티|역할|목적|
|--|--|--|
|위치|원본|비행기가 출발하는 위치|
|위치|destination|비행기가 도착하는 위치|

## <a name="how-are-roles-used-in-patterns"></a>패턴에서 역할이 사용되는 방식
패턴의 템플릿 발화에서 역할은 발화 내에서 사용됩니다. 

```
buy a ticket from {Location:origin} to {Location:destination}
```

## <a name="role-syntax-in-patterns"></a>패턴의 역할 구문
엔터티 및 역할은 괄호(`{}`)로 묶습니다. 엔터티 및 역할은 콜론으로 구분됩니다. 

## <a name="roles-versus-hierarchical-entities"></a>역할 및 계층 구조 엔터티
계층 구조 엔터티는 역할과 동일한 컨텍스트 정보를 제공하지만 **의도**의 발화에 대한 컨텍스트 정보만 제공합니다. 마찬가지로 역할은 계층 구조 엔터티와 동일한 컨텍스트 정보를 제공하지만 **패턴**의 컨텍스트 정보만 제공합니다.

|컨텍스트 학습|사용 위치|
|--|--|
|계층 구조 엔터티|의도|
|roles|패턴|

## <a name="next-steps"></a>다음 단계

* [역할](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)을 추가하는 방법 알아보기
