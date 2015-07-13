<properties
   pageTitle="SQL 데이터 웨어하우스에서 데이터베이스 보호 | Microsoft Azure"
   description="솔루션 개발을 위해 Azure SQL 데이터 웨어하우스에서 데이터베이스를 보호하는 팁"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# SQL 데이터 웨어하우스에서 데이터베이스 보호

이 문서는 Azure SQL 데이터 웨어하우스 데이터베이스 보호에 대한 기본 사항을 안내합니다. 특히, 이 문서는 데이터베이스에서 액세스 제한, 데이터 보호 및 작업 모니터링을 위한 리소스로 시작할 수 있습니다.

## 연결 보안

연결 보안은 방화벽 규칙 및 연결 암호화를 사용하여 데이터베이스에 대 한 연결을 제한하고 보호하는 방법을 가리킵니다.

방화벽 규칙은 서버와 데이터베이스에서 명시적으로 허용 목록에 없는 IP 주소로부터의 연결 시도를 거부하는 데 사용됩니다. 응용 프로그램 또는 클라이언트 컴퓨터의 공용 IP 주소에서 새 데이터베이스에 대해 연결을 시도할 수 있도록 허용하려면 먼저 Azure 관리 포털, REST API 또는 PowerShell을 사용하여 서버 수준 방화벽 규칙을 만들어야 합니다. 서버 방화벽을 통해 허용되는 IP 주소 범위를 최대한 많이 제한하는 것이 좋습니다. 자세한 내용은 [Azure SQL 데이터베이스 방화벽][]을 참조하세요.


## 인증

인증은 데이터베이스에 연결할 때 사용자의 ID를 증명하는 방법을 가리킵니다. SQL 데이터 웨어하우스는 현재 사용자 이름 및 암호를 사용한 SQL 인증을 지원합니다.

데이터베이스의 논리 서버를 만들 때 사용자 이름 및 암호를 사용하여 "서버 관리자" 로그인을 지정했습니다. 이러한 자격 증명을 사용하면 해당 서버의 모든 데이터베이스에 데이터베이스 소유자 또는 "dbo"로 인증할 수 있습니다.

그러나 가장 좋은 방법은 조직의 사용자들이 다른 계정을 사용하여 인증하는 것입니다. 이 방법을 사용하면 응용 프로그램에 부여되는 사용 권한을 제한하여 응용 프로그램 코드가 SQL 삽입 공격에 취약한 경우 악의적인 활동의 위험을 줄일 수 있습니다. 응용 프로그램을 통해 사용자 이름과 암호로 단일 데이터베이스에 직접 인증할 수 있는 포함된 데이터베이스 사용자를 만드는 것이 좋습니다. 서버 관리자 로그인으로 사용자 데이터베이스에 연결되어 있는 동안 다음 T-SQL을 실행하여 포함된 데이터베이스 사용자를 만들 수 있습니다.

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password';
```

SQL 데이터베이스 인증에 대한 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리][]를 참조하세요.


## 권한 부여

권한 부여는 Azure SQL 데이터 웨어하우스 데이터베이스 내에서 수행할 수 있는 작업을 가리키며 사용자 계정의 역할 멤버 자격과 사용 권한에 의해 제어됩니다. 사용자에게 필요한 최소한의 권한을 부여하는 것이 가장 좋습니다. Azure SQL 데이터 웨어하우스를 사용하면 T-SQL에서 역할을 통해 쉽게 관리할 수 있습니다.

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

연결 중인 서버 관리자 계정은 데이터베이스 내에서 작업을 수행할 권한이 있는 db_owner의 구성원입니다. 스키마 업그레이드 및 기타 관리 작업을 배포하기 위해서는 이 계정을 저장합니다. 응용 프로그램에서 해당 응용 프로그램에 필요한 최소한의 권한이 있는 데이터베이스에 연결하려면 보다 제한된 사용 권한을 가진 "ApplicationUser" 계정을 사용합니다.

사용자가 Azure SQL 데이터베이스로 수행할 수 있는 작업을 추가로 제한하는 방법이 있습니다.- db_datareader 및 db_datawriter 이외에 [데이터베이스 역할][]을 사용하여 더 강력한 응용 프로그램 사용자 계정 또는 덜 강력한 관리 계정을 만들 수 있습니다. - 세분화된 [사용 권한][]을 통해 개별 열, 테이블, 뷰, 프로시저 및 데이터베이스 내 다른 개체에 대해 수행할 수 있는 작업을 제어할 수 있습니다. - [저장 프로시저][]를 사용하여 데이터베이스에서 수행할 수 있는 작업을 제한할 수 있습니다.

Azure 관리 포털에서 또는 Azure 리소스 관리자 API를 사용하여 데이터베이스 및 논리 서버를 관리하는 것은 포털 사용자 계정의 역할 할당에 의해 제어됩니다. 이 항목에 대한 자세한 내용은 [Azure Preview 포털의 역할 기반 액세스 제어][]를 참조하세요.


## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[개발 개요]: sql-data-warehouse-overview-develop.md


<!--MSDN references-->
[Azure SQL 데이터베이스 방화벽]: https://msdn.microsoft.com/library/ee621782.aspx
[데이터베이스 역할]: https://msdn.microsoft.com/library/ms189121.aspx
[Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리]: https://msdn.microsoft.com/library/ee336235.aspx
[사용 권한]: https://msdn.microsoft.com/library/ms191291.aspx
[저장 프로시저]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: http://go.microsoft.com/fwlink/?linkid=526242&clcid=0x409

<!--Other Web references-->
[Azure Preview 포털의 역할 기반 액세스 제어]: http://azure.microsoft.com/documentation/articles/role-based-access-control-configure.aspx

<!---HONumber=July15_HO1-->