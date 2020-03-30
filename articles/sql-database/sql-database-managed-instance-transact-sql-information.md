---
title: 관리되는 인스턴스 T-SQL 차이점
description: 이 문서에서는 Azure SQL Database 관리형 인스턴스 및 SQL Server 간의 T-SQL 차이점에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: e01c61ca4f415ffbb46c86034d4b7441bc2617d9
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365482"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>관리되는 인스턴스 T-SQL 차이 및 제한 사항

이 문서에서는 Azure SQL Database 관리 인스턴스와 온-프레미스 SQL Server 데이터베이스 엔진 간의 구문 및 동작의 차이점을 요약하고 설명합니다. 관리되는 인스턴스 배포 옵션은 온-프레미스 SQL Server 데이터베이스 엔진과의 높은 호환성을 제공합니다. 대부분의 SQL Server 데이터베이스 엔진 기능은 관리되는 인스턴스에서 지원됩니다.

![마이그레이션](./media/sql-database-managed-instance/migration.png)

관리 인스턴스에 도입된 몇 가지 PaaS 제한 사항과 SQL Server에 비해 일부 동작 변경 사항이 있습니다. 차이점은 다음과 같은 범주로 나뉩니다.<a name="Differences"></a>

- [가용성에는](#availability) [항상 사용 가능 그룹](#always-on-availability-groups) 및 [백업의 차이점이 포함됩니다.](#backup)
- [보안에는](#security) [감사,](#auditing) [인증서,](#certificates)자격 [증명,](#credential) [암호화 공급자,](#cryptographic-providers) [로그인 및 사용자,](#logins-and-users) [서비스 키 및 서비스 마스터 키의](#service-key-and-service-master-key)차이점이 포함됩니다.
- [구성에는](#configuration) 버퍼 [풀 확장,](#buffer-pool-extension) [데이터 정렬,](#collation) [호환성 수준,](#compatibility-levels) [데이터베이스 미러링,](#database-mirroring) [데이터베이스 옵션,](#database-options) [SQL Server 에이전트](#sql-server-agent)및 [테이블 옵션의](#tables)차이점이 포함됩니다.
- [기능으로는](#functionalities) [BULK INSERT/OPENROWSET,](#bulk-insert--openrowset) [CLR,](#clr) [DBCC,](#dbcc) [분산 거래,](#distributed-transactions) [확장 된 이벤트,](#extended-events) [외부 라이브러리, 파일](#external-libraries)스트림 및 [FileTable,](#filestream-and-filetable) [전체 텍스트 시맨틱 검색,](#full-text-semantic-search) [연결된 서버,](#linked-servers) [PolyBase,](#polybase) [복제,](#replication) [복원](#restore-statement), [서비스 브로커,](#service-broker) [저장 프로시저, 기능 및 트리거가 포함됩니다.](#stored-procedures-functions-and-triggers)
- VNet 및 서브넷 구성과 같은 [환경 설정입니다.](#Environment)

이러한 피쳐의 대부분은 아키텍처 제약 조건이며 서비스 피쳐를 나타냅니다.

관리되는 인스턴스에서 발견되고 나중에 해결될 임시 알려진 문제는 [릴리스 정보 페이지에](sql-database-release-notes.md)설명되어 있습니다.

## <a name="availability"></a>가용성

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Always On 가용성 그룹

[고가용성은](sql-database-high-availability.md) 관리되는 인스턴스에 내장되어 있으며 사용자가 제어할 수 없습니다. 다음 문은 지원되지 않습니다.

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [가용성 그룹 만들기](/sql/t-sql/statements/create-availability-group-transact-sql)
- [가용성 그룹 변경](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER 데이터베이스](/sql/t-sql/statements/alter-database-transact-sql) 문의 [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 절

### <a name="backup"></a>Backup

관리되는 인스턴스에는 자동 백업이 있으므로 `COPY_ONLY` 사용자는 전체 데이터베이스 백업을 만들 수 있습니다. 차동, 로그 및 파일 스냅샷 백업은 지원되지 않습니다.

- 관리되는 인스턴스를 사용하면 Azure Blob 저장소 계정에만 인스턴스 데이터베이스를 백업할 수 있습니다.
  - `BACKUP TO URL`만 지원됩니다.
  - `FILE`및 `TAPE`백업 장치가 지원되지 않습니다.
- 대부분의 일반 `WITH` 옵션이 지원됩니다.
  - `COPY_ONLY`필수입니다.
  - `FILE_SNAPSHOT`는 지원되지 않습니다.
  - 테이프 `REWIND`옵션: `NOREWIND` `UNLOAD`, `NOUNLOAD` 및 지원되지 않습니다.
  - 로그 관련 `NORECOVERY`옵션: `STANDBY` `NO_TRUNCATE` 및 지원되지 않습니다.

 제한 사항: 

- 관리되는 인스턴스를 사용하면 인스턴스 데이터베이스를 최대 32개의 스트라이프가 있는 백업에 백업할 수 있으며, 백업 압축을 사용하는 경우 최대 4TB의 데이터베이스에 충분합니다.
- 서비스 관리 투명 `BACKUP DATABASE ... WITH COPY_ONLY` 데이터 암호화(TDE)로 암호화된 데이터베이스에서는 실행할 수 없습니다. 서비스 관리 TDE는 내부 TDE 키로 백업을 암호화하도록 합니다. 키를 내보낼 수 없으므로 백업을 복원할 수 없습니다. 자동 백업 및 시점 복원을 사용하거나 [BYOK(고객 관리) TDE를](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) 대신 사용합니다. 데이터베이스에서 암호화를 사용하지 않도록 설정할 수도 있습니다.
- 관리되는 인스턴스에서 `BACKUP` 명령을 사용하여 최대 백업 스트라이프 크기는 최대 Blob 크기인 195GB입니다. 개별 스트라이프 크기를 줄이고 이 제한 내로 유지하려면 백업 명령에서 스트라이프 수를 늘립니다.

    > [!TIP]
    > 이 제한을 해결하려면 온-프레미스 환경이나 가상 컴퓨터에서 SQL Server에서 데이터베이스를 백업할 때 다음을 수행할 수 있습니다.
    >
    > - 백업하는 `DISK` 대신 에 백업합니다. `URL`
    > - 백업 파일을 Blob 저장소에 업로드합니다.
    > - 관리되는 인스턴스로 복원합니다.
    >
    > 관리되는 인스턴스의 `Restore` 명령은 업로드된 백업 파일의 저장에 다른 Blob 형식이 사용되므로 백업 파일에서 더 큰 Blob 크기를 지원합니다.

T-SQL을 사용하는 백업에 대한 자세한 내용은 [BACKUP](/sql/t-sql/statements/backup-transact-sql)을 참조하세요.

## <a name="security"></a>보안

### <a name="auditing"></a>감사

Azure SQL Database의 데이터베이스 및 SQL Server의 데이터베이스에서 감사 간의 주요 차이점은 다음과 같습니다.

- Azure SQL Database에서 관리되는 인스턴스 배포 옵션을 사용하면 감사가 서버 수준에서 작동합니다. `.xel` 로그 파일은 Azure Blob 저장소에 저장됩니다.
- Azure SQL Database의 단일 데이터베이스 및 탄력적 풀 배포 옵션을 사용하면 감사는 데이터베이스 수준에서 작동합니다.
- SQL Server 온-프레미스 또는 가상 컴퓨터에서 감사는 서버 수준에서 작동합니다. 이벤트는 파일 시스템 또는 Windows 이벤트 로그에 저장됩니다.
 
관리되는 인스턴스의 XEvent 감사는 Azure Blob Storage 대상을 지원합니다. 파일 및 Windows 로그는 지원되지 않습니다.

Azure Blob Storage에 대한 감사에서 `CREATE AUDIT` 구문의 주요 차이점은 다음과 같습니다.

- 파일이 배치되는 Azure Blob 저장소 컨테이너의 URL을 지정하는 데 사용할 수 있는 새 구문이 `TO URL` 제공됩니다. `.xel`
- 관리되는 `TO FILE` 인스턴스가 Windows 파일 공유에 액세스할 수 없으므로 구문이 지원되지 않습니다.

자세한 내용은 다음을 참조하세요. 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [감사](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>인증서

관리되는 인스턴스는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음 제약 조건이 적용됩니다.

- `CREATE FROM` 파일은 인증서에 대해 지원되지 / `BACKUP TO` 않습니다.
- `CREATE` / `BACKUP` 인증서가 `FILE` / 지원되지 `ASSEMBLY` 않습니다. 프라이빗 키 파일은 사용할 수 없습니다. 

[CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) 및 [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql)를 참조하세요. 
 
**해결 :** 인증서 백업을 만들고 백업을 복원하는 대신 [인증서 바이너리 콘텐츠 및 개인 키를 가져 와서 .sql 파일로 저장하고 바이너리에서 만듭니다.](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>자격 증명

Azure Key Vault 및 `SHARED ACCESS SIGNATURE` ID만 지원됩니다. Windows 사용자는 지원되지 않습니다.

[CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) 및 [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql)을 참조하세요.

### <a name="cryptographic-providers"></a>암호화 공급자

관리되는 인스턴스는 파일에 액세스할 수 없으므로 암호화 공급자를 만들 수 없습니다.

- `CREATE CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql)를 참조하세요.
- `ALTER CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)를 참조하세요.

### <a name="logins-and-users"></a>로그인 및 사용자

- 을 `FROM CERTIFICATE` `FROM ASYMMETRIC KEY`사용하여 만든 SQL 로그인 `FROM SID` 및 지원됩니다. [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql)을 참조하세요.
- [CREATE 로그인](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 구문 또는 [로그인에서 사용자 만들기 [Azure AD 로그인]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 구문으로 만든 Azure Active Directory(Azure AD) 서버 주체(로그인)가 지원됩니다. 이러한 로그인은 서버 수준에서 만들어집니다.

    관리되는 인스턴스는 구문을 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`사용하여 Azure AD 데이터베이스 주체를 지원합니다. 이 기능은 Azure AD 포함 데이터베이스 사용자라고도 합니다.

- `CREATE LOGIN ... FROM WINDOWS` 구문으로 만든 Windows 로그인은 지원되지 않습니다. Azure Active Directory 로그인 및 사용자를 사용합니다.
- 인스턴스를 만든 Azure AD 사용자에게 [무제한 관리자 권한이 있습니다.](sql-database-manage-logins.md)
- 관리자가 아닌 Azure AD 데이터베이스 수준 사용자는 `CREATE USER ... FROM EXTERNAL PROVIDER` 구문을 사용하여 만들 수 있습니다. 사용자 만들기 를 참조하십시오 [... 외부 공급자에서](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
- Azure AD 서버 주체(로그인)는 관리되는 하나의 인스턴스 내에서만 SQL 기능을 지원합니다. 동일한 Azure AD 테넌트 내에 있든 다른 테넌트에 있든 관계없이 상호 인스턴스 상호 작용이 필요한 기능은 Azure AD 사용자에 대해 지원되지 않습니다. 이러한 기능의 예는 다음과 같습니다.

  - SQL 트랜잭션 복제.
  - 링크 서버.

- Azure AD 그룹에 매핑된 Azure AD 로그인을 데이터베이스 소유자로 설정할 수 없습니다.
- [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) 절과 같은 다른 Azure AD 보안 주체를 사용하여 Azure AD 서버 수준 보안 주체의 가장이 지원됩니다. 제한으로 실행:

  - 이름이 로그인 이름과 다를 때 Azure AD 사용자에 대 한 지원 되지 않습니다 사용자로 실행 합니다. 예를 들어 사용자가 로그인에서john@contoso.com사용자 만들기 [myAadUser] 구문을 통해 생성되고 EXEC AS USER = _myAadUser_. Azure AD 서버 주체(로그인)에서 **사용자를** 만들 때 **로그인**에서 동일한 login_name user_name 지정합니다.
  - `sysadmin` 역할의 일부인 SQL Server 수준 보안 주체(로그인)만 Azure AD 보안 주체를 대상으로 하는 다음 작업을 실행할 수 있습니다.

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Bacpac 파일을 사용한 데이터베이스 내보내기/가져오기는 [SSMS V18.4 이상](/sql/ssms/download-sql-server-management-studio-ssms)또는 [SQLPackage.exe](/sql/tools/sqlpackage-download)를 사용하여 관리되는 인스턴스에서 Azure AD 사용자에 대해 지원됩니다.
  - 다음 구성은 데이터베이스 bacpac 파일을 사용하여 지원됩니다. 
    - 동일한 Azure AD 도메인 내의 다른 관리 인스턴스 간에 데이터베이스를 내보내/가져오기합니다.
    - 관리되는 인스턴스에서 데이터베이스를 내보내고 동일한 Azure AD 도메인 내에서 SQL 데이터베이스로 가져옵니다. 
    - SQL Database에서 데이터베이스를 내보내고 동일한 Azure AD 도메인 내에서 관리되는 인스턴스로 가져옵니다.
    - 관리되는 인스턴스에서 데이터베이스를 내보내고 SQL Server(버전 2012 이상)로 가져옵니다.
      - 이 구성에서 모든 Azure AD 사용자는 로그인 없이 SQL 데이터베이스 주체(사용자)로 만들어집니다. 사용자 유형은 SQL로 나열됩니다(sys.database_principals SQL_USER 표시됨). 해당 권한 및 역할은 SQL Server 데이터베이스 메타데이터에 남아 있으며 가장에 사용할 수 있습니다. 그러나 자격 증명을 사용하여 SQL Server에 액세스하고 로그인하는 데 사용할 수는 없습니다.

- 관리되는 인스턴스 프로비저닝 프로세스에 의해 생성되는 서버 수준 주체 로그인, `securityadmin` 서버 `sysadmin`수준에서 ALTER ANY LOGIN 권한을 가진 기타 로그인 과 같은 서버 역할의 구성원만 관리되는 인스턴스의 마스터 데이터베이스에 Azure AD 서버 주체(로그인)를 만들 수 있습니다.
- 로그인이 SQL 보안 주체인 경우 `sysadmin` 역할의 일부인 로그인만 Create 명령을 사용하여 Azure AD 계정에 대한 로그인을 만들 수 있습니다.
- Azure AD 로그인은 Azure SQL Database 관리 인스턴스에 사용되는 동일한 디렉터리 내에서 Azure AD의 구성원이어야 합니다.
- Azure AD 서버 주체(로그인)는 SQL Server 관리 Studio 18.0 미리 보기 5부터 시작하는 개체 탐색기에서 볼 수 있습니다.
- Azure AD 서버 보안 주체(로그인)를 Azure AD 관리자 계정과 겹칠 수 있습니다. Azure AD 서버 주체(로그인)는 보안 주체를 해결하고 관리되는 인스턴스에 권한을 적용할 때 Azure AD 관리자보다 우선합니다.
- 인증 하는 동안 다음 순서는 인증 주체를 해결 하기 위해 적용 됩니다.

    1. Azure AD 계정이 sys.server_principals "E"로 존재하는 Azure AD 서버 주체(로그인)에 직접 매핑된 대로 존재하는 경우 액세스 권한을 부여하고 Azure AD 서버 주체(로그인)의 권한을 적용합니다.
    2. Azure AD 계정이 sys.server_principals "X"로 있는 Azure AD 서버 주체(로그인)에 매핑된 Azure AD 그룹의 구성원인 경우 액세스 권한을 부여하고 Azure AD 그룹 로그인의 권한을 적용합니다.
    3. Azure AD 계정이 관리되는 인스턴스 보기에 존재하지 않는 관리되는 인스턴스에 대해 Portal에서 구성된 특수 포털 구성 Azure AD 관리자인 경우 관리되는 인스턴스(레거시 모드)에 대해 Azure AD 관리자의 특별한 고정 권한을 적용합니다.
    4. Azure AD 계정이 sys.database_principals "E"로 존재하는 데이터베이스의 Azure AD 사용자에게 직접 매핑된 대로 존재하는 경우 액세스 권한을 부여하고 Azure AD 데이터베이스 사용자의 권한을 적용합니다.
    5. Azure AD 계정이 데이터베이스의 Azure AD 사용자에게 매핑된 Azure AD 그룹의 구성원인 경우 sys.database_principals 유형 "X"로 표시되어 액세스 권한을 부여하고 Azure AD 그룹 로그인의 권한을 적용합니다.
    6. Azure AD 사용자 계정 또는 인증 중인 사용자에게 확인되는 Azure AD 그룹 계정에 매핑된 Azure AD 로그인이 있는 경우 이 Azure AD 로그인의 모든 사용 권한이 적용됩니다.

### <a name="service-key-and-service-master-key"></a>서비스 키 및 서비스 마스터 키

- [마스터 키 백업이](/sql/t-sql/statements/backup-master-key-transact-sql) 지원되지 않습니다(SQL Database 서비스에서 관리).
- [마스터 키 복원이](/sql/t-sql/statements/restore-master-key-transact-sql) 지원되지 않습니다(SQL Database 서비스에서 관리).
- [서비스 마스터 키 백업이](/sql/t-sql/statements/backup-service-master-key-transact-sql) 지원되지 않습니다(SQL Database 서비스에서 관리).
- [서비스 마스터 키 복원이](/sql/t-sql/statements/restore-service-master-key-transact-sql) 지원되지 않습니다(SQL Database 서비스에서 관리).

## <a name="configuration"></a>Configuration

### <a name="buffer-pool-extension"></a>버퍼 풀 확장

- [버퍼 풀 확장이](/sql/database-engine/configure-windows/buffer-pool-extension) 지원되지 않습니다.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`는 지원되지 않습니다. [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql)을 참조하세요.

### <a name="collation"></a>데이터 정렬

기본 인스턴스 데이터 정렬은 `SQL_Latin1_General_CP1_CI_AS`이며 생성 매개 변수로 지정할 수 있습니다. [데이터 정렬](/sql/t-sql/statements/collations)을 참조하세요.

### <a name="compatibility-levels"></a>호환성 수준

- 지원되는 호환성 수준은 100, 110, 120, 130, 140 및 150입니다.
- 100 미만의 호환성 수준은 지원되지 않습니다.
- 새 데이터베이스의 기본 호환성 수준은 140입니다. 복원된 데이터베이스의 경우 호환성 수준이 100 이상인 경우 호환성 수준은 변경되지 않습니다.

[ALTER DATABASE 호환성 수준](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요.

### <a name="database-mirroring"></a>데이터베이스 미러링

데이터베이스 미러링은 지원되지 않습니다.

- `ALTER DATABASE SET PARTNER` 및 `SET WITNESS` 옵션은 지원되지 않습니다.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`는 지원되지 않습니다.

자세한 내용은 [ALTER DATABASE SET PARTNER 및 SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 및 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)을 참조하세요.

### <a name="database-options"></a>데이터베이스 옵션

- 다중 로그 파일은 지원되지 않습니다.
- 메모리 내 개체는 범용 서비스 계층에서 지원되지 않습니다. 
- 범용 인스턴스당 파일은 280개로 제한되며, 이는 데이터베이스당 최대 280개의 파일을 의미합니다. 범용 계층의 데이터와 로그 파일은 모두 이 제한에 계산됩니다. [비즈니스 크리티컬 계층은 데이터베이스당 32,767개의 파일을 지원합니다.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)
- 데이터베이스에는 파일 스트림 데이터가 포함된 파일 그룹을 포함할 수 없습니다. .bak에 데이터가 `FILESTREAM` 포함된 경우 복원이 실패합니다. 
- 모든 파일은 Azure Blob Storage에 배치됩니다. 파일별 IO 및 처리량은 각 개별 파일의 크기에 따라 달라집니다.

#### <a name="create-database-statement"></a>CREATE DATABASE 문

다음과 같은 제한 `CREATE DATABASE`사항이 적용됩니다.

- 파일 및 파일 그룹을 정의할 수 없습니다. 
- 이 `CONTAINMENT` 옵션은 지원되지 않습니다. 
- `WITH`옵션은 지원되지 않습니다. 
   > [!TIP]
   > 해결 해결 방법을 `ALTER DATABASE` 사용하여 `CREATE DATABASE` after를 사용하여 데이터베이스 옵션을 설정하여 파일을 추가하거나 포함을 설정합니다. 

- 이 `FOR ATTACH` 옵션은 지원되지 않습니다.
- 이 `AS SNAPSHOT OF` 옵션은 지원되지 않습니다.

자세한 내용은 [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql)를 참조하세요.

#### <a name="alter-database-statement"></a>ALTER DATABASE 문

일부 파일 속성은 설정하거나 변경할 수 없습니다.

- `ALTER DATABASE ADD FILE (FILENAME='path')` 파일 경로는 T-SQL 문에 지정할 수 없습니다. 관리되는 인스턴스에서 파일을 자동으로 저장하기 때문에 스크립트에서 `FILENAME`을 제거합니다. 
- `ALTER DATABASE` 명령문을 사용하여 파일 이름을 변경할 수 없습니다.

기본적으로 설정되고 변경할 수 없는 옵션은 다음과 같습니다.

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

수정할 수 없는 옵션은 다음과 같습니다.

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

자세한 내용은 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)를 참조하세요.

### <a name="sql-server-agent"></a>SQL Server 에이전트

- SQL Server 에이전트를 사용하도록 설정 및 비활성화하는 것은 현재 관리되는 인스턴스에서 지원되지 않습니다. SQL 에이전트는 항상 실행됩니다.
- SQL 서버 에이전트 설정은 읽기 전용입니다. 관리되는 `sp_set_agent_properties` 인스턴스에서는 프로시저가 지원되지 않습니다. 
- 작업
  - T-SQL 작업 단계가 지원됩니다.
  - 다음 복제 작업이 지원됩니다.
    - 트랜잭션 로그 판독기
    - 스냅샷
    - 배포자
  - SSIS 작업 단계가 지원됩니다.
  - 다른 유형의 작업 단계는 현재 지원되지 않습니다.
    - 병합 복제 작업 단계가 지원되지 않습니다. 
    - 큐 판독기는 지원되지 않습니다. 
    - 명령 셸은 아직 지원되지 않습니다.
  - 관리되는 인스턴스는 로보카를 통해 네트워크 공유와 같은 외부 리소스에 액세스할 수 없습니다. 
  - SQL 서버 분석 서비스는 지원되지 않습니다.
- 알림은 부분적으로 지원됩니다.
- 데이터베이스 메일 프로필을 구성해야 하지만 전자 메일 알림이 지원됩니다. SQL Server 에이전트는 하나의 데이터베이스 메일 프로필만 `AzureManagedInstance_dbmail_profile`사용할 수 있으며 이 프로필을 호출해야 합니다. 
  - 호출기는 지원되지 않습니다.
  - NetSend는 지원되지 않습니다.
  - 경고는 아직 지원되지 않습니다.
  - 프록시는 지원되지 않습니다.
- EventLog는 지원되지 않습니다.

현재 다음과 같은 SQL Agent 기능은 지원되지 않습니다.

- 프록시
- 유휴 CPU에서 작업 예약
- 에이전트 사용 또는 비활성화
- 경고

SQL Server 에이전트에 대한 자세한 내용은 [SQL Server 에이전트](/sql/ssms/agent/sql-server-agent)를 참조하세요.

### <a name="tables"></a>테이블

다음 테이블 유형은 지원되지 않습니다.

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [Filetable](/sql/relational-databases/blob/filetables-sql-server)
- [외부](/sql/t-sql/statements/create-external-table-transact-sql) 테이블(폴리베이스)
- [MEMORY_OPTIMIZED(범용](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) 계층에서만 지원되지 않음)

테이블을 만들고 변경하는 방법에 대한 자세한 내용은 [테이블 만들기](/sql/t-sql/statements/create-table-transact-sql) 및 [테이블 변경](/sql/t-sql/statements/alter-table-transact-sql)을 참조하십시오.

## <a name="functionalities"></a>기능

### <a name="bulk-insert--openrowset"></a>벌크 인서트/오픈로우세트

관리되는 인스턴스는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 Azure Blob 저장소에서 파일을 가져와야 합니다.

- `DATASOURCE`Azure Blob `BULK INSERT` 저장소에서 파일을 가져오는 동안 명령에 필요합니다. [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql)를 참조하세요.
- `DATASOURCE`는 Azure `OPENROWSET` Blob 저장소에서 파일의 내용을 읽을 때 함수에 필요합니다. [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.
- `OPENROWSET`다른 Azure SQL 단일 데이터베이스, 관리되는 인스턴스 또는 SQL Server 인스턴스의 데이터를 읽는 데 사용할 수 있습니다. Oracle 데이터베이스 또는 Excel 파일과 같은 다른 소스는 지원되지 않습니다.

### <a name="clr"></a>CLR

관리되는 인스턴스는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음 제약 조건이 적용됩니다.

- `CREATE ASSEMBLY FROM BINARY`만 지원됩니다. [바이너리에서 ASSEM BLY 만들기를](/sql/t-sql/statements/create-assembly-transact-sql)참조하십시오. 
- `CREATE ASSEMBLY FROM FILE`는 지원되지 않습니다. [파일에서 어셈블리 만들기](/sql/t-sql/statements/create-assembly-transact-sql)를 참조하십시오.
- `ALTER ASSEMBLY`는 파일을 참조할 수 없습니다. [어셈블리 변경을](/sql/t-sql/statements/alter-assembly-transact-sql)참조하십시오.

### <a name="database-mail-db_mail"></a>데이터베이스 메일(db_mail)
 - `sp_send_dbmail`매개 변수를 @file_attachments 사용하여 첨부 파일을 보낼 수 없습니다. 로컬 파일 시스템 및 외부 공유 또는 Azure Blob 저장소는 이 절차에서 액세스할 수 없습니다.
 - `@query` 매개 변수 및 인증과 관련된 알려진 문제를 참조하십시오.
 
### <a name="dbcc"></a>DBCC

SQL Server에서 활성화된 문서화되지 않은 DBCC 문은 관리되는 인스턴스에서 지원되지 않습니다.

- 제한된 수의 전역 추적 플래그만 지원됩니다. 세션 수준은 `Trace flags` 지원되지 않습니다. [추적 플래그를](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)참조하십시오.
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) 및 [DBCC TRACEON은](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) 제한된 수의 글로벌 트레이스 플래그와 함께 작동합니다.
- 데이터베이스가 `SINGLE_USER` 모드에서 설정할 수 없으므로 REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST 및 REPAIR_REBUILD 옵션을 갖춘 [DBCC CHECKDB는](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) 데이터베이스 [차이 ALTER를](#alter-database-statement)참조하십시오. 잠재적인 데이터베이스 손상은 Azure 지원 팀에서 처리합니다. 수정해야 하는 데이터베이스 손상이 없는 경우 Azure 지원에 문의하십시오.

### <a name="distributed-transactions"></a>분산 트랜잭션

MSDTC 및 [탄력적 트랜잭션은](sql-database-elastic-transactions-overview.md) 현재 관리되는 인스턴스에서 지원되지 않습니다.

### <a name="extended-events"></a>확장 이벤트

확장 이벤트(XEvents)에 대한 일부 Windows 관련 대상은 지원되지 않습니다.

- `etw_classic_sync` 대상이 지원되지 않습니다. Azure `.xel` Blob 저장소에 파일을 저장합니다. [etw_classic_sync 대상](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)을 참조하세요.
- `event_file` 대상이 지원되지 않습니다. Azure `.xel` Blob 저장소에 파일을 저장합니다. [event_file 대상](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)을 참조하세요.

### <a name="external-libraries"></a>외부 라이브러리

데이터베이스 내 R 및 파이썬, 외부 라이브러리는 아직 지원되지 않습니다. [SQL Server Machine Learning Services](/sql/advanced-analytics/r/sql-server-r-services)를 참조하세요.

### <a name="filestream-and-filetable"></a>Filestream 및 FileTable

- 파일 스트림 데이터는 지원되지 않습니다.
- 데이터베이스에는 데이터가 포함된 파일 `FILESTREAM` 그룹을 포함할 수 없습니다.
- `FILETABLE`는 지원되지 않습니다.
- 테이블에 `FILESTREAM` 형식을 사용할 수 없습니다.
- 지원되지 않는 함수는 다음과 같습니다.
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

자세한 내용은 [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) 및 [FileTables](/sql/relational-databases/blob/filetables-sql-server)를 참조하세요.

### <a name="full-text-semantic-search"></a>전체 텍스트 의미 체계 검색

[의미 체계 검색](/sql/relational-databases/search/semantic-search-sql-server)은 지원되지 않습니다.

### <a name="linked-servers"></a>연결된 서버

관리되는 인스턴스의 연결된 서버는 제한된 수의 대상을 지원합니다.

- 지원되는 대상은 관리되는 인스턴스, 단일 데이터베이스 및 SQL Server 인스턴스입니다. 
- 연결된 서버는 MS DTC(분산 쓰기 가능 트랜잭션)를 지원하지 않습니다.
- 지원되지 않는 대상은 파일, 분석 서비스 및 기타 RDBMS입니다. Azure Blob Storage에서 기본 CSV `BULK INSERT` 가져오기를 사용하거나 `OPENROWSET` 파일 가져오기의 대안으로 사용해 보십시오.

작업

- 인스턴스 간 쓰기 트랜잭션은 지원되지 않습니다.
- `sp_dropserver`는 연결된 서버를 삭제하는 데 지원됩니다. [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)를 참조하세요.
- 이 `OPENROWSET` 함수는 SQL Server 인스턴스에서만 쿼리를 실행하는 데 사용할 수 있습니다. 관리, 온-프레미스 또는 가상 컴퓨터로 관리할 수 있습니다. [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.
- 이 `OPENDATASOURCE` 함수는 SQL Server 인스턴스에서만 쿼리를 실행하는 데 사용할 수 있습니다. 관리, 온-프레미스 또는 가상 컴퓨터로 관리할 수 있습니다. `SQLNCLI`및 `SQLNCLI11` `SQLOLEDB` 및 값만 공급자로 지원됩니다. 예제는 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`입니다. [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)를 참조하세요.
- 연결된 서버는 네트워크 공유에서 파일(Excel, CSV)을 읽는 데 사용할 수 없습니다. Azure Blob 저장소에서 CSV 파일을 읽는 [대량 삽입](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 또는 [OPENROWSET을](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 사용해 보십시오. [관리되는 인스턴스 피드백 항목에서](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources) 이 요청을 추적합니다.|

### <a name="polybase"></a>PolyBase

HDFS 또는 Azure Blob 저장소의 파일을 참조하는 외부 테이블은 지원되지 않습니다. 폴리베이스에 대한 자세한 내용은 [PolyBase](/sql/relational-databases/polybase/polybase-guide)를 참조하십시오.

### <a name="replication"></a>복제

- 스냅숏 및 양방향 복제 유형이 지원됩니다. 병합 복제, 피어 투 피어 복제 및 업데이터 구독은 지원되지 않습니다.
- [트랜잭션 복제는](sql-database-managed-instance-transactional-replication.md) 일부 제약 조건이 있는 관리되는 인스턴스에서 공개 미리 보기에 사용할 수 있습니다.
    - 모든 유형의 복제 참가자(게시자, 배포자, 끌어오기 구독자 및 푸시 구독자)는 관리되는 인스턴스에 배치할 수 있지만 게시자와 배포자는 클라우드 또는 온-프레미스 모두에 있어야 합니다.
    - 관리되는 인스턴스는 SQL Server의 최신 버전과 통신할 수 있습니다. 자세한 내용은 [지원되는 버전 매트릭스를](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems) 참조하십시오.
    - 트랜잭션 복제에는 몇 가지 [추가 네트워킹 요구 사항이 있습니다.](sql-database-managed-instance-transactional-replication.md#requirements)

트랜잭션 복제 구성에 대한 자세한 내용은 다음 자습서를 참조하십시오.
- [MI 게시자와 구독자 간의 복제](replication-with-sql-database-managed-instance.md)
- [MI 게시자, MI 배포자 및 SQL Server 구독자 간의 복제](sql-database-managed-instance-configure-replication-tutorial.md)

### <a name="restore-statement"></a>RESTORE 문 

- 지원되는 구문:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- 지원되지 않는 구문:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- 원본: 
  - `FROM URL`(Azure Blob 저장소)는 지원되는 유일한 옵션입니다.
  - `FROM DISK`/`TAPE`/백업 디바이스는 지원되지 않습니다.
  - 백업 세트는 지원되지 않습니다.
- `WITH`옵션은 지원되지 않습니다(예: `DIFFERENTIAL` `STATS`아니요 또는 .
- `ASYNC RESTORE`: 클라이언트 연결이 끊어도 복원이 계속됩니다. 연결이 끊어지면 `sys.dm_operation_status` 뷰에서 복원 작업의 상태와 CREATE 및 DROP 데이터베이스를 확인할 수 있습니다. 자세한 내용은 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)를 참조하세요. 

다음 데이터베이스 옵션은 설정되거나 재정의되며 나중에 변경할 수 없습니다. 

- `NEW_BROKER`브로커가 .bak 파일에서 활성화되지 않은 경우. 
- `ENABLE_BROKER`브로커가 .bak 파일에서 활성화되지 않은 경우. 
- `AUTO_CLOSE=OFF`.bak 파일의 데이터베이스에 `AUTO_CLOSE=ON`있는 경우 . 
- `RECOVERY FULL`.bak 파일의 데이터베이스에 복구 `SIMPLE` `BULK_LOGGED` 모드가 있는 경우.
- 메모리에 최적화된 파일 그룹이 추가되고 원본 .bak 파일에 없는 경우 XTP라고 합니다. 
- 기존 메모리에 최적화된 파일 그룹이름이 XTP로 바뀝니다. 
- `SINGLE_USER`및 `RESTRICTED_USER` 옵션은 `MULTI_USER`로 변환됩니다.

 제한 사항: 

- 손상된 데이터베이스의 백업은 손상 유형에 따라 복원될 수 있지만 손상이 해결될 때까지 자동화된 백업은 수행되지 않습니다. 이 문제를 방지하기 `DBCC CHECKDB` 위해 원본 인스턴스에서 `WITH CHECKSUM` 실행하고 백업을 사용해야 합니다.
- 이 `.BAK` 문서에 설명된 제한 사항(예: `FILESTREAM` 또는 `FILETABLE` 개체)이 포함된 데이터베이스 파일의 복원은 관리되는 인스턴스에서 복원할 수 없습니다.
- `.BAK`백업 세트가 여러 개인 파일은 복원할 수 없습니다. 
- `.BAK`로그 파일이 여러 개인 파일은 복원할 수 없습니다.
- 8TB보다 큰 데이터베이스, 활성 인메모리 OLTP 개체 또는 인스턴스당 280개의 파일을 초과하는 파일 수가 포함된 백업은 범용 인스턴스에서 복원할 수 없습니다. 
- [리소스 제한에](sql-database-managed-instance-resource-limits.md) 설명된 크기보다 큰 총 크기인 4TB 또는 메모리 내 OLTP 개체를 포함하는 데이터베이스는 비즈니스 중요 인스턴스에서 복원할 수 없습니다.
복원 문에 대한 자세한 내용은 [RESTORE 문을](/sql/t-sql/statements/restore-statements-transact-sql)참조하십시오.

 > [!IMPORTANT]
 > 기본 제공 시간 복원 작업에도 동일한 제한이 적용됩니다. 예를 들어 4TB를 초과하는 범용 데이터베이스는 비즈니스 크리티컬 인스턴스에서 복원할 수 없습니다. 인메모리 OLTP 파일 또는 280개 이상의 파일이 있는 비즈니스 크리티컬 데이터베이스는 범용 인스턴스에서 복원할 수 없습니다.

### <a name="service-broker"></a>Service Broker

인스턴스 간 서비스 broker는 지원되지 않습니다.

- `sys.routes`: 전제 조건으로 sys.routes에서 주소를 선택해야 합니다. 주소는 모든 경로에서 LOCAL이어야 합니다. [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)를 참조하세요.
- `CREATE ROUTE`: 다른 것 `CREATE ROUTE` 이외에는 `ADDRESS` `LOCAL`사용할 수 없습니다. [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql)를 참조하세요.
- `ALTER ROUTE`: 다른 것 `ALTER ROUTE` 이외에는 `ADDRESS` `LOCAL`사용할 수 없습니다. [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql)를 참조하세요. 

### <a name="stored-procedures-functions-and-triggers"></a>저장 프로시저, 함수 및 트리거

- `NATIVE_COMPILATION`범용 계층에서는 지원되지 않습니다.
- 지원되지 않는 [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 옵션은 다음과 같습니다. 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`는 지원되지 않습니다. [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)를 참조하세요.
- `xp_cmdshell`는 지원되지 않습니다. [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)을 참조하세요.
- `Extended stored procedures`을 `sp_addextendedproc`   `sp_dropextendedproc`포함하며, 을 포함하여 지원되지 않습니다. [확장 저장 프로시저를](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)참조하십시오.
- `sp_attach_db`, `sp_attach_single_file_db` 및 `sp_detach_db`는 지원되지 않습니다. [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 및 [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)를 참조하세요.

### <a name="system-functions-and-variables"></a>시스템 기능 및 변수

다른 결과를 반환하는 변수, 함수 및 뷰는 다음과 같습니다.

- `SERVERPROPERTY('EngineEdition')`값 8을 반환합니다. 이 속성은 관리되는 인스턴스를 고유하게 식별합니다. [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `SERVERPROPERTY('InstanceName')`SQL Server에 대해 존재하는 인스턴스 개념이 관리되는 인스턴스에 적용되지 않기 때문에 NULL을 반환합니다. [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `@@SERVERNAME`예를 들어 my-managed-instance.wcus17662feb9ce98.database.windows.net 전체 DNS "연결 가능" 이름을 반환합니다. [@를@SERVERNAME](/sql/t-sql/functions/servername-transact-sql)참조하십시오. 
- `SYS.SERVERS`속성 "이름" 및 "data_source"과 `myinstance.domain.database.windows.net` 같은 전체 DNS "연결 가능" 이름을 반환합니다. [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)를 참조하세요.
- `@@SERVICENAME`SQL Server에 대해 존재하는 서비스 개념이 관리되는 인스턴스에 적용되지 않기 때문에 NULL을 반환합니다. [@를@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql)참조하십시오.
- `SUSER_ID`이 지원됩니다. Azure AD 로그인이 sys.syslogins에 없는 경우 NULL을 반환합니다. [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql)를 참조하세요. 
- `SUSER_SID`는 지원되지 않습니다. 잘못된 데이터가 반환되며, 이는 일시적으로 알려진 문제입니다. [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql)를 참조하세요. 

## <a name="environment-constraints"></a><a name="Environment"></a>환경 제약 조건

### <a name="subnet"></a>서브넷
-  관리되는 인스턴스를 배포한 서브넷에 다른 리소스(예: 가상 컴퓨터)를 배치할 수 없습니다. 다른 서브넷을 사용하여 이러한 리소스를 배포합니다.
- 서브넷에는 사용 가능한 [IP 주소가](sql-database-managed-instance-connectivity-architecture.md#network-requirements)충분해야 합니다. 최소 값은 16이며 서브넷에 32개 이상의 IP 주소가 있어야 합니다.
- [서비스 끝점은 관리되는 인스턴스의 서브넷과 연결할 수 없습니다.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) 가상 네트워크를 만들 때 서비스 끝점 옵션이 비활성화되어 있는지 확인합니다.
- 지역에 배포할 수 있는 vCore및 인스턴스 유형에는 몇 가지 [제약 조건과 제한이 있습니다.](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)
- [서브넷에 적용해야 하는](sql-database-managed-instance-connectivity-architecture.md#network-requirements)몇 가지 보안 규칙이 있습니다.

### <a name="vnet"></a>VNET
- VNet은 리소스 모델을 사용하여 배포할 수 있습니다 - VNet용 클래식 모델은 지원되지 않습니다.
- 관리되는 인스턴스를 만든 후 관리되는 인스턴스 또는 VNet을 다른 리소스 그룹 또는 구독으로 이동하는 것은 지원되지 않습니다.
- 앱 서비스 환경, 논리 앱 및 관리되는 인스턴스(지리적 복제, 트랜잭션 복제 또는 연결된 서버를 통해 사용)와 같은 일부 서비스는 [VNet이 전역 피어링을](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)사용하여 연결된 경우 다른 지역에서 관리되는 인스턴스에 액세스할 수 없습니다. 익스프레스루트 또는 VNet-VNet을 통해 VNet 게이트웨이를 통해 이러한 리소스에 연결할 수 있습니다.

### <a name="tempdb"></a>Tempdb

범용 계층의 `tempdb` 최대 파일 크기는 코어당 24GB를 초과할 수 없습니다. 비즈니스 `tempdb` 크리티컬 계층의 최대 크기는 인스턴스 저장소 크기에 따라 제한됩니다. `Tempdb`로그 파일 크기는 범용 계층에서 120GB로 제한됩니다. 일부 쿼리는 코어당 24GB 이상이 `tempdb` 필요하거나 120GB 이상의 로그 데이터를 생성하는 경우 오류를 반환할 수 있습니다.

### <a name="msdb"></a>MSDB

관리되는 인스턴스의 다음 MSDB 스키마는 미리 정의된 각 역할에 의해 소유되어야 합니다.

- 일반 역할
  - 대상 서버역할
- [고정된 데이터베이스 역할](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [데이터베이스메일 역할](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - 데이터베이스메일사용자역할
- [통합 서비스 역할:](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15)
  - msdb
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> 미리 정의된 역할 이름, 스키마 이름 및 스키마 소유자를 고객별로 변경하면 서비스의 정상적인 작동에 영향을 줄 수 있습니다. 이러한 변경 사항은 감지되는 즉시 미리 정의된 값으로 되돌아갑니다.

### <a name="error-logs"></a>오류 로그

관리되는 인스턴스는 오류 로그에 자세한 정보를 배치합니다. 오류 로그에 기록된 많은 내부 시스템 이벤트가 있습니다. 사용자 지정 프로시저를 사용하여 관련이 없는 항목을 필터링하는 오류 로그를 읽습니다. 자세한 내용은 Azure Data [Studio에](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) sp_readmierrorlog 또는 [관리되는 인스턴스 확장(미리 보기)을](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) 참조하세요.

## <a name="next-steps"></a>다음 단계

- 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스란 무엇입니까?](sql-database-managed-instance.md)
- 기능 및 비교 목록은 [Azure SQL Database 기능 비교를](sql-database-features.md)참조하십시오.
- 릴리스 업데이트 및 알려진 문제 상태는 [SQL Database 릴리스 참고 노트를](sql-database-release-notes.md) 참조하십시오.
- 새 관리되는 인스턴스를 만드는 방법을 보여 주는 빠른 시작의 경우 [관리되는 인스턴스 만들기를](sql-database-managed-instance-get-started.md)참조하십시오.
