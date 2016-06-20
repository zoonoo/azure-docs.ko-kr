<properties
   pageTitle="Azure SQL 데이터베이스 솔루션 빠른 시작 | Microsoft Azure"
   description="Azure SQL 데이터베이스 솔루션에 대한 자세한 정보"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="06/01/2016"
   ms.author="carlrab"/>

# Azure SQL 데이터베이스 솔루션 빠른 시작 탐색

이 문서에서는 Azure SQL 데이터베이스 솔루션 빠른 시작에 대해 대략적으로 설명합니다. 이러한 빠른 시작 설명서는 실제 시나리오를 기준으로 완전한 솔루션에서 SQL 데이터베이스를 사용하는 방법을 보여 줍니다. 특정 Azure SQL 데이터베이스 기능 사용을 보여 주는 간단한 단계별 자습서를 보려면 [Azure SQL 데이터베이스 자습서 탐색](sql-database-explore-tutorials.md)을 참조하세요.

## 여러 풀에 걸친 리소스 사용 데이터 수집 및 모니터링

이 솔루션 빠른 시작에서는 구독의 여러 풀에 걸쳐 Azure SQL 데이터베이스 리소스 사용량을 수집 및 모니터링하기 위한 솔루션을 제공합니다. 구독에 데이터베이스가 많은 경우 각 탄력적 풀을 개별적으로 모니터링하는 작업은 번거롭습니다. 이 문제를 해결하기 위해 리소스 사용량을 모니터링하고 분석하기 위해 여러 풀 및 해당 데이터베이스에서 리소스 사용 데이터를 수집하기 위해 SQL 데이터베이스 PowerShell cmdlet 및 T-SQL 쿼리를 결합할 수 있습니다.

GitHub SQL Server 예제 리포지토리의 [PowerShell 및 Power BI를 사용하여 SQL 데이터베이스에서 여러 탄력적 풀 관리](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)에서는 powershell 스크립트 및 T-SQL 쿼리 집합과 이러한 기능이 수행하는 작업 및 사용 방법에 대한 설명서를 제공합니다.

## SaaS 시나리오에서 탄력적 풀을 사용하여 시작

이 솔루션 빠른 시작 설명서는 탄력적 풀을 활용하여 SaaS 응용 프로그램의 비용 효과적이고 확장 가능한 데이터베이스 백 엔드를 제공하는 SaaS(Softwware-as-a-Solution) 시나리오에 대한 솔루션을 제공합니다. 이 솔루션에서는 Azure 포털을 보완하는 사용자 지정 대시보드를 사용하여 부하 생성기가 탄력적 풀에서 생성하는 부하를 시각화할 수 있는 웹앱 구현 과정을 안내합니다.

GitHub SQL Server 샘플 리포지토리의 [Saas-scenario-with-elastic-pool](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)에서는 부하 생성기와 모니터링 웹앱 및 이러한 기능이 수행하는 작업과 사용 방법에 대한 설명서를 제공합니다.

## 다음 단계

[Azure SQL 데이터베이스 자습서 탐색](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0608_2016-->