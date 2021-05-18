---
title: 고가용성 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 고가용성 및 재해 복구 개념
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 406b4f2ada665d65ee0452579e24744d1f7cfc63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91314856"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 고가용성

HA(고가용성)는 서버 그룹에 있는 모든 노드의 대기 복제본을 유지 관리하여 데이터베이스 가동 중지 시간을 방지합니다. 노드가 다운되면 하이퍼스케일(Citus)은 오류가 발생한 노드에서 들어오는 연결을 대기 복제본으로 전환합니다. 몇 분 이내에 장애 조치(failover)가 발생하며 승격된 노드는 PostgreSQL 동기 스트리밍 복제를 통해 항상 새 데이터를 포함합니다.

코디네이터 노드에서 HA를 활용하려면 데이터베이스 애플리케이션에서 삭제된 연결과 오류가 발생한 트랜잭션을 검색하고 다시 시도해야 합니다. 동일한 연결 문자열을 사용하여 새로 승격된 코디네이터에 액세스할 수 있습니다.

복구는 검색, 장애 조치(failover), 전체 복구의 세 단계로 나눌 수 있습니다.  하이퍼스케일(Citus)은 모든 노드에서 정기적으로 상태 검사를 실행하고 검사에 4회 실패하면 노드가 다운된 것으로 결정합니다. 그런 다음, 하이퍼스케일(Citus)은 대기 노드를 주 노드 상태로 승격(장애 조치(failover))하고 새 대기 노드를 프로비저닝합니다.
스트리밍 복제가 시작되어 새 노드를 최신 상태로 만듭니다.  모든 데이터가 복제되면 노드가 전체 복구된 것입니다.

### <a name="next-steps"></a>다음 단계

- 하이퍼스케일(Citus) 서버 그룹에서 [고가용성을 사용하도록 설정](howto-hyperscale-high-availability.md)하는 방법을 알아봅니다.
