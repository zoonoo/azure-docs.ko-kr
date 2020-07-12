---
title: Azure Cosmos DB 쿼리 언어의 날짜 및 시간 함수
description: Azure Cosmos DB에서 날짜 및 시간 SQL 시스템 함수를 학습 하 여 DateTime 및 timestamp 작업을 수행 합니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3666f58b12855c19dd9b8ecf5519ab772c49743
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246940"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>날짜 및 시간 함수 (Azure Cosmos DB)

날짜 및 시간 함수를 사용 하 여 Azure Cosmos DB에서 DateTime 및 timestamp 작업을 수행할 수 있습니다.

## <a name="functions-to-obtain-the-date-and-time"></a>날짜 및 시간을 가져오는 함수

다음 스칼라 함수를 사용 하면 ISO 8601 형식을 따르는 문자열 또는 값이 Unix epoch 인 숫자 타임 스탬프 (밀리초)로 현재 UTC 날짜 및 시간을 가져올 수 있습니다.

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)

## <a name="functions-to-work-with-datetime-values"></a>DateTime 값으로 작업 하는 함수

다음 함수를 사용 하 여 DateTime 값을 쉽게 조작할 수 있습니다.

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)

## <a name="next-steps"></a>다음 단계

- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
- [사용자 정의 함수](sql-query-udfs.md)
- [집계](sql-query-aggregates.md)
