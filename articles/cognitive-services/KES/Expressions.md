---
title: 구조화된 쿼리 식 - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: KES(Knowledge Exploration Service) API에서 구조화된 쿼리 식을 사용하는 방법에 대해 알아봅니다.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: bdde2dfc9ab8e8ffdf7123c916538a8c98ecfce9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129170"
---
# <a name="structured-query-expression"></a>구조화된 쿼리 식

구조화된 쿼리 식은 데이터 인덱스에 대해 평가하도록 작업의 집합을 지정합니다.  특성 쿼리 식과 상위 수준 함수로 구성됩니다.  [*evaluate*](evaluateMethod.md) 메서드를 사용하여 식과 일치하는 개체를 계산합니다.  다음은 2013년 이후 Jaime Teevan이 쓴 저서를 반환하는 학술 저서 도메인의 예제입니다.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

구조화된 쿼리 식은 [*interpret*](interpretMethod.md) 요청에서 가져올 수 있습니다. 여기서 각 해석의 의미 체계 출력은 입력된 자연어 쿼리와 일치하는 인덱스 개체를 반환하는 구조화된 쿼리 식입니다.  또는 이 섹션에 설명된 구문을 사용하여 수동으로 작성할 수 있습니다.

## <a name="attribute-query-expression"></a>특성 쿼리 식

특성 쿼리 식은 특정 특성에 대해 일치하는 개체 집합을 식별합니다.  [schema](SchemaFormat.md)에 지정된 인덱스 작업 및 특성 유형에 따라 다양한 일치 작업이 지원됩니다.

| type | 작업(Operation) | 예 |
|------|-------------|------------|
| 문자열 | equals | Title='latent semantic analysis'  (canonical + synonyms) |
| 문자열 | equals | Author.Name=='susan t dumais'  (canonical only)|
| 문자열 | starts_with | Title='latent s'... |
| Int32/Int64/Double | equals | Year=2000 |
| Int32/Int64/Double | starts_with | Year='20'... (“20”으로 시작하는 10진수 값) |
| Int32/Int64/Double | is_between | Year&lt;2000 <br/> Year&lt;=2000 <br/> Year&gt;2000 <br/> Year&gt;=2000 <br/> Year=[2010,2012) *(왼쪽 경계 값만 포함: 2010, 2011)* <br/> Year=[2000,2012] *(두 경계 값 포함: 2010, 2011, 2012)* |
| Date | equals | BirthDate='1984-05-14' |
| Date | is_between | BirthDate&lt;='2008/03/14' <br/> PublishDate=['2000-01-01','2009-12-31'] |
| Guid | equals | Id='602DD052-CC47-4B23-A16A-26B52D30C05B' |


예를 들어 “Title='latent s'...”는 제목이 “latent s” 문자열로 시작하는 모든 학술 저서와 일치합니다.  이 식을 평가하기 위해 Title 특성은 인덱스를 빌드하는 데 사용되는 스키마에서 “starts_with” 작업을 지정해야 합니다.

연결된 동의어를 사용한 특성의 경우 쿼리 식은 canonical 값이 “==” 연산자를 사용하는 주어진 문자열과 일치하는 개체 또는 canonical/동의어 값이 “=” 연산자 사용과 일치하는 개체를 지정할 수 있습니다.  둘 다 특성 정의에서 지정해야 하는 “equals” 연산자가 필요합니다.


## <a name="functions"></a>Functions

기본 특성 쿼리에서 더 복잡한 쿼리 식의 생성을 허용하는 기본 제공되는 함수 집합이 있습니다.

### <a name="and-function"></a>And 함수

`And(expr1, expr2)`

두 입력된 쿼리 식의 교집합을 반환합니다.

다음 예제에서는 정보 검색에 대해 2000년에 게시된 학술 저서를 반환합니다.

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Or 함수

`Or(expr1, expr2)`

두 입력된 쿼리 식의 합집합을 반환합니다.

다음 예제에서는 정보 검색 또는 사용자 모델링에 대해 2000년에 게시된 학술 저서를 반환합니다.

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Composite 함수

`Composite(expr)`

공통 복합 특성의 하위 특성 기준 쿼리로 이루어진 내부 식을 캡슐화하는 쿼리를 반환합니다.  캡슐화는 개별적으로 내부 식을 충족하는 하나 이상의 값을 갖기 위해 일치하는 데이터 개체의 복합 특성이 필요합니다.  복합 특성 중 하위 특성의 쿼리 식은 Composite() 함수를 사용하여 캡슐화되어야 다른 쿼리 식과 결합할 수 있습니다.

예를 들어 다음 식은 “harry shum”이 “microsoft”에 있는 동안 저술한 학술 저서를 반환합니다.

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

반면에 다음 식은 “harry shum”이 저서 중 한 명이며, “microsoft”가 소속 중 하나인 학술 저서를 반환합니다.

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
