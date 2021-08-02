---
title: MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션 가이드 평가
description: MySQL 워크로드를 마이그레이션하기 전에 수행해야 하는 많은 실사가 있습니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/25/2021
ms.openlocfilehash: f5a02a9e4ec6b6c1e9961637d588a3838570c432
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969911"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-assessment"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션 가이드 평가

MySQL 워크로드를 마이그레이션하기 전에 수행해야 하는 많은 실사가 있습니다. 여기에는 데이터, 호스팅 환경 및 애플리케이션 워크로드를 분석하여 Azure 랜딩 존이 올바르게 구성되고 곧 마이그레이션될 워크로드를 호스트할 준비가 되었는지 확인하는 작업이 포함됩니다.

### <a name="limitations"></a>제한 사항

Azure Database for MySQL은 플랫폼으로 실행되는 MySQL 커뮤니티 버전의 완전히 지원되는 버전입니다. 그러나 초기 평가를 수행하는 경우 익숙해져야 할 [몇 가지 제한 사항](../concepts-limits.md)이 있습니다.

가장 중요한 것은 다음과 같습니다.

  - `InnoDB` 및 `Memory` 전용 스토리지 엔진 지원

  - 제한적 `Privilege` 지원(`DBA`, `SUPER`, `DEFINER`)

  - 사용하지 않도록 설정된 데이터 조직 문(`SELECT ... INTO OUTFILE`, `LOAD DATA INFILE`)

  - 중요한 데이터베이스 자동 마이그레이션(5.6에서 5.7로, 5.7에서 8.0으로)

  - [MySQL Server UDF(사용자 정의 함수)](https://dev.mysql.com/doc/refman/5.7/en/server-udfs.html)를 사용하는 경우 `so` 또는 `dll` 구성 요소를 Azure Database for MySQL에 업로드할 수 있는 기능이 없기 때문에 실행 가능한 유일한 호스팅 옵션은 Azure Hosted VM입니다.

다른 많은 항목은 관리자가 운영 데이터 워크로드 수명 주기 관리의 일부로 숙지해야 하는 운영 측면입니다. 이 가이드에서는 마이그레이션 후 관리 섹션에서 이러한 여러 운영 측면을 살펴봅니다.

### <a name="mysql-versions"></a>MySQL 버전

MySQL은 1995년부터 다채로운 역사를 갖습니다. 이때부터 널리 사용되는 데이터베이스 관리 시스템으로 발전했습니다. Azure Database for MySQL은 최초로 MySQL 버전 5.6을 지원했으며 5.7과 최근의 8.0까지 지원하고 있습니다. Azure Database for MySQL 버전 지원에 대한 최신 정보는 [지원되는 Azure Database for MySQL 서버 버전](../concepts-supported-versions.md)을 참조하세요. 마이그레이션 후 관리 섹션에서 Azure의 MySQL 인스턴스에 업그레이드가 적용되는 방식(예: 5.7.20에서 5.7.21로)을 검토합니다.

> [!NOTE]
> 버전이 5.x에서 8.0으로 대폭 업그레이드된 것은 Oracle에서 MySQL을 인수했기 때문입니다. MySQL 기록에 대해 자세히 알아보려면 [MySQL wiki 페이지](https://en.wikipedia.org/wiki/MySQL)로 이동합니다.

원본 MySQL 버전을 아는 것은 필수적입니다. 시스템을 사용하는 애플리케이션은 해당 버전과 관련된 데이터베이스 개체 및 기능을 사용할 수 있습니다. 데이터베이스를 더 낮은 버전으로 마이그레이션하면 호환성 문제 및 기능 손실이 발생할 수 있습니다. 또한 도입된 기능이 애플리케이션을 중단시킬 수 있기 때문에 최신 버전으로 마이그레이션하기 전에 데이터 및 애플리케이션 인스턴스를 철저히 테스트하는 것이 좋습니다.

마이그레이션 경로 및 버전에 영향을 줄 수 있는 예제:

  - 5.6: 올바른 스토리지에 대한 TIMESTAMP 열(밀리초) 및 전체 텍스트 검색

  - 5.7: 네이티브 JSON 데이터 형식 지원

  - 8.0: NoSQL 문서 저장소, 원자성 및 크래시 안전 DDL 및 JSON 테이블 함수 지원

    > [!NOTE]
    > MySQL 5.6은 2021년 2월부터 더 이상 일반적인 지원이 제공되지 않습니다. MySQL 워크로드는 MySQL 버전 5.7 이상으로 마이그레이션해야 합니다.

MySQL 서버 버전을 확인하려면 다음을 수행합니다.

```
SHOW VARIABLES LIKE "%version%";
```

#### <a name="database-storage-engines"></a>데이터베이스 스토리지 엔진

Azure Database for MySQL은 [InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html) 및 [Memory](https://dev.mysql.com/doc/refman/8.0/en/memory-storage-engine.html) 데이터베이스 스토리지 엔진만 지원합니다. [MyISAM](https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html) 등의 다른 스토리지 엔진은 지원되는 스토리지 엔진으로 마이그레이션해야 합니다. MyISAM과 InnoDB 간의 차이점은 작동 기능과 성능 결과입니다. 상위 수준 테이블 및 스키마 구조는 일반적으로 엔진 간에 변경되지 않지만 스토리지 및 성능상의 이유로 인덱스와 테이블 열 유형이 변경될 수 있습니다. InnoDB는 대용량 데이터 파일이 포함된 것으로 알려져 있지만 이러한 스토리지 세부 정보는 Azure Database for MySQL 서비스에서 관리됩니다.

##### <a name="migrating-from-myisam-to-innodb"></a>MyISAM에서 InnoDB로 마이그레이션

MyISAM 데이터베이스 및 테이블을 InnoDB 테이블로 변환해야 합니다. 변환 후에 애플리케이션의 호환성 및 성능을 테스트해야 합니다. 대부분의 경우 테스트를 수행하려면 테이블을 다시 만들고 DDL 문을 통해 대상 스토리지 엔진을 변경해야 합니다. 이 변경은 Azure 대상에서 스키마를 만드는 동안 발생하므로 마이그레이션 중에 수행해야 할 필요는 거의 없습니다. 자세한 내용은 MySQL 웹 사이트에서 [테이블 변환 개발자 설명서](https://dev.mysql.com/doc/refman/5.6/en/converting-tables-to-innodb.html)를 검토하세요.

유용한 테이블 정보를 찾으려면 다음 쿼리를 사용합니다.

```dotnetcli
    SELECT 
        tab.table_schema,   
        tab.table_name,   
        tab.engine as engine_type,   
        tab.auto_increment,   
        tab.table_rows,   
        tab.create_time,   
        tab.update_time,   
        tco.constraint_type 
    FROM information_schema.tables tab   
    LEFT JOIN information_schema.table_constraints tco   
        ON (tab.table_schema = tco.table_schema   
            AND tab.table_name = tco.table_name   
            )   
    WHERE  
        tab.table_schema NOT IN ('mysql', 'information_schema', 'performance_
schema', 'sys')  
        AND tab.table_type = 'BASE TABLE';
```

> [!NOTE]
> 여러 데이터베이스에서 INFORMATION \_SCHEMA에 대해 쿼리를 실행하면 성능에 영향을 미칠 수 있습니다. 사용량이 낮은 기간 동안 실행합니다.

MyISAM에서 InnoDB로 테이블을 변환하려면 다음을 실행합니다.

```
ALTER TABLE {table\_name} ENGINE=InnoDB;
```

> [!NOTE]
> 이 변환 방법을 통해 테이블이 잠기게 되며 모든 애플리케이션은 작업이 완료될 때까지 대기합니다. 테이블 잠금으로 인해 데이터베이스가 짧은 기간 동안 오프라인으로 표시됩니다.

대신, 구조는 같지만 스토리지 엔진은 다른 테이블을 만들 수 있습니다. 일단 만들어지면 새 테이블에 행을 복사합니다.

```
INSERT INTO {table\_name} SELECT * FROM {myisam\_table} ORDER BY {primary\_key\_columns}
```

> [!NOTE]
> 이 접근 방식이 성공하려면 원래 테이블을 삭제한 다음, 새 테이블의 이름을 바꿔야 합니다. 이 작업으로 인해 짧은 가동 중지 시간이 발생합니다.

#### <a name="database-data-and-objects"></a>데이터베이스 데이터 및 개체

데이터는 데이터베이스 마이그레이션의 한 구성 요소입니다. 애플리케이션이 계속 안정적으로 실행되도록 데이터베이스 지원 개체를 마이그레이션하고 유효성을 검사해야 합니다.

다음은 마이그레이션 전후에 인벤토리해야 하는 항목 목록입니다.

  - 테이블(스키마)

  - 기본 키, 외래 키

  - 인덱스

  - Functions

  - 프로시저

  - 트리거

  - 뷰

#### <a name="user-defined-functions"></a>사용자 정의 함수

MySQL을 사용하면 외부 코드를 호출하는 함수를 사용할 수 있습니다. 아쉽게도 외부 코드와 함께 UDF(사용자 정의 함수)를 사용하는 데이터 워크로드는 Azure Database for MySQL로 마이그레이션할 수 없습니다. 필요한 MySQL 함수가 이러한 방식을 지원하거나 dll 코드를 Azure 서버에 업로드할 수 없기 때문입니다.

다음 쿼리를 실행하여 설치할 수 있는 모든 UDF를 찾습니다.

```
SELECT * FROM mysql.func;
```

### <a name="source-systems"></a>원본 시스템

마이그레이션 준비에 필요한 작업의 양은 원본 시스템 및 해당 위치에 따라 달라질 수 있습니다. 데이터베이스 개체 외에도 원본 시스템에서 대상 시스템으로 데이터를 가져오는 방법을 고려합니다. 방화벽 및 기타 네트워킹 구성 요소가 원본과 대상 사이에 있는 경우 데이터 마이그레이션이 어려울 수 있습니다.

또한 인터넷을 통해 데이터를 이동하는 것이 Azure에 대한 전용 회로를 사용하는 것보다 더 느릴 수 있습니다. 따라서 기가바이트, 테라바이트 및 페타바이트 단위의 많은 데이터를 이동할 때는 원본 네트워크와 Azure 네트워크 간에 [ExpressRoute](../../expressroute/expressroute-introduction.md) 연결을 설정하는 것이 좋습니다.

ExpressRoute가 이미 있는 경우 다른 애플리케이션에서 연결을 사용하고 있는 것일 수 있습니다. 기존 경로를 통해 마이그레이션을 수행하면 네트워크 처리량에 부담을 줄 수 있으며 네트워크를 사용하는 마이그레이션 및 다른 애플리케이션에서 상당한 성능 저하가 발생할 수 있습니다.

마지막으로 디스크 공간을 평가해야 합니다. 큰 데이터베이스를 내보낼 때 데이터의 크기를 고려합니다. 도구가 실행되고 있는 시스템을 확인하고, 궁극적으로 내보내기 위치에 내보내기 작업을 수행하기 위한 충분한 디스크 공간이 있는지 확인합니다.

#### <a name="cloud-providers"></a>클라우드 공급자

AWS(Amazon Web Services)와 같은 클라우드 서비스 공급자에서 데이터베이스를 마이그레이션하는 경우 클라우드 호스팅 MySQL 인스턴스에 액세스하기 위해 추가 네트워킹 구성 단계가 필요할 수 있습니다. Data Migration Service와 같은 마이그레이션 도구는 외부 IP 범위에서 액세스해야 하며, 그러지 않으면 차단될 수 있습니다.

#### <a name="on-premises"></a>온-프레미스

클라우드 공급자와 마찬가지로 MySQL 데이터 워크로드가 방화벽 또는 기타 네트워크 보안 계층 뒤에 있는 경우 온-프레미스 인스턴스와 Azure Database for MySQL 간에 경로를 만들어야 합니다.

### <a name="tools"></a>도구

많은 도구와 방법을 사용하여 MySQL 데이터 워크로드 및 환경을 평가할 수 있습니다. 각 도구는 다양한 평가 및 마이그레이션 기능 모음을 제공합니다. 이 가이드의 일부로 MySQL 데이터 워크로드를 평가하는 데 가장 일반적으로 사용되는 도구를 검토합니다.

#### <a name="azure-migrate"></a>Azure Migrate

[Azure Migrate](../../migrate/migrate-services-overview.md)는 MySQL 데이터베이스 워크로드를 직접 마이그레이션하는 것을 지원하지 않지만, 가상 머신에 호스트되는지 또는 하드웨어 기반 머신에 호스트되는지에 관계없이 관리자가 어떤 사용자 및 어떤 애플리케이션에서 해당 데이터를 사용하는지 잘 모를 때 사용할 수 있습니다. [종속성 분석](../../migrate/concepts-dependency-visualization.md)은 MySQL 워크로드를 호스트하는 머신에서 모니터링 에이전트를 설치하고 실행하여 수행할 수 있습니다. 에이전트는 월과 같은 설정된 기간 동안 정보를 수집합니다. 종속성 데이터를 분석하여 데이터베이스에 대한 알 수 없는 연결을 찾을 수 있습니다. 연결 데이터는 보류 중인 마이그레이션에 대한 알림을 받아야 하는 애플리케이션 소유자를 식별하는 데 유용할 수 있습니다.

애플리케이션 및 사용자 연결 데이터의 종속성 분석 외에도, Azure Migrate에서 [Hyper-V, VMware 또는 물리적 서버](../../migrate/migrate-appliance-architecture.md)를 분석하여 적절한 대상 환경을 제안하는 데 도움이 되는 데이터베이스 워크로드의 사용 패턴을 제공할 수 있습니다.

#### <a name="telgraf-for-linux"></a>Linux용 Telgraf

Linux 워크로드는 [MMA(Microsoft Monitoring Agent)를](../../azure-monitor/agents/agent-linux.md)를 활용하여 가상 및 물리적 머신에서 데이터를 수집할 수 있습니다. 또한 [Telegraf 에이전트](../../azure-monitor/essentials/collect-custom-metrics-linux-telegraf.md) 및 광범위한 플러그 인 배열을 사용하여 성능 메트릭을 수집하는 것이 좋습니다.

#### <a name="service-tiers"></a>서비스 계층

평가 정보(CPU, 메모리, 스토리지 등)를 확보한 경우 마이그레이션 사용자는 다음으로 사용할 Azure Database for MySQL [가격 책정 계층](../concepts-pricing-tiers.md)을 결정합니다.

현재 다음 세 가지 계층이 있습니다.

  - **기본 사항**: 부담 없는 컴퓨팅 및 I/O 성능이 필요한 워크로드

  - **범용**: 확장 가능한 I/O 처리량과 함께 부하가 분산된 컴퓨팅 및 메모리가 필요한 대부분의 비즈니스 워크로드

  - **메모리에 최적화**: 보다 빠른 트랜잭션 처리 및 동시성 향상을 위해 메모리 내 성능이 필요한 고성능 데이터베이스 워크로드

계층 결정은 데이터 워크로드의 RTO 및 RPO 요구 사항에 따라 영향을 받을 수 있습니다. 데이터 워크로드에 4TB 이상의 스토리지가 필요한 경우 추가 단계가 필요합니다. 최대 16TB의 스토리지를 [지원하는 지역](../concepts-pricing-tiers.md#storage)을 검토하고 선택합니다.

> [!NOTE]
> 스토리지 요구 사항을 지원하지 않는 지역의 경우 MySQL 팀(AskAzureDBforMySQL@service.microsoft.com)에 문의하세요.

일반적으로 의사 결정은 스토리지 및 IOPS, 초당 입/출력 작업, 필요량에 중점을 둡니다. 따라서 대상 시스템에는 항상 원본 시스템의 스토리지 크기 만큼이 필요합니다. 또한 IOPS는 3GB로 할당되므로 IOP 필요량을 최종 스토리지 크기에 맞춰 조정하는 것이 중요합니다.

| 요소          | 계층                                                                                                                                                                                                            |
|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **기본**            | 개발 머신, 1TB 미만의 스토리지를 포함하며 고성능이 필요하지 않음                                                                                                                                    |
| **범용**  | 기본 계층이 제공할 수 있는 것보다 더 많은 IOPS가 필요하지만, 16TB 미만의 스토리지, 4GB 미만의 메모리가 필요합니다.                                                                                               |
| **메모리 최적화** | 높은 동시성 innodb_buffer_pool_instances, 큰 BLOB 크기, 복제 대상이 많은 시스템 등, 높은 메모리나 높은 캐시, 버퍼 관련 서버 구성을 활용하는 데이터 워크로드 |

#### <a name="costs"></a>비용

전체 WWI MySQL 데이터 워크로드를 평가한 후 WWI는 최소 4개의 vCore, 20GB의 메모리, IOP 용량이 450 IOPS인 100GB 이상의 스토리지 공간이 필요하다고 결정했습니다. [Azure Database for MySQL IOP 할당 방법](../concepts-pricing-tiers.md#storage)으로 인해 450의 IOPS 요구 사항이 발생하므로 150GB 이상의 스토리지를 할당해야 합니다. 또한 프로비저닝된 서버 스토리지의 100% 이상이 백업 스토리지로 필요하고 하나의 읽기 복제본이 필요합니다. 아웃바운드 송신이 5GB를 초과할 것으로 예상하지는 않습니다.

WWI는 [Azure Database for MySQL 가격 계산기](https://azure.microsoft.com/en-us/pricing/details/mysql/)를 사용하여 Azure Database for MySQL 인스턴스에 대한 비용을 확인할 수 있었습니다. 2020년 9월부터 TCO(총 소유 비용)는 WWI Conference 데이터베이스의 다음 테이블에 표시됩니다.

| 리소스                  | Description                       | 수량                                                    | 비용          |
|---------------------------|-----------------------------------|-------------------------------------------------------------|---------------|
| **컴퓨팅(범용)** | 4개 vCore, 20GB                    | 1 @ $0.351/시간                                               | $3074.76/년 |
| **스토리지**                   | 5GB                               | 12 x 150 @ $0.115                                           | $207/년     |
| **Backup**                    | 프로비저닝된 스토리지의 최대 100% | 프로비저닝된 서버 스토리지의 100%까지 추가 비용 없음 | $0.00/년    |
| **읽기 복제본**              | 1초 지역 복제본           | 컴퓨팅 + 스토리지                                           | $3281.76/년 |
| **Network**                   | < 5GB/월 송신                | 무료                                                        |               |
| **합계**                     |                                   |                                                             | $6563.52/년 |

WWI의 CIO는 초기 비용을 검토한 후 3년보다 훨씬 긴 기간 동안 Azure를 사용하고 있다는 사실을 확인했습니다. 3년의 [예약 인스턴스](../concept-reserved-pricing.md)를 사용하여 연간 \~$4K를 추가로 절약하기로 결정했습니다.

| 리소스                  | Description                       | 수량                                                     | 비용         |
|---------------------------|-----------------------------------|--------------------------------------------------------------|--------------|
| **컴퓨팅(범용)** | vCore 4개                          | 1 @ $0.1375/시                                               | $1204.5/년 |
| **스토리지**                   | 5GB                               | 12 x 150 @ $0.115                                            | $207/년    |
| **Backup**                    | 프로비저닝된 스토리지의 최대 100% | 프로비저닝된 서버 스토리지의 100%까지 추가 비용 없음 | $0.00/년   |
| **Network**                   | < 5GB/월 송신                | 무료                                                         |              |
| **읽기 복제본**              | 1초 지역 복제본           | 컴퓨팅 + 스토리지                                            | $1411.5/년 |
| **합계**                     |                                   |                                                              | $2823/년   |

위의 표와 같이 TCO(총 소유 비용)에서 백업, 네트워크 송신 및 모든 읽기 복제본을 고려해야 합니다. 더 많은 데이터베이스가 추가됨에 따라 생성된 스토리지 및 네트워크 트래픽을 추가적인 비용 기반 요소로 고려해야 할 것입니다.

> [!NOTE]
> 위의 예상치에 애플리케이션 계층에 대한 [ExpressRoute](../../expressroute/expressroute-introduction.md), [Azure App Gateway](../../application-gateway/overview.md), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) 또는 [App Service](../../app-service/overview.md) 비용은 포함되지 않습니다.
>
> 위의 가격은 언제든지 변경될 수 있으며 지역에 따라 달라질 수 있습니다.

#### <a name="application-implications"></a>애플리케이션 영향

Azure Database for MySQL으로 전환할 때 SSL(Secure Sockets Layer) 기반 통신으로의 전환이 애플리케이션에 대해 가장 중요한 변경 중 하나일 수 있습니다. SSL은 Azure Database for MySQL에서 기본적으로 사용하도록 설정되어 있으며, 온-프레미스 애플리케이션 및 데이터 워크로드는 SSL을 사용하여 MySQL에 연결하도록 설정되지 않을 가능성이 높습니다. 이 옵션을 사용하도록 설정하면 SSL 사용으로 인해 처리 오버헤드가 추가되므로 모니터링이 필요할 것입니다.

> [!NOTE]
> SSL은 기본적으로 사용하도록 설정되어 있지만 사용하지 않도록 설정하는 옵션이 제공됩니다.

[Azure Database for MySQL에 안전하게 연결하기 위한 사용자 애플리케이션의 SSL 연결 구성](../howto-configure-ssl.md)의 활동에 따라 이러한 강력한 인증 경로를 지원하도록 애플리케이션을 다시 구성합니다.

마지막으로 애플리케이션 연결 문자열에서 서버 이름을 수정하거나 새 Azure Database for MySQL 서버를 가리키도록 DNS를 전환합니다.

### <a name="wwi-scenario"></a>WWI 시나리오

WWI는 MySQL 데이터 자산에 대한 정보를 수집하여 평가를 시작했습니다. 다음을 컴파일할 수 있었습니다.

| 속성         | 원본      | Db 엔진 | 크기 | IOPS | 버전 | 소유자          | 가동 중지 시간 |
|--------------|-------------|-----------|------|------|---------|----------------|----------|
| **WwwDB**        | AWS(PaaS)  | InnoDB    | 1GB  | 150  | 5.7     | 마케팅 부서 | 1시간     |
| **BlogDB**       | AWS(PaaS)  | InnoDB    | 1GB  | 100  | 5.7     | 마케팅 부서 | 4시간    |
| **ConferenceDB** | 온-프레미스 | InnoDB    | 5GB  | 50   | 5.5     | 판매 부서     | 4시간    |
| **CustomerDB**   | 온-프레미스 |           | 10 GB | 75   | 5.5     | 판매 부서     | 2시간    |
| **SalesDB**      | 온-프레미스 | InnoDB    | 20GB | 75   | 5.5     | 판매 부서     | 1시간     |

허용 가능한 가동 중지 시간을 결정하기 위해 각 데이터베이스 소유자에게 연락했습니다. 계획 및 마이그레이션 방법은 허용 가능한 데이터베이스 가동 중지 시간을 기준으로 선택했습니다.

첫 번째 단계에서 WWI는 ConferenceDB 데이터베이스에만 중점을 두었습니다. 팀은 데이터 워크로드 마이그레이션을 계속하는 데 도움이 되는 마이그레이션 환경이 필요했습니다. 간단한 데이터베이스 구조와 허용 가능한 대규모 가동 중지 시간 때문에 ConferenceDB 데이터베이스를 선택했습니다. 데이터베이스가 마이그레이션되면 팀은 안전한 Azure 랜딩 존으로 애플리케이션을 마이그레이션하는 데 주력했습니다.

### <a name="assessment-checklist"></a>평가 검사 목록

  - 워크로드가 대상 시스템에서 성공적으로 실행되는지 테스트합니다.

  - 마이그레이션에 적합한 네트워킹 구성 요소가 준비되어 있는지 확인합니다.

  - 데이터 워크로드 리소스 요구 사항을 이해합니다.

  - 총 비용을 예측합니다.

  - 가동 중지 시간 요구 사항을 이해합니다.

  - 애플리케이션을 변경할 준비를 수행합니다.


> [!div class="nextstepaction"]
> [계획](./planning.md)