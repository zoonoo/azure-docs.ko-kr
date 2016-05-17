<properties
	pageTitle="활성 지역 복제를 위한 보안 구성"
	description="이 항목에서는 SQL 데이터베이스에 대한 활성 지역 복제 시나리오를 관리하기 위한 보안 고려 사항을 설명합니다."
	services="sql-database"
	documentationCenter="na"
	authors="carlrabeler"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="04/27/2016"
	ms.author="carlrab" />

# 지역에서 복제를 위한 보안 구성

>[AZURE.NOTE] [Active Geo-Replication](sql-database-geo-replication-overview.md)은 현재 모든 서비스 계층의 모든 데이터베이스에 대해 제공됩니다.

## 활성 지역 복제에 대한 인증 요구 사항 개요
이 항목은 [활성 지역 복제](sql-database-geo-replication-overview.md) 구성 및 제어에 대한 인증 요구 사항 및 보조 데이터베이스에 대한 사용자 액세스 설정이 필요한 단계를 설명합니다. 지역 복제 사용에 대한 자세한 내용은 [중단 상태에서 Azure SQL 데이터베이스 복구](sql-database-disaster-recovery.md)를 참조하세요.

## 포함된 사용자에 활성 지역 복제 사용
[Azure SQL 데이터베이스의 V12 버전](sql-database-v12-whats-new.md)과 함께 SQL 데이터베이스는 이제 포함된 사용자를 지원합니다. master 데이터베이스에서 로그인에 매핑되어야 하는 기존 사용자와 달리 포함된 사용자는 데이터베이스 자체에서 완전히 관리됩니다. 두 가지 이점이 있습니다. 지역 복제 시나리오에서 데이터베이스는 사용자를 관리하므로 사용자는 추가 구성 없이 보조 데이터베이스에 계속해서 연결할 수 있습니다. 로그인 관점에서 이 구성에는 잠재적인 확장성 및 성능 이점이 있습니다. 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요.

동일한 로그인을 사용하는 여러 데이터베이스가 있는 경우 여러 데이터베이스에 포함된 사용자를 사용하여 자격 증명을 유지 관리하면 포함된 사용자의 이점이 없어질 수 있습니다. 예를 들어 암호가 변경될 경우 서버 수준에서 로그인 대한 암호를 한 번 변경하는 대신, 각 데이터베이스에 포함된 사용자에 대해 변경 내용이 별도로 적용되어야 합니다. 이러한 이유로 동일한 사용자 이름 및 암호를 사용하는 여러 데이터베이스가 있는 경우 포함된 사용자를 사용하는 것은 좋지 않습니다.

## 활성 지역 복제에 로그인 및 사용자 사용
로그인 및 사용자를 사용하는 경우(포함된 사용자 대신) 보조 데이터베이스 서버에 동일한 로그인이 존재하는지 보장하기 위해 추가 단계를 수행해야 합니다. 다음 섹션에서는 관련된 단계 및 추가 고려 사항을 간략히 설명합니다.

### 보조 데이터베이스에 대한 사용자 액세스 설정
보조 데이터베이스를 읽기 전용 보조 데이터베이스 또는 장애 조치 후 실행 가능한 주 데이터베이스로 사용할 수 있으려면 보조 데이터베이스에 적절한 보안 구성이 있어야 합니다.

서버 관리자 또는 적절한 권한이 있는 사용자가 항목에 설명된 구성 단계를 완료할 수 있습니다. 각 단계에 대한 특정 사용 권한은 이 항목의 뒷 부분에서 설명됩니다.

활성 지역 복제 온라인 보조에 대한 사용자 액세스 준비는 언제든지 수행될 수 있습니다. 아래에서 설명하는 세 단계가 포함됩니다.

1. 주 데이터베이스에 대한 액세스를 사용하여 로그인을 확인합니다.
2. 원본 서버에서 해당 로그인에 대한 SID를 찾습니다.
3. 원본 서버에서 일치하는 SID를 가진 대상 서버에서 로그인을 만듭니다.

>[AZURE.NOTE] 대상 서버의 로그인이 보조 데이터베이스에 있는 사용자로 적절히 매핑되지 않으면 읽기 전용 데이터베이스로 액세스 또는 장애 조치 후 새 주 데이터베이스로 액세스가 서버 관리자로만 제한됩니다.

#### 1\. 주 데이터베이스에 대한 액세스를 사용하여 로그인을 확인합니다.
프로세스의 첫 번째 단계는 대상 서버에 중복되어야 하는 로그인을 결정하는 것입니다. 이는 원본 서버의 논리적 master 데이터베이스에 있는 하나와 주 데이터베이스 자체에 있는 하나로 이루어진 SELECT 문 쌍으로 수행됩니다.

서버 관리자 또는 **LoginManager** 서버 역할의 멤버만 다음 SELECT 문을 사용하여 원본 서버에서 로그인을 결정할 수 있습니다.

	SELECT [name], [sid] 
	FROM [sys].[sql_logins] 
	WHERE [type_desc] = 'SQL_Login'

db\_owner 데이터베이스 역할의 멤버, dbo 사용자 또는 서버 관리자만 주 데이터베이스에서 모든 데이터베이스 사용자 계정을 결정할 수 있습니다.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

#### 2\. 1단계에서 확인된 로그인에 대한 SID를 찾습니다.
이전 섹션에서 쿼리의 출력을 비교하고 SID와 일치하여 서버 로그인을 데이터베이스 사용자에 매핑할 수 있습니다. 일치하는 SID가 있는 데이터베이스 사용자를 가진 로그인은 해당 데이터베이스 사용자 계정으로 해당 데이터베이스에 대한 사용자 액세스를 가집니다.

다음 쿼리를 사용하여 데이터베이스에서 모든 사용자 계정 및 해당 SID를 볼 수 있습니다. db\_owner 데이터베이스 역할의 멤버 또는 서버 관리자만 이 쿼리를 실행할 수 있습니다.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

>[AZURE.NOTE] **INFORMATION\_SCHEMA** 및 **sys** 사용자는 *NULL* SID가 있으며 **게스트** SID는 **0x00**입니다. 데이터베이스 작성자가 **DbManager**의 멤버가 아닌 서버 관리자인 경우 **dbo** SID는 *0x01060000000001648000000000048454*로 시작할 수 있습니다.

#### 3\. 대상 서버의 로그인을 만듭니다.
마지막 단계는 대상 서버 또는 서버로 이동하고 적절한 SID를 사용하여 로그인을 생성하는 것입니다. 기본 구문은 다음과 같습니다.

	CREATE LOGIN [<login name>]
	WITH PASSWORD = <login password>,
	SID = <desired login SID>

>[AZURE.NOTE] 주 데이터베이스가 아닌 보조 데이터베이스에 사용자 액세스 권한을 부여하려는 경우 주 서버의 사용자 로그인을 변경하고 다음 구문을 사용하여 수행할 수 있습니다.
>
>ALTER LOGIN <login name> DISABLE
>
>DISABLE은 암호를 변경하지 않으므로 필요한 경우 항상 사용하도록 설정할 수 있습니다.

## 다음 단계
활성 지역 복제에 대한 자세한 내용은 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.


## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [복구된 Azure SQL 데이터베이스 마무리](sql-database-recovered-finalize.md)
- [SQL 데이터베이스 BCDR FAQ](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0504_2016-->