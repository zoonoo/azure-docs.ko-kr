---
title: 데이터를 분산 하 고 Arc를 사용 하는 PostgreSQL Hyperscale 서버 그룹을 확장 하는 개념
titleSuffix: Azure Arc enabled data services
description: Arc를 사용 하는 데이터 배포에 대 한 개념 PostgreSQL Hyperscale 서버 그룹
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939784"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>Arc를 사용 하는 데이터 배포에 대 한 개념 PostgreSQL Hyperscale 서버 그룹

이 문서에서는 Azure Arc 사용 PostgreSQL Hyperscale을 최대한 활용 하는 데 중요 한 주요 개념을 설명 합니다.
아래 링크 된 문서는 Azure Database for PostgreSQL Hyperscale (Citus)에 대해 설명 된 개념을 가리킵니다. 동일한 개념 및 관점이 적용 되도록 Azure Arc PostgreSQL Hyperscale을 사용 하는 것과 동일한 기술입니다.

**차이점은 무엇 인가요?**
- _Azure Database for PostgreSQL Hyperscale (Citus)_

Azure (PaaS)에서 Database as a Service로 제공 되는 postgres 데이터베이스 엔진의 대규모 폼 요소입니다. 이는 Citus 확장을 통해 하이퍼 크기 조정 환경을 가능 하 게 합니다. 이 폼 팩터에서 서비스는 Microsoft 데이터 센터에서 실행 되며 Microsoft에서 운영 됩니다.

- _Azure Arc 사용 PostgreSQL Hyperscale_

Azure Arc enabled 데이터 서비스에서 제공 되는 postgres 데이터베이스 엔진의 대규모 폼 요소입니다. 이 폼 팩터에서 고객은 시스템을 호스트 하 고 운영 하는 인프라를 제공 합니다.

Azure Arc 사용 PostgreSQL Hyperscale의 주요 개념은 아래에 요약 되어 있습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>노드 및 테이블
Azure Arc 사용 설정 Postgres의 가장 큰 혜택을 제공 하기 위해 다음 개념에 대해 알고 있어야 합니다.
- Azure Arc의 특수 Postgres 노드 사용 PostgreSQL Hyperscale: 코디네이터 및 작업자
- 테이블 형식: 분산 테이블, 참조 테이블 및 로컬 테이블
- 분할

[Azure Database for PostgreSQL – Hyperscale (Citus)의 노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)에서 자세한 정보를 참조 하세요. 

## <a name="determine-the-application-type"></a>응용 프로그램 유형 결정
빌드 중인 응용 프로그램의 유형을 명확 하 게 식별 하는 것이 중요 합니다. 그 이유는 Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹에서 효율적인 쿼리를 실행 하려면 테이블이 서버 전체에 제대로 분산 되어야 합니다. 권장 배포는 응용 프로그램의 유형과 쿼리 패턴에 따라 달라 집니다. Azure Arc에서 잘 작동 하는 두 종류의 응용 프로그램은 Postgres Hyperscale을 사용 합니다.
- 다중 테 넌 트 응용 프로그램
- 실시간 응용 프로그램

데이터 모델링의 첫 번째 단계는 응용 프로그램과 더 밀접 하 게 비슷한 요소를 식별 하는 것입니다.

[응용 프로그램 유형 결정](../../postgresql/concepts-hyperscale-app-type.md)에서 세부 정보를 참조 하세요.


## <a name="choose-a-distribution-column"></a>배포 열 선택
분산 열을 선택 해야 하는 이유

가장 중요 한 모델링 결정 중 하나입니다. Azure Arc enabled PostgreSQL Hyperscale은 행의 분포 열 값을 기준으로 분할에 행을 저장 합니다. 적절 한 선택은 같은 실제 노드에서 관련 데이터를 함께 그룹화 하 여 쿼리를 빠르게 수행 하 고 모든 SQL 기능에 대 한 지원을 추가 합니다. 잘못 된 항목을 선택 하면 시스템이 느리게 실행 되 고 노드에서 모든 SQL 기능을 지원 하지 않습니다. 이 문서에서는 가장 일반적인 두 가지 대규모 시나리오에 대 한 배포 열 팁을 제공 합니다.

[배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)에서 세부 정보를 참조 하세요.


## <a name="table-colocation"></a>테이블 공동 배치

공동 배치는 동일한 노드에 관련 정보를 함께 저장 하는 것입니다. 모든 필요한 데이터를 네트워크 트래픽 없이 사용할 수 있게 되 면 쿼리가 신속 하 게 진행 될 수 있습니다. 서로 다른 노드에 있는 관련 데이터를 공동 배치 하면 쿼리가 각 노드에서 병렬로 효율적으로 실행 될 수 있습니다.

[표 공동 위치](../../postgresql/concepts-hyperscale-colocation.md)에서 세부 정보를 참조 하세요.


## <a name="next-steps"></a>다음 단계
- [Azure Arc 사용 PostgreSQL Hyperscale 만들기에 대 한 자세한 정보](create-postgresql-hyperscale-server-group.md)
- [Arc 데이터 컨트롤러에서 만든 Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹 확장에 대 한 자세한 정보](scale-out-postgresql-hyperscale-server-group.md)
- [Azure Arc 사용 Data Services에 대해 읽기](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Azure Arc에 대해 읽기](https://aka.ms/azurearc)

