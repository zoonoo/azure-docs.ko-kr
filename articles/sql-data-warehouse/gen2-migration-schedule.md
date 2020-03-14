---
title: Gen2로 SQL 풀 마이그레이션
description: 기존 SQL 풀을 Gen2로 마이그레이션하기 위한 지침과 지역별 마이그레이션 일정.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 00180c1791e765240f3f8feac188b9250162408e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256706"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>Gen2로 SQL 풀 업그레이드

Microsoft는 SQL 풀 실행에 대 한 초급 수준의 비용을 줄이는 데 도움이 됩니다.  까다로운 쿼리를 처리할 수 있는 낮은 계산 계층이 이제 SQL 풀에서 사용할 수 있습니다. [Gen2에 대 한 전체 공지 낮은 계산 계층 지원](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)을 참조 하세요. 새 제품은 아래 표에 나와 있는 지역에서 제공 됩니다. 지원 되는 지역에서 기존 Gen1 SQL 풀은 다음 중 하나를 통해 Gen2로 업그레이드할 수 있습니다.

- **자동 업그레이드 프로세스는 다음과 같습니다.** 자동 업그레이드는 지역에서 서비스를 사용할 수 있게 되는 즉시 시작 되지 않습니다.  자동 업그레이드가 특정 지역에서 시작 되 면 선택한 유지 관리 일정에 따라 개별 데이터 웨어하우스 업그레이드가 수행 됩니다.
- [**Gen2로 자체 업그레이드:** ](#self-upgrade-to-gen2) Gen2에 대 한 자체 업그레이드를 수행 하 여 업그레이드 시기를 제어할 수 있습니다. 지역이 아직 지원 되지 않는 경우 복원 지점에서 지원 되는 지역의 Gen2 인스턴스로 직접 복원할 수 있습니다.

## <a name="automated-schedule-and-region-availability-table"></a>자동화 일정 및 지역 가용성 표

다음 표에서는 하위 Gen2 컴퓨팅 계층 지원 일정 및 자동 업그레이드 시작 일정을 지역별로 요약해서 보여 줍니다. 날짜는 변경될 수 있습니다. 해당 지역이 지원되는지 다시 확인하세요.

\*은 해당 지역의 특정 일정을 현재 사용할 수 없음을 나타냅니다.

| **지역** | **하위 Gen2 사용 가능** | **자동 업그레이드 시작** |
|:--- |:--- |:--- |
| 캐나다 동부 |2020 년 6 월 1 일 |2020 년 7 월 1 일 |
| 중국 동부 |\* |\* |
| 중국 북부 |\* |\* |
| 독일 중부 |\* |\* |
| 독일 중서부 |사용 가능 |5 월 1 일, 2020 |
| 인도 서부 |사용 가능 |5 월 1 일, 2020  |

## <a name="automatic-upgrade-process"></a>자동 업그레이드 프로세스

위의 가용성 차트에 따라 Gen1 인스턴스에 대해 자동화 된 업그레이드를 예약 합니다. SQL 풀의 가용성에 대 한 예기치 않은 중단을 방지 하기 위해 유지 관리 일정 동안 자동화 된 업그레이드가 예약 됩니다. 새 Gen1 인스턴스를 만드는 기능은 Gen2로 자동 업그레이드 하는 지역에서 사용할 수 없습니다. 자동 업그레이드가 완료 되 면 Gen1는 사용 되지 않습니다. 일정에 대한 자세한 내용은 [유지 관리 일정 보기](viewing-maintenance-schedule.md)를 참조하세요.

업그레이드 프로세스에는 SQL 풀을 다시 시작 하는 짧은 연결 (약 5 분)이 포함 됩니다.  SQL 풀을 다시 시작한 후에는 완전히 사용할 수 있습니다. 그러나 업그레이드 프로세스가 백그라운드에서 데이터 파일을 계속 업그레이드 하는 동안 성능이 저하 될 수 있습니다. 총 성능 저하 시간은 데이터 파일의 크기에 따라 달라집니다.

다시 시작한 후에 더 큰 SLO 및 리소스 클래스를 사용하는 모든 기본 columnstore 테이블에 [Alter Index rebuild](sql-data-warehouse-tables-index.md)를 실행하여 데이터 파일 업그레이드 프로세스를 신속하게 진행할 수도 있습니다.

> [!NOTE]
> Alter Index rebuild는 오프라인 작업이며, 다시 빌드가 완료될 때까지 테이블을 사용할 수 없습니다.

## <a name="self-upgrade-to-gen2"></a>Gen2로 자체 업그레이드

기존 Gen1 SQL 풀에서 다음 단계를 수행 하 여 자체 업그레이드를 선택할 수 있습니다. 자동 업그레이드를 선택 하는 경우 해당 지역에서 자동 업그레이드 프로세스가 시작 되기 전에 완료 해야 합니다. 이렇게 하면 자동 업그레이드에서 충돌을 일으키는 위험을 피할 수 있습니다.

자체 업그레이드를 수행할 때는 두 가지 옵션을 사용할 수 있습니다.  현재 SQL 풀을 현재 위치로 업그레이드 하거나 Gen1 SQL 풀을 Gen2 인스턴스로 복원할 수 있습니다.

- [내부 업그레이드](upgrade-to-latest-generation.md) -이 옵션은 기존 Gen1 SQL 풀을 Gen2로 업그레이드 합니다. 업그레이드 프로세스에는 SQL 풀을 다시 시작 하는 짧은 연결 (약 5 분)이 포함 됩니다.  SQL 풀을 다시 시작한 후에는 완전히 사용할 수 있습니다. 업그레이드 하는 동안 문제가 발생 하는 경우 [지원 요청](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) 을 열고 가능한 원인으로 "Gen2 upgrade"를 참조 합니다.
- [복원 지점에서 업그레이드](sql-data-warehouse-restore.md) -현재 Gen1 SQL 풀에 사용자 정의 복원 지점을 만든 다음 Gen2 인스턴스로 직접 복원 합니다. 기존 Gen1 SQL 풀은 그대로 유지 됩니다. 복원이 완료 되 면 Gen2 SQL 풀은 완전히 사용할 수 있습니다.  복원된 Gen2 인스턴스에서 모든 테스트 및 유효성 검사 프로세스를 실행한 후에는 원래 Gen1 인스턴스를 삭제할 수 있습니다.

   - 1 단계: Azure Portal에서 [사용자 정의 복원 지점을 만듭니다](sql-data-warehouse-restore-active-paused-dw.md).
   - 2 단계: 사용자 정의 복원 지점에서 복원 하는 경우 "성능 수준"을 기본 설정 Gen2 계층으로 설정 합니다.

업그레이드 프로세스가 백그라운드에서 데이터 파일을 계속 업그레이드하는 동안 일정 기간 동안 성능이 저하될 수 있습니다. 총 성능 저하 시간은 데이터 파일의 크기에 따라 달라집니다.

백그라운드 데이터 마이그레이션 프로세스를 더 신속히 처리하기 위해 더 큰 SLO 및 리소스 클래스에서 쿼리하는 모든 기본 columnstore 테이블에서 [Alter Index rebuild](sql-data-warehouse-tables-index.md)를 실행하여 데이터 이동을 즉시 실행할 수 있습니다.

> [!NOTE]
> Alter Index rebuild는 오프라인 작업이며, 다시 빌드가 완료될 때까지 테이블을 사용할 수 없습니다.

SQL 풀에 문제가 발생 하는 경우 [지원 요청](sql-data-warehouse-get-started-create-support-ticket.md) 을 만들고 가능한 원인으로 "Gen2 upgrade"를 참조 합니다.

자세한 내용은 [Gen2로 업그레이드](upgrade-to-latest-generation.md)를 참조하세요.

## <a name="migration-frequently-asked-questions"></a>마이그레이션 질문과 대답

**Q: Gen2 비용은 Gen1와 동일 하나요?**

- A: 예.

**Q: 업그레이드는 내 자동화 스크립트에 어떤 영향을 미칩니까?**

- A: 서비스 수준 목표를 참조 하는 모든 자동화 스크립트는 동등한 Gen2 일치 하도록 변경 해야 합니다.  자세한 내용은 [여기](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal)를 참조하세요.

**Q: 자동 업그레이드가 일반적으로 수행 하는 작업은 얼마 인가요?**

- A: 현재 위치에서 업그레이드 하거나 복원 지점에서 업그레이드할 수 있습니다.  
   - 현재 위치가 업그레이드 되 면 SQL 풀이 일시적으로 일시 중지 되 고 다시 시작 됩니다.  SQL 풀이 온라인 상태인 동안에는 백그라운드 프로세스가 계속 됩니다.  
   - 복원 지점까지 업그레이드하는 경우 업그레이드가 전체 복원 프로세스를 거치게 되므로 시간이 더 오래 걸립니다.

**Q: 자동 업그레이드에 소요 되는 기간은 얼마 인가요?**

- A: 업그레이드에 대 한 실제 가동 중지 시간은 서비스를 일시 중지 하 고 다시 시작 하는 데 걸리는 시간 (5 ~ 10 분)입니다. 잠깐 가동 중지된 후에 백그라운드 프로세스에서 스토리지 마이그레이션이 수행됩니다. 백그라운드 프로세스의 기간은 SQL 풀의 크기에 따라 달라 집니다.

**Q:이 자동 업그레이드는 언제 발생 하나요?**

- A: 유지 관리 일정 중에 선택한 유지 관리 일정을 활용하면 비즈니스 중단이 최소화됩니다.

**Q: 배경 업그레이드 프로세스가 중단 되는 것 처럼 보이는 경우 어떻게 해야 하나요?**

 - A: Columnstore 테이블의 인덱스를 해제 합니다. 이 작업 동안 테이블의 다시 인덱싱 작업은 오프라인에서 수행됩니다.

**Q: Gen2에는 Gen1에 대 한 서비스 수준 목표가 없는 경우 어떻게 되나요?**
- A: Gen1에서 W 600 또는 DW1200를 실행 하는 경우 DW1000c가 Gen2 보다 더 많은 메모리, 리소스 및 높은 성능을 제공 하므로 각각 DW500c 또는 Gen1를 사용 하는 것이 좋습니다.

**Q: 지역 백업을 사용 하지 않도록 설정할 수 있나요?**
- A: 아니요. 지역 백업은 지역에서 사용할 수 없게 되는 경우 SQL 풀 가용성을 유지 하는 엔터프라이즈 기능입니다. 문제가 더 있는 경우 [지원 요청](sql-data-warehouse-get-started-create-support-ticket.md)을 엽니다.

**Q: Gen1와 Gen2 간의 T-sql 구문에 차이가 있나요?**

- A: T-sql 언어 구문은 Gen1에서 Gen2로 변경 되지 않습니다.

**Q: Gen2은 유지 관리 기간을 지원 하나요?**

- A: 예.

**Q: 내 지역이 업그레이드 된 후 새 Gen1 인스턴스를 만들 수 있나요?**

- A: 아니요. 지역을 업그레이드한 후에는 Gen1 인스턴스를 새로 만들 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [업그레이드 단계](upgrade-to-latest-generation.md)
- [유지 관리 기간](maintenance-scheduling.md)
- [Resource Health 모니터링](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [마이그레이션 시작 전 검토 사항](upgrade-to-latest-generation.md#before-you-begin)
- [현재 위치 업그레이드 및 복원 지점에서 업그레이드](upgrade-to-latest-generation.md)
- [사용자 정의 복원 지점 만들기](sql-data-warehouse-restore-points.md)
- [Gen2로 복원하는 방법 알아보기](sql-data-warehouse-restore-active-paused-dw.md)
- [SQL Data Warehouse 지원 요청 열기](https://go.microsoft.com/fwlink/?linkid=857950)
