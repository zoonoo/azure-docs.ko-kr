---
title: 회전 TDE 프로텍터 - 파워쉘
description: Azure SQL Server에 대해 TDE(Transparent Data Encryption) 보호기를 회전하는 방법을 알아봅니다.
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
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067180"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell을 사용하여 TDE(Transparent Data Encryption) 보호기 회전

이 문서에서는 Azure Key Vault에서 TDE 보호기를 사용한 Azure SQL Server 키 회전에 대해 설명합니다. Azure SQL 서버의 TDE 보호기를 회전하는 것은 서버의 데이터베이스를 보호하는 새 비대칭 키로 전환하는 것을 의미합니다. 키 회전은 온라인 작업이므로 전체 데이터베이스가 아닌 데이터베이스의 데이터 암호화 키만 해독하고 다시 암호화하기 때문에 완료하는 데 몇 초밖에 걸리지 않습니다.

이 가이드에서는 서버에서 TDE 보호기를 회전하는 두 가지 옵션을 설명합니다.

> [!NOTE]
> 일시 중지된 SQL Data Warehouse는 키 회전 전에 재개해야 합니다.

> [!IMPORTANT]
> 롤오버 후 이전 버전의 키를 삭제하지 마십시오. 키가 롤오버될 때 구 데이터베이스 백업 같은 일부 데이터는 이전 키로 계속 암호화되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 이 방법 가이드에서는 이미 Azure SQL Database나 데이터 웨어하우스에 대해 Azure Key Vault의 키를 TDE 보호기로 사용하고 있다고 가정합니다. [BYOK 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-azure-sql.md)를 참조하세요.
- Azure PowerShell을 설치하고 실행중이어야 합니다.
- [권장 선택 사항] HSM(하드웨어 보안 모듈)이나 로컬 키 저장소의 TDE 보호기에 대한 키 자료를 먼저 만들고 키 자료를 Azure Key Vault로 가져옵니다. [HSM(하드웨어 보안 모듈) 및 Key Vault 사용 지침](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)에 따라 자세히 알아봅니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Az 모듈 설치 지침은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. 특정 cmdlet에 대 한 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조하십시오.

> [!IMPORTANT]
> PowerShell Azure 리소스 관리자(RM) 모듈은 Azure SQL Database에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈용입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 받을 것입니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 거의 동일합니다. 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개를](/powershell/azure/new-azureps-module-az)참조하십시오.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

설치에 대 한 [참조 Azure CLI 설치](/cli/azure/install-azure-cli)

* * *

## <a name="manual-key-rotation"></a>수동 키 회전

수동 키 회전은 다음 명령을 사용하여 새 키 이름 또는 다른 키 자격 증명 모음 아래에 있을 수 있는 완전히 새 키를 추가합니다. 이 방법을 사용하면 고가용성 및 지리적으로 분산된 시나리오를 지원하기 위해 서로 다른 키 자격 증명 모음에 동일한 키를 추가할 수 있습니다.

> [!NOTE]
> 키 자격 증명 모음 이름과 키 이름을 결합한 길이는 94자를 초과할 수 없습니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[Add-AzKeyVaultKey,](/powershell/module/az.keyvault/Add-AzKeyVaultKey) [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)및 [Set-AzSqlServer투명데이터 암호화 보호기](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용합니다.

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

az [키 볼트 만들기,](/cli/azure/keyvault/key#az-keyvault-key-create) [az sql 서버 키 만들기](/cli/azure/sql/server/key#az-sql-server-key-create)및 az sql 서버 [tde-키 집합](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) 명령을 사용합니다.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>유용한 PowerShell cmdlet

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

- TDE 프로텍터를 Microsoft관리 에서 BYOK 모드로 전환하려면 [Set-AzSqlServerTransparentData암호화 보호기](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용합니다.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- TDE 프로텍터를 BYOK 모드에서 Microsoft 관리로 전환하려면 [Set-AzSqlServerTransparentData암호화 보호기](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용합니다.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 예제는 [az sql 서버 tde 키 집합을](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)사용합니다.

- TDE 프로텍터를 Microsoft 관리 에서 BYOK 모드로 전환하려면

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- TDE 프로텍터를 BYOK 모드에서 Microsoft 관리로 전환하려면

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>다음 단계

- 보안 위험이 있는 경우 잠재적으로 손상될 수 있는 TDE 보호기를 제거하는 방법을 알아봅니다([잠재적으로 손상될 수 있는 키 제거](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)).

- Azure 키 볼트 통합을 시작하고 TDE에 대한 사용자 고유의 키 지원 가져오기: [PowerShell을 사용하여 키 볼트에서 자신의 키를 사용하여 TDE켜기](transparent-data-encryption-byok-azure-sql-configure.md)
