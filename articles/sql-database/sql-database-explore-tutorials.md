<properties
   pageTitle="Azure SQL 데이터베이스 자습서 탐색 | Microsoft Azure"
   description="SQL 데이터베이스 기능에 대해 알아보기"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="08/24/2016"
   ms.author="carlrab"/>
   
# Azure SQL 데이터베이스 자습서 탐색

아래 링크를 통해 나열된 각 기능 영역의 개요 및 각 영역에 대한 간단한 단계별 시작 자습서로 이동합니다. 실제 시나리오를 기준으로 완벽한 솔루션에서 SQL 데이터베이스를 사용하는 방법을 보여 주는 솔루션 범위별 빠른 시작 방법을 보려면 [Azure SQL 데이터베이스 솔루션 빠른 시작](sql-database-solution-quick-starts.md)을 참조하세요.

## SQL Server Management Studio 사용

다음 자습서에서 SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스를 관리 및 쿼리하는 방법을 배웁니다.


> [AZURE.IMPORTANT] Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).


| 자습서 | 설명 |
|---|---|---|
| [서버 수준 보안 주체 로그인을 사용하여 AzureSQL 데이터베이스에 연결](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| 이 자습서에서는 서버 수준 보안 주체를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다.|
| [사용자로 Azure SQL 데이터베이스에 연결](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | 이 자습서에서는 데이터베이스 수준 사용자 계정을 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다.|
||||

## 탄력적 풀

다음 자습서에서 [탄력적 풀](sql-database-elastic-pool.md)을 사용하여 광범위하고 예측할 수 없는 사용 패턴을 가진 여러 데이터베이스에 대한 성능 목표를 관리하는 방법을 알아봅니다.

| 자습서 | 설명 |
|---|---|---|
| [탄력적 풀 만들기](sql-database-elastic-pool-create-portal.md) | 이 자습서에서는 Azure SQL 데이터베이스의 확장성 있는 풀을 만드는 방법을 알아봅니다. |
| [탄력적 데이터베이스 모니터링](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | 이 자습서에서는 잠재적인 문제에 대해 개별 탄력적 데이터베이스를 모니터링하는 방법을 알아봅니다. |
| [리소스 풀에 경고 추가](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | 이 자습서에서는 리소스가 설정한 사용률 임계값에 도달하면 사용자에게 메일을 보내거나 URL 끝점에 경고 문자열을 보내는 리소스에 대한 규칙을 추가하는 방법을 알아봅니다. |
| [탄력적 풀로 데이터베이스 이동](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | 이 자습서에서는 데이터베이스를 탄력적 풀 내로 이동하는 방법을 알아봅니다. |
| [탄력적 풀 외부로 데이터베이스 이동](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | 이 자습서에서는 데이터베이스를 탄력적 풀 에서 이동하는 방법을 알아봅니다. |
| [풀의 성능 설정 변경](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | 이 자습서에서는 풀에 대한 성능 및 저장소 제한을 조정하는 방법을 알아봅니다. |
||||

## 탄력적 데이터베이스 작업

다음 자습서에서 [탄력적 데이터베이스 작업](sql-database-elastic-jobs-overview.md)을 사용하는 방법을 알아봅니다.

| 자습서 | 설명 |
|---|---|---|
| [탄력적 데이터베이스 도구 시작하기](sql-database-elastic-scale-get-started.md) | 이 자습서에서는 간단한 분할된 응용 프로그램을 사용하여 탄력적 데이터베이스 도구의 기능을 사용하는 방법을 알아봅니다. |
| [Azure SQL 데이터베이스 탄력적 작업 시작](sql-database-elastic-jobs-getting-started.md) | 이 자습서에서는 관련 데이터베이스 그룹을 관리하는 작업을 만들고 관리하는 방법을 알아봅니다. | 
||||

## 탄력적 쿼리

다음 자습서에서 [탄력적 쿼리](sql-database-elastic-query-overview.md)를 실행하는 방법을 알아봅니다.

| 자습서 | 설명 |
|---|---|---|
| [수평 분할된(분할) 데이터베이스 간 쿼리](sql-database-elastic-query-getting-started.md) | 이 자습서에서는 [탄력적 쿼리](sql-database-elastic-query-overview.md)를 사용하여 수평 분할된(분할) 데이터베이스의 모든 데이터베이스에서 보고서를 만드는 방법을 알아봅니다. |
| [수직 분할된 데이터베이스 간 쿼리](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | 이 자습서에서는 [탄력적 쿼리](sql-database-elastic-query-overview.md)를 사용하여 수직 분할된 데이터베이스의 모든 데이터베이스에서 보고서를 만드는 방법을 알아봅니다. |
| [확장하기 위해 기존 데이터베이스 마이그레이션](sql-database-elastic-convert-to-use-elastic-tools.md)| 이 자습서에서는 Azure SQL 데이터베이스를 수평으로 확장(분할)하는 방법을 알아봅니다. |
||||

## 성능 최적화

다음 자습서에서 [단일 데이터베이스의 성능](sql-database-performance-guidance.md)을 최적화하는 방법을 알아봅니다. 여러 데이터베이스의 성능 최적화에 대한 자세한 내용은 [탄력적 풀](#elastic-pools)을 참조하세요.

| 자습서 | 설명 |
|---|---|---|
| [데이터베이스의 서비스 계층 및 성능 수준 변경](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | 이 자습서에서는 서비스 계층을 사용하여 Azure SQL 데이터베이스의 성능을 확장 또는 축소하는 방법을 알아봅니다. |
| [SQL 데이터베이스 관리자 Query Performance Insight](sql-database-performance.md#performance-overview) | 이 자습서에서는 SQL 데이터베이스 관리자 Query Performance Insight를 열고 사용하는 방법을 알아봅니다.|
| [SQL 데이터베이스 관리자 성능 권장 사항](sql-database-advisor.md#viewing-recommendations) | 이 자습서에서는 SQL 데이터베이스 관리자 성능 권장 사항을 검토 및 적용하는 방법을 알아봅니다. |
| [최상위 CPU 소비 쿼리 검토](sql-database-query-performance.md#review-top-cpu-consuming-queries)| 이 자습서에서는 SQL 데이터베이스 관리자 Query Performance Insight를 사용하여 쿼리를 사용하는 최상위 CPU를 검토하는 방법을 알아봅니다.|
| [개별 쿼리 세부 정보 보기](sql-database-query-performance.md#viewing-individual-query-details)| 이 자습서에서는 SQL 데이터베이스 관리자 Query Performance Insight를 사용하여 개별 쿼리 성능 세부 정보를 보는 방법을 알아봅니다.|
||||

## SQL 데이터베이스 마이그레이션 및 보관 

다음 자습서에서 [Azure SQL 데이터베이스에 기존 SQL Server 데이터베이스를 마이그레이션](sql-database-cloud-migrate.md)하는 방법을 알아봅니다.

| 자습서 | 설명 |
|---|---|---|
| [Visual Studio용 SQL Server Data Tools를 사용하여 호환성 문제 검색](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | 이 자습서에서는 Visual Studio용 SQL Server Data Tools를 사용하여 Azure SQL 데이터베이스 호환성을 확인하는 방법을 알아봅니다. |
| [Visual Studio용 SQL Server Data Tools를 사용하여 호환성 문제 해결](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | 이 자습서에서는 Visual Studio용 SQL Server Data Tools를 사용하여 Azure SQL 데이터베이스의 호환성 문제를 해결하는 방법을 알아봅니다. |
| [SqlPackage.exe를 사용하여 SQL 데이터베이스 호환성 확인](ql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | 이 자습서에서는 SQLPackage.exe 명령줄 유틸리티를 사용하여 Azure SQL 데이터베이스의 호환성을 확인하는 방법을 알아봅니다.|
| [SSMS를 사용하여 SQL 데이터베이스 호환성 확인](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |이 자습서에서는 SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 호환성을 확인하는 방법을 알아봅니다.|
| [Microsoft Azure 데이터베이스에 데이터베이스 배포 마법사를 사용하여 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | 이 자습서에서는 SQL Server Management Studio의 Microsoft Azure 데이터베이스에 데이터베이스 배포 마법사를 사용하여 호환되는 SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 방법을 알아봅니다.
| [SSMS를 사용하여 SQL Server 데이터베이스를 BACPAC 파일로 내보내기](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | 이 자습서에서는 SQL Server Management Studio의 데이터 계층 응용 프로그램 내보내기 마법사를 사용하여 호환 가능한 SQL Server 데이터베이스를 BACPAC 파일로 내보내는 방법을 알아봅니다.|
| [SqlPackage를 사용하여 SQL Server 데이터베이스를 BACPAC 파일로 내보내기](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | 이 자습서에서는 SQLPackage.exe 명령줄 유틸리티를 사용하여 호환되는 SQL Server 데이터베이스를 BACPAC 파일에 내보내는 방법을 알아봅니다.|
| [SSMS를 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | 이 자습서에서는 SQL Server Management Studio의 데이터 계층 응용 프로그램 내보내기 마법사를 사용하여 BACPAC 파일에서 Azure SQL 데이터베이스로 데이터베이스를 가져오는 방법을 알아봅니다. |
| [SqlPackage를 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | 이 자습서에서는 SQLPackage 명령줄 유틸리티를 사용하여 BACPAC 파일에서 Azure SQL 데이터베이스로 데이터베이스를 가져오는 방법을 알아봅니다. |
| [Azure 포털을 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기](sql-database-import.md) | 이 자습서에서는 Azure 포털을 사용하여 Azure Blob에 저장된 BACPAC 파일에서 Azure SQL 데이터베이스로 데이터베이스를 가져오는 방법을 알아봅니다.|
| [PowerShell을 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기](sql-database-import-powershell.md) | 이 자습서에서는 PowerShell을 사용하여 BACPAC 파일에서 Azure SQL 데이터베이스로 데이터베이스를 가져오는 방법을 알아봅니다.|
| [Azure 포털을 사용하여 Azure SQL 데이터베이스 보관](sql-database-export.md#export-your-database) | 이 자습서에서는 Azure 포털을 사용하여 BACPAC 파일에 Azure SQL 데이터베이스를 보관하는 방법을 알아봅니다. |
| [PowerShell을 사용하여 Azure SQL 데이터베이스 보관](sql-database-export-powershell.md) | 이 자습서에서는 PowerShell을 사용하여 BACPAC 파일에 Azure SQL 데이터베이스를 보관하는 방법을 알아봅니다. |
| [Azure 포털을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy.md#copy-your-sql-database) | 이 자습서에서는 Azure 포털을 사용하여 Azure SQL 데이터베이스를 복사하는 방법을 알아봅니다. |
| [PowerShell을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-powershell#copy-your-sql-database) | 이 자습서에서는 PowerShell을 사용하여 Azure SQL 데이터베이스를 복사하는 방법을 알아봅니다. |
| [Transact-SQL을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-transact-sql.md#copy-your-sql-database) | 이 자습서에서는 Transact-SQL을 사용하여 Azure SQL 데이터베이스를 복사하는 방법을 알아봅니다. |
||||

##개발

다음 자습서에서 [SQL 데이터베이스 개발](sql-database-develop-overview.md) 및 [연결 라이브러리](sql-database-libraries.md) 사용에 대해 알아봅니다.

| 자습서 | 설명 |
|---|---|---|
| [.NET(C#)을 사용하여 SQL 데이터베이스에 연결](sql-database-develop-dotnet-simple.md) | 이 자습서에서는 C#을 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다. |
| [Java를 사용하여 SQL 데이터베이스에 연결](sql-database-develop-java-simple.md) | 이 자습서에서는 Java를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다. |
| [Node.js를 사용하여 SQL 데이터베이스에 연결](sql-database-develop-nodejs-simple.md) | 이 자습서에서는 Node.js를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다. |
| [PHP를 사용하여 SQL 데이터베이스에 연결](sql-database-develop-php-simple.md) | 이 자습서에서는 PHP를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다. |
| [Python을 사용하여 SQL 데이터베이스에 연결](sql-database-develop-python-simple.md) | 이 자습서에서는 Python을 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다. |
| [Ruby를 사용하여 SQL 데이터베이스에 연결](sql-database-develop-ruby-simple.md) | 이 자습서에서는 Ruby를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다. |
||||
 
## 데이터베이스 액세스

다음 자습서에서 [로그인 및 사용자 만들기 및 관리](sql-database-manage-logins.md)에 대해 알아봅니다.

| 자습서 | 설명 |
|---|---|---|
| [Azure 포털을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 만들기](sql-database-configure-firewall-settings.md) | 이 자습서에서는 Azure 포털을 사용하여 SQL 데이터베이스 서버 수준 방화벽을 구성하는 방법을 알아봅니다. |
| [Transact-SQL을 사용하여 데이터베이스 수준 방화벽 규칙 만들기](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | 이 자습서에서는 Transact-SQL을 사용하여 데이터베이스 수준 방화벽 규칙을 만드는 방법을 알아봅니다.|
| [Transact-SQL을 사용하여 서버 수준 방화벽 규칙 관리](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | 이 자습서에서는 Transact-SQL을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽을 관리하는 방법을 알아봅니다.|
| [PowerShell을 사용하여 서버 수준 방화벽 규칙 관리](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | 이 자습서에서는 PowerShell을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽을 관리하는 방법을 알아봅니다.|
| [REST API를 사용하여 서버 수준 방화벽 규칙 관리](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | 이 자습서에서는 RESET API를 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽을 관리하는 방법을 알아봅니다.|
| [서버 수준 보안 주체 로그인을 사용하여 AzureSQL 데이터베이스에 연결](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| 이 자습서에서는 서버 수준 보안 주체를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다.|
| [로그인에 데이터베이스 액세스 권한 부여](sql-database-manage-logins.md#granting-database-access-to-a-login() | 이 자습서에서는 서버 수준 로그인에 데이터베이스 액세스 권한을 부여하는 방법을 알아봅니다.|
| [SSMS를 사용하여 새 데이터베이스 사용자 만들기](sql-database-get-started-security.md#create-new-database-user-using-ssms) | 이 자습서에서는 SSMS를 사용하여 기존 데이터베이스에서 새 데이터베이스 사용자를 만드는 방법을 알아봅니다.|
| [새 데이터베이스 사용자 db\_owner 권한 부여](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | 이 자습서에서는 기존 데이터베이스 사용자 db\_owner 권한을 부여하는 방법을 알아봅니다.|
| [사용자로 Azure SQL 데이터베이스에 연결](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | 이 자습서에서는 데이터베이스 수준 사용자 계정을 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다.|
||||


## 데이터 보안

다음 자습서에서 [Azure SQL 데이터베이스 데이터 보호](sql-database-security.md)에 대해 알아봅니다.

| 자습서 | 설명 |
|---|---|---|
| [Azure 포털을 사용하여 데이터베이스에 대한 위협 검색 사용](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | 이 자습서에서는 데이터베이스에 대해 Azure 포털에서 위협 검색을 설정하는 방법을 알아봅니다.|
| [상시 암호화를 사용하여 중요한 데이터 보호](sql-database-always-encrypted-azure-key-vault.md) | 이 자습서에서는 상시 암호화 마법사를 사용하여 Azure SQL 데이터베이스의 중요한 데이터를 보호합니다.|
| [투명한 데이터 암호화를 사용하여 중요한 데이터 보호](https://msdn.microsoft.com/library/dn948096.aspx)| 이 자습서에서는 투명한 데이터 암호화를 사용하여 중요한 데이터를 보호하는 방법을 알아봅니다.|
| [데이터의 열 암호화](https://msdn.microsoft.com/library/ms179331.aspx)| 이 자습서에서는 Transact-SQL을 사용하여 데이터의 열을 암호화하는 방법을 알아봅니다.|
| [동적 데이터 마스킹 설정](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal) | 이 자습서에서는 Azure SQL 데이터베이스에 대한 동적 데이터 마스킹을 설정하는 방법을 알아봅니다. |
||||

## 무중단 업무 방식 및 쿼리 확장

다음 자습서에서 무중단 업무 방식 및 쿼리 확장을 위해 [지역 복원 및 활성 지역 복제](sql-database-business-continuity.md)를 사용하여 오류를 복구하는 방법에 대해 알아봅니다.

| 자습서 | 설명 |
|---|---|---|
| [Azure 포털을 사용하여 이전 시점으로 Azure SQL 데이터베이스 복원](sql-database-point-in-time-restore-portal.md)| 이 자습서에서는 Azure 포털을 사용하여 이전 시점으로 데이터베이스를 복원하는 방법을 알아봅니다.|
| [PowerShell을 사용하여 이전 시점으로 Azure SQL 데이터베이스 복원](sql-database-point-in-time-restore-powershell.md) | 이 자습서에서는 PowerShell을 사용하여 이전 시점으로 데이터베이스를 복원하는 방법을 알아봅니다.|
| [Azure 포털을 사용하여 삭제된 Azure SQL 데이터베이스 복원](sql-database-restore-deleted-database-portal.md) | 이 자습서에서는 Azure 포털을 사용하여 삭제된 데이터베이스를 복원하는 방법을 알아봅니다.|
| [PowerShell을 사용하여 삭제된 Azure SQL 데이터베이스 복원](sql-database-restore-deleted-database-powershell.md) | 이 자습서에서는 PowerShell을 사용하여 삭제된 데이터베이스를 복원하는 방법을 알아봅니다.|
| [Azure 포털을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-portal.md)| 이 자습서에서는 Azure 포털을 사용하여 활성 지역 복제를 구성하는 방법을 알아봅니다.|
| [PowerShell을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-powershell.md)| 이 자습서에서는 Transact-SQL을 사용하여 활성 지역 복제를 구성하는 방법을 알아봅니다.|
| [TRANSACT-SQL을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-transact-sql.md)| 이 자습서에서는 Transact-SQL을 사용하여 활성 지역 복제를 구성하는 방법을 알아봅니다.|
| [Azure 포털을 사용하여 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작](sql-database-geo-replication-failover-portal.md) | 이 자습서에서는 Azure 포털을 사용하여 지역에서 복제된 보조 복제본으로 장애 조치(failover)하는 방법을 알아봅니다.|
| [PowerShell을 사용하여 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작](sql-database-geo-replication-failover-powershell.md) | 이 자습서에서는 PowerShell을 사용하여 지역에서 복제된 보조 복제본으로 장애 조치(failover)하는 방법을 알아봅니다.|
| [Transact-SQL을 사용하여 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작](sql-database-geo-replication-failover-transact-sql.md) | 이 자습서에서는 Transact-SQL을 사용하여 지역에서 복제된 보조 복제본으로 장애 조치(failover)하는 방법을 알아봅니다.|
||||

## 데이터 동기화

이 자습서에서는 [데이터 동기화](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)에 대해 알아봅니다.

| 자습서 | 설명 |
|---|---|---| 
| [Azure SQL 데이터 동기화 시작(미리 보기)](sql-database-get-started-sql-data-sync.md) | 이 자습서에서는 Azure 클래식 포털을 사용하여 Azure SQL 데이터 동기화의 기본 사항에 대해 알아봅니다. |
||||

## 다음 단계

[Azure SQL 데이터베이스 솔루션 빠른 시작 탐색](sql-database-solution-quick-starts.md)

<!---HONumber=AcomDC_0824_2016-->