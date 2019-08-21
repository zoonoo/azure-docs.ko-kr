---
title: Azure SQL Database 관리 되는 인스턴스 T-sql 차이점 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database 관리형 인스턴스 및 SQL Server 간의 T-SQL 차이점에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 08/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: b792c0fc5d02a84d45b47ac68e0058144f31e673
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641009"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>관리 되는 인스턴스 T-sql의 차이점, 제한 사항 및 알려진 문제

이 문서에서는 Azure SQL Database 관리 되는 인스턴스와 온-프레미스 SQL Server 데이터베이스 엔진 간의 구문과 동작의 차이점을 요약 하 고 설명 합니다. 관리되는 인스턴스 배포 옵션은 온-프레미스 SQL Server 데이터베이스 엔진과의 높은 호환성을 제공합니다. 대부분의 SQL Server 데이터베이스 엔진 기능은 관리되는 인스턴스에서 지원됩니다.

![마이그레이션](./media/sql-database-managed-instance/migration.png)

Managed Instance에서 도입 된 몇 가지 PaaS 제한 사항이 있으며 SQL Server에 비해 일부 동작이 변경 되었습니다. 차이점은 다음 범주로 구분 됩니다.<a name="Differences"></a>

- [가용성](#availability) 은 [Always On](#always-on-availability) 및 [백업의](#backup)차이를 포함 합니다.
- [보안](#security) 에는 [감사](#auditing), [인증서](#certificates), [자격 증명](#credential), [암호화 공급자](#cryptographic-providers), [로그인 및 사용자](#logins-and-users), [서비스 키 및 서비스 마스터 키](#service-key-and-service-master-key)의 차이점이 포함 됩니다.
- [구성](#configuration) 에는 [버퍼 풀 확장](#buffer-pool-extension), [데이터 정렬](#collation), [호환성 수준](#compatibility-levels), [데이터베이스 미러링](#database-mirroring), [데이터베이스 옵션](#database-options), [SQL Server 에이전트](#sql-server-agent)및 [테이블 옵션](#tables)의 차이점이 포함 됩니다.
- [기능](#functionalities) 에는 [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [분산 트랜잭션](#distributed-transactions), [확장 이벤트](#extended-events), [외부 라이브러리](#external-libraries), [filestream 및 FileTable](#filestream-and-filetable)이 포함 됩니다. [전체 텍스트 의미 체계 검색](#full-text-semantic-search), [연결 된 서버](#linked-servers), [PolyBase](#polybase), [복제](#replication), [복원](#restore-statement), [Service Broker](#service-broker), [저장 프로시저, 함수 및 트리거가](#stored-procedures-functions-and-triggers)있습니다.
- Vnet 및 서브넷 구성과 같은 [환경 설정](#Environment) .

이러한 기능 중 대부분은 아키텍처 제약 조건 이며 서비스 기능을 나타냅니다.

또한이 페이지에서는 관리 되는 인스턴스에서 검색 된 [알려진 알려진 문제](#Issues) 에 대해 설명 하며,이 문제는 나중에 해결 될 예정입니다.

## <a name="availability"></a>가용성

### <a name="always-on-availability"></a>Always On

[고가용성](sql-database-high-availability.md) 은 관리 되는 인스턴스에 기본 제공 되며 사용자가 제어할 수 없습니다. 다음 문은 지원 되지 않습니다.

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) 문의 [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 절

### <a name="backup"></a>백업

관리 되는 인스턴스에는 자동 백업이 있으므로 사용자는 전체 데이터베이스 `COPY_ONLY` 백업을 만들 수 있습니다. 차등, 로그 및 파일 스냅숏 백업은 지원 되지 않습니다.

- 관리 되는 인스턴스를 사용 하면 Azure Blob storage 계정에만 인스턴스 데이터베이스를 백업할 수 있습니다.
  - `BACKUP TO URL`만 지원됩니다.
  - `FILE`, `TAPE`및 백업 장치는 지원 되지 않습니다.
- 대부분의 일반 `WITH` 옵션이 지원 됩니다.
  - `COPY_ONLY`필수입니다.
  - `FILE_SNAPSHOT`는 지원되지 않습니다.
  - 테이프 옵션: `REWIND` `NOREWIND` ,,`NOUNLOAD` 및는 지원되지않습니다.`UNLOAD`
  - 로그 관련 옵션: `NORECOVERY`, `STANDBY`및 `NO_TRUNCATE` 은 지원 되지 않습니다.

제한 사항: 

- 관리 되는 인스턴스를 사용 하면 최대 32 개의 스트라이프를 사용 하 여 백업에 인스턴스 데이터베이스를 백업할 수 있습니다. 백업 압축을 사용 하는 경우 최대 2TB의 데이터베이스에 충분 합니다.
- Tde ( `BACKUP DATABASE ... WITH COPY_ONLY` 서비스 관리 투명한 데이터 암호화)로 암호화 된 데이터베이스에서는 실행할 수 없습니다. 서비스 관리 TDE는 내부 TDE 키를 사용 하 여 백업을 암호화 합니다. 키를 내보낼 수 없으므로 백업을 복원할 수 없습니다. 자동 백업 및 지정 시간 복원을 사용 하거나 대신 [고객 관리 (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) 를 사용 합니다. 데이터베이스에서 암호화를 사용 하지 않도록 설정할 수도 있습니다.
- 관리 되는 인스턴스에서 `BACKUP` 명령을 사용 하 여 최대 백업 스트라이프 크기는 최대 blob 크기인 195 GB입니다. 백업 명령에서 스트라이프 수를 늘려 개별 스트라이프 크기를 줄이고 이 제한 내에서 유지합니다.

    > [!TIP]
    > 이 제한을 해결 하기 위해 온-프레미스 환경 또는 가상 머신에서 SQL Server에서 데이터베이스를 백업 하는 경우 다음 작업을 수행할 수 있습니다.
    >
    > - `DISK` 에`URL`백업 하는 대신에 백업 합니다.
    > - Blob 저장소에 백업 파일을 업로드 합니다.
    > - 관리 되는 인스턴스로 복원 합니다.
    >
    > 업로드 된 백업 파일의 저장소에 다른 blob 유형이 사용 되기 때문에 관리 되는 인스턴스의 명령은백업파일의더큰blob크기를지원합니다.`Restore`

T-SQL을 사용하는 백업에 대한 자세한 내용은 [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)을 참조하세요.

## <a name="security"></a>보안

### <a name="auditing"></a>감사

Azure SQL Database의 데이터베이스 및 SQL Server의 데이터베이스에서 감사 간의 주요 차이점은 다음과 같습니다.

- Azure SQL Database에서 관리 되는 인스턴스 배포 옵션을 사용 하는 경우 감사는 서버 수준에서 작동 합니다. `.xel` 로그 파일은 Azure Blob 저장소에 저장 됩니다.
- Azure SQL Database의 단일 데이터베이스 및 탄력적 풀 배포 옵션을 사용하면 감사는 데이터베이스 수준에서 작동합니다.
- 온-프레미스 또는 가상 컴퓨터 SQL Server에서 감사는 서버 수준에서 작동 합니다. 이벤트는 파일 시스템 또는 Windows 이벤트 로그에 저장 됩니다.
 
관리되는 인스턴스의 XEvent 감사는 Azure Blob Storage 대상을 지원합니다. 파일 및 Windows 로그는 지원 되지 않습니다.

Azure Blob Storage에 대한 감사에서 `CREATE AUDIT` 구문의 주요 차이점은 다음과 같습니다.

- 파일이 배치 되 `TO URL` 는 Azure Blob 저장소 컨테이너의 URL을 지정 하는 데 사용할 수 있는 새 구문이 제공 됩니다. `.xel`
- 관리 되 `TO FILE` 는 인스턴스에서 Windows 파일 공유에 액세스할 수 없으므로 구문은 지원 되지 않습니다.

자세한 내용은 다음을 참조하세요. 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>인증서

관리 되는 인스턴스는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음과 같은 제약 조건이 적용 됩니다.

- 파일이인증서에대해`CREATE FROM`지원되지않습니다. / `BACKUP TO`
- 에서 `CREATE` / 인증서는`BACKUP` 지원 되지 않습니다. `FILE` / `ASSEMBLY` 프라이빗 키 파일은 사용할 수 없습니다. 

[CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 및 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)를 참조하세요. 
 
**해결 방법**: 인증서 또는 개인 키에 대 한 스크립트를 .sql 파일로 저장 하 고 이진 파일에서 만듭니다.

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>자격 증명

Azure Key Vault 및 `SHARED ACCESS SIGNATURE` ID만 지원됩니다. Windows 사용자는 지원되지 않습니다.

[CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) 및 [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql)을 참조하세요.

### <a name="cryptographic-providers"></a>암호화 공급자

관리 되는 인스턴스는 파일에 액세스할 수 없으므로 암호화 공급자를 만들 수 없습니다.

- `CREATE CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)를 참조하세요.
- `ALTER CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)를 참조하세요.

### <a name="logins-and-users"></a>로그인 및 사용자

- , `FROM CERTIFICATE` `FROM ASYMMETRIC KEY`및 를사용하여만든SQL로그인이지원됩니다.`FROM SID` [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)을 참조하세요.
- [CREATE login](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 구문을 사용 하 여 만든 Azure Active Directory (azure ad) 서버 보안 주체 (로그인) 또는 [로그인에서 사용자 만들기 [Azure ad 로그인]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 구문은 지원 됩니다 (공개 미리 보기). 이러한 로그인은 서버 수준에서 만들어집니다.

    관리 되는 인스턴스는 구문을 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`사용 하 여 Azure AD 데이터베이스 보안 주체를 지원 합니다. 이 기능은 Azure AD에 포함 된 데이터베이스 사용자 라고도 합니다.

- `CREATE LOGIN ... FROM WINDOWS` 구문을 사용 하 여 만든 Windows 로그인은 지원 되지 않습니다. Azure Active Directory 로그인 및 사용자를 사용합니다.
- 인스턴스를 만든 Azure AD 사용자에 게는 [무제한 관리자 권한이](sql-database-manage-logins.md#unrestricted-administrative-accounts)있습니다.
- 관리자가 아닌 Azure AD 데이터베이스 수준 사용자는 `CREATE USER ... FROM EXTERNAL PROVIDER` 구문을 사용 하 여 만들 수 있습니다. [CREATE USER ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users)를 참조하세요.
- Azure AD 서버 보안 주체 (로그인)는 하나의 관리 되는 인스턴스 내 에서만 SQL 기능을 지원 합니다. 동일한 Azure AD 테 넌 트 또는 다른 테 넌 트 내에 있든 관계 없이 크로스 인스턴스 상호 작용이 필요한 기능은 Azure AD 사용자에 대해 지원 되지 않습니다. 이러한 기능의 예는 다음과 같습니다.

  - SQL 트랜잭션 복제.
  - 링크 서버.

- Azure AD 그룹에 매핑된 Azure AD 로그인을 데이터베이스 소유자로 설정할 수 없습니다.
- 다른 Azure AD 주체를 사용 하 여 Azure AD 서버 수준 보안 주체의 가장을 지원 합니다 (예: [EXECUTE as](/sql/t-sql/statements/execute-as-transact-sql) 절). 실행 제한 사항은 다음과 같습니다.

  - 이름이 로그인 이름과 다를 경우 Azure AD 사용자에 대해 EXECUTE AS 사용자가 지원 되지 않습니다. 예를 들어, 사용자가 LOGIN [john@contoso.com]에서 CREATE user [myAadUser] 구문을 통해 만들어지고 가장이 EXEC를 user = _myAadUser_로 시도 하는 경우를 들 수 있습니다. Azure AD 서버 보안 주체 (로그인)에서 **사용자** 를 만드는 경우 User_name을 **로그인**에서 동일한 login_name로 지정 합니다.
  - `sysadmin` 역할의 일부인 SQL Server 수준 보안 주체 (로그인)만 Azure AD 보안 주체를 대상으로 하는 다음 작업을 실행할 수 있습니다.

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Azure AD 서버 보안 주체 (로그인)에 대 한 공개 미리 보기 제한 사항:

  - 관리 되는 인스턴스에 대 한 Active Directory 관리자 제한:

    - 관리 되는 인스턴스를 설정 하는 데 사용 되는 Azure AD 관리자를 사용 하 여 관리 되는 인스턴스 내에 Azure AD 서버 보안 주체 (로그인)를 만들 수 없습니다. `sysadmin` 역할의 SQL Server 계정을 사용 하 여 첫 번째 Azure AD 서버 보안 주체 (로그인)를 만들어야 합니다. Azure AD 서버 보안 주체 (로그인)가 일반 공급 되 면이 임시 제한이 제거 됩니다. Azure AD 관리자 계정을 사용 하 여 로그인을 만들려고 하면 다음과 같은 오류가 표시 됩니다.`Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - 현재 master 데이터베이스에서 만든 첫 번째 azure ad 로그인은 외부 공급자에서 `sysadmin` [만들기 로그인](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 을 사용 하 여 역할인 표준 SQL Server 계정 (비 Azure ad)에서 만들어야 합니다. 일반 공급 후에는 이러한 제한이 제거 됩니다. 그런 다음 관리 되는 인스턴스의 Active Directory admin을 사용 하 여 초기 Azure AD 로그인을 만들 수 있습니다.
    - SQL Server Management Studio 또는 SqlPackage에 사용 되는 DacFx (내보내기/가져오기)는 Azure AD 로그인에 대해 지원 되지 않습니다. Azure AD 서버 보안 주체 (로그인)가 일반 공급 되 면이 제한이 제거 됩니다.
    - SQL Server Management Studio에서 Azure AD 서버 보안 주체 (로그인) 사용:

      - 인증 된 로그인을 사용 하는 Azure AD 로그인 스크립팅은 지원 되지 않습니다.
      - IntelliSense는 외부 공급자의 CREATE LOGIN 문을 인식 하지 않으며 빨간색 밑줄이 표시 됩니다.

- 관리 되는 인스턴스 프로 비전 프로세스에서 생성 된 서버 수준 보안 주체 로그인, 또는 `securityadmin` `sysadmin`와 같은 서버 역할의 멤버 또는 서버 수준에서 ALTER ANY login 권한이 있는 다른 로그인만 Azure AD를 만들 수 있습니다. 관리 되는 인스턴스에 대 한 master 데이터베이스의 서버 보안 주체 (로그인)입니다.
- 로그인이 SQL 보안 주체가 면 `sysadmin` 역할의 일부인 로그인만 만들기 명령을 사용 하 여 Azure AD 계정에 대 한 로그인을 만들 수 있습니다.
- Azure AD 로그인은 Azure SQL Database 관리 되는 인스턴스에 사용 되는 것과 동일한 디렉터리 내에서 Azure AD의 멤버 여야 합니다.
- Azure AD 서버 보안 주체 (로그인)는 개체 탐색기 SQL Server Management Studio 18.0 preview 5부터 표시 됩니다.
- Azure AD 서버 보안 주체(로그인)를 Azure AD 관리자 계정과 겹칠 수 있습니다. Azure AD 서버 보안 주체 (로그인)는 주 서버를 확인 하 고 관리 되는 인스턴스에 사용 권한을 적용 하는 경우 Azure AD 관리자 보다 우선적으로 적용 됩니다.
- 인증 하는 동안 다음 시퀀스가 적용 되어 인증 주체를 확인 합니다.

    1. Azure ad 계정이 server_principals에 있는 Azure AD 서버 보안 주체 (로그인)에 직접 매핑되는 경우에는 Azure AD 서버 보안 주체 (로그인)의 액세스 권한 부여 및 권한 부여를 사용 합니다.
    2. Azure ad 계정이 azure ad 서버 보안 주체 (로그인)에 매핑되는 azure ad 그룹의 멤버 이면 (로그인) server_principals에 "X" 형식으로 액세스 권한을 부여 하 고 Azure AD 그룹 로그인의 사용 권한을 적용 합니다.
    3. Azure AD 계정이 관리 되는 인스턴스 시스템 뷰에 존재 하지 않는 관리 되는 인스턴스에 대해 특별 한 포털 구성 Azure AD 관리자 인 경우 관리 되는 인스턴스 (레거시 모드)에 대해 Azure AD 관리자의 특수 한 고정 권한을 적용 합니다.
    4. Azure ad 계정이 database_principals에 있는 데이터베이스의 Azure AD 사용자에 게 직접 매핑된 경우 Azure AD 데이터베이스 사용자의 액세스 권한을 부여 하 고 권한을 부여 합니다.
    5. Azure AD 계정이 데이터베이스에서 Azure AD 사용자에 매핑되는 Azure ad 그룹의 구성원 인 경우, database_principals에 "X" 형식으로 액세스 권한을 부여 하 고 Azure AD 그룹 로그인의 사용 권한을 적용 합니다.
    6. 인증 하는 사용자로 확인 되는 azure ad 사용자 계정 또는 azure ad 그룹 계정에 매핑된 Azure AD 로그인이 있는 경우이 Azure AD 로그인의 모든 사용 권한이 적용 됩니다.

### <a name="service-key-and-service-master-key"></a>서비스 키 및 서비스 마스터 키

- [마스터 키 백업은](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) 지원 되지 않습니다 (SQL Database 서비스에서 관리).
- [마스터 키 복원은](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) 지원 되지 않습니다 (SQL Database 서비스에서 관리).
- [서비스 마스터 키 백업은](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) 지원 되지 않습니다 (SQL Database 서비스에서 관리).
- [서비스 마스터 키 복원은](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) 지원 되지 않습니다 (SQL Database 서비스에서 관리).

## <a name="configuration"></a>Configuration

### <a name="buffer-pool-extension"></a>버퍼 풀 확장

- [버퍼 풀 확장](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) 은 지원 되지 않습니다.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`는 지원되지 않습니다. [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)을 참조하세요.

### <a name="collation"></a>데이터 정렬

기본 인스턴스 데이터 정렬은 `SQL_Latin1_General_CP1_CI_AS`이며 생성 매개 변수로 지정할 수 있습니다. [데이터 정렬](https://docs.microsoft.com/sql/t-sql/statements/collations)을 참조하세요.

### <a name="compatibility-levels"></a>호환성 수준

- 지원 되는 호환성 수준은 100, 110, 120, 130 및 140입니다.
- 100 미만의 호환성 수준은 지원되지 않습니다.
- 새 데이터베이스의 기본 호환성 수준은 140입니다. 복원 된 데이터베이스의 경우 100 이상인 경우 호환성 수준이 변경 되지 않은 상태로 유지 됩니다.

[ALTER DATABASE 호환성 수준](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요.

### <a name="database-mirroring"></a>데이터베이스 미러링

데이터베이스 미러링은 지원되지 않습니다.

- `ALTER DATABASE SET PARTNER` 및 `SET WITNESS` 옵션은 지원되지 않습니다.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`은 지원되지 않습니다.

자세한 내용은 [ALTER DATABASE SET PARTNER 및 SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 및 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)을 참조하세요.

### <a name="database-options"></a>데이터베이스 옵션

- 다중 로그 파일은 지원되지 않습니다.
- 메모리 내 개체는 범용 서비스 계층에서 지원되지 않습니다. 
- 서버당 최대 280 파일을 의미 하는 범용 인스턴스당 280 파일의 제한이 있습니다. 범용 계층의 데이터와 로그 파일은 모두이 제한에 계산 됩니다. [중요 비즈니스용 계층은 데이터베이스당 32767 파일을 지원](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)합니다.
- 데이터베이스는 filestream 데이터를 포함 하는 파일 그룹을 포함할 수 없습니다. .Bak에 데이터가 포함 되어 `FILESTREAM` 있으면 복원이 실패 합니다. 
- 모든 파일은 Azure Blob Storage에 배치됩니다. 파일별 IO 및 처리량은 각 개별 파일의 크기에 따라 달라집니다.

#### <a name="create-database-statement"></a>CREATE DATABASE 문

에 `CREATE DATABASE`적용 되는 제한 사항은 다음과 같습니다.

- 파일 및 파일 그룹을 정의할 수 없습니다. 
- 옵션 `CONTAINMENT` 은 지원 되지 않습니다. 
- `WITH`옵션은 지원 되지 않습니다. 
   > [!TIP]
   > 해결 방법으로 뒤 `ALTER DATABASE` `CREATE DATABASE` 에를 사용 하 여 파일을 추가 하거나 포함을 설정 하는 데이터베이스 옵션을 설정 합니다. 

- 옵션 `FOR ATTACH` 은 지원 되지 않습니다.
- 옵션 `AS SNAPSHOT OF` 은 지원 되지 않습니다.

자세한 내용은 [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)를 참조하세요.

#### <a name="alter-database-statement"></a>ALTER DATABASE 문

일부 파일 속성은 설정하거나 변경할 수 없습니다.

- `ALTER DATABASE ADD FILE (FILENAME='path')` T-sql 문에는 파일 경로를 지정할 수 없습니다. 관리되는 인스턴스에서 파일을 자동으로 저장하기 때문에 스크립트에서 `FILENAME`을 제거합니다. 
- `ALTER DATABASE` 문을 사용 하 여 파일 이름을 변경할 수 없습니다.

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

- SQL Server 에이전트 활성화 및 비활성화는 현재 관리 되는 인스턴스에서 지원 되지 않습니다. SQL 에이전트는 항상 실행되고 있습니다.
- SQL Server 에이전트 설정은 읽기 전용입니다. 프로시저 `sp_set_agent_properties` 는 관리 되는 인스턴스에서 지원 되지 않습니다. 
- 에서
  - T-SQL 작업 단계가 지원됩니다.
  - 다음 복제 작업이 지원됩니다.
    - 트랜잭션 로그 판독기
    - 스냅숏
    - 배포자
  - SSIS 작업 단계가 지원 됩니다.
  - 다른 유형의 작업 단계는 현재 지원 되지 않습니다.
    - 병합 복제 작업 단계가 지원 되지 않습니다. 
    - 큐 판독기는 지원되지 않습니다. 
    - 명령 셸은 아직 지원 되지 않습니다.
  - 관리 되는 인스턴스는 외부 리소스 (예: robocopy를 통한 네트워크 공유)에 액세스할 수 없습니다. 
  - SQL Server Analysis Services 지원 되지 않습니다.
- 알림은 부분적으로 지원됩니다.
- 데이터베이스 메일 프로필을 구성 해야 하지만 전자 메일 알림이 지원 됩니다. SQL Server 에이전트는 데이터베이스 메일 프로필을 하나만 사용할 수 있으며이 프로필은 호출 `AzureManagedInstance_dbmail_profile`되어야 합니다. 
  - 호출기는 지원되지 않습니다.
  - NetSend는 지원되지 않습니다.
  - 경고는 아직 지원 되지 않습니다.
  - 프록시가 지원 되지 않습니다.
- EventLog는 지원 되지 않습니다.

현재 지원 되지 않는 SQL 에이전트 기능은 다음과 같습니다.

- 프록시
- 유휴 CPU에서 작업 예약
- 에이전트 사용 또는 사용 안 함
- ,

SQL Server 에이전트에 대한 자세한 내용은 [SQL Server 에이전트](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)를 참조하세요.

### <a name="tables"></a>테이블

다음 테이블 형식은 지원 되지 않습니다.

- [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)
- [외부 테이블](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) Polybase
- [MEMORY_OPTIMIZED](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (범용 계층 에서만 지원 됨)

테이블을 만들고 변경 하는 방법에 대 한 자세한 내용은 [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 및 [alter TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)을 참조 하세요.

## <a name="functionalities"></a>기능

### <a name="bulk-insert--openrowset"></a>Bulk insert/OPENROWSET

관리 되는 인스턴스는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 Azure Blob storage에서 파일을 가져와야 합니다.

- `DATASOURCE`는 Azure Blob 저장소 `BULK INSERT` 에서 파일을 가져오는 동안 명령에 필요 합니다. [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)를 참조하세요.
- `DATASOURCE`는 Azure Blob storage `OPENROWSET` 에서 파일의 콘텐츠를 읽을 때 함수에 필요 합니다. [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.

### <a name="clr"></a>CLR

관리 되는 인스턴스는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음과 같은 제약 조건이 적용 됩니다.

- `CREATE ASSEMBLY FROM BINARY`만 지원됩니다. [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)를 참조하세요. 
- `CREATE ASSEMBLY FROM FILE`는 지원되지 않습니다. [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)을 참조하세요.
- `ALTER ASSEMBLY`는 파일을 참조할 수 없습니다. [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)를 참조하세요.

### <a name="dbcc"></a>DBCC

SQL Server에서 사용할 수 있는 문서화 되지 않은 DBCC 문은 관리 되는 인스턴스에서 지원 되지 않습니다.

- 제한 된 수의 전역 `Trace flags` 만 지원 됩니다. 세션 수준은 `Trace flags` 지원 되지 않습니다. [추적 플래그](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)를 참조 하세요.
- [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) 및 [dbcc TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) 는 제한 된 수의 전역 추적 플래그를 사용 하 여 작동 합니다.
- REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST 및 REPAIR_REBUILD 옵션을 사용한 [DBCC CHECKDB](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) 는 데이터베이스를 모드에서 `SINGLE_USER` 설정할 수 없기 때문에 사용할 수 없습니다. [ALTER database 차이점](#alter-database-statement)을 참조 하십시오. 잠재적 데이터베이스 손상은 Azure 지원 팀에서 처리 합니다. 수정 해야 하는 데이터베이스 손상을 발견 하는 경우 Azure 지원에 문의 하세요.

### <a name="distributed-transactions"></a>분산 트랜잭션

MSDTC 및 [탄력적 트랜잭션은](sql-database-elastic-transactions-overview.md) 현재 관리 되는 인스턴스에서 지원 되지 않습니다.

### <a name="extended-events"></a>확장 이벤트

확장 이벤트 (Xevent)에 대 한 일부 Windows 관련 대상은 지원 되지 않습니다.

- `etw_classic_sync` 대상이 지원 되지 않습니다. Azure `.xel` Blob 저장소에 파일을 저장 합니다. [etw_classic_sync 대상](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)을 참조하세요.
- `event_file` 대상이 지원 되지 않습니다. Azure `.xel` Blob 저장소에 파일을 저장 합니다. [event_file 대상](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)을 참조하세요.

### <a name="external-libraries"></a>외부 라이브러리

데이터베이스 내 R 및 Python에서 외부 라이브러리는 아직 지원 되지 않습니다. [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)를 참조하세요.

### <a name="filestream-and-filetable"></a>Filestream 및 FileTable

- Filestream 데이터는 지원 되지 않습니다.
- 데이터베이스에는 데이터가 있는 `FILESTREAM` 파일 그룹이 포함 될 수 없습니다.
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

관리되는 인스턴스의 연결된 서버는 제한된 수의 대상을 지원합니다.

- 지원 되는 대상은 관리 되는 인스턴스, 단일 데이터베이스 및 SQL Server 인스턴스입니다. 
- 연결 된 서버는 MS DTC (distributed 쓰기 가능 트랜잭션)를 지원 하지 않습니다.
- 지원 되지 않는 대상은 파일, Analysis Services 및 기타 RDBMS입니다. 파일 가져오기의 대 안으로 또는 `BULK INSERT` `OPENROWSET` 를 사용 하 여 Azure Blob Storage에서 네이티브 CSV 가져오기를 사용해 보세요.

작업

- 인스턴스 간 쓰기 트랜잭션은 지원되지 않습니다.
- `sp_dropserver`는 연결된 서버를 삭제하는 데 지원됩니다. [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)를 참조하세요.
- 함수 `OPENROWSET` 는 SQL Server 인스턴스에서만 쿼리를 실행 하는 데 사용할 수 있습니다. 관리, 온-프레미스 또는 가상 컴퓨터 중 하나를 사용할 수 있습니다. [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.
- 함수 `OPENDATASOURCE` 는 SQL Server 인스턴스에서만 쿼리를 실행 하는 데 사용할 수 있습니다. 관리, 온-프레미스 또는 가상 컴퓨터 중 하나를 사용할 수 있습니다. `SQLNCLI`, 및값`SQLOLEDB` 만 공급자로 지원 됩니다. `SQLNCLI11` 예제입니다. `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee` [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)를 참조하세요.
- 연결 된 서버를 사용 하 여 네트워크 공유에서 파일 (Excel, CSV)을 읽을 수 없습니다. Azure Blob Storage에서 CSV 파일을 읽는 [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 또는 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 을 사용 하십시오. [관리 되는 인스턴스 피드백 항목](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources) 에서이 요청 추적|

### <a name="polybase"></a>PolyBase

HDFS 또는 Azure Blob storage의 파일을 참조 하는 외부 테이블은 지원 되지 않습니다. PolyBase에 대 한 자세한 내용은 [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)를 참조 하세요.

### <a name="replication"></a>복제

- 스냅숏과 양방향 복제 유형이 지원 됩니다. 병합 복제, 피어 투 피어 복제 및 업데이트할 수 있는 구독은 지원 되지 않습니다.
- [트랜잭션 복제](sql-database-managed-instance-transactional-replication.md) 는 다음과 같은 몇 가지 제약 조건으로 관리 되는 인스턴스에서 공개 미리 보기에 사용할 수 있습니다.
    - 모든 유형의 복제 참여자 (게시자, 배포자, 끌어오기 구독자 및 밀어넣기 구독자)를 관리 되는 인스턴스에 배치할 수 있지만 게시자와 배포자를 서로 다른 인스턴스에 배치할 수는 없습니다.
    - 관리 되는 인스턴스는 최신 버전의 SQL Server와 통신할 수 있습니다. [여기](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)에서 지원 되는 버전을 참조 하세요.
    - 트랜잭션 복제에는 몇 가지 [추가 네트워킹 요구 사항이](sql-database-managed-instance-transactional-replication.md#requirements)있습니다.

복제를 구성 하는 방법에 대 한 자세한 내용은 [replication 자습서](replication-with-sql-database-managed-instance.md)를 참조 하십시오.


[장애 조치 (failover) 그룹](sql-database-auto-failover-group.md)의 데이터베이스에서 복제를 사용 하는 경우 관리 되는 인스턴스 관리자는 이전 주 데이터베이스의 모든 게시를 정리 하 고 장애 조치 (failover)가 발생 한 후 새 주 데이터베이스에서 다시 구성 해야 합니다 이 시나리오에는 다음과 같은 작업이 필요 합니다.

1. 데이터베이스에서 실행 중인 모든 복제 작업을 중지 합니다 (있는 경우).
2. 게시자 데이터베이스에서 다음 스크립트를 실행 하 여 게시자에서 구독 메타 데이터를 삭제 합니다.

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. 구독자에서 구독 메타 데이터를 삭제 합니다. 구독자 인스턴스의 구독 데이터베이스에서 다음 스크립트를 실행 합니다.

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. 게시 된 데이터베이스에서 다음 스크립트를 실행 하 여 게시자에서 모든 복제 개체를 강제로 삭제 합니다.

   ```sql
   EXEC sp_removedbreplication
   ```

1. 원래 주 인스턴스에서 이전 배포자를 강제로 삭제 합니다 (배포자를 사용 하는 이전 주 복제본으로 장애 복구 (failback) 하는 경우). 이전 배포자 관리 인스턴스의 master 데이터베이스에서 다음 스크립트를 실행 합니다.

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

### <a name="restore-statement"></a>RESTORE 문 

- 지원 되는 구문:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- 지원 되지 않는 구문:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- 출처: 
  - `FROM URL`(Azure Blob storage)는 유일 하 게 지원 되는 옵션입니다.
  - `FROM DISK`/`TAPE`/백업 디바이스는 지원되지 않습니다.
  - 백업 세트는 지원되지 않습니다.
- `WITH`옵션이 지원 되지 않습니다 (예: `DIFFERENTIAL` no `STATS`또는).
- `ASYNC RESTORE`: 클라이언트 연결이 끊어지는 경우에도 복원이 계속 됩니다. 연결을 삭제 한 경우에는 복원 작업의 `sys.dm_operation_status` 상태와 CREATE 및 DROP database의 뷰를 확인할 수 있습니다. 자세한 내용은 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)를 참조하세요. 

다음 데이터베이스 옵션은 설정 되거나 재정의 되며 나중에 변경할 수 없습니다. 

- `NEW_BROKER`브로커가 .bak 파일에서 사용 하도록 설정 되지 않은 경우입니다. 
- `ENABLE_BROKER`브로커가 .bak 파일에서 사용 하도록 설정 되지 않은 경우입니다. 
- `AUTO_CLOSE=OFF`.bak 파일의 데이터베이스에이 `AUTO_CLOSE=ON`있는 경우 
- `RECOVERY FULL`.bak 파일의 데이터베이스에 또는 `SIMPLE` `BULK_LOGGED` 복구 모드가 있는 경우
- 원본 .bak 파일에 없는 경우 메모리 최적화 파일 그룹이 추가 되 고 XTP 라고 합니다. 
- 모든 기존 메모리 최적화 파일 그룹은 XTP로 이름이 변경 됩니다. 
- `SINGLE_USER`및 `RESTRICTED_USER` 옵션은로 `MULTI_USER`변환 됩니다.

제한 사항: 

- 손상 된 데이터베이스의 백업은 손상 유형에 따라 복원 될 수 있지만 손상이 수정 될 때까지 자동화 된 백업은 사용 되지 않습니다. 원본 인스턴스에서를 실행 `DBCC CHECKDB` 하 고이 문제를 방지 하기 위해 백업을 `WITH CHECKSUM` 사용 해야 합니다.
- 이 문서 `.BAK` 에서 설명 하는 제한 ( `FILESTREAM` 예: 또는 `FILETABLE` 개체)을 포함 하는 데이터베이스 파일의 복원은 Managed Instance에서 복원할 수 없습니다.
- `.BAK`여러 백업 세트를 포함 하는 파일은 복원할 수 없습니다. 
- `.BAK`여러 로그 파일이 포함 된 파일은 복원할 수 없습니다.
- 8TB 보다 큰 데이터베이스, 활성 메모리 내 OLTP 개체 또는 280 개 이상의 파일을 포함 하는 백업은 범용 인스턴스에서 복원할 수 없습니다. 
- 4TB 보다 큰 데이터베이스가 포함 된 백업 또는 전체 크기가 [리소스 제한](sql-database-managed-instance-resource-limits.md) 에 설명 된 크기 보다 큰 메모리 내 OLTP 개체를 포함 하는 백업은 중요 비즈니스용 인스턴스에서 복원할 수 없습니다.
Restore 문에 대 한 자세한 내용은 [restore 문](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)을 참조 하십시오.

### <a name="service-broker"></a>Service Broker

인스턴스 간 서비스 broker는 지원되지 않습니다.

- `sys.routes`: 필수 구성 요소로 서, sys. 경로에서 주소를 선택 해야 합니다. 주소는 모든 경로에서 로컬 이어야 합니다. [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)를 참조하세요.
- `CREATE ROUTE`: 이외 `CREATE ROUTE` `ADDRESS` 에는를 사용할 수 없습니다. `LOCAL` [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)를 참조하세요.
- `ALTER ROUTE`: 이외 `ALTER ROUTE` `ADDRESS` 에는를 사용할 수 없습니다. `LOCAL` [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)를 참조하세요. 

### <a name="stored-procedures-functions-and-triggers"></a>저장 프로시저, 함수 및 트리거

- `NATIVE_COMPILATION`는 범용 계층에서 지원 되지 않습니다.
- 지원되지 않는 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 옵션은 다음과 같습니다. 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`는 지원되지 않습니다. [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)를 참조하세요.
- `xp_cmdshell`은 지원되지 않습니다. [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)을 참조하세요.
- `Extended stored procedures`는 지원 되지 않습니다. `sp_addextendedproc`여기 `sp_dropextendedproc`에는 및가 포함 됩니다  . [확장 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)를 참조 하세요.
- `sp_attach_db`, `sp_attach_single_file_db` 및 `sp_detach_db`는 지원되지 않습니다. [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 및 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)를 참조하세요.

### <a name="system-functions-and-variables"></a>시스템 함수 및 변수

다른 결과를 반환하는 변수, 함수 및 뷰는 다음과 같습니다.

- `SERVERPROPERTY('EngineEdition')`값 8을 반환 합니다. 이 속성은 관리되는 인스턴스를 고유하게 식별합니다. [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `SERVERPROPERTY('InstanceName')`SQL Server에 대 한 인스턴스 개념이 관리 되는 인스턴스에 적용 되지 않기 때문에 NULL을 반환 합니다. [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `@@SERVERNAME`전체 DNS "연결 가능" 이름 (예: my-managed-instance.wcus17662feb9ce98.database.windows.net)을 반환 합니다. [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)을 참조하세요. 
- `SYS.SERVERS`"name" 및 "data_source" 속성의 경우와 `myinstance.domain.database.windows.net` 같이 전체 DNS "연결 가능" 이름을 반환 합니다. [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)를 참조하세요.
- `@@SERVICENAME`SQL Server에 대해 존재 하는 서비스 개념이 관리 되는 인스턴스에 적용 되지 않으므로 NULL을 반환 합니다. [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)을 참조하세요.
- `SUSER_ID`가 지원됩니다. Azure AD 로그인이 sys. syslogins에 없으면 NULL을 반환 합니다. [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)를 참조하세요. 
- `SUSER_SID`는 지원되지 않습니다. 잘못 된 데이터가 반환 됩니다 .이는 알려진 문제입니다. [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)를 참조하세요. 

## <a name="Environment"></a>환경 제약 조건

### <a name="subnet"></a>Subnet
-  관리 되는 인스턴스를 배포한 서브넷에 다른 리소스 (예: 가상 컴퓨터)를 배치할 수 없습니다. 다른 서브넷을 사용 하 여 이러한 리소스를 배포 합니다.
- 서브넷에 사용 가능한 [IP 주소](sql-database-managed-instance-connectivity-architecture.md#network-requirements)수가 충분 해야 합니다. 최소는 16 이지만 권장 구성에는 서브넷의 IP 주소를 32 개 이상 포함 하는 것이 좋습니다.
- [서비스 끝점을 관리 되는 인스턴스의 서브넷과 연결할 수 없습니다](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 가상 네트워크를 만들 때 서비스 끝점 옵션이 사용 하지 않도록 설정 되어 있는지 확인 합니다.
- 지역에 배포할 수 있는 vCores 및 인스턴스 유형 수에는 몇 가지 [제약 조건 및 제한이](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)있습니다.
- [서브넷에 적용 해야 하는 몇 가지 보안 규칙이](sql-database-managed-instance-connectivity-architecture.md#network-requirements)있습니다.

### <a name="vnet"></a>VNET
- VNet은 리소스 모델을 사용 하 여 배포할 수 있습니다.-VNet에 대 한 클래식 모델은 지원 되지 않습니다.
- 관리 되는 인스턴스를 만든 후에는 관리 되는 인스턴스 또는 VNet을 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.
- App Service 환경, 논리 앱, 관리 되는 인스턴스 (예: 지역에서 복제, 트랜잭션 복제 또는 연결 된 서버를 통해)와 같은 일부 서비스의 경우 Vnet가 global을 사용 하 여 연결 된 경우 다른 지역의 관리 되는 인스턴스에 액세스할 수 없습니다. [ 피어 링](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). VNet 게이트웨이를 통해 Express 경로 또는 VNet 간을 통해 이러한 리소스에 연결할 수 있습니다.

### <a name="tempdb"></a>TEMPDB

범용 계층의 최대 파일 `tempdb` 크기는 코어 당 24gb 보다 클 수 없습니다. 중요 비즈니스용 계층 `tempdb` 의 최대 크기는 인스턴스 저장소 크기에 의해 제한 됩니다. `Tempdb`로그 파일 크기는 일반적인 용도와 중요 비즈니스용 계층 모두 120 GB로 제한 됩니다. 일부 쿼리는에서 `tempdb` 코어 당 24gb 이상을 필요로 하거나 120 이상의 로그 데이터를 생성 하는 경우 오류를 반환할 수 있습니다.

### <a name="error-logs"></a>오류 로그

관리 되는 인스턴스는 자세한 정보를 오류 로그에 배치 합니다. 오류 로그에 기록 되는 내부 시스템 이벤트가 많이 있습니다. 사용자 지정 프로시저를 사용 하 여 일부 관련이 없는 항목을 필터링 하는 오류 로그를 읽을 수 있습니다. 자세한 내용은 [관리 되는 인스턴스 – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)를 참조 하세요.

## <a name="Issues"></a>알려진 문제

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>서비스 계층 업그레이드 후 데이터베이스 간 Service Broker 대화 상자를 다시 초기화 해야 함

**날** 8 월 2019

데이터베이스 간 Service Broker 대화 상자는 서비스 계층 변경 작업 후에 다른 데이터베이스의 서비스에 메시지를 배달 하지 않습니다. 메시지는 **손실 되지** 않으며 발신자 큐에서 찾을 수 있습니다. Managed Instance에서 vcores 또는 인스턴스 저장소 크기를 변경 하면 모든 데이터베이스에 `service_broke_guid` 대해 [sys. 데이터베이스](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) 보기의 값이 변경 됩니다. 다른 `DIALOG` 데이터베이스의 Service broker를 참조 하는 [시작 대화 상자](https://docs.microsoft.com/en-us/sql/t-sql/statements/begin-dialog-conversation-transact-sql) 를 사용 하 여 만든 모든는 메시지 메시지를 대상 서비스로 배달 하는 것을 중지 합니다.

**해결 방법:** 서비스 계층을 업데이트 하기 전에 데이터베이스 간 Service Broker 대화 상자를 사용 하는 작업을 중지 하 고 이후에 다시 초기화 합니다. 서비스 계층 변경 후 배달 되지 않은 메시지가 남아 있으면 원본 큐에서 메시지를 읽고 대상 큐에 다시 보냅니다.

### <a name="impresonification-of-aad-login-types-is-not-supported"></a>AAD 로그인 형식의 Impresonification은 지원 되지 않습니다.

**날** 7 월 2019

다음 AAD `EXECUTE AS USER` 보안 `EXECUTE AS LOGIN` 주체를 사용 하는 가장이 지원 되지 않습니다.
-   별칭이 지정 되는 AAD 사용자입니다. 이 경우 `15517`다음과 같은 오류가 반환 됩니다.
- Aad 응용 프로그램 또는 서비스 주체를 기반으로 하는 AAD 로그인 및 사용자입니다. 이 경우 `15517` 및 `15406`에는 다음과 같은 오류가 반환 됩니다.

### <a name="database-email"></a>데이터베이스 전자 메일 

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@querysp_send_db_mail에서 지원 되지 않는 매개 변수

**날** 2019년 4월

[Sp_send_db_mail 프로시저](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 의 매개변수가작동하지`@query` 않습니다.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>지역 장애 조치 (failover) 후 트랜잭션 복제를 다시 구성 해야 합니다.

**날** 3 월 2019

자동 장애 조치 그룹의 데이터베이스에서 트랜잭션 복제를 사용 하는 경우 관리 되는 인스턴스 관리자는 이전 주 데이터베이스의 모든 게시를 정리 하 고 다른 지역에 대 한 장애 조치 (failover)가 발생 한 후 새 주 데이터베이스에서 다시 구성 해야 합니다. 자세한 내용은 [복제](#replication) 를 참조 하세요.

### <a name="aad-logins-and-users-are-not-supported-in-tools"></a>AAD 로그인 및 사용자가 도구에서 지원 되지 않음

**날** 1 월 2019

SQL Server Management Studio 및 SQL Server Data Tools는 Azure Acctive directory 로그인 및 사용자를 지원 하지 fuly.
- 현재 SQL Server Data Tools에서 Azure AD 서버 보안 주체 (로그인) 및 사용자 (공개 미리 보기)를 사용 하는 것은 지원 되지 않습니다.
- Azure AD 서버 보안 주체 (로그인) 및 사용자 (공개 미리 보기)에 대 한 스크립팅은 SQL Server Management Studio에서 지원 되지 않습니다.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB 구조와 콘텐츠를 다시 만들었습니다.

데이터베이스 `tempdb` 는 항상 12 개의 데이터 파일로 분할 되며 파일 구조를 변경할 수 없습니다. 파일당 최대 크기를 변경할 수 없으며 새 파일을에 `tempdb`추가할 수 없습니다. `Tempdb`는 인스턴스가 시작 되거나 장애 조치 (failover) 될 때 항상 빈 데이터베이스로 다시 생성 되며에서 `tempdb` 변경한 내용은 유지 되지 않습니다.

### <a name="exceeding-storage-space-with-small-database-files"></a>작은 데이터베이스 파일이 포함된 스토리지 공간 초과

`CREATE DATABASE`인스턴스가 `ALTER DATABASE ADD FILE`Azure Storage 제한 `RESTORE DATABASE` 에 도달할 수 있으므로, 및 문이 실패할 수 있습니다.

범용으로 관리 되는 각 인스턴스에는 Azure Premium 디스크 공간 용으로 예약 된 저장소 최대 35 TB가 있습니다. 각 데이터베이스 파일은 별도의 실제 디스크에 배치 됩니다. 디스크 크기는 128GB, 256GB, 512GB, 1TB 또는 4TB일 수 있습니다. 디스크의 사용 되지 않는 공간은 청구 되지 않지만 Azure Premium 디스크 크기의 총 합계는 35 TB를 초과할 수 없습니다. 경우에 따라 총 8TB가 필요 없는 관리 되는 인스턴스는 내부 조각화로 인해 저장소 크기에 대 한 35 TB Azure 제한을 초과할 수 있습니다.

예를 들어 범용 관리 되는 인스턴스의 크기가 4tb 디스크에 배치 되는 1.2 TB의 빅 파일이 있을 수 있습니다. 또한 각각 별도의 128 GB 디스크에 배치 되는 248 파일 1GB 파일이 있을 수 있습니다. 이 예제에서:

- 전체 할당된 디스크 스토리지 크기는 1x4TB + 248x128GB = 35TB입니다.
- 인스턴스에서 데이터베이스에 대해 예약된 총 공간은 1x1.2TB + 248x1GB = 1.4TB입니다.

이 예에서는 특정 상황에서 파일의 특정 배포로 인해 관리 되는 인스턴스가 연결 된 Azure Premium 디스크에 대해 예약 된 35-TB 제한에 도달할 수 있음을 보여 줍니다.

이 예제에서 기존 데이터베이스는 계속 작동 하며 새 파일이 추가 되지 않는 한 문제 없이 커질 수 있습니다. 모든 데이터베이스의 총 크기가 인스턴스 크기 제한에 도달 하지 않더라도 새 디스크 드라이브에 충분 한 공간이 없기 때문에 새 데이터베이스를 만들거나 복원할 수 없습니다. 이 경우 반환 되는 오류는 명확 하지 않습니다.

시스템 뷰를 사용 하 여 [남은 파일 수를 식별할](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) 수 있습니다. 이 한도에 도달 하면 [DBCC SHRINKFILE 문을 사용 하 여 작은 파일 중 일부를 비우고 삭제](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) 하거나 [이 제한이 없는 중요 비즈니스용 계층](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)으로 전환 해 보세요.

### <a name="guid-values-shown-instead-of-database-names"></a>데이터베이스 이름 대신 표시 되는 GUID 값

몇 가지 시스템 뷰, 성능 카운터, 오류 메시지, XEvent 및 오류 로그 항목에는 실제 데이터베이스 이름 대신 GUID 데이터베이스 식별자가 표시됩니다. 이러한 GUID 식별자는 나중에 실제 데이터베이스 이름으로 대체 되기 때문에 사용 하지 마세요.

### <a name="error-logs-arent-persisted"></a>오류 로그는 지속 되지 않습니다.

관리 되는 인스턴스에서 사용할 수 있는 오류 로그는 지속 되지 않으며 크기는 최대 저장소 제한에 포함 되지 않습니다. 장애 조치 (failover)가 발생 하면 오류 로그가 자동으로 삭제 될 수 있습니다. 여러 가상 컴퓨터에서 Managed Instance 여러 번 이동 되었으므로 오류 로그 기록에 차이가 있을 수 있습니다.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>동일한 인스턴스 내에 있는 두 데이터베이스의 트랜잭션 범위는 지원 되지 않습니다.

동일한 인스턴스 내에서 동일한 트랜잭션 범위에서 두 개의 쿼리를 두 개의 데이터베이스로 보내면 .net의 클래스가작동하지않습니다.`TransactionScope`

```csharp
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

이 코드는 동일한 인스턴스 내에서 데이터를 사용할 수 있지만 MSDTC가 필요 합니다.

**해결 방법:** 두 개의 연결을 사용 하는 대신 [SqlConnection 데이터베이스 (String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) 를 사용 하 여 연결 컨텍스트에서 다른 데이터베이스를 사용 합니다.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 모듈 및 연결 된 서버는 때때로 로컬 IP 주소를 참조할 수 없습니다.

관리 되는 인스턴스 및 연결 된 서버 또는 현재 인스턴스를 참조 하는 분산 쿼리에 배치 된 CLR 모듈은 종종 로컬 인스턴스의 IP를 확인할 수 없습니다. 이 오류는 일시적인 문제입니다.

**해결 방법:** 가능 하면 CLR 모듈에서 컨텍스트 연결을 사용 합니다.

## <a name="next-steps"></a>다음 단계

- 관리 되는 인스턴스에 대 한 자세한 내용은 [관리 되는 인스턴스 정의](sql-database-managed-instance.md) 를 참조 하세요.
- 기능 및 비교 목록은 [Azure SQL Database 기능 비교](sql-database-features.md)를 참조 하세요.
- 새 관리 되는 인스턴스를 만드는 방법을 보여 주는 빠른 시작은 [관리 되는 인스턴스 만들기](sql-database-managed-instance-get-started.md)를 참조 하세요.
