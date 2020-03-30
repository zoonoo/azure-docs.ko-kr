---
title: 고가용성 - 하이퍼스케일(Citus) - PostgreSQL용 Azure 데이터베이스
description: 고가용성 및 재해 복구 개념
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975536"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL용 Azure 데이터베이스의 고가용성 – 하이퍼스케일(시터스)

HA(고가용성)는 서버 그룹의 모든 노드의 대기 복제본을 유지 관리하여 데이터베이스 가동 중지 시간을 방지합니다. 노드가 다운되면 하이퍼스케일은 실패한 노드에서 대기로 들어오는 연결을 전환합니다. 장애 조치(Failover)는 몇 분 내에 발생하며 승격된 노드에는 항상 PostgreSQL 동기 스트리밍 복제를 통해 최신 데이터가 있습니다.

코디네이터 노드에서 HA를 활용하려면 데이터베이스 응용 프로그램이 삭제된 연결 및 실패한 트랜잭션을 검색하고 다시 시도해야 합니다. 새로 승격된 코디네이터는 동일한 연결 문자열로 액세스할 수 있습니다.

복구는 검색, 장애 조치 및 전체 복구의 세 단계로 나눌 수 있습니다.  하이퍼스케일은 모든 노드에서 정기적인 상태 확인을 실행하고 4번의 실패한 검사 후에 노드가 다운되었는지 확인합니다. 그런 다음 하이퍼스케일은 대기를 기본 노드 상태(장애 조치)로 승격하고 새 대기 를 프로비전합니다.
스트리밍 복제가 시작되어 새 노드를 최신 상태로 유지합니다.  모든 데이터가 복제되면 노드가 전체 복구에 도달했습니다.

### <a name="next-steps"></a>다음 단계

- 하이퍼스케일 서버 그룹에서 [고가용성을 활성화하는](howto-hyperscale-high-availability.md) 방법을 알아봅니다.
