---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272958"
---
| 데이터 형식 | 람다 식에서 허용되는 기능`any` | 람다 식에서 허용되는 기능`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | 모든 `search.ismatch` 것을 제외 하 고`search.ismatchscoring` | 동일 |
| `Collection(Edm.String)` | 비교 `eq` 또는`search.in` <br/><br/> 하위 표현식을`or` | 비교 `ne` 또는`not search.in()` <br/><br/> 하위 표현식을`and` |
| `Collection(Edm.Boolean)` | 비교 `eq` 또는`ne` | 동일 |
| `Collection(Edm.GeographyPoint)` | 사용 `geo.distance` `lt` 또는`le` <br/><br/> `geo.intersects` <br/><br/> 하위 표현식을`or` | 사용 `geo.distance` `gt` 또는`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> 하위 표현식을`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | `eq`을 `ne`사용한 비교 `lt` `gt`. `le``ge` <br/><br/> 다음을 사용하여 다른 하위 표현식과 비교를 결합합니다.`or` <br/><br/> 다음을 사용하는 `ne` 다른 하위 표현식과 비교를 결합합니다.`and` <br/><br/> `and` `or` [DNF(비정규형)의](https://en.wikipedia.org/wiki/Disjunctive_normal_form) 조합을 사용하는 식 | `eq`을 `ne`사용한 비교 `lt` `gt`. `le``ge` <br/><br/> 다음을 사용하여 다른 하위 표현식과 비교를 결합합니다.`and` <br/><br/> 다음을 사용하는 `eq` 다른 하위 표현식과 비교를 결합합니다.`or` <br/><br/> `and` `or` [CNF(결막법형)의](https://en.wikipedia.org/wiki/Conjunctive_normal_form) 조합을 사용한 식 |
