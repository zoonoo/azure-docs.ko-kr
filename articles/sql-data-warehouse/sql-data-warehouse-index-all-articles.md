<properties
	pageTitle="SQL 데이터 웨어하우스 서비스에 대한 모든 항목 | Microsoft Azure"
	description="http://azure.microsoft.com/documentation/articles/에 있는 SQL 데이터 웨어하우스라는 Azure 서비스에 대한 모든 항목, 제목 및 설명에 대한 테이블입니다."
	services="sql-data-warehouse"
	documentationCenter=""
	authors="barbkess"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-data-warehouse"
	ms.workload="sql-data-warehouse"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="barbkess"/>


# Azure SQL 데이터 웨어하우스 서비스에 대한 모든 항목

이 항목에서는 Azure의 **SQL 데이터 웨어하우스** 서비스에 직접 적용되는 모든 항목을 나열합니다. 이 웹 페이지에서 **Ctrl+F**를 사용해 키워드를 검색하여 현재 관심 있는 항목을 찾을 수 있습니다.



## 업데이트된 문서

이 섹션에서는 최근에 대규모로 수행되거나 중요하게 업데이트된 문서가 목록으로 표시됩니다. 업데이트된 각 문서에 대해 추가된 markdown 텍스트의 대략적인 코드 조각이 표시됩니다. 날짜 범위 **2016-07-26**부터 **2016-08-21**까지 문서가 업데이트되었습니다.

| &nbsp; | 문서 | 업데이트된 텍스트, 코드 조각 |
| --: | :-- | :-- |
| 1 | [SQL 데이터 웨어하우스의 동시성 및 워크로드 관리](sql-data-warehouse-develop-concurrency.md) | **동시성 한도를 적용하는 쿼리** 대부분의 쿼리는 리소스 클래스에 의해 제어됩니다. 이러한 쿼리는 동시 쿼리 및 동시성 슬롯 임계값 둘 다를 벗어나지 않아야 합니다. 최종 사용자는 동시성 슬롯 모델에서 쿼리를 제외하도록 선택할 수 없습니다. 다시 말해서 다음 문은 리소스 클래스를 **적용**합니다. / INSERT-SELECT / UPDATE / DELETE / SELECT (when querying user tables) / ALTER INDEX REBUILD / ALTER INDEX REORGANIZE / ALTER TABLE REBUILD / CREATE INDEX / CREATE CLUSTERED COLUMNSTORE INDEX / CREATE TABLE AS SELECT (CTAS) / DMS(데이터 이동 서비스)에서 수행하는 데이터 이동 작업 ** 동시성 제한에 대한 쿼리 예외** |
| 2 | [프리미엄 저장소 세부 정보로 마이그레이션](sql-data-warehouse-migrate-to-premium-storage.md) | 프리미엄 저장소로 변경하여 데이터 웨어하우스의 기반 아키텍처에서 데이터베이스 blob 파일 수도 증가시켰습니다. 성능 문제가 발생하는 경우 아래 스크립트를 사용하여 클러스터형 Columnstore를 다시 빌드하는 것이 좋습니다. 그러면 일부 기존 데이터가 추가 blob에 적용됩니다. 아무 작업도 하지 않으면 자연스럽게 시간이 지나면서 데이터 웨어하우스 테이블에 더 많은 데이터를 로드함에 따라 데이터가 재배포됩니다. **필수 구성 요소:** 1. 데이터 웨어하우스를 1,000 DWU 이상으로 실행해야 합니다(계산 능력 크기 조정). 2. 스크립트를 실행하는 사용자가 mediumrc 역할 이상이어야 합니다. 1. 이 역할에 사용자를 추가하려면 다음을 실행합니다. 1. ````EXEC sp_addrolemember 'xlargerc', 'MyUser'```` ````sql /------------------------------------------------------------------------------ /- Step 1: Create Table to control Index Rebuild /- Run as user in mediumrc or higher /------------------------------------------------------------ |





## 시작

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 3 | [Azure SQL 데이터 웨어하우스에 대한 인증](sql-data-warehouse-authentication.md) | Azure SQL 데이터 웨어하우스에 대한 AAD(Azure Active Directory) 및 SQL Server 인증 |
| 4 | [Azure SQL 데이터 웨어하우스에 대한 모범 사례](sql-data-warehouse-best-practices.md) | Azure SQL 데이터 웨어하우스에 대한 솔루션을 개발하면서 알아야 할 권장 사항 및 모범 사례입니다. 이 내용은 성공적인 개발에 도움이 됩니다. |
| 5 | [Azure SQL 데이터 웨어하우스용 드라이버](sql-data-warehouse-connection-strings.md) | SQL 데이터 웨어하우스용 드라이버 및 연결 문자열 |
| 6 | [Azure SQL 데이터 웨어하우스에 연결](sql-data-warehouse-connect-overview.md) | Azure SQL 데이터 웨어하우스에 연결에 대한 연결 개요 |
| 7 | [Azure 기계 학습을 사용하여 데이터 분석](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) | Azure 기계 학습을 사용하여 Azure SQL 데이터 웨어하우스에 저장된 데이터를 기반으로 예측 기계 학습 모델을 구축합니다. |
| 8 | [Azure SQL 데이터 웨어하우스 쿼리(sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) | sqlcmd 명령줄 유틸리티를 사용하여 Azure SQL 데이터 웨어하우스를 쿼리합니다. |
| 9 | [TRANSACT-SQL(TSQL)를 사용하여 SQL 데이터 웨어하우스 데이터베이스 만들기](sql-data-warehouse-get-started-create-database-tsql.md) | TSQL를 사용하여 SQL 데이터 웨어하우스를 만드는 방법을 알아봅니다. |
| 10 | [SQL 데이터 웨어하우스에 대한 지원 티켓을 만드는 방법](sql-data-warehouse-get-started-create-support-ticket.md) | Azure SQL 데이터 웨어하우스에서 지원 티켓을 만드는 방법 |
| 11 | [Azure Data Factory를 사용하여 데이터 로드](sql-data-warehouse-get-started-load-with-azure-data-factory.md) | Azure Data Factory를 사용하여 데이터를 로드하는 방법을 알아보세요. |
| 12 | [SQL 데이터 웨어하우스에서 PolyBase를 사용하여 데이터 로드](sql-data-warehouse-get-started-load-with-polybase.md) | PolyBase 정의 및 데이터 웨어하우징 시나리오에 대해 사용하는 방법에 대해 알아봅니다. |
| 13 | [새로운 논리 서버 만들기](sql-data-warehouse-get-started-new-server.md) | Azure 포털에서 SQL 데이터 웨어하우스를 만드는 방법을 알아봅니다. |
| 14 | [Azure SQL 데이터 웨어하우스 만들기](sql-data-warehouse-get-started-provision.md) | Azure 포털에서 SQL 데이터 웨어하우스를 만드는 방법을 알아봅니다. |
| 15 | [PowerShell을 사용하여 SQL 데이터 웨어하우스 만들기](sql-data-warehouse-get-started-provision-powershell.md) | PowerShell을 사용하여 SQL 데이터 웨어하우스 만들기 |
| 16 | [Power BI를 사용하여 데이터 시각화](sql-data-warehouse-get-started-visualize-with-power-bi.md) | Power BI로 SQL 데이터 웨어하우스 데이터 시각화 |
| 17 | [Azure SQL 데이터 웨어하우스 쿼리(Visual Studio)](sql-data-warehouse-query-visual-studio.md) | Visual Studio를 사용하여 SQL 데이터 웨어하우스를 쿼리합니다. |



## 개발

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 18 | [SQL 데이터 웨어하우스에 대해 트랜잭션 최적화](sql-data-warehouse-develop-best-practices-transactions.md) | Azure SQL 데이터 웨어하우스에서 효율적인 트랜잭션 업데이트를 작성하는 모범 사례 가이드 |
| 19 | [SQL 데이터 웨어하우스의 동시성 및 워크로드 관리](sql-data-warehouse-develop-concurrency.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 동시성 및 워크로드 관리를 이해합니다. |
| 20 | [SQL 데이터 웨어하우스의 CTAS(Create Table As Select)](sql-data-warehouse-develop-ctas.md) | 솔루션 개발을 위해 Azure SQL 데이터 웨어하우스의 CTAS(Create Table As Select) 문으로 코딩에 대한 팁. |
| 21 | [SQL 데이터 웨어하우스의 동적 SQL](sql-data-warehouse-develop-dynamic-sql.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 동적 SQL 사용 팁. |
| 22 | [SQL 데이터 웨어하우스의 GROUP BY 옵션](sql-data-warehouse-develop-group-by-options.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 GROUP BY 옵션 구현을 위한 팁. |
| 23 | [SQL 데이터 웨어하우스에서 레이블을 사용하여 쿼리 계측](sql-data-warehouse-develop-label.md) | 솔루션 개발을 위해 Azure SQL 데이터 웨어하우스에서 레이블을 사용하여 쿼리를 계측하기 위한 팁. |
| 24 | [SQL 데이터 웨어하우스의 루프](sql-data-warehouse-develop-loops.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 Transact-SQL 루프 및 커서 대체를 위한 팁. |
| 25 | [SQL 데이터 웨어하우스의 저장된 프로시저](sql-data-warehouse-develop-stored-procedures.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 저장 프로시저 구현을 위한 팁 |
| 26 | [SQL 데이터 웨어하우스의 트랜잭션](sql-data-warehouse-develop-transactions.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 트랜잭션 구현을 위한 팁 |
| 27 | [SQL 데이터 웨어하우스의 사용자 정의 스키마](sql-data-warehouse-develop-user-defined-schemas.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 Transact-SQL 스키마 사용을 위한 팁 |
| 28 | [SQL 데이터 웨어하우스의 변수 할당](sql-data-warehouse-develop-variable-assignment.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 Transact-SQL 변수 할당을 위한 팁 |
| 29 | [SQL 데이터 웨어하우스의 뷰](sql-data-warehouse-develop-views.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 Transact-SQL 뷰 사용을 위한 팁 |
| 30 | [SQL 데이터 웨어하우스에 대한 디자인 결정 및 코딩 기술](sql-data-warehouse-overview-develop.md) | SQL 데이터 웨어하우스에 대한 개발 개념, 디자인 결정, 권장 사항 및 코딩 기술입니다. |



## 관리

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 31 | [Azure SQL 데이터 웨어하우스의 계산 능력 관리(개요)](sql-data-warehouse-manage-compute-overview.md) | Azure SQL 데이터 웨어하우스의 성능 확장 기능입니다. 또는 DWU를 조정하거나 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다. |
| 32 | [Azure SQL 데이터 웨어하우스의 계산 능력 관리(Azure 포털)](sql-data-warehouse-manage-compute-portal.md) | 계산 능력을 관리하는 Azure 포털 작업 DWU를 조정하여 계산 리소스 크기를 조정합니다. 또는 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다. |
| 33 | [Azure SQL 데이터 웨어하우스의 계산 능력 관리(PowerShell)](sql-data-warehouse-manage-compute-powershell.md) | 계산 능력을 관리하는 PowerShell 작업 DWU를 조정하여 계산 리소스 크기를 조정합니다. 또는 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다. |
| 34 | [Azure SQL 데이터 웨어하우스의 계산 능력 관리(REST)](sql-data-warehouse-manage-compute-rest-api.md) | 계산 능력을 관리하는 PowerShell 작업 DWU를 조정하여 계산 리소스 크기를 조정합니다. 또는 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다. |
| 35 | [Azure SQL 데이터 웨어하우스의 계산 능력 관리(T-SQL)](sql-data-warehouse-manage-compute-tsql.md) | DWU를 조정하여 성능을 확장하는 Transact-SQL (T-SQL) 작업입니다. 사용량이 많지 않은 시간 동안 다시 조정하여 비용을 절감합니다. |
| 36 | [DMV를 사용하여 작업 모니터링](sql-data-warehouse-manage-monitor.md) | DMV를 사용하여 작업을 모니터링하는 방법을 알아봅니다. |
| 37 | [Azure SQL 데이터 웨어하우스의 데이터베이스 관리](sql-data-warehouse-overview-manage.md) | SQL 데이터 웨어하우스 데이터베이스 관리 개요. 관리 도구, DWU 및 성능 확장, 쿼리 성능 문제 해결, 보안 정책 설정, 데이터 손상 또는 지역적 중단으로부터 데이터베이스 복원 등이 포함되어 있습니다. |
| 38 | [Azure SQL 데이터 웨어하우스의 사용자 쿼리 모니터링](sql-data-warehouse-overview-manage-user-queries.md) | 고려 사항, 모범 사례 및 Azure SQL 데이터 웨어하우스의 사용자 쿼리 모니터링 작업 개요입니다. |
| 39 | [Azure SQL 데이터 웨어하우스 복원(개요)](sql-data-warehouse-restore-database-overview.md) | Azure SQL 데이터 웨어하우스의 데이터베이스를 복구하기 위한 데이터베이스 복원 옵션 개요입니다. |
| 40 | [Azure SQL 데이터 웨어하우스 복원(포털)](sql-data-warehouse-restore-database-portal.md) | Azure SQL 데이터 웨어하우스 복원을 위한 Azure 포털 작업. |
| 41 | [Azure SQL 데이터 웨어하우스 복원(PowerShell)](sql-data-warehouse-restore-database-powershell.md) | Azure SQL 데이터 웨어하우스 복원을 위한 PowerShell 작업. |
| 42 | [Azure SQL 데이터 웨어하우스 복원(REST API)](sql-data-warehouse-restore-database-rest-api.md) | Azure SQL 데이터 웨어하우스 복원을 위한 REST API 작업. |



## 테이블 및 인덱스

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 43 | [SQL 데이터 웨어하우스 테이블의 데이터 형식](sql-data-warehouse-tables-data-types.md) | Azure SQL 데이터 웨어하우스 테이블에 대한 데이터 형식으로 시작 |
| 44 | [SQL 데이터 웨어하우스의 테이블 배포](sql-data-warehouse-tables-distribute.md) | Azure SQL 데이터 웨어하우스에서 테이블 배포 시작 |
| 45 | [SQL 데이터 웨어하우스의 테이블 인덱싱](sql-data-warehouse-tables-index.md) | Azure SQL 데이터 웨어하우스에서 테이블 인덱싱 시작 |
| 46 | [SQL 데이터 웨어하우스의 테이블 개요](sql-data-warehouse-tables-overview.md) | Azure SQL 데이터 웨어하우스 테이블로 시작 |
| 47 | [SQL 데이터 웨어하우스의 테이블 분할](sql-data-warehouse-tables-partition.md) | Azure SQL 데이터 웨어하우스에서 테이블 분할 시작 |
| 48 | [SQL 데이터 웨어하우스의 테이블에 대한 통계 관리](sql-data-warehouse-tables-statistics.md) | Azure SQL 데이터 웨어하우스에서 테이블에 대한 통계 시작 |
| 49 | [SQL 데이터 웨어하우스의 임시 테이블](sql-data-warehouse-tables-temporary.md) | Azure SQL 데이터 웨어하우스에서 임시 테이블로 시작 |



## 통합

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 50 | [SQL 데이터 웨어하우스와 함께 Azure 데이터 팩터리 사용](sql-data-warehouse-integrate-azure-data-factory.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 Azure 데이터 팩터리(ADF) 사용을 위한 팁 |
| 51 | [SQL 데이터 웨어하우스와 함께 Azure 기계 학습 사용](sql-data-warehouse-integrate-azure-machine-learning.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스와 함께 Azure 기계 학습 사용을 위한 팁 |
| 52 | [SQL 데이터 웨어하우스와 함께 Azure 스트림 분석 사용](sql-data-warehouse-integrate-azure-stream-analytics.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스와 함께 Azure 스트림 분석 사용을 위한 팁 |
| 53 | [SQL 데이터 웨어하우스와 함께 Power BI 사용](sql-data-warehouse-integrate-power-bi.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스와 함께 Power BI 사용을 위한 팁 |
| 54 | [SQL 데이터 웨어하우스와 함께 기타 서비스 활용](sql-data-warehouse-overview-integrate.md) | SQL 데이터 웨어하우스와 통합된 솔루션과 파트너 및 도구 |



## 로드

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 55 | [Azure Blob 저장소에서 Azure SQL 데이터 웨어하우스로 데이터 로드(Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) | Azure Data Factory를 사용하여 데이터를 로드하는 방법을 알아보세요. |
| 56 | [Azure blob 저장소에서 SQL 데이터 웨어하우스로 데이터를 로드합니다(PolyBase).](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | PolyBase를 사용하여 Azure blob 저장소에서 SQL 데이터 웨어하우스로 데이터를 로드하는 방법을 알아봅니다. 몇 개의 테이블을 공용 데이터에서 Contoso 소매 데이터 웨어하우스 스키마로 로드합니다. |
| 57 | [SQL Server에서 Azure SQL 데이터 웨어하우스로 데이터 로드(AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) | bcp를 사용하여 SQL Server에서 플랫 파일로 데이터를 내보내고 AZCopy를 사용하여 Azure Blob 저장소로 데이터를 가져오고, PolyBase를 사용하여 Azure SQL 데이터 웨어하우스로 데이터를 수집합니다. |
| 58 | [SQL Server에서 Azure SQL 데이터 웨어하우스로 데이터 로드(플랫 파일)](sql-data-warehouse-load-from-sql-server-with-bcp.md) | 데이터의 크기가 작으면, bcp를 사용하여 SQL Server의 데이터를 플랫 파일로 내보내고 Azure SQL 데이터 웨어하우스로 데이터를 직접 가져옵니다. |
| 59 | [SQL Server에서 Azure SQL 데이터 웨어하우스로 데이터 로드(SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) | SSIS(SQL Server Integration Services) 패키지를 만들어 다양한 데이터 원본에서 SQL 데이터 웨어하우스로 데이터를 이동하는 방법을 보여줍니다. |
| 60 | [SQL 데이터 웨어하우스에서 PolyBase를 사용하여 데이터 로드](sql-data-warehouse-load-from-sql-server-with-polybase.md) | bcp를 사용하여 SQL Server에서 플랫 파일로 데이터를 내보내고 AZCopy를 사용하여 Azure Blob 저장소로 데이터를 가져오고, PolyBase를 사용하여 Azure SQL 데이터 웨어하우스로 데이터를 수집합니다. |
| 61 | [SQL 데이터 웨어하우스의 PolyBase 사용을 위한 가이드](sql-data-warehouse-load-polybase-guide.md) | SQL 데이터 웨어하우스 시나리오에서 PolyBase를 사용하는 방법에 대한 지침 및 권장 사항입니다. |
| 62 | [SQL 데이터 웨어하우스로 샘플 데이터를 로드](sql-data-warehouse-load-sample-databases.md) | SQL 데이터 웨어하우스로 샘플 데이터를 로드 |
| 63 | [bcp를 사용하여 데이터 로드](sql-data-warehouse-load-with-bcp.md) | bcp 정의 및 데이터 웨어하우징 시나리오에 대해 사용하는 방법에 대해 알아봅니다. |
| 64 | [Azure SQL 데이터 웨어하우스에 데이터 로드](sql-data-warehouse-overview-load.md) | SQL 데이터 웨어하우스에 데이터 로드를 위한 일반적인 시나리오에 대해 알아봅니다. 여기에는 PolyBase, Azure Blob 저장소, 플랫 파일 및 디스크 배송 사용이 포함됩니다. 타사 도구를 사용할 수도 있습니다. |



## 마이그레이션

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 65 | [SQL 데이터 웨어하우스에 SQL 코드 마이그레이션](sql-data-warehouse-migrate-code.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스로 SQL 코드를 마이그레이션하기 위한 팁 |
| 66 | [데이터 마이그레이션](sql-data-warehouse-migrate-data.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스로 데이터를 마이그레이션하기 위한 팁 |
| 67 | [데이터 웨어하우스 마이그레이션 유틸리티(미리 보기)](sql-data-warehouse-migrate-migration-utility.md) | SQL 데이터 웨어하우스로 마이그레이션합니다. |
| 68 | [SQL 데이터 웨어하우스로 스키마 마이그레이션](sql-data-warehouse-migrate-schema.md) | 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스로 스키마를 마이그레이션하기 위한 팁 |
| 69 | [SQL 데이터 웨어하우스에 솔루션 마이그레이션](sql-data-warehouse-overview-migrate.md) | Azure SQL 데이터 웨어하우스 플랫폼에 솔루션을 가져오기 위한 마이그레이션 지침 |



## 파트너

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 70 | [SQL 데이터 웨어하우스 비즈니스 인텔리전스 파트너](sql-data-warehouse-partner-business-intelligence.md) | SQL 데이터 웨어하우스를 지원하는 솔루션을 제공하는 타사 비즈니스 인텔리전스 파트너 목록 |
| 71 | [SQL 데이터 웨어하우스 데이터 통합 파트너](sql-data-warehouse-partner-data-integration.md) | Azure SQL 데이터 웨어하우스를 지원하는 데이터 통합 솔루션을 제공하는 타사 파트너 목록 |
| 72 | [SQL 데이터 웨어하우스 데이터 관리 파트너](sql-data-warehouse-partner-data-management.md) | SQL 데이터 웨어하우스를 지원하는 솔루션을 제공하는 타사 데이터 관리 파트너 목록 |



## 참조

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 73 | [SQL 데이터 웨어하우스에 대한 참조 항목](sql-data-warehouse-overview-reference.md) | SQL 데이터 웨어하우스에 대한 참조 콘텐츠 링크 |
| 74 | [SQL 데이터 웨어하우스용 PowerShell cmdlet 및 REST API](sql-data-warehouse-reference-powershell-cmdlets.md) | 데이터베이스를 일시 중지하고 다시 시작하는 방법을 포함하여 Azure SQL 데이터 웨어하우스용 PowerShell cmdlet을 확인합니다. |
| 75 | [언어 요소](sql-data-warehouse-reference-tsql-language-elements.md) | SQL 데이터 웨어하우스에 사용되는 TRANSACT-SQL 언어 요소에 대한 참조 내용에 대한 링크 목록입니다. |
| 76 | [Transact-SQL 항목](sql-data-warehouse-reference-tsql-statements.md) | SQL 데이터 웨어하우스에 의해 사용되는 TRANSACT-SQL 항목에 대한 참조 내용에 대한 링크 |
| 77 | [시스템 뷰](sql-data-warehouse-reference-tsql-system-views.md) | SQL 데이터 웨어하우스에 대한 시스템 보기 콘텐츠에 연결하는 링크. |



## 보안

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 78 | [SQL 데이터 웨어하우스 - 감사 및 동적 데이터 마스킹에 대한 하위 수준 클라이언트 지원](sql-data-warehouse-auditing-downlevel-clients.md) | 데이터 감사에 대한 SQL 데이터 웨어하우스 하위 수준 클라이언트 지원에 대해 알아보기 |
| 79 | [Azure SQL 데이터 웨어하우스 감사](sql-data-warehouse-auditing-overview.md) | Azure SQL 데이터 웨어하우스 감사 시작 |
| 80 | [SQL 데이터 웨어하우스에서 투명한 데이터 암호화(TDE) 시작](sql-data-warehouse-encryption-tde.md) | SQL 데이터 웨어하우스에서 투명한 데이터 암호화(TDE) 시작 |
| 81 | [투명한 데이터 암호화(TDE) 시작](sql-data-warehouse-encryption-tde-tsql.md) | SQL 데이터 웨어하우스 투명한 데이터 암호화(TDE) TSQL 시작 |
| 82 | [SQL 데이터 웨어하우스에서 데이터베이스 보호](sql-data-warehouse-overview-manage-security.md) | 솔루션 개발을 위해 Azure SQL 데이터 웨어하우스에서 데이터베이스를 보호하는 팁 |



## 기타

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 83 | [SQL 데이터 웨어하우스용 Visual Studio 2015 및 SSDT 설치](sql-data-warehouse-install-visual-studio.md) | Azure SQL 데이터 웨어하우스용 Visual Studio 및 SSDT(SQL Server 개발 도구) 설치 |
| 84 | [프리미엄 저장소 세부 정보로 마이그레이션](sql-data-warehouse-migrate-to-premium-storage.md) | 기존 SQL 데이터 웨어하우스를 프리미엄 저장소로 마이그레이션하기 위한 지침 |
| 85 | [위협 감지 시작](sql-data-warehouse-security-threat-detection.md) | 위협 감지를 시작하는 시기 |
| 86 | [SQL 데이터 웨어하우스 용량 제한](sql-data-warehouse-service-capacity-limits.md) | SQL 데이터 웨어하우스의 연결, 데이터베이스, 테이블 및 쿼리에 대한 최대값입니다. |
| 87 | [Azure SQL 데이터 웨어하우스 문제 해결](sql-data-warehouse-troubleshoot.md) | Azure SQL 데이터 웨어하우스 문제 해결 |

<!---HONumber=AcomDC_0824_2016-->