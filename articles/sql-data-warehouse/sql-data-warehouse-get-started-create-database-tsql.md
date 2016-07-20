<properties
   pageTitle="TSQL를 사용하여 SQL 데이터 웨어하우스 만들기 | Microsoft Azure"
   description="TSQL를 사용하여 SQL 데이터 웨어하우스를 만드는 방법을 알아봅니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/11/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# TRANSACT-SQL(TSQL)를 사용하여 SQL 데이터 웨어하우스 데이터베이스 만들기

> [AZURE.SELECTOR]
- [Azure 포털](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

이 문서에서는 TRANSACT-SQL(T-SQL)를 사용하여 SQL 데이터 웨어하우스 데이터베이스를 만드는 방법을 보여 줍니다.

## 필수 조건
시작하기 전에 다음과 같은 필수 조건을 충족하는지 확인합니다.

- **Azure 계정**: [Azure 무료 평가판][] 또는 [MSDN Azure 크레딧][]을 참조하여 계정을 만듭니다.
- **V12 Azure SQL Server**: [Azure 포털을 사용하여 Azure SQL 데이터베이스 논리 서버 만들기][] 또는 [PowerShell을 사용하여 Azure SQL 데이터베이스 논리 서버 만들기][]를 참조하세요.
- **리소스 그룹 이름**: V12 Azure SQL 서버와 동일한 리소스 그룹을 사용하거나 [리소스 그룹][]을 참조하여 새 리소스 그룹을 만듭니다.
- **SQL Server Data Tools를 사용하여 Visual Studio**: 설치 지침은 [Visual Studio 및 SSDT 설치][]를 참조하세요.

> [AZURE.NOTE] 새 SQL 데이터 웨어하우스를 만들면 새로운 유료 서비스가 발생할 수 있습니다. 가격에 대한 자세한 내용은 [SQL 데이터 웨어하우스 가격 책정][]을 참조하세요.

## Visual Studio를 사용하여 데이터베이스 만들기

Visual Studio를 처음 접하는 경우 [Visual Studio로 SQL 데이터 웨어하우스에 연결][] 문서를 참조하세요. 시작하려면 Visual Studio에서 SQL Server 개체 탐색기를 열고 SQL 데이터 웨어하우스 데이터베이스를 호스팅할 서버에 연결합니다. 연결한 후에는 **마스터** 데이터베이스에 대해 다음 SQL 명령을 실행하여 SQL 데이터 웨어하우스를 만들 수 있습니다. 이 명령은 서비스 목표 DW400이 있는 MySqlDwDb 데이터베이스를 만들고 데이터베이스가 최대 10TB까지 증대될 수 있게 허용합니다.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## sqlcmd로 데이터베이스 만들기

또는 명령 프롬프트에서 다음을 실행하여 sqlcmd로 동일한 명령을 실행할 수 있습니다.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

`MAXSIZE`는 250GB~240TB 사이가 될 수 있습니다. `SERVICE_OBJECTIVE`는 DW100~DW2000 [DWU][] 사이가 될 수 있습니다. 유효한 모든 값의 목록은 [CREATE DATABASE][]에 대한 MSDN 설명서를 참조하세요. MAXSIZE와 SERVICE\_OBJECTIVE 모두 [ALTER DATABASE][] T-SQL 명령으로 변경할 수 있습니다. SERVICE\_OBJECTIVE를 변경하면 서비스가 재시작되어 진행 중인 모든 쿼리가 취소될 수 있으므로 주의가 필요합니다. MAXSIZE 변경은 간단한 메타데이터 작업이므로 서비스를 다시 시작하지 않습니다.

## 다음 단계
SQL 데이터 웨어하우스에서 프로비전을 완료한 후 [샘플 데이터를 로드][]하거나 [개발][], [로드][] 또는 [마이그레이션][] 방법을 확인할 수 있습니다.

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: ./sql-data-warehouse-get-started-provision.md
[Visual Studio로 SQL 데이터 웨어하우스에 연결]: ./sql-data-warehouse-get-started-connect.md
[마이그레이션]: ./sql-data-warehouse-overview-migrate.md
[개발]: ./sql-data-warehouse-overview-develop.md
[로드]: ./sql-data-warehouse-overview-load.md
[샘플 데이터를 로드]: ./sql-data-warehouse-get-started-load-sample-databases.md
[Azure 포털을 사용하여 Azure SQL 데이터베이스 논리 서버 만들기]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[PowerShell을 사용하여 Azure SQL 데이터베이스 논리 서버 만들기]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[리소스 그룹]: ../azure-portal/resource-group-portal.md
[Visual Studio 및 SSDT 설치]: ./sql-data-warehouse-install-visual-studio.md


<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[SQL 데이터 웨어하우스 가격 책정]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 무료 평가판]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure 크레딧]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0713_2016-->