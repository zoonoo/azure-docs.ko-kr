---
title: Azure Database for PostgreSQL 서버 쿼리 통계 컬렉션에서 쿼리 통계 컬렉션 최적화
description: 이 문서에서는 Azure Database for PostgreSQL 서버에서 쿼리 통계 컬렉션을 최적화하는 방법을 설명합니다.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 076442d85d7f628504cca95c36f3e99f4d0c5117
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966690"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL 서버에서 쿼리 통계 컬렉션 최적화 
이 문서에서는 Azure Database for PostgreSQL 서버에서 쿼리 통계 컬렉션을 최적화하는 방법을 설명합니다.

## <a name="using-pgstatsstatements"></a>pg_stats_statements 사용
**Pg_stat_statements**는 Azure Database for PostgreSQL에서 기본적으로 사용하도록 설정된 PostgreSQL 확장입니다. 이 확장은 서버에서 실행되는 모든 SQL 문의 실행 통계를 추적하는 수단을 제공합니다. 그러나 이 모듈은 모든 쿼리 실행에 연결되며 적지 않은 성능 비용이 수반됩니다. **pg_stat_statements**을 사용하면 디스크 파일에 쿼리 텍스트 쓰기를 해야 합니다.

긴 쿼리 텍스트를 사용한 고유한 쿼리가 있거나 **pg_stat_statements**를 적극적으로 모니터링하지 않는 고객의 경우 최상의 성능을 위해서는 `pg_stat_statements.track = NONE`를 설정하여 **pg_stat_statements**를 사용하지 않도록 설정하는 것이 좋습니다.

일부 고객 워크 로드에서 **pg_stat_statements**를 사용하지 않도록 설정하여 최대 50퍼센트의 성능 향상을 기록했습니다. 그러나 pg_stat_statements를 사용하지 않도록 설정하여 생기하는 단점은 성능 문제를 해결할 수 없다는 것입니다.

`pg_stat_statements.track = NONE`을 설정하려면

- Azure Portal에서 [PostgreSQL 리소스 관리 페이지로 이동하고 서버 매개 변수 블레이드를 선택합니다](howto-configure-server-parameters-using-portal.md).

![PostgreSQL 서버 매개 변수 블레이드](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- [Azure CLI](howto-configure-server-parameters-using-cli.md)를 사용하여 az postgres server configuration set`--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="using-query-store"></a>쿼리 저장소 사용 
Azure Database for PostgreSQL에서 [쿼리 저장소](concepts-query-store.md) 기능은 쿼리 통계를 추적하기에 성능이 더 좋은 방법을 제공하고 *pg_stats_statements*의 사용에 대한 대안으로 권장됩니다. 

## <a name="next-steps"></a>다음 단계
[Azure Portal](howto-configure-server-parameters-using-portal.md)에서 `pg_stat_statements.track = NONE`을 설정하거나 [Azure CLI](howto-configure-server-parameters-using-cli.md)을 사용하는 것이 좋습니다.

자세한 내용은 [쿼리 저장소 사용 시나리오](concepts-query-store-scenarios.md) 및 [쿼리 저장소 모범 사례](concepts-query-store-best-practices.md)를 참조하세요. 
