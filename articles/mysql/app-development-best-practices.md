---
title: 앱 개발 모범 사례-Azure Database for MySQL
description: Azure Database for MySQL를 사용 하 여 앱을 빌드할 때의 모범 사례에 대해 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 36f9cfa2369032351f6ed2948fc0c98c1648bcb6
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259254"
---
# <a name="best-practices-for-building-applications-with-azure-database-for-mysql"></a>Azure Database for MySQL를 사용 하 여 응용 프로그램을 빌드하기 위한 모범 사례 

응용 프로그램의 개발 시간을 줄일 수 있는 Azure Database for MySQL를 사용 하 여 클라우드 지원 응용 프로그램을 빌드하는 데 도움이 되는 몇 가지 모범 사례는 다음과 같습니다. 

## <a name="application-and-database-resource-configuration"></a>응용 프로그램 및 데이터베이스 리소스 구성

### <a name="application-and-database-in-the-same-region"></a>동일한 지역의 응용 프로그램 및 데이터베이스
Azure에서 응용 프로그램을 배포할 때 **모든 종속성이 동일한 지역에**  있는지 확인 합니다. 영역 또는 가용성 영역에 걸쳐 인스턴스를 분산 하는 경우 네트워크 대기 시간이 만들어지므로 응용 프로그램의 전반적인 성능에 영향을 줄 수 있습니다. 

### <a name="keep-your-mysql-server-secure"></a>MySQL 서버를 안전 하 게 유지
MySQL 서버를 안전 하 게 [보호](https://docs.microsoft.com/azure/mysql/concepts-security) 하 고 공개적으로 액세스할 수 없도록 구성 해야 합니다. 다음 옵션 중 하나를 사용 하 여 서버를 보호 합니다. 
- [방화벽 규칙](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) 또는
- [가상 네트워크](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) 또는 
- [Private Link](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

보안을 위해 항상 **SSL** 을 통해 mysql 서버에 연결 하 고 **tls 1.2**를 사용 하도록 mysql 서버 및 응용 프로그램을 구성 해야 합니다. [SSL/TLS를 구성 하는 방법을](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security)참조 하세요. 

### <a name="tune-your-server-parameters"></a>서버 매개 변수 조정
이러한 매개 변수를 튜닝 하는 읽기 작업이 많은 워크 로드의 경우에는 ' tmp_table_size 및 max_heap_table_size '를 통해 성능을 향상 시킬 수 있습니다. Tmp_table_size 및 max_heap_table_size에 필요한 값을 계산 하려면 총 연결당 메모리 값 및 기본 메모리를 확인 합니다. 서버의 총 메모리에 대 한 기본 메모리 계정과 결합 된 연결 별 메모리의 합계 (tmp_table_size 제외)입니다.

Tmp_table_size 및 max_heap_table_size의 최대 크기를 계산 하려면 다음 수식을 사용 합니다.

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> 총 메모리는 서버가 프로 비전 된 vCores 전체에 걸쳐 있는 총 메모리 양을 나타냅니다.  예를 들어 범용 2 vCore Azure Database for MySQL 서버에서 총 메모리는 5GB * 2가 됩니다.  각 계층의 메모리에 대 한 자세한 내용은 [가격 책정 계층](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) 설명서에서 찾을 수 있습니다.
> 기본 메모리는 MySQL이 서버 시작 시 초기화 하 고 할당 하는 query_cache_size 및 innodb_buffer_pool_size와 같은 메모리 변수를 나타냅니다.  Sort_buffer_size 및 join_buffer_size와 같이 연결 메모리는 쿼리를 필요로 하는 경우에만 할당 되는 메모리입니다.

### <a name="create-a-non-admin-user"></a>비관리자 사용자 만들기 
각 데이터베이스에 대해 비관리자 [사용자를 만듭니다](https://docs.microsoft.com/azure/mysql/howto-create-users) . 일반적으로 사용자 이름은 DB 이름으로 식별 됩니다.

### <a name="resetting-your-password"></a>암호 재설정
Azure Portal를 사용 하 여 MySQL server에 대 한 [암호를 다시 설정할](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) 수 있습니다. 

프로덕션 데이터베이스에 대 한 서버 암호를 다시 설정 하면 응용 프로그램을 종료할 수 있습니다. 응용 프로그램 최종 사용자에 대 한 영향을 최소화 하기 위해 사용량이 적은 시간에 프로덕션 워크 로드에 대 한 암호를 다시 설정 하는 것이 좋습니다.

## <a name="performance-and-resiliency"></a>성능 및 복원 력 
응용 프로그램의 성능 문제를 디버그 하는 데 사용할 수 있는 몇 가지 도구와 패턴은 다음과 같습니다.

### <a name="enable-slow-query-logs-identify-performance-issues"></a>느리게 쿼리 로그를 사용 하 여 성능 문제 식별
서버에서 [느리게 쿼리 로그](https://docs.microsoft.com/azure/mysql/concepts-server-logs) 및 [감사 로그](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) 를 사용 하도록 설정할 수 있습니다. 느리게 쿼리 로그를 분석 하면 문제 해결에 대 한 성능 병목 상태를 식별할 수 있습니다. 

감사 로그는 Azure Monitor 로그, Event Hubs 및 저장소 계정 에서도 Azure 진단 로그를 통해서도 사용할 수 있습니다. [쿼리 성능 문제를 해결 하는 방법을](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)참조 하세요.

### <a name="use-connection-pooling"></a>연결 풀링 사용
데이터베이스 연결을 관리 하면 전체적으로 응용 프로그램의 성능에 상당한 영향을 줄 수 있습니다. 성능 최적화를 위해 연결이 설정 된 횟수와 키 코드 경로에서 연결을 설정 하는 데 걸리는 시간을 줄여야 합니다.  [연결 풀링을](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) 사용 하 여 Azure Database for MySQL에 연결 하면 복원 력 및 성능을 향상 시킬 수 있습니다. 

연결 풀러 인 [Proxysql](https://proxysql.com/)은 연결을 관리 하는 데 효율적으로 사용할 수 있습니다. 연결 풀을 사용 하면 유휴 연결을 줄이고 기존 연결을 다시 사용 하 여 문제를 방지할 수 있습니다. 자세히 알아보려면 [ProxySQL을 설정 하는 방법](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) 을 참조 하세요. 

### <a name="retry-logic-to-handle-transient-errors"></a>일시적인 오류를 처리 하는 재시도 논리
응용 프로그램에서 데이터베이스에 대 한 연결이 일시적으로 삭제 되거나 손실 되는 [일시적인 오류가](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) 발생할 수 있습니다. 이러한 경우 서버는 1 ~ 2 번의 다시 시도 후 5-10 초 후에 실행 됩니다. 

다시 시도 하는 것이 좋은 패턴은 처음 다시 시도 하기 전에 5 초 동안 기다린 후 각 다시 60 시도를 수행 하는 것입니다. 응용 프로그램에서 작업에 실패 한 것으로 간주 하는 최대 다시 시도 횟수를 제한 하 여 더 자세히 조사할 수 있습니다. 자세히 알아보려면 [연결 오류 문제를 해결 하는 방법](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) 을 참조 하세요. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>장애 조치 (failover)를 완화 하기 위해 읽기 복제 사용
장애 조치 (failover) 시나리오에 대해 [데이터 복제](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) 를 사용할 수 있습니다. 읽기 복제본을 사용 하는 경우 마스터 서버와 복제 서버 간에 자동 장애 조치 (failover)가 수행 되지 않습니다. 복제는 비동기 이므로 마스터와 복제본 사이에 지연이 발생 하는 것을 알 수 있습니다. 네트워크 지연은 마스터 서버에서 실행 되는 워크 로드의 범위와 데이터 센터 간의 대기 시간 등의 다양 한 요인에 의해 영향을 받을 수 있습니다. 대부분의 경우 복제본 지연 시간 범위는 몇 초에서 몇 분 사이입니다.

## <a name="database-deployment"></a>데이터베이스 배포 

### <a name="configure-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>CI/CD 배포 파이프라인에서 MySQL 용 Azure database 작업 구성
경우에 따라 데이터베이스에 변경 내용을 배포 해야 합니다. 이러한 경우 [Azure 파이프라인](https://azure.microsoft.com/services/devops/pipelines/) 을 통해 CI (지속적인 통합) 및 CD (지속적인 업데이트)를 사용 하 고 [MySQL server](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) 에 대 한 작업을 사용 하 여 데이터베이스에 대해 사용자 지정 스크립트를 실행 하 여 데이터베이스를 업데이트할 수 있습니다.

### <a name="manual-database-deployment"></a>수동 데이터베이스 배포 
수동 데이터베이스 배포 중에는 가동 중지 시간을 최소화 하거나 실패 한 배포의 위험을 줄이기 위해 따라야 할 좋은 패턴은 다음과 같습니다. 

1. [Mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) 또는 [MySQL 워크 벤치](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html) 를 사용 하 여 새 데이터베이스에 프로덕션 데이터베이스의 복사본 만들기 
2. 새 데이터베이스를 새 스키마 변경 내용으로 업데이트 하거나 데이터베이스에 필요한 업데이트를 업데이트 합니다. 
3. 프로덕션 데이터베이스를 읽기 전용 상태로 전환 합니다. 배포가 완료 될 때까지 프로덕션 데이터베이스에 대 한 쓰기 작업이 없어야 합니다. 
4. 1 단계의 새로 업데이트 된 데이터베이스를 사용 하 여 응용 프로그램을 테스트 합니다.
5. 응용 프로그램 변경 내용을 배포 하 고 응용 프로그램이 현재 최신 업데이트가 있는 새 데이터베이스를 사용 하 고 있는지 확인 합니다. 
6. 변경 사항을 롤백할 수 있도록 이전 프로덕션 데이터베이스를 유지 합니다. 그런 다음, 이전 프로덕션 데이터베이스를 삭제 하거나 필요한 경우 Azure storage에서 내보내도록 평가할 수 있습니다. 

>[!NOTE]
>  - 응용 프로그램이 읽기 전용 상태로 전환 하지 못할 수 있는 전자 상거래 앱과 같은 경우에는 백업을 수행한 후 프로덕션 데이터베이스에 변경 내용을 직접 배포 합니다.  일부 사용자가 실패 한 요청을 경험할 수 있으므로 앱에 대 한 트래픽이 낮은 사용량이 적은 시간에 이러한 변경이 발생 하 여 영향을 minimze 합니다. 
>  - 응용 프로그램 코드가 실패 한 모든 요청을 처리 하는지 확인 합니다.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>MySQL 기본 메트릭을 사용 하 여 워크 로드가 메모리 내 임시 테이블 크기를 초과 하는지 확인 합니다.
읽기 작업이 많은 작업을 수행 하는 경우 MySQL 서버에 대해 실행 되는 쿼리는 메모리 내 임시 테이블 크기를 초과할 수 있습니다. 이로 인해 서버가 응용 프로그램의 성능에 영향을 주는 디스크에 임시 테이블을 기록 하도록 전환할 수 있습니다. 임시 테이블 크기를 초과 하 여 서버가 디스크에 쓰고 있는지 확인 하려면 다음 메트릭을 확인 합니다.

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
Created_tmp_disk_tables 메트릭은 디스크에 생성 된 테이블 수를 표시 하는 반면, created_tmp_table 메트릭은 워크 로드에 따라 메모리에 형성 되어야 하는 임시 테이블의 수를 알려 줍니다. 특정 쿼리 실행이 임시 테이블을 사용 하는지 확인 하려면 쿼리에서 설명을 실행 합니다. 임시 테이블을 사용 하 여 쿼리를 실행 하는 경우 ' 추가 ' 열의 세부 정보는 ' 사용 임시 '를 나타냅니다.

디스크로 분산 쿼리를 사용 하 여 워크 로드의 비율을 계산 하려면 아래 수식에서 메트릭 값을 사용 합니다.

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

이상적으로이 백분율은 25% 미만 이어야 합니다. 백분율이 25% 이상으로 표시 되는 경우 두 개의 서버 매개 변수를 수정 하는 것이 좋습니다 tmp_table_size 및 max_heap_table_si


## <a name="database-schema-and-queries"></a>데이터베이스 스키마 및 쿼리

데이터베이스 스키마와 쿼리를 빌드할 때 유의 해야 할 몇 가지 팁과 트릭은 다음과 같습니다.

### <a name="always-use-the-right-datatype-for-your--table-columns"></a>항상 테이블 열에 올바른 데이터 형식을 사용 합니다.
저장 하려는 데이터 형식에 따라 올바른 데이터 형식을 사용 하면 저장소를 최적화 하 고 잘못 된 데이터 형식으로 인해 발생할 수 있는 오류를 줄일 수 있습니다.

### <a name="use-indexes"></a>인덱스 사용
쿼리 속도가 느려지는 것을 방지 하기 위해 인덱스를 사용할 수 있습니다. 인덱스를 사용 하면 특정 열이 있는 행을 신속 하 게 찾을 수 있습니다. [MySQL에서 인덱스를 사용 하는 방법을](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html)참조 하세요.

### <a name="explain-your-select-queries"></a>SELECT 쿼리 설명
[설명을](https://dev.mysql.com/doc/refman/8.0/en/explain.html) 사용 하 여 MySQL에서 쿼리를 실행 하는 방법에 대 한 정보를 얻을 수 있습니다. 이를 통해 쿼리 병목 현상 또는 문제를 검색할 수 있습니다. [설명을 사용 하 여 쿼리 성능을 프로 파일링 하는 방법](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)을 참조 하세요.


