---
title: PowerShell - TDE 보호기 제거 - Azure SQL Database| Microsoft Docs
description: BYOK(Bring Your Own Key) 지원을 통해 TDE를 사용하는 Azure SQL Database 또는 데이터 웨어하우스에 대해 손상 가능성이 있는 TDE 보호기에 대처하기 위한 방법 가이드.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: dc117dd844a3a47cafa1b37170c95fe852bb82ef
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566058"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell을 사용하여 TDE(Transparent Data Encryption) 보호기 제거

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원하지만 모든 향후 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대한 내용은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조합니다. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

- Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
- Azure PowerShell 설치 되 고 실행 중 이어야 합니다. 
- 이 방법 가이드에서는 이미 Azure SQL Database나 데이터 웨어하우스에 대해 Azure Key Vault의 키를 TDE 보호기로 사용하고 있다고 가정합니다. 자세한 내용은 [BYOK 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-azure-sql.md)를 참조하세요.

## <a name="overview"></a>개요

이 방법 가이드는 Azure Key Vault에서 고객 관리 키 - BYOK(Bring Your Own Key) 지원을 통해 TDE를 사용하는 Azure SQL Database 또는 Data Warehouse에 대해 손상 가능성이 있는 TDE 보호기에 대처하는 방법을 설명합니다. TDE의 BYOK 지원에 대해 자세한 내용은 [개요 페이지](transparent-data-encryption-byok-azure-sql.md)를 참조하세요. 

다음 절차는 극단적인 경우 또는 테스트 환경에서만 수행합니다. Azure Key Vault에서 적극적으로 사용되는 TDE 보호기를 삭제하면 **데이터 손실**을 초래할 수 있으므로 방법 가이드를 주의 깊게 검토하세요. 

키가 손상되어 서비스나 사용자가 키에 무단 액세스한 것으로 의심될 경우 키를 삭제하는 것이 가장 좋습니다.

TDE 보호기가 Key Vault에서 삭제된 후에는 **서버 아래의 암호화된 데이터베이스에 대한 모든 연결이 차단되고, 이들 데이터베이스는 오프라인 상태가 되어 24시간 이내에 삭제됩니다**. 손상된 키로 암호화된 기존 백업에는 더 이상 액세스할 수 없습니다.

다음 단계는 지정 된 데이터베이스의 가상 로그 파일 (VLF)에서 아직 사용 중인 TDE 보호기 지문을 확인 하는 방법을 간략하게 설명 합니다. 데이터베이스의 현재 TDE 보호기의 지문과 데이터베이스 ID는 다음을 실행 하 여 찾을 수 있습니다. SELECT [database_id],       [encryption_state], [encryptor_type],/*비대칭 키, AKV, certificate는 서비스 관리 키*/[encryptor_thumbprint], [sys]. [ dm_database_encryption_keys] 
 
다음 쿼리는 사용 중인 Vlf 및 해당 지문을 반환 합니다. 각 지문이 Azure Key Vault (AKV)의 다른 키를 참조 합니다. SELECT * FROM sys.dm_db_log_info (database_id) 

PowerShell 명령 Add-azurermsqlserverkeyvaultkey은 쿼리에서 사용 되는 TDE 보호기의 지문을 제공 하므로 유지할 키와 AKV에서 삭제할 키를 확인할 수 있습니다. 데이터베이스에서 더 이상 사용 하지 않는 키만 Azure Key Vault에서 안전 하 게 삭제할 수 있습니다.

이 방법 가이드에서는 사건 대처 후 원하는 결과에 따라 두 가지 방법을 살펴봅니다.

- Azure SQL 데이터베이스/Data Warehouse에 **액세스할 수 있도록** 유지
- Azure SQL 데이터베이스/Data Warehouse에 **액세스할 수 없도록** 유지

## <a name="to-keep-the-encrypted-resources-accessible"></a>암호화된 리소스를 액세스할 수 있게 하려면

1. [Key Vault에 새 키](/powershell/module/az.keyvault/add-azkeyvaultkey)를 만듭니다. 액세스 제어는 자격 증명 모음 수준에서 프로비전되므로 이 새 키는 손상 가능성이 있는 TDE 보호기와는 별도의 키 자격 증명 모음에 만들어야 합니다.
2. [AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) 및 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 하 여 서버에 새 키를 추가 하 고 서버의 새 tde 보호기로 업데이트 합니다.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 하 여 서버 및 모든 복제본이 새 tde 보호기로 업데이트 되었는지 확인 합니다. 

   >[!NOTE]
   > 새 TDE 보호기가 서버 아래의 모든 데이터베이스와 보조 데이터베이스로 전파되는 데 몇 분 정도 걸릴 수 있습니다.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Key Vault에 [새 키의 백업](/powershell/module/az.keyvault/backup-azkeyvaultkey)을 작성합니다.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. [AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet을 사용 하 여 Key Vault에서 손상 된 키를 삭제 합니다. 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. 나중에 [AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet을 사용 하 여 Key Vault 키를 복원 하려면 다음을 수행 합니다.
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>암호화된 리소스를 액세스할 수 없도록 하려면

1. 손상 가능성이 있는 키로 암호화된 데이터베이스를 삭제합니다.

   데이터베이스 및 로그 파일은 자동으로 백업되므로 (키를 제공하는 한) 어느 지점에서나 데이터베이스의 시점 복원을 수행할 수 있습니다. 최대 10분의 가장 최근 트랜잭션에 대한 데이터 손실 가능성을 차단하기 위해 활성 TDE 보호기를 삭제하기 전에 데이터베이스를 삭제해야 합니다. 
2. Key Vault에서 TDE 보호기의 키 자료를 백업합니다.
3. Key Vault에서 잠재적으로 손상된 키 제거

## <a name="next-steps"></a>다음 단계

- 보안 요구 사항을 준수하도록 서버의 TDE 보호기를 회전하는 방법 알아보기: [PowerShell을 사용하여 투명한 데이터 암호화 보호기 회전](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- TDE에 대한 Bring Your Own Key 지원 시작: [PowerShell을 사용하여 Key Vault에서 고유 키로 TDE 설정](transparent-data-encryption-byok-azure-sql-configure.md)
