---
title: TDE 보호기 회전 (Azure CLI PowerShell &)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: PowerShell 및 Azure CLI를 사용 하 여 Azure SQL Database 및 Azure Synapse Analytics에서 사용 하는 Azure의 서버에 대 한 TDE (투명한 데이터 암호화) 보호기를 회전 하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 699434e89872b9525ea99883bc4fcbc86757ab8e
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619851"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>TDE (투명한 데이터 암호화) 보호기 회전
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


이 문서에서는 Azure Key Vault에서 TDE 보호기를 사용 하는 [서버의](logical-servers.md) 키 회전에 대해 설명 합니다. 서버에 대 한 논리 TDE 보호기를 회전 하는 것은 서버에서 데이터베이스를 보호 하는 새 비대칭 키로 전환 하는 것을 의미 합니다. 키 회전은 온라인 작업 이므로 완료 하는 데 몇 초 밖에 걸리지 않습니다 .이는 전체 데이터베이스가 아니라 데이터베이스의 데이터 암호화 키를 해독 하 고 다시 암호화 하기 때문입니다.

이 가이드에서는 서버에서 TDE 보호기를 회전하는 두 가지 옵션을 설명합니다.

> [!NOTE]
> 일시 중지 된 Azure Synapse Analytics SQL 풀은 키 회전 전에 다시 시작 해야 합니다.

> [!IMPORTANT]
> 롤오버 후에는 이전 버전의 키를 삭제 하지 마십시오. 키가 롤오버될 때 구 데이터베이스 백업 같은 일부 데이터는 이전 키로 계속 암호화되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 이 방법 가이드에서는 Azure Key Vault의 키를 Azure SQL Database 또는 Azure Synapse Analytics의 TDE 보호기로 이미 사용 한다고 가정 합니다. [BYOK 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-overview.md)를 참조하세요.
- Azure PowerShell 설치 되 고 실행 중 이어야 합니다.
- [권장 선택 사항] HSM(하드웨어 보안 모듈)이나 로컬 키 저장소의 TDE 보호기에 대한 키 자료를 먼저 만들고 키 자료를 Azure Key Vault로 가져옵니다. [HSM(하드웨어 보안 모듈) 및 Key Vault 사용 지침](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)에 따라 자세히 알아봅니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az 모듈 설치 지침은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. 특정 cmdlet에 대 한 자세한 내용은 [AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)를 참조 하세요.

> [!IMPORTANT]
> RM (PowerShell Azure Resource Manager) 모듈은 계속 지원 되지만 모든 향후 개발은 Az. Sql 모듈에 대 한 것입니다. AzureRM 모듈은 12 월 2020 일까 때까지 버그 수정을 계속 받습니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 호환성에 대 한 자세한 내용은 [새 Azure PowerShell Az Module 소개](/powershell/azure/new-azureps-module-az)를 참조 하세요.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

설치 [는 Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요.

* * *

## <a name="manual-key-rotation"></a>수동 키 회전

수동 키 회전은 다음 명령을 사용 하 여 새 키 이름 또는 다른 키 자격 증명 모음에 있을 수 있는 완전히 새로운 키를 추가 합니다. 이 방법을 사용하면 고가용성 및 지리적으로 분산된 시나리오를 지원하기 위해 서로 다른 키 자격 증명 모음에 동일한 키를 추가할 수 있습니다.

> [!NOTE]
> 키 자격 증명 모음 이름과 키 이름을 결합한 길이는 94자를 초과할 수 없습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)및 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 합니다.

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create), [az sql server key create](/cli/azure/sql/server/key#az-sql-server-key-create)및 [az sql server tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) 명령을 사용 합니다.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>스위치 TDE 보호기 모드

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- TDE 보호기를 Microsoft 관리에서 BYOK 모드로 전환 하려면 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 합니다.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- TDE 보호기를 BYOK 모드에서 Microsoft 관리로 전환 하려면 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 합니다.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 예에서는 [az sql server tde 키 집합](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)을 사용 합니다.

- TDE 보호기를 Microsoft 관리에서 BYOK 모드로 전환 하려면

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- TDE 보호기를 BYOK 모드에서 Microsoft 관리로 전환 하려면

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>다음 단계

- 보안 위험이 발생할 경우 잠재적으로 손상 된 TDE 보호기를 제거 하는 방법에 대해 알아보기: [잠재적으로 손상 된 키를 제거](transparent-data-encryption-byok-remove-tde-protector.md)합니다.

- [PowerShell을 사용 하 여 Key Vault에서 고유한 키를 사용 하 여 TDE](transparent-data-encryption-byok-configure.md)에 대 한 Azure Key Vault 통합 및 Bring Your Own Key 지원 시작.
