---
title: PowerShell - TDE 보호기 제거 - Azure SQL Database| Microsoft Docs
description: BYOK(Bring Your Own Key) 지원을 통해 TDE를 사용하는 Azure SQL Database 또는 데이터 웨어하우스에 대해 손상 가능성이 있는 TDE 보호기에 대처하기 위한 방법 가이드
keywords: ''
services: sql-database
documentationcenter: ''
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: ''
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: rebeccaz
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: feb187101ec02d6e765d6b025f518dc416f55b8b
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043552"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell을 사용하여 TDE(Transparent Data Encryption) 보호기 제거
## <a name="prerequisites"></a>필수 조건
- Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
- Azure PowerShell 버전 4.2.0 이상이 설치되어 실행 중이어야 합니다. 
- 이 방법 가이드에서는 이미 Azure SQL Database나 데이터 웨어하우스에 대해 Azure Key Vault의 키를 TDE 보호기로 사용하고 있다고 가정합니다. 자세한 내용은 [BYOK 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-azure-sql.md)를 참조하세요.

## <a name="overview"></a>개요
이 방법 가이드는 BYOK(Bring Your Own Key) 지원을 통해 TDE를 사용하는 Azure SQL Database 또는 데이터 웨어하우스에 대해 손상 가능성이 있는 TDE 보호기에 대처하는 방법을 설명합니다. TDE의 BYOK 지원에 대해 자세한 내용은 [개요 페이지](transparent-data-encryption-byok-azure-sql.md)를 참조하세요. 

다음 절차는 극단적인 경우 또는 테스트 환경에서만 수행합니다. Azure Key Vault에서 적극적으로 사용되는 TDE 보호기를 삭제하면 **데이터 손실**을 초래할 수 있으므로 방법 가이드를 주의 깊게 검토하세요. 

키가 손상되어 서비스나 사용자가 키에 무단 액세스한 것으로 의심될 경우 키를 삭제하는 것이 가장 좋습니다.

TDE 보호기가 Key Vault에서 삭제된 후에는 **서버 아래의 암호화된 데이터베이스에 대한 모든 연결이 차단되고, 이들 데이터베이스는 오프라인 상태가 되어 24시간 이내에 삭제됩니다**. 손상된 키로 암호화된 기존 백업에는 더 이상 액세스할 수 없습니다.

이 방법 가이드에서는 사건 대처 후 원하는 결과에 따라 두 가지 방법을 살펴봅니다.
- Azure SQL Database/데이터 웨어하우스를 **액세스할 수 있게** 유지
- Azure SQL Database/데이터 웨어하우스를 **액세스할 수 없게** 유지

## <a name="to-keep-the-encrypted-resources-accessible"></a>암호화된 리소스를 액세스할 수 있게 하려면
1. [Key Vault에 새 키](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey?view=azurermps-4.1.0)를 만듭니다. 액세스 제어는 자격 증명 모음 수준에서 프로비전되므로 이 새 키는 손상 가능성이 있는 TDE 보호기와는 별도의 키 자격 증명 모음에 만들어야 합니다. 
2. [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) 및 [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) cmdlet으로 새 키를 서버에 추가하고 서버의 새 TDE 보호기로 업데이트합니다.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>
   
   # Set the key as the TDE protector for all resources under the server
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) cmdlet을 사용하여 서버 및 복제본이 새 TDE 보호기로 업데이트되게 합니다. 

   >[!NOTE]
   > 새 TDE 보호기가 서버 아래의 모든 데이터베이스와 보조 데이터베이스로 전파되는 데 몇 분 정도 걸릴 수 있습니다.
   >

   ```powershell
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Key Vault에 [새 키의 백업](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)을 작성합니다.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. [Remove-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey) cmdlet을 사용하여 Key Vault에서 손상된 키를 삭제합니다. 

   ```powershell
   Remove-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey) cmdlet을 사용하여 Key Vault에 키를 복원하려면
   ```powershell
   Restore-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```
 
## <a name="to-make-the-encrypted-resources-inaccessible"></a>암호화된 리소스를 액세스할 수 없도록 하려면
1. 손상 가능성이 있는 키로 암호화된 데이터베이스를 삭제합니다.
데이터베이스 및 로그 파일은 자동으로 백업되므로 (키를 제공하는 한) 어느 지점에서나 데이터베이스의 시점 복원을 수행할 수 있습니다. 최대 10분의 가장 최근 트랜잭션에 대한 데이터 손실 가능성을 차단하기 위해 활성 TDE 보호기를 삭제하기 전에 데이터베이스를 삭제해야 합니다. 
2. Key Vault에서 TDE 보호기의 키 자료를 백업합니다.
3. Key Vault에서 잠재적으로 손상된 키 제거

## <a name="next-steps"></a>다음 단계

- 보안 요구 사항에 맞게 서버의 TDE 보호기를 회전하는 방법을 알아봅니다([PowerShell을 사용하여 투명한 데이터 암호화 보호기 회전](transparent-data-encryption-byok-azure-sql-key-rotation.md)).

- TDE에 대한 BYOK(Bring Your Own Key) 지원 시작: [PowerShell을 사용하여 Key Vault에서 고유 키로 TDE 설정](transparent-data-encryption-byok-azure-sql-configure.md)
