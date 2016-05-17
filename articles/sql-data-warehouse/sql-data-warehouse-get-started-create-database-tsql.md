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
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# TRANSACT-SQL(TSQL)를 사용하여 SQL 데이터 웨어하우스 데이터베이스 만들기

> [AZURE.SELECTOR]
- [Azure 포털](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

이 문서에서는 TRANSACT-SQL(TSQL)를 사용하여 SQL 데이터 웨어하우스 데이터베이스를 만드는 방법을 보여줍니다.

## 시작하기 전에

이 문서의 단계를 완료하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- V12 논리 SQL server SQL 데이터 웨어하우스에 만들려면 V12 SQL server가 필요합니다. V12 논리 SQL 서버가 없는 경우 [Azure 포털에서 SQL 데이터 웨어하우스를 만드는 방법][] 문서의 **서버 구성 및 만들기**를 참조하세요.
- 있습니다. Visual Studio의 무료 버전은 [Visual Studio 다운로드][] 페이지를 참조하세요.


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

**MAXSIZE** 및 **SERVICE\_OBJECTIVE** 매개 변수는 데이터베이스가 디스크에서 사용할 수 있는 최대 공간과, 데이터 웨어하우스 인스턴스에 할당되는 계산 리소스를 지정합니다. 서비스 목표는 기본적으로 DWU의 크기에 따라 선형적으로 확장되는 CPU와 메모리를 할당 받습니다.

MAXSIZE는 250GB ~ 60TB 범위가 될 수 있습니다. 서비스 목표는 DW100 ~ DW2000 범위가 될 수 있습니다. MAXSIZE 및 SERVICE\_OBJECTIVE에 유효한 모든 값의 목록은 [CREATE DATABASE][]에 대한 MSDN 설명서를 참조하세요. MAXSIZE와 SERVICE\_OBJECTIVE 모두 [ALTER DATABASE][] T-SQL 명령으로 변경할 수 있습니다. SERVICE\_OBJECTIVE를 변경하면 서비스가 재시작되어 진행 중인 모든 쿼리가 취소될 수 있으므로 주의가 필요합니다. MAXSIZE 변경은 간단한 메타데이터 작업이므로 여기에 해당하지 않습니다.

## 다음 단계
SQL 데이터 웨어하우스에서 프로비전을 완료한 후 [샘플 데이터를 로드][]하거나 [개발][], [로드][] 또는 [마이그레이션][] 방법을 확인할 수 있습니다.

<!--Article references-->
[Azure 포털에서 SQL 데이터 웨어하우스를 만드는 방법]: sql-data-warehouse-get-started-provision.md
[Visual Studio로 SQL 데이터 웨어하우스에 연결]: sql-data-warehouse-get-started-connect.md
[마이그레이션]: sql-data-warehouse-overview-migrate.md
[개발]: sql-data-warehouse-overview-develop.md
[로드]: sql-data-warehouse-overview-load.md
[샘플 데이터를 로드]: sql-data-warehouse-get-started-manually-load-samples.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[SQL 데이터 웨어하우스 가격 책정]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Visual Studio 다운로드]: https://www.visualstudio.com/downloads/download-visual-studio-vs

<!---HONumber=AcomDC_0511_2016-->