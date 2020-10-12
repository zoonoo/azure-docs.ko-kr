---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80272958"
---
| 데이터 형식 | 람다 식에서 허용 되는 기능 `any` | 람다 식에서 허용 되는 기능 `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | 및를 제외한 모든 항목 `search.ismatch``search.ismatchscoring` | 동일 |
| `Collection(Edm.String)` | 또는와 비교 `eq``search.in` <br/><br/> 하위 식 결합 `or` | 또는와 비교 `ne``not search.in()` <br/><br/> 하위 식 결합 `and` |
| `Collection(Edm.Boolean)` | 또는와 비교 `eq``ne` | 동일 |
| `Collection(Edm.GeographyPoint)` | `geo.distance`또는와 함께 사용 `lt``le` <br/><br/> `geo.intersects` <br/><br/> 하위 식 결합 `or` | `geo.distance`또는와 함께 사용 `gt``ge` <br/><br/> `not geo.intersects(...)` <br/><br/> 하위 식 결합 `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | ,, `eq` `ne` ,, `lt` `gt` `le` 또는를 사용 하 여 비교 `ge` <br/><br/> 를 사용 하 여 다른 하위 식과 비교 결합 `or` <br/><br/> `ne`을 사용 하는 다른 하위 식의 경우를 제외 하 고 비교 결합`and` <br/><br/> `and` `or` [Dnf (분리 Normal Form)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) 에서 and 조합을 사용 하는 식 | ,, `eq` `ne` ,, `lt` `gt` `le` 또는를 사용 하 여 비교 `ge` <br/><br/> 를 사용 하 여 다른 하위 식과 비교 결합 `and` <br/><br/> `eq`을 사용 하는 다른 하위 식의 경우를 제외 하 고 비교 결합`or` <br/><br/> `and`결합 Normal 폼에서 and 조합을 사용 하는 식 `or` [(my.cnf)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
