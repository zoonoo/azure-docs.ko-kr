---
title: Azure SQL Database 및 Data Warehouse에 대한 투명한 데이터 암호화 | Microsoft Docs
description: SQL Database 및 Data Warehouse에 대한 투명한 데이터 암호화를 간략히 설명하는 개요입니다. 이 문서에서는 이점 및 서비스 관리 투명한 데이터 암호화 및 Bring Your Own Key를 포함한 구성 옵션을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: becczhang
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 07/09/2018
ms.openlocfilehash: 50b433c65dec1f667f32aaf60148a6e393c67320
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165929"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>SQL Database 및 Data Warehouse에 대한 투명한 데이터 암호화

TDE(투명한 데이터 암호화)는 악의적인 활동의 위협으로부터 Azure SQL Database 및 Azure Data Warehouse를 보호하는 데 도움이 됩니다. 응용 프로그램에 대한 변경 없이 미사용 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행합니다. 기본적으로 TDE는 새로 배포된 모든 Azure SQL 데이터베이스에 대해 활성화됩니다. TDE는 SQL Database의 논리 **master** 데이터베이스를 암호화하는 데 사용할 수 없습니다.  **master** 데이터베이스에는 사용자 데이터베이스에서 TDE 작업을 수행하는 데 필요한 개체가 포함되어 있습니다.

이전 데이터베이스 또는 Azure SQL Data Warehouse에 대해서는 TDE를 수동으로 활성화해야 합니다.  

투명한 데이터 암호화는 데이터베이스 암호화 키라는 대칭 키를 사용하여 전체 데이터베이스의 저장소를 암호화합니다. 이 데이터베이스 암호화 키는 투명한 데이터 암호화 보호기에서 보호됩니다. 보호기는 서비스 관리 인증서(서비스 관리 투명한 데이터 암호화) 또는 Azure Key Vault에 저장된 비대칭 키(Bring Your Own Key)입니다. 투명한 데이터 암호화 보호기는 서버 수준에서 설정합니다. 

데이터베이스를 시작하면 암호화된 데이터베이스 암호화 키가 해독된 다음, SQL Server 데이터베이스 엔진 프로세스에서 데이터베이스 파일의 암호 해독 및 다시 암호화에 사용됩니다. 투명한 데이터 암호화는 페이지 수준에서 데이터의 실시간 I/O 암호화 및 해독을 수행합니다. 각 페이지는 메모리로 읽을 때 해독된 다음, 디스크에 쓰기 전에 암호화됩니다. 투명한 데이터 암호화에 대한 일반적인 설명은 [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)를 참조하세요.

Azure 가상 머신에서 실행되는 SQL Server는 Key Vault의 비대칭 키를 사용할 수도 있습니다. 구성 단계는 SQL Database에서 비대칭 키를 사용하는 것과 다릅니다. 자세한 내용은 [Azure Key Vault(SQL Server)를 사용한 확장 가능 키 관리](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)를 참조하세요.

## <a name="service-managed-transparent-data-encryption"></a>서비스 관리 투명한 데이터 암호화

Azure에서 투명한 데이터 암호화의 기본 설정은 기본 제공 서버 인증서에서 데이터베이스 암호화 키를 보호한다는 것입니다. 기본 제공 서버 인증서는 각 서버마다 고유합니다. 데이터베이스가 지역 복제 관계에 있는 경우 주 데이터베이스와 지역 보조 데이터베이스는 모두 주 데이터베이스의 부모 서버 키로 보호됩니다. 두 개의 데이터베이스가 동일한 서버에 연결되어 있으면 동일한 기본 제공 인증서를 공유합니다. Microsoft는 적어도 90일마다 이러한 인증서를 자동으로 회전합니다.

또한 Microsoft는 지역 복제 및 복원에 필요한 대로 키를 원활하게 이동하고 관리합니다. 

> [!IMPORTANT]
> 새로 만든 모든 SQL 데이터베이스는 기본적으로 서비스 관리 투명한 데이터 암호화를 사용하여 암호화됩니다. 2017년 5월 이전의 기존 데이터베이스와 복원, 지역 복제 및 데이터베이스 복사를 통해 만들어진 데이터베이스는 기본적으로 암호화되지 않습니다.
>

## <a name="bring-your-own-key"></a>Bring Your Own Key

Bring Your Own Key 지원을 사용하면 투명한 데이터 암호화 키뿐만 아니라 액세스할 수 있는 사용자와 시기도 제어할 수 있습니다. Azure 클라우드 기반의 외부 키 관리 시스템인 Key Vault는 투명한 데이터 암호화가 Bring Your Own Key 지원과 통합된 최초의 키 관리 서비스입니다. 데이터베이스 암호화 키는 Bring Your Own Key 지원을 통해 Key Vault에 저장된 비대칭 키로 보호됩니다. 비대칭 키는 Key Vault를 벗어나지 않습니다. 키 자격 증명 모음에 대한 권한이 서버에 있으면 해당 서버에서 Key Vault를 통해 기본 키 작업 요청을 보냅니다. 비대칭 키는 서버 수준에서 설정합니다. 그러면 해당 서버 아래의 모든 데이터베이스에서 이 키를 상속합니다.

이제는 Bring Your Own Key 지원을 사용하여 키 회전 및 키 자격 증명 모음 권한과 같은 키 관리 작업을 제어할 수 있습니다. 또한 키를 삭제할 수 있고, 모든 암호화 키에 대한 감사/보고를 사용하도록 설정할 수도 있습니다. Key Vault는 중앙 키 관리를 제공하며, 긴밀하게 모니터링되는 하드웨어 보안 모듈을 사용합니다. Key Vault는 키와 데이터 관리의 분리를 촉진하여 규정을 준수할 수 있도록 합니다. Key Vault에 대한 자세한 내용은 [Key Vault 설명서 페이지](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)를 참조하세요.

SQL Database 및 Data Warehouse에 Bring Your Own Key 지원을 사용하는 투명한 데이터 암호화에 대한 자세한 내용은 [Bring Your Own Key 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-azure-sql.md)를 참조하세요.

Bring Your Own Key 지원을 통해 투명한 데이터 암호화의 사용을 시작하려면 [PowerShell을 통해 Key Vault에서 사용자 고유 키를 사용하여 투명한 데이터 암호화를 설정하는 방법](transparent-data-encryption-byok-azure-sql-configure.md) 가이드를 참조하세요.

## <a name="move-a-transparent-data-encryption-protected-database"></a>투명한 데이터 암호화로 보호된 데이터베이스 이동

Azure 내에서 작업용 데이터베이스를 해독할 필요가 없습니다. 원본 데이터베이스 또는 주 데이터베이스에 대한 투명한 데이터 암호화 설정은 대상에서 투명하게 상속됩니다. 포함되는 작업은 다음과 같습니다.
- 지역 복원
- 셀프 서비스 특정 시점 복원
- 삭제된 데이터베이스 복원
- 활성 지역 복제
- 데이터베이스 복사본 만들기

투명한 데이터 암호화로 보호된 데이터베이스를 내보내는 경우 내보내는 데이터베이스의 콘텐츠는 암호화되지 않습니다. 이 내보낸 콘텐츠는 암호화되지 않은 BACPAC 파일에 저장됩니다. BACPAC 파일을 적절히 보호하고, 새 데이터베이스 가져오기가 완료되면 투명한 데이터 암호화를 사용하도록 설정합니다.

예를 들어 온-프레미스 SQL Server 인스턴스에서 BACPAC 파일을 내보낸 경우 가져온 새 데이터베이스의 콘텐츠가 자동으로 암호화되지 않습니다. 마찬가지로 BACPAC 파일을 온-프레미스 SQL Server 인스턴스로 내보낸 경우 새 데이터베이스도 자동으로 암호화되지 않습니다.

한 가지 예외는 SQL Database 간에 내보내는 경우입니다. 새 데이터베이스에서 투명한 데이터 암호화를 사용하도록 설정되지만 BACPAC 파일 자체는 여전히 암호화되지 않습니다.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Azure Portal에서 투명한 데이터 암호화 관리

Azure Portal을 통해 투명한 데이터 암호화를 구성하려면 사용자가 Azure 소유자, 기여자 또는 SQL 보안 관리자 권한으로 연결되어야 합니다. 

투명한 데이터 암호화는 데이터베이스 수준에서 설정합니다. 데이터베이스에서 투명한 데이터 암호화를 사용하도록 설정하려면 [Azure Portal](https://portal.azure.com)로 이동하여 Azure 관리자 또는 기여자 계정으로 로그인합니다. 사용자 데이터베이스 아래에서 투명한 데이터 암호화 설정을 찾습니다. 기본적으로 서비스 관리 투명한 데이터 암호화를 사용하도록 설정되어 있습니다. 투명한 데이터 암호화 인증서는 데이터베이스가 포함된 서버에 대해 자동으로 생성됩니다. 

![서비스 관리 투명한 데이터 암호화](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

투명한 데이터 암호화 보호기라고도 하는 투명한 데이터 암호화 마스터 키는 서버 수준에서 설정합니다. Bring Your Own Key 지원을 통해 투명한 데이터 암호화를 사용하고 Key Vault의 키를 사용하여 데이터베이스를 보호하려면 서버에 속한 투명한 데이터 암호화 설정을 참조하세요. 

![Bring Your Own Key 지원을 통한 투명한 데이터 암호화](./media/transparent-data-encryption-azure-sql/tde-byok-support.png) 

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>PowerShell을 통한 투명한 데이터 암호화 관리

PowerShell을 통해 투명한 데이터 암호화를 구성하려면 사용자가 Azure 소유자, 기여자 또는 SQL 보안 관리자 권한으로 연결되어야 합니다. 

| Cmdlet | 설명 |
| --- | --- |
| [Set-AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) |데이터베이스에 대한 투명한 데이터 암호화를 사용하거나 사용하지 않도록 설정합니다.|
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) |데이터베이스에 대한 투명한 데이터 암호화 상태를 가져옵니다. |
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption-Activity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) |데이터베이스에 대한 암호화 진행률을 확인합니다. |
| [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) |SQL Server 인스턴스에 Key Vault 키를 추가합니다. |
| [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) |SQL Server 인스턴스에 대한 Key Vault 키를 가져옵니다.  |
| [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) |SQL Server 인스턴스에 대한 투명한 데이터 암호화 보호기를 설정합니다. |
| [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) |투명한 데이터 암호화 보호기를 가져옵니다. |
| [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) |SQL Server 인스턴스에서 Key Vault 키를 제거합니다. |
|  | |

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Transact-SQL을 통한 투명한 데이터 암호화 관리

master 데이터베이스에서 **dbmanager** 역할의 관리자 또는 구성원으로 로그인하여 데이터베이스에 연결합니다.

| 명령 | 설명 |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF는 데이터베이스를 암호화하거나 해독합니다. |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |데이터베이스 및 연결된 해당 데이터베이스 암호화 키의 암호화 상태에 대한 정보를 반환합니다. |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |각 데이터 웨어하우스 노드 및 연결된 해당 데이터베이스 암호화 키의 암호화 상태에 대한 정보를 반환합니다. | 
|  | |

투명한 데이터 암호화 보호기는 Transact-SQL을 사용하여 Key Vault의 키로 전환할 수 없습니다. 대신 PowerShell 또는 Azure Portal을 사용합니다.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>REST API를 통한 투명한 데이터 암호화 관리
 
REST API를 통해 투명한 데이터 암호화를 구성하려면 사용자가 Azure 소유자, 기여자 또는 SQL 보안 관리자 권한으로 연결되어야 합니다. 

| 명령 | 설명 |
| --- | --- |
|[서버 만들기 또는 업데이트](/rest/api/sql/servers/createorupdate)|SQL Server 인스턴스에 Azure Active Directory ID(Key Vault에 대한 액세스 권한을 부여하는 데 사용)를 추가합니다.|
|[서버 키 만들기 또는 업데이트](/rest/api/sql/serverkeys/createorupdate)|SQL Server 인스턴스에 Key Vault 키를 추가합니다.|
|[서버 키 삭제](/rest/api/sql/serverkeys/delete)|SQL Server 인스턴스에서 Key Vault 키를 제거합니다.|
|[서버 키 가져오기](/rest/api/sql/serverkeys/get)|SQL Server 인스턴스에서 특정 Key Vault 키를 가져옵니다.|
|[서버별 서버 키 나열](/rest/api/sql/serverkeys/listbyserver)|SQL Server 인스턴스에 대한 Key Vault 키를 가져옵니다. |
|[암호화 보호기 만들기 또는 업데이트](/rest/api/sql/encryptionprotectors/createorupdate)|SQL Server 인스턴스에 대한 투명한 데이터 암호화 보호기를 설정합니다.|
|[암호화 보호기 가져오기](/rest/api/sql/encryptionprotectors/get)|SQL Server 인스턴스에 대한 투명한 데이터 암호화 보호기를 가져옵니다.|
|[서버별 암호화 보호기 나열](/rest/api/sql/encryptionprotectors/listbyserver)|SQL Server 인스턴스에 대한 투명한 데이터 암호화 보호기를 가져옵니다. |
|[투명한 데이터 암호화 구성 만들기 또는 업데이트](/rest/api/sql/transparentdataencryptions/createorupdate)|데이터베이스에 대한 투명한 데이터 암호화를 사용하거나 사용하지 않도록 설정합니다.|
|[투명한 데이터 암호화 구성 가져오기](/rest/api/sql/transparentdataencryptions/get)|데이터베이스에 대한 투명한 데이터 암호화 구성을 가져옵니다.|
|[투명한 데이터 암호화 구성 결과 나열](/rest/api/sql/transparentdataencryptionactivities/ListByConfiguration)|데이터베이스에 대한 암호화 결과를 가져옵니다.|

## <a name="next-steps"></a>다음 단계

- 투명한 데이터 암호화에 대한 일반적인 설명은 [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)를 참조하세요.
- SQL Database 및 Data Warehouse에 Bring Your Own Key 지원을 사용하는 투명한 데이터 암호화에 대한 자세한 내용은 [Bring Your Own Key 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-azure-sql.md)를 참조하세요.
- Bring Your Own Key 지원을 통해 투명한 데이터 암호화의 사용을 시작하려면 [PowerShell을 통해 Key Vault에서 사용자 고유 키를 사용하여 투명한 데이터 암호화를 설정하는 방법](transparent-data-encryption-byok-azure-sql-configure.md) 가이드를 참조하세요.
- Key Vault에 대한 자세한 내용은 [Key Vault 설명서 페이지](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)를 참조하세요.
