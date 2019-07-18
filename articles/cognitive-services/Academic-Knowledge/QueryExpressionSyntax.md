---
title: 쿼리 식 구문 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API에서 쿼리 식 구문을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 95c2e9d3f54f967b3ebb434c742f69251b80573e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61336759"
---
# <a name="query-expression-syntax"></a>쿼리 식 구문

앞서 살펴본 것처럼 **해석** 요청에 대한 응답에는 쿼리 식이 포함되어 있습니다. 사용자의 쿼리를 해석하는 문법은 각 해석에 대한 쿼리 식을 생성했습니다. 그 후 쿼리 식을 사용하여 엔터티 검색 결과를 검색하는 **평가** 요청을 실행할 수 있습니다.

또한 고유의 쿼리 식을 작성하여 **평가** 요청에 사용할 수 있습니다. 이는 사용자의 작업에 따라 쿼리 식을 만드는 고유의 사용자 인터페이스를 빌드하는 경우에 유용할 수 있습니다. 이렇게 하려면 쿼리 식에 대한 구문을 알고 있어야 합니다.  

쿼리 식에 포함할 수 있는 각 엔터티 특성에는 특정 데이터 형식 및 가능한 쿼리 연산자 집합이 있습니다. 엔터티 특성 및 각 특성에 지원되는 연산자 집합은 [엔터티 특성](EntityAttributes.md)에서 지정됩니다. 단일 값 쿼리는 특성에서 *Equals* 연산을 지원해야 합니다. 접두사 쿼리는 특성에서 *StartsWith* 연산을 지원해야 합니다. 숫자 범위 쿼리는 특성에서 *IsBetween* 연산을 지원해야 합니다.

엔터티 데이터 중 일부는 복합 특성으로 저장되고 특성 이름에서 점('.')으로 표시됩니다. 예를 들어 작성자/소속 정보는 복합 특성으로 표시됩니다. 4개의 구성 요소가 포함됩니다. AuN, AuId, AfN, AfId 이러한 구성 요소는 단일 엔터티 특성 값을 구성하는 별도의 데이터 조각입니다.


**문자열 특성: 단일 값**(동의어에 대한 일치 항목 포함)  
Ti='indexing by latent semantic analysis'  
Composite(AA.AuN='sue dumais')

**문자열 특성: 정확한 단일 값**(정식 값만 일치)  
Ti=='indexing by latent semantic analysis'  
Composite(AA.AuN=='susan t dumais')
     
**문자열 특성: 접두사 값**   
Ti='indexing by latent seman'...  
Composite(AA.AuN='sue du'...)

**숫자 특성: 단일 값**  
Y=2010
 
**숫자 특성: 범위 값**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y=\[2010, 2012\)(왼쪽 경계 값만 포함: 2010, 2011)  
Y=\[2010, 2012\](두 경계 값을 모두 포함: 2010, 2011, 2012)
 
**숫자 특성: 접두사 값**  
Y='19'...(19로 시작하는 모든 숫자 값) 
 
**날짜 특성: 단일 값**  
D='2010-02-04'

**날짜 특성: 범위 값**  
D>'2010-02-03'  
D=['2010-02-03','2010-02-05']

**And/Or 쿼리:**  
And(Y=1985, Ti='disordered electronic systems')  
Or(Ti='disordered electronic systems', Ti='fault tolerance principles and practice')  
And(Or(Y=1985,Y=2008), Ti='disordered electronic systems')
 
**복합 쿼리:**  
복합 특성의 구성 요소를 쿼리하려면 쿼리 식에서 Composite() 함수의 복합 특성을 참조하는 부분을 묶어야 합니다. 

예를 들어 작성자 이름으로 논문을 쿼리하려면 다음 쿼리를 사용합니다.
```
Composite(AA.AuN='mike smith')
```
<br>작성자를 기준으로 논문을 쿼리하려 하는데 해당 작성자가 특정 기관 출신인 경우 다음 쿼리를 사용합니다.
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Composite() 함수는 복합 특성의 두 부분을 함께 연결합니다. 다시 말해서, 작성자 중 한 명이 "Mike Smith"이고 작성자가 Harvard 출신인 논문만 가져옵니다. 

특정 기관 출신의 (다른) 작성자와 함께 특성 작성자를 기준으로 논문을 쿼리하려면 다음 쿼리를 사용합니다.
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>이 버전에서는 Composite()가 And() 앞에 있는 작성자 및 소속에 개별적으로 적용되므로 작성자 중 한 명이 "Mike Smith"이고 작성자 중 한 명의 소속이 "Harvard"인 모든 논문을 가져옵니다. 이전 쿼리 예제와 비슷하지만, 완전히 똑같지는 않습니다.

일반적으로 다음 예제를 생각해 봅시다. C라는 복합 특성이 있고 이 복합 특성에는 A와 B라는 두 개의 구성 요소가 있습니다. 한 엔터티는 C에 대한 여러 값을 가질 수 있습니다. 이러한 엔터티로는 다음과 같은 것들이 있습니다.
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>쿼리 
```
Composite(And(C.A=1, C.B=2))
```

<br>C에 대한 값을 갖고 있으며 구성 요소 C.A는 1이고 구성 요소 C.B는 2인 엔터티만 일치합니다. E1만 이 쿼리와 일치합니다.

쿼리 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>C에 대한 값을 갖고 있으며 C.A는 1인 엔터티 그리고 C에 대한 값을 갖고 있으며 C.B가 2인 엔터티가 일치합니다. E1 및 E2 모두 이 쿼리와 일치합니다.

참고:  
- Composite() 함수 외부의 복합 특성 부분을 참조할 수 없습니다.
- 동일한 Composite() 함수 내부에 있는 두 복합 특성 부분을 참조할 수 없습니다.
- Composite() 함수 내부에 있는 복합 특성 부분을 참조할 수 없습니다.
