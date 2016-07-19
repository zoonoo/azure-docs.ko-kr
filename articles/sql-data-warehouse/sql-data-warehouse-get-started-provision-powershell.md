<properties
   pageTitle="Powershell을 사용하여 SQL 데이터 웨어하우스 만들기 | Microsoft Azure"
   description="Powershell을 사용하여 SQL 데이터 웨어하우스 만들기"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/11/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Powershell을 사용하여 SQL 데이터 웨어하우스 만들기

> [AZURE.SELECTOR]
- [Azure 포털](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## 필수 조건
시작하기 전에 다음과 같은 필수 조건을 충족하는지 확인합니다.

- **Azure 계정**: [Azure 무료 평가판][] 또는 [MSDN Azure 크레딧][]을 참조하여 계정을 만듭니다.
- **V12 Azure SQL Server**: [Azure 포털을 사용하여 Azure SQL 데이터베이스 논리 서버 만들기][] 또는 [PowerShell을 사용하여 Azure SQL 데이터베이스 논리 서버 만들기][]를 참조하세요.
- **리소스 그룹 이름**: V12 Azure SQL 서버와 동일한 리소스 그룹을 사용하거나 [리소스 그룹][]을 참조하여 새 리소스 그룹을 만듭니다.
- **PowerShell 버전 1.0.3 이상**: **Get-Module -ListAvailable -Name Azure**를 실행하여 버전을 확인할 수 있습니다. 최신 버전은 [Microsoft 웹 플랫폼 설치 관리자][]를 통해 설치할 수 있습니다. 최신 버전 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법][]을 참조하세요.

> [AZURE.NOTE] 새 SQL 데이터 웨어하우스를 만들면 새로운 유료 서비스가 발생할 수 있습니다. 가격에 대한 자세한 내용은 [SQL 데이터 웨어하우스 가격 책정][]을 참조하세요.

## SQL 데이터 웨어하우스 만들기
1. Windows PowerShell을 엽니다.
2. 이 cmdlet을 실행하여 Azure 리소스 관리자에 로그인합니다.

	```Powershell
	Login-AzureRmAccount
	```
	
3. 현재 세션에 사용하려는 구독을 선택합니다.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

4.  데이터베이스를 만듭니다. 이 예제에서는 서비스 목표 수준이 "DW400”이고 이름이 "mynewsqldw"인 새 데이터베이스를 만들고, 리소스 그룹 "mywesteuroperesgp1"에 있는 이름이 "sqldwserver1"인 서버에 배치합니다.

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

이 cmdlet에 필요한 매개 변수 목록은 다음과 같습니다.

- **RequestedServiceObjectiveName**: 요청 중인 [DWU][]의 양입니다. 지원되는 값은 DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 및 DW6000입니다.
- **DatabaseName**: 만들려는 SQL 데이터 웨어하우스의 이름입니다.
- **ServerName**: 만들기에 사용하는 서버의 이름입니다(V12이어야 함).
- **ResourceGroupName**: 사용 중인 리소스 그룹입니다. 구독에서 사용 가능한 리소스 그룹을 찾으려면 Get-AzureResource를 사용합니다.
- **Edition**: SQL 데이터 웨어하우스를 만들 버전을 "DataWarehouse"로 설정해야 합니다.

매개 변수 옵션에 대한 자세한 내용은 [데이터베이스 만들기(Azure SQL 데이터 웨어하우스)][]를 참조하세요. 명령 참조는 [New-AzureRmSqlDatabase][]를 참조하세요.

## 다음 단계

SQL 데이터 웨어하우스에서 프로비전을 완료한 후 [샘플 데이터를 로드][]하거나 [개발][], [로드][] 또는 [마이그레이션][]을 시도할 수 있습니다.

SQL 데이터 웨어하우스를 프로그래밍 방식으로 관리하는 방법에 대한 자세한 내용은 [PowerShell cmdlet and REST API][] 사용 방법에 관한 문서를 참조하세요.

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[마이그레이션]: ./sql-data-warehouse-overview-migrate.md
[개발]: ./sql-data-warehouse-overview-develop.md
[로드]: ./sql-data-warehouse-load-with-bcp.md
[샘플 데이터를 로드]: ./sql-data-warehouse-get-started-load-sample-databases.md
[PowerShell cmdlet and REST API]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Azure PowerShell 설치 및 구성 방법]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Azure 포털을 사용하여 Azure SQL 데이터베이스 논리 서버 만들기]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[PowerShell을 사용하여 Azure SQL 데이터베이스 논리 서버 만들기]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[리소스 그룹]: ../azure-portal/resource-group-portal.md

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[데이터베이스 만들기(Azure SQL 데이터 웨어하우스)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft 웹 플랫폼 설치 관리자]: https://aka.ms/webpi-azps
[SQL 데이터 웨어하우스 가격 책정]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 무료 평가판]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure 크레딧]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0713_2016-->