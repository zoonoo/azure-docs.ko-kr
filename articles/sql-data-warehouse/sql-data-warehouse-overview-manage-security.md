<properties
   pageTitle="SQL 데이터 웨어하우스에서 데이터베이스 보호 | Microsoft Azure"
   description="솔루션 개발을 위해 Azure SQL 데이터 웨어하우스에서 데이터베이스를 보호하는 팁"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/22/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# SQL 데이터 웨어하우스에서 데이터베이스 보호

> [AZURE.SELECTOR]
- [보안 개요](sql-data-warehouse-overview-manage-security.md)
- [위협 감지](sql-data-warehouse-security-threat-detection.md)
- [암호화(포털)](sql-data-warehouse-encryption-tde.md)
- [암호화(T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [감사 개요](sql-data-warehouse-auditing-overview.md)
- [하위 수준 클라이언트 감사](sql-data-warehouse-auditing-downlevel-clients.md)



이 문서는 Azure SQL 데이터 웨어하우스 데이터베이스 보호에 대한 기본 사항을 안내합니다. 특히, 이 문서는 데이터베이스에서 액세스 제한, 데이터 보호 및 작업 모니터링을 위한 리소스로 시작할 수 있습니다.

## 연결 보안

연결 보안은 방화벽 규칙 및 연결 암호화를 사용하여 데이터베이스에 대 한 연결을 제한하고 보호하는 방법을 가리킵니다.

방화벽 규칙은 서버와 데이터베이스에서 명시적으로 허용 목록에 없는 IP 주소로부터의 연결 시도를 거부하는 데 사용됩니다. 응용 프로그램 또는 클라이언트 컴퓨터의 공용 IP 주소에서 연결할 수 있도록 허용하려면 먼저 Azure 클래식 포털, REST API 또는 PowerShell을 사용하여 서버 수준 방화벽 규칙을 만들어야 합니다. 서버 방화벽을 통해 허용되는 IP 주소 범위를 최대한 많이 제한하는 것이 좋습니다. 로컬 컴퓨터에서 Azure SQL 데이터 웨어하우스로 액세스하려면 네트워크의 방화벽과 로컬 컴퓨터가 TCP 포트 1433으로 나가는 통신을 허용하는지 확인합니다. 자세한 내용은 [Azure SQL 데이터베이스 방화벽][], [sp\_set\_firewall\_rule][] 및 [sp\_set\_database\_firewall\_rule][]을 참조하세요.

연결 문자열에서 암호화 모드를 설정하여 SQL 데이터 웨어하우스에 대한 연결을 암호화할 수 있습니다. 연결에 암호화를 설정하는 구문은 프로토콜에 따라 다릅니다. 연결 문자열 설정 방법을 알아보려면 Azure 포털에서 사용자 데이터베이스로 이동합니다. *필수*에서 *데이터베이스 연결 문자열 표시*를 클릭합니다.


## 인증

인증은 데이터베이스에 연결할 때 사용자의 ID를 증명하는 방법을 가리킵니다. SQL 데이터 웨어하우스는 현재 Azure Active Directory의 미리 보기뿐만 아니라 사용자 이름 및 암호를 사용하는 SQL Server 인증을 지원합니다.

데이터베이스의 논리 서버를 만들 때 사용자 이름 및 암호를 사용하여 "서버 관리자" 로그인을 지정했습니다. 이러한 자격 증명을 사용하면, SQL Server 인증을 통해 해당 서버의 모든 데이터베이스에 데이터베이스 소유자 또는 "dbo"로 인증할 수 있습니다.

그러나 조직의 사용자는 다른 계정으로 인증하는 것이 좋습니다. 이렇게 하면 응용 프로그램에 부여되는 사용 권한을 제한하여 응용 프로그램 코드가 SQL 삽입 공격에 취약한 경우 악의적인 활동의 위험을 줄일 수 있습니다.

SQL Server 인증 사용자를 만들려면 서버 관리자 로그인을 사용하여 서버의 **master** 데이터베이스에 연결하고 새 서버 로그인을 만듭니다.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'strong_password';

```

그런 다음, 서버 관리자 로그인을 사용하여 **SQL 데이터 웨어하우스 데이터베이스**에 연결하고 방금 만든 서버 로그인에 따라 데이터베이스 사용자를 만듭니다.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;

```

SQL 데이터베이스 인증에 대한 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리][]를 참조하세요. SQL 데이터 웨어하우스에 Azure AD 미리 보기를 사용하는 것에 대한 자세한 내용은 [Azure Active Directory 인증을 사용하여 SQL 데이터 웨어하우스에 연결][]을 참조하세요.


## 권한 부여

권한 부여는 Azure SQL 데이터 웨어하우스 데이터베이스 내에서 수행할 수 있는 작업을 가리키며 사용자 계정의 역할 멤버 자격과 사용 권한에 의해 제어됩니다. 사용자에게 필요한 최소한의 권한을 부여하는 것이 가장 좋습니다. Azure SQL 데이터 웨어하우스를 사용하면 T-SQL에서 역할을 통해 쉽게 관리할 수 있습니다.

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

연결 중인 서버 관리자 계정은 데이터베이스 내에서 작업을 수행할 권한이 있는 db\_owner의 구성원입니다. 스키마 업그레이드 및 기타 관리 작업을 배포하기 위해서는 이 계정을 저장합니다. 응용 프로그램에서 해당 응용 프로그램에 필요한 최소한의 권한이 있는 데이터베이스에 연결하려면 보다 제한된 사용 권한을 가진 "ApplicationUser" 계정을 사용합니다.

사용자가 Azure SQL 데이터베이스로 수행할 수 있는 작업을 더욱 제한할 수 있는 방법이 있습니다.

- 세분화된 [권한][]을 사용하면 개별 열, 테이블, 뷰, 프로시저 및 데이터베이스의 다른 개체에서 수행할 수 있는 작업을 제어할 수 있습니다. 세분화된 사용 권한을 사용하여 최대한으로 제어하고 최소한의 필요 권한을 부여합니다. 세분화된 사용 권한 시스템은 다소 복잡하며 효과적으로 사용하려면 약간의 연구가 필요합니다.
- db\_datareader 및 db\_datawriter 이외의 [데이터베이스 역할][]은 더 강력한 응용 프로그램 사용자 계정이나 덜 강력한 관리 계정을 만드는 데 사용할 수 있습니다. 기본 제공되는 고정 데이터베이스 역할은 사용 권한을 부여하는 쉬운 방법을 제공하지만 필요한 것보다 많은 사용 권한이 부여될 수 있습니다.
- [저장 프로시저][]는 데이터베이스에서 수행할 수 있는 작업을 제한하는 데 사용할 수 있습니다.

Azure 클래식 포털에서 또는 Azure 리소스 관리자 API를 사용하여 데이터베이스 및 논리 서버를 관리하는 것은 포털 사용자 계정의 역할 할당에 의해 제어됩니다. 이 항목에 대한 자세한 내용은 [Azure 포털의 역할 기반 액세스 제어][]를 참조하세요.

## 암호화

Azure SQL 데이터 웨어하우스는 데이터가 "휴지 상태"일 때 또는 데이터베이스 파일 및 백업에 저장된 경우 [투명한 데이터 암호화][]를 통해 데이터를 암호화하여 데이터를 보호할 수 있도록 도와줍니다. TDE를 사용하려면 master 데이터베이스에서 관리자 또는 dbmanager 역할의 구성원이어야 합니다. 데이터베이스를 암호화하려면 서버에서 master 데이터베이스에 연결하고 다음을 실행합니다.


```sql

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;

```

[Azure 포털][]의 데이터베이스 설정에서 투명한 데이터 암호화를 사용하도록 설정할 수도 있습니다. 자세한 내용은 [TDE(투명한 데이터 암호화) 시작][]을 참조하세요.

## 감사

데이터베이스 이벤트의 감사 및 추적은 규정을 준수하고 의심스러운 활동을 식별할 수 있도록 도와줍니다. SQL 데이터 웨어하우스 감사를 사용하면 Azure 저장소 계정의 감사 로그에 데이터베이스의 이벤트를 기록할 수 있습니다. 또한 드릴다운 보고서 및 분석을 용이하게 하려면 SQL 데이터 웨어하우스 감사 기능을 Microsoft Power BI와 통합합니다. 자세한 내용은 [SQL 데이터베이스 감사 시작][]을 참조하세요.

## 다음 단계
다양한 프로토콜을 사용하여 SQL 데이터 웨어하우스에 연결하는 방법에 대한 정보와 예제는 [SQL 데이터 웨어하우스에 연결][]을 참조하세요.

<!--Image references-->

<!--Article references-->
[SQL 데이터 웨어하우스에 연결]: ./sql-data-warehouse-connect-overview.md
[SQL 데이터베이스 감사 시작]: ./sql-data-warehouse-auditing-overview.md
[TDE(투명한 데이터 암호화) 시작]: ./sql-data-warehouse-encryption-tde.md
[Azure Active Directory 인증을 사용하여 SQL 데이터 웨어하우스에 연결]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL 데이터베이스 방화벽]: https://msdn.microsoft.com/library/ee621782.aspx
[sp\_set\_firewall\_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp\_set\_database\_firewall\_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[데이터베이스 역할]: https://msdn.microsoft.com/library/ms189121.aspx
[Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리]: https://msdn.microsoft.com/library/ee336235.aspx
[권한]: https://msdn.microsoft.com/library/ms191291.aspx
[저장 프로시저]: https://msdn.microsoft.com/library/ms190782.aspx
[투명한 데이터 암호화]: https://go.microsoft.com/fwlink/?LinkId=526242
[Azure 포털]: https://portal.azure.com/

<!--Other Web references-->
[Azure 포털의 역할 기반 액세스 제어]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure

<!---HONumber=AcomDC_0817_2016-->