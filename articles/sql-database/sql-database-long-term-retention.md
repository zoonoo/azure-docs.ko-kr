---
title: "최대 10년 동안 Azure SQL Database 백업 저장 | Microsoft Docs"
description: "Azure SQL Database에서 최대 10년 동안 백업을 저장하도록 지원하는 방법에 대해 알아봅니다."
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/22/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 768a630e1652a48fa4478ec2c25173d536ea6c09
ms.lasthandoff: 03/23/2017


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>최대 10년 동안 Azure SQL Database 백업 저장
여러 응용 프로그램에서는 규정, 규정 준수 또는 기타 비즈니스를 목적으로 SQL Database의 [자동 백업](sql-database-automated-backups.md)에서 제공하는 전체 데이터베이스 자동 백업을 7-35일 넘게 보존하도록 요구합니다. **장기 백업 보존** 기능을 사용하면 최대 10년 동안 Azure Recovery Services 자격 증명 모음에서 Azure SQL Database 백업을 저장할 수 있습니다. 자격 증명 모음 당 최대 1000개의 데이터베이스를 저장할 수 있습니다. 자격 증명 모음에서 백업을 선택하여 새 데이터베이스로 복원할 수 있습니다.

> [!IMPORTANT]
> 장기 백업 보존 기간은 현재 미리 보기로 제공되며 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 미국 중부, 동아시아, 미국 동부, 미국 동부 2, 인도 중부, 인도 남부, 일본 동부, 일본 서부, 미국 중북부, 북유럽, 미국 중남부, 동남 아시아, 서유럽 및 미국 서부에서 사용할 수 있습니다.
>

> [!NOTE]
> 24시간 동안 자격 증명 모음 당 최대 200개의 데이터베이스를 사용할 수 있습니다. 따라서 이 제한의 영향을 최소화하려면 각 서버에 별도 자격 증명 모음을 사용하는 것이 좋습니다. 
> 
> 
## <a name="how-does-sql-database-long-term-backup-retention-work"></a>SQL Database 장기 백업 보존은 어떻게 작동하나요?

백업의 장기 백업 보존을 사용하면 Azure Recovery Services 자격 증명 모음과 Azure SQL Database 서버를 연결할 수 있습니다. 

* SQL Server를 만든 동일한 Azure 구독에서 동일한 지역 및 리소스 그룹에 자격 증명 모음을 만들어야 합니다. 
* 그런 다음 데이터베이스에 대한 보존 정책을 구성합니다. 정책으로 인해 전체 주간 데이터베이스 백업이 Recovery Services 자격 증명 모음에 복사되고 지정된 보존 기간(최대 10년) 동안 유지됩니다. 
* 그러면 이러한 백업에서 구독의 모든 서버에 있는 새 데이터베이스로 복원할 수 있습니다. 복사는 기존 백업의 Azure Storage에서 수행되고 기존 데이터베이스의 성능에 영향을 주지 않습니다.

> [!TIP]
> 자습서는 [Azure Portal을 사용하여 데이터 보호 및 복구를 위한 백업 및 복원 시작](sql-database-get-started-backup-recovery-portal.md) 또는 [PowerShell을 사용하여 데이터 보호 및 복구를 위한 백업 및 복원 시작](sql-database-get-started-backup-recovery-powershell.md)을 참조하세요.

## <a name="how-do-i-enable-long-term-backup-retention"></a>장기 백업 보존을 사용하려면 어떻게 해야 하나요?

데이터베이스에 장기 백업 보존을 구성하려면:

1. SQL Database 서버와 동일한 지역, 구독 및 리소스 그룹에서 Azure Recovery Services 자격 증명 모음을 만듭니다. 
2. 자격 증명 모음에 서버 등록
3. Azure Recovery Services 보호 정책 만들기
4. 장기 백업 보존해야 하는 데이터베이스에 보호 정책 적용

Azure Portal을 사용하여 Azure Recovery Services 자격 증명 모음에서 자동화된 백업을 장기 백업 보존에서 구성, 관리 및 복원하려면 [Azure Portal을 사용하여 장기 백업 보존 관리](sql-database-manage-long-term-backup-retention-portal.md)를 참조하세요. PowerShell을 사용하여 Azure Recovery Services에서 자동화된 백업을 장기 백업 보존에서 구성, 관리 및 복원하려면 [PowerShell을 사용하여 장기 백업 보존 관리](sql-database-manage-long-term-backup-retention-powershell.md)를 참조하세요.

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-backup-retention-feature"></a>장기 보존 기능과 함께 저장된 데이터베이스를 복원하려면 어떻게 해야 하나요?

장기 백업 보존 백업에서 복원하려면:

1. 백업이 저장된 자격 증명 모음 나열
2. 논리 서버에 매핑되는 컨테이너 나열
3. 데이터베이스에 매핑된 자격 증명 모음 내에서 데이터 원본 나열
4. 복원할 수 있는 복구 지점 나열
5. 복구 지점에서 구독 내의 대상 서버로 복원

Azure Portal을 사용하여 Azure Recovery Services 자격 증명 모음에서 자동화된 백업을 장기 백업 보존에서 구성, 관리 및 복원하려면 [Azure Portal을 사용하여 장기 백업 보존 관리](sql-database-manage-long-term-backup-retention-portal.md)를 참조하세요. PowerShell을 사용하여 Azure Recovery Services에서 자동화된 백업을 장기 백업 보존에서 구성, 관리 및 복원하려면 [PowerShell을 사용하여 장기 백업 보존 관리](sql-database-manage-long-term-backup-retention-powershell.md)를 참조하세요.

## <a name="how-much-does-long-term-backup-retention-cost"></a>장기 백업 보존 비용은 얼마인가요?

Azure SQL Database의 장기 백업 보존은 [Azure 백업 서비스 가격 책정 요금](https://azure.microsoft.com/pricing/details/backup/)에 따라 청구됩니다.

Azure SQL Database 서버를 자격 증명 모음에 등록한 후에 자격 증명 모음에 저장된 주별 백업에서 사용하는 총 저장소에 대한 요금이 청구됩니다.

## <a name="view-available-backups-stored-in-long-term-backup-retention"></a>장기 백업 보존에 저장된 사용 가능한 백업 보기

Azure Portal을 사용하여 Azure Recovery Services 자격 증명 모음에서 자동화된 백업을 장기 백업 보존에서 구성, 관리 및 복원하려면 [Azure Portal을 사용하여 장기 백업 보존 관리](sql-database-manage-long-term-backup-retention-portal.md)를 참조하세요. PowerShell을 사용하여 Azure Recovery Services에서 자동화된 백업을 장기 백업 보존에서 구성, 관리 및 복원하려면 [PowerShell을 사용하여 장기 백업 보존 관리](sql-database-manage-long-term-backup-retention-powershell.md)를 참조하세요.

## <a name="disabling-long-term-retention"></a>장기 보존 비활성화

Recovery Service는 제공된 보존 정책에 따라 백업 정리를 자동으로 처리합니다. 

* 자격 증명 모음에 특정 데이터베이스의 백업을 보내는 작업을 중지하려면 해당 데이터베이스에 대한 보존 정책을 제거합니다.
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> 자격 증명 모음에 이미 있는 백업은 영향을 받지 않습니다. 해당 보존 기간이 만료되면 Recovery Service에 의해 자동으로 삭제됩니다.


## <a name="removing-long-term-backup-retention-backups-from-the-azure-recovery-services-vault"></a>Azure Recovery Services 자격 증명 모음에서 장기 백업 보존 백업 제거

자격 증명 모음에서 장기 백업 보존 백업을 제거하려면 [장기 백업 보존 백업 삭제](sql-database-manage-long-term-backup-retention-powershell.md)를 참조하세요.

## <a name="long-term-backup-retention-faq"></a>장기 백업 보존 FAQ:

1. Q: 자격 증명 모음에서 특정 백업을 수동으로 삭제할 수 있나요?

    A: 이 시점에서는 안됩니다. 보존 기간이 만료되면 자격 증명 모음은 자동으로 백업을 정리합니다.
2. Q: 내 서버를 등록하여 둘 이상의 자격 증명 모음에 백업을 저장할 수 있나요?

    A: 아니요, 현재로서는 한 개의 자격 증명 모음에만 백업을 저장할 수 있습니다.
3. Q: 다른 구독에서 자격 증명 모음 및 서버를 보유할 수 있나요?

    A: 아니요, 현재 자격 증명 모음 및 서버는 모두 동일한 구독 및 리소스 그룹에 있어야 합니다.
4. Q: 내 서버의 지역이 아닌 다른 지역에서 만든 자격 증명 모음을 사용할 수 있나요?

    A: 아니요, 자격 증명 모음 및 서버는 복사 시간을 최소화하고 트래픽 요금을 방지하기 위해 동일한 지역에 있어야 합니다.
5. Q: 한 개의 자격 증명 모음에 저장할 수 있는 데이터베이스는 몇 개인가요?

    A: 현재 자격 증명 모음당 최대 1000개의 데이터베이스만 지원됩니다. 
6. Q. 구독 당 만들 수 있는 자격 증명 모음은 몇 개인가요?

    A. 구독당 최대 25개의 자격 증명 모음을 만들 수 있습니다.
7. Q. 자격 증명 모음 당 하루에 구성할 수 있는 데이터베이스 개수는 몇 개인가요?

    A. 자격 증명 모음당 하루에 200개의 데이터베이스를 설정할 수 있습니다.
8. Q: 장기 백업 보존은 탄력적 풀에서 작동하나요?

    A: 예. 풀에 있는 모든 데이터베이스는 보존 정책을 사용하여 구성할 수 있습니다.
9. Q: 백업이 생성되는 시간을 선택할 수 있나요?

    A: 아니요, SQL Database는 데이터베이스에 대한 성능 영향을 최소화하기 위해 백업 일정을 제어합니다.
10. Q: 내 데이터베이스에 TDE를 활성화했습니다. 자격 증명 모음과 함께 TDE를 사용할 수 있습니까? 

    A. 예, TDE를 사용할 수 있습니다. 원본 데이터베이스가 더 이상 존재하지 않는 경우더라도 자격 증명 모음에서 데이터베이스를 복원할 수 있습니다.
11. Q. 내 구독이 일시 중단된 경우 자격 증명 모음에 있는 백업은 어떻게 되나요? 

    A. 구독이 일시 중단된 경우 기존 데이터베이스와 백업을 유지하지만 새 백업은 자격 증명 모음에 복사되지 않습니다. 구독을 다시 활성화한 후에 서비스는 백업을 자격 증명 모음에 다시 복사하기 시작합니다. 사용자 자격 증명 모음은 구독이 일시 중단되기 전에 복사된 백업을 사용하여 복원 작업에 액세스할 수 있게 됩니다. 
12. Q: SQL Server를 다운로드/복원할 수 있도록 SQL Database 백업 파일에 액세스할 수 있나요?

    A: 아니요, 현재는 아닙니다.
13. Q: SQL 보존 정책 내에서 여러 일정(매일, 매주, 매월, 매년)이 있을 수 있나요?

    A: 아니요, 현재로는 Virtal Machine 백업에서만 사용할 수 있습니다.
14. Q. 활성 지역 복제 보조 데이터베이스에 장기 백업 보존을 설정하면 어떻게 되나요?

    A: 현재 복제본에 대한 백업은 지원되지 않으므로 보조 데이터베이스에 대한 장기 백업 보존 옵션은 없습니다. 하지만 활성 지역 복제 보조 데이터베이스에 장기 백업 보존을 설정하는 것이 중요한 이유는 다음과 같습니다.
    - 장애 조치가 발생하여 데이터베이스가 주가 되면 전체 백업을 수행하고 이 전체 백업이 자격 증명 모음에 업로드됩니다.
    - 보조 데이터베이스에 장기 백업 보존을 설정할 경우에 추가 비용은 없습니다.



## <a name="next-steps"></a>다음 단계
데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 다른 Azure SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.


