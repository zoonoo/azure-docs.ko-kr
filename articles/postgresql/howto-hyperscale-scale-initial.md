---
title: 초기 서버 그룹 크기 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 사용 사례에 적합한 초기 크기 선택
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8b87cfc8276d13ccc7e12a4901489ea0b1e770a5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012509"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>하이퍼스케일(Citus) 서버 그룹에 대해 초기 크기 선택

서버 그룹의 크기(노드 수와 하드웨어 용량 모두)는 [변경하기 쉽습니다](howto-hyperscale-scale-grow.md). 하지만 새 서버 그룹에 대한 초기 크기를 선택해야 합니다. 적절한 선택을 위한 몇 가지 팁이 여기에 있습니다.

## <a name="use-cases"></a>사용 사례

하이퍼스케일(Citus)은 다음과 같은 방법으로 자주 사용됩니다.

### <a name="multi-tenant-saas"></a>다중 테넌트 SaaS

기존 단일 노드 PostgreSQL 데이터베이스 인스턴스에서 하이퍼스케일(Citus)로 마이그레이션할 때 총 작업자 vCore 및 RAM 수가 원본 인스턴스의 수와 같은 클러스터를 선택합니다. 이러한 시나리오에서는 분할된 데이터베이스가 리소스 사용률을 개선하고 더 작은 인덱스 등을 허용하기 때문에 2~3배 성능이 향상되었습니다.

실제로 vCore 수만 결정하면 됩니다. [하이퍼스케일(Citus) 구성 옵션](concepts-hyperscale-configuration-options.md) 페이지에 설명된 대로 RAM 할당은 현재 vCore 수를 기준으로 결정됩니다.
코디네이터 노드에는 작업자만큼 많은 RAM이 필요하지 않지만, RAM과 vCore를 독립적으로 선택할 수 있는 방법이 없습니다.

### <a name="real-time-analytics"></a>실시간 분석

총 vCore: 작업 데이터가 RAM에 적합한 경우 작업자 코어 수에 비례하여 하이퍼스케일(Citus)의 선형 성능 향상을 기대할 수 있습니다. 사용자 요구에 적합한 vCore 수를 결정하려면 단일 노드 데이터베이스의 쿼리에 대한 현재 대기 시간 및 하이퍼스케일(Citus)의 필요한 대기 시간을 고려합니다. 현재 대기 시간을 원하는 대기 시간으로 나누고 결과를 반올림합니다.

작업자 RAM: 최상의 사례는 대부분의 작업 집합이 메모리에 맞는 충분한 메모리를 제공하는 것입니다. 애플리케이션에서 사용하는 쿼리 유형은 메모리 요구 사항에 영향을 줍니다. 쿼리에서 EXPLAIN ANALYZE를 실행하여 얼마나 많은 메모리가 필요한지 확인할 수 있습니다. [하이퍼스케일(Citus) 구성 옵션](concepts-hyperscale-configuration-options.md) 문서에 설명된 대로 vCore와 RAM은 크기가 함께 조정됩니다.

## <a name="choosing-a-hyperscale-citus-tier"></a>하이퍼스케일(Citus) 계층 선택

> [!IMPORTANT]
> 하이퍼스케일(Citus) 서비스 계층은 현재 미리 보기로 제공되고 있습니다.  이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>
> [하이퍼스케일(Citus)의 미리 보기 기능](hyperscale-preview-features.md)에서 다른 새 기능의 전체 목록을 볼 수 있습니다.

위의 섹션에서는 각 사용 사례에 대해 얼마나 많은 vCore 및 RAM이 필요한지 아이디어를 제공합니다. 두 하이퍼스케일(Citus) 계층(기본 계층과 표준 계층) 중에 선택하여 이러한 요구 사항을 충족할 수 있습니다.

기본 계층은 단일 데이터베이스 노드를 사용하여 처리를 수행하는 반면 표준 계층은 더 많은 노드를 허용합니다. 계층은 그 외에는 동일하며 같은 기능을 제공합니다. 일부 경우에는 단일 노드의 vCore 및 디스크 공간의 크기를 충분히 조정할 수 있으며 다른 경우에는 여러 노드의 협력이 필요합니다.

계층 비교는 [기본 계층](concepts-hyperscale-tiers.md) 개념 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [서버 그룹 스케일링](howto-hyperscale-scale-grow.md)
- 서버 그룹 [성능 옵션](concepts-hyperscale-configuration-options.md)에 대해 자세히 알아봅니다.
