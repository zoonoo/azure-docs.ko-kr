---
title: MySQL 서버 운영 모범 사례 - Azure Database for MySQL
description: 이 문서에서는 Azure에서 MySQL 데이터베이스를 운영하는 모범 사례를 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96355120"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Azure Database for MySQL 서버 작업에 대한 모범 사례 - 단일 서버

Azure Database for MySQL 작업에 대한 모범 사례에 대해 알아봅니다. 플랫폼에 새로운 기능을 추가하는 경우 이 섹션에서 자세히 설명된 모범 사례를 계속해서 구체화하는 데 집중할 것입니다.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Azure Database for MySQL 운영 지침 

다음은 데이터베이스의 성능을 향상시키기 위해 Azure Database for MySQL 작업을 할 경우 따라야 하는 운영 지침입니다. 

* **공동 배치:** 네트워크 대기 시간을 줄이려면 클라이언트와 데이터베이스 서버를 동일한 Azure 지역에 배치합니다.

* **메모리, CPU 및 스토리지 사용량 모니터링:** 시스템 성능 및 가용성을 유지할 수 있도록 사용 패턴이 변경되거나 배포 용량에 근접할 때 알리도록 [경고를 설정](howto-alert-on-metric.md)할 수 있습니다. 

* **DB 인스턴스 확장:** 스토리지 용량 제한에 근접할 때 [확장](howto-create-manage-server-portal.md)할 수 있습니다. 애플리케이션의 예기치 않은 수요 증가를 수용하려면 스토리지 및 메모리에 일부 버퍼가 있어야 합니다. [스토리지 크기 자동 증가를 사용](howto-auto-grow-storage-portal.md)하도록 'ON'으로 설정하여 스토리지 한도에 가까워지면 서비스가 스토리지 크기를 자동으로 조정하도록 할 수도 있습니다. 

* **백업 구성:** 비즈니스 요구 사항에 따라 [로컬 또는 지역 중복 백업을 사용](howto-restore-server-portal.md#set-backup-configuration)하도록 설정합니다. 또한 비즈니스 연속성을 위해 백업을 사용할 수 있는 기간에 대한 보존 기간을 수정합니다. 

* **I/O 용량 증가:** 데이터베이스 워크로드에 프로비전한 것보다 더 많은 I/O가 필요한 경우 데이터베이스에 대한 복구 또는 기타 트랜잭션 작업이 느려집니다. 서버 인스턴스의 I/O 용량을 늘리려면 다음을 수행합니다. 

    * Azure database for MySQL은 프로비전된 GB 스토리지당 3개의 IOPS 속도로 IOPS 크기 조정을 제공합니다. 성능 향상을 위해 [프로비전된 스토리지를 늘려](howto-create-manage-server-portal.md#scale-storage-up) IOPS 크기를 조정합니다. 

    * 이미 프로비전된 IOPS 스토리지를 사용 중인 경우 [추가 처리량 용량](howto-create-manage-server-portal.md#scale-storage-up)을 프로비전합니다. 

* **크기 조정 계산**: 데이터베이스 워크로드는 CPU 또는 메모리로 인해 제한될 수도 있으며 트랜잭션 처리에 심각한 영향을 미칠 수 있습니다. 계산(가격 책정 계층)은 [범용 또는 메모리 최적화](concepts-pricing-tiers.md) 계층 간에만 확장하거나 축소할 수 있습니다. 

* **장애 조치(failover) 테스트**: 서버 인스턴스에 대한 장애 조치(failover)를 수동으로 테스트하여 사용 사례에 대해 프로세스가 수행되는 시간을 파악하고 서버 인스턴스에 액세스하는 애플리케이션이 장애 조치(failover) 후에 자동으로 새 서버 인스턴스에 연결할 수 있도록 합니다.

* **기본 키 사용**: Azure Database for MySQL에서 작업할 때 테이블에 기본 키 또는 고유 키가 있는지 확인합니다. 이를 통해 백업, 복제본 등에 많은 도움이 되며 성능을 향상시킬 수 있습니다.

* **TTL 값 구성**: 클라이언트 애플리케이션이 서버 인스턴스의 DNS(도메인 이름 서비스) 데이터를 캐싱하는 경우 30초 미만의 TTL(time-to-live) 값을 설정합니다. 서버 인스턴스의 기본 IP 주소는 장애 조치(failover) 후에 변경될 수 있기 때문에 확장된 시간 동안 DNS 데이터를 캐싱하면 애플리케이션이 더 이상 서비스에 없는 IP 주소에 연결하려고 할 경우 연결 오류가 발생할 수 있습니다.

* 연결 풀링을 사용하여 [최대 연결 제한](concepts-server-parameters.md#max_connections)에 도달하지 않도록 하고 재시도 논리를 사용하여 일시적인 연결 문제를 방지합니다. 

* 복제본을 사용하는 경우 주 서버와 읽기 가능한 보조 복제본 서버 간의 부하를 [분산시키도록 ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847)을 사용합니다. 여기에서 설정 단계를 참조 하세요. </br> 

* 리소스를 프로비전할 경우 [Azure Database for MySQL 자동 증가를 사용](howto-auto-grow-storage-portal.md)하도록 설정했는지 확인합니다. 이렇게 하면 추가 비용이 발생하지 않으며, 실행될 수 있는 모든 스토리지 병목 상태로부터 데이터베이스를 보호할 수 있습니다. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>Azure Database for MySQL에서 InnoDB 사용

*   시스템 테이블스페이스 데이터 파일인 `ibdata1` 기능을 사용하는 경우 테이블에서 데이터를 삭제하거나 테이블을 테이블당 파일 `tablespaces`로 이동하여 축소하거나 제거할 수 없습니다.

* 크기가 1TB보다 큰 데이터베이스의 경우 **innodb_file_per_table** `tablespace`에 테이블을 만들어야 합니다. 크기가 1TB보다 큰 단일 테이블의 경우 [파티션](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) 테이블이어야 합니다.

*   `tablespace`가 많은 서버의 경우 MySQL 시작 또는 장애 조치(failover) 중에 순차적 테이블스페이스 검색으로 인해 엔진 시작이 매우 느려집니다. 

* 총 테이블 번호가 500보다 작은 경우 테이블을 만들기 전에 innodb_file_per_table = ON으로 설정합니다.

* 데이터베이스에 500개 이상의 테이블이 있는 경우 각 개별 테이블의 테이블 크기를 검토합니다. 큰 테이블의 경우 시스템 테이블스페이스 파일이 최대 스토리지 한도에 도달하지 않도록 테이블별 파일 테이블스페이스를 사용하는 것이 좋습니다.

> [!NOTE]
> 크기가 5GB 미만인 테이블의 경우 시스템 테이블스페이스를 사용하는 것이 좋습니다. 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* 매우 큰 테이블이 1TB를 초과하여 증가할 수 있는 경우 테이블을 만들 때 테이블을 [파티션](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html)합니다.

* 여러 MySQL 서버를 사용하고 해당 서버에 테이블을 분산합니다. 약 10000개 이상의 테이블이 있는 경우 단일 서버에 너무 많은 테이블을 배치하지 마십시오. 

## <a name="next-steps"></a>다음 단계
- [Azure Database for MySQL의 성능에 대한 모범 사례](concept-performance-best-practices.md)
- [Azure Database for MySQL 모니터링에 대한 모범 사례](concept-monitoring-best-practices.md)
