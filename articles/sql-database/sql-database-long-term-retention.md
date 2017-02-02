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
ms.date: 11/22/2016
ms.author: carlrab; sashan
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 8b13faf1f6cdac355cc4d22b825cc2362a50e8f9


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>최대 10년 동안 Azure SQL Database 백업 저장
여러 응용 프로그램에서는 규정, 규정 준수 또는 기타 비즈니스를 목적으로 SQL Database의 [자동 백업](sql-database-automated-backups.md)에서 제공하는 전체 데이터베이스 자동 백업을 7-35일 넘게 보존하도록 요구합니다.

**장기 백업 보존** 기능을 사용하면 최대 10년 동안 Azure Recovery Services 자격 증명 모음에서 Azure SQL Database 백업을 저장할 수 있습니다. 자격 증명 모음 당 최대 1000개의 데이터베이스를 저장할 수 있습니다. 자격 증명 모음에서 백업을 선택하여 새 데이터베이스로 복원할 수 있습니다.

> [!NOTE]
> 24시간 동안 자격 증명 모음 당 최대 200개의 데이터베이스를 사용할 수 있습니다. 따라서 이 제한의 영향을 최소화하려면 각 서버에 별도 자격 증명 모음을 사용하는 것이 좋습니다. 
> 
> 

## <a name="how-does-sql-database-long-term-retention-work"></a>SQL Database 장기 보존을 작동하려면 어떻게 해야 하나요?

백업의 장기 보존을 사용하면 Azure Recovery Services 자격 증명 모음과 Azure SQL Database 서버를 연결할 수 있습니다. 

* SQL Server를 만든 동일한 Azure 구독에서 동일한 지역 및 리소스 그룹에 자격 증명 모음을 만들어야 합니다. 
* 그런 다음 데이터베이스에 대한 보존 정책을 구성합니다. 정책으로 인해 전체 주간 데이터베이스 백업이 Recovery Services 자격 증명 모음에 복사되고 지정된 보존 기간(최대 10년) 동안 유지됩니다. 
* 그러면 이러한 백업에서 구독의 모든 서버에 있는 새 데이터베이스로 복원할 수 있습니다. 복사는 기존 백업의 Azure Storage에서 수행되고 기존 데이터베이스의 성능에 영향을 주지 않습니다.

## <a name="how-do-i-enable-long-term-retention"></a>장기 보존을 사용하려면 어떻게 해야 하나요?

데이터베이스에 장기 백업 보존을 구성하려면:

1. SQL Database 서버와 동일한 지역, 구독 및 리소스 그룹에서 Azure Recovery Services 자격 증명 모음을 만듭니다. 
2. 자격 증명 모음에 서버 등록
3. Azure Recovery Services 보호 정책 만들기
4. 장기 백업 보존해야 하는 데이터베이스에 보호 정책 적용

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-retention-feature"></a>장기 보존 기능과 함께 저장된 데이터베이스를 복원하려면 어떻게 해야 하나요?

장기 보존 백업에서 복원하려면:

1. 백업이 저장된 자격 증명 모음 나열
2. 논리 서버에 매핑되는 컨테이너 나열
3. 데이터베이스에 매핑된 자격 증명 모음 내에서 데이터 원본 나열
4. 복원할 수 있는 복구 지점 나열
5. 복구 지점에서 구독 내의 대상 서버로 복원

## <a name="how-much-does-long-term-retention-cost"></a>장기 보존 비용은 얼마인가요?

Azure SQL Database의 장기 보존은 [Azure 백업 서비스 가격 책정 요금](https://azure.microsoft.com/pricing/details/backup/)에 따라 청구됩니다.

Azure SQL Database 서버를 자격 증명 모음에 등록한 후에 자격 증명 모음에 저장된 주별 백업에서 사용하는 총 저장소에 대한 요금이 청구됩니다.

## <a name="configuring-long-term-retention-in-the-azure-portal"></a>Azure Portal에서 장기 보존 구성

Azure SQL Database 서버 블레이드에서 장기 보존을 구성하고 필요한 경우 Azure Recovery Services 자격 증명 모음을 만들 수 있습니다.

- Azure Recovery Services 자격 증명 모음에 자동화된 백업의 장기 보존을 구성하려면 [장기 백업 보존 구성](sql-database-configure-long-term-retention.md)을 참조하세요.
- 장기 보존에 있는 백업에서 데이터베이스를 복구하려면 [장기 보존에 있는 백업에서 복구](sql-database-restore-from-long-term-retention.md)를 참조하세요
- Azure 복구 서비스 자격 증명 모음에서 백업을 보려면 [장기 보존에서 백업을 보기](sql-database-view-backups-in-vault.md)를 참조하세요.

> [!TIP]
> 자습서는 [데이터 보호 및 복구를 위한 백업 및 복원 시작](sql-database-get-started-backup-recovery.md)을 참조하세요.
>

## <a name="configuring-long-term-retention-using-powershell"></a>PowerShell을 사용하여 장기 보존 구성

PowerShell을 사용하여 장기 보존을 구성하려면 다음 단계를 따르세요.
1. 복구 서비스 자격 증명 모음 만들기
   
   ```
   New-AzureRmResourceGroup -Name $ResourceGroupName –Location 'WestUS' 
   $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName $ResourceGroupName -Location 'WestUS' 
   Set-AzureRmRecoveryServicesBackupProperties   -BackupStorageRedundancy LocallyRedundant  -Vault $vault
   ```
2. 서버 내의 데이터베이스가 장기간 저장된 백업을 보유할 수 있도록 Azure SQL Database 서버를 자격 증명 모음에 등록합니다.
   
   ```
   Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName 'RG1' -ServerName 'Server1' –ResourceId $vault.Id
   ```
3. 백업을 저장하는 보존 정책을 만듭니다.
   
   ```
   #retrieve the default in-memory policy object for AzureSQLServer workload and set the retention period
   $RP1 = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase
   #Sets the retention value to two years
   $RP1.RetentionDurationType='Years'
   $RP1.RetentionCount=2
   #register the policy for use with any SQL database
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name 'SQLBackup1' –WorkloadType AzureSQLDatabase -retentionPolicy $RP1
   ```
4. 자격 증명 모음에 백업을 저장하려는 SQL Database에 장기 보존을 사용하도록 설정합니다.
   
   ```
   #for your database you can select any policy created in the vault with which your server is registered
   Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'enabled' -ResourceId $policy.Id
   ```
5. 자격 증명 모음과 연결된 서버를 나열합니다. 각 서버는 자격 증명 모음에서 특정 컨테이너와 연결됩니다. 다음 명령을 실행하여 등록된 서버를 나열할 수 있습니다.
   
   ```
   #each server has an associated container in the vault
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL   
   #each database has an associated backup item in the respective container
   Get-AzureRmRecoveryServicesBackupItem –container $container
   ```
6. 컨테이너에서 보존 정책을 포함하는 데이터베이스를 나열합니다. 각 데이터베이스는 해당 컨테이너에서 연결된 백업 항목을 보유합니다. 백업 항목 이름은 데이터베이스 이름에서 파생됩니다.
   
    ```
    #list the backup items in the container
    Get-AzureRmRecoveryServicesBackupItem –container $container
    ```

## <a name="restore-from-a-long-term-retention-backup"></a>장기 보존 백업에서 복원

다음 단계를 사용하여 Azure Recovery Service 자격 증명 모음에서 백업의 데이터베이스를 복원합니다.

1. SQL Server와 연결된 Recovery Service 컨테이너를 찾습니다.
   
   ```
   #the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -Name 'Sql;myresourcegroup;myserver'
   ```
2. 데이터베이스와 연결된 백업 항목을 찾습니다.
   
    ``` 
    #the following command finds the backup item associated with the database 'mydb'
    $item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name 'mydb' 
    ```
3. 복원하려는 백업을 찾습니다.
   
   ```
   #The following command lists the backups (also known as the “recovery points”) created in the specific time period.
   $RP=Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item –StartDate '2016-02-01' -EndDate '2016-02-20'
   ```
4. 복구 지점에서 새 Azure SQL Database로 복원합니다.
   
   ```
   #This command restores from a selected backup. If there are multiple recovery points in the specified range $RP[0] refers to the first one.
   Restore-AzureRMSqlDatabase –FromLongTermRetentionBackup –ResourceId $RP[0].ID TargetResourceGroupName 'RG2' -TargetServerName 'Server2' -TargetDatabaseName 'DB2' [-Edition <String>] [-ServiceObjectiveName <String>] [-ElasticPoolName <String>] [<CommonParameters>]
   ```

## <a name="disabling-long-term-retention"></a>장기 보존 비활성화

Recovery Service는 제공된 보존 정책에 따라 백업 정리를 자동으로 처리합니다. 

* 자격 증명 모음에 특정 데이터베이스의 백업을 보내는 작업을 중지하려면 해당 데이터베이스에 대한 보존 정책을 제거합니다.
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> 자격 증명 모음에 이미 있는 백업은 영향을 받지 않습니다. 해당 보존 기간이 만료되면 Recovery Service에 의해 자동으로 삭제됩니다.
> 
> 

## <a name="removing-backups-from-the-azure-recovery-services-vault"></a>Azure Recovery Services 자격 증명 모음에서 백업 제거

자격 증명 모음에서 수동으로 백업을 제거하려면:

1. 'myserver'의 자격 증명 모음에 있는 컨테이너 식별
   
    ```
    Set-AzureRMRecoveryServicesVaultContext -Vault $vault 
    $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -FriendlyName 'myserver'
    ```
2. 삭제할 백업 항목을 식별합니다.
   
    ``` 
    $item=Get-AzureRmRecoveryServicesBackupItem –container $container -Name 'mydb'
    ```
3. 백업 항목 삭제('mydb' 데이터베이스의 모든 백업)
   
    ```
    $job = Disable-AzureRmRecoveryServicesBackupProtection –item $item -Removerecoverypoints 
    Wait-AzureRmRecoveryServicesBackupJob $job
    ```
4. 'myserver'와 연결된 컨테이너 삭제
   
    ```
    Unregister-AzureRmRecoveryServicesBackupContainer –Container $container $container
    ```

## <a name="long-term-retention-faq"></a>장기 보존 FAQ:

1. Q: 자격 증명 모음에서 특정 백업을 수동으로 삭제할 수 있나요?
   A: 이 시점에서는 안됩니다. 보존 기간이 만료되면 자격 증명 모음은 자동으로 백업을 정리합니다.
2. Q: 내 서버를 등록하여 둘 이상의 자격 증명 모음에 백업을 저장할 수 있나요?
   A: 아니요, 현재로서는 한 개의 자격 증명 모음에만 백업을 저장할 수 있습니다.
3. Q: 다른 구독에서 자격 증명 모음 및 서버를 보유할 수 있나요?
   A: 아니요, 현재 자격 증명 모음 및 서버는 모두 동일한 구독 및 리소스 그룹에 있어야 합니다.
4. Q: 내 서버의 지역이 아닌 다른 지역에서 만든 자격 증명 모음을 사용할 수 있나요?
   A: 아니요, 자격 증명 모음 및 서버는 복사 시간을 최소화하고 트래픽 요금을 방지하기 위해 동일한 지역에 있어야 합니다.
5. Q: 한 개의 자격 증명 모음에 저장할 수 있는 데이터베이스는 몇 개인가요?
   A: 현재 자격 증명 모음 당 최대 1000개의 데이터베이스를 지원합니다. 
6. Q. 구독 당 만들 수 있는 자격 증명 모음은 몇 개인가요? A. 구독당 최대 25개의 자격 증명 모음을 만들 수 있습니다.
7. Q. 자격 증명 모음 당 하루에 구성할 수 있는 데이터베이스 개수는 몇 개인가요? A. 자격 증명 모음 당 하루에 200개의 데이터베이스를 설정할 수 있습니다.
8. Q: 장기 보존은 탄력적 풀에서 작동하나요?
   A: 예. 풀에 있는 모든 데이터베이스는 보존 정책을 사용하여 구성할 수 있습니다.
9. Q: 백업이 생성되는 시간을 선택할 수 있나요?
   A: 아니요, SQL Database는 데이터베이스에 대한 성능 영향을 최소화하기 위해 백업 일정을 제어합니다.
10. Q: 내 데이터베이스에 TDE를 활성화했습니다. 자격 증명 모음과 함께 TDE를 사용할 수 있습니까? A: 예, TDE를 사용할 수 있습니다. 원본 데이터베이스가 더 이상 존재하지 않는 경우더라도 자격 증명 모음에서 데이터베이스를 복원할 수 있습니다.
11. Q. 내 구독이 일시 중단된 경우 자격 증명 모음에 있는 백업은 어떻게 되나요? A. 구독이 일시 중단된 경우 기존 데이터베이스와 백업을 유지하지만 새 백업은 자격 증명 모음에 복사되지 않습니다. 구독을 다시 활성화한 후에 서비스는 백업을 자격 증명 모음에 다시 복사하기 시작합니다. 사용자 자격 증명 모음은 구독이 일시 중단되기 전에 복사된 백업을 사용하여 복원 작업에 액세스할 수 있게 됩니다. 
12. Q: SQL Server를 다운로드/복원할 수 있도록 SQL Database 백업 파일에 액세스할 수 있나요?
   A: 아니요, 현재는 아닙니다.
13. Q: SQL 보존 정책 내에서 여러 일정(매일, 매주, 매월, 매년)이 있을 수 있나요?
   A: 아니요, 현재로는 Virtal Machine 백업에서만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다.. 다른 Azure SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.




<!--HONumber=Dec16_HO2-->


