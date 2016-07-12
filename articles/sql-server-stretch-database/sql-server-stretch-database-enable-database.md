<properties
	pageTitle="데이터베이스에 대해 스트레치 데이터베이스를 사용하도록 설정 | Microsoft Azure"
	description="스트레치 데이터베이스에 대해 데이터베이스를 구성하는 방법을 알아봅니다."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="douglasl"/>

# 데이터베이스에 대해 스트레치 데이터베이스를 사용하도록 설정

스트레치 데이터베이스에 대해 기존 데이터베이스를 구성하려면 SQL Server Management Studio에서 데이터베이스에 대해 **작업 | 스트레치 | 사용**을 선택하여 **스트레치에 데이터베이스 사용** 마법사를 엽니다. Transact-SQL을 사용하여 데이터베이스에 대해 스트레치 데이터베이스를 사용하도록 설정할 수도 있습니다.

스트레치 데이터베이스에 대해 데이터베이스를 사용하도록 설정하지 않은 경우 개별 테이블에 대해 **작업 | 스트레치 | 사용**을 선택하면 마법사에서 스트레치 데이터베이스에 대해 데이터베이스를 구성하고 사용자가 프로세스의 일부로 테이블을 선택할 수 있도록 합니다. [테이블에 대해 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-database.md)의 단계 대신 이 항목의 단계를 따르세요.

데이터베이스 또는 테이블에 대해 스트레치 데이터베이스를 사용하도록 설정하려면 db\_owner 권한이 필요합니다. 또한 데이터베이스에 대해 스트레치 데이터베이스를 사용하도록 설정하려면 CONTROL DATABASE 권한이 필요합니다.

## 시작하기 전에

-   스트레치에 대해 데이터베이스를 구성하기 전에 스트레치 데이터베이스 관리자를 실행하여 스트레치에 적합한 데이터베이스 및 테이블을 식별하는 것이 좋습니다. 스트레치 데이터베이스 관리자는 차단 문제도 식별합니다. 자세한 내용은 [스트레치 데이터베이스용 데이터베이스 및 테이블 식별](sql-server-stretch-database-identify-databases.md)을 참조하세요.

-   [스트레치 데이터베이스에 대한 제한](sql-server-stretch-database-limitations.md)을 검토하세요.

-   스트레치 데이터베이스는 데이터를 Azure에 마이그레이션합니다. 따라서 대금 청구를 위한 Azure 계정 및 구독이 있어야 합니다. Azure 계정을 얻으려면 [여기를 클릭](http://azure.microsoft.com/pricing/free-trial/)하세요.

-   새 Azure 서버를 만들거나 기존 Azure 서버를 선택하는 데 필요한 연결 및 로그인 정보가 필요합니다.

## <a name="EnableTSQLServer"></a>필수 조건: 서버에서 스트레치 데이터베이스를 사용하도록 설정
데이터베이스 또는 테이블에서 스트레치 데이터베이스를 사용하도록 설정하려면 먼저 로컬 서버에서 사용하도록 설정해야 합니다. 이 작업에는 sysadmin 또는 serveradmin 권한이 필요합니다.

-   필요한 관리 권한이 있는 경우 **스트레치에 데이터베이스 사용** 마법사가 스트레치에 대해 서버를 구성합니다.

-   필요한 권한이 없는 경우 마법사를 실행하려면 먼저 관리자가 **sp\_configure**를 실행하여 옵션을 수동으로 설정해야 합니다. 또는 관리자가 마법사를 실행해야 합니다.

서버에서 수동으로 스트레치 데이터베이스를 사용하도록 설정하려면 **sp\_configure**를 실행하고 **원격 데이터 보관** 옵션을 설정합니다. 다음 예에서는 해당 값을 1로 설정하여 **원격 데이터 보관** 옵션을 사용하도록 설정합니다.

```
EXEC sp_configure 'remote data archive' , '1';
GO
RECONFIGURE;
GO
```
자세한 내용은 [원격 데이터 보관 서버 구성 옵션 구성](https://msdn.microsoft.com/library/mt143175.aspx) 및 [sp\_configure(TRANSACT-SQL)](https://msdn.microsoft.com/library/ms188787.aspx)를 참조하세요.

## <a name="Wizard"></a>마법사를 사용하여 데이터베이스에서 스트레치 데이터베이스를 사용하도록 설정
입력해야 하는 정보 및 선택해야 하는 항목을 포함하여 스트레치에 데이터베이스 사용 마법사에 대한 자세한 내용은 [스트레치에 데이터베이스 사용 마법사를 실행하여 시작](sql-server-stretch-database-wizard.md)을 참조하세요.

## <a name="EnableTSQLDatabase"></a>Transact-SQL을 사용하여 데이터베이스에서 스트레치 데이터베이스를 사용하도록 설정
개별 테이블에서 스트레치 데이터베이스를 사용하도록 설정하려면 먼저 데이터베이스에서 사용하도록 설정해야 합니다.

데이터베이스 또는 테이블에 대해 스트레치 데이터베이스를 사용하도록 설정하려면 db\_owner 권한이 필요합니다. 또한 데이터베이스에 대해 스트레치 데이터베이스를 사용하도록 설정하려면 CONTROL DATABASE 권한이 필요합니다.

1.  시작하기 전에 스트레치 데이터베이스에서 마이그레이션하는 데이터에 대한 기존 Azure 서버를 선택하거나 새 Azure 서버를 만듭니다.

2.  Azure 서버에서 SQL Server가 원격 서버와 통신하는 데 필요한 SQL Server의 IP 주소 범위를 사용하여 방화벽 규칙을 만듭니다.

3.  스트레치 데이터베이스에 대해 SQL Server 데이터베이스를 구성하려면 데이터베이스에 데이터베이스 마스터 키가 있어야 합니다. 데이터베이스 마스터 키는 스트레치 데이터베이스가 원격 데이터베이스에 연결하는 데 사용하는 자격 증명을 보호합니다. 새 데이터베이스 마스터 키를 만드는 예제는 다음과 같습니다.

    ```tsql
    USE <database>;
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
	GO
    ```

    데이터베이스 마스터 키에 대한 자세한 내용은 [CREATE MASTER KEY(Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) 및 [데이터베이스 마스터 키](https://msdn.microsoft.com/library/aa337551.aspx)를 참조하세요.

4.  스트레치 데이터베이스에 대해 데이터베이스를 구성하는 경우 온-프레미스 SQL Server와 원격 Azure 서버 간의 통신에 사용할 스트레치 데이터베이스의 자격 증명을 제공해야 합니다. 다음 두 가지 옵션을 사용할 수 있습니다.

    -   관리자 자격 증명을 제공할 수 있습니다.

        -   마법사를 실행하여 스트레치 데이터베이스를 사용하도록 설정하는 경우 해당 시점에서 자격 증명을 만들 수 있습니다.

        -   **ALTER DATABASE**를 실행하여 스트레치 데이터베이스를 사용하도록 설정하려는 경우 **ALTER DATABASE**를 실행하기 전에 먼저 자격 증명을 수동으로 만들어야 합니다.

		새 자격 증명을 만드는 예제는 다음과 같습니다.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>';
        GO
        ```

		자격 증명에 대한 자세한 내용은 [데이터베이스 범위 자격 증명 만들기(Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx)를 참조하세요. 자격 증명을 만들려면 ALTER ANY CREDENTIAL 권한이 필요합니다.

    -   다음 조건을 모두 충족하는 경우 SQL Server의 페더레이션된 서비스 계정을 사용하여 원격 Azure 서버와 통신할 수 있습니다.

        -   SQL Server 인스턴스가 실행되는 서비스 계정은 도메인 계정입니다.

        -   도메인 계정은 Active Directory가 Azure Active Directory와 페더레이션된 도메인에 속해 있습니다.

        -   원격 Azure 서버는 Azure Active Directory 인증을 지원하도록 구성됩니다.

        -   SQL Server 인스턴스가 실행되는 서비스 계정은 Azure 원격 서버에서 dbmanager 또는 sysadmin 계정으로 구성되어야 합니다.

5.  스트레치 데이터베이스에 대해 데이터베이스를 구성하려면 ALTER DATABASE 명령을 실행합니다.

    1.  SERVER 인수에 대해 기존 Azure 서버의 이름(`.database.windows.net` 부분 포함)을 제공합니다(예: `MyStretchDatabaseServer.database.windows.net`).

    2.  CREDENTIAL 인수에 기존 관리자가 자격 증명을 제공하거나 FEDERATED\_SERVICE\_ACCOUNT = ON을 지정합니다. 다음 예제에서는 기존 자격 증명을 제공합니다.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO
    ```

## 다음 단계
-   [테이블에 대해 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-table.md) - 추가 테이블 사용

-   [스트레치 데이터베이스 모니터링](sql-server-stretch-database-monitor.md) - 데이터 마이그레이션 상태 확인

-   [스트레치 데이터베이스 일시 중지 및 다시 시작](sql-server-stretch-database-pause.md)

-   [스트레치 데이터베이스 관리 및 문제 해결](sql-server-stretch-database-manage.md)

-   [스트레치 지원 데이터베이스 백업](sql-server-stretch-database-backup.md)

## 참고 항목

[스트레치 데이터베이스에 대한 데이터베이스 및 테이블 식별](sql-server-stretch-database-identify-databases.md)

[ALTER DATABASE SET 옵션(TRANSACT-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

<!---HONumber=AcomDC_0629_2016-->