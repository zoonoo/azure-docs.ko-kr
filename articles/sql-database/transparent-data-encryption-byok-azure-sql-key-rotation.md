---
title: PowerShell - TDE 보호기 회전 - Azure SQL Database| Microsoft Docs
description: Azure SQL Server에 대해 TDE(Transparent Data Encryption) 보호기를 회전하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 08/07/2017
ms.openlocfilehash: 02f97b318be975f4ff24b4e72276776ebc30535c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52991965"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell을 사용하여 TDE(Transparent Data Encryption) 보호기 회전 

이 문서에서는 Azure Key Vault에서 TDE 보호기를 사용한 Azure SQL Server 키 회전에 대해 설명합니다. Azure SQL Server의 TDE 보호기 회전은 서버의 데이터베이스를 보호하는 새로운 비대칭 키로의 전환을 의미합니다. 키 회전은 온라인 작업이며, 전체 데이터베이스가 아닌 데이터베이스의 암호화 키를 해독했다 다시 암호화하는 작업이기 때문에 완료되는 데 몇 초밖에 걸리지 않습니다. 

이 가이드에서는 서버에서 TDE 보호기를 회전하는 두 가지 옵션을 설명합니다.

> [!NOTE]
> 일시 중지된 SQL Data Warehouse는 키 회전 전에 재개해야 합니다.
>

> [!IMPORTANT]
> 롤오버 후 키의 이전 버전은 **삭제하지 마세요**.  키가 롤오버될 때 구 데이터베이스 백업 같은 일부 데이터는 이전 키로 계속 암호화되어 있습니다. 
>

## <a name="prerequisites"></a>필수 조건

- 이 방법 가이드에서는 이미 Azure SQL Database나 데이터 웨어하우스에 대해 Azure Key Vault의 키를 TDE 보호기로 사용하고 있다고 가정합니다. [BYOK 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-azure-sql.md)를 참조하세요.
- Azure PowerShell 버전 3.7.0 이상이 설치되어 실행 중이어야 합니다. 
- [권장 선택 사항] HSM(하드웨어 보안 모듈)이나 로컬 키 저장소의 TDE 보호기에 대한 키 자료를 먼저 만들고 키 자료를 Azure Key Vault로 가져옵니다.  [HSM(하드웨어 보안 모듈) 및 Key Vault 사용 지침](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)에 따라 자세히 알아봅니다.

## <a name="option-1-auto-rotation"></a>옵션 1: 자동 회전

Key Vault에서 같은 키 이름과 키 자격 증명 모음을 사용하여 기존 TDE 보호기 키의 새 버전을 생성합니다. Azure SQL 서비스는 새 버전을 24시간 안에 사용하기 시작합니다. 

[Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) cmdlet을 사용하여 TDE 보호기의 새 버전을 만들려면

   ```powershell
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>
   ```

## <a name="option-2-manual-rotation"></a>옵션 2: 수동 회전

이 옵션에서는 [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey), [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) 및 [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) cmdlet을 사용하여 완전히 새로운 키를 추가합니다. 새로운 키 이름 또는 다른 키 자격 증명 모음을 사용할 수 있습니다. 

>[!NOTE]
>키 자격 증명 모음 이름과 키 이름을 결합한 길이는 94자를 초과할 수 없습니다.
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>다른 유용한 PowerShell cmdlet

- Microsoft 관리에서 BYOK 모드로 TDE를 전환하려면 [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) cmdlet을 사용합니다.

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- BYOK 모드에서 Microsoft 관리로 TDE를 전환하려면 [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) cmdlet을 사용합니다.

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>다음 단계

- 보안 위험이 있는 경우 잠재적으로 손상될 수 있는 TDE 보호기를 제거하는 방법을 알아봅니다. [잠재적으로 손상될 수 있는 키 제거](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- TDE에 대한 Bring Your Own Key 지원 시작: [PowerShell을 사용하여 Key Vault에서 고유 키로 TDE 설정](transparent-data-encryption-byok-azure-sql-configure.md)
