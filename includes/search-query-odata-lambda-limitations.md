---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80272958"
---
| 데이터 형식 | `any`가 있는 람다 식에서 허용되는 기능 | `all`이 있는 람다 식에서 허용되는 기능 |
|---|---|---|
| `Collection(Edm.ComplexType)` | `search.ismatch`와 `search.ismatchscoring`을 제외한 모든 항목 | 동일 |
| `Collection(Edm.String)` | `eq` 또는 `search.in`과 비교 <br/><br/> 하위 식과 `or` 결합 | `ne` 또는 `not search.in()`과 비교 <br/><br/> 하위 식과 `and` 결합 |
| `Collection(Edm.Boolean)` | `eq` 또는 `ne`와 비교 | 동일 |
| `Collection(Edm.GeographyPoint)` | `lt` 또는 `le`와 함께 `geo.distance` 사용 <br/><br/> `geo.intersects` <br/><br/> 하위 식과 `or` 결합 | `gt` 또는 `ge`와 함께 `geo.distance` 사용 <br/><br/> `not geo.intersects(...)` <br/><br/> 하위 식과 `and` 결합 |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | `eq`, `ne`, `lt`, `gt`, `le` 또는 `ge`를 사용하여 비교 <br/><br/> `or`을 사용하여 다른 하위 식과 비교 결합 <br/><br/> `and`를 사용하여 `ne`를 제외한 다른 하위 식과 비교 결합 <br/><br/> [DNF(Disjunctive Normal Form)](https://en.wikipedia.org/wiki/Disjunctive_normal_form)에서 `and`와 `or`의 조합을 사용하는 식 | `eq`, `ne`, `lt`, `gt`, `le` 또는 `ge`를 사용하여 비교 <br/><br/> `and`를 사용하여 다른 하위 식과 비교 결합 <br/><br/> `or`을 사용하여 `eq`를 제외한 다른 하위 식과 비교 결합 <br/><br/> [CNF(Conjunctive Normal Form)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)에서 `and`와 `or`의 조합을 사용하는 식 |
