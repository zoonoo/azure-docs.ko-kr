---
title: 데이터베이스 백업 및 복원-Azure SQL Edge (미리 보기)
description: Azure SQL Edge (미리 보기)의 백업 및 복원 기능에 대해 알아봅니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 92a37babbcc0bbba3845267ca2eb0f95b9fceafa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84667865"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge-preview"></a>Azure SQL Edge에서 데이터베이스 백업 및 복원 (미리 보기) 

Azure SQL Edge는 Linux에서 최신 버전의 Microsoft SQL Server 데이터베이스 엔진을 기반으로 합니다. SQL Server on Linux 및 컨테이너에서 실행 되는 SQL Server에서 사용할 수 있는 것과 비슷한 백업 및 복원 데이터베이스 기능을 제공 합니다. 백업 및 복원 구성 요소는 Azure SQL Edge 데이터베이스에 저장 된 데이터를 보호 하기 위한 필수 보호 방법을 제공 합니다. 

치명적인 데이터 손실 위험을 최소화 하려면 정기적으로 데이터를 수정 하기 위해 데이터베이스를 정기적으로 백업 해야 합니다. 잘 계획된 백업 및 복원 전략은 다양한 오류로 인한 데이터 손실을 막아줍니다. 백업 집합을 복원한 후 데이터베이스를 복구 하 여 재해에 효과적으로 대응할 수 있도록 준비 하 여 전략을 테스트 합니다.

백업이 중요 한 이유에 대 한 자세한 내용은 [SQL Server 데이터베이스의 백업 및 복원](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/)을 참조 하세요.

Azure SQL Edge를 사용 하면 로컬 저장소와 Azure blob 모두에 백업 하 고 복원할 수 있습니다. 자세한 내용은 [Azure Blob storage를 사용 하 여 백업 및 복원 SQL Server](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) 및 [URL에 백업 SQL Server](/sql/relational-databases/backup-restore/sql-server-backup-to-url)을 참조 하세요.

## <a name="back-up-a-database-in-azure-sql-edge"></a>Azure SQL Edge에서 데이터베이스 백업

Azure SQL Edge는 SQL Server와 동일한 백업 유형을 지원 합니다. 전체 목록은 [백업 개요](/sql/relational-databases/backup-restore/backup-overview-sql-server/)를 참조 하세요.

> [!IMPORTANT] 
> Azure SQL Edge에서 만든 데이터베이스는 기본적으로 단순 복구 모델을 사용 합니다. 따라서 이러한 데이터베이스에 대해 로그 백업을 수행할 수 없습니다. 이 작업을 수행 해야 하는 경우 데이터베이스 복구 모델을 전체 복구 모델로 변경 하려면 관리자 권한이 필요 합니다. SQL Server에서 지 원하는 복구 모델의 전체 목록은 [복구 모델 개요](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov)를 참조 하세요.

### <a name="back-up-to-local-disk"></a>로컬 디스크에 백업

다음 예에서는 Transact-sql 명령을 사용 하 여 `BACKUP DATABASE` 컨테이너에 데이터베이스 백업을 만듭니다. 이 예에서는 백업 파일을 저장 하기 위해 *backup* 이라는 새 폴더를 만듭니다.

1. 백업 폴더를 만듭니다. Azure SQL Edge 컨테이너가 실행 되는 호스트에서이 명령을 실행 합니다. 다음 명령에서 **<AzureSQLEdge_Container_Name>** 를 배포의 Azure SQL Edge 컨테이너 이름으로 바꿉니다.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. SSMS (SQL Server Management Studio)를 사용 하거나 Azure Data Studio를 사용 하 여 Azure SQL Edge 인스턴스에 연결 합니다. 명령을 실행 `BACKUP DATABASE` 하 여 사용자 데이터베이스의 백업을 수행 합니다. 다음 예에서는 *IronOreSilicaPrediction* 데이터베이스의 백업을 수행 합니다.

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. 명령을 실행 한 후 데이터베이스 백업이 성공적으로 완료 되 면 SSMS 또는 Azure Data Studio의 결과 섹션에 다음과 비슷한 메시지가 표시 됩니다.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="back-up-to-url"></a>URL로 백업

Azure SQL Edge는 페이지 Blob 및 블록 Blob에 백업하는 것을 지원합니다. 자세한 내용은 [블록 blob 및 페이지 blob에 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob)을 참조 하세요. 다음 예제에서는 *IronOreSilicaPrediction* 데이터베이스를 블록 blob에 백업 합니다. 

1. 블록 blob에 백업을 구성 하려면 먼저 Azure SQL Edge에서 SQL Server 자격 증명을 만드는 데 사용할 수 있는 SAS (공유 액세스 서명) 토큰을 생성 합니다. 스크립트는 저장 된 액세스 정책과 연결 된 SAS를 만듭니다. 자세한 내용은 [공유 액세스 서명, 1 부: SAS 모델 이해](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)를 참조 하세요. 또한 스크립트는 SQL Server에 자격 증명을 만드는 데 필요한 T-SQL 명령을 작성합니다. 다음 스크립트는 저장소 계정이 있는 Azure 구독 및 백업용 저장소 컨테이너가 이미 있다고 가정 합니다.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    스크립트를 성공적으로 실행 한 후 `CREATE CREDENTIAL` 쿼리 도구에 명령을 복사 합니다. 그런 다음 SQL Server 인스턴스에 연결 하 고 명령을 실행 하 여 SAS를 사용 하 여 자격 증명을 만듭니다.

2. SSMS 또는 Azure Data Studio를 사용 하 여 Azure SQL Edge 인스턴스에 연결 하 고 이전 단계의 명령을 사용 하 여 자격 증명을 만듭니다. `CREATE CREDENTIAL`명령을 이전 단계의 실제 출력으로 바꾸어야 합니다.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. 다음 명령은 *IronOreSilicaPrediction* 데이터베이스를 Azure 스토리지 컨테이너에 백업합니다.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restore-a-database-in-azure-sql-edge"></a>Azure SQL Edge에서 데이터베이스 복원

Azure SQL Edge에서는 로컬 디스크, 네트워크 위치 또는 Azure Blob storage 계정에서 복원할 수 있습니다. SQL Server의 복원 및 복구에 대 한 자세한 내용은 [복원 및 복구 개요](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15)를 참조 하세요. SQL Server의 단순 복구 모델에 대 한 개요는 [전체 데이터베이스 복원 (단순 복구 모델)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15)을 참조 하세요.

### <a name="restore-from-a-local-disk"></a>로컬 디스크에서 복원

이 예제에서는 이전 예제에서 만든 *IronOreSilicaPrediction* 백업을 사용 합니다. 이제 다른 이름을 사용 하 여 새 데이터베이스로 복원 합니다.

1. 데이터베이스 백업 파일이 아직 컨테이너에 없는 경우 다음 명령을 사용 하 여 컨테이너에 파일을 복사할 수 있습니다. 다음 예에서는 백업 파일이 로컬 호스트에 있고/var/opt/mssql/backup 폴더에 *sql1*라는 Azure SQL Edge 컨테이너에 복사 되는 것으로 가정 합니다.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. SSMS 또는 Azure Data Studio를 사용 하 여 Azure SQL Edge 인스턴스에 연결 하 여 복원 명령을 실행 합니다. 다음 예제에서 **IronOrePredictDB** 는 **IronOreSilicaPrediction_2**새 데이터베이스를 만들기 위해 복원 됩니다.

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. 복원 명령을 실행 한 후 복원 작업에 성공 하면 출력 창에 다음과 비슷한 메시지가 표시 됩니다. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>URL에서 복원

Azure SQL Edge는 Azure Storage 계정에서 데이터베이스를 복원하는 것도 지원합니다. 블록 blob 또는 페이지 blob 백업에서 복원할 수 있습니다. 다음 예제에서는 블록 blob의 *IronOreSilicaPrediction_2020_04_16 .bak* 데이터베이스 백업 파일을 복원 하 여 *IronOreSilicaPrediction_3*데이터베이스를 만듭니다.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


