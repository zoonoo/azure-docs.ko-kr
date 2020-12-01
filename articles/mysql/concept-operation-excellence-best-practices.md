---
title: MySQL server 운영 모범 사례-Azure Database for MySQL
description: 이 문서에서는 Azure에서 MySQL 데이터베이스를 작동 하는 모범 사례를 설명 합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355120"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Azure Database for MySQL에서 서버 작업에 대 한 모범 사례-단일 서버

Azure Database for MySQL 사용을 위한 모범 사례에 대해 알아봅니다. 플랫폼에 새로운 기능을 추가 하는 경우이 섹션에서 자세히 설명 하는 모범 사례를 구체화 하는 데 계속 집중 하 게 됩니다.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Azure Database for MySQL 작업 지침 

데이터베이스의 성능을 향상 시키기 위해 Azure Database for MySQL 작업할 때 따라야 하는 작업 지침은 다음과 같습니다. 

* **공동** 배치: 네트워크 대기 시간을 줄이려면 클라이언트와 데이터베이스 서버를 동일한 Azure 지역에 배치 합니다.

* **메모리, CPU 및 저장소 사용량 모니터링**: 시스템 성능 및 가용성을 유지 관리할 수 있도록 사용 패턴이 변경 되거나 배포 용량에 접근 하는 경우 알리도록 [경고를 설정할](howto-alert-on-metric.md) 수 있습니다. 

* **DB 인스턴스** 강화: 저장소 용량 제한에 도달할 때 [확장할](howto-create-manage-server-portal.md) 수 있습니다. 응용 프로그램에서 예상치 못한 수요를 수용할 수 있도록 저장소 및 메모리에 몇 가지 버퍼가 있어야 합니다. 저장소 제한에 가까워지면 저장소 크기 자동 조정 기능을 [사용 하도록 설정](howto-auto-grow-storage-portal.md) 하 여 서비스에서 저장소 크기를 자동으로 조정 하도록 할 수도 있습니다. 

* **백업 구성**: 비즈니스 요구 사항에 따라 [로컬 또는 지역 중복 백업을](howto-restore-server-portal.md#set-backup-configuration) 사용 하도록 설정 합니다. 또한 비즈니스 연속성을 위해 백업을 사용할 수 있는 기간에 대 한 보존 기간을 수정 합니다. 

* **I/o 용량 증가**: 데이터베이스 워크 로드에 프로 비전 된 것 보다 많은 i/o가 필요한 경우 데이터베이스에 대 한 복구 또는 기타 트랜잭션 작업이 느릴 수 있습니다. 서버 인스턴스의 i/o 용량을 늘리려면 다음 중 하나 또는 모두를 수행 합니다. 

    * Azure database for MySQL은 프로 비전 된 GB 당 IOPS 3 개를 속도로 IOPS 크기 조정을 제공 합니다. 더 나은 성능을 위해 [프로 비전 된 저장소를 늘려](howto-create-manage-server-portal.md#scale-storage-up) IOPS 크기를 조정 합니다. 

    * 프로 비전 된 IOPS 저장소를 이미 사용 중인 경우 [추가 처리량 용량](howto-create-manage-server-portal.md#scale-storage-up)을 프로 비전 합니다. 

* **크기 조정 계산**: 데이터베이스 작업은 CPU 또는 메모리로 인해 제한 될 수도 있으며 트랜잭션 처리에 심각한 영향을 미칠 수 있습니다. 계산 (가격 책정 계층)은 [범용 또는 메모리](concepts-pricing-tiers.md) 액세스에 최적화 된 계층에 대해서만 확장 하거나 축소할 수 있습니다. 

* **장애 조치 (failover) 테스트**: 서버 인스턴스에 대 한 장애 조치 (failover)를 수동으로 테스트 하 여 사용 사례에 대해 프로세스가 수행 되는 시간을 파악 하 고 서버 인스턴스에 액세스 하는 응용 프로그램이 장애 조치 (failover) 후에 자동으로 새 서버 인스턴스에 연결할 수 있도록 합니다.

* **기본 키 사용**: Azure Database for MySQL에서 작업할 때 테이블에 기본 키 또는 고유 키가 있는지 확인 합니다. 이를 통해 백업, 복제본 등을 많이 사용 하 고 성능을 향상 시킬 수 있습니다.

* **TTL 값 구성**: 클라이언트 응용 프로그램이 서버 인스턴스의 DNS (도메인 이름 서비스) 데이터를 캐시 하는 경우 30 초 미만의 ttl (time-to-live) 값을 설정 합니다. 서버 인스턴스의 기본 IP 주소는 장애 조치 (failover) 후에 변경 될 수 있으므로 확장 된 시간 동안 DNS 데이터를 캐시 하면 응용 프로그램이 더 이상 서비스에 없는 IP 주소에 연결 하려고 할 때 연결 오류가 발생할 수 있습니다.

* 연결 풀링을 사용 하 여 [최대 연결 제한에 도달](concepts-server-parameters.md#max_connections)하지 않도록 하 고 재시도 논리를 사용 하 여 일시적인 연결 문제를 방지 합니다. 

* 복제본을 사용 하는 경우 [Proxysql을 사용 하](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) 여 주 서버와 읽기 가능한 보조 복제본 서버 간의 부하를 분산 합니다. 여기에서 설정 단계를 참조 하세요. </br> 

* 리소스를 프로 비전 할 때 Azure Database for MySQL 자동 [증가를 사용 하도록 설정](howto-auto-grow-storage-portal.md) 했는지 확인 합니다. 이렇게 하면 추가 비용이 발생 하지 않으며, 실행 될 수 있는 모든 저장소 병목 상태의 데이터베이스를 보호할 수 있습니다. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>Azure Database for MySQL에서 InnoDB 사용

*   시스템 테이블 스페이스 데이터 파일을 사용 하는 경우 `ibdata1` 테이블에서 데이터를 삭제 하거나 테이블을 테이블당 파일로 이동 하 여 축소 하거나 제거할 수 없습니다 `tablespaces` .

* 크기가 1TB 보다 큰 데이터베이스의 경우 **innodb_file_per_table** 에서 테이블을 만들어야 합니다 `tablespace` . 크기가 1TB 보다 큰 단일 테이블의 경우 [파티션](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) 테이블이 있어야 합니다.

*   가 많은 서버의 경우 `tablespace` MySQL 시작 또는 장애 조치 (failover) 중에 순차적 테이블 스페이스 검색 때문에 엔진 시작 속도가 매우 느립니다. 

* 테이블을 만들기 전에 innodb_file_per_table = ON을 설정 합니다. 전체 테이블 번호가 500 보다 작은 경우에는를 설정 합니다.

* 데이터베이스에 테이블이 500 개 이상 있는 경우 각 개별 테이블에 대 한 테이블 크기를 검토 합니다. 규모가 많은 테이블의 경우에는 테이블 별 파일 테이블을 사용 하 여 시스템 테이블 스페이스 파일에 최대 저장소 용량 제한이 발생 하지 않도록 주의 해야 합니다.

> [!NOTE]
> 크기가 5GB 보다 작은 테이블의 경우 시스템 테이블 스페이스를 사용 하는 것이 좋습니다. 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* 매우 큰 테이블이 1tb를 초과 하 여 커질 수 있는 경우 테이블을 만들 때 테이블을 [분할](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) 합니다.

* 여러 MySQL 서버를 사용 하 고 해당 서버에서 테이블을 분산 합니다. 1만 테이블을 많이 사용 하는 경우 단일 서버에 너무 많은 테이블을 배치 하지 마세요. 

## <a name="next-steps"></a>다음 단계
- [Azure Database for MySQL의 성능에 대 한 모범 사례](concept-performance-best-practices.md)
- [Azure Database for MySQL 모니터링에 대 한 모범 사례](concept-monitoring-best-practices.md)
