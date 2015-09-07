<properties
   pageTitle="Azure SQL 데이터베이스 보안 지침 및 제한 사항 | Microsoft Azure"
	description="Microsoft Azure SQL 데이터베이스 지침 및 보안과 관련된 제한 사항을 알아봅니다."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""
	tags=""/>

<tags
   ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="08/20/2015"
	ms.author="rickbyh"/>

# Azure SQL 데이터베이스 보안 지침 및 제한 사항

이 항목에서는 Microsoft Azure SQL 데이터베이스 지침 및 보안과 관련 된 제한 사항을 설명합니다. Azure SQL 데이터베이스의 보안을 관리할 때 다음 사항을 고려합니다.

## 방화벽

Azure SQL 데이터베이스 서비스는 TCP 포트 1433을 통해서만 사용할 수 있습니다. 사용자의 컴퓨터에서 SQL 데이터베이스에 액세스하려면 방화벽이 TCP 포트 1433을 통해 나가는 TCP 통신을 허용해야 합니다. 연결 프로세스의 일부로 Azure 가상 컴퓨터에서 연결은 각 작업자 역할에 대한 고유한 다른 IP 주소 및 포트에 리디렉션됩니다. 포트 번호의 범위는 11000에서 11999입니다.

처음으로 Azure SQL 데이터베이스 서버에 연결하기 전에 [Azure 포털](https://portal.azure.com) 또는 [Azure 플랫폼 관리 포털](https://manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/All/dashboard)을 사용하여 Azure SQL 데이터베이스 방화벽을 구성해야 합니다. 컴퓨터 또는 Azure에서 Azure SQL 데이터베이스 서버로 연결을 시도할 수 있는 서버 수준 방화벽 설정을 만들어야 합니다. 또한 Azure SQL 데이터베이스 서버에서 특정 데이터베이스에 액세스를 제어하려면 해당 데이터베이스에 대한 데이터베이스 수준 방화벽 규칙을 만듭니다. 자세한 내용은 [Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)을 참조하세요.

## 연결 암호화 및 인증서 유효성 검사

SQL 데이터베이스와 응용 프로그램 간의 모든 통신은 항상 암호화(SSL)가 필요합니다. 클라이언트 응용 프로그램이 연결 시 인증서의 유효성을 검사하지 않는 경우 SQL 데이터베이스에 연결은 "메시지 가로채기" 공격을 받기 쉽습니다.

응용 프로그램 코드 또는 도구를 사용하여 인증서의 유효성을 검사하려면 명시적으로 암호화된 연결을 요청하고 서버 인증서를 신뢰하지 않습니다. 응용 프로그램 코드 또는 도구가 암호화된 연결을 요청하지 않더라도 암호화된 연결을 받습니다. 그러나 서버 인증서의 유효성을 검사하지 않고 따라서 "메시지 가로채기" 공격에 노출되기 쉽습니다.

ADO.NET 응용 프로그램 코드를 사용하여 인증서의 유효성을 검사하려면 데이터베이스 연결 문자열에서 ``Encrypt=True`` 및 ``TrustServerCertificate=False``를 설정합니다. 더 자세한 내용은 [코드 샘플: ADO.NET을 사용하여 Azure SQL 데이터베이스에 연결하는 논리 다시 시도](https://msdn.microsoft.com/library/azure/ee336243.aspx)를 참조하세요.

또한 SQL Server Management Studio는 인증서 유효성 검사를 지원합니다. **서버에 연결** 대화 상자의 **연결 속성** 탭에서 **연결 암호화**를 클릭합니다.

> [AZURE.NOTE]SQL Server Management Studio는 SQL Server 2008 R2 이전 버전에서 SQL 데이터베이스에 연결을 지원하지 않습니다.

SQLCMD이 SQL Server 2008부터 SQL 데이터베이스를 지원했지만 SQL Server 2008 R2 이전 버전에서는 인증서 유효성 검사를 지원하지 않습니다. SQL Server 2008 R2에서 시작하는 SQLCMD를 사용하는 인증서의 유효성을 검사하려면 ``-N`` 명령 줄 옵션을 사용하고 ``-C`` 옵션을 사용하지 않습니다. -N 옵션을 사용하여 SQLCMD가 암호화된 연결을 요청합니다. ``-C`` 옵션을 사용하지 않고 SQLCMD가 암시적으로 서버 인증서를 신뢰하지 않고 강제로 인증서의 유효성을 검사합니다.

보충 기술 정보는 TechNet Wiki 사이트에서 [Azure SQL 데이터베이스 연결 보안](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#comment-4847) 문서를 참조하세요.

## 인증

SQL 데이터베이스는 SQL Server 인증만 지원합니다. Windows 인증(통합 보안)은 지원하지 않습니다. 사용자는 SQL 데이터베이스에 연결할 때마다 자격 증명(로그인 및 암호)을 제공해야 합니다. SQL Server 인증에 대한 자세한 내용은 SQL Server 온라인 설명서에서 [인증 모드 선택](https://msdn.microsoft.com/library/ms144284.aspx)을 참조하세요.

[SQL 데이터베이스 V12](sql-database-v12-whats-new.md)를 사용하면 사용자는 포함된 데이터베이스 사용자를 사용하여 데이터베이스에서 인증할 수 있습니다. 자세한 내용은[ 포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER(Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) 및 [포함된 데이터베이스](https://technet.microsoft.com/library/ff929071.aspx)를 참조하세요.

> [AZURE.NOTE]Microsoft는 포함된 데이터베이스 사용자를 사용하여 확장성을 향상하는 것을 권장합니다.

데이터베이스 엔진은 30분 이상 유휴 상태로 있는 연결을 닫습니다. 연결을 사용하기 전에 다시 로그인해야 합니다.

SQL 데이터베이스에 활성 연결은 지속적으로 적어도 10시간 마다 권한을 다시 부여받아야 합니다.(데이터베이스 엔진에서 수행함) 데이터베이스 엔진은 전송된 원래 암호를 사용하여 권한을 다시 부여하려고 하며 사용자 입력이 필요하지 않습니다. 성능상의 이유로 SQL 데이터베이스에 암호를 다시 설정할 경우 연결 풀링으로 인해 연결을 재설정하더라도 연결은 다시 인증되지 않습니다. 온-프레미스 SQL Server의 동작과 다릅니다. 연결이 처음에 권한을 부여받은 이후에 암호가 변경되었다면 연결을 종료해야 하고 새 암호를 사용하여 새로 연결합니다. KILL DATABASE CONNECTION 권한이 있는 사용자는 [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 명령을 사용하여 SQL 데이터베이스에 연결을 명시적으로 종료할 수 있습니다.

## 로그인 및 사용자

SQL 데이터베이스에서 로그인 및 사용자를 관리하는 경우 제한이 있습니다.

서버 수준 보안 주체 로그인의 경우 다음과 같은 제한 사항이 적용됩니다.

- 서버 수준 보안 주체 로그인에 해당하는 마스터 데이터베이스에서 데이터베이스 사용자를 변경하거나 삭제할 수 없습니다. 
- 서버 수준 보안 주체 로그인이 **마스터** 데이터베이스에서 **dbmanager** 및 **loginmanager**라는 두 개의 데이터베이스 역할의 구성원이 아니라도 이 두 역할에 부여된 모든 사용 권한을 갖습니다.

> [AZURE.NOTE]이 로그인은 서버를 프로비전하는 동안 만들어 지고 SQL Server의 인스턴스에서 **sa** 로그인과 비슷합니다.

모든 로그인의 경우 다음과 같은 제한 사항이 적용됩니다.

- 영어(미국)는 기본 언어입니다.
- **마스터** 데이터베이스에 액세스하려면 모든 로그인은 **마스터** 데이터베이스의 사용자 계정에 매핑해야 합니다. **마스터** 데이터베이스는 포함된 데이터베이스 사용자를 지원하지 않습니다.
- 연결 문자열에 데이터베이스를 지정하지 않는 경우 기본적으로 **마스터** 데이터베이스에 연결됩니다.
- ``CREATE/ALTER/DROP LOGIN`` 및 ``CREATE/ALTER/DROP DATABASE`` 문을 실행할 경우 **마스터** 데이터베이스에 연결해야 합니다. 
- ADO.NET 응용 프로그램에서 ``CREATE/ALTER/DROP LOGIN`` 및 ``CREATE/ALTER/DROP DATABASE`` 문을 실행하는 경우 매개 변수화된 명령을 사용할 수 없습니다. 자세한 내용은 [명령 및 매개 변수](https://msdn.microsoft.com/library/ms254953.aspx)를 참조하세요.
- ``CREATE/ALTER/DROP DATABASE`` 및 ``CREATE/ALTER/DROP LOGIN`` 문을 실행하는 경우 이러한 각 문은 Transact-SQL 배치에서 유일한 문이어야 합니다. 그렇지 않은 경우 오류가 발생합니다. 예를 들어 다음 Transact-SQL는 데이터베이스가 있는지를 확인합니다. 있는 경우 ``DROP DATABASE`` 문이 호출되어 데이터베이스를 제거합니다. ``DROP DATABASE`` 문은 배치에서 유일한 문이 아니기 때문에 다음 Transact-SQL 문을 실행하면 오류가 발생합니다.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- ``CREATE USER``문을 ``FOR/FROM LOGIN`` 옵션과 함께 실행하는 경우 Transact-SQL 배치에서 유일한 문이어야 합니다.
- ``ALTER USER``문을 ``WITH LOGIN`` 옵션과 함께 실행하는 경우 Transact-SQL 배치에서 유일한 문이어야 합니다.
- **마스터** 데이터베이스에서 **dbmanager** 데이터베이스 역할의 서버 수준 보안 주체 로그인 및 멤버는 ``CREATE DATABASE`` 및 ``DROP DATABASE`` 문을 실행할 권한이 있습니다.
- **마스터** 데이터베이스에서 **loginmanager** 데이터베이스 역할의 서버 수준 보안 주체 로그인 및 멤버는 ``CREATE LOGIN``, ``ALTER LOGIN`` 및 ``DROP LOGIN`` 문을 실행할 권한이 있습니다.
- ``CREATE/ALTER/DROP``에 사용자가 ``ALTER ANY USER`` 데이터베이스에 대한 권한을 요청합니다.
- 데이터베이스 역할의 소유자가 해당 데이터베이스 역할에서 다른 데이터베이스 사용자를 추가 또는 제거하려고 할 때 다음과 같은 오류가 발생할 수 있습니다. **사용자 또는 역할 '이름'이 데이터베이스에 존재하지 않습니다.** 사용자가 소유자에게 표시되지 않기 때문에 이 오류가 발생합니다. 이 문제를 해결하려면 역할 소유자에게 사용자에 대한 ``VIEW DEFINITION`` 권한을 부여합니다. 

로그인 및 사용자에 대한 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요.

## 보안 모범 사례

다음 사항을 고려하여 Azure SQL 데이터베이스 응용 프로그램을 보안 위협에 덜 취약하게 합니다.

- 항상 최신 업데이트를 사용합니다. SQL 데이터베이스에 연결할 때 항상 최신 버전의 도구 및 라이브러리를 사용하여 보안 취약점을 방지합니다. 지원되는 도구 및 라이브러리에 대한 자세한 내용은 [Azure SQL 데이터베이스 일반 지침 및 제한 사항](https://msdn.microsoft.com/library/azure/ee336245.aspx)을 참조하세요.
- TCP 포트 1433에서 인바운드 연결을 차단합니다. SQL 데이터베이스와 통신하는 응용 프로그램에 대한 TCP 포트 1433에서 아웃 바운드 연결만 필요합니다. 해당 컴퓨터에서 다른 응용 프로그램이 인바운드 통신을 필요로 하지 않는 경우 방화벽이 TCP 포트 1433에서 인바운드 연결을 계속 차단하는지 확인합니다.
- 삽입 공격 취약성을 방지합니다. 가능한 경우 응용 프로그램에 SQL 주입 공격 취약성이 없는지 확인하려면 매개 변수가 있는 쿼리를 사용합니다. 또한 응용 프로그램을 배포하기 전에 코드를 철저히 검토하고 침투 테스트를 실행합니다.


## 참고 항목

[Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)

[방화벽 설정 구성: 만들기 (Azure SQL 데이터베이스)](sql-database-configure-firewall-settings.md)

[Azure SQL 데이터베이스 일반 지침 및 제한 사항](https://msdn.microsoft.com/library/azure/ee336245.aspx)

[Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)

<!---HONumber=August15_HO9-->