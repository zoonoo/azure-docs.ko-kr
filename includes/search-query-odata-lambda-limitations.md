---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80272958"
---
| 데이터 형식 | 람다 식에서 허용 되는 기능`any` | 람다 식에서 허용 되는 기능`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | 및를 `search.ismatch` 제외한 모든 항목`search.ismatchscoring` | 동일 |
| `Collection(Edm.String)` | 또는와 `eq` 비교`search.in` <br/><br/> 하위 식 결합`or` | 또는와 `ne` 비교`not search.in()` <br/><br/> 하위 식 결합`and` |
| `Collection(Edm.Boolean)` | 또는와 `eq` 비교`ne` | 동일 |
| `Collection(Edm.GeographyPoint)` | 또는 `geo.distance` 와 `lt` 함께 사용`le` <br/><br/> `geo.intersects` <br/><br/> 하위 식 결합`or` | 또는 `geo.distance` 와 `gt` 함께 사용`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> 하위 식 결합`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | `eq`, `ne`, `lt`, `gt`, 또는를 사용 하 여 비교 `le``ge` <br/><br/> 를 사용 하 여 다른 하위 식과 비교 결합`or` <br/><br/> 을 사용 하 `ne` 는 다른 하위 식의 경우를 제외 하 고 비교 결합`and` <br/><br/> `and` [Dnf (분리 Normal Form)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) 에서 and `or` 조합을 사용 하는 식 | `eq`, `ne`, `lt`, `gt`, 또는를 사용 하 여 비교 `le``ge` <br/><br/> 를 사용 하 여 다른 하위 식과 비교 결합`and` <br/><br/> 을 사용 하 `eq` 는 다른 하위 식의 경우를 제외 하 고 비교 결합`or` <br/><br/> 결합 Normal 폼에서 `and` and `or` 조합을 사용 하는 식 [(my.cnf)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
