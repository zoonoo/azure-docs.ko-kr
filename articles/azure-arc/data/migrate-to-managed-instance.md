---
title: SQL Server에서 Azure Arc 사용 SQL Managed Instance로 데이터베이스 마이그레이션
description: SQL Server에서 Azure Arc 사용 SQL Managed Instance로 데이터베이스 마이그레이션
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939629"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>마이그레이션: Azure Arc 사용 SQL Managed Instance에 대 한 SQL Server

이 시나리오에서는 두 개의 서로 다른 백업 및 복원 방법을 통해 SQL Server 인스턴스에서 azure Arc의 Azure SQL 관리 되는 인스턴스로 데이터베이스를 마이그레이션하는 단계를 안내 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>Azure blob storage 사용 

Azure Arc 사용 SQL Managed Instance로 마이그레이션하려면 Azure blob storage를 사용 합니다.

이 메서드는로 백업 하 고 복원할 수 있는 임시 저장소 위치로 Azure Blob Storage를 사용 합니다.

### <a name="prerequisites"></a>사전 요구 사항

- [Azure Data Studio 설치](install-client-tools.md)
- [Azure Storage 탐색기 설치](https://azure.microsoft.com/features/storage-explorer/)
- Azure 구독

### <a name="step-1-provision-azure-blob-storage"></a>1 단계: Azure blob storage 프로 비전

1. [Azure Blob Storage 계정 만들기](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal) 에 설명 된 단계를 따릅니다.
1. 시작 Azure Storage 탐색기
1. [Azure에 로그인](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) 하 여 이전 단계에서 만든 blob 저장소에 액세스 합니다.
1. Blob storage 계정을 마우스 오른쪽 단추로 클릭 하 고 **Blob 컨테이너 만들기** 를 선택 하 여 백업 파일이 저장 될 새 컨테이너를 만듭니다.

### <a name="step-2-get-storage-blob-credentials"></a>2 단계: 저장소 blob 자격 증명 가져오기

1. Azure Storage 탐색기에서 방금 만든 blob 컨테이너를 마우스 오른쪽 단추로 클릭 하 고 **공유 액세스 서명 가져오기** 를 선택 합니다.

1. **읽기**, **쓰기** 및 **목록** 선택

1. **만들기**를 선택합니다.

   이 화면에서 URI 및 쿼리 문자열을 기록해 둡니다. 이러한 작업은 이후 단계에서 필요 합니다. **복사** 단추를 클릭 하 여 메모장/OneNote 등에 저장 합니다.

1. **공유 액세스 서명** 창을 닫습니다.

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>3 단계: Azure Blob Storage 데이터베이스 파일 백업

이 단계에서는 원본 SQL Server에 연결 하 고 SQL Managed Instance-Azure Arc로 마이그레이션할 데이터베이스의 백업 파일을 만듭니다.

1. 시작 Azure Data Studio
1. SQL Managed Instance로 마이그레이션하려는 데이터베이스가 있는 SQL Server 인스턴스에 연결-Azure Arc
1. 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **새 쿼리** 를 선택 합니다.
1. `<...>`이전 단계에서 공유 액세스 서명의 정보를 사용 하 여에 표시 된 자리 표시자를 대체 하는 다음 형식으로 쿼리를 준비 합니다.  값을 대체 한 후 쿼리를 실행 합니다.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. 마찬가지로 다음과 같이 **BACKUP DATABASE** 명령을 준비 하 여 blob 컨테이너에 대 한 백업 파일을 만듭니다.  값을 대체 한 후 쿼리를 실행 합니다.

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Azure Storage 탐색기를 열고 이전 단계에서 만든 백업 파일이 Blob 컨테이너에 표시 되는지 확인 합니다.

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>4 단계: Azure blob storage에서 SQL Managed Instance로 데이터베이스 복원-Azure Arc

1. Azure Data Studio에서 로그인 하 고 SQL Managed Instance-Azure Arc에 연결 합니다.
1. **시스템 데이터베이스**를 확장 하 고 **master** 데이터베이스를 마우스 오른쪽 단추로 클릭 한 다음 **새 쿼리**를 선택 합니다.
1. 쿼리 편집기 창에서 이전 단계에서 동일한 쿼리를 준비 하 고 실행 하 여 자격 증명을 만듭니다.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. 다음 명령을 준비 하 고 실행 하 여 백업 파일을 읽을 수 있고 그대로 유지 하는지 확인 합니다.

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. 다음과 같이 **데이터베이스 복원** 명령을 준비 하 고 실행 하 여 SQL Managed Instance의 데이터베이스로 백업 파일을 복원 합니다.-Azure Arc

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

URL에 백업에 대 한 자세한 내용은 여기를 참조 하세요.

[URL에 백업 문서](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[SQL Server Management Studio를 사용 하 여 URL에 백업 (SSMS)](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>방법 2: kubectl를 사용 하 여 azure SQL Managed Instance azure Arc pod에 백업 파일 복사

이 메서드는 메서드를 통해 만든 백업 파일을 가져온 다음 Azure SQL 관리 되는 인스턴스 pod의 로컬 저장소로 복사 하 여 Windows 또는 Linux에서 일반적인 파일 시스템에서와 같은 방식으로 복원할 수 있도록 하는 방법을 보여 줍니다. 이 시나리오에서는 명령을 사용 하 여 `kubectl cp` 한 곳에서 pod의 파일 시스템으로 파일을 복사 합니다.

### <a name="prerequisites"></a>사전 요구 사항

- Kubectl를 설치 하 고 구성 하 여 Azure Arc data services가 배포 된 Kubernetes 클러스터를 가리키도록 합니다.
- Azure Data Studio 또는 SQL Server Management Server와 같은 도구를 설치 하 고 백업 파일을 만들거나 기존 .bak 파일을 로컬 파일 시스템에 이미 만든 SQL Server에 연결 합니다.

### <a name="step-1-backup-the-database-if-you-havent-already"></a>1 단계: 아직 데이터베이스를 백업 하지 않은 경우 데이터베이스를 백업 합니다.

디스크에 대 한 일반적인 SQL Server 백업 처럼 SQL Server 데이터베이스를 로컬 파일 경로에 백업 합니다.

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>2 단계: pod의 파일 시스템에 백업 파일 복사

Sql 인스턴스가 배포 되는 pod의 이름을 찾습니다. 일반적으로 다음과 같습니다. `pod/<sqlinstancename>-0`

다음을 실행 하 여 모든 pod 목록을 가져옵니다.

 ```console
kubectl get pods -n <namespace of data controller>
```

예제:

백업 파일을 로컬 저장소에서 클러스터의 sql pod로 복사 합니다.

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>3 단계: 데이터베이스 복원

복원 명령을 준비 하 고 실행 하 여 Azure SQL 관리 되는 인스턴스로 백업 파일 복원-Azure Arc

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

예제:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>다음 단계

[Azure Arc 사용 SQL Managed Instance의 기능 및 기능에 대해 자세히 알아보세요.](managed-instance-features.md)

[데이터 컨트롤러를 만들기 시작](create-data-controller.md)

[데이터 컨트롤러가 이미 만들어져 있나요? Azure Arc 사용 SQL Managed Instance 만들기](create-sql-managed-instance.md)