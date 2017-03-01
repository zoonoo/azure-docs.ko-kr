# 개요

## [SQL Data Warehouse란 무엇입니까?](sql-data-warehouse-overview-what-is.md)
## [데이터 웨어하우스 워크로드](sql-data-warehouse-overview-workload.md)
## [분선 데이터](sql-data-warehouse-distributed-data.md)

# 시작

## [초보자를 위한 자습서](sql-data-warehouse-get-started-tutorial.md)
## [모범 사례](sql-data-warehouse-best-practices.md)
## [관리](sql-data-warehouse-overview-manage.md)
## [지원 받기](sql-data-warehouse-get-started-create-support-ticket.md)

# 방법

## 백업 및 복원

### [Backup 개요](sql-data-warehouse-backups.md)
### [복원 개요](sql-data-warehouse-restore-database-overview.md)
#### [Azure Portal](sql-data-warehouse-restore-database-portal.md)
#### [PowerShell](sql-data-warehouse-restore-database-powershell.md)
#### [REST](sql-data-warehouse-restore-database-rest-api.md)

## 연결

### [개요](sql-data-warehouse-connect-overview.md)
### [연결 문자열](sql-data-warehouse-connection-strings.md)
### [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
### [SSMS](sql-data-warehouse-query-ssms.md)
### [Visual Studio](sql-data-warehouse-query-visual-studio.md)
### [Visual Studio 설치](sql-data-warehouse-install-visual-studio.md)

## 생성
### [Azure Portal](sql-data-warehouse-get-started-provision.md)
### [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
### [T-SQL](sql-data-warehouse-get-started-create-database-tsql.md)

## 개발

### [개요](sql-data-warehouse-overview-develop.md)

### 테이블

#### [개요](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [데이터 형식](sql-data-warehouse-tables-data-types.md)
#### [분산 테이블](sql-data-warehouse-tables-distribute.md)
#### [인덱스](sql-data-warehouse-tables-index.md)
#### [파티션](sql-data-warehouse-tables-partition.md)
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

## 통합

### [개요](sql-data-warehouse-overview-integrate.md)
### [데이터 팩터리](sql-data-warehouse-integrate-azure-data-factory.md)
### [기계 학습](sql-data-warehouse-integrate-azure-machine-learning.md)
### [Machine Learning 자습서](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI](sql-data-warehouse-integrate-power-bi.md)
### [Power BI 시각화](sql-data-warehouse-get-started-visualize-with-power-bi.md)
### [스트림 분석](sql-data-warehouse-integrate-azure-stream-analytics.md)

## 로드

### [개요](sql-data-warehouse-overview-load.md)
### [샘플 데이터](sql-data-warehouse-load-sample-databases.md)
### [Azure 데이터 레이크 저장소](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [AZCopy](sql-data-warehouse-load-from-sql-server-with-azcopy.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [데이터 팩터리](sql-data-warehouse-load-with-data-factory.md)
### [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
### [PolyBase 지침](sql-data-warehouse-load-polybase-guide.md)
### [Blob 저장소에서 PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [SQL server에서 PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)

## 마이그레이션

### [개요](sql-data-warehouse-overview-migrate.md)
### [마이그레이션 유틸리티](sql-data-warehouse-migrate-migration-utility.md)
### [마이그레이션 스키마](sql-data-warehouse-migrate-schema.md)
### [코드 마이그레이션](sql-data-warehouse-migrate-code.md)
### [데이터 마이그레이션](sql-data-warehouse-migrate-data.md)
### [프리미엄 저장소로 마이그레이션](sql-data-warehouse-migrate-to-premium-storage.md)

## 일시 중지 및 확장

### [개요](sql-data-warehouse-manage-compute-overview.md)
### [쉬운 테이블](sql-data-warehouse-manage-compute-portal.md)
### [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [REST API](sql-data-warehouse-manage-compute-rest-api.md)
### [T-SQL](sql-data-warehouse-manage-compute-tsql.md)

## 성능

### [개요](sql-data-warehouse-overview-manage-user-queries.md)
### [Columnstore 압축](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [쿼리](sql-data-warehouse-manage-monitor.md)
### [워크로드](sql-data-warehouse-develop-concurrency.md)
### [문제 해결](sql-data-warehouse-troubleshoot.md)

## 보안

### [개요](sql-data-warehouse-overview-manage-security.md)
### [감사](sql-data-warehouse-auditing-overview.md)
### [하위 수준 클라이언트에 대한 감사](sql-data-warehouse-auditing-downlevel-clients.md)
### [인증](sql-data-warehouse-authentication.md)
### [암호화](sql-data-warehouse-encryption-tde.md)
### [T-SQL을 사용하여 암호화](sql-data-warehouse-encryption-tde-tsql.md)
### [위협 감지](sql-data-warehouse-security-threat-detection.md)

# 참조

## [용량 제한](sql-data-warehouse-service-capacity-limits.md)
## [T-SQL 언어 요소](sql-data-warehouse-reference-tsql-language-elements.md)
## [T-SQL 문](sql-data-warehouse-reference-tsql-statements.md)
## [T-SQL 시스템 뷰](sql-data-warehouse-reference-tsql-system-views.md)
## [PowerShell cmdlet](sql-data-warehouse-reference-powershell-cmdlets.md)

# 리소스
## [가격 책정](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## 파트너
### [비즈니스 인텔리전스](sql-data-warehouse-partner-business-intelligence.md)
### [데이터 통합](sql-data-warehouse-partner-data-integration.md)
### [데이터 관리](sql-data-warehouse-partner-data-management.md)
