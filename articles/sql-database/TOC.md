# 개요
## [SQL Database 정의](sql-database-technical-overview.md)
### [서비스 계층](sql-database-service-tiers.md)
### [DTU 및 eDTU](sql-database-what-is-a-dtu.md)
### [DTU 벤치마크 개요](sql-database-benchmark-overview.md)
### [리소스 한도](sql-database-resource-limits.md)
### [기능](sql-database-features.md)
### [SQL Database FAQ](sql-database-faq.md)
## 이점
### [학습 및 적응](sql-database-learn-and-adapt.md)
### [즉석에서 확장](sql-database-scale-on-the-fly.md)
### [다중 테넌트 앱 빌드](sql-database-build-multi-tenant-apps.md)
### [보안 및 보호](sql-database-helps-secures-and-protects.md)
### [사용자 환경에서 작동](sql-database-works-in-your-environment.md)
## 비교
### [VM의 SQL Database 및 SQL](sql-database-paas-vs-sql-server-iaas.md)
### [T-SQL 차이점](sql-database-transact-sql-information.md)
### [SQL 및 NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [SQL Database 도구](sql-database-manage-overview.md)
## [SQL Database 자습서](sql-database-explore-tutorials.md)
## [솔루션 빠른 시작](sql-database-solution-quick-starts.md)
## 보안
### [Azure SQL Database에 대한 Azure Security Center](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [SQL Security Center](https://msdn.microsoft.com/library/azure/bb510589)
# 시작
## 데이터베이스 및 서버
### 유용한 정보
#### [서버](sql-database-server-overview.md)
#### [단일 데이터베이스](sql-database-overview.md)
#### [여러 데이터베이스](sql-database-elastic-scale-introduction.md)
##### 매핑 테넌트
###### [탄력적인 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)
###### [분할된 데이터베이스 맵 관리자](sql-database-elastic-scale-shard-map-management.md)
###### [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)
###### [자격 증명 관리](sql-database-elastic-scale-manage-credentials.md)
###### [다중 분할된 데이터베이스 쿼리](sql-database-elastic-scale-multishard-querying.md)
##### 탄력적 풀(리소스 풀)
###### [탄력적 풀이란?](sql-database-elastic-pool.md)
###### [탄력적 풀을 사용하는 경우](sql-database-elastic-pool-guidance.md)
###### [탄력적 풀 가격](sql-database-elastic-pool-price.md)
##### 분할된 데이터베이스
###### [탄력적 도구 용어집](sql-database-elastic-scale-glossary.md)
###### [분할된 데이터베이스 간 데이터 이동](sql-database-elastic-scale-overview-split-and-merge.md)
###### [탄력적 도구 FAQ](sql-database-elastic-scale-faq.md)
##### 탄력적 쿼리(데이터베이스 간 쿼리)
###### [탄력적 쿼리란?](sql-database-elastic-query-overview.md)
##### 탄력적 트랜잭션(분산된 트랜잭션)
###### [클라우드 데이터베이스의 트랜잭션](sql-database-elastic-transactions-overview.md)
##### 탄력적 작업(데이터베이스 간 작업)
###### [탄력적 작업이란?](sql-database-elastic-jobs-overview.md)
#### [Azure RemoteApp을 사용하여 SQL Database에 연결](sql-database-ssms-remoteapp.md)
#### [Azure Automation 서비스를 사용하여 Azure SQL Database 관리](sql-database-manage-automation.md)
### 실행 사항
#### [Azure Portal을 사용하여 단일 데이터베이스 만들기](sql-database-get-started.md)
#### [PowerShell을 사용하여 단일 데이터베이스 만들기](sql-database-get-started-powershell.md)
#### [C#을 사용하여 단일 데이터베이스 만들기](sql-database-get-started-csharp.md)
#### [분할된 데이터베이스 응용 프로그램 만들기](sql-database-elastic-scale-get-started.md)
#### [분할된 데이터베이스 간 데이터 이동](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [탄력적 작업 시작](sql-database-elastic-jobs-getting-started.md)
#### [탄력적 쿼리 시작](sql-database-elastic-query-getting-started-vertical.md)
#### [탄력적 쿼리를 사용하여 보고서 만들기](sql-database-elastic-query-getting-started.md)
#### [여러 스키마를 사용하여 데이터베이스 쿼리](sql-database-elastic-query-vertical-partitioning.md)
#### [확장된 데이터베이스에 대한 보고](sql-database-elastic-query-horizontal-partitioning.md)
## 데이터 마이그레이션 및 이동
### 유용한 정보
#### [데이터베이스 마이그레이션](sql-database-cloud-migrate.md)
#### [트랜잭션 복제](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [데이터 동기화](sql-database-get-started-sql-data-sync.md)
#### [SQL 데이터베이스 복사](sql-database-copy.md)
## 방화벽 규칙, 인증 및 권한 부여
### 유용한 정보
#### [개요](sql-database-security.md)
#### [보안 지침](sql-database-security-guidelines.md)
#### [방화벽](sql-database-firewall-configure.md)
#### [로그인 관리](sql-database-manage-logins.md)
### 실행 사항
#### [SQL 인증 및 권한 부여](sql-database-get-started-security.md)
## 데이터 보안 및 보호
### 유용한 정보
#### 감사
##### [감사](sql-database-auditing-get-started.md)
##### [하위 클라이언트 지원 및 감사에 대한 IP 끝점 변경](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [위협 감지](sql-database-threat-detection-get-started.md)
#### 데이터 암호화
##### [Azure 키 자격 증명 모음](sql-database-always-encrypted-azure-key-vault.md)
##### [투명한 데이터 암호화](https://msdn.microsoft.com/library/azure/dn948096)
##### [열 암호화](https://msdn.microsoft.com/library/azure/ms179331)
#### 데이터 마스킹
##### 동적 데이터 마스킹
###### [Azure Portal](sql-database-dynamic-data-masking-get-started.md)
### 실행 사항
#### [Azure Portal을 사용하여 동적 데이터 마스킹](sql-database-dynamic-data-masking-get-started.md)
##### [Windows 인증서 저장소를 사용하여 항상 암호화](sql-database-always-encrypted.md)
## 비즈니스 연속성
### 유용한 정보
#### [개요](sql-database-business-continuity.md)
#### [데이터베이스 백업](sql-database-automated-backups.md)
#### [장기 보존](sql-database-long-term-retention.md)
#### [백업을 사용한 데이터베이스 복구](sql-database-recovery-using-backups.md)
#### [단일 테이블 복구](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [데이터 센터 가동 중단에서 복구](sql-database-disaster-recovery.md)
#### [재해 복구에 대한 인증 요구 사항](sql-database-geo-replication-security-config.md)
#### [비즈니스 연속성 디자인 시나리오](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [탄력적 풀을 사용한 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [롤링 업그레이드](sql-database-manage-application-rolling-upgrade.md)
#### [재해 복구 훈련 수행](sql-database-disaster-recovery-drills.md)
#### [활성 지역 복제 개요](sql-database-geo-replication-overview.md)
### 실행 사항
#### [SQL Database 백업 및 복원 시작](sql-database-get-started-backup-recovery.md)
## 앱 개발
### 유용한 정보
#### [데이터베이스 및 응용 프로그램 개발 개요](sql-database-develop-overview.md)
#### [연결 라이브러리](sql-database-libraries.md)
#### [다중 테넌트 SaaS 응용 프로그램](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [행 수준 보안이 포함된 다중 테넌트 SaaS 응용 프로그램 크기 조정](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [ADO.NET 4.5에 대해 1433 이외의 포트 사용](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [응용 프로그램 인증을 위한 필수 값 가져오기](sql-database-client-id-keys.md)
### 실행 사항
#### 응용 프로그램 연결
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [C 및 C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.JS](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Visual Studio를 사용하여 연결](sql-database-connect-query.md)
#### [클라이언트 응용 프로그램 빌드](https://www.microsoft.com/sql-server/developer-get-started)
#### [오류 메시지 작업](sql-database-develop-error-messages.md)
#### [엔터티 프레임워크 사용](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Dapper와 함께 클라이언트 라이브러리 사용](sql-database-elastic-scale-working-with-dapper.md)
### 고객 구현
#### [Daxko/CSI 소프트웨어](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## 데이터베이스 개발
### 유용한 정보
#### 임시 테이블
##### [임시 테이블](sql-database-temporal-tables.md)
##### [보존 정책](sql-database-temporal-tables-retention-policy.md)
#### [JSON 데이터](sql-database-json-features.md)
#### [메모리 내 최적화](sql-database-in-memory.md)
### 실행 사항
#### [SQL Server 개발](https://msdn.microsoft.com/library/ms179422.aspx)
#### [메모리 내 OLTP 채택](sql-database-in-memory-oltp-migration.md)
## 모니터링 및 튜닝
### 유용한 정보
#### [단일 데이터베이스](sql-database-single-database-monitor.md)
#### [SQL Database 관리자 개요](sql-database-advisor.md)
#### [단일 데이터베이스 지침](sql-database-performance-guidance.md)
#### [Azure Portal의 워크로드 정보](sql-database-performance.md)
#### [일괄 처리 사용](sql-database-use-batching-to-improve-performance.md)
#### [확장 이벤트](sql-database-xevent-db-diff-from-svr.md)
## SQL Database V11
### [Web 및 Business 버전 지원 종료](sql-database-web-business-sunset-faq.md)
### [서비스 계층 작업](sql-database-service-tier-advisor.md)
### [탄력적 풀 평가 도구](sql-database-elastic-pool-database-assessment-powershell.md)
### [V12로 업그레이드](sql-database-v12-plan-prepare-upgrade.md)
#### [Azure Portal을 사용하여 업그레이드](sql-database-upgrade-server-portal.md)
#### [Powershell을 사용하여 업그레이드](sql-database-upgrade-server-powershell.md)
# 방법
## 만들기 및 관리
### [Azure Portal을 사용하여 SQL Database 관리](sql-database-manage-portal.md)
### [PowerShell을 사용하여 SQL Database 관리](sql-database-manage-powershell.md)
### [SSMS를 사용하여 SQL Database 관리](sql-database-manage-azure-ssms.md)
### 서버
#### [서버 만들기](sql-database-create-servers.md)
#### [서버 설정 확인 또는 업데이트](sql-database-view-update-server-settings.md)
### 단일 데이터베이스
#### [단일 데이터베이스 만들기](sql-database-create-databases.md)
#### [데이터베이스 설정 확인 또는 업데이트](sql-database-view-update-database-settings.md)
### 방화벽 규칙
#### [Azure Portal을 사용하여 방화벽 규칙 만들기](sql-database-configure-firewall-settings.md)
#### [PowerShell을 사용하여 방화벽 규칙 만들기](sql-database-configure-firewall-settings-powershell.md)
#### [REST API를 사용하여 방화벽 규칙 만들기](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL을 사용하여 방화벽 규칙 만들기](sql-database-configure-firewall-settings-tsql.md)
### 여러 데이터베이스
#### [클라이언트 응용 프로그램에서 클라이언트 라이브러리 업그레이드](sql-database-elastic-scale-upgrade-client-library.md)
#### 분할된 데이터베이스
##### [보안 구성](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [분할된 데이터베이스 추가](sql-database-elastic-scale-add-a-shard.md)
##### [분할된 데이터베이스 맵 문제 해결](sql-database-elastic-database-recovery-manager.md)
##### [기존 확장된 데이터베이스를 분할된 데이터베이스로 마이그레이션](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [분할된 맵 관리자에 대한 성능 카운터 만들기](sql-database-elastic-database-perf-counters.md)
#### 탄력적 작업
##### [탄력적 작업 서비스를 설치하려면 어떻게 해야 하나요?](sql-database-elastic-jobs-service-installation.md)
##### [PowerShell을 사용하여 탄력적 작업 만들기 및 관리](sql-database-elastic-jobs-powershell.md) 
##### [Azure Portal을 사용하여 탄력적 작업 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md)
##### [탄력적 작업을 제거하려면 어떻게 해야 하나요?](sql-database-elastic-jobs-uninstall.md)
#### 탄력적 풀
##### [Azure Portal을 사용하여 만들기](sql-database-elastic-pool-create-portal.md)
##### [PowerShell을 사용하여 만들기](sql-database-elastic-pool-create-powershell.md)
##### [C#을 사용하여 만들기](sql-database-elastic-pool-create-csharp.md)
##### [Azure Portal을 사용하여 관리](sql-database-elastic-pool-manage-portal.md)
##### [Powershell을 사용하여 관리](sql-database-elastic-pool-manage-powershell.md)
##### [C#을 사용하여 관리](sql-database-elastic-pool-manage-csharp.md)
##### [T-SQL을 사용하여 관리](sql-database-elastic-pool-manage-tsql.md)
##  인증 및 권한 부여
### [Azure AD 인증](sql-database-aad-authentication.md)
### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
## 데이터 암호화
### [투명한 데이터 암호화](https://msdn.microsoft.com/library/azure/dn948096)
### [열 암호화](https://msdn.microsoft.com/library/azure/ms179331)
## 데이터베이스 마이그레이션
### 호환성 확인
#### [SQL 패키지 유틸리티를 사용하여 호환성 확인](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SSMS를 사용하여 호환성 확인](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### 호환성 문제 해결
#### [SSDT를 사용하여 호환성 문제 해결](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SSMS를 사용하여 호환성 문제 해결](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [SMW를 사용하여 호환성 문제 해결](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [SSMS 마이그레이션 마법사를 사용하여 마이그레이션](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
## 모니터링 및 튜닝
### [Query Performance Insight](sql-database-query-performance.md)
### [SQL Database 관리자](sql-database-advisor-portal.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [호환성 수준](sql-database-compatibility-level-query-performance-130.md)
### [성능 튜닝 팁](sql-database-troubleshoot-performance.md)
### 서비스 계층 및 성능 수준 변경
#### [Azure Portal을 사용하여 서비스 계층 변경](sql-database-scale-up.md)
#### [PowerShell을 사용하여 서비스 계층 변경](sql-database-scale-up-powershell.md)
### [경고 만들기](sql-database-insights-alerts-portal.md)
#### [메모리 내 OLTP 저장소 모니터링](sql-database-in-memory-oltp-monitoring.md)
### 쿼리 저장소
#### [쿼리 저장소를 사용한 성능 모니터링](https://msdn.microsoft.com/library/dn817826.aspx)
#### [쿼리 저장소 사용 시나리오](https://msdn.microsoft.com/library/mt614796.aspx)
#### [쿼리 저장소 작동](sql-database-operate-query-store.md)
### 확장 이벤트
#### [이벤트 파일 대상 코드](sql-database-xevent-code-event-file.md)
#### [링 버퍼 대상 코드](sql-database-xevent-code-ring-buffer.md)
## 데이터 이동
### SQL 데이터베이스 복사
#### [Azure Portal을 사용하여 복사](sql-database-copy-portal.md)
#### [PowerShell을 사용하여 복사](sql-database-copy-powershell.md)
#### [T-SQL을 사용하여 복사](sql-database-copy-transact-sql.md)
### 데이터베이스를 BACPAC 파일로 내보내기
#### [Azure Portal을 사용하여 내보내기](sql-database-export.md)
#### [SSMS를 사용하여 내보내기](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [SQL 패키지 유틸리티를 사용하여 내보내기](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell을 사용하여 내보내기](sql-database-export-powershell.md)
### 데이터베이스를 BACPAC 파일에서 가져오기
#### [Azure Portal을 사용하여 가져오기](sql-database-import.md)
#### [PowerShell을 사용하여 가져오기](sql-database-import-powershell.md)
#### [SSMS를 사용하여 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [SQL 패키지 유틸리티를 사용하여 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [BCP를 사용하여 CSV 파일에서 로드](sql-database-load-from-csv-with-bcp.md)
## 쿼리
### [SSMS를 사용하여 쿼리](sql-database-connect-query-ssms.md)
## 백업 및 복원
### 장기 백업 보존
#### [장기 백업 보존 구성](sql-database-configure-long-term-retention.md)
#### [Recovery Services 자격 증명 모음에서 백업 확인](sql-database-view-backups-in-vault.md)
#### [장기 백업 보존에서 복원](sql-database-restore-from-long-term-retention.md)
### 삭제된 데이터베이스 복원
#### [Azure Portal을 사용하여 삭제된 데이터베이스 복원](sql-database-restore-deleted-database-portal.md)
#### [PowerShell을 사용하여 삭제된 데이터베이스 복원](sql-database-restore-deleted-database-powershell.md)
### 특정 시점 복원
#### [지정 시간으로 복원](sql-database-point-in-time-restore.md)
#### [가장 오래된 복원 지점 확인](sql-database-view-oldest-restore-point.md)
### 지역 복원
#### [Azure Portal을 사용한 지역 복원](sql-database-geo-restore-portal.md)
#### [PowerShell을 사용한 지역 복원](sql-database-geo-restore-powershell.md)
## 활성 지역 복제
### [Azure Portal을 사용한 구성](sql-database-geo-replication-portal.md)
### [PowerShell을 사용한 구성](sql-database-geo-replication-powershell.md)
### [T-SQL을 사용한 구성](sql-database-geo-replication-transact-sql.md)
### [Azure Portal을 사용한 장애 조치](sql-database-geo-replication-failover-portal.md)
### [PowerShell을 사용한 장애 조치](sql-database-geo-replication-failover-powershell.md)
### [T-SQL을 사용한 장애 조치](sql-database-geo-replication-failover-transact-sql.md)
## 문제 해결
### [연결 문제](sql-database-troubleshoot-common-connection-issues.md)
### [일시적인 연결 오류](sql-database-troubleshoot-connection.md)
### [진단 및 방지](sql-database-connectivity-issues.md)
### [권한](sql-database-troubleshoot-permissions.md)
### [데이터베이스 이동](sql-database-troubleshoot-moving-data.md)
# 참조
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell(탄력적 DB)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](/dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.JS](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/bb510741.aspx)
## [REST (영문)](https://msdn.microsoft.com/library/azure/mt163571.aspx)

# 관련 항목
## SQL Database 관리 라이브러리
### [SQL Database 관리 라이브러리 패키지 가져오기](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [SQL Server 드라이버](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)

# 리소스
## [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)
## [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/sql-azure)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [서비스 업데이트](https://azure.microsoft.com/updates/?service=sql-database)
## [SQL Server 도구](https://msdn.microsoft.com/library/mt238365.aspx)
## [SSMS(SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SSDT(SQL Server Data Tools)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)


<!--HONumber=Jan17_HO3-->


