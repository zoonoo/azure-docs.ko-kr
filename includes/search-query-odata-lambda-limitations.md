---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079640"
---
| 데이터 형식 | 람다 식에서 허용 하는 기능 `any` | 람다 식에서 허용 하는 기능 `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | 제외한 모든 것 `search.ismatch` 및 `search.ismatchscoring` | 같은 데이터 집합 |
| `Collection(Edm.String)` | 와 비교할 `eq` 또는 `search.in` <br/><br/> 사용 하 여 하위 식을 결합 `or` | 와 비교할 `ne` 또는 `not search.in()` <br/><br/> 사용 하 여 하위 식을 결합 `and` |
| `Collection(Edm.Boolean)` | 와 비교할 `eq` 또는 `ne` | 같은 데이터 집합 |
| `Collection(Edm.GeographyPoint)` | 사용 하 여 `geo.distance` 사용 하 여 `lt` 또는 `le` <br/><br/> `geo.intersects` <br/><br/> 사용 하 여 하위 식을 결합 `or` | 사용 하 여 `geo.distance` 사용 하 여 `gt` 또는 `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> 사용 하 여 하위 식을 결합 `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | 사용한 비교 `eq`, `ne`를 `lt`를 `gt`, `le`, 또는 `ge` <br/><br/> 비교를 사용 하 여 다른 하위 식을 결합 `or` <br/><br/> 제외 하 고 비교를 결합 `ne` 을 사용 하 여 다른 하위 식 `and` <br/><br/> 조합을 사용 하 여 식을 `and` 하 고 `or` 에서 [Disjunctive Normal Form (로)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | 사용한 비교 `eq`, `ne`를 `lt`를 `gt`, `le`, 또는 `ge` <br/><br/> 비교를 사용 하 여 다른 하위 식을 결합 `and` <br/><br/> 제외 하 고 비교를 결합 `eq` 을 사용 하 여 다른 하위 식 `or` <br/><br/> 조합을 사용 하 여 식을 `and` 하 고 `or` 에서 [으로만 정규 양식 (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
