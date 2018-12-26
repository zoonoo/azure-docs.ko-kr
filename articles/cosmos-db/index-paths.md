---
title: Azure Cosmos DB의 인덱스 경로 사용
description: Azure Cosmos DB의 인덱스 경로 개요
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: 0515397fb9ab0f05b4c763a2b05e9d986960bd91
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628836"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Azure Cosmos DB의 인덱스 경로

Azure Cosmos DB의 인덱스 경로를 사용하면 인덱싱에서 특정 경로를 포함할지 여부를 선택할 수 있습니다. 특정 경로를 선택하면 쿼리 패턴이 알려진 시나리오의 경우 쓰기 성능이 향상되고 인덱스 스토리지가 줄 수 있습니다. 인덱스 경로는 루트(`/`) 와일드카드 연산자로 시작하고 일반적으로 `?` 와일드카드 연산자로 종료됩니다. 이 패턴은 접두사로 사용 가능한 값이 여러 개 있음을 나타냅니다. 예를 들어 `SELECT * FROM Families F WHERE F.familyName = "Andersen"` 쿼리를 사용하려면 컨테이너의 인덱스 정책에 `/familyName/?`의 인덱스 경로가 포함돼야 합니다.

또한 인덱스 경로에 `*`와일드카드 연산자를 사용하여 접두사 아래에 재귀적으로 경로에 대한 동작을 지정할 수 있습니다. 예를 들어 `/payload/*`를 사용하여 페이로드 속성의 모든 항목을 인덱싱에서 제외할 수 있습니다.

## <a name="common-patterns-for-index-paths"></a>인덱스 경로의 일반적인 패턴

인덱스 경로를 지정하는 일반적인 패턴은 다음과 같습니다.

| **Path** | **설명/사용 사례** |
| ---------- | ------- |
| /   | 컬렉션의 기본 경로입니다. 재귀적이며 전체 문서 트리에 적용됩니다.|
| /prop/?  | 인덱스 경로는 다음과 같은 쿼리를 처리하는 데 필요합니다(각각 해시, 범위 유형이 포함되어 있음).<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop  |
| /prop/*  | 지정된 레이블 아래의 모든 경로의 인덱스 경로입니다. 다음 쿼리로 작동<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop |
| /props/[]/?  | ["a", "b", "c"]와 같은 스칼라의 배열에 대한 반복 및 JOIN 쿼리를 제공하는 데 필요한 인덱스 경로입니다.<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5  |
| /props/[]/subprop/? | [{subprop: "a"}, {subprop: "b"}]와 같은 개체의 배열에 대한 반복 및 JOIN 쿼리를 제공하는 데 필요한 인덱스 경로입니다.<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value" |
| /prop/subprop/? | 인덱스 경로는 다음과 같은 쿼리를 처리하는 데 필요합니다(각각 해시 또는 범위 유형이 포함되어 있음).<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5  |

사용자 지정 인덱스 경로를 설정하는 경우 `/*` 특수 경로로 지정된 전체 항목에 대한 기본 인덱싱 규칙을 지정해야 합니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서는 Azure Cosmos DB의 인덱싱에 대해 자세히 알아봅니다.

- [인덱싱 개요](index-overview.md)
- [Azure Cosmos DB의 인덱싱 정책](indexing-policies.md)
- [Azure Cosmos DB의 인덱스 유형](index-types.md)
