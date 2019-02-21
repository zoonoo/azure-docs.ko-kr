---
title: 최대 10년 동안 Azure SQL Database 백업 저장 | Microsoft Docs
description: Azure SQL Database에서 최대 10년 동안 전체 데이터베이스 백업을 저장하도록 지원하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 68bcddeee2cec1a77f20f8f470669f170fa50743
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992486"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>최대 10년 동안 Azure SQL Database 백업 저장

여러 애플리케이션에서는 규정, 규정 준수 또는 기타 비즈니스를 목적으로 Azure SQL Database의 [자동 백업](sql-database-automated-backups.md)에서 제공하는 데이터베이스 백업을 7-35일 넘게 보존하도록 요구합니다. LTR(장기 보존) 기능을 사용하여 [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) Blob Storage에 최대 10년 동안 지정된 SQL 데이터베이스 전체 백업을 저장할 수 있습니다. 그런 다음, 새 데이터베이스로 모든 백업을 복원할 수 있습니다.

> [!NOTE]
> 단일 데이터베이스와 풀링된 데이터베이스에 대해 LTR을 사용하도록 설정할 수 있습니다. Managed Instance의 인스턴스 데이터베이스에는 아직 LTR을 사용할 수 없습니다. SQL 에이전트 작업을 사용하여 35일이 초과된 LTR의 대안으로 [복사 전용 데이터베이스 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)을 예약할 수 있습니다.
> 

## <a name="how-sql-database-long-term-retention-works"></a>SQL Database의 장기 보존 작동 방법

LTR(장기 백업 보존)은 [자동 생성](sql-database-automated-backups.md)되는 전체 데이터베이스 백업을 활용하여 PITR(지정 시간 복원)을 지원합니다. LTR 정책이 구성된 경우 이러한 백업이 여러 저장소 Blob에 복사됩니다.
각 SQL 데이터베이스의 LTR 정책을 구성하고 장기 저장소 Blob에 백업을 복사해야 하는 빈도를 지정할 수 있습니다. 해당 유연성을 활성화하기 위해 네 개의 매개 변수(주간 백업 보존(W), 월간 백업 보존(M), 연간 백업 보존(Y), 연간 주(WeekOfYear)) 조합을 사용하여 정책을 정의할 수 있습니다. W를 지정하는 경우 매주 하나의 백업이 장기 저장소에 복사됩니다. M을 지정하는 경우 각 월의 첫 주 동안 하나의 백업이 장기 저장소에 복사됩니다. Y를 지정하는 경우 WeekOfYear로 지정된 주 동안 하나의 백업이 장기 저장소에 복사됩니다. 각 백업은 이러한 매개 변수에 의해 지정된 기간 동안 장기 저장소에 유지됩니다. 

예제:

-  W=0, M=0, Y=5, WeekOfYear=3

   매년 세 번째 전체 백업이 5년 동안 유지됩니다.
- W=0, M=3, Y=0

   매월 첫 번째 전체 백업이 3개월 동안 유지됩니다.

- W=12, M=0, Y=0

   각각의 매주 전체 백업이 12주 동안 유지됩니다.

- W=6, M=12, Y=10, WeekOfYear=16

   각각의 매주 전체 백업이 6주 동안 유지됩니다. 매월 첫 번째 전체 백업을 제외하고 12개월 동안 유지됩니다. 연간 16번째 주에서 수행된 전체 백업을 제외하고 10년 동안 유지됩니다. 

다음 표는 다음 정책에 대한 장기 백업의 주기 및 만료를 나타냅니다.

W=12주(84일), M=12개월(365일), Y=10년(3650일), WeekOfYear=15(4월 15일 이후 주)

   ![ltr 예제](./media/sql-database-long-term-retention/ltr-example.png)


 
위의 정책을 수정하고 W=0(주별 백업 없음)으로 설정하는 경우 백업 복사본의 주기는 강조 표시된 날짜까지 위의 표에 나와 있는 것처럼 변경됩니다. 이러한 백업을 유지하는 데 필요한 저장소 크기는 적절하게 줄어듭니다. 

> [!NOTE]
1. LTR 복사본은 Azure 저장소 서비스에서 생성되므로 복사 프로세스는 기존 데이터베이스에 성능 영향을 주지 않습니다.
2. 이 정책은 향후 백업에 적용됩니다. 예: 지정된 WeekOfYear가 정책이 구성된 지난 해인 경우 첫 번째 LTR 백업은 다음 해에 만들어집니다. 
3. LTR 저장소에서 데이터베이스를 복원하기 위해 해당 타임스탬프에 따라 특정 백업을 선택할 수 있습니다.   데이터베이스는 원본 데이터베이스와 동일한 구독 아래의 기존 서버로 복원될 수 있습니다. 
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>지역 복제 및 장기 백업 보존

활성 지역 복제 또는 장애 조치(failover) 그룹을 비즈니스 연속성 솔루션으로 사용하는 경우, 최종 장애 조치(failover)를 준비하고 지역 보조 데이터베이스에 동일한 LTR 정책을 구성해야 합니다. 이렇게 해도 백업이 보조 데이터베이스에서 생성되지 않으므로 LTR 저장소 비용이 증가하지 않습니다. 보조 데이터베이스가 주 데이터베이스가 되는 경우에만 백업이 생성됩니다. 이 방법으로 장애 조치(failover)가 트리거되고 주 데이터베이스가 보조 지역으로 이동될 때 LTR 백업이 중단 없이 생성되도록 합니다. 

> [!NOTE]
원본 주 데이터베이스는 장애 조치(failover)를 유발하는 중단에서 복구되면 새 보조 데이터베이스가 됩니다. 따라서 백업 생성이 다시 시작되지 않고 기존 LTR 정책은 다시 주 데이터베이스가 될 때까지 적용되지 않습니다. 
> 

## <a name="configure-long-term-backup-retention"></a>장기 백업 보존 구성

Azure Portal 또는 PowerShell을 사용하여 장기 보존을 구성하는 방법을 알아보려면 [장기 백업 보존 구성](sql-database-long-term-backup-retention-configure.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략에서 필수입니다. 다른 SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
