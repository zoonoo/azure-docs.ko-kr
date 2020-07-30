---
title: 노드-Hyperscale (Citus)-Azure Database for PostgreSQL
description: Azure Database for PostgreSQL의 서버 그룹에서 두 가지 유형의 노드, 코디네이터 및 작업자에 대해 알아봅니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382800"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL의 노드-Hyperscale (Citus)

Citus (Hyperscale) 호스팅 형식을 사용 하면 Azure Database for PostgreSQL 서버 (노드)가 "비공유" 아키텍처에서 서로 조정 될 수 있습니다. 서버 그룹의 노드는 단일 서버에서 사용할 수 있는 것 보다 더 많은 데이터를 포함 하 고 더 많은 CPU 코어를 사용 합니다. 아키텍처를 사용 하면 서버 그룹에 노드를 더 추가 하 여 데이터베이스의 크기를 조정할 수도 있습니다.

## <a name="coordinator-and-workers"></a>코디네이터 및 작업자

모든 서버 그룹에는 코디네이터 노드와 여러 작업자가 있습니다. 응용 프로그램은 해당 쿼리를 코디네이터 노드에 전송 하 여 관련 작업자에 게 릴레이 하 고 결과를 누적 합니다. 응용 프로그램은 작업자에 직접 연결할 수 없습니다.

Citus (hyperscale)를 사용 하면 데이터베이스 관리자가 테이블을 *배포* 하 여 다른 작업자 노드에 다른 행을 저장할 수 있습니다. 분산 테이블은 성능의 규모를 조정 하는 핵심입니다. 테이블 배포에 실패 하면 해당 테이블은 완전히 코디네이터 노드에 그대로 남아 있으므로 크로스 시스템 병렬 처리를 사용할 수 없습니다.

코디네이터는 분산 된 테이블에 대 한 각 쿼리에 대해이를 단일 작업자 노드로 라우트 하거나, 필요한 데이터가 단일 노드에 있는지 또는 여러 데이터가 아니라에 따라 몇 가지에 걸쳐 있습니다. 코디네이터는 컨설팅 메타 데이터 테이블에서 수행할 작업을 결정 합니다. 이러한 테이블은 작업자 노드의 DNS 이름 및 상태와 노드 간 데이터 배포를 추적 합니다.

## <a name="next-steps"></a>다음 단계
- [분산 테이블](concepts-hyperscale-distributed-data.md) 에 대 한 자세한 정보
