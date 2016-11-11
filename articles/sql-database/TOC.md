# 개요
## [SQL 데이터베이스 정의](sql-database-technical-overview.md)
## 기능
### [서비스 계층](sql-database-service-tiers.md)
### [DTU란?](sql-database-what-is-a-dtu.md)
### [DTU 벤치마크 개요](sql-database-benchmark-overview.md)
### [SQL Database 방화벽 및 방화벽 규칙](sql-database-firewall-configure.md)
### [관리 도구](sql-database-manage-overview.md)
## 고려 사항 및 제한 사항
### [VM의 SQL Database 및 SQL](sql-database-paas-vs-sql-server-iaas.md)
### [T-SQL 차이점](sql-database-transact-sql-information.md)
### [리소스 한도](sql-database-resource-limits.md)
### [일반적인 제한 사항](sql-database-general-limitations.md)
## [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)
## [새로운 기능](https://azure.microsoft.com/updates/?service=sql-database)
## [SQL 데이터베이스 FAQ](sql-database-faq.md)

## 이점
### [학습 및 적응](sql-database-learn-and-adapt.md)
### [즉석에서 확장](sql-database-scale-on-the-fly.md)
### [다중 테넌트 앱 빌드](sql-database-build-multi-tenant-apps.md)
### [보안 및 보호](sql-database-helps-secures-and-protects.md)
### [사용자 환경에서 작동](sql-database-works-in-your-environment.md)

## 시나리오
### 서버, 풀, 데이터베이스 및 방화벽 만들기 및 관리
#### 탄력적 데이터베이스 풀 만들기 및 관리
#### [Elastic Database 풀을 사용하는 경우](sql-database-elastic-pool-guidance.md)
#### [보안 지침](sql-database-security-guidelines.md)
#### [Elastic Database 풀](sql-database-elastic-pool.md)
#### [자동화](sql-database-manage-automation.md)
#### 서비스 계층 및 성능 수준 수정
##### [Azure [포털](sql-database-scale-up.md)
##### [PowerShell](sql-database-scale-up-powershell.md)
### 규모가 확장된 데이터베이스 만들기 및 관리
#### [개요](sql-database-elastic-scale-introduction.md)
#### [확장성 있는 클라우드 데이터베이스 빌드](sql-database-elastic-database-client-library.md)
#### [분할된 데이터베이스 맵 관리자를 사용하여 규모 확장](sql-database-elastic-scale-shard-map-management.md)
#### [확장하기 위해 기존 데이터베이스 마이그레이션](sql-database-elastic-convert-to-use-elastic-tools.md)
#### [규모 확장된 클라우드 데이터베이스 간 데이터 이동](sql-database-elastic-scale-overview-split-and-merge.md)
#### [엔터티 프레임워크 사용](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Dapper 작업](sql-database-elastic-scale-working-with-dapper.md)
#### [분할된 맵 관리자에 대한 성능 카운터](sql-database-elastic-database-perf-counters.md)
#### 크로스 데이터베이스 작업 및 쿼리
##### [크로스 데이터베이스 쿼리](sql-database-elastic-query-overview.md)
##### [서로 다른 스키마를 사용하여 크로스 데이터베이스 쿼리](sql-database-elastic-query-vertical-partitioning.md)
##### [크로스 데이터베이스 보고](sql-database-elastic-query-horizontal-partitioning.md)
##### [크로스 데이터베이스 작업](sql-database-elastic-jobs-overview.md)
##### [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)
##### [다중 분할된 데이터베이스 쿼리](sql-database-elastic-scale-multishard-querying.md)
##### [다중 테넌트 행 수준 보안](sql-database-elastic-tools-multi-tenant-row-level-security.md)
##### [클라우드 데이터베이스의 분산 트랜잭션](sql-database-elastic-transactions-overview.md)
####[탄력적 데이터베이스 도구 용어집](sql-database-elastic-scale-glossary.md)
#### [FAQ](sql-database-elastic-scale-faq.md)
### 액세스 및 사용 권한 만들기 및 관리
#### [개요](sql-database-security.md)
#### [보안 지침](sql-database-security-guidelines.md)
#### [로그인 관리](sql-database-manage-logins.md)
#### [Azure SQL Database에 대한 Azure Security Center](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
#### [SQL 보안 센터](https://msdn.microsoft.com/library/azure/bb510589)
### [데이터베이스 및 응용 프로그램 개발](sql-database-develop-overview.md)
### 데이터베이스 마이그레이션
#### [SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)
### 비즈니스 연속성을 위한 제공
#### [개요](sql-database-business-continuity.md)
#### [데이터베이스 백업](sql-database-automated-backups.md) 
#### [백업을 사용한 데이터베이스 복구](sql-database-recovery-using-backups.md)
#### [데이터 센터 가동 중단에서 복구](sql-database-disaster-recovery.md)
#### [재해 복구에 대한 인증 요구 사항](sql-database-geo-replication-security-config.md)
#### [비즈니스 연속성 디자인 시나리오](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [탄력적 풀을 사용한 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [롤링 업그레이드](sql-database-manage-application-rolling-upgrade.md)
### 데이터베이스 모니터링 및 튜닝
#### [단일 데이터베이스](sql-database-single-database-monitor.md)
#### [단일 데이터베이스 지침](sql-database-performance-guidance.md)
#### [Azure Portal의 워크로드 정보](sql-database-performance.md)

## 고객 구현
### [Daxko/CSI 소프트웨어](sql-database-implementation-daxko.md)
### [GEP](sql-database-implementation-gep.md)
### [SnelStart](sql-database-implementation-snelstart.md)
### [Umbraco](sql-database-implementation-umbraco.md)

# 시작
## 서버, 풀, 데이터베이스 및 방화벽 만들기
### [Azure Portal](sql-database-get-started.md)
### [PowerShell](sql-database-get-started-powershell.md)
### [C#](sql-database-get-started-csharp.md)
## 쿼리 데이터
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
## 서버, 풀, 데이터베이스 및 방화벽 관리
### [Azure Portal](sql-database-manage-portal.md)
### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### [PowerShell](sql-database-manage-powershell.md)
## [액세스 및 사용 권한 만들기 및 관리](sql-database-get-started-security.md)
## 데이터 보안 및 보호
### [감사](sql-database-auditing-get-started.md)
### [위협 감지](sql-database-threat-detection-get-started.md)
### 동적 데이터 마스킹
#### [Azure Portal](sql-database-dynamic-data-masking-get-started-portal.md)
## 규모가 확장된 데이터베이스 만들기 및 관리
### [탄력적 확장](sql-database-elastic-scale-get-started.md)
### [탄력적 작업](sql-database-elastic-jobs-getting-started.md)
### 탄력적 쿼리
### [크로스 데이터베이스 보고서](sql-database-elastic-query-getting-started.md)
### [크로스 데이터베이스 쿼리](sql-database-elastic-query-getting-started-vertical.md)
## [메모리 내 최적화](sql-database-in-memory.md)
## [데이터베이스 이동](sql-database-troubleshoot-moving-data.md)
## [데이터 동기화](sql-database-get-started-sql-data-sync.md)
##데이터베이스 모니터링 및 튜닝
### [SQL Database 관리자 개요](sql-database-advisor.md)
### [Azure Portal의 워크로드 정보](sql-database-performance.md)
## [솔루션 빠른 시작](sql-database-solution-quick-starts.md)
# 방법
## [감사](sql-database-auditing-get-started.md)
## 생성
### 리소스 그룹
#### [PowerShell](sql-database-manage-powershell.md#create-a-resource-group)
### 서버
#### [Azure Portal](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-server)
### 탄력적 데이터베이스 풀
#### [Azure Portal](sql-database-elastic-pool-create-portal.md)
#### [C#](sql-database-elastic-pool-create-csharp.md)
#### [PowerShell](sql-database-elastic-pool-create-powershell.md)
### 데이터베이스
#### 단일 데이터베이스
##### [Azure Portal](sql-database-get-started.md)
##### [C#](sql-database-get-started-csharp.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-get-started-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
#### 분할된 데이터베이스
##### [분할된 데이터베이스 맵 관리자 사용](sql-database-elastic-scale-shard-map-management.md)
##### [분할 병합 보안 구성](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Dapper 작업](sql-database-elastic-scale-working-with-dapper.md)
##### [엔터티 프레임워크 사용](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
##### [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)
##### [다중 테넌트 행 수준 보안](sql-database-elastic-tools-multi-tenant-row-level-security.md)
### 방화벽 규칙
#### 서버
##### [Azure 포털](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [REST API](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### 데이터베이스
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### 작업
#### [서비스 설치](sql-database-elastic-jobs-service-installation.md)
#### [Azure Portal](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
### 로그인
#### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-logins.md)
## 개발
### [개요](https://msdn.microsoft.com/library/mt763826.aspx)
### 시나리오
#### [다중 테넌트 SaaS 응용 프로그램](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### 임시 테이블
##### [임시 테이블](sql-database-temporal-tables.md)
##### [보존 정책](sql-database-temporal-tables-retention-policy.md)
#### [JSON 데이터](sql-database-json-features.md)
#### [메모리 내](sql-database-in-memory.md)
###시작
#### [연결 라이브러리](sql-database-libraries.md)
#### 응용 프로그램 연결
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.JS](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Visual Studio를 사용하여 연결](sql-database-connect-query.md)
### 방법
#### 서버 만들기
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### 탄력적 풀 만들기
###### [PowerShell](sql-database-elastic-pool-create-powershell.md)
###### [C#](sql-database-elastic-pool-create-csharp.md)
#### 데이터베이스 만들기
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### 방화벽 규칙 만들기
###### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
###### [REST API](sql-database-configure-firewall-settings-rest.md)
#### 서버, 풀, 데이터베이스 및 방화벽 관리
##### [PowerShell](sql-database-manage-powershell.md)
##### 탄력적 풀 관리
###### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
###### [C#](sql-database-elastic-pool-manage-csharp.md)
##### [서비스 계층 및 성능 수준 변경](sql-database-scale-up-powershell.md)
#### 데이터 이동
##### [데이터베이스를 BACPAC 파일로 내보내기](sql-database-export-powershell.md)
##### [데이터베이스를 BACPAC 파일에서 가져오기](sql-database-import-powershell.md)
##### [Azure의 다른 위치로 데이터베이스 복사](sql-database-copy-powershell.md)
#### [응용 프로그램 인증을 위한 필수 값 가져오기](sql-database-client-id-keys.md)
#### [탄력적 작업](sql-database-elastic-jobs-powershell.md)
#### 데이터베이스 복원 및 복구
##### 삭제된 데이터베이스 복원
###### [PowerShell](sql-database-restore-deleted-database-powershell.md)
##### 지정 시간 데이터베이스 복원
###### [PowerShell](sql-database-point-in-time-restore-powershell.md)
##### 지역 복원
###### [PowerShell](sql-database-geo-restore-powershell.md)
#### 활성 지역 복제를 사용하여 데이터 복제
##### 구성
###### [PowerShell](sql-database-geo-replication-powershell.md)
##### 장애 조치(Failover)
###### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [ADO.NET 4.5에 대해 1433 이외의 포트 사용](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [오류 메시지 작업](sql-database-develop-error-messages.md)
#### [일괄 처리 사용](sql-database-use-batching-to-improve-performance.md)
### 참조
#### [Transact-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
#### [SQL Server용 .NET Framework 데이터 공급자(개념)](https://msdn.microsoft.com/library/kb9s9ks0.aspx)
#### [SQL Server용 .NET Framework 데이터 공급자(API 참조)](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)
#### SQL PowerShell
##### [Azure SQL Database Cmdlet(리소스 관리)](https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
##### [Azure SQL Database Cmdlet(서비스 관리)](https://msdn.microsoft.com/library/azure/dn546723(v=azure.300\).aspx)
##### [SQL Server Cmdlet](https://msdn.microsoft.com/library/mt740629.aspx)
#### SQL Database REST API
##### [REST API(리소스 관리)](https://msdn.microsoft.com/library/azure/mt420159)
##### [REST API(서비스 관리)](https://msdn.microsoft.com/library/azure/dn505719.aspx)
#### SQL Database 관리 라이브러리
##### [SQL Database 관리 라이브러리 참조](https://msdn.microsoft.com/library/azure/mt349017.aspx)
##### [SQL Database 관리 라이브러리 패키지 가져오기](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
#### Entity Framework
##### [Entity Framework 패키지 가져오기](https://www.nuget.org/packages/EntityFramework/)
#### [SQL Server 드라이버](https://msdn.microsoft.com/library/mt654049.aspx)
##### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
##### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
##### [Node.JS](https://msdn.microsoft.com/library/mt652093.aspx)
##### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
##### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
##### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
##### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
#### [Azure SDK(다운로드)](https://www.visualstudio.com/vs/azure-tools/)
#### [Azure SDK(설명서)](https://azure.microsoft.com/documentation/articles/dotnet-sdk/)
### 리소스
#### [SQL Server 도구](https://msdn.microsoft.com/library/mt238365.aspx)
#### [SSMS(SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx)
#### [SSDT(SQL Server Data Tools)](https://msdn.microsoft.com/library/mt204009.aspx)
#### [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
#### [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
#### [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)
## 삭제
### 데이터베이스
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database)
### 서버
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database-server)
## 위협 감지
### [위협 감지](sql-database-threat-detection-get-started.md)
### [방화벽](sql-database-firewall-configure.md)
## 데이터 암호화
### 상시 암호화
#### [상시 암호화 개요](sql-database-always-encrypted.md)
#### [상시 암호화 Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
### [투명한 데이터 암호화](https://msdn.microsoft.com/library/azure/dn948096)
### [열 암호화](https://msdn.microsoft.com/library/azure/ms179331)
## 관리
###  인증
#### SQL 인증
#### [Azure Active Directory 인증](sql-database-aad-authentication.md)
#### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
### 서버
### 탄력적 풀
#### [Azure Portal](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### 데이터베이스
#### 단일 데이터베이스
##### [Azure Portal](sql-database-manage-portal.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-blank)
#### 서비스 계층 및 성능 수준 변경
#### [Azure [포털](sql-database-scale-up.md)
#### [PowerShell](sql-database-manage-powershell.md#change-the-performance-level-of-a-sql-database)
#### 분할된 데이터베이스
##### [기존 확장된 데이터베이스를 확장하도록 마이그레이션](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [자격 증명 관리](sql-database-elastic-scale-manage-credentials.md)
##### [확장된 클라우드 데이터베이스 간 데이터 이동](sql-database-elastic-scale-overview-split-and-merge.md)
##### [분할/병합 서비스 배포](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
##### [분할된 데이터베이스 추가](sql-database-elastic-scale-add-a-shard.md)
##### [RecoveryManager 클래스를 사용하여 분할된 데이터베이스 맵 문제 해결](sql-database-elastic-database-recovery-manager.md)
### 방화벽 규칙
#### 서버
##### [Azure 포털](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [REST API](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### 데이터베이스
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### 작업
#### [서비스 설치](sql-database-elastic-jobs-service-installation.md)
#### [Azure Portal](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
#### [클라이언트 라이브러리 업그레이드](sql-database-elastic-scale-upgrade-client-library.md)
### 로그인
#### [로그인 관리](sql-database-manage-logins.md)
## 데이터 마스킹
### 동적 데이터 마스킹
#### [Azure Portal](sql-database-dynamic-data-masking-get-started-portal.md)
#### [하위 수준 클라이언트](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
## 마이그레이션
### 호환성 확인
#### [SQL 패키지 유틸리티](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### 호환성 문제 해결
#### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [SQL Azure 마이그레이션 마법사](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [SQL Server Management Studio 마이그레이션 마법사 사용](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [트랜잭션 복제 사용](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### 데이터베이스를 BACPAC 파일로 내보내기
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [SQL 패키지 유틸리티](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### 데이터베이스를 BACPAC 파일에서 가져오기
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [SQL 패키지 유틸리티](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Azure Portal](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)

## 모니터링 및 튜닝
### [단일 데이터베이스](sql-database-performance-guidance.md)
### 탄력적 풀
#### [Azure Portal](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### [쿼리 성능 Insight](sql-database-query-performance.md)
### SQL 데이터베이스 관리자
#### [Azure Portal](sql-database-advisor-portal.md)
### 서비스 계층 및 성능 수준 변경
#### [Azure Portal](sql-database-scale-up.md)
#### [PowerShell](sql-database-scale-up-powershell.md)
### [성능 튜닝 팁](sql-database-troubleshoot-performance.md)
### 메모리 내 OLTP
#### [메모리 내 OLTP 채택](sql-database-in-memory-oltp-migration.md)
#### [메모리 내 OLTP 저장소 모니터링](sql-database-in-memory-oltp-monitoring.md)
### 쿼리 저장소
#### [쿼리 저장소를 사용한 성능 모니터링](https://msdn.microsoft.com/library/dn817826.aspx)
#### [쿼리 저장소 사용 시나리오](https://msdn.microsoft.com/library/mt614796.aspx)
#### [쿼리 저장소 작동](sql-database-operate-query-store.md)
### [호환성 수준](sql-database-compatibility-level-query-performance-130.md)
### [이벤트 감사](sql-database-auditing-get-started.md)
### [분할된 맵 관리자에 대한 성능 카운터](sql-database-elastic-database-perf-counters.md)
### 확장 이벤트
#### [확장 이벤트](sql-database-xevent-db-diff-from-svr.md)
#### [이벤트 파일 대상 코드](sql-database-xevent-code-event-file.md)
#### [링 버퍼 대상 코드](sql-database-xevent-code-ring-buffer.md)
### Dmv
#### [DMV](sql-database-monitoring-with-dmvs.md)
#### [Dmv](sql-database-manage-azure-ssms#monitor-sql-database-using-dynamic-management-views


## 데이터 이동
### SQL 데이터베이스 복사
#### [개요](sql-database-copy.md)
#### [Azure 포털](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### 데이터베이스를 BACPAC 파일로 내보내기
#### [Azure Portal](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [PowerShell](sql-database-export-powershell.md)
### 데이터베이스를 BACPAC 파일에서 가져오기
#### [Azure Portal](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
### [데이터 동기화](sql-database-get-started-sql-data-sync.md)
### [BCP를 사용하여 CSV 파일에서 로드](sql-database-load-from-csv-with-bcp.md)

## 쿼리
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [다중 분할된 데이터베이스 쿼리](sql-database-elastic-scale-multishard-querying.md)
### 크로스 데이터베이스 쿼리
#### [서로 다른 스키마를 사용하여 크로스 데이터베이스 쿼리](sql-database-elastic-query-vertical-partitioning.md)
#### [크로스 데이터베이스 보고](sql-database-elastic-query-horizontal-partitioning.md)
#### [클라우드 데이터베이스의 분산 트랜잭션](sql-database-elastic-transactions-overview.md)
#### [클라이언트 라이브러리 업그레이드](sql-database-elastic-scale-upgrade-client-library.md)
#### [다중 분할된 데이터베이스 쿼리](sql-database-elastic-scale-multishard-querying.md)

## 복원
### 삭제된 데이터베이스 복원
### [Azure Portal](sql-database-restore-deleted-database-portal.md)
### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### 특정 시점 복원
#### [Azure Portal](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### 지역 복원
#### [Azure Portal](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [단일 테이블](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [데이터 센터 가동 중단에서 복구](sql-database-disaster-recovery.md)
### [재해 복구 훈련 수행](sql-database-disaster-recovery-drills.md)

## 복제
### [활성 지역 복제 개요](sql-database-geo-replication-overview.md)
### 구성
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### 장애 조치(Failover)
#### [Azure Portal](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)

## 문제 해결
### 연결
#### [연결 문제](sql-database-troubleshoot-common-connection-issues.md)
#### [일시적인 연결 오류](sql-database-troubleshoot-connection.md)
#### [진단 및 방지](sql-database-connectivity-issues.md)
### [권한](sql-database-troubleshoot-permissions.md)

# 참조
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## SQL PowerShell
### [Azure SQL Database Cmdlet(리소스 관리)](https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
### [SQL Server Cmdlet](https://msdn.microsoft.com/library/mt740629.aspx)
## SQL Database REST API
### [REST API(리소스 관리)](https://msdn.microsoft.com/library/azure/mt420159)
## SQL Database 관리 라이브러리
### [SQL Database 관리 라이브러리 참조](https://msdn.microsoft.com/library/azure/mt349017.aspx)
### [SQL Database 관리 라이브러리 패키지 가져오기](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [SQL Server 드라이버](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [Node.JS](https://msdn.microsoft.com/library/mt652093.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)

# 리소스
## [SQL Server 도구](https://msdn.microsoft.com/library/mt238365.aspx)
## [SSMS(SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SSDT(SQL Server Data Tools)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)

<!--HONumber=Nov16_HO2-->


