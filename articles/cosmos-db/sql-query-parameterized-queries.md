---
title: Azure 코스모스 DB의 매개 변수화된 쿼리
description: SQL 매개 변수화된 쿼리가 사용자 입력을 강력하게 처리하고 이스케이프처리하는 방법을 알아보고 SQL 주입을 통해 실수로 데이터가 노출되는 것을 방지하는 방법을 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870822"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure 코스모스 DB의 매개 변수화된 쿼리

Cosmos DB는 익숙한 @ 표기어로 표현된 매개 변수로 쿼리를 지원합니다. 매개 변수화된 SQL은 사용자 입력을 강력하게 처리하고 이스케이프처리하여 SQL 주입을 통해 실수로 데이터가 노출되는 것을 방지합니다.

## <a name="examples"></a>예

예를 들어 매개 변수를 `lastName` 가져와서 `address.state` 매개 변수로 작성하는 쿼리를 `lastName` 작성하고 `address.state` 사용자 입력에 따라 다양한 값에 대해 쿼리를 실행할 수 있습니다.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

그런 다음 다음과 같이 매개 변수화된 JSON 쿼리로 Cosmos DB에 이 요청을 보낼 수 있습니다.

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

다음 예제는 매개 변수화된 쿼리를 사용 하 고 TOP 인수를 설정 합니다. 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

매개 변수 값은 문자열, 숫자, 부울, null, 배열 또는 중첩 된 JSON과 같은 유효한 JSON일 수 있습니다. Cosmos DB는 스키마가 없으므로 매개 변수는 모든 형식에 대해 유효성이 검사되지 않습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [모델 문서 데이터](modeling-data.md)
