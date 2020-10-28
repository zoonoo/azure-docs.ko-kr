---
title: TDE 보호기 제거 (PowerShell & Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: BYOK (사용자 고유 키) 지원을 통해 TDE를 사용 하 여 Azure SQL Database 또는 Azure Synapse Analytics에 대해 잠재적으로 손상 된 TDE 보호기에 대응 하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 657e3967d9e34147364114cec4d946e900f60032
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791378"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell을 사용하여 TDE(Transparent Data Encryption) 보호기 제거
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


이 항목에서는 BYOK (Azure Key Vault-Bring Your Own Key) 지원을 통해 TDE를 고객 관리 키와 함께 사용 하는 Azure SQL Database 또는 Azure Synapse Analytics에 대해 잠재적으로 손상 된 TDE 보호에 대응 하는 방법에 대해 설명 합니다. TDE의 BYOK 지원에 대해 자세한 내용은 [개요 페이지](transparent-data-encryption-byok-overview.md)를 참조하세요.

> [!CAUTION]
> 이 문서에 설명 된 절차는 극단적인 경우 나 테스트 환경 에서만 수행 해야 합니다. Azure Key Vault에서 적극적으로 사용 되는 TDE 보호기를 삭제 하면 **데이터베이스를 사용할 수 없게** 되므로 단계를 신중 하 게 검토 합니다.

키가 손상 된 것으로 의심 되는 경우 서비스 또는 사용자가 키에 무단으로 액세스할 수 있는 경우 해당 키를 삭제 하는 것이 가장 좋습니다.

Key Vault에서 TDE 보호기를 삭제 한 후에는 최대 10 분 내에 모든 암호화 된 데이터베이스에서 해당 오류 메시지와 함께 모든 연결 거부를 시작 하 고 해당 상태를 [액세스할](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector)수 없음으로 변경 합니다.

이 방법 가이드는 손상 된 인시던트 응답 후 원하는 결과에 따라 두 가지 방법으로 이동 합니다.

- Azure SQL Database/Azure Synapse Analytics의 데이터베이스에 **액세스할 수** 없게 합니다.
- Azure SQL Database/Azure Azure Synapse Analytics (이전의 SQL Data Warehouse)의 데이터베이스에 **액세스할** 수 없도록 설정 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
- Azure PowerShell 설치 되 고 실행 중 이어야 합니다.
- 이 방법 가이드에서는 이미 Azure Key Vault의 키를 Azure SQL Database 또는 Azure Synapse (이전의 SQL Data Warehouse)의 TDE 보호기로 사용 한다고 가정 합니다. 자세한 내용은 [BYOK 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-overview.md)를 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Az 모듈 설치 지침은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. 특정 cmdlet에 대 한 자세한 내용은 [AzureRM](/powershell/module/AzureRM.Sql/)를 참조 하세요.

> [!IMPORTANT]
> RM (PowerShell Azure Resource Manager) 모듈은 계속 지원 되지만 모든 향후 개발은 Az. Sql 모듈에 대 한 것입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 수신할 예정입니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 호환성에 대한 자세한 내용은 [새로운 Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

설치 [는 Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요.

* * *

## <a name="check-tde-protector-thumbprints"></a>TDE 보호기 지문을 확인 합니다.

다음 단계는 지정 된 데이터베이스의 가상 로그 파일 (VLF)에서 아직 사용 중인 TDE 보호기 지문을 확인 하는 방법을 간략하게 설명 합니다.
데이터베이스의 현재 TDE 보호기의 지문과 데이터베이스 ID는 다음을 실행 하 여 찾을 수 있습니다.

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

다음 쿼리는 사용 중인 각 Vlf 및 TDE 보호기를 반환 합니다. 각 지문이 Azure Key Vault (AKV)의 다른 키를 참조 합니다.

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

또는 PowerShell 또는 Azure CLI을 사용할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 명령 **add-azurermsqlserverkeyvaultkey** 은   쿼리에서 사용 되는 tde 보호기의 지문을 제공 하므로 유지할 키와 AKV에서 삭제할 키를 확인할 수 있습니다. 데이터베이스에서 더 이상 사용 하지 않는 키만 Azure Key Vault에서 안전 하 게 삭제할 수 있습니다.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell 명령 **az sql server key show** 는   쿼리에 사용 된 tde 보호기의 지문을 제공 하므로 유지할 키와 AKV에서 삭제할 키를 확인할 수 있습니다. 데이터베이스에서 더 이상 사용 하지 않는 키만 Azure Key Vault에서 안전 하 게 삭제할 수 있습니다.

* * *

## <a name="keep-encrypted-resources-accessible"></a>암호화 된 리소스에 액세스할 수 있도록 유지

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Key Vault에 새 키](/powershell/module/az.keyvault/add-azkeyvaultkey)를 만듭니다. 액세스 제어는 자격 증명 모음 수준에서 프로비전되므로 이 새 키는 손상 가능성이 있는 TDE 보호기와는 별도의 키 자격 증명 모음에 만들어야 합니다.

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
   > 새 TDE 보호기가 서버 아래의 모든 데이터베이스와 보조 데이터베이스로 전파되는 데 몇 분 정도 걸릴 수 있습니다.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Key Vault에 [새 키의 백업](/powershell/module/az.keyvault/backup-azkeyvaultkey)을 작성합니다.

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

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

명령 참조는 [Azure CLI keyvault](/cli/azure/keyvault/key)를 참조 하세요.

1. [Key Vault에 새 키](/cli/azure/keyvault/key#az-keyvault-key-create)를 만듭니다. 액세스 제어는 자격 증명 모음 수준에서 프로비전되므로 이 새 키는 손상 가능성이 있는 TDE 보호기와는 별도의 키 자격 증명 모음에 만들어야 합니다.

2. 서버에 새 키를 추가 하 고 서버의 새 TDE 보호기로 업데이트 합니다.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. 서버 및 모든 복제본이 새 TDE 보호기로 업데이트 되었는지 확인 합니다.

   > [!NOTE]
   > 새 TDE 보호기가 서버 아래의 모든 데이터베이스와 보조 데이터베이스로 전파되는 데 몇 분 정도 걸릴 수 있습니다.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Key Vault에 새 키의 백업을 작성합니다.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Key Vault에서 손상 된 키를 삭제 합니다.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. 키를 복원 하 여 나중에 Key Vault 합니다.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>암호화 된 리소스에 액세스할 수 없도록 설정

1. 손상 가능성이 있는 키로 암호화된 데이터베이스를 삭제합니다.

   데이터베이스 및 로그 파일은 자동으로 백업되므로 (키를 제공하는 한) 어느 지점에서나 데이터베이스의 시점 복원을 수행할 수 있습니다. 최대 10분의 가장 최근 트랜잭션에 대한 데이터 손실 가능성을 차단하기 위해 활성 TDE 보호기를 삭제하기 전에 데이터베이스를 삭제해야 합니다.

2. Key Vault에서 TDE 보호기의 키 자료를 백업합니다.
3. Key Vault에서 잠재적으로 손상된 키 제거

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>다음 단계

- 보안 요구 사항에 맞게 서버의 TDE 보호기를 회전하는 방법을 알아봅니다([PowerShell을 사용하여 투명한 데이터 암호화 보호기 회전](transparent-data-encryption-byok-key-rotation.md)).
- TDE에 대한 BYOK(Bring Your Own Key) 지원 시작: [PowerShell을 사용하여 Key Vault에서 고유 키로 TDE 설정](transparent-data-encryption-byok-configure.md)