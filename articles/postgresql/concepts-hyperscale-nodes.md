---
title: 노드-Hyperscale (Citus)-Azure Database for PostgreSQL
description: Azure Database for PostgreSQL의 서버 그룹에서 두 가지 유형의 노드, 코디네이터 및 작업자에 대해 알아봅니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974005"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL의 노드-Hyperscale (Citus)

Citus (Hyperscale) 호스팅 형식을 사용 하면 Azure Database for PostgreSQL 서버 (노드)가 "비공유" 아키텍처에서 서로 조정 될 수 있습니다. 서버 그룹의 노드는 단일 서버에 있을 때 보다 전체적으로 많은 데이터를 저장하고 더 많은 CPU 코어를 사용합니다. 이 아키텍처에서는 서버 그룹에 더 많은 노드를 추가하여 데이터베이스를 확장할 수 있습니다.

## <a name="coordinator-and-workers"></a>코디네이터 및 작업자

각 서버 그룹에는 하나의 코디네이터 노드 및 여러 작업자가 있습니다. 응용 프로그램은 코디네이터 노드로 쿼리를 보내고, 이 노드는 관련 작업자에 릴레이하고 그 결과를 누적합니다. 응용 프로그램은 작업자와 직접 연결할 수 없습니다.

각 쿼리에 대해 코디네이터는 필요한 데이터가 단일 노드 또는 여러 노드에 있는지 여부에 따라 단일 작업자 노드로 라우팅하거나 여러 노드에 걸쳐 병렬화합니다. 코디네이터는 메타 데이터 테이블을 참조하여 어떻게 처리할지 결정합니다. 이러한 테이블은 DNS 이름 및 작업자 노드 상태, 노드 간의 데이터 분포를 추적합니다.

## <a name="next-steps"></a>다음 단계
- 노드가 [분산 데이터](concepts-hyperscale-distributed-data.md) 를 저장 하는 방법 알아보기
