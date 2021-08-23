---
title: 읽기 복제본 - Azure Database for PostgreSQL - 하이퍼스케일(Citus)
description: 이 문서에서는 Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 읽기 복제본 기능을 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 150d6b02ff2c11d2489c46b4dc2dd44fd2276b75
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566777"
---
# <a name="read-replicas-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL의 읽기 복제본 – 하이퍼스케일(Citus)

읽기 복제본 기능을 사용하면 하이퍼스케일(Citus) 서버 그룹에서 읽기 전용 서버 그룹으로 데이터를 복제할 수 있습니다. 복제본은 PostgreSQL의 물리적 복제 기술을 이용하여 **비동기식으로** 업데이트됩니다. 주 서버에서 복제본을 무제한으로 복제할 수 있습니다.

복제본은 일반 하이퍼스케일(Citus) 서버 그룹과 유사하게 관리되는 새 서버 그룹입니다. 읽기 복제본의 경우, vCore 및 스토리지에 프로비저닝된 컴퓨팅에 대한 비용이 GB/월 단위로 청구됩니다.

[복제본을 만들고 관리](howto-hyperscale-read-replicas-portal.md)하는 방법을 알아봅니다.

## <a name="when-to-use-a-read-replica"></a>읽기 복제본을 사용하는 경우

읽기 복제본 기능은 읽기 작업이 많은 워크로드의 성능과 규모를 향상시키는 데 도움이 됩니다. 읽기 워크로드를 복제본과 격리할 수 있고 쓰기 워크로드를 주 서버로 보낼 수 있습니다.

일반적인 시나리오는 BI와 분석 워크로드가 보고를 위한 데이터 원본으로 읽기 복제본을 사용하도록 합니다.

복제본은 읽기 전용이기 때문에 주 서버에 대한 쓰기 용량 부담을 직접적으로 줄이지는 못합니다.

### <a name="considerations"></a>고려 사항

이 기능은 복제 지연이 허용되는 시나리오를 위한 것이며 쿼리를 오프로딩하기 위한 것입니다. 복제본 데이터가 최신 상태로 유지되는 동기 복제 시나리오를 위한 것은 아닙니다. 주 서버와 복제본 간에는 측정 가능한 지연이 발생합니다. 지연 시간은 주 서버와 복제본 간의 대기 시간 및 워크로드에 따라 몇 분 또는 몇 시간까지도 걸릴 수 있습니다.  복제본의 데이터는 결과적으로 주 서버의 데이터와 일치하게 됩니다. 이러한 지연 시간을 수용할 수 있는 워크로드에 이 기능을 사용합니다. 

## <a name="create-a-replica"></a>복제본 만들기

복제본 만들기 워크플로를 시작할 때 비어 있는 하이퍼스케일(Citus) 서버 그룹이 만들어집니다. 새 그룹은 주 서버 그룹에 있던 데이터로 채워집니다. 생성 시간은 주 서버의 데이터 양과 지난 주 전체 백업 이후의 시간에 따라 달라집니다. 시간은 몇 분에서 몇 시간까지 걸릴 수 있습니다.

읽기 복제본 기능은 PostgreSQL의 물리적 복제(논리 복제 아님)를 사용합니다. 기본 모드는 복제 슬롯을 사용하는 스트리밍 복제입니다.
필요한 경우 따라잡기 위해 로그 전달이 사용됩니다.

[Azure Portal에서 읽기 복제본을 만드는 방법](howto-hyperscale-read-replicas-portal.md)을 알아봅니다.

## <a name="connect-to-a-replica"></a>복제본에 연결

복제본을 만들 때 주 서버 그룹에서 방화벽 규칙을 상속하지 않습니다. 이러한 규칙은 복제본에 대해 별도로 설정해야 합니다.

복제본은 주 서버 그룹에서 관리자("citus") 계정을 상속합니다.
사용자 계정은 모두 읽기 복제본으로 복제됩니다. 주 서버에서 사용 가능한 사용자 계정을 사용해야만 읽기 복제본에 연결할 수 있습니다.

일반 하이퍼스케일(Citus) 서버 그룹에서처럼 해당 호스트 이름 및 유효한 사용자 계정을 이용하여 복제본의 코디네이터 노드에 연결할 수 있습니다.
관리 사용자 이름이 **citus** 인 **my replica** 라는 이름의 서버에 대해서는 다음과 같이 psql을 사용하여 복제본의 코디네이터 노드에 연결할 수 있습니다.

```bash
psql -h c.myreplica.postgres.database.azure.com -U citus@myreplica -d postgres
```

프롬프트가 표시되면 사용자 계정의 암호를 입력합니다.

## <a name="considerations"></a>고려 사항

이 섹션에서는 읽기 복제본 기능의 고려 사항에 대한 요약이 제공됩니다.

### <a name="new-replicas"></a>새 복제본

읽기 복제본은 새 하이퍼스케일(Citus) 서버 그룹으로 만들어집니다. 기존 서버 그룹을 복제본으로 만들 수 없습니다. 다른 읽기 복제본의 복제본은 만들 수 없습니다.

### <a name="replica-configuration"></a>복제본 구성

복제본은 주 서버와 동일한 컴퓨팅, 스토리지 및 작업자 노드 설정을 사용하여 만듭니다. 복제본을 만든 후 스토리지 및 백업 보존 기간을 포함하여 여러 가지 설정을 변경할 수 있습니다. 스토리지 크기 및 작업자 노드 수와 같은 다른 설정은 복제본에서 변경할 수 없습니다.

주 서버에서 도착하는 변경 내용을 유지할 수 있도록 복제본을 충분히 강력하게 유지해야 합니다. 예를 들어 주 서버에서 컴퓨팅 능력을 업스케일링하면 복제본에서도 컴퓨팅 능력을 업스케일링해야 합니다.

복제본을 만들 때나 그 후에 방화벽 규칙 및 매개 변수 설정은 주 서버에서 복제본으로 상속되지 않습니다.

### <a name="regions"></a>영역

하이퍼스케일(Citus) 서버 그룹은 동일 지역 복제만 지원합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 읽기 복제본을 만들고 관리하는 방법](howto-hyperscale-read-replicas-portal.md)을 알아봅니다.
