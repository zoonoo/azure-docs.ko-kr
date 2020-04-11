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
ms.date: 04/10/2020
ms.openlocfilehash: 87abdb37ff7773b0205a2ce3ee21689a10c459d7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113544"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>SQL 데이터베이스 및 Azure 시냅스를 위한 투명한 데이터 암호화

[투명 데이터 암호화(TDE)는](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) Azure SQL Database, Azure SQL 관리 인스턴스 및 Azure Synapse 분석에서 미사용 데이터를 암호화하여 악의적인 오프라인 활동의 위협으로부터 Sql을 보호하는 데 도움이 됩니다. 이 기능은 애플리케이션을 변경할 필요 없이 미사용 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행합니다. 기본적으로 TDE는 새로 배포된 모든 Azure SQL 데이터베이스에 대해 활성화되며 Azure SQL Database, Azure SQL 관리 인스턴스 또는 Azure Synapse의 이전 데이터베이스에 대해 수동으로 사용하도록 설정해야 합니다.

TDE를 통해 페이지 수준에서 데이터의 실시간 I/O 암호화 및 암호 해독을 수행합니다. 각 페이지는 메모리로 읽을 때 해독된 다음, 디스크에 쓰기 전에 암호화됩니다. TDE는 DEK(데이터베이스 암호화 키)라는 대칭 키를 사용하여 전체 데이터베이스의 저장소를 암호화합니다. 데이터베이스 시작 시 암호화된 DEK가 해독된 다음 SQL Server 데이터베이스 엔진 프로세스에서 데이터베이스 파일의 암호 해독 및 재암호화에 사용됩니다. DEK는 TDE 프로텍터에 의해 보호됩니다. TDE 프로텍터는 서비스 관리 인증서(서비스 관리 투명 데이터 암호화) 또는 [Azure Key Vault에](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) 저장된 비대칭 키(고객 관리 투명 데이터 암호화)입니다. 

Azure SQL 데이터베이스 및 Azure Synapse의 경우 TDE 보호기는 논리적 SQL 서버 수준에서 설정되며 해당 서버와 연결된 모든 데이터베이스에 의해 상속됩니다. Azure SQL 관리 인스턴스(미리 보기의 BYOK 기능)의 경우 TDE 보호기는 인스턴스 수준에서 설정되고 해당 인스턴스의 모든 암호화된 데이터베이스에 의해 상속됩니다. *서버*라는 용어는 달리 언급하지 않는 한, 이 문서 전체에서 서버와 인스턴스를 모두 나타냅니다.

> [!IMPORTANT]
> 새로 생성된 모든 Azure SQL 데이터베이스는 서비스 관리투명 데이터 암호화를 사용하여 기본적으로 암호화됩니다. 2017년 5월 이전에 생성된 기존 SQL 데이터베이스와 복원, 지역 복제 및 데이터베이스 복사본을 통해 생성된 SQL 데이터베이스는 기본적으로 암호화되지 않습니다. 2019년 2월 이전에 생성된 기존 관리형 인스턴스 데이터베이스는 기본적으로 암호화되지 않습니다. 복원을 통해 생성된 관리되는 인스턴스 데이터베이스는 원본에서 암호화 상태를 상속합니다.

> [!NOTE]
> TDE는 SQL Database의 논리 **master** 데이터베이스를 암호화하는 데 사용할 수 없습니다.  **master** 데이터베이스에는 사용자 데이터베이스에서 TDE 작업을 수행하는 데 필요한 개체가 포함되어 있습니다.


## <a name="service-managed-transparent-data-encryption"></a>서비스 관리 투명한 데이터 암호화

Azure에서 TDE의 기본 설정은 DEK가 기본 제공 서버 인증서로 보호된다는 것입니다. 기본 제공 서버 인증서는 각 서버에 대해 고유하며 사용되는 암호화 알고리즘은 AES 256입니다. 데이터베이스가 지역 복제 관계에 있는 경우 주 데이터베이스와 지역 보조 데이터베이스는 주 데이터베이스의 상위 서버 키로 보호됩니다. 두 개의 데이터베이스가 동일한 서버에 연결되어 있으면 동일한 기본 제공 인증서도 공유합니다.  Microsoft는 내부 보안 정책에 따라 이러한 인증서를 자동으로 회전하며, 루트 키는 Microsoft 내부 비밀 저장소를 통해 보호됩니다.  고객은 [Microsoft 신뢰 센터에서](https://servicetrust.microsoft.com/)사용할 수 있는 독립적인 타사 감사 보고서에서 SQL Database내부 보안 정책을 준수하는지 확인할 수 있습니다.

또한 Microsoft는 지역 복제 및 복원에 필요한 대로 키를 원활하게 이동하고 관리합니다.


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>고객 관리형 투명한 데이터 암호화 - Bring Your Own Key

고객 관리 TDE는 TDE에 대한 BYOK(사용자 고유의 키 가져오기) 지원이라고도 합니다. 이 시나리오에서 DEK를 암호화하는 TDE 프로텍터는 고객 소유 및 관리되는 Azure Key Vault(Azure의 클라우드 기반 외부 키 관리 시스템)에 저장되고 키 자격 증명 모음을 남기지 않는 고객 관리 비대칭 키입니다. TDE 프로텍터는 [키 자격 증명 모음에서 생성하거나](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) 온-프레미스 하드웨어 보안 모듈(HSM) 장치에서 키 자격 증명 모음으로 전송할 수 있습니다. SQL Database가 DEK를 암호화 및 암호 해독하려면 고객 소유의 Key Vault에 대한 권한이 부여되어야 합니다. 키 자격 증명 모음에 대한 논리 SQL 서버의 권한이 취소되면 데이터베이스에 액세스할 수 없으며 모든 데이터가 암호화됩니다.

TDE와 Azure Key Vault가 통합되면 사용자는 키 회전, Key Vault 권한, 키 백업을 포함한 키 관리 작업을 제어하고, Azure Key Vault 기능을 사용하여 모든 TDE 보호기에 감사/보고를 사용하도록 설정할 수 있습니다. Key Vault는 중앙 키 관리를 제공하고, 엄격하게 모니터링되는 HSM을 활용하며, 키 관리와 데이터 관리 간에 업무를 분리하여 보안 정책 준수를 지원합니다.
Azure SQL 데이터베이스 및 Azure 시냅스용 BYOK에 대해 자세히 알아보려면 [Azure 키 볼트 통합을 사용한 투명 데이터 암호화를](transparent-data-encryption-byok-azure-sql.md)참조하십시오.

Azure Key Vault 통합을 사용하여 TDE를 사용하려면 Key Vault 에서 [자신의 키를 사용하여 투명 데이터 암호화 켜기](transparent-data-encryption-byok-azure-sql-configure.md)방법을 참조하세요.

## <a name="move-a-transparent-data-encryption-protected-database"></a>투명한 데이터 암호화로 보호된 데이터베이스 이동

Azure 내에서 작업용 데이터베이스를 해독할 필요가 없습니다. 원본 데이터베이스 또는 주 데이터베이스의 TDE 설정은 대상에서 투명하게 상속됩니다. 포함되는 작업은 다음과 같습니다.

- 지리적 복원
- 셀프 서비스 특정 시점 복원
- 삭제된 데이터베이스 복원
- 활성 지리적 복제
- 데이터베이스 복사본 만들기
- Azure SQL Managed Instance에 백업 파일 복원

> [!IMPORTANT]
> 암호화에 사용되는 인증서에 액세스할 수 없으므로 Azure SQL Managed Instance에서는 서비스 관리 TDE로 암호화된 데이터베이스에 대한 COPY-ONLY(복사 전용) 백업을 수동으로 수행할 수 없습니다. 특정 시점 복원 기능을 사용하여 이 유형의 데이터베이스를 다른 Managed Instance로 이동합니다.

TDE로 보호된 데이터베이스를 내보낼 때 내보낸 데이터베이스의 콘텐츠는 암호화되지 않습니다. 이 내보낸 콘텐츠는 암호화되지 않은 BACPAC 파일에 저장됩니다. BACPAC 파일을 적절하게 보호하고 새 데이터베이스 가져오기가 완료된 후 TDE를 사용하도록 설정해야 합니다.

예를 들어 온-프레미스 SQL Server 인스턴스에서 BACPAC 파일을 내보낸 경우 가져온 새 데이터베이스의 콘텐츠가 자동으로 암호화되지 않습니다. 마찬가지로 BACPAC 파일을 온-프레미스 SQL Server 인스턴스로 내보낸 경우 새 데이터베이스도 자동으로 암호화되지 않습니다.

한 가지 예외는 SQL Database 간에 내보내는 경우입니다. 새 데이터베이스에서 TDE가 활성화되어 있지만 BACPAC 파일 자체는 여전히 암호화되지 않습니다.


## <a name="manage-transparent-data-encryption"></a>투명한 데이터 암호화 관리
# <a name="portal"></a>[포털](#tab/azure-portal)
Azure 포털에서 TDE를 관리합니다.

Azure 포털을 통해 TDE를 구성하려면 Azure 소유자, 기여자 또는 SQL 보안 관리자로 연결해야 합니다.

데이터베이스 수준에서 TDE를 켜고 끕니다. 데이터베이스에서 TDE를 사용하려면 [Azure 포털로](https://portal.azure.com) 이동하여 Azure 관리자 또는 기여자 계정으로 로그인합니다. 사용자 데이터베이스에서 TDE 설정을 찾습니다. 기본적으로 서비스 관리 투명한 데이터 암호화를 사용하도록 설정되어 있습니다. 데이터베이스가 포함된 서버에 대해 TDE 인증서가 자동으로 생성됩니다. Azure SQL 관리 인스턴스의 경우 T-SQL을 사용하여 데이터베이스에서 TDE를 켜고 끕니다.

![서비스 관리 투명한 데이터 암호화](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

서버 수준에서 TDE 보호기라고 하는 TDE 마스터 키를 설정합니다. BYOK 지원으로 TDE를 사용하고 키 볼트의 키로 데이터베이스를 보호하려면 서버에서 TDE 설정을 엽니다.

![Bring Your Own Key 지원을 통한 투명한 데이터 암호화](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용하여 TDE를 관리합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 리소스 관리자 모듈은 Azure SQL Database에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈용입니다. 이러한 cmdlet에 대 한 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조 합니다. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 거의 동일합니다.

PowerShell을 통해 TDE를 구성하려면 Azure 소유자, 참가자 또는 SQL 보안 관리자로 연결되어 있어야 합니다.

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
> Azure SQL 관리 인스턴스의 경우 T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) 명령을 사용하여 데이터베이스 수준에서 TDE를 켜고 끄고 [샘플 PowerShell 스크립트를](transparent-data-encryption-byok-azure-sql-configure.md) 확인하여 인스턴스 수준에서 TDE를 관리합니다.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
거래 SQL을 사용하여 TDE를 관리합니다.

master 데이터베이스에서 **dbmanager** 역할의 관리자 또는 구성원으로 로그인하여 데이터베이스에 연결합니다.

| 명령 | Description |
| --- | --- |
| [ALTER DATABASE(Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF는 데이터베이스를 암호화하거나 해독합니다. |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |데이터베이스 및 연결된 해당 데이터베이스 암호화 키의 암호화 상태에 대한 정보를 반환합니다. |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |각 Azure Synapse 노드및 관련 데이터베이스 암호화 키의 암호화 상태에 대한 정보를 반환합니다. |
|  | |

Transact-SQL을 사용하여 TDE 프로텍터를 키 볼트에서 키로 전환할 수 없습니다. 대신 PowerShell 또는 Azure Portal을 사용합니다.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
나머지 API를 사용하여 TDE를 관리합니다.

나머지 API를 통해 TDE를 구성하려면 Azure 소유자, 기여자 또는 SQL 보안 관리자로 연결해야 합니다.
Azure SQL 데이터베이스 및 Azure 시냅스에 대한 다음 명령 집합을 사용합니다.

| 명령 | Description |
| --- | --- |
|[서버 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|SQL Server 인스턴스에 Azure Active Directory ID(Key Vault에 대한 액세스 권한을 부여하는 데 사용)를 추가합니다.|
|[서버 키 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|SQL Server 인스턴스에 Key Vault 키를 추가합니다.|
|[서버 키 삭제](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|SQL Server 인스턴스에서 Key Vault 키를 제거합니다.|
|[서버 키 가져오기](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|SQL Server 인스턴스에서 특정 Key Vault 키를 가져옵니다.|
|[서버별 서버 키 나열](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|SQL Server 인스턴스에 대한 Key Vault 키를 가져옵니다. |
|[암호화 보호기 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|SQL Server 인스턴스에 대한 TDE 보호기 설정|
|[암호화 보호기 가져오기](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|SQL Server 인스턴스에 대한 TDE 보호기 가져옵니다.|
|[서버별 암호화 보호기 나열](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|SQL Server 인스턴스에 대한 TDE 보호기 가져옵니다. |
|[투명한 데이터 암호화 구성 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|데이터베이스에 대한 TDE를 사용 하거나 사용하지 않도록 설정|
|[투명한 데이터 암호화 구성 가져오기](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|데이터베이스에 대한 TDE 구성을 가져옵니다.|
|[투명한 데이터 암호화 구성 결과 나열](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|데이터베이스에 대한 암호화 결과를 가져옵니다.|

## <a name="see-also"></a>참고 항목
- Azure 가상 머신에서 실행되는 SQL Server는 Key Vault의 비대칭 키를 사용할 수도 있습니다. 구성 단계는 SQL Database 및 SQL Managed Instance에서 비대칭 키를 사용하는 것과 다릅니다. 자세한 내용은 [Azure Key Vault(SQL Server)를 사용한 확장 가능 키 관리](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)를 참조하세요.
- TDE에 대한 일반적인 설명은 [투명 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)를 참조하십시오.
- Azure SQL Database, Azure SQL 관리 인스턴스 및 Azure Synapse에 대한 BYOK 지원을 사용하여 TDE에 대해 자세히 알아보려면 [사용자 고유의 키 지원을 가져오는 투명 데이터 암호화를](transparent-data-encryption-byok-azure-sql.md)참조하십시오.
- 자신의 키 를 가져오기 지원과 함께 TDE를 사용하기 시작하려면 방법 가이드를 참조, [키 볼트에서 자신의 키를 사용하여 투명 한 데이터 암호화를 켭니다.](transparent-data-encryption-byok-azure-sql-configure.md)
- 키 자격 증명 모음에 대한 자세한 내용은 [키 자격 증명 모음에 대한 보안 액세스를](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)참조하십시오.
