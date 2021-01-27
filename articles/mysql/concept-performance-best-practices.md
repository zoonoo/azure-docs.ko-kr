---
title: 성능 모범 사례-Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL에 대 한 성능을 모니터링 하 고 조정 하는 모범 사례를 설명 합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 30176e2df850e6d2794ab9c1542bcb6a89d8f89f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880409"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Azure Database for MySQL의 최적의 성능을 위한 모범 사례-단일 서버

Azure Database for MySQL 단일 서버를 사용 하는 동안 최상의 성능을 얻기 위한 모범 사례에 대해 알아봅니다. 플랫폼에 새로운 기능을 추가 하는 경우이 섹션에서 자세히 설명 하는 모범 사례를 계속 구체화 합니다.

## <a name="physical-proximity"></a>물리적 근접

 응용 프로그램과 데이터베이스를 동일한 지역에 배포 해야 합니다. 성능 벤치마킹 실행을 시작 하기 전에 빠른 검사는 간단한 SELECT 1 쿼리를 사용 하 여 클라이언트와 데이터베이스 간의 네트워크 대기 시간을 확인 하는 것입니다. 

## <a name="accelerated-networking"></a>가속 네트워킹

Azure 가상 머신, Azure Kubernetes 또는 App Services를 사용 하는 경우 응용 프로그램 서버에 가속화 된 네트워킹을 사용 합니다. 가속화된 네트워킹을 사용하면 VM에 대한 SR-IOV(단일 루트 I/O 가상화)를 구현할 수 있어 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회함으로써 대기 시간, 지터 및 CPU 사용률을 줄이므로 지원되는 VM 유형에서 가장 까다로운 네트워크 워크로드에 사용합니다.

## <a name="connection-efficiency"></a>연결 효율성

새 연결을 설정 하는 작업은 항상 비용과 시간이 많이 걸리는 작업입니다. 응용 프로그램은 데이터베이스 연결을 요청할 때 새 유휴 데이터베이스 연결을 만드는 대신 기존 유휴 데이터베이스 연결의 할당을 우선적으로 적용 합니다.  다음은 좋은 연결 방법에 대 한 몇 가지 옵션입니다.

- **Proxysql**: 기본 제공 연결 풀링을 제공 하 고 응용 프로그램 코드를 변경 하 여 요청 시 필요에 따라 작업 부하를 여러 읽기 복제본으로 [부하 분산](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) 하는 [proxysql](https://proxysql.com/) 을 사용 합니다.

- **Heimdall Data proxy**: 또는 공급 업체 중립적 소유 프록시 솔루션인 Heimdall data proxy를 활용할 수도 있습니다. 복제 지연 검색을 사용 하 여 쿼리 캐싱 및 읽기/쓰기 분할을 지원 합니다. [Heimdall 프록시를 사용 하 여 MySQL 성능을 가속화](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349)하는 방법을 참조할 수도 있습니다.  

- **영구적 또는 Long-Lived 연결**: 응용 프로그램에 일반적으로 실행 시간 < 5-10 밀리초를 포함 하는 짧은 트랜잭션 또는 쿼리가 있는 경우 영구 연결로 short 연결을 대체 합니다. Short 연결을 영구 연결로 대체 하려면 코드를 약간 변경 해야 하지만 많은 일반적인 응용 프로그램 시나리오에서 성능을 향상 시키는 측면에서 큰 영향을 주지 않습니다. 트랜잭션이 완료 되 면 시간 제한 또는 연결 닫기를 설정 해야 합니다.

- **복제본**: 복제본을 사용 하는 경우 [proxysql](https://proxysql.com/) 을 사용 하 여 주 서버와 읽기 가능한 보조 복제본 서버 간의 부하를 분산 합니다. [ProxySQL을 설정 하는 방법](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847)에 대해 알아봅니다.

## <a name="data-import-configurations"></a>데이터 가져오기 구성

- 데이터 가져오기 작업을 시작 하기 전에 인스턴스를 더 높은 SKU 크기로 임시로 확장 한 다음 가져오기가 성공할 경우 규모를 축소할 수 있습니다.
- 온라인 또는 오프 라인 마이그레이션에 대해 [DMS (Azure Database Migration Service)](https://datamigration.microsoft.com/) 를 사용 하 여 최소 가동 중지 시간으로 데이터를 가져올 수 있습니다. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Azure Database for MySQL 메모리 권장 사항

Azure Database for MySQL 성능 모범 사례는 충분 한 RAM을 할당 하 여 작업 집합이 메모리에 거의 완전히 상주할 수 있도록 하는 것입니다. 

- [MySQL 서버에 대 한 메트릭을](./concepts-monitoring.md)사용 하 여 한 [도](./concepts-pricing-tiers.md) 에 도달 하는 데 사용 되는 메모리 백분율이 올바른지 확인 합니다. 
- 이러한 번호에 대 한 경고를 설정 하 여 서버가 제한에 도달 하면 문제를 해결 하기 위해 프롬프트 작업을 수행할 수 있도록 합니다. 정의 된 제한에 따라 데이터베이스 SKU를 더 높은 계산 크기나 더 나은 가격 책정 계층으로 확장 하 여 성능이 크게 증가 하는지 확인 합니다. 
- 크기 조정 작업 후 성능 숫자가 더 이상 크게 떨어질 때까지 확장 합니다. DB 인스턴스의 메트릭을 모니터링 하는 방법에 대 한 자세한 내용은 [MYSQL DB 메트릭](./concepts-monitoring.md#metrics)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL를 사용 하는 서버 작업에 대 한 모범 사례](concept-operation-excellence-best-practices.md) <br/>
- [Azure Database for MySQL 모니터링에 대 한 모범 사례](concept-monitoring-best-practices.md)<br/>
- [Azure Database for MySQL 시작](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>