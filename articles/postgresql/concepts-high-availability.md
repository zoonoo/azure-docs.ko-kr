---
title: 고가용성 - Azure Database for PostgreSQL - 단일 서버
description: 이 문서에서는 Azure Database for PostgreSQL - 단일 서버의 고가용성에 대한 정보를 제공합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/15/2020
ms.openlocfilehash: aa9f38b2cefa60a0c3341c1317cf45fbcb735301
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92485446"
---
# <a name="high-availability-in-azure-database-for-postgresql--single-server"></a>Azure Database for PostgreSQL - 단일 서버의 고가용성
Azure Database for PostgreSQL – 단일 서버 서비스는 재정적으로 지원되는 [99.99%](https://azure.microsoft.com/support/legal/sla/postgresql) 작동 시간 SLA(서비스 수준 계약)로 보장되는 높은 수준의 가용성을 제공합니다. Azure Database for PostgreSQL은 사용자가 시작하는 컴퓨팅 스케일링 작업 같은 계획된 이벤트와 하드웨어, 소프트웨어 또는 네트워크 장애 발생과 같은 계획되지 않은 이벤트 중에 고가용성을 제공합니다. Azure Database for PostgreSQL은 대부분의 위험 상황으로부터 신속하게 복구할 수 있음으로 이 서비스를 사용할 때는 애플리케이션 가동 중지 시간이 거의 없습니다.

Azure Database for PostgreSQL은 높은 작동 시간이 필요한 중요 업무용 데이터베이스를 실행하는 데 적합합니다. Azure 아키텍처를 기반으로 구축된 이 서비스에는 계획되거나 계획되지 않은 중단으로 인한 데이터베이스 가동 중지 시간을 완화하는 고가용성, 중복성 및 복원력 기능이 내재되어 있으며, 사용자가 추가 구성 요소를 구성할 필요가 없습니다. 

## <a name="components-in-azure-database-for-postgresql--single-server"></a>Azure Database for PostgreSQL – 단일 서버의 구성 요소

| **구성 요소** | **설명**|
| ------------ | ----------- |
| <b>PostgreSQL 데이터베이스 서버 | Azure Database for PostgreSQL은 데이터베이스 서버의 보안, 격리, 리소스 보호 및 빠른 다시 시작 기능을 제공합니다. 이러한 기능은 가동 중단 후 몇 초 후에 스케일링 및 데이터베이스 서버 복구 작업 등과 같은 작업이 쉽게 수행되도록 합니다. <br/> 데이터베이스 서버에서의 데이터 수정은 일반적으로 데이터베이스 트랜잭션 컨텍스트에서 발생합니다. 모든 데이터베이스 변경 내용은 데이터베이스 서버에 연결된 Azure Storage에서 WAL(미리 쓰기 로그) 형식으로 동기적으로 기록됩니다. 데이터베이스 [검사점](https://www.postgresql.org/docs/11/sql-checkpoint.html) 프로세스 중에 데이터베이스 서버 메모리의 데이터 페이지도 스토리지로 플러시됩니다. |
| <b>원격 스토리지 | 모든 PostgreSQL 물리적 데이터 파일 및 WAL 파일은 데이터 중복성, 가용성, 안정성을 보장하기 위해 한 지역 내에 3개의 데이터 복사본을 저장하도록 설계된 Azure Storage에 저장됩니다. 스토리지 계층은 데이터베이스 서버와도 독립적입니다. 오류가 발생한 데이터베이스 서버에서 스토리지 계층을 분리하고 몇 초 내에 새 데이터베이스 서버에 다시 연결할 수 있습니다. 또한 Azure Storage는 스토리지 오류를 지속적으로 모니터링합니다. 블록 손상이 검색되면 새 스토리지 복사본을 인스턴스화하여 자동으로 수정됩니다. |
| <b>게이트웨이 | 게이트웨이는 데이터베이스 프록시 역할을 하며, 모든 클라이언트 연결을 데이터베이스 서버로 라우팅합니다. |

## <a name="planned-downtime-mitigation"></a>계획된 가동 중지 시간 완화
Azure Database for PostgreSQL은 계획된 가동 중지 시간 동안 고가용성을 제공하도록 설계되었습니다. 

:::image type="content" source="./media/concepts-high-availability/azure-postgresql-elastic-scaling.png" alt-text=" Azure PostgreSQL에서 탄력적 스케일링 보기":::

1. 몇 초 안에 PostgreSQL 데이터베이스 서버를 스케일 업하고 스케일 다운합니다.
2. 클라이언트를 라우팅하기 위한 프록시 역할을 하는 게이트웨이가 적절한 데이터베이스 서버에 연결합니다.
3. 스토리지 스케일 업은 가동 중지 시간 없이 수행할 수 있습니다. 원격 스토리지가 장애 조치(failover) 후 빠른 분리/다시 연결을 사용하도록 설정합니다.
다음은 몇 가지 계획된 유지 관리 시나리오입니다.

| **시나리오** | **설명**|
| ------------ | ----------- |
| <b>컴퓨팅 스케일 업/다운 | 사용자가 컴퓨팅 스케일 업/다운 작업을 수행하면 스케일링된 컴퓨팅 구성을 사용하여 새 데이터베이스 서버가 프로비저닝됩니다. 기존 데이터베이스 서버에서는 활성 검사점을 완료할 수 있고, 클라이언트 연결이 드레이닝되며, 커밋되지 않은 트랜잭션은 취소된 후 종료됩니다. 그런 다음 이전 데이터베이스 서버에서 스토리지가 분리되고 새 데이터베이스 서버에 연결됩니다. 클라이언트 애플리케이션에서 연결을 다시 시도하거나 새 연결을 시도하는 경우 게이트웨이는 새 데이터베이스 서버로 연결 요청을 보냅니다.|
| <b>스토리지 스케일 업 | 스토리지 스케일 업은 온라인 작업이며 데이터베이스 서버를 중단하지 않습니다.|
| <b>새 소프트웨어 배포(Azure) | 새 기능 출시 또는 버그 수정은 서비스의 계획된 유지 관리의 일부로 자동으로 이루어집니다. 자세한 내용은 [문서](./concepts-monitoring.md#planned-maintenance-notification) 및 해당 [포털](https://aka.ms/servicehealthpm)을 참조하세요.|
| <b>부 버전 업그레이드 | Azure Database for PostgreSQL은 Azure에서 확인된 부 버전으로 데이터베이스 서버를 자동으로 패치합니다. 이러한 작업은 서비스의 계획된 유지 관리 중에 수행됩니다. 이러한 작업은 몇 초 이내의 짧은 가동 중지 시간 내에 발생하며, 새로운 부 버전으로 데이터베이스 서버가 자동으로 다시 시작됩니다. 자세한 내용은 [문서](./concepts-monitoring.md#planned-maintenance-notification) 및 해당 [포털](https://aka.ms/servicehealthpm)을 참조하세요.|


##  <a name="unplanned-downtime-mitigation"></a>계획되지 않은 가동 중지 시간 완화

계획되지 않은 가동 중지 시간은 기본 하드웨어 결함, 네트워킹 문제 및 소프트웨어 버그와 같은 예상되지 않은 오류의 결과로 발생할 수 있습니다. 데이터베이스 서버가 예기치 않게 작동 중단되면 새 데이터베이스 서버가 몇 초 안에 자동으로 프로비저닝됩니다. 원격 스토리지는 새 데이터베이스 서버에 자동으로 연결됩니다. PostgreSQL 엔진은 WAL 및 데이터베이스 파일을 사용하여 복구 작업을 수행하고, 클라이언트가 연결할 수 있도록 데이터베이스 서버를 엽니다. 커밋되지 않은 트랜잭션은 손실되며 애플리케이션에서 다시 시도해야 합니다. 계획되지 않은 가동 중지 시간은 피할 수 없지만 Azure Database for PostgreSQL은 작업자의 개입 없이 데이터베이스 서버 및 스토리지 계층 모두에서 복구 작업을 자동으로 수행하여 가동 중지 시간을 완화합니다. 


:::image type="content" source="./media/concepts-high-availability/azure-postgresql-built-in-high-availability.png" alt-text="Azure PostgreSQL의 고가용성 보기":::

1. 빠른 스케일링 기능을 갖춘 Azure PostgreSQL 서버
2. 적절한 데이터베이스 서버로 클라이언트 연결을 라우팅하기 위한 프록시 역할을 하는 게이트웨이
3. 안정성, 가용성 및 중복성을 위한 세 가지 복사본이 있는 Azure Storage
4. 또한 원격 스토리지가 장애 조치(failover) 후 빠른 분리/다시 연결을 사용하도록 설정함
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>예기치 않은 가동 중지 시간: 오류 시나리오 및 서비스 복구
다음은 몇 가지 오류 시나리오와 Azure Database for PostgreSQL이 자동으로 복구하는 방법입니다.

| **시나리오** | **자동 복구** |
| ---------- | ---------- |
| <B>데이터베이스 서버 오류 | 기본 하드웨어 결함으로 인해 데이터베이스 서버가 가동 중지되면 활성 연결이 삭제되고 모든 전송 중인 트랜잭션이 중단됩니다. 새 데이터베이스 서버가 자동으로 배포되고 원격 데이터 스토리지가 새 데이터베이스 서버에 연결됩니다. 데이터베이스 복구가 완료된 후 클라이언트는 게이트웨이를 통해 새 데이터베이스 서버에 연결할 수 있습니다. <br /> <br /> RTO(복구 시간 목표)는 데이터베이스 서버 시작 프로세스 중 수행해야 하는 큰 트랜잭션 및 복구 작업의 양과 같은 장애가 발생한 시점의 작업을 포함하여 여러 요소들에 따라 달라집니다. <br /> <br /> PostgreSQL 데이터베이스를 사용하는 애플리케이션은 삭제된 연결과 실패한 트랜잭션을 검색하고 다시 시도하도록 빌드되어야 합니다.  애플리케이션에서 다시 시도할 때 게이트웨이는 새로 만든 데이터베이스 서버로 연결을 투명하게 리디렉션합니다. |
| <B>스토리지 오류 | 디스크 오류 또는 물리적 블록 손상과 같은 스토리지 관련 문제는 애플리케이션에 영향을 주지 않습니다. 데이터가 3개 복사본에 저장되므로 남은 스토리지에서 데이터 복사본이 제공됩니다. 블록 손상은 자동으로 수정됩니다. 데이터 복사본이 손실된 경우 새로운 데이터 복사본이 자동으로 생성됩니다. |

다음은 복구하려면 사용자 작업이 필요한 몇 가지 오류 시나리오입니다.

| **시나리오** | **복구 계획** |
| ---------- | ---------- |
| <b> 지역 오류 | 지역 오류는 드문 이벤트입니다. 그러나 지역 장애로부터 보호해야 하는 경우 DR(재해 복구)를 위해 다른 지역에 하나 이상의 읽기 복제본을 구성할 수 있습니다. (자세한 내용은 읽기 복제본 만들기 및 관리에 대한 [이 문서](./howto-read-replicas-portal.md)를 참조하세요.) 지역 수준 오류가 발생할 경우 다른 지역에 구성된 읽기 복제본을 프로덕션 데이터베이스 서버로 수동으로 승격할 수 있습니다. |
| <b> 논리적/사용자 오류 | 실수로 삭제된 테이블 또는 잘못 업데이트된 데이터와 같은 사용자 오류로부터 복구하려면 오류가 발생하기 바로 전의 시간으로 데이터를 복원 및 복구하는 [PITR(지정 시간 복구)](./concepts-backup.md)가 수행됩니다.<br> <br>  데이터베이스 서버에 있는 모든 데이터베이스 대신 데이터베이스의 일부 또는 특정 테이블만 복원하려면 새 인스턴스에서 데이터베이스 서버를 복원하고 [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)를 통해 테이블을 내보낸 후 [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html)를 사용하여 테이블을 데이터베이스로 복원하면 됩니다. |



## <a name="summary"></a>요약

Azure Database for PostgreSQL은 데이터베이스 서버 빠른 시작 기능, 중복 스토리지, 게이트웨이에서의 효율적인 라우팅을 제공합니다. 추가 데이터 보호를 위해 지역에서 복제되도록 백업을 구성하고 다른 지역에 하나 이상의 읽기 복제본을 배포할 수도 있습니다. 내재된 고가용성 기능을 통해 Azure Database for PostgreSQL은 가장 일반적인 중단으로부터 데이터베이스를 보호하고, 업계 최고 수준의 재정 지원 [99.99% 가동 시간 SLA](https://azure.microsoft.com/support/legal/sla/postgresql)를 제공합니다. Azure는 이러한 모든 가용성 및 안정성 기능을 통해 중요 업무용 애플리케이션을 실행하는 데 이상적인 플랫폼이 될 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Azure 지역](../availability-zones/az-overview.md)에 대해 자세히 알아보기
- [일시적인 연결 오류 처리](concepts-connectivity.md)에 대해 알아보기
- [읽기 복제본을 사용하여 데이터를 복제하는](howto-read-replicas-portal.md) 방법에 대해 알아보기