---
title: 알려진 문제 및 제한 사항-Azure Database for PostgreSQL-단일 서버 및 유연한 서버 (미리 보기)
description: 고객이 알아야 하는 알려진 문제를 나열 합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106430"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL-알려진 문제 및 제한 사항

이 페이지에서는 응용 프로그램에 영향을 줄 수 있는 Azure Database for PostgreSQL의 알려진 문제 목록을 제공 합니다. 또한 문제 해결을 위해 완화 및 권장 사항을 나열 합니다.

## <a name="intelligent-performance---query-store"></a>지능형 성능-쿼리 저장소

Azure Database for PostgreSQL-단일 서버에 적용 됩니다.

| 적용 가능 | 원인 | 수정|
| ----- | ------ | ---- | 
| PostgreSQL 9.6, 10, 11 | 서버 매개 변수를 설정 하면 `pg_qs.replace_parameter_placeholders` 일부 드문 시나리오에서 서버가 종료 될 수 있습니다. | Azure Portal의 서버 매개 변수 섹션에서 매개 변수 값을로 설정 하 `pg_qs.replace_parameter_placeholders` `OFF` 고 저장 합니다.   | 

## <a name="server-parameters"></a>서버 매개 변수

Azure Database for PostgreSQL-단일 서버 및 유연한 서버에 적용 가능

| 적용 가능 | 원인 | 수정| 
| ----- | ------ | ---- | 
| PostgreSQL 9.6, 10, 11 | 서버 매개 변수를 `max_locks_per_transaction` [권장](https://www.postgresql.org/docs/11/kernel-resources.html) 값 보다 높은 값으로 변경 하면 서버가 다시 시작 된 후에도 서버가 나올 수 없습니다. | 이 값을 기본값 (32 또는 64)으로 두거나 PostgreSQL [설명서](https://www.postgresql.org/docs/11/kernel-resources.html)당 적절 한 값으로 변경 합니다. <br> <br> 서비스 쪽에서 SKU에 따라 높은 값을 제한 하기 위해이 작업을 수행 하 고 있습니다.  | 

## <a name="next-steps"></a>다음 단계
- 쿼리 저장소 [모범 사례](./concepts-query-store-best-practices.md) 를 참조 하세요.
