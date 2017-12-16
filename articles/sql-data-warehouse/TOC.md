# 개요

## [SQL Data Warehouse 정보](sql-data-warehouse-overview-what-is.md)

# 빠른 시작

## [만들기 및 연결 - 포털](create-data-warehouse-portal.md)

# 자습서
## [1 - blob에서 데이터 로드](load-data-from-azure-blob-storage-using-polybase.md)

# 개념
## 서비스 기능
### [MPP 아키텍처](massively-parallel-processing-mpp-architecture.md)
### [성능 계층](performance-tiers.md)
### [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [데이터 웨어하우스 백업](sql-data-warehouse-backups.md)
### [감사](sql-data-warehouse-auditing-overview.md)
### [용량 제한](sql-data-warehouse-service-capacity-limits.md)
### [FAQ](sql-data-warehouse-overview-faq.md)

## 보안
### [개요](sql-data-warehouse-overview-manage-security.md)
### [인증](sql-data-warehouse-authentication.md)


## SQL Data Warehouse로 마이그레이션
### [개요](sql-data-warehouse-overview-migrate.md)
### [마이그레이션 스키마](sql-data-warehouse-migrate-schema.md)
### [코드 마이그레이션](sql-data-warehouse-migrate-code.md)
### [데이터 마이그레이션](sql-data-warehouse-migrate-data.md)

## 데이터 로드 및 이동
### [개요](design-elt-data-loading.md)
### [지침](guidance-for-loading-data.md)


## 통합
### [개요](sql-data-warehouse-overview-integrate.md)
### [SQL Database 탄력적 쿼리](how-to-use-elastic-query-with-sql-data-warehouse.md)


## 모니터링 및 튜닝
### [지침](resource-classes-for-workload-management.md)
### [Columnstore 압축](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [모니터](sql-data-warehouse-manage-monitor.md)
### [문제 해결](sql-data-warehouse-troubleshoot.md)

## 데이터 웨어하우스 개발
### [개요](sql-data-warehouse-overview-develop.md)
### [데이터 웨어하우스 구성 요소](sql-data-warehouse-overview-workload.md)

### 테이블
#### [개요](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [데이터 형식](sql-data-warehouse-tables-data-types.md)
#### [분산 테이블](sql-data-warehouse-tables-distribute.md)
#### [인덱스](sql-data-warehouse-tables-index.md)
#### [ID](sql-data-warehouse-tables-identity.md)
#### [파티션](sql-data-warehouse-tables-partition.md)
#### [복제된 테이블](design-guidance-for-replicated-tables.md)
#### [통계](sql-data-warehouse-tables-statistics.md)
#### [임시](sql-data-warehouse-tables-temporary.md)

### 쿼리
#### [동적 SQL](sql-data-warehouse-develop-dynamic-sql.md)
#### [옵션으로 그룹화](sql-data-warehouse-develop-group-by-options.md)
#### [레이블](sql-data-warehouse-develop-label.md)

### T-SQL 언어 요소
#### [루프](sql-data-warehouse-develop-loops.md)
#### [저장 프로시저](sql-data-warehouse-develop-stored-procedures.md)
#### [트랜잭션](sql-data-warehouse-develop-transactions.md)
#### [트랜잭션 모범 사례](sql-data-warehouse-develop-best-practices-transactions.md)
#### [사용자 정의 스키마](sql-data-warehouse-develop-user-defined-schemas.md)
#### [변수 할당](sql-data-warehouse-develop-variable-assignment.md)
#### [뷰](sql-data-warehouse-develop-views.md)

# 방법 가이드
## 서비스 기능
### [데이터 웨어하우스 복원 - 포털](sql-data-warehouse-restore-database-portal.md)
### [데이터 웨어하우스 복원 - PowerShell](sql-data-warehouse-restore-database-powershell.md)
### [데이터 웨어하우스 복원 - REST API](sql-data-warehouse-restore-database-rest-api.md)

## 보안
### [암호화 사용 - 포털](sql-data-warehouse-encryption-tde.md)
### [암호화 사용 - T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [위협 감지](sql-data-warehouse-security-threat-detection.md)


## 데이터 로드 및 이동
### [AdventureWorks](sql-data-warehouse-load-sample-databases.md)
### [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [Blob 저장소에서 PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [SQL server에서 PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)


## 통합
### [SQL Database 탄력적 쿼리 구성](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
### [Azure Stream Analytics 작업 추가](sql-data-warehouse-integrate-azure-stream-analytics.md)
### [Machine Learning 사용](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI를 사용하여 시각화](sql-data-warehouse-get-started-visualize-with-power-bi.md)

## 모니터링 및 튜닝
### [워크로드 분석](analyze-your-workload.md)

## 확장
### [계산 관리 - 포털](sql-data-warehouse-manage-compute-portal.md)
### [계산 관리 - PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [계산 관리 - REST API](sql-data-warehouse-manage-compute-rest-api.md)
### [계산 수준 자동화](manage-compute-with-azure-functions.md)


# 참조


## T-SQL
### [전체 참조](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [SQL DW 언어 요소](sql-data-warehouse-reference-tsql-language-elements.md)
### [SQL DW 문](sql-data-warehouse-reference-tsql-statements.md)
## [시스템 뷰](sql-data-warehouse-reference-tsql-system-views.md)
## [PowerShell cmdlet](sql-data-warehouse-reference-powershell-cmdlets.md)

# 리소스
## [Azure 로드맵](https://azure.microsoft.com/roadmap/?category=databases)
## [포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [가격](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [요금 계산기](https://azure.microsoft.com/pricing/calculator/)
## [기능 요청](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [지원](sql-data-warehouse-get-started-create-support-ticket.md)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## 파트너
### [비즈니스 인텔리전스](sql-data-warehouse-partner-business-intelligence.md)
### [데이터 통합](sql-data-warehouse-partner-data-integration.md)
### [데이터 관리](sql-data-warehouse-partner-data-management.md)
