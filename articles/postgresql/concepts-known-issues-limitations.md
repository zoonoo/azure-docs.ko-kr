---
title: 알려진 문제 및 제한 사항 - Azure Database for PostgreSQL - 단일 서버 및 유연한 서버(미리 보기)
description: 고객이 알아야 하는 알려진 문제를 나열합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100106430"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL - 알려진 문제 및 제한 사항

이 페이지에서는 애플리케이션에 영향을 줄 수 있는 Azure Database for PostgreSQL의 알려진 문제 목록을 제공합니다. 또한 문제 해결을 위한 완화 및 권장 사항을 나열합니다.

## <a name="intelligent-performance---query-store"></a>Intelligent Performance - 쿼리 저장소

Azure Database for PostgreSQL - 단일 서버에 해당

| 적용 가능 | 원인 | 수정|
| ----- | ------ | ---- | 
| PostgreSQL 9.6, 10, 11 | 서버 매개 변수 `pg_qs.replace_parameter_placeholders`를 켜면 일부 드문 시나리오에서 서버가 종료될 수 있습니다. | Azure Portal의 서버 매개 변수 섹션에서 `pg_qs.replace_parameter_placeholders` 매개 변수 값을 `OFF`로 설정하고 저장합니다.   | 

## <a name="server-parameters"></a>서버 매개 변수

Azure Database for PostgreSQL - 단일 서버 및 유연한 서버에 해당

| 적용 가능 | 원인 | 수정| 
| ----- | ------ | ---- | 
| PostgreSQL 9.6, 10, 11 | 서버 매개 변수 `max_locks_per_transaction`를 [권장](https://www.postgresql.org/docs/11/kernel-resources.html) 값보다 높은 값으로 변경하면 서버가 다시 시작 후 시작되지 않을 수 있습니다. | 기본값(32 또는 64)으로 두거나 PostgreSQL [설명서](https://www.postgresql.org/docs/11/kernel-resources.html)에 따른 적절한 값으로 변경합니다. <br> <br> 서비스 쪽에서 SKU를 기준으로 높은 값을 제한하는 방법을 마련하고 있습니다.  | 

## <a name="next-steps"></a>다음 단계
- 쿼리 저장소 [모범 사례](./concepts-query-store-best-practices.md)를 참조하세요.
