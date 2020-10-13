---
title: TDE 인증서 관리 되는 인스턴스 마이그레이션
description: 투명한 데이터 암호화를 사용 하 여 데이터베이스의 데이터베이스 암호화 키를 보호 하는 인증서를 Azure SQL Managed Instance로 마이그레이션
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: 08adfd7b69d580f6a231f13f9fb2793d828e16a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618168"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>TDE로 보호 되는 데이터베이스의 인증서를 Azure SQL Managed Instance로 마이그레이션
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[TDE (투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) )로 보호 되는 데이터베이스를 기본 복원 옵션을 사용 하 여 Azure SQL Managed Instance 마이그레이션하는 경우에는 데이터베이스를 복원 하기 전에 SQL Server 인스턴스의 해당 인증서를 마이그레이션해야 합니다. 이 문서에서는 Azure SQL Managed Instance로 인증서를 수동으로 마이그레이션하는 과정을 안내 합니다.

> [!div class="checklist"]
>
> * 인증서를 개인 정보 교환 (.pfx) 파일로 내보내기
> * 파일의 인증서를 base-64 문자열로 추출 합니다.
> * PowerShell cmdlet을 사용 하 여 업로드

TDE로 보호 되는 데이터베이스와 해당 인증서를 원활 하 게 마이그레이션하기 위해 완전히 관리 되는 서비스를 사용 하는 다른 옵션은 [Azure Database Migration Service를 사용 하 여 온-프레미스 데이터베이스를 AZURE SQL Managed Instance로 마이그레이션하는 방법](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조 하세요.

> [!IMPORTANT]
> 마이그레이션된 인증서는 TDE로 보호 되는 데이터베이스에만 복원 하는 데 사용 됩니다. 복원이 완료 된 후에도 마이그레이션된 인증서는 인스턴스에 대해 설정 된 TDE의 유형에 따라 서비스 관리 인증서 또는 키 자격 증명 모음의 비대칭 키와 같은 다른 보호기로 대체 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하려면 다음 필수 구성 요소가 필요합니다.

* 파일로 내보낸 인증서에 대한 액세스 권한이 있는 온-프레미스 서버나 기타 시스템에 설치된 [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) 명령줄 도구. Pvk2Pfx 도구는 자체 포함 된 명령줄 환경인 [Enterprise Windows 드라이버 키트](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk)의 일부입니다.
* [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) 버전 5.0 이상 설치.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 항목이 있는지 확인합니다.

* 모듈 [을 설치 하 고 업데이트](https://docs.microsoft.com/powershell/azure/install-az-ps)Azure PowerShell 합니다.
* [Az .sql module](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 Azure SQL Managed Instance에서 계속 지원 되지만 모든 향후 개발은 Az. Sql 모듈에 대 한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조하세요. Az module 및 AzureRM 모듈의 명령에 대 한 인수는 실질적으로 동일 합니다.

PowerShell에서 다음 명령을 실행 하 여 모듈을 설치/업데이트 합니다.

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>TDE 인증서를 .pfx 파일로 내보내기

인증서는 원본 SQL Server 인스턴스 또는 인증서 저장소 (있는 경우)에서 직접 내보낼 수 있습니다.

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>원본 SQL Server 인스턴스에서 인증서를 내보냅니다.

SQL Server Management Studio를 사용 하 여 인증서를 내보내고 .pfx 형식으로 변환 하려면 다음 단계를 수행 합니다. 일반 이름 *TDE_Cert* 및 *full_path* 는 인증서 및 파일 이름과 경로에 사용 됩니다. 이 항목은 실제 이름으로 대체해야 합니다.

1. SSMS에서 새 쿼리 창을 열고 원본 SQL Server 인스턴스에 연결 합니다.

1. 다음 스크립트를 사용 하 여 TDE로 보호 되는 데이터베이스를 나열 하 고 마이그레이션할 데이터베이스의 암호화를 보호 하는 인증서의 이름을 가져옵니다.

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![TDE 인증서 목록](./media/tde-certificate-migrate/onprem-certificate-list.png)

1. 다음 스크립트를 실행하여 공개 키 및 프라이빗 키 정보를 유지하면서 인증서를 파일 쌍(.cer 및 .pvk)으로 내보냅니다.

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![TDE 인증서 백업](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. Pvk2Pfx 도구를 사용 하 여 새로 만든 파일 쌍에서 .pfx 파일로 인증서 정보를 복사 하려면 PowerShell 콘솔을 사용 합니다.

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>인증서 저장소에서 인증서 내보내기

인증서가 SQL Server 로컬 컴퓨터 인증서 저장소에 보관 된 경우 다음 단계를 사용 하 여 내보낼 수 있습니다.

1. PowerShell 콘솔을 열고 다음 명령을 실행 하 여 Microsoft Management Console의 인증서 스냅인을 엽니다.

   ```cmd
   certlm
   ```

2. 인증서 MMC 스냅인에서 경로 개인 > 인증서를 확장 하 여 인증서 목록을 표시 합니다.

3. 인증서를 마우스 오른쪽 단추로 클릭 하 고 **내보내기**를 클릭 합니다.

4. 마법사에 따라 인증서와 개인 키를 .pfx 형식으로 내보냅니다.

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>Azure PowerShell cmdlet을 사용 하 여 Azure SQL Managed Instance에 인증서 업로드

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. PowerShell에서 준비 단계부터 시작합니다.

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. 모든 준비 단계가 완료 되 면 다음 명령을 실행 하 여 base-64로 인코딩된 인증서를 대상 관리 되는 인스턴스에 업로드 합니다.

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 *.pfx* 파일을 사용 하 여 [Azure key vault를 설정](/azure/key-vault/key-vault-manage-with-cli2) 해야 합니다.

1. PowerShell에서 준비 단계부터 시작합니다.

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. 모든 준비 단계가 완료 되 면 다음 명령을 실행 하 여 base-64로 인코딩된 인증서를 대상 관리 되는 인스턴스로 업로드 합니다.

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

이제 지정 된 관리 되는 인스턴스에서 인증서를 사용할 수 있으며 해당 TDE로 보호 되는 데이터베이스의 백업을 성공적으로 복원할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 투명한 데이터 암호화를 사용 하 여 데이터베이스의 암호화 키를 보호 하는 인증서를 온-프레미스 또는 IaaS SQL Server 인스턴스에서 Azure SQL Managed Instance로 마이그레이션하는 방법에 대해 알아보았습니다.

Azure SQL Managed Instance에 데이터베이스 백업을 복원 하는 방법을 알아보려면 [AZURE sql Managed Instance에 데이터베이스 백업 복원](restore-sample-database-quickstart.md) 을 참조 하세요.
