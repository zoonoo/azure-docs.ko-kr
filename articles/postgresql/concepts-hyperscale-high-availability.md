---
title: Azure Database for PostgreSQL-Hyperscale (Citus)의 고가용성
description: 고가용성 및 재해 복구 개념
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 616b5bff735f52d137c12c58ac6023c38a2d4044
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514746"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale (Citus)의 고가용성

HA (고가용성)는 서버 그룹에 있는 모든 노드의 대기 복제본을 유지 관리 하 여 데이터베이스 가동 중지 시간을 방지 합니다. 노드가 다운 되 면 Hyperscale은 실패 한 노드에서 대기로 들어오는 연결을 전환 합니다. 장애 조치 (Failover)는 몇 분 내에 발생 하며 승격 된 노드는 항상 PostgreSQL 동기 스트리밍 복제를 통해 새 데이터를 포함 합니다.

코디네이터 노드에서 HA를 활용 하려면 데이터베이스 응용 프로그램에서 삭제 된 연결과 실패 한 트랜잭션을 검색 하 고 다시 시도 해야 합니다. 새로 승격 된 코디네이터는 동일한 연결 문자열을 사용 하 여 액세스할 수 있습니다.

복구는 검색, 장애 조치 (failover), 전체 복구의 세 단계로 나눌 수 있습니다.  Hyperscale은 모든 노드에서 정기적으로 상태 검사를 실행 하 고, 4 개의 실패 한 검사 후 노드가 다운 되었음을 확인 합니다. 그런 다음 하이퍼 확장은 대기를 주 노드 상태 (장애 조치)로 승격 하 고 새 대기를 프로 비전 합니다.
스트리밍 복제가 시작 되어 새 노드를 최신 상태로 전환 합니다.  모든 데이터가 복제 된 후 노드가 전체 복구에 도달 했습니다.

### <a name="next-steps"></a>다음 단계

- 하이퍼 확장 서버 그룹에서 고가용성을 [사용 하도록 설정](howto-hyperscale-high-availability.md) 하는 방법에 대해 알아봅니다.
