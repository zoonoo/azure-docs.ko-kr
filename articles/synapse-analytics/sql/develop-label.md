---
title: Synapse SQL의 쿼리 레이블 사용
description: 이 문서에는 Synapse SQL에서 쿼리 레이블을 사용하기 위한 필수 팁이 포함되어 있습니다.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 59fa68d12f1d8be598810399fc5623c2af983979
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462253"
---
# <a name="use-query-labels-in-synapse-sql"></a>Synapse SQL의 쿼리 레이블 사용

이 문서에는 Synapse SQL에서 쿼리 레이블을 사용하기 위한 필수 팁이 포함되어 있습니다.

> [!NOTE]
> 서버리스 SQL 풀은 레이블링 쿼리를 지원하지 않습니다.

## <a name="what-are-query-labels"></a>쿼리 레이블이란?

전용 SQL 풀은 쿼리 레이블이라는 개념을 지원합니다. 좀더 깊이 들어가기 전에 한 가지 예를 살펴보겠습니다.

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

마지막 줄은 쿼리에 문자열 '내 쿼리 레이블' 태그를 추가합니다. 레이블은 DMV를 통해 쿼리가 가능하므로 이 태그는 유용합니다. 레이블을 쿼리하면 문제 쿼리를 찾고 ELT 실행을 통해 진행률을 파악할 수 있는 메커니즘을 제공합니다.

가장 유용한 것은 좋은 명명 규칙입니다. 예를 들어 PROJECT, PROCEDURE, STATEMENT 또는 COMMENT 문으로 레이블을 시작하면 소스 제어의 모든 코드 중에서 쿼리를 고유하게 식별할 수 있습니다.

다음 쿼리는 동적 관리 뷰를 사용하여 레이블별로 검색을 수행합니다.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 쿼리할 때 반드시 단어 레이블을 대괄호 또는 큰따옴표로 묶어야 합니다. 레이블은 예약어이므로 구분하지 않으면 오류가 발생합니다. 
> 
> 

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요](develop-overview.md)를 참조하세요.


