---
title: Azure SQL Database 관리 되는 인스턴스 T-SQL 차이점 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database 관리형 인스턴스 및 SQL Server 간의 T-SQL 차이점에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 5c8a15aa5198983a56a0238c1bb56f9345d07acc
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258602"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database Managed Instance 및 SQL Server 간의 T-SQL 차이점

이 문서에서는 요약 하 고 구문과 동작에 Azure SQL Database 관리 되는 인스턴스 및 온-프레미스 SQL Server 데이터베이스 엔진 간의 차이점을 설명 합니다. 다음 주제에 설명 합니다. <a name="Differences"></a>

- [가용성](#availability) 의 차이도 포함 됩니다 [Always-on](#always-on-availability) 하 고 [백업](#backup)합니다.
- [보안](#security) 차이 포함 [감사](#auditing), [인증서](#certificates)를 [자격 증명](#credential), [암호화 공급자](#cryptographic-providers)하십시오 [로그인 및 사용자](#logins-and-users), 및 [서비스 키 및 서비스 마스터 키](#service-key-and-service-master-key)합니다.
- [Configuration](#configuration) 의 차이도 포함 됩니다 [버퍼 풀 확장](#buffer-pool-extension), [데이터 정렬을](#collation)를 [호환성 수준](#compatibility-levels), [데이터베이스 미러링 ](#database-mirroring), [데이터베이스 옵션](#database-options)합니다 [SQL Server 에이전트](#sql-server-agent), 및 [테이블 옵션](#tables)합니다.
- [기능](#functionalities) 포함 [대량 삽입/OPENROWSET](#bulk-insert--openrowset)를 [CLR](#clr)를 [DBCC](#dbcc)를 [분산 트랜잭션](#distributed-transactions), [확장 이벤트](#extended-events), [외부 라이브러리](#external-libraries), [filestream 및 FileTable](#filestream-and-filetable)하십시오 [전체 텍스트 의미 체계 검색](#full-text-semantic-search), [연결 된 서버](#linked-servers), [PolyBase](#polybase), [복제](#replication)합니다 [복원](#restore-statement), [Service Broker](#service-broker)를 [저장 프로시저, 함수 및 트리거](#stored-procedures-functions-and-triggers)합니다.
- [환경 설정을](#Environment) Vnet 및 서브넷 구성 등입니다.
- [관리 되는 인스턴스 기능 서로 다른 동작을](#Changes)입니다.
- [임시 제한 사항 및 알려진된 문제](#Issues)합니다.

Managed Instance 배포 옵션은 온-프레미스 SQL Server 데이터베이스 엔진과의 높은 호환성을 제공합니다. 대부분의 SQL Server 데이터베이스 엔진 기능은 관리되는 인스턴스에서 지원됩니다.

![마이그레이션](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>가용성

### <a name="always-on-availability"></a>Always On

[고가용성](sql-database-high-availability.md) 는 관리 되는 인스턴스에 만들어지고 사용자가 제어할 수 없습니다. 다음 문은 지원 되지 않습니다.

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- 합니다 [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 절을 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) 문

### <a name="backup"></a>Backup

사용자가 전체 데이터베이스를 만들 수 있도록 관리 되는 인스턴스는 자동 백업, `COPY_ONLY` 백업 합니다. 차등, 로그 및 파일 스냅숏 백업은 지원 되지 않습니다.

- 관리 되는 인스턴스를 사용 하 여 Azure Blob storage 계정에만 인스턴스 데이터베이스를 백업할 수 있습니다.
  - `BACKUP TO URL`만 지원됩니다.
  - `FILE``TAPE`, 백업 장치에서 지원 되지 않습니다.
- 대부분의 일반 `WITH` 옵션이 지원 됩니다.
  - `COPY_ONLY` 반드시 필요 합니다.
  - `FILE_SNAPSHOT`는 지원되지 않습니다.
  - 테이프 옵션: `REWIND`, `NOREWIND`를 `UNLOAD`, 및 `NOUNLOAD` 지원 되지 않습니다.
  - 로그 관련 옵션: `NORECOVERY`, `STANDBY`, 및 `NO_TRUNCATE` 지원 되지 않습니다.

제한 사항: 

- 관리 되는 인스턴스를 사용 하 여 백업할 수 있습니다 인스턴스에 데이터베이스를 데이터베이스에 있는 최대 32 개의 스트라이프가 있는 백업으로 백업 압축을 사용 하는 경우 최대 4TB입니다.
- 사용 하 여 최대 백업 스트라이프 크기는 `BACKUP` 명령은 관리 되는 인스턴스에서 195GB(최대 blob 크기는 합니다. 백업 명령에서 스트라이프 수를 늘려 개별 스트라이프 크기를 줄이고 이 제한 내에서 유지합니다.

    > [!TIP]
    > 가상 머신 또는 온-프레미스 환경에서 SQL Server에서 데이터베이스를 백업할 때이 제한을 해결 하려면 다음을 수행할 수 있습니다.
    >
    > - 백업할 `DISK` 에 백업 하는 대신 `URL`합니다.
    > - Blob 저장소에 백업 파일을 업로드 합니다.
    > - 관리 되는 인스턴스로 복원 합니다.
    >
    > `Restore` 명령은 관리 되는 인스턴스에 업로드 된 백업 파일을 저장 하기 위해 다른 blob 유형이 사용 되기 때문에 백업 파일에서 더 큰 blob 크기를 지원 합니다.

T-SQL을 사용하는 백업에 대한 자세한 내용은 [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)을 참조하세요.

## <a name="security"></a>보안

### <a name="auditing"></a>감사

Azure SQL Database의 데이터베이스 및 SQL Server의 데이터베이스에서 감사 간의 주요 차이점은 다음과 같습니다.

- Azure SQL Database에서 관리 되는 인스턴스에 배포 옵션을 사용 하 여 감사 서버 수준에서 작동 합니다. `.xel` 로그 파일은 Azure Blob storage에 저장 됩니다.
- Azure SQL Database의 단일 데이터베이스 및 탄력적 풀 배포 옵션을 사용하면 감사는 데이터베이스 수준에서 작동합니다.
- SQL Server 온-프레미스 또는 가상 컴퓨터에서 감사 서버 수준에서 작동 합니다. 이벤트는 파일 시스템 또는 Windows 이벤트 로그에 저장 됩니다.
 
Managed Instance의 XEvent 감사는 Azure Blob Storage 대상을 지원합니다. 파일 및 Windows 로그 지원 되지 않습니다.

Azure Blob Storage에 대한 감사에서 `CREATE AUDIT` 구문의 주요 차이점은 다음과 같습니다.

- 새로운 구문 `TO URL` Azure Blob 저장소 컨테이너의 URL을 지정 하 여 제공 됩니다 여기서는 `.xel` 파일 배치 됩니다.
- 구문을 `TO FILE` 관리 되는 인스턴스에서 Windows 파일 공유에 액세스할 수 없으므로 지원 되지 않습니다.

자세한 내용은 다음을 참조하세요. 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>인증서

다음과 같은 제약 조건을 적용 하므로 관리 되는 인스턴스는 파일 공유 및 Windows 폴더에 액세스할 수 없습니다.

- 합니다 `CREATE FROM` / `BACKUP TO` 인증서에 대 한 파일 지원 되지 않습니다.
- 합니다 `CREATE` / `BACKUP` 에서 인증서 `FILE` / `ASSEMBLY` 지원 되지 않습니다. 프라이빗 키 파일은 사용할 수 없습니다. 

[CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 및 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)를 참조하세요. 
 
**해결 방법**: 인증서 또는 개인 키에 대 한 스크립트를.sql 파일로 저장 하 고 바이너리에서 만듭니다.

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>자격 증명

Azure Key Vault 및 `SHARED ACCESS SIGNATURE` ID만 지원됩니다. Windows 사용자는 지원되지 않습니다.

[CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) 및 [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql)을 참조하세요.

### <a name="cryptographic-providers"></a>암호화 공급자

관리 되는 인스턴스 파일에 액세스할 수 없으므로 암호화 공급자를 만들 수 없습니다.

- `CREATE CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)를 참조하세요.
- `ALTER CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)를 참조하세요.

### <a name="logins-and-users"></a>로그인 및 사용자

- SQL 로그인을 사용 하 여 만든 `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, 및 `FROM SID` 지원 됩니다. [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)을 참조하세요.
- Azure Active Directory (Azure AD) 서버 보안 주체 (로그인) 사용 하 여 만든 합니다 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 구문 또는 [CREATE USER에서 LOGIN [Azure AD 로그인]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 구문이 지원 됩니다 (공개 미리 보기). 이러한 로그인은 서버 수준에서 생성 됩니다.

    Managed Instance는 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 구문을 사용하여 Azure AD 데이터베이스 보안 주체를 지원합니다. 이 기능은 Azure AD 포함 데이터베이스 사용자에 게가 라고도 합니다.

- 사용 하 여 만든 Windows 로그인을 `CREATE LOGIN ... FROM WINDOWS` 구문은 지원 되지 않습니다. Azure Active Directory 로그인 및 사용자를 사용합니다.
- Azure AD 사용자에 게 인스턴스를 만든 [무제한 관리자 권한](sql-database-manage-logins.md#unrestricted-administrative-accounts)합니다.
- 데이터베이스 수준 사용자를 관리자가 아닌 Azure AD 사용 하 여 만들 수 있습니다는 `CREATE USER ... FROM EXTERNAL PROVIDER` 구문입니다. [CREATE USER ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users)를 참조하세요.
- Azure AD 서버 보안 주체 (로그인) 하나의 관리 되는 인스턴스만 해당 내에서 SQL 기능을 지원 합니다. Azure AD 사용자에 대 한 여부는 하는 동일한 Azure AD 내에서 테 넌 트 또는 다른 테 넌 트에 관계 없이 인스턴스 간 상호 작용을 필요로 하는 기능이 지원 되지 않습니다. 이러한 기능의 예는 다음과 같습니다.

  - SQL 트랜잭션 복제입니다.
  - 링크 서버입니다.

- Azure AD 그룹에 매핑된 Azure AD 로그인을 데이터베이스 소유자로 설정할 수 없습니다.
- 와 같은 다른 Azure AD 보안 주체를 사용 하 여 Azure AD 서버 수준 보안 주체 가장 지원 되는 [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) 절. EXECUTE AS 제한은 다음과 같습니다.

  - EXECUTE AS 사용자 이름이 로그인 이름과 다른 경우에 Azure AD 사용자가 지원 되지 않습니다. 예로 사용자 로그인에서 사용자 만들기 [myAadUser] 구문을 통해 만들어지면 [john@contoso.com] EXEC AS USER를 통해 가장을 시도 하 고 = _myAadUser_합니다. 만들 때를 **사용자** Azure AD 서버 주체 (로그인)에서 동일한 login_name로는 user_name 지정할 **로그인**합니다.
  - 만 SQL Server 수준 보안 주체 (로그인)의 일부인는 `sysadmin` 역할이 Azure AD 보안 주체를 대상으로 하는 다음 작업을 실행할 수 있습니다.

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Azure AD 서버 보안 주체 (로그인)에 대 한 공개 미리 보기 제한 사항:

  - 관리 되는 인스턴스에 대 한 active Directory 관리자 제한:

    - 서버를 만들려면 Azure AD 보안 주체 (로그인) 관리 되는 인스턴스 내에서 관리 되는 인스턴스를 설정 하는 데 사용 하는 Azure AD 관리자를 사용할 수 없습니다. SQL Server 계정을 사용 하 여 첫 번째 Azure AD 서버 보안 주체 (로그인)을 만들어야 합니다 하는 `sysadmin` 역할입니다. 이 임시 제한 사항 Azure AD 서버 보안 주체 (로그인)가 일반 공급 되 면 제거 됩니다. Azure AD 관리자 계정을 사용 하 여 로그인을 만들 하려고 하면 다음 오류가 표시 됩니다. `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - 표준 SQL Server 계정 (비 Azure AD)에서 master 데이터베이스에서 만든 첫 번째 Azure AD 로그인 만들어야 현재는 한 `sysadmin` 를 사용 하 여 역할 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 외부 공급자에서. 일반 공급 후에이 제한이 제거 됩니다. 초기에 만들 수 있습니다 관리 되는 인스턴스에 대 한 Active Directory 관리자를 사용 하 여 Azure AD 로그인 합니다.
    - SQL Server Management Studio 또는 SqlPackage를 사용 하 여는 데 사용 되는 DacFx (내보내기/가져오기)는 Azure AD 로그인에 대해 지원 되지 않습니다. Azure AD 서버 보안 주체 (로그인)가 일반 공급 되 면이 제한이 제거 됩니다.
    - SQL Server Management Studio를 사용 하 여 Azure AD 서버 보안 주체 (로그인)를 사용합니다.

      - 모든 인증 된 로그인을 사용 하는 Azure AD 로그인 스크립팅 지원 되지 않습니다.
      - IntelliSense는 CREATE LOGIN에서 외부 PROVIDER 문을 인식 하지 못하는 하 고 빨간색 밑줄이 보여 줍니다.

- 서버 수준 보안 주체 로그인만, 프로 비전 프로세스에서 서버 역할의 멤버와 같은 관리 되는 인스턴스에서 만들어지는 `securityadmin` 또는 `sysadmin`, 또는 다른 로그인 서버 수준의 ALTER ANY LOGIN 권한이 있는 Azure AD를 만들 수 관리 되는 인스턴스의 master 데이터베이스의 서버 주체 (로그인)입니다.
- SQL 보안 주체 로그인을 사용 하는 경우 로그인만의 일부인는 `sysadmin` 역할 만들기 로그인에 create 명령을 사용 하 여 Azure AD 계정의 수 있습니다.
- Azure AD 로그인에는 Azure SQL Database Managed Instance에 사용 되는 동일한 디렉터리 내에서 Azure AD의 멤버 여야 합니다.
- Azure AD 서버 보안 주체 (로그인) SQL Server Management Studio 18.0 preview 5 사용 하 여 시작 하는 개체 탐색기에 표시 됩니다.
- Azure AD 서버 보안 주체(로그인)를 Azure AD 관리자 계정과 겹칠 수 있습니다. Azure AD 서버 보안 주체 (로그인) 보안 주체를 확인 하 고 관리 되는 인스턴스에 권한을 적용 하는 경우 Azure AD 관리자 보다 우선 합니다.
- 인증 하는 동안 인증 보안 주체를 해결 하려면 다음 순서 대로 적용 됩니다.

    1. Azure AD 계정으로 직접 있으면 액세스 권한을 부여 인 sys.server_principals에 "E" 형식으로 Azure AD 서버 주체 (로그인)에 매핑되고, Azure AD 서버 보안 주체 (로그인)의 사용 권한을 적용 됩니다.
    2. Azure AD 계정을 Azure AD 서버 보안 주체 (로그인) 인 "X"를 입력 하는 대로 sys.server_principals에 매핑되는 Azure AD 그룹의 멤버인 경우 액세스 권한을 부여 하 고 Azure AD 그룹 로그인의 권한을 적용 합니다.
    3. Azure AD 계정 포털 구성 하는 특수 한 경우 관리 되는 인스턴스 시스템 뷰에서 존재 하지 않으면 관리 되는 인스턴스에 대 한 Azure AD 관리자는 관리 되는 인스턴스 (레거시 모드)에 대 한 Azure AD 관리자의 고정된 특별 한 권한이 적용 됩니다.
    4. Azure AD 계정으로 Azure AD 사용자가 있는 sys.database_principals의 "E" 형식으로는 데이터베이스에 직접 매핑된 경우 액세스 권한을 부여 하 고 Azure AD 데이터베이스 사용자의 권한을 적용 합니다.
    5. Azure AD 계정을 인 sys.database_principals에 있는 "X" 형식으로 데이터베이스에서 Azure AD 사용자에 매핑되는 Azure AD 그룹의 멤버인 경우 액세스 권한을 부여 하 고 Azure AD 그룹 로그인의 권한을 적용 합니다.
    6. Azure AD 사용자 계정 또는 사용자에 게 인증 하는 확인 되 면가 Azure AD 그룹 계정에 매핑되는 Azure AD 로그인이 없으면이 Azure AD 로그인에서 모든 사용 권한은 적용 됩니다.

### <a name="service-key-and-service-master-key"></a>서비스 키 및 서비스 마스터 키

- [마스터 키 백업](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) (SQL Database 서비스에서 관리 되는) 지원 되지 않습니다.
- [마스터 키 복원](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) (SQL Database 서비스에서 관리 되는) 지원 되지 않습니다.
- [서비스 마스터 키 백업](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) (SQL Database 서비스에서 관리 되는) 지원 되지 않습니다.
- [서비스 마스터 키 복원](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) (SQL Database 서비스에서 관리 되는) 지원 되지 않습니다.

## <a name="configuration"></a>구성

### <a name="buffer-pool-extension"></a>버퍼 풀 확장

- [버퍼 풀 확장](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) 지원 되지 않습니다.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`는 지원되지 않습니다. [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)을 참조하세요.

### <a name="collation"></a>데이터 정렬

기본 인스턴스 데이터 정렬은 `SQL_Latin1_General_CP1_CI_AS`이며 생성 매개 변수로 지정할 수 있습니다. [데이터 정렬](https://docs.microsoft.com/sql/t-sql/statements/collations)을 참조하세요.

### <a name="compatibility-levels"></a>호환성 수준

- 지원 되는 호환성 수준은 100, 110, 120, 130 및 140입니다.
- 100 미만의 호환성 수준은 지원되지 않습니다.
- 새 데이터베이스의 기본 호환성 수준은 140입니다. 복원 된 데이터베이스에 대 한 호환성 수준을 그대로 100 되었으면 이상.

[ALTER DATABASE 호환성 수준](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요.

### <a name="database-mirroring"></a>데이터베이스 미러링

데이터베이스 미러링은 지원되지 않습니다.

- `ALTER DATABASE SET PARTNER` 및 `SET WITNESS` 옵션은 지원되지 않습니다.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`은 지원되지 않습니다.

자세한 내용은 [ALTER DATABASE SET PARTNER 및 SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 및 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)을 참조하세요.

### <a name="database-options"></a>데이터베이스 옵션

- 다중 로그 파일은 지원되지 않습니다.
- 메모리 내 개체는 범용 서비스 계층에서 지원되지 않습니다. 
- 데이터베이스당 280 개 파일의 최대 수 반하는 범용 인스턴스당 280 개 파일의 제한이 됩니다. 일반적인 용도 계층에서 데이터와 로그 파일은이 제한에 대해 계산 됩니다. [중요 비즈니스용 계층은 데이터베이스당 32,767 파일 지원](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)합니다.
- 데이터베이스는 filestream 데이터가 포함 된 파일 그룹을 포함할 수 없습니다. .Bak에 포함 된 경우 복원이 실패 `FILESTREAM` 데이터입니다. 
- 모든 파일은 Azure Blob Storage에 배치됩니다. 파일별 IO 및 처리량은 각 개별 파일의 크기에 따라 달라집니다.

#### <a name="create-database-statement"></a>CREATE DATABASE 문

다음 제한 사항을 적용할 `CREATE DATABASE`:

- 파일 및 파일 그룹을 정의할 수 없습니다. 
- `CONTAINMENT` 옵션이 지원 되지 않습니다. 
- `WITH` 옵션은 지원 되지 않습니다. 
   > [!TIP]
   > 대 안으로 사용 하 여 `ALTER DATABASE` 후 `CREATE DATABASE` 파일을 추가 하거나 포함을 설정 하려면 데이터베이스 옵션을 설정 합니다. 

- `FOR ATTACH` 옵션이 지원 되지 않습니다.
- `AS SNAPSHOT OF` 옵션이 지원 되지 않습니다.

자세한 내용은 [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)를 참조하세요.

#### <a name="alter-database-statement"></a>ALTER DATABASE 문

일부 파일 속성은 설정하거나 변경할 수 없습니다.

- 파일 경로 지정할 수 없습니다는 `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL 문입니다. 관리되는 인스턴스에서 파일을 자동으로 저장하기 때문에 스크립트에서 `FILENAME`을 제거합니다. 
- 파일 이름을 사용 하 여 변경할 수 없습니다는 `ALTER DATABASE` 문입니다.

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

자세한 내용은 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)를 참조하세요.

### <a name="sql-server-agent"></a>SQL Server 에이전트

- SQL Server 에이전트 설정은 읽기 전용입니다. 프로시저 `sp_set_agent_properties` 관리 되는 인스턴스에서 지원 되지 않습니다. 
- 교육
  - T-SQL 작업 단계가 지원됩니다.
  - 다음 복제 작업이 지원됩니다.
    - 트랜잭션 로그 판독기
    - 스냅샷
    - 배포자
  - SSIS 작업 단계는 지원 됩니다.
  - 다른 유형의 작업 단계는 현재 지원 되지 않습니다.
    - 병합 복제 작업 단계는 지원 되지 않습니다. 
    - 큐 판독기는 지원되지 않습니다. 
    - 명령 셸은 아직 지원 되지 않습니다.
  - 관리 되는 인스턴스 외부 리소스에 액세스할 수 없습니다, 그리고 robocopy를 통한 네트워크 공유 하는 예를 들어 있습니다. 
  - SQL Server Analysis Services가 지원 되지 않습니다.
- 알림은 부분적으로 지원됩니다.
- 데이터베이스 메일 프로필을 구성 하는 필요 하지만 전자 메일 알림을 지원 됩니다. SQL Server 에이전트에는 하나의 데이터베이스 메일 프로필을 사용할 수 있으며 호출 해야 `AzureManagedInstance_dbmail_profile`합니다. 
  - 호출기는 지원되지 않습니다. 
  - NetSend는 지원되지 않습니다.
  - 경고가 아직 지원 되지 않습니다.
  - 프록시 지원 되지 않습니다. 
- EventLog는 지원 되지 않습니다.

다음 기능은 현재 지원 되지 않습니다 하지만 나중에 사용할:

- 프록시
- 유휴 CPU 대 한 작업 예약
- 사용 하도록 설정 하거나 에이전트를 사용 하지 않도록 설정
- 경고

SQL Server 에이전트에 대한 자세한 내용은 [SQL Server 에이전트](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)를 참조하세요.

### <a name="tables"></a>테이블

다음 표에서 지원 되지 않습니다.

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

만들고 테이블을 변경 하는 방법에 대 한 정보를 참조 하세요 [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 하 고 [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)합니다.

## <a name="functionalities"></a>기능

### <a name="bulk-insert--openrowset"></a>대량 삽입/openrowset

파일 공유 및 Windows 폴더에 관리 되는 인스턴스를 액세스할 수 없으므로 Azure Blob storage에서 파일을 가져올 수 있어야 합니다.

- `DATASOURCE` 에 필요 합니다 `BULK INSERT` Azure Blob storage에서 파일을 가져와야 하는 동안 명령입니다. [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)를 참조하세요.
- `DATASOURCE` 에 필요 합니다 `OPENROWSET` Azure Blob storage에서 파일의 콘텐츠를 읽을 때 작동 합니다. [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.

### <a name="clr"></a>CLR

다음과 같은 제약 조건을 적용 하므로 관리 되는 인스턴스는 파일 공유 및 Windows 폴더에 액세스할 수 없습니다.

- `CREATE ASSEMBLY FROM BINARY`만 지원됩니다. [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)를 참조하세요. 
- `CREATE ASSEMBLY FROM FILE`는 지원되지 않습니다. [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)을 참조하세요.
- `ALTER ASSEMBLY`는 파일을 참조할 수 없습니다. [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)를 참조하세요.

### <a name="dbcc"></a>DBCC

SQL Server에서 사용할 수 있는 문서화 되지 않은 DBCC 문은 관리 되는 인스턴스에서 지원 되지 않습니다.

- `Trace flags`는 지원되지 않습니다. 참조 [추적 플래그](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)합니다.
- `DBCC TRACEOFF`는 지원되지 않습니다. [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)를 참조하세요.
- `DBCC TRACEON`은 지원되지 않습니다. [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)을 참조하세요.

### <a name="distributed-transactions"></a>분산 트랜잭션

MSDTC 및 [탄력적 트랜잭션](sql-database-elastic-transactions-overview.md) 현재 관리 되는 인스턴스에서 지원 되지 않습니다.

### <a name="extended-events"></a>확장 이벤트

확장 이벤트 (Xevent)에 대 한 일부 Windows 관련 대상은 지원 되지 않습니다.

- `etw_classic_sync` 대상 지원 되지 않습니다. 저장소 `.xel` Azure Blob storage의 파일입니다. [etw_classic_sync 대상](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)을 참조하세요.
- `event_file` 대상 지원 되지 않습니다. 저장소 `.xel` Azure Blob storage의 파일입니다. [event_file 대상](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)을 참조하세요.

### <a name="external-libraries"></a>외부 라이브러리

데이터베이스 내 R 및 Python, 외부 라이브러리 되지 지원 합니다. [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)를 참조하세요.

### <a name="filestream-and-filetable"></a>Filestream 및 FileTable

- filestream 데이터는 지원 되지 않습니다.
- 데이터베이스에 있는 파일 그룹이 포함할 수 없습니다 `FILESTREAM` 데이터입니다.
- `FILETABLE`는 지원되지 않습니다.
- 테이블에 `FILESTREAM` 형식을 사용할 수 없습니다.
- 지원되지 않는 함수는 다음과 같습니다.
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

자세한 내용은 [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) 및 [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)를 참조하세요.

### <a name="full-text-semantic-search"></a>전체 텍스트 의미 체계 검색

[의미 체계 검색](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server)은 지원되지 않습니다.

### <a name="linked-servers"></a>연결된 서버

Managed Instance의 연결된 서버는 제한된 개수의 대상을 지원합니다.

- 지원 되는 대상에는 SQL Server 및 SQL Database입니다.
- 지원 되지 않는 대상 파일, Analysis Services 및 기타 RDBMS 됩니다.

작업

- 인스턴스 간 쓰기 트랜잭션은 지원되지 않습니다.
- `sp_dropserver`는 연결된 서버를 삭제하는 데 지원됩니다. [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)를 참조하세요.
- `OPENROWSET` 만 SQL Server 인스턴스에서 쿼리를 실행 하는 함수를 사용할 수 있습니다. 관리 되는 온-프레미스, 수 또는 virtual machines입니다. [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.
- `OPENDATASOURCE` 만 SQL Server 인스턴스에서 쿼리를 실행 하는 함수를 사용할 수 있습니다. 관리 되는 온-프레미스, 수 또는 virtual machines입니다. 만 `SQLNCLI`, `SQLNCLI11`, 및 `SQLOLEDB` 값 공급자로 지원 됩니다. 예는 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`입니다. [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)를 참조하세요.

### <a name="polybase"></a>PolyBase

외부 테이블 참조 하는 HDFS 또는 Azure Blob 저장소의 파일 지원 되지 않습니다. PolyBase에 대 한 정보를 참조 하세요 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)합니다.

### <a name="replication"></a>복제

복제는 Managed Instance의 공개 미리 보기에 사용할 수 있습니다. 복제에 대 한 정보를 참조 하세요 [SQL Server 복제](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)합니다.

### <a name="restore-statement"></a>RESTORE 문 

- 지원 되는 구문:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- 지원 되지 않는 구문을 사용 하십시오.
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- 원본: 
  - `FROM URL` (Azure Blob storage)에 지원 되는 옵션입니다.
  - `FROM DISK`/`TAPE`/백업 디바이스는 지원되지 않습니다.
  - 백업 세트는 지원되지 않습니다.
- `WITH` 없음 같은 옵션이 지원 되지 않음 `DIFFERENTIAL` 또는 `STATS`합니다.
- `ASYNC RESTORE`: 클라이언트 연결이 중단 되더라도 복원이 계속 됩니다. 사용자 연결을 삭제 하는 경우 확인할 수 있습니다는 `sys.dm_operation_status` CREATE 및 DROP 데이터베이스 및 복원 작업의 상태에 대 한 보기입니다. 자세한 내용은 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)를 참조하세요. 

다음 데이터베이스 옵션 설정 되거나 재정의 되 고 나중에 변경할 수 없습니다. 

- `NEW_BROKER` 브로커는.bak 파일에서 활성화 되어 있지 않으면입니다. 
- `ENABLE_BROKER` 브로커는.bak 파일에서 활성화 되어 있지 않으면입니다. 
- `AUTO_CLOSE=OFF` .bak 파일에서 데이터베이스에 `AUTO_CLOSE=ON`입니다. 
- `RECOVERY FULL` .bak 파일에서 데이터베이스에 있으면 `SIMPLE` 또는 `BULK_LOGGED` 복구 모드.
- 메모리 최적화 파일 그룹이 추가 되 고 원본.bak 파일의 경우 XTP를 호출 합니다. 
- 모든 기존 메모리 최적화 파일 그룹은 XTP로 이름이 변경 됩니다. 
- `SINGLE_USER` 및 `RESTRICTED_USER` 옵션으로 변환 됩니다 `MULTI_USER`합니다.

제한 사항: 

- `.BAK` 여러 백업 세트를 포함 하는 파일을 복원할 수 없습니다. 
- `.BAK` 여러 로그 파일을 포함 하는 파일을 복원할 수 없습니다.
- .Bak에 포함 된 경우 복원이 실패 `FILESTREAM` 데이터입니다.
- 일반 목적 인스턴스에 현재 메모리 내 개체에 있는 데이터베이스를 포함 하는 백업은 복원할 수 없습니다. 복원 문에 대 한 자세한 내용은 [RESTORE 문](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)합니다.

### <a name="service-broker"></a>Service Broker

인스턴스 간 서비스 broker는 지원되지 않습니다.

- `sys.routes`: 필수 요소로, sys.routes에서 주소를 선택 해야 합니다. 주소 모든 경로에서 LOCAL 이어야 합니다. [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)를 참조하세요.
- `CREATE ROUTE`: 사용할 수 없습니다 `CREATE ROUTE` 사용 하 여 `ADDRESS` 이외의 `LOCAL`합니다. [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)를 참조하세요.
- `ALTER ROUTE`: 사용할 수 없습니다 `ALTER ROUTE` 사용 하 여 `ADDRESS` 이외의 `LOCAL`합니다. [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)를 참조하세요. 

### <a name="stored-procedures-functions-and-triggers"></a>저장된 프로시저, 함수 및 트리거

- `NATIVE_COMPILATION` 일반적인 용도 계층에서 지원 되지 않습니다.
- 지원되지 않는 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 옵션은 다음과 같습니다. 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`는 지원되지 않습니다. [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)를 참조하세요.
- `xp_cmdshell`은 지원되지 않습니다. [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)을 참조하세요.
- `Extended stored procedures` 지원 되지 않으며, 이때 `sp_addextendedproc`  및 `sp_dropextendedproc`합니다. 참조 [확장 저장된 프로시저](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)합니다.
- `sp_attach_db`, `sp_attach_single_file_db` 및 `sp_detach_db`는 지원되지 않습니다. [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 및 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)를 참조하세요.

## <a name="Environment"></a>Environmet 제약 조건

### <a name="subnet"></a>서브넷
- 관리 되는 인스턴스에 대 한 예약 된 서브넷에서 다른 리소스 (예: 가상 머신)을 배치할 수 없습니다. 이러한 리소스는 다른 사용자에 배치할 서브넷입니다.
- 서브넷의 사용 가능한 충분 한 수 있어야 합니다. [IP 주소](sql-database-managed-instance-connectivity-architecture.md#network-requirements)합니다. 최소 16 이며 서브넷에서 IP 주소를 최소한 32가 권장 됩니다.
- [서비스 끝점을 관리 되는 인스턴스의 서브넷과 연결할 수 없습니다](sql-database-managed-instance-connectivity-architecture.md#network-requirements)합니다. 가상 네트워크를 만들 때 서비스 끝점 옵션은 사용 되지 않음을 확인 합니다.
- 서브넷에 배치할 수 있는 인스턴스의 종류와 수는 일부의 [제약 조건 및 제한](sql-database-managed-instance-resource-limits.md#strategies-for-deploying-mixed-general-purpose-and-business-critical-instances)
- 몇 가지 [서브넷에 적용 해야 하는 보안 규칙](sql-database-managed-instance-connectivity-architecture.md#network-requirements)합니다.

### <a name="vnet"></a>VNET
- 리소스 모델을 사용 하 여 VNet을 배포할 수 있습니다-VNet에 대 한 클래식 모델이 지원 되지 않습니다.
- App Service Environment, Logic apps 및 관리 되는 인스턴스 (연결 된 서버 또는 지역에서 복제, 트랜잭션 복제에 사용 됨)와 같은 일부 서비스가 Vnet 를사용하여연결된경우다른지역에관리되는인스턴스를액세스할수없습니다[전역 피어 링](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)합니다. VNet 대 VNet을 통해 VNet 게이트웨이 또는 ExpressRoute를 통해 이러한 리소스에 연결할 수 있습니다.

## <a name="Changes"></a> 동작 변경

다른 결과를 반환하는 변수, 함수 및 뷰는 다음과 같습니다.

- `SERVERPROPERTY('EngineEdition')` 8 값을 반환합니다. 이 속성은 관리되는 인스턴스를 고유하게 식별합니다. [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `SERVERPROPERTY('InstanceName')` SQL Server 관리 되는 인스턴스에 적용 되지 않습니다에 대 한 해당 인스턴스로 개념이 존재 하기 때문에 NULL을 반환 합니다. [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `@@SERVERNAME` 내 managed instance.wcus17662feb9ce98.database.windows.net 예를 들어 전체 DNS "연결 가능" 이라는 반환합니다. [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)을 참조하세요. 
- `SYS.SERVERS` 와 같은 전체 DNS "연결 가능" 이라는 반환 `myinstance.domain.database.windows.net` 속성 "name" 및 "data_source." [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)를 참조하세요.
- `@@SERVICENAME` SQL Server 관리 되는 인스턴스에 적용 되지 않습니다에 대 한 개념으로는 서비스의 존재 하기 때문에 NULL을 반환 합니다. [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)을 참조하세요.
- `SUSER_ID`가 지원됩니다. Azure AD 로그인이 sys.syslogins에 없는 경우 NULL을 반환 합니다. [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)를 참조하세요. 
- `SUSER_SID`는 지원되지 않습니다. 임시 알려진 문제는 잘못 된 데이터가 반환 됩니다. [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)를 참조하세요. 

## <a name="Issues"> </a> 알려진 문제 및 제한 사항

### <a name="tempdb-size"></a>TEMPDB 크기

최대 파일 크기인 `tempdb` 범용 계층에서 코어당 24GB 보다 클 수 없습니다. 최대 `tempdb` 중요 비즈니스 계층의 크기는 인스턴스 저장소 크기를 제한 합니다. `tempdb` 데이터베이스는 항상 12 데이터 파일로 분할 됩니다. 파일당이 최대 크기를 변경할 수 없으며, 새 파일에 추가할 수 없습니다 및 `tempdb`합니다. 일부 쿼리가 둘 24GB의 코어당 해야 하는 경우 오류를 반환할 수 있습니다 `tempdb`합니다. `tempdb` 인스턴스 시작 또는 장애 조치 및 모든 변경에서 만든 빈 데이터베이스를 다시 생성 항상 됩니다 `tempdb` 유지 되지 것입니다. 

### <a name="cant-restore-contained-database"></a>포함 된 데이터베이스를 복원할 수 없습니다.

관리 되는 인스턴스를 복원할 수 없습니다 [포함 된 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)합니다. 관리 되는 인스턴스에서 기존 포함 된 데이터베이스의 지정 시간 복원이 작동 하지 않습니다. 이 문제는 곧 해결 될 예정입니다. 한편 관리 되는 인스턴스에서 배치 되는 데이터베이스에서 포함 옵션을 제거 하는 것이 좋습니다. 프로덕션 데이터베이스에 대 한 포함 옵션을 사용 하지 마세요. 

### <a name="exceeding-storage-space-with-small-database-files"></a>작은 데이터베이스 파일이 포함된 저장소 공간 초과

`CREATE DATABASE`하십시오 `ALTER DATABASE ADD FILE`, 및 `RESTORE DATABASE` 인스턴스는 Azure 저장소 용량 한도 도달 수 있기 때문에 문이 실패할 수 있습니다.

관리 되는 범용 인스턴스마다 최대 35TB Azure Premium 디스크 공간에 대 한 예약 된 저장소에 있습니다. 각 데이터베이스 파일은 별도 물리적 디스크에 배치 됩니다. 디스크 크기는 128GB, 256GB, 512GB, 1TB 또는 4TB일 수 있습니다. 이 속성을 사용 하지 않는 공간이 디스크에는 요금이 청구 되지 않습니다 하지만 Azure Premium 디스크 크기의 총 합계는 35TB를 초과할 수 없습니다. 경우에 따라 총에서 8TB가 필요 하지 않습니다는 관리 되는 인스턴스에 대 한 35tb Azure 제한을 내부 조각화로 인해 저장소 크기를 초과할 수 있습니다.

예를 들어 관리 되는 일반 목적 인스턴스는 1.2TB 크기는 4TB 디스크에 저장 된 파일을 하나 있을 수 있습니다. 또한 별도 128GB 디스크에 배치 되는 각 1GB 크기는 248 파일이 있을 수 있습니다. 이 예제에서:

- 전체 할당된 디스크 스토리지 크기는 1x4TB + 248x128GB = 35TB입니다.
- 인스턴스에서 데이터베이스에 대해 예약된 총 공간은 1x1.2TB + 248x1GB = 1.4TB입니다.

이 예제에는 특정 한 상황에서는 파일의 특정 배포로 인해 관리 되는 인스턴스 예상치 못한 되도록 하는 경우 연결된 된 Azure Premium 디스크에 대 한 예약 된 35 TB 제한에 도달할 수 있는지 보여 줍니다.

이 예제에서는 기존 데이터베이스 작업을 계속 새 파일이 추가 되지 않습니다 있다면 문제 없이 커질 수 있습니다. 새 데이터베이스를 만들거나 새 디스크 드라이브에 공간이 충분 하지 않기 때문에 모든 데이터베이스의 총 크기가 인스턴스 크기 제한에 도달 하지 않는 경우에 복원 될 수 없습니다. 이 경우 반환 되는 오류를 명확 하지 않습니다.

할 수 있습니다 [남아 있는 파일 수를 확인](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) 시스템 뷰를 사용 하 여 합니다. 이 한도 도달 하면 하려고 [빈 데이터 요소 및 DBCC SHRINKFILE 문을 사용 하 여 일부 더 작은 파일을 삭제](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) 전환 또는 합니다 [중요 비즈니스용 계층에서는이 제한이 없는](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>SAS 키의 잘못 된 구성 하는 동안 데이터베이스 복원

`RESTORE DATABASE` .bak 파일을을.bak을 읽으려고 계속 시도 수 수 읽기 파일과 오랜 기간 후 오류를 반환 하는 경우에 공유 액세스 서명을 `CREDENTIAL` 올바르지 않습니다. SAS 키가 올바른 해야 하는 데이터베이스를 복원 하기 전에 RESTORE HEADERONLY를 실행 합니다.
앞에 오는 제거 해야 `?` Azure portal을 사용 하 여 생성 되는 SAS 키에서 합니다.

### <a name="tooling"></a>도구

SQL Server Management Studio 및 SQL Server Data Tools는 관리 되는 인스턴스를 액세스 하는 동안 몇 가지 문제가 있을 수 있습니다.

- 사용 하 여 Azure AD 서버 보안 주체 (로그인) 및 사용자 (공개 미리 보기) SQL Server Data Tools를 사용 하 여 현재 지원 되지 않습니다.
- SQL Server Management Studio에서 Azure AD 서버 보안 주체 (로그인) 및 사용자 (공개 미리 보기)에 대 한 스크립팅이 지원 되지 않습니다.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>일부 뷰, 로그 및 메시지에 잘못된 데이터베이스 이름이 있음

몇 가지 시스템 뷰, 성능 카운터, 오류 메시지, XEvent 및 오류 로그 항목에는 실제 데이터베이스 이름 대신 GUID 데이터베이스 식별자가 표시됩니다. 나중에 실제 데이터베이스 이름으로 대체 하는 것 때문에이 GUID 식별자를 의존 하지 마십시오.

### <a name="database-mail"></a>데이터베이스 메일

합니다 `@query` 의 매개 변수를 [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 프로시저 작동 하지 않습니다.

### <a name="database-mail-profile"></a>데이터베이스 메일 프로필

SQL Server 에이전트에서 사용 하는 데이터베이스 메일 프로필을 호출 해야 `AzureManagedInstance_dbmail_profile`합니다. 다른 데이터베이스 메일 프로필 이름에 대 한 제한은 없습니다.

### <a name="error-logs-arent-persisted"></a>오류 로그 유지 되지 않습니다.

관리 되는 인스턴스에서 사용할 수 있는 오류 로그 유지 되지 않습니다 하 고 해당 크기는 최대 저장소 용량 한도에 포함 되지 않습니다. 장애 조치가 발생 하는 경우 오류 로그를 자동으로 지울 수 있습니다.

### <a name="error-logs-are-verbose"></a>오류 로그에 자세한 정보가 표시됨

오류 로그에 대 한 자세한 정보를 배치 하는 관리 되는 인스턴스 및 대부분의 관련이 없습니다. 오류 로그에서 정보의 양을 앞으로 줄어듭니다.

**해결 방법:** 관련이 없는 일부 항목을 필터링 하는 오류 로그를 읽을 사용자 지정 프로시저를 사용 합니다. 자세한 내용은 [관리 되는 인스턴스 – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)합니다.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>트랜잭션 범위는 같은 인스턴스 내에서 두 데이터베이스에서 지원 되지 않습니다.

`TransactionScope` .net에서 클래스는 동일한 트랜잭션 범위에서 같은 인스턴스 내에서 두 데이터베이스에 두 개의 쿼리를 보내는 경우 작동 하지 않습니다.

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

이 코드는 작동 하지만 동일한 인스턴스 내의 데이터를 사용 하 여, MSDTC 필요 합니다.

**해결 방법:** 사용 하 여 [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) 에서 두 개의 연결을 사용 하는 대신 연결 컨텍스트를 다른 데이터베이스를 사용 하도록 합니다.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 모듈 및 연결 된 서버에 따라 로컬 IP 주소를 참조할 수 없습니다.

경우에 따라 현재 인스턴스를 참조 하는 분산된 쿼리 및 관리 되는 인스턴스 및 연결 된 서버에 배치 하는 CLR 모듈의 로컬 인스턴스 IP를 확인할 수 없습니다. 이 오류는 일시적인 문제입니다.

**해결 방법:** 가능한 경우 CLR 모듈에서 컨텍스트 연결을 사용 합니다.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>서비스 관리 키를 사용 하 여 TDE로 암호화 된 데이터베이스 사용자가 시작한 백업을 지원 하지 않습니다.

실행할 수 없습니다 `BACKUP DATABASE ... WITH COPY_ONLY` 서비스 관리 투명 한 데이터 암호화 (TDE) 암호화 된 데이터베이스입니다. 서비스 관리 TDE 백업을 내부 TDE 키를 사용 하 여 암호화 되도록 합니다. 백업을 복원할 수 없습니다 있도록 키를 내보낼 수 없습니다.

**해결 방법:** 자동 백업 및 지정 시간 복원을 사용 하거나 사용 하 여 [고객 관리 (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) 대신 합니다. 데이터베이스에서 암호화를 비활성화할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- 관리 되는 인스턴스에 대 한 자세한 내용은 참조 하세요. [관리 되는 인스턴스란 무엇 인가요?](sql-database-managed-instance.md)
- 기능 및 비교 목록은 참조 하세요 [Azure SQL Database 기능 비교](sql-database-features.md)합니다.
- 새 관리 되는 인스턴스를 만드는 방법을 보여 주는 빠른 시작을 참조 하세요 [관리 되는 인스턴스를 만들고](sql-database-managed-instance-get-started.md)합니다.
