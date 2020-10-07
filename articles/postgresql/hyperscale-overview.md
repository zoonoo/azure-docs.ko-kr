---
title: Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 개요
description: 하이퍼스케일(Citus) 배포 옵션의 개요를 제공합니다.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: 90d3cb106fa93649f7d6dda5ab5755061d118f66
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268386"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - 하이퍼스케일(Citus)이란?

Azure Database for PostgreSQL은 개발자용으로 빌드된 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. 오픈 소스 [PostgreSQL](https://www.postgresql.org/) 데이터베이스 엔진의 커뮤니티 버전을 기반으로 합니다.

하이퍼스케일(Citus)은 분할을 사용하여 여러 머신에 걸쳐 쿼리 크기를 수평으로 조정하는 배포 옵션입니다. 쿼리 엔진은 들어오는 SQL 쿼리를 이러한 서버 간에 병렬 처리하여 큰 데이터 세트에서 더 빠르게 응답합니다. 이 옵션은 다른 배포 옵션에 비해 더 뛰어난 크기 조정과 성능이 필요한 애플리케이션, 일반적으로 100GB의 데이터에 이르거나 이미 초과한 워크로드를 처리합니다.

하이퍼스케일(Citus)은 다음을 제공합니다.

- 분할을 사용하여 여러 머신에서 수평적 크기 조정
- 이러한 서버 간에 쿼리를 병렬 처리하여 큰 데이터 세트에서 더 빠르게 응답
- 다중 테넌트 애플리케이션, 실시간 운영 분석 및 높은 처리량 트랜잭션 워크로드에 대한 탁월한 지원

PostgreSQL용으로 빌드된 애플리케이션은 표준 [연결 라이브러리](./concepts-connection-libraries.md) 및 최소한의 변경을 통해 하이퍼스케일(Citus)에서 분산 쿼리를 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for PostgreSQL - 하이퍼스케일(Citus) 서버 그룹을 [먼저 만들고](./quickstart-create-hyperscale-portal.md) 시작합니다.
- 비용 비교 및 계산기는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/)를 참조하세요. 하이퍼스케일(Citus)은 선불 예약 인스턴스 할인도 제공합니다. 자세한 내용은 [하이퍼스케일(Citus) RI 가격 책정](concepts-hyperscale-reserved-pricing.md) 페이지를 참조하세요.
- 서버 그룹에 적합한 [처음 크기](howto-hyperscale-scaling.md#picking-initial-size) 결정
