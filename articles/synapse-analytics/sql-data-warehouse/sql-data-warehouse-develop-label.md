---
title: 레이블을 사용하여 쿼리계
description: 솔루션을 개발하기 위해 Synapse SQL 풀에서 레이블을 사용하여 쿼리를 계측하는 팁입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5e2cd03ae878e80139a7f7a8ba67cef15b24d571
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633502"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>레이블을 사용하여 Synapse SQL 풀에서 쿼리를 계측

이 문서에는 레이블을 사용하여 SQL 풀의 계측기 쿼리를 사용하는 솔루션을 개발하기 위한 팁이 포함되어 있습니다.

솔루션 개발을 위해 Azure SQL Data Warehouse에서 레이블을 사용하여 쿼리를 계측하기 위한 팁.

## <a name="what-are-labels"></a>레이블이란?

SQL 풀은 쿼리 레이블이라는 개념을 지원합니다. 좀더 깊이 들어가기 전에 한 가지 예를 살펴보겠습니다.

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

마지막 줄은 쿼리에 문자열 '내 쿼리 레이블' 태그를 추가합니다. 이 태그는 레이블이 DMV를 통해 쿼리할 수 있기 때문에 유용합니다.

레이블을 쿼리하면 문제 쿼리를 찾고 ELT 실행을 통해 진행률을 파악할 수 있는 메커니즘을 제공합니다.

좋은 명명 규칙은 큰 도움이 됩니다. 예를 들어 PROJECT, 프로시저, 문 또는 COMMENT로 레이블을 시작하면 소스 제어의 모든 코드 중에서 쿼리를 고유하게 식별합니다.

다음 쿼리는 동적 관리 보기를 사용하여 레이블별로 검색합니다.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 쿼리할 때 반드시 단어 레이블을 대괄호 또는 큰따옴표로 묶어야 합니다. 레이블은 예약어이므로 구분하지 않으면 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
