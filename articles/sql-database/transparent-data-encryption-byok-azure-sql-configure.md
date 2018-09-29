---
title: 'PowerShell 및 CLI: 사용자 고유 키로 SQL TDE 사용 - Azure SQL Database | Microsoft Docs'
description: PowerShell 또는 CLI를 통해 미사용 데이터 암호화에 TDE(투명한 데이터 암호화)를 사용하도록 Azure SQL Database 및 Data Warehouse를 구성하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 0fad0cd32e8df38c5a9c06ecf01a14340f1bc9ef
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165078"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-using-your-own-key-from-azure-key-vault"></a>PowerShell 및 CLI: Azure Key Vault에서 사용자 고유 키를 사용하여 투명한 데이터 암호화 사용

이 문서에서는 SQL Database 또는 Data Warehouse에서 TDE(투명한 데이터 암호화)에 대해 Azure Key Vault의 키를 사용하는 방법에 대해 설명합니다. BYOK(Bring Your Own Key) 지원을 통한 TDE에 대한 자세한 내용은 [Azure SQL에 대한 TDE Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md)를 방문하세요. 

## <a name="prerequisites-for-powershell"></a>PowerShell용 필수 구성 요소

- Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
- [권장되는 선택 사항] TDE 보호기 키 자료의 로컬 복사본을 만들기 위한 HSM(하드웨어 보안 모듈) 또는 로컬 키 저장소가 있어야 합니다.
- Azure PowerShell 버전 4.2.0 이상이 설치되어 실행 중이어야 합니다. 
- TDE에 사용할 Azure Key Vault 및 키를 만듭니다.
   - [PowerShell에 대한 Key Vault 지침](../key-vault/key-vault-get-started.md)
   - [HSM(하드웨어 보안 모듈) 및 Key Vault 사용 지침](../key-vault/key-vault-get-started.md#HSM)
 - 키 자격 증명 모음에는 TDE에 사용할 다음 속성이 있어야 합니다.
   - [일시 삭제](../key-vault/key-vault-ovw-soft-delete.md)
   - [PowerShell로 Key Vault 일시 삭제를 사용하는 방법](../key-vault/key-vault-soft-delete-powershell.md) 
- 키에는 TDE에 사용할 다음 특성이 있어야 합니다.
   - 만료 날짜 없음
   - 사용 안 함 없음
   - *가져오기*, *키 래핑*, *키 래핑 해제* 작업도 수행 가능

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>1단계. 서버에 Azure AD ID 할당 

기존 서버가 있는 경우 다음과 같이 Azure AD ID를 서버에 추가합니다.

   ```powershell
   $server = Set-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

서버를 만드는 경우 서버를 만드는 동안 -Identity 태그가 있는 [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) cmdlet을 사용하여 Azure AD ID를 추가합니다.

   ```powershell
   $server = New-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>2단계. 서버에 Key Vault 권한 부여

서버에서 키 자격 증명 모음의 키를 TDE에 사용하기 전에 [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet을 사용하여 해당 키 자격 증명 모음에 대한 액세스 권한을 서버에 부여합니다.

   ```powershell
   Set-AzureRmKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>3단계. Key Vault 키를 서버에 추가하고 TDE 보호기를 설정합니다

- [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) cmdlet을 사용하여 Key Vault의 키를 서버에 추가합니다.
- [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) cmdlet을 사용하여 키를 모든 서버 리소스에 대한 TDE 보호기로 설정합니다.
- [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) cmdlet을 사용하여 TDE 보호기가 의도한 대로 구성되었는지 확인합니다.

> [!Note]
> 키 자격 증명 모음 이름과 키 이름을 결합한 길이는 94자를 초과할 수 없습니다.
> 

>[!Tip]
>Key Vault의 KeyId 예제: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>4단계. TDE 설정 

[Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) cmdlet을 사용하여 TDE를 설정합니다.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

이제 데이터베이스 또는 데이터 웨어하우스에서 Key Vault에 있는 암호화 키를 사용하여 TDE를 사용할 수 있도록 설정되었습니다.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>5단계. 암호화 상태 및 암호화 작업 확인

[Get-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption)을 사용하여 암호화 상태를 가져오고, [Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity)를 사용하여 데이터베이스 또는 데이터 웨어하우스의 암호화 진행률을 확인합니다.

   ```powershell
   # Get the encryption state
   Get-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>다른 유용한 PowerShell cmdlet

- TDE를 해제하려면 [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) cmdlet을 사용합니다.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- 서버에 추가된 Key Vault 키 목록을 반환하려면 [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) cmdlet을 사용합니다.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- 서버에서 Key Vault 키를 제거하려면 [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey)를 사용합니다.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>문제 해결

문제가 발생하면 다음과 같이 확인합니다.
- 키 자격 증명 모음을 찾을 수 없으면 [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) cmdlet을 사용하여 올바른 구독에 있는지 확인합니다.

   ```powershell
   Get-AzureRmSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- 새 키를 서버에 추가할 수 없거나 TDE 보호기로 업데이트할 수 없으면 다음을 확인합니다.
   - 키의 만료 날짜가 없어야 합니다.
   - 키에는 사용하도록 설정된 *가져오기*, *키 래핑* 및 *키 래핑 해제* 작업이 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- 보안 요구 사항에 맞게 서버의 TDE 보호기를 회전하는 방법을 알아봅니다([PowerShell을 사용하여 투명한 데이터 암호화 보호기 회전](transparent-data-encryption-byok-azure-sql-key-rotation.md)).
- 보안 위험이 있는 경우 잠재적으로 손상될 수 있는 TDE 보호기를 제거하는 방법을 알아봅니다([잠재적으로 손상될 수 있는 키 제거](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)). 

## <a name="prerequisites-for-cli"></a>CLI용 필수 구성 요소

- Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
- [권장되는 선택 사항] TDE 보호기 키 자료의 로컬 복사본을 만들기 위한 HSM(하드웨어 보안 모듈) 또는 로컬 키 저장소가 있어야 합니다.
- 명령줄 인터페이스 버전 2.0 이상. 최신 버전을 설치하고 Azure 구독에 연결하려면 [Azure 플랫폼 간 명령줄 인터페이스 2.0 설치 및 구성](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)을 참조하세요. 
- TDE에 사용할 Azure Key Vault 및 키를 만듭니다.
   - [CLI 2.0을 사용하여 Key Vault 관리](../key-vault/key-vault-manage-with-cli2.md)
   - [HSM(하드웨어 보안 모듈) 및 Key Vault 사용 지침](../key-vault/key-vault-get-started.md#HSM)
 - 키 자격 증명 모음에는 TDE에 사용할 다음 속성이 있어야 합니다.
   - [일시 삭제](../key-vault/key-vault-ovw-soft-delete.md)
   - [CLI로 Key Vault 일시 삭제를 사용하는 방법](../key-vault/key-vault-soft-delete-cli.md) 
- 키에는 TDE에 사용할 다음 특성이 있어야 합니다.
   - 만료 날짜 없음
   - 사용 안 함 없음
   - *가져오기*, *키 래핑*, *키 래핑 해제* 작업도 수행 가능
   
## <a name="step-1-create-a-server-and-assign-an-azure-ad-identity-to-your-server"></a>1단계. 서버 만들기 및 서버에 Azure AD ID 할당
      cli
      # create server (with identity) and database
      az sql server create -n "ServerName" -g "ResourceGroupName" -l "westus" -u "cloudsa" -p "YourFavoritePassWord99@34" -I 
      az sql db create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 
      

 
## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>2단계. 서버에 Key Vault 권한 부여
      cli
      # create key vault, key and grant permission
      az keyvault create -n "VaultName" -g "ResourceGroupName" 
      az keyvault key create -n myKey -p software --vault-name "VaultName" 
      az keyvault set-policy -n "VaultName" --object-id "ServerIdentityObjectId" -g "ResourceGroupName" --key-permissions wrapKey unwrapKey get list 
      

 
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>3단계. Key Vault 키를 서버에 추가하고 TDE 보호기를 설정합니다
  
     cli
     # add server key and update encryption protector
      az sql server key create -g "ResourceGroupName" -s "ServerName" -t "AzureKeyVault" -u "FullVersionedKeyUri 
      az sql server tde-key update -g "ResourceGroupName" -s "ServerName" -t AzureKeyVault -u "FullVersionedKeyUri" 
      
  
  > [!Note]
> 키 자격 증명 모음 이름과 키 이름을 결합한 길이는 94자를 초과할 수 없습니다.
> 

>[!Tip]
>Key Vault의 KeyId 예제: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
  
## <a name="step-4-turn-on-tde"></a>4단계. TDE 설정 
      cli
      # enable encryption
      az sql db tde create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" --status Enabled 
      

이제 데이터베이스 또는 데이터 웨어하우스에서 Key Vault에 있는 암호화 키를 사용하여 TDE를 사용할 수 있도록 설정되었습니다.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>5단계. 암호화 상태 및 암호화 작업 확인

     cli
      # get encryption scan progress
      az sql db tde show-activity -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

      # get whether encryption is on or off
      az sql db tde show-configuration -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

## <a name="sql-cli-references"></a>SQL CLI 참조

https://docs.microsoft.com/cli/azure/sql?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/tde-key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/db/tde?view=azure-cli-latest 

