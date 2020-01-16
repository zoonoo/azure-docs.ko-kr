---
title: Azure Key Vault에서 SQL TDE 사용
description: PowerShell 또는 CLI를 통해 미사용 데이터 암호화에 TDE(투명한 데이터 암호화)를 사용하도록 Azure SQL Database 및 Data Warehouse를 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: 72825a709ec55bf5879e15287eb8b258dc4c8e94
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965784"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell 및 CLI: Azure Key Vault에서 고객이 관리 하는 키로 투명한 데이터 암호화를 사용 하도록 설정

이 문서에서는 SQL Database 또는 Data Warehouse에서 TDE(투명한 데이터 암호화)에 대해 Azure Key Vault의 키를 사용하는 방법에 대해 설명합니다. Azure Key Vault 통합으로 TDE - BYOK(Bring Your Own Key) 지원에 대해 자세히 알아보려면 [Azure Key Vault에서 고객 관리형 키로 TDE](transparent-data-encryption-byok-azure-sql.md)를 참조하세요.

## <a name="prerequisites-for-powershell"></a>PowerShell용 필수 구성 요소

- Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
- [권장되는 선택 사항] TDE 보호기 키 자료의 로컬 복사본을 만들기 위한 HSM(하드웨어 보안 모듈) 또는 로컬 키 저장소가 있어야 합니다.
- Azure PowerShell 설치 되 고 실행 중 이어야 합니다.
- TDE에 사용할 Azure Key Vault 및 키를 만듭니다.
  - [HSM(하드웨어 보안 모듈) 및 Key Vault 사용 지침](../key-vault/key-vault-hsm-protected-keys.md)
    - 키 자격 증명 모음에는 TDE에 사용할 다음 속성이 있어야 합니다.
  - [일시 삭제](../key-vault/key-vault-ovw-soft-delete.md) 및 보호 제거
- 키에는 TDE에 사용할 다음 특성이 있어야 합니다.
   - 만료 날짜 없음
   - 사용 안 함 없음
   - *가져오기*, *키 래핑*, *키 래핑 해제* 작업도 수행 가능

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Az 모듈 설치 지침은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. 특정 cmdlet에 대 한 자세한 내용은 [AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)를 참조 하세요.

Key Vault에 대 한 자세한 내용은 [Key Vault powershell 지침](../key-vault/quick-create-powershell.md) 및 [powershell에서 일시 삭제 Key Vault 사용 하는 방법](../key-vault/key-vault-soft-delete-powershell.md)을 참조 하세요.

> [!IMPORTANT]
> Azure SQL Database에서 RM (PowerShell Azure Resource Manager) 모듈을 계속 사용할 수 있지만 향후의 모든 개발은 Az. Sql 모듈에 대 한 것입니다. AzureRM 모듈은 12 월 2020 일까 때까지 버그 수정을 계속 받습니다.  Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다. 호환성에 대 한 자세한 내용은 [새 Azure PowerShell Az Module 소개](/powershell/azure/new-azureps-module-az)를 참조 하세요.

## <a name="assign-an-azure-ad-identity-to-your-server"></a>서버에 Azure AD ID 할당

기존 서버가 있는 경우 다음과 같이 Azure AD ID를 서버에 추가합니다.

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

서버를 만드는 경우에는 [AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet을 태그 id와 함께 사용 하 여 서버 생성 중에 Azure AD id를 추가 합니다.

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>서버에 Key Vault 권한 부여

TDE에 대해 키를 사용 하기 전에 [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet을 사용 하 여 서버에 키 자격 증명 모음에 대 한 액세스 권한을 부여 합니다.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Key Vault 키를 서버에 추가하고 TDE 보호기를 설정합니다

- [AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) cmdlet을 사용 하 여 key vault에서 키 ID를 검색 합니다.
- [AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet을 사용 하 여 Key Vault 서버에 키를 추가 합니다.
- [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 하 여 모든 서버 리소스에 대 한 tde 보호기로 키를 설정 합니다.
- [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 하 여 tde 보호기가 의도 한 대로 구성 되었는지 확인 합니다.

> [!NOTE]
> 키 자격 증명 모음 이름과 키 이름을 결합한 길이는 94자를 초과할 수 없습니다.

> [!TIP]
> Key Vault의 KeyId 예제: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>TDE 설정

[AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet을 사용 하 여 tde를 설정 합니다.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

이제 데이터베이스 또는 데이터 웨어하우스에서 Key Vault에 있는 암호화 키를 사용하여 TDE를 사용할 수 있도록 설정되었습니다.

## <a name="check-the-encryption-state-and-encryption-activity"></a>암호화 상태 및 암호화 작업 확인

[AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) 를 사용 하 여 암호화 상태를 가져오고 [AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) 를 사용 하 여 데이터베이스 또는 데이터 웨어하우스의 암호화 진행률을 확인 합니다.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

필수 명령줄 인터페이스 버전 2.0 이상을 설치 하 고 Azure 구독에 연결 하려면 [Azure 플랫폼 간 명령줄 인터페이스 2.0 설치 및 구성](https://docs.microsoft.com/cli/azure/install-azure-cli)을 참조 하세요.

Key Vault에 대 한 자세한 내용은 [cli 2.0을 사용 하 여 Key Vault 관리](../key-vault/key-vault-manage-with-cli2.md) 및 [cli에서 일시 삭제 Key Vault 사용 하는 방법](../key-vault/key-vault-soft-delete-cli.md)을 참조 하세요.

## <a name="assign-an-azure-ad-identity-to-your-server"></a>서버에 Azure AD ID 할당

```powershell
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> 서버를 만드는 과정에서 얻은 “principalID”를 보관합니다. 이 ID는 다음 단계에서 키 자격 증명 모음 권한을 할당하는 데 사용되는 개체 ID입니다.

## <a name="grant-key-vault-permissions-to-your-server"></a>서버에 Key Vault 권한 부여

```powershell
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> 다음 단계를 위해 새 키의 키 URI 또는 keyID를 보관합니다(예: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h ).

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Key Vault 키를 서버에 추가하고 TDE 보호기를 설정합니다

```powershell
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> 키 자격 증명 모음 이름과 키 이름을 결합한 길이는 94자를 초과할 수 없습니다.

## <a name="turn-on-tde"></a>TDE 설정

```powershell
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

이제 데이터베이스 또는 데이터 웨어하우스에서 Azure Key Vault에 있는 고객 관리형 암호화 키로 TDE를 사용할 수 있습니다.

## <a name="check-the-encryption-state-and-encryption-activity"></a>암호화 상태 및 암호화 작업 확인

```powershell
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>유용한 PowerShell cmdlet

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- [AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet을 사용 하 여 tde를 해제 합니다.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled”
   ```

- [AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet을 사용 하 여 서버에 추가 된 Key Vault 키 목록을 반환 합니다.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- [AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) 를 사용 하 여 서버에서 Key Vault 키를 제거 합니다.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

- 일반 데이터베이스 설정은 [az sql](/cli/azure/sql)을 참조 하세요.

- 자격 증명 모음 키 설정에 대 한 자세한 내용은 [az sql server key](/cli/azure/sql/server/key)를 참조 하세요.

- TDE 설정에 대해서는 [az sql server TDE 키](/cli/azure/sql/server/tde-key) 및 [az sql db TDE](/cli/azure/sql/db/tde)를 참조 하세요.

* * *

## <a name="troubleshooting"></a>문제 해결

문제가 발생하면 다음과 같이 확인합니다.

- 키 자격 증명 모음을 찾을 수 없는 경우 올바른 구독에 있는지 확인 합니다.

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- 새 키를 서버에 추가할 수 없거나 TDE 보호기로 업데이트할 수 없으면 다음을 확인합니다.
   - 키의 만료 날짜가 없어야 합니다.
   - 키에는 사용하도록 설정된 *가져오기*, *키 래핑* 및 *키 래핑 해제* 작업이 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- 보안 요구 사항에 맞게 서버의 TDE 보호기를 회전하는 방법을 알아봅니다([PowerShell을 사용하여 투명한 데이터 암호화 보호기 회전](transparent-data-encryption-byok-azure-sql-key-rotation.md)).
- 보안 위험이 있는 경우 잠재적으로 손상될 수 있는 TDE 보호기를 제거하는 방법을 알아봅니다([잠재적으로 손상될 수 있는 키 제거](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)).
