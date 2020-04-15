---
title: 장기 백업 보존
description: Azure SQL Database가 장기 보존 정책을 통해 최대 10년 동안 전체 데이터베이스 백업을 저장하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: d015eea21bcfa499d6751e024a882a7316b7f1a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380753"
---
# <a name="azure-sql-database-long-term-retention"></a>Azure SQL Database 장기 보존

여러 애플리케이션에서는 규정, 규정 준수 또는 기타 비즈니스를 목적으로 Azure SQL Database의 [자동 백업](sql-database-automated-backups.md)에서 제공하는 데이터베이스 백업을 7-35일 넘게 보존하도록 요구합니다. 장기 보존(LTR) 기능을 사용하면 지정된 SQL 데이터베이스 전체 백업을 Azure Blob 저장소에 저장할 수 있으며 읽기 액세스 지역 중복 저장소를 최대 10년 동안 저장할 수 있습니다. 그런 다음, 새 데이터베이스로 모든 백업을 복원할 수 있습니다. Azure 저장소 중복성에 대한 자세한 내용은 [Azure 저장소 중복을](../storage/common/storage-redundancy.md)참조하십시오. 

단일 데이터베이스 및 풀풀된 데이터베이스에 대해 장시간 보존을 사용할 수 있으며 Azure SQL Database 관리 형 인스턴스에 대한 공개 미리 보기가 제한되어 있습니다. 

> [!NOTE]
> SQL 에이전트 작업을 사용하여 35일이 초과된 LTR의 대안으로 [복사 전용 데이터베이스 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)을 예약할 수 있습니다.


## <a name="how-sql-database-long-term-retention-works"></a>SQL Database의 장기 보존 작동 방법

LTR(장기 백업 보존)은 [자동 생성](sql-database-automated-backups.md)되는 전체 데이터베이스 백업을 활용하여 PITR(지정 시간 복원)을 지원합니다. LTR 정책이 구성된 경우 이러한 백업은 장기 저장소를 위해 다른 Blob으로 복사됩니다. 복사본은 데이터베이스 워크로드에 성능에 영향을 미치지 않는 백그라운드 작업입니다. 각 SQL 데이터베이스에 대한 LTR 정책은 LTR 백업이 생성되는 빈도를 지정할 수도 있습니다.

LTR을 사용하려면 주간 백업 보존(W), 월별 백업 보존(M), 연간 백업 보존(Y) 및 연도(WeekOfYear)의 네 가지 매개 변수를 조합하여 정책을 정의할 수 있습니다. W를 지정하는 경우 매주 하나의 백업이 장기 스토리지에 복사됩니다. M을 지정하면 매월 첫 번째 백업이 장기 저장소에 복사됩니다. Y를 지정하는 경우 WeekOfYear로 지정된 주 동안 하나의 백업이 장기 스토리지에 복사됩니다. 지정된 WeekOfYear 정책이 구성된 과거에 있는 경우 첫 번째 LTR 백업이 다음 해에 만들어집니다. 각 백업은 LTR 백업을 만들 때 구성된 정책 매개 변수에 따라 장기 저장소에 보관됩니다.

> [!NOTE]
> LTR 정책의 변경 사항은 이후의 백업에만 적용됩니다. 예를 들어 주간 백업 보존(W), 월별 백업 보존(M) 또는 연간 백업 보존(Y)이 수정되면 새 보존 설정은 새 백업에만 적용됩니다. 기존 백업의 보존은 수정되지 않습니다. 보존 기간이 만료되기 전에 이전 LTR 백업을 삭제하려는 경우 [백업을 수동으로 삭제해야 합니다.](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups)
> 

LTR 정책의 예:

-  W=0, M=0, Y=5, WeekOfYear=3

   매년 세 번째 전체 백업은 5 년 동안 유지됩니다.
   
- W=0, M=3, Y=0

   매월 첫 번째 전체 백업은 3개월 동안 유지됩니다.

- W=12, M=0, Y=0

   각각의 매주 전체 백업이 12주 동안 유지됩니다.

- W=6, M=12, Y=10, WeekOfYear=16

   매주 전체 백업은 6주 동안 유지됩니다. 매월 첫 번째 전체 백업을 제외하고 12개월 동안 유지됩니다. 연간 16번째 주에서 수행된 전체 백업을 제외하고 10년 동안 유지됩니다. 

다음 표는 다음 정책에 대한 장기 백업의 주기 및 만료를 나타냅니다.

W=12주(84일), M=12개월(365일), Y=10년(3650일), WeekOfYear=15(4월 15일 이후 주)

   ![ltr 예제](./media/sql-database-long-term-retention/ltr-example.png)



위의 정책을 수정하고 W=0(주간 백업 없음)을 설정하면 백업 복사본의 흐름이 강조 표시된 날짜로 위의 표에 표시된 대로 변경됩니다. 이러한 백업을 유지하는 데 필요한 스토리지 용량은 적절하게 줄어듭니다. 

> [!IMPORTANT]
> 개별 LTR 백업의 타이밍은 Azure SQL Database에 의해 제어됩니다. LTR 백업을 수동으로 만들거나 백업 생성 타이밍을 제어할 수 없습니다. LTR 정책을 구성한 후 첫 번째 LTR 백업이 사용 가능한 백업 목록에 표시되기까지 최대 7일이 걸릴 수 있습니다.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>지역 복제 및 장기 백업 보존

비즈니스 연속성 솔루션으로 활성 지역 복제 또는 장애 조치 그룹을 사용하는 경우 최종 장애 복구를 준비하고 지역 보조 데이터베이스에서 동일한 LTR 정책을 구성해야 합니다. 백업이 보조 데이터베이스에서 생성되지 않기 때문에 LTR 저장소 비용이 증가하지 않습니다. 보조 데이터베이스가 주 데이터베이스가 되는 경우에만 백업이 생성됩니다. 장애 조치(failover)가 트리거되고 기본 백업이 보조 영역으로 이동할 때 LTR 백업의 중단되지 않은 생성을 보장합니다. 

> [!NOTE]
> 원래 기본 데이터베이스가 장애 조치(failover)를 일으킨 중단에서 복구되면 새 보조 데이터베이스가 됩니다. 따라서 백업 생성이 다시 시작되지 않고 기존 LTR 정책은 다시 주 데이터베이스가 될 때까지 적용되지 않습니다. 

## <a name="managed-instance-support"></a>Managed Instance 지원

Azure SQL Database 관리형 인스턴스에서 장기 백업 보존을 사용하면 다음과 같은 제한 사항이 있습니다.

- **제한된 공개 미리 보기** - 이 미리 보기는 EA 및 CSP 구독에서만 사용할 수 있으며 제한된 가용성에 따라 달라질 수 있습니다.  
- [**PowerShell 만**](sql-database-managed-instance-long-term-backup-retention-configure.md) - 현재 Azure 포털 지원이 없습니다. LtR은 PowerShell을 사용하여 활성화해야 합니다. 

등록을 요청하려면 **백업, 복원 및 비즈니스 연속성 / 장기 백업 보존**지원 항목 아래에 Azure 지원 [티켓을](https://azure.microsoft.com/support/create-ticket/) 만듭니다.


## <a name="configure-long-term-backup-retention"></a>장기 백업 보존 구성

Azure 포털 또는 PowerShell을 사용하여 장기 보존을 구성하는 방법에 대해 알아보려면 [Azure SQL Database 장기 백업 보존 관리를](sql-database-long-term-backup-retention-configure.md)참조하십시오.

## <a name="restore-database-from-ltr-backup"></a>LTR 백업에서 데이터베이스 복원

LTR 스토리지에서 데이터베이스를 복원하기 위해 해당 타임스탬프에 따라 특정 백업을 선택할 수 있습니다. 데이터베이스는 원본 데이터베이스와 동일한 구독 아래의 기존 서버로 복원될 수 있습니다. Azure 포털 또는 PowerShell을 사용하여 LTR 백업에서 데이터베이스를 복원하는 방법을 알아보려면 [Azure SQL Database 장기 백업 보존 관리를](sql-database-long-term-backup-retention-configure.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략에서 필수입니다. 다른 SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
