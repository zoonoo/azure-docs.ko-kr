---
title: 노드 - 하이퍼스케일 (시터스) - PostgreSQL용 Azure 데이터베이스
description: PostgreSQL용 Azure 데이터베이스의 서버 그룹에서 두 가지 유형의 노드, 코디네이터 및 작업자에 대해 알아봅니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974005"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL용 Azure 데이터베이스의 노드 – 하이퍼스케일(시터스)

하이퍼스케일(Citus) 호스팅 유형을 사용하면 PostgreSQL 서버용 Azure 데이터베이스(노드라고 함)를 "공유된 없음" 아키텍처에서 서로 조정할 수 있습니다. 서버 그룹의 노드는 단일 서버에서 가능한 것보다 더 많은 데이터를 보유하고 더 많은 CPU 코어를 사용합니다. 또한 아키텍처를 사용하면 서버 그룹에 노드를 더 추가하여 데이터베이스의 크기를 조정할 수 있습니다.

## <a name="coordinator-and-workers"></a>코디네이터 및 근로자

모든 서버 그룹에는 코디네이터 노드와 여러 작업자가 있습니다. 응용 프로그램은 쿼리를 코디네이터 노드로 보내 관련 작업자에게 전달하고 결과를 누적합니다. 응용 프로그램은 작업자에게 직접 연결할 수 없습니다.

각 쿼리에 대해 코디네이터는 단일 작업자 노드로 라우팅하거나 필요한 데이터가 단일 노드 또는 여러 노드에 있는지 여부에 따라 여러 쿼리에 병렬화합니다. 코디네이터는 메타데이터 테이블을 참조하여 수행할 작업을 결정합니다. 이러한 테이블은 작업자 노드의 DNS 이름 및 상태 와 노드 간 데이터 분포를 추적합니다.

## <a name="next-steps"></a>다음 단계
- 노드가 분산 [데이터를](concepts-hyperscale-distributed-data.md) 저장하는 방법 알아보기
