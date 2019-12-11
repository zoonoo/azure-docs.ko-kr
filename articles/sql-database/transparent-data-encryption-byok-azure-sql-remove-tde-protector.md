---
title: TDE 보호기 제거-PowerShell
description: BYOK (사용자 고유 키) 지원을 통해 TDE를 사용 하 여 Azure SQL Database 또는 데이터 웨어하우스의 잠재적으로 손상 된 TDE 보호기에 대응 하는 방법 가이드입니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: d4c9b926afe93f52946c5f1adf40835f72812f2a
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995825"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell을 사용 하 여 TDE (투명한 데이터 암호화) 보호기 제거

## <a name="prerequisites"></a>Előfeltételek

- Azure 구독이 있어야 하 고 해당 구독의 관리자 여야 합니다.
- Azure PowerShell 설치 되 고 실행 중 이어야 합니다.
- 이 방법 가이드에서는 Azure Key Vault의 키를 Azure SQL Database 또는 데이터 웨어하우스의 TDE 보호기로 이미 사용 한다고 가정 합니다. 자세한 내용은 [BYOK 지원 투명한 데이터 암호화](transparent-data-encryption-byok-azure-sql.md) 을 참조 하세요.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 Az Az modul telepítési útmutatását [az Azure PowerShell telepítését](/powershell/azure/install-az-ps) ismertető cikkben találja. 특정 cmdlet에 대 한 자세한 내용은 [AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)를 참조 하세요.

> [!IMPORTANT]
> Azure SQL Database에서 RM (PowerShell Azure Resource Manager) 모듈을 계속 사용할 수 있지만 향후의 모든 개발은 Az. Sql 모듈에 대 한 것입니다. AzureRM 모듈은 12 월 2020 일까 때까지 버그 수정을 계속 받습니다.  Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다. 호환성에 대 한 자세한 내용은 [새 Azure PowerShell Az Module 소개](/powershell/azure/new-azureps-module-az)를 참조 하세요.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

설치 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요.

* * *

## <a name="overview"></a>Áttekintés

이 방법 가이드에서는 BYOK (Azure Key Vault Bring Your Own Key) 지원에서 TDE를 사용 하 여 TDE를 사용 하는 Azure SQL Database 또는 데이터 웨어하우스의 잠재적으로 손상 된 TDE 보호기에 대응 하는 방법을 설명 합니다. TDE에 대 한 BYOK 지원에 대 한 자세한 내용은 [개요 페이지](transparent-data-encryption-byok-azure-sql.md)를 참조 하세요.

다음 절차는 극단적인 경우 나 테스트 환경 에서만 수행 해야 합니다. Azure Key Vault에서 적극적으로 사용 되는 TDE 보호기를 삭제 하면 **데이터가 손실**될 수 있으므로 방법 가이드를 주의 깊게 검토 하세요.

키가 손상 된 것으로 의심 되는 경우 서비스 또는 사용자가 키에 무단으로 액세스할 수 있는 경우 해당 키를 삭제 하는 것이 가장 좋습니다.

Key Vault에서 TDE 보호기를 삭제 한 후에는 **서버에서 암호화 된 데이터베이스에 대 한 모든 연결이 차단 되 고 이러한 데이터베이스가 오프 라인으로 전환 되며 24 시간 이내에 삭제**됩니다. 손상 된 키로 암호화 된 이전 백업은 더 이상 액세스할 수 없습니다.

다음 단계는 지정 된 데이터베이스의 가상 로그 파일 (VLF)에서 아직 사용 중인 TDE 보호기 지문을 확인 하는 방법을 간략하게 설명 합니다.
데이터베이스의 현재 TDE 보호기의 지문과 데이터베이스 ID는 다음을 실행 하 여 찾을 수 있습니다.

```sql
SELECT [database_id], 
       [encryption_state], 
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/ 
       [encryptor_thumbprint], 
 FROM [sys].[dm_database_encryption_keys]
```

다음 쿼리는 사용 중인 Vlf 및 해당 지문을 반환 합니다. 각 지문이 Azure Key Vault (AKV)의 다른 키를 참조 합니다.

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 명령 **add-azurermsqlserverkeyvaultkey** 은 쿼리에서 사용 되는 Tde 보호기의 지문을 제공 하므로 유지할 키와 AKV에서 삭제할 키를 확인할 수 있습니다. 데이터베이스에서 더 이상 사용 하지 않는 키만 Azure Key Vault에서 안전 하 게 삭제할 수 있습니다.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell 명령 **az sql server key show** 는 쿼리에 사용 된 Tde 보호기의 지문을 제공 하므로 유지할 키와 AKV에서 삭제할 키를 확인할 수 있습니다. 데이터베이스에서 더 이상 사용 하지 않는 키만 Azure Key Vault에서 안전 하 게 삭제할 수 있습니다.

* * *

이 방법 가이드는 인시던트 응답 후 원하는 결과에 따라 두 가지 방법으로 이동 합니다.

- Azure SQL 데이터베이스/데이터 웨어하우스를 **액세스할 수** 있도록 하려면
- Azure SQL 데이터베이스/데이터 웨어하우스를 **액세스할 수** 없게 하려면

## <a name="to-keep-the-encrypted-resources-accessible"></a>암호화 된 리소스에 액세스할 수 있도록 하려면

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Key Vault에서 새 키](/powershell/module/az.keyvault/add-azkeyvaultkey)를 만듭니다. 액세스 제어가 자격 증명 모음 수준에서 프로 비전 되기 때문에 잠재적으로 손상 된 TDE 보호기에서 별도의 키 자격 증명 모음에이 새 키를 만들었는지 확인 합니다.

2. [AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) 및 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 하 여 서버에 새 키를 추가 하 고 서버의 새 tde 보호기로 업데이트 합니다.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 하 여 서버 및 모든 복제본이 새 tde 보호기로 업데이트 되었는지 확인 합니다.

   > [!NOTE]
   > 서버에 있는 모든 데이터베이스 및 보조 데이터베이스에 새 TDE 보호기를 전파 하는 데 몇 분 정도 걸릴 수 있습니다.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Key Vault에서 [새 키의 백업을](/powershell/module/az.keyvault/backup-azkeyvaultkey) 수행 합니다.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. [AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet을 사용 하 여 Key Vault에서 손상 된 키를 삭제 합니다.

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. 나중에 [AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet을 사용 하 여 Key Vault 키를 복원 하려면 다음을 수행 합니다.

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

명령 참조는 [Azure CLI keyvault](/cli/azure/keyvault/key)를 참조 하세요.

1. [Key Vault에서 새 키](/cli/azure/keyvault/key#az-keyvault-key-create)를 만듭니다. 액세스 제어가 자격 증명 모음 수준에서 프로 비전 되기 때문에 잠재적으로 손상 된 TDE 보호기에서 별도의 키 자격 증명 모음에이 새 키를 만들었는지 확인 합니다.

2. 서버에 새 키를 추가 하 고 서버의 새 TDE 보호기로 업데이트 합니다.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. 서버 및 모든 복제본이 새 TDE 보호기로 업데이트 되었는지 확인 합니다.

   > [!NOTE]
   > 서버에 있는 모든 데이터베이스 및 보조 데이터베이스에 새 TDE 보호기를 전파 하는 데 몇 분 정도 걸릴 수 있습니다.

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Key Vault에서 새 키의 백업을 수행 합니다.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Key Vault에서 손상 된 키를 삭제 합니다.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. 키를 복원 하 여 나중에 Key Vault 합니다.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>암호화 된 리소스를 액세스할 수 없도록 설정 하려면

1. 잠재적으로 손상 된 키로 암호화 되는 데이터베이스를 삭제 합니다.

   데이터베이스 및 로그 파일이 자동으로 백업 되므로 키를 제공 하는 동안 언제 든 지 데이터베이스의 특정 시점 복원을 수행할 수 있습니다. 최신 트랜잭션의 최대 10 분 동안 잠재적인 데이터 손실을 방지 하기 위해 활성 TDE 보호기를 삭제 하기 전에 데이터베이스를 삭제 해야 합니다.

2. Key Vault에서 TDE 보호기의 핵심 자료를 백업 합니다.
3. Key Vault에서 잠재적으로 손상 된 키 제거

## <a name="next-steps"></a>Következő lépések

- 보안 요구 사항을 준수 하는 서버의 TDE 보호기를 회전 하는 방법에 대해 알아봅니다. [PowerShell을 사용 하 여 투명한 데이터 암호화 보호기 회전](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- TDE에 대 한 Bring Your Own Key 지원 시작: [PowerShell을 사용 하 여 Key Vault에서 고유한 키를 사용 하 여 Tde 설정](transparent-data-encryption-byok-azure-sql-configure.md)
