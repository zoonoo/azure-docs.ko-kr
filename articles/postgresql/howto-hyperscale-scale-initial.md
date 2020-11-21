---
title: 초기 서버 그룹 크기-Hyperscale (Citus)-Azure Database for PostgreSQL
description: 사용 사례에 적합 한 초기 크기를 선택 합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026406"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Citus (Hyperscale) 서버 그룹에 대 한 초기 크기를 선택 합니다.

서버 그룹의 크기 (노드 수와 하드웨어 용량 모두)는 [변경 하기 쉽습니다](howto-hyperscale-scale-grow.md). 그러나 새 서버 그룹에 대 한 초기 크기를 선택 해야 합니다. 적절 한 선택에 대 한 몇 가지 팁은 다음과 같습니다.

## <a name="multi-tenant-saas-use-case"></a>다중 테 넌 트 SaaS 사용 사례

기존 단일 노드 PostgreSQL 데이터베이스 인스턴스에서 Citus (Hyperscale)로 마이그레이션하는 경우 작업자 vCores의 수와 총 RAM이 원본 인스턴스의 수와 같은 클러스터를 선택 합니다. 이러한 시나리오에서는 분할 리소스 사용률을 향상 시키고 더 작은 인덱스를 허용 하기 때문에 2 ~ 3 배 성능이 개선 되었습니다.

VCore 수는 실제로 유일한 결정입니다. [Citus (Hyperscale) 구성 옵션](concepts-hyperscale-configuration-options.md) 페이지에 설명 된 대로 RAM 할당은 현재 vcore 수를 기준으로 결정 됩니다.
코디네이터 노드에는 작업자 만큼 많은 RAM이 필요 하지 않지만, RAM 및 vCores를 독립적으로 선택할 수 있는 방법은 없습니다.

## <a name="real-time-analytics-use-case"></a>실시간 분석 사용 사례

총 vCores: 작업 데이터가 RAM에 적합 한 경우 작업자 코어 수에 비례하여 Citus (Hyperscale)의 선형 성능 향상을 예측할 수 있습니다. 요구 사항에 적합 한 vCores 수를 결정 하려면 단일 노드 데이터베이스의 쿼리에 대 한 현재 대기 시간 및 Hyperscale의 필요한 대기 시간 (Citus)을 고려 하세요. 현재 대기 시간을 원하는 대기 시간으로 나누고 결과를 반올림합니다.

작업자 RAM: 최상의 사례는 대부분의 작업 집합이 메모리에 맞는 충분한 메모리를 제공하는 것입니다. 응용 프로그램에서 사용 하는 쿼리 형식은 메모리 요구 사항에 영향을 줍니다. 쿼리에 대해 설명 하는 분석을 실행 하 여 필요한 메모리 양을 확인할 수 있습니다. VCores 및 RAM은 [Citus (Hyperscale) 구성 옵션](concepts-hyperscale-configuration-options.md) 문서에 설명 된 대로 함께 확장 됩니다.

## <a name="next-steps"></a>다음 단계

- [서버 그룹 스케일링](howto-hyperscale-scale-grow.md)
- 서버 그룹 [성능 옵션](concepts-hyperscale-configuration-options.md)에 대해 자세히 알아보세요.
