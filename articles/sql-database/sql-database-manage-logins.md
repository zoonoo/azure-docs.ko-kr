<properties
   pageTitle="Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리 | Microsoft Azure"
   description="서버 수준 보안 주체 및 기타 계정을 사용하여 SQL 데이터베이스에서 로그인 및 데이터베이스를 관리하는 방법입니다."
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
   ms.date="09/25/2015"
   ms.author="rickbyh"/>

# Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리

Microsoft Azure SQL 데이터베이스에서 서비스에 등록하면 프로비전 프로세스가 Azure SQL 데이터베이스 서버의 서버 수준 보안 주체가 되는 Azure SQL 데이터베이스 서버, **마스터**라는 데이터베이스 및 로그인을 만듭니다. 이 로그인은 SQL Server의 온-프레미스 인스턴스의 경우 서버 수준 보안 주체(**sa**)와 비슷합니다.

Azure SQL 데이터베이스 서버 수준 보안 주체 계정은 항상 모든 서버 수준 및 데이터베이스 수준 보안을 관리할 권한이 있습니다. 이 항목에서는 서버 수준 보안 주체 및 기타 계정을 사용하여 SQL 데이터베이스에서 로그인 및 데이터베이스를 관리하는 방법을 설명합니다.

> [AZURE.IMPORTANT]SQL 데이터베이스 V12 사용자를 사용하면 포함된 데이터베이스 사용자를 사용하여 데이터베이스에서 인증할 수 있습니다. 포함된 데이터베이스 사용자는 로그인하지 않아도 됩니다. 데이터베이스를 이식 가능하지만 데이터베이스에 액세스를 제어하는 서버 수준 보안 주체의 성능이 감소합니다. 포함된 데이터베이스 사용자를 사용하면 보안에 중요한 영향이 발생합니다. 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx), [포함된 데이터베이스](https://technet.microsoft.com/library/ff929071.aspx), [CREATE USER(Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx), [Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결](sql-database-aad-authentication.md)을 참조하십시오.

## SQL 데이터베이스 보안 관리 개요

SQL 데이터베이스에서 보안 관리는 SQL Server의 온-프레미스 인스턴스에 대한 보안 관리와 비슷합니다. 데이터베이스 수준에서 보안 관리는 사용 가능한 매개 변수만 다르고 거의 동일합니다. SQL 데이터베이스는 하나 이상의 물리적 컴퓨터로 크기를 조정할 수 있기 때문에 Azure SQL 데이터베이스는 서버 수준 관리에 다른 전략을 사용합니다. 다음 테이블은 온-프레미스 SQL Server에 대한 보안 관리가 Azure SQL 데이터베이스와 어떻게 다른지 요약합니다.

| 차이점 | 온-프레미스 SQL Server | Azure SQL 데이터베이스 |
|------------------------------------------------|-----------------------------------------------------------------------------|--------------------------------------------------|
| 서버 수준 보안을 관리하는 위치 | SQL Server Management Studio의 개체 탐색기에서 **보안** 폴더 | **마스터** 데이터베이스 및 Azure 포털을 통해 |
| Windows 인증 | Active Directory ID | Azure Active Directory ID |
| 로그인을 만들 서버 수준 보안 역할 | **securityadmin** 고정된 서버 역할 | **마스터** 데이터베이스에서 **loginmanager** 데이터베이스 역할 |
| 로그인 관리 명령 | CREATE LOGIN, ALTER LOGIN, DROP LOGIN | CREATE LOGIN, ALTER LOGIN, DROP LOGIN(일부 매개 변수 제한 사항이 있으며 **마스터** 데이터베이스에 연결되어야 함) |
| 모든 로그인을 보여 주는 보기 | sys.server\_principals | sys.sql\_logins(**마스터** 데이터베이스에 연결되어야 함)|
| 데이터베이스를 만들기 위한 서버 수준 역할 | **dbcreator** 고정된 데이터베이스 역할 | **마스터** 데이터베이스에서 **dbmanager** 데이터베이스 역할 |
| 데이터베이스를 만들기 위한 명령 | CREATE DATABASE | CREATE DATABASE(일부 매개 변수 제한 사항이 있으며 **마스터** 데이터베이스에 연결되어야 함) |
| 모든 데이터베이스를 나열하는 보기 | sys.databases | sys.databases(**마스터** 데이터베이스에 연결되어야 함) |

## 서버 수준 관리 및 마스터 데이터베이스

Azure SQL 데이터베이스 서버는 데이터베이스 그룹화를 정의하는 추상입니다. Azure SQL 데이터베이스 서버와 연결된 데이터베이스는 Microsoft 데이터 센터의 개별 물리적 컴퓨터에 상주할 수 있습니다. 이들 모두에 **마스터**라는 단일 데이터베이스를 사용하여 서버 수준 관리를 수행합니다.

**마스터** 데이터베이스는 로그인을 추적하여 데이터베이스 또는 다른 로그인을 만들 수 있는 권한이 있는 로그인을 추적합니다. 로그인 또는 데이터베이스를 생성, 변경 또는 삭제할 때마다 **마스터** 데이터베이스에 연결해야 합니다. **마스터** 데이터베이스에는 각각 로그인과 데이터베이스를 보는 데 사용할 수 있는 ``sys.sql_logins`` 및 ``sys.databases`` 보기가 있습니다.

> [AZURE.NOTE]``USE`` 명령은 데이터베이스 간의 전환에 대해 지원되지 않습니다. 대상 데이터베이스에 직접 연결해야 합니다.

SQL Server의 온-프레미스 인스턴스에 대해 수행하는 것과 동일한 방식으로 Azure SQL 데이터베이스의 사용자 및 개체에 대한 데이터베이스 수준 보안을 관리할 수 있습니다. 해당 명령에 사용할 수 있는 매개 변수에 차이가 있습니다. 자세한 내용은 [Azure SQL 데이터베이스 보안 지침 및 제한 사항](sql-database-security-guidelines.md)을 참조하세요.

## 포함된 데이터베이스 사용자 관리

서버 수준 보안 주체로 데이터베이스에 연결하여 데이터베이스에 처음 포함된 데이터베이스 사용자를 만듭니다. ``CREATE USER``, ``ALTER USER`` 또는 ``DROP USER`` 문을 사용합니다. 다음 예제에서는 user1이라는 사용자를 만듭니다.

```
CREATE USER user1 WITH password='<Strong_Password>';
```

> [AZURE.NOTE]포함된 데이터베이스 사용자를 만들 때 강력한 암호를 사용해야 합니다. 자세한 내용은 [강력한 암호](https://msdn.microsoft.com/library/ms161962.aspx)를 참조하십시오.

추가로 포함된 데이터베이스 사용자는 **ALTER ANY USER** 권한으로 사용자가 만들 수 있습니다.

SQL 데이터베이스 V12는 Azure Active Directory ID를 포함된 데이터베이스 사용자로 미리 보는 기능을 지원합니다. 자세한 내용은 [Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결](sql-database-aad-authentication.md)을 참조하십시오.

Microsoft는 SQL 데이터베이스로 포함된 데이터베이스 사용자를 사용하는 것을 권장합니다. 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요.

## 로그인 관리

마스터 데이터베이스에 연결하여 서버 수준 보안 주체 로그인으로 로그인을 관리합니다. ``CREATE LOGIN``, ``ALTER LOGIN`` 또는 ``DROP LOGIN`` 문을 사용할 수 있습니다. 다음 예제에서는 **login1**이라는 로그인을 만듭니다.

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
```

> [AZURE.NOTE]로그인을 만들 때 강력한 암호를 사용해야 합니다. 자세한 내용은 [강력한 암호](https://msdn.microsoft.com/library/ms161962.aspx)를 참조하십시오.

#### 새 로그인 사용

만든 로그인을 사용하여 Microsoft Azure SQL 데이터베이스에 연결하기 위해 먼저 ``CREATE USER`` 명령을 사용하여 각 로그인 데이터베이스 수준의 권한을 부여해야 합니다. 자세한 내용은 [로그인에 데이터베이스 수준 사용 권한 부여](https://msdn.microsoft.com/library/ee336235.aspx#DatabasePerms)를 참조하세요.

일부 도구는 TDS(Tabular Data Stream)를 다르게 구현하기 때문에 ``<login>@<server>`` 표기법을 사용하여 연결 문자열의 로그인에 Azure SQL 데이터베이스 서버 이름을 추가해야할 수 있습니다. 이러한 경우 ``@`` 기호가 있는 로그인 및 Azure SQL 데이터베이스 서버 이름을 분리합니다. 예를 들어 로그인 이름 **login1**이고 Azure SQL 데이터베이스 서버의 정규화된 이름이 **servername.database. windows.net**인 경우 연결 문자열의 사용자 이름 매개 변수는 * ***login1@servername**여야 합니다. 제한 사항은 로그인 이름으로 선택할 수 있는 텍스트를 제한합니다. 자세한 내용은 [로그인 만들기(Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)를 참조하세요.

## 로그인에 서버 수준 권한을 부여합니다.

서버 수준 보안을 관리하기 위해 서버 수준 보안 주체가 아닌 다른 로그인을 위해 Azure SQL 데이터베이스가 두 가지 보안 역할을 제공합니다. 로그인을 만들기 위한 **loginmanager** 및 데이터베이스를 만들기 위한 **dbmanager**입니다. **마스터** 데이터베이스의 사용자만이 이러한 데이터베이스 역할에 추가될 수 있습니다.

> [AZURE.NOTE]로그인 또는 데이터베이스를 만들려면 **마스터** 데이터베이스에 연결되어야 합니다.(**마스터**의 논리적 표현임)

### Loginmanager 역할

SQL Server의 온-프레미스 인스턴스에 대한 **securityadmin** 고정 서버 역할과 마찬가지로 Azure SQL 데이터베이스의 **loginmanager** 데이터베이스 역할은 로그인을 만들 수 있는 권한이 있습니다. 서버 수준 보안 주체 로그인(프로비전 프로세스로 만들어진) 또는 **loginmanager** 데이터베이스 역할의 멤버는 새 로그인을 만들 수 있습니다.

### dbmanager 역할

Azure SQL 데이터베이스 **dbmanager** 데이터베이스 역할은 SQL Server의 온-프레미스 인스턴스에 대한 **dbcreator** 고정된 서버 역할과 유사합니다. 서버 수준 보안 주체 로그인(프로비전 프로세스로 만들어진) 또는 **dbmanager** 데이터베이스 역할의 멤버는 데이터베이스를 만들 수 있습니다. 사용자가 **dbmanager** 데이터베이스 역할의 멤버이면 Azure SQL 데이터베이스 ``CREATE DATABASE`` 명령을 사용하여 데이터베이스를 만들지만 해당 명령은 마스터 데이터베이스에서 실행되어야 합니다. 자세한 내용은 [데이터베이스 만들기(Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx)를 참조하세요.

### SQL 데이터베이스 서버 수준 역할을 할당하는 방법

데이터베이스 또는 다른 로그인을 만들 수 있는 로그인 및 연결된 사용자를 만들려면 다음 단계를 수행합니다.

1. 서버 수준 보안 주체 로그인의 자격 증명(프로비전 프로세스로 만들어짐) 또는 **loginmanager** 데이터베이스 역할의 기존 멤버 자격 증명을 사용하여 **마스터** 데이터베이스에 연결합니다.
2. ``CREATE LOGIN`` 명령을 사용하여 로그인을 만듭니다. 자세한 내용은 [로그인 만들기(Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)를 참조하세요.
3. ``CREATE USER`` 명령을 사용하여 마스터 데이터베이스에서 해당 로그인에 새 사용자를 만듭니다. 자세한 내용은 [사용자 만들기(Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)를 참조하세요.
4. 저장된 프로시저 ``sp_addrolememeber``를 사용하여 **dbmanager** 데이터베이스 역할, loginmanager 데이터베이스 역할 중 하나 또는 둘 모두에 새 사용자를 추가합니다.

다음 코드 예제에서는 **login1**이라는 로그인과 데이터베이스나 **마스터** 데이터베이스에 연결되어 있는 동안 다른 로그인을 만들 수 있는 **login1User**라는 해당 데이터베이스 사용자를 만드는 방법을 보여줍니다.

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE USER login1User FROM LOGIN login1;
EXEC sp_addrolemember 'dbmanager', 'login1User';
EXEC sp_addrolemember 'loginmanager', 'login1User';
```

> [AZURE.NOTE]로그인을 만들 때 강력한 암호를 사용해야 합니다. 자세한 내용은 [강력한 암호](https://msdn.microsoft.com/library/ms161962.aspx)를 참조하십시오.

## 로그인에 데이터베이스 액세스 권한 부여

모든 로그인은 **마스터** 데이터베이스에서 만들어야 합니다. 로그인이 만들어진 후에 해당 로그인에 대해 다른 데이터베이스에 사용자 계정을 만들 수 있습니다. 또한 Azure SQL 데이터베이스는 SQL Server의 온-프레미스 인스턴스가 수행하는 동일한 방식으로 데이터베이스 역할을 지원합니다.

다른 데이터베이스에 사용자 계정을 만들려면 로그인 또는 데이터베이스를 만들지 않았다고 가정하고 다음 단계를 수행합니다.

1. **마스터** 데이터베이스에 연결합니다.(**loginmanager** 및 **dbmanager** 역할이 있는 로그인을 사용)
2. ``CREATE LOGIN`` 명령을 사용하여 새 로그인을 만듭니다. 자세한 내용은 [로그인 만들기(Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)를 참조하세요. Windows 인증은 지원되지 않습니다.
3. ``CREATE DATABASE`` 명령을 사용하여 새 데이터베이스를 만듭니다. 자세한 내용은 [데이터베이스 만들기(Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx)를 참조하세요.
4. 새 데이터베이스에 연결을 설정합니다.(데이터베이스를 만든 로그인을 사용)
5. ``CREATE USER`` 명령을 사용하여 새 데이터베이스에 새 사용자를 만듭니다. 자세한 내용은 [사용자 만들기(Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)를 참조하세요.

다음 코드 예제에서는 **login1**이라는 로그인 및 **database1**라는 데이터베이스를 만드는 방법을 보여줍니다.

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE DATABASE database1;
```

> [AZURE.NOTE]로그인을 만들 때 강력한 암호를 사용해야 합니다. 자세한 내용은 [강력한 암호](https://msdn.microsoft.com/library/ms161962.aspx)를 참조하십시오.

이 다음 예제에서는 로그인 **login1**에 해당하는 데이터베이스 **database1**에 **login1User**라는 데이터베이스 사용자를 만드는 방법을 보여줍니다. 다음 예제를 실행하려면 해당 데이터베이스에서 **ALTER ANY USER** 권한으로 로그인을 사용하여 먼저 database1에 대한 새 연결을 만들어야 합니다. **db\_owner** 역할의 멤버로 연결하는 사용자는 데이터베이스를 만든 로그인 등과 같은 권한을 갖습니다.

```
-- Establish a new connection to the database1 database
CREATE USER login1User FROM LOGIN login1;
```

Azure SQL 데이터베이스에서 이 데이터베이스 수준 권한 모델은 SQL Server의 온-프레미스 인스턴스와 동일합니다. 정보는 SQL Server 온라인 설명서 참조의 다음 항목을 참조하세요.

- [로그인, 사용자 및 스키마 방법 항목 관리](https://msdn.microsoft.com/library/aa337552.aspx) 
- [단원 2: 데이터베이스 개체에 사용 권한 구성](https://msdn.microsoft.com/library/ms365345.aspx) 

> [AZURE.NOTE]Azure SQL 데이터베이스에서 보안 관련 Transact-SQL 문은 사용할 수 있는 매개 변수에서 약간 다를 수 있습니다. 자세한 내용은 특정 문에 대한 온라인 설명서 구문을 참조하세요.

## 로그인 및 데이터베이스 보기


Azure SQL 데이터베이스 서버에서 로그인 및 데이터베이스를 보려면 마스터 데이터베이스의 ``sys.sql_logins`` 및 ``sys.databases`` 보기를 각각 사용합니다. 다음 예제에서는 Azure SQL 데이터베이스 서버에서 모든 로그인 및 데이터베이스 목록을 표시하는 방법을 보여줍니다.

```
-- first, connect to the master database
SELECT * FROM sys.sql_logins;
SELECT * FROM sys.databases; 
```

## 참고 항목

[Azure SQL 데이터베이스 보안 지침 및 제한 사항](sql-database-security-guidelines.md) [Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결](sql-database-aad-authentication.md)

<!---HONumber=Oct15_HO1-->