---
title: 투명한 데이터 암호화
description: Azure 시냅스 분석에서 SQL 데이터베이스 및 시냅스 SQL에 대한 투명한 데이터 암호화개요입니다. 이 문서에서는 이점 및 서비스 관리 투명한 데이터 암호화 및 Bring Your Own Key를 포함한 구성 옵션을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/06/2020
ms.openlocfilehash: 982a59f1eb8717e2fe2d86728cdae731c919aaf0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476959"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>SQL 데이터베이스 및 Azure 시냅스를 위한 투명한 데이터 암호화

투명 데이터 암호화(TDE)는 Azure SQL Database, Azure SQL 관리 인스턴스 및 Azure Synapse 분석에서 미사용 데이터를 암호화하여 악의적인 오프라인 활동의 위협으로부터 Sql을 보호하는 데 도움이 됩니다. 이 기능은 애플리케이션을 변경할 필요 없이 미사용 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행합니다. 기본적으로 TDE는 새로 배포된 모든 Azure SQL 데이터베이스에 대해 활성화됩니다. TDE는 SQL Database의 논리 **master** 데이터베이스를 암호화하는 데 사용할 수 없습니다.  **master** 데이터베이스에는 사용자 데이터베이스에서 TDE 작업을 수행하는 데 필요한 개체가 포함되어 있습니다.

Azure SQL Database, Azure SQL 관리 인스턴스 또는 Azure Synapse의 이전 데이터베이스에 대해 TDE를 수동으로 사용하도록 설정해야 합니다.
복원을 통해 생성된 관리되는 인스턴스 데이터베이스는 원본 데이터베이스에서 암호화 상태를 상속합니다.

투명한 데이터 암호화는 데이터베이스 암호화 키라는 대칭 키를 사용하여 전체 데이터베이스의 스토리지를 암호화합니다. 이 데이터베이스 암호화 키는 투명한 데이터 암호화 보호기에서 보호됩니다. 보호기는 서비스 관리 인증서(서비스 관리 투명한 데이터 암호화) 또는 Azure Key Vault에 저장된 비대칭 키(Bring Your Own Key)입니다. Azure SQL Database 및 Azure Synapse의 서버 수준에서 투명 한 데이터 암호화 보호기를 설정 하 고 Azure SQL 관리 인스턴스에 대 한 인스턴스 수준입니다. *서버*라는 용어는 달리 언급하지 않는 한, 이 문서 전체에서 서버와 인스턴스를 모두 나타냅니다.

데이터베이스를 시작하면 암호화된 데이터베이스 암호화 키가 해독된 다음, SQL Server 데이터베이스 엔진 프로세스에서 데이터베이스 파일의 암호 해독 및 다시 암호화에 사용됩니다. 투명한 데이터 암호화는 페이지 수준에서 데이터의 실시간 I/O 암호화 및 해독을 수행합니다. 각 페이지는 메모리로 읽을 때 해독된 다음, 디스크에 쓰기 전에 암호화됩니다. 투명 데이터 암호화에 대한 일반적인 설명은 [투명 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)를 참조하십시오.

Azure 가상 머신에서 실행되는 SQL Server는 Key Vault의 비대칭 키를 사용할 수도 있습니다. 구성 단계는 SQL Database 및 SQL Managed Instance에서 비대칭 키를 사용하는 것과 다릅니다. 자세한 내용은 [Azure Key Vault(SQL Server)를 사용한 확장 가능 키 관리](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)를 참조하세요.

## <a name="service-managed-transparent-data-encryption"></a>서비스 관리 투명한 데이터 암호화

Azure에서 투명한 데이터 암호화의 기본 설정은 기본 제공 서버 인증서에서 데이터베이스 암호화 키를 보호한다는 것입니다. 기본 제공 서버 인증서는 각 서버에 대해 고유하며 사용되는 암호화 알고리즘은 AES 256입니다. 데이터베이스가 지역 복제 관계에 있는 경우 주 데이터베이스와 지역 보조 데이터베이스는 모두 주 데이터베이스의 부모 서버 키로 보호됩니다. 두 개의 데이터베이스가 동일한 서버에 연결되어 있으면 동일한 기본 제공 인증서도 공유합니다.  Microsoft는 내부 보안 정책에 따라 이러한 인증서를 자동으로 회전하며, 루트 키는 Microsoft 내부 비밀 저장소를 통해 보호됩니다.  고객은 [Microsoft 신뢰 센터에서](https://servicetrust.microsoft.com/)사용할 수 있는 독립적인 타사 감사 보고서에서 SQL Database내부 보안 정책을 준수하는지 확인할 수 있습니다.

또한 Microsoft는 지역 복제 및 복원에 필요한 대로 키를 원활하게 이동하고 관리합니다.

> [!IMPORTANT]
> 새로 생성된 모든 SQL 데이터베이스와 관리되는 인스턴스 데이터베이스는 기본적으로 서비스 관리되는 투명한 데이터 암호화를 사용하여 암호화됩니다. 2017년 5월 이전에 생성된 기존 SQL 데이터베이스와 복원, 지역 복제 및 데이터베이스 복사본을 통해 생성된 SQL 데이터베이스는 기본적으로 암호화되지 않습니다. 2019년 2월 이전에 생성된 기존 관리형 인스턴스 데이터베이스는 기본적으로 암호화되지 않습니다. 복원을 통해 생성된 관리되는 인스턴스 데이터베이스는 원본에서 암호화 상태를 상속합니다.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>고객 관리형 투명한 데이터 암호화 - Bring Your Own Key

[Azure Key Vault에서 고객 관리형 키와 TDE(투명한 데이터 암호화)](transparent-data-encryption-byok-azure-sql.md)를 통해 TDE 보호기라는 고객 관리형 비대칭 키를 사용하여 DEK(데이터베이스 암호화 키)를 암호화할 수 있습니다.  일반적으로 투명한 데이터 암호화를 위한 BYOK(Bring Your Own Key) 지원이라고도 합니다. BYOK 시나리오에서 TDE 프로텍터는 Azure의 클라우드 기반 외부 키 관리 시스템인 고객 소유 및 관리되는 [Azure Key Vault에](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)저장됩니다. TDE 프로텍터는 [키 자격 증명 모음에서 생성하거나](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) 온프레미스 HSM 장치에서 키 자격 증명 모음으로 전송할 수 있습니다. 데이터베이스의 부팅 페이지에 저장되는 TDE DEK는 TDE 보호기를 통해 암호화 및 암호 해독되고, 보호기는 Azure Key Vault에 저장되며 절대 Key Vault를 벗어나지 않습니다.  SQL Database가 DEK를 암호화 및 암호 해독하려면 고객 소유의 Key Vault에 대한 권한이 부여되어야 합니다. Key Vault에 대한 논리 SQL 서버의 권한이 철회되면 데이터베이스에 액세스할 수 없게 되고 모든 데이터가 암호화됩니다. Azure SQL Database의 경우 TDE 보호기는 논리 SQL 서버 수준에서 설정되며 해당 서버와 연결된 모든 데이터베이스에서 상속됩니다. [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)의 경우 TDE 보호기는 인스턴스 수준에서 설정되며 해당 인스턴스의 모든 *암호화된* 데이터베이스에 의해 상속됩니다. *서버*라는 용어는 달리 언급하지 않는 한, 이 문서 전체에서 서버와 인스턴스를 모두 나타냅니다.

TDE와 Azure Key Vault가 통합되면 사용자는 키 회전, Key Vault 권한, 키 백업을 포함한 키 관리 작업을 제어하고, Azure Key Vault 기능을 사용하여 모든 TDE 보호기에 감사/보고를 사용하도록 설정할 수 있습니다. Key Vault는 중앙 집중식 키 관리를 제공하고, 철저하게 모니터링되는 HSM(하드웨어 보안 모듈)을 활용하고, 보안 정책 규정을 준수하도록 키 관리와 데이터 관리의 책임을 분리합니다.
Azure SQL Database, SQL 관리 인스턴스 및 Azure Synapse에 대한 Azure Key Vault 통합(사용자 고유의 키 지원 가져오기)을 사용한 투명 데이터 암호화에 대해 자세히 알아보려면 [Azure Key Vault 통합을 사용한 투명 데이터 암호화를](transparent-data-encryption-byok-azure-sql.md)참조하십시오.

Azure Key Vault 통합(Bring Your Own Key 지원)을 통해 투명한 데이터 암호화 사용을 시작하려면 [PowerShell을 통해 Key Vault에서 사용자 고유 키를 사용하여 투명한 데이터 암호화를 설정하는 방법](transparent-data-encryption-byok-azure-sql-configure.md) 가이드를 참조하세요.

## <a name="move-a-transparent-data-encryption-protected-database"></a>투명한 데이터 암호화로 보호된 데이터베이스 이동

Azure 내에서 작업용 데이터베이스를 해독할 필요가 없습니다. 원본 데이터베이스 또는 주 데이터베이스에 대한 투명한 데이터 암호화 설정은 대상에서 투명하게 상속됩니다. 포함되는 작업은 다음과 같습니다.

- 지리적 복원
- 셀프 서비스 특정 시점 복원
- 삭제된 데이터베이스 복원
- 활성 지리적 복제
- 데이터베이스 복사본 만들기
- Azure SQL Managed Instance에 백업 파일 복원

> [!IMPORTANT]
> 암호화에 사용되는 인증서에 액세스할 수 없으므로 Azure SQL Managed Instance에서는 서비스 관리 TDE로 암호화된 데이터베이스에 대한 COPY-ONLY(복사 전용) 백업을 수동으로 수행할 수 없습니다. 특정 시점 복원 기능을 사용하여 이 유형의 데이터베이스를 다른 Managed Instance로 이동합니다.

투명한 데이터 암호화로 보호된 데이터베이스를 내보내는 경우 내보내는 데이터베이스의 콘텐츠는 암호화되지 않습니다. 이 내보낸 콘텐츠는 암호화되지 않은 BACPAC 파일에 저장됩니다. BACPAC 파일을 적절히 보호하고, 새 데이터베이스 가져오기가 완료되면 투명한 데이터 암호화를 사용하도록 설정합니다.

예를 들어 온-프레미스 SQL Server 인스턴스에서 BACPAC 파일을 내보낸 경우 가져온 새 데이터베이스의 콘텐츠가 자동으로 암호화되지 않습니다. 마찬가지로 BACPAC 파일을 온-프레미스 SQL Server 인스턴스로 내보낸 경우 새 데이터베이스도 자동으로 암호화되지 않습니다.

한 가지 예외는 SQL Database 간에 내보내는 경우입니다. 새 데이터베이스에서 투명한 데이터 암호화를 사용하도록 설정되지만 BACPAC 파일 자체는 여전히 암호화되지 않습니다.


## <a name="manage-transparent-data-encryption"></a>투명한 데이터 암호화 관리
# <a name="portal"></a>[포털](#tab/azure-portal)
Azure 포털에서 투명한 데이터 암호화를 관리합니다.

Azure Portal을 통해 투명한 데이터 암호화를 구성하려면 사용자가 Azure 소유자, 기여자 또는 SQL 보안 관리자 권한으로 연결되어야 합니다.

데이터베이스 수준에서 투명한 데이터 암호화를 설정 및 해제합니다. 데이터베이스에서 투명한 데이터 암호화를 사용하도록 설정하려면 [Azure Portal](https://portal.azure.com)로 이동하여 Azure 관리자 또는 기여자 계정으로 로그인합니다. 사용자 데이터베이스 아래에서 투명한 데이터 암호화 설정을 찾습니다. 기본적으로 서비스 관리 투명한 데이터 암호화를 사용하도록 설정되어 있습니다. 투명한 데이터 암호화 인증서는 데이터베이스가 포함된 서버에 대해 자동으로 생성됩니다. Azure SQL Managed Instance의 경우 T-SQL을 사용하여 데이터베이스에서 투명한 데이터 암호화를 설정 및 해제합니다.

![서비스 관리 투명한 데이터 암호화](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)

투명한 데이터 암호화 보호기라고도 하는 투명한 데이터 암호화 마스터 키는 서버 수준에서 설정합니다. Bring Your Own Key 지원을 통해 투명한 데이터 암호화를 사용하고 Key Vault의 키를 사용하여 데이터베이스를 보호하려면 서버에 속한 투명한 데이터 암호화 설정을 여세요.

![Bring Your Own Key 지원을 통한 투명한 데이터 암호화](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용하여 투명한 데이터 암호화를 관리합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 리소스 관리자 모듈은 Azure SQL Database에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈용입니다. 이러한 cmdlet에 대 한 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조 합니다. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 거의 동일합니다.

PowerShell을 통해 투명한 데이터 암호화를 구성하려면 사용자가 Azure 소유자, 기여자 또는 SQL 보안 관리자 권한으로 연결되어야 합니다.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Azure SQL 데이터베이스 및 Azure 시냅스용 Cmdlet

Azure SQL 데이터베이스 및 Azure 시냅스에 대해 다음 cmdlet을 사용합니다.

| Cmdlet | Description |
| --- | --- |
| [세트-아즈Sql데이터베이스투명데이터암호화](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |데이터베이스에 대한 투명한 데이터 암호화를 사용하거나 사용하지 않도록 설정합니다.|
| [받기-AzSql데이터베이스투명데이터암호화](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |데이터베이스에 대한 투명한 데이터 암호화 상태를 가져옵니다. |
| [받기-AzSql데이터베이스투명데이터암호화활동](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |데이터베이스에 대한 암호화 진행률을 확인합니다. |
| [추가 아즈SqlServer키볼트키](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |SQL Server 인스턴스에 Key Vault 키를 추가합니다. |
| [겟-아즈Sql서버키볼트키](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Azure SQL Database 서버의 Key Vault 키를 가져옵니다.  |
| [세트-아즈SqlServer투명데이터암호화보호기](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |SQL Server 인스턴스에 대한 투명한 데이터 암호화 보호기를 설정합니다. |
| [Get-AzSqlServer투명데이터암호화보호기](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |투명한 데이터 암호화 보호기를 가져옵니다. |
| [제거-아즈Sql서버키볼트키](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |SQL Server 인스턴스에서 Key Vault 키를 제거합니다. |
|  | |

> [!IMPORTANT]
> Azure SQL Managed Instance의 경우 T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) 명령을 사용하여 데이터베이스 수준에서 투명한 데이터 암호화를 설정 및 해제하고 [샘플 PowerShell 스크립트](transparent-data-encryption-byok-azure-sql-configure.md)를 확인하여 인스턴스 수준에서 투명한 데이터 암호화를 관리합니다.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Transact-SQL을 사용하여 투명한 데이터 암호화를 관리합니다.

master 데이터베이스에서 **dbmanager** 역할의 관리자 또는 구성원으로 로그인하여 데이터베이스에 연결합니다.

| 명령 | Description |
| --- | --- |
| [ALTER DATABASE(Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF는 데이터베이스를 암호화하거나 해독합니다. |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |데이터베이스 및 연결된 해당 데이터베이스 암호화 키의 암호화 상태에 대한 정보를 반환합니다. |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |각 데이터 웨어하우스 노드 및 연결된 해당 데이터베이스 암호화 키의 암호화 상태에 대한 정보를 반환합니다. |
|  | |

투명한 데이터 암호화 보호기는 Transact-SQL을 사용하여 Key Vault의 키로 전환할 수 없습니다. 대신 PowerShell 또는 Azure Portal을 사용합니다.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
REST API를 사용하여 투명한 데이터 암호화를 관리합니다.

REST API를 통해 투명한 데이터 암호화를 구성하려면 사용자가 Azure 소유자, 기여자 또는 SQL 보안 관리자 권한으로 연결되어야 합니다.
Azure SQL 데이터베이스 및 Azure 시냅스에 대한 다음 명령 집합을 사용합니다.

| 명령 | Description |
| --- | --- |
|[서버 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|SQL Server 인스턴스에 Azure Active Directory ID(Key Vault에 대한 액세스 권한을 부여하는 데 사용)를 추가합니다.|
|[서버 키 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|SQL Server 인스턴스에 Key Vault 키를 추가합니다.|
|[서버 키 삭제](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|SQL Server 인스턴스에서 Key Vault 키를 제거합니다.|
|[서버 키 가져오기](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|SQL Server 인스턴스에서 특정 Key Vault 키를 가져옵니다.|
|[서버별 서버 키 나열](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|SQL Server 인스턴스에 대한 Key Vault 키를 가져옵니다. |
|[암호화 보호기 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|SQL Server 인스턴스에 대한 투명한 데이터 암호화 보호기를 설정합니다.|
|[암호화 보호기 가져오기](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|SQL Server 인스턴스에 대한 투명한 데이터 암호화 보호기를 가져옵니다.|
|[서버별 암호화 보호기 나열](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|SQL Server 인스턴스에 대한 투명한 데이터 암호화 보호기를 가져옵니다. |
|[투명한 데이터 암호화 구성 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|데이터베이스에 대한 투명한 데이터 암호화를 사용하거나 사용하지 않도록 설정합니다.|
|[투명한 데이터 암호화 구성 가져오기](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|데이터베이스에 대한 투명한 데이터 암호화 구성을 가져옵니다.|
|[투명한 데이터 암호화 구성 결과 나열](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|데이터베이스에 대한 암호화 결과를 가져옵니다.|

## <a name="next-steps"></a>다음 단계

- 투명 데이터 암호화에 대한 일반적인 설명은 [투명 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)를 참조하십시오.
- Azure SQL Database, Azure SQL 관리 인스턴스 및 Azure Synapse에 대한 사용자 고유의 키 지원을 사용하여 투명 데이터 암호화에 대해 자세히 알아보려면 [사용자 고유의 키 지원을 가져오는 투명 데이터 암호화를](transparent-data-encryption-byok-azure-sql.md)참조하십시오.
- Bring Your Own Key 지원을 통해 투명한 데이터 암호화의 사용을 시작하려면 [PowerShell을 통해 Key Vault에서 사용자 고유 키를 사용하여 투명한 데이터 암호화를 설정하는 방법](transparent-data-encryption-byok-azure-sql-configure.md) 가이드를 참조하세요.
- Key Vault에 대한 자세한 내용은 [Key Vault 설명서 페이지](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)를 참조하세요.
