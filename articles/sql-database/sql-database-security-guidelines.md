<properties
   pageTitle="Azure SQL 데이터베이스 보안 지침 및 제한 사항 | Microsoft Azure"
   description="Microsoft Azure SQL 데이터베이스 지침 및 보안과 관련된 제한 사항을 알아봅니다."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/02/2016"
   ms.author="rickbyh"/>

# Azure SQL 데이터베이스 보안 지침 및 제한 사항

이 항목에서는 Microsoft Azure SQL 데이터베이스 지침 및 보안과 관련 된 제한 사항을 설명합니다. Azure SQL 데이터베이스의 보안을 관리할 때 다음 사항을 고려합니다.

## 가상 master 데이터베이스에 대한 액세스

일반적으로 관리자만 master 데이터베이스에 대한 액세스가 필요합니다. 각 사용자 데이터베이스에 대한 일상적인 액세스는 각 데이터베이스에서 관리자가 아닌 포함된 데이터베이스 사용자를 통해서여야 합니다. 포함된 데이터베이스 사용자를 사용하는 경우 master 데이터베이스에서 로그인을 만들 필요가 없습니다. 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요.


## 방화벽

전체 Azure SQL Server에 대해 범위가 지정되는 SQL Server 방화벽은 일반적으로 포털을 통해 구성되고 관리자가 사용하는 IP 주소만 허용해야 합니다. 사용자 데이터베이스에 연결한 다음 [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) Transact-SQL 문을 사용하여 각 데이터베이스에 대한 필요한 범위의 IP 주소를 열게 될 데이터베이스 범위 방화벽 규칙을 만듭니다.

Azure SQL 데이터베이스 서비스는 TCP 포트 1433을 통해서만 사용할 수 있습니다. 사용자의 컴퓨터에서 SQL 데이터베이스에 액세스하려면 클라이언트 컴퓨터 방화벽이 TCP 포트 1433을 통해 나가는 TCP 통신을 허용해야 합니다. 다른 응용 프로그램에 필요하지 않은 경우 TCP 포트 1433의 인바운드 연결을 차단합니다.

연결 프로세스의 일부로 Azure 가상 컴퓨터에서 연결은 각 작업자 역할에 대한 고유한 다른 IP 주소 및 포트에 리디렉션됩니다. 포트 번호의 범위는 11000에서 11999입니다. TCP 포트에 대한 자세한 내용은 [ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.


## 인증

가능하면 Active Directory 인증(통합 보안)을 사용합니다. AD 인증 구성에 대한 자세한 내용은 [Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결](sql-database-aad-authentication.md) 및 SQL Server 온라인 설명서의 [인증 모드 선택](https://msdn.microsoft.com/library/ms144284.aspx)을 참조하세요.

포함된 데이터베이스 사용자를 사용하여 확장성을 향상합니다. 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER(Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) 및 [포함된 데이터베이스](https://technet.microsoft.com/library/ff929071.aspx)를 참조하세요.

데이터베이스 엔진은 30분 이상 유휴 상태로 있는 연결을 닫습니다. 연결을 사용하기 전에 다시 로그인해야 합니다.

SQL 데이터베이스에 활성 연결은 지속적으로 적어도 10시간 마다 권한을 다시 부여받아야 합니다.(데이터베이스 엔진에서 수행함) 데이터베이스 엔진은 전송된 원래 암호를 사용하여 권한을 다시 부여하려고 하며 사용자 입력이 필요하지 않습니다. 성능상의 이유로 SQL 데이터베이스에 암호를 다시 설정할 경우 연결 풀링으로 인해 연결을 재설정하더라도 연결은 다시 인증되지 않습니다. 온-프레미스 SQL Server의 동작과 다릅니다. 연결이 처음에 권한을 부여받은 이후에 암호가 변경되었다면 연결을 종료해야 하고 새 암호를 사용하여 새로 연결합니다. KILL DATABASE CONNECTION 권한이 있는 사용자는 [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 명령을 사용하여 SQL 데이터베이스에 연결을 명시적으로 종료할 수 있습니다.

## 로그인 및 사용자

SQL 데이터베이스에서 로그인 및 사용자를 관리하는 경우 제한이 있습니다.


- ``CREATE/ALTER/DROP DATABASE`` 문을 실행할 때 **master** 데이터베이스에 연결해야 합니다. 서버 수준 보안 주체 로그인에 해당하는 master 데이터베이스의 데이터베이스 사용자는 변경 또는 삭제할 수 없습니다.
- 서버 수준 보안 주체 로그인의 기본 언어는 미국 영어입니다.
- **마스터** 데이터베이스에 액세스하려면 모든 로그인은 **마스터** 데이터베이스의 사용자 계정에 매핑해야 합니다. **마스터** 데이터베이스는 포함된 데이터베이스 사용자를 지원하지 않습니다.
- **마스터** 데이터베이스에서 **dbmanager** 데이터베이스 역할의 서버 수준 보안 주체 로그인 및 멤버는 ``CREATE DATABASE`` 및 ``DROP DATABASE`` 문을 실행할 권한이 있습니다.
- ``CREATE/ALTER/DROP LOGIN`` 문을 실행할 경우 master 데이터베이스에 연결해야 합니다. 그러나 로그인 사용은 권장되지 않습니다. 대신에 포함된 데이터베이스 사용자를 사용합니다.
- 사용자 데이터베이스에 연결하려면 연결 문자열에 데이터베이스 이름을 제공해야 합니다.
- **마스터** 데이터베이스에서 **loginmanager** 데이터베이스 역할의 서버 수준 보안 주체 로그인 및 멤버는 ``CREATE LOGIN``, ``ALTER LOGIN`` 및 ``DROP LOGIN`` 문을 실행할 권한이 있습니다.
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
- ``CREATE/ALTER/DROP``에 사용자가 ``ALTER ANY USER`` 데이터베이스에 대한 권한을 요청합니다.
- 데이터베이스 역할의 소유자가 해당 데이터베이스 역할에서 다른 데이터베이스 사용자를 추가 또는 제거하려고 할 때 다음과 같은 오류가 발생할 수 있습니다. **사용자 또는 역할 '이름'이 데이터베이스에 존재하지 않습니다.** 사용자가 소유자에게 표시되지 않기 때문에 이 오류가 발생합니다. 이 문제를 해결하려면 역할 소유자에게 사용자에 대한 ``VIEW DEFINITION`` 권한을 부여합니다.

로그인 및 사용자에 대한 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요.


## 참고 항목

[Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)

[방화벽 설정 구성: 만들기 (Azure SQL 데이터베이스)](sql-database-configure-firewall-settings.md)

[Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)

[SQL Server 데이터베이스 엔진 및 Azure SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_0803_2016-->