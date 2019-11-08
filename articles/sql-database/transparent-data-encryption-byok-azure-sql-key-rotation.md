---
title: TDE 보호기 회전-PowerShell
description: Azure SQL Server에 대해 TDE(Transparent Data Encryption) 보호기를 회전하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 5bfcacb9348940e0b36947f6e4e0d27839de35bb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824675"
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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원하지만 모든 향후 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대 한 자세한 내용은 [AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)를 참조 하세요. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

- 이 방법 가이드에서는 이미 Azure SQL Database나 데이터 웨어하우스에 대해 Azure Key Vault의 키를 TDE 보호기로 사용하고 있다고 가정합니다. [BYOK 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-azure-sql.md)를 참조하세요.
- Azure PowerShell 설치 되 고 실행 중 이어야 합니다.
- [권장 선택 사항] HSM(하드웨어 보안 모듈)이나 로컬 키 저장소의 TDE 보호기에 대한 키 자료를 먼저 만들고 키 자료를 Azure Key Vault로 가져옵니다. [HSM(하드웨어 보안 모듈) 및 Key Vault 사용 지침](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)에 따라 자세히 알아봅니다.

## <a name="manual-key-rotation"></a>수동 키 회전

수동 키 회전은 [AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)및 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 하 여 새 키 이름 또는 또 다른 키를 사용 하는 완전히 새로운 키를 추가 합니다. 자격 증명 모음. 이 방법을 사용하면 고가용성 및 지리적으로 분산된 시나리오를 지원하기 위해 서로 다른 키 자격 증명 모음에 동일한 키를 추가할 수 있습니다.

>[!NOTE]
>키 자격 증명 모음 이름과 키 이름을 결합한 길이는 94자를 초과할 수 없습니다.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>다른 유용한 PowerShell cmdlet

- TDE 보호기를 Microsoft 관리에서 BYOK 모드로 전환 하려면 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 합니다.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- TDE 보호기를 BYOK 모드에서 Microsoft 관리로 전환 하려면 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 합니다.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>다음 단계

- 보안 위험이 있는 경우 잠재적으로 손상될 수 있는 TDE 보호기를 제거하는 방법을 알아봅니다([잠재적으로 손상될 수 있는 키 제거](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)). 

- [PowerShell을 사용 하 여 Key Vault에서 고유한 키를 사용 하 여 TDE](transparent-data-encryption-byok-azure-sql-configure.md) 에 대 한 Azure Key Vault 통합 및 Bring Your Own Key 지원 시작
