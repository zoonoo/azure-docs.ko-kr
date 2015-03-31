<properties 
	pageTitle="최신 SQL 데이터베이스 업데이트 V12(미리 보기)로 업그레이드" 
	description="최신 SQL 데이터베이스 업데이트 V12(미리 보기)로 업그레이드" 
	services="sql-database" 
	documentationCenter="" 
	authors="sonalmm" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="12/11/2014" 
	ms.author="sonalm"/>



# 최신 SQL 데이터베이스 업데이트 V12(미리 보기)로 업그레이드


Microsoft Azure에서 차세대 SQL 데이터베이스를 활용하려면 최신 SQL 데이터베이스 업데이트 V12에 [등록](https://portal.azure.com)  하세요. 먼저 Microsoft Azure를 구독해야 합니다. [무료 Azure 평가판](http://azure.microsoft.com/pricing/free-trial)에 등록하고 [가격](http://azure.microsoft.com/pricing/details/sql-database) 정보를 검토하세요. 

## 최신 SQL 데이터베이스 업데이트 기능을 사용하여 서버를 바로 업그레이드하는 방법 ##

| 업그레이드  | 스크린샷 |
| :--- | :--- |
| 1. [http://portal.azure.com/](http://portal.azure.com/)에 로그인합니다. | ![New Azure Portal][1] |
| 2. **찾아보기**를 클릭합니다. | ![Browse Services][2] |
| 3.	**SQL Server**를 클릭합니다. SQL Server 이름 목록이 표시됩니다. | ![Select SQL Server service][3] |
| 4. SQL 데이터베이스 업데이트를 사용하여 새 서버로 복사할 서버를 선택합니다. | ![Shows a list of SQL Servers][4] |
| 5. **최신 SQL 데이터베이스 업데이트 V12**를 클릭합니다. | ![Latest preview feature][5] |
| 6. **이 서버 업그레이드**를 클릭합니다. | ![Upgrades the SQL Server to the preview][6] |

> [AZURE.NOTE] **중요**: 업그레이드 옵션을 선택하면 서버와 해당 서버 내의 데이터베이스에서 SQL 데이터베이스 업데이트 V12 기능이 사용되며, 이 설정은 되돌릴 수 없습니다. 서버를 SQL 데이터베이스 업데이트 V12로 업그레이드하려면 기본, 표준 또는 프리미엄 서비스 계층이 필요합니다. 서비스 계층에 대한 자세한 내용은 [SQL 데이터베이스 Web/Business 데이터베이스를 새 서비스 계층으로 업그레이드](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)를 참조하세요.

> **중요**: 지역에서 복제는 SQL 데이터베이스 업데이트 V12(미리 보기)에서 지원되지 않습니다. 자세한 내용은 [Azure SQL 데이터베이스 V12 미리 보기로 업그레이드 계획 및 준비](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade)를 참조하세요.


**이 서버 업그레이드** 옵션을 클릭하면 열리는 블레이드에 유효성 검사 프로세스에 대한 메시지가 표시됩니다. 

- 유효성 검사 프로세스에서는 데이터베이스의 서비스 계층을 확인하고 지역에서 복제를 사용할 수 있는지 여부를 확인합니다. 유효성 검사가 완료되면 블레이드에 결과가 표시됩니다. 
- 유효성 검사 프로세스가 완료된 후 SQL 데이터베이스 업데이트 V12로의 업그레이드 요구 사항을 충족하기 위해 작업을 수행해야 하는 데이터베이스 이름 목록이 표시됩니다. **각 데이터베이스에 대해 작업을 완료해야 SQL 데이터베이스 업데이트 V12로 업그레이드할 수 있습니다**.
- 각 데이터베이스 이름을 클릭하면 새 블레이드에서 현재 사용을 기반으로 하여 권장 서비스 가격 계층을 제공합니다. 다양한 가격 계층을 찾아보고 환경에 가장 적합한 가격 계층을 선택할 수도 있습니다. 복제를 중지하려면 지역에서 복제가 설정된 모든 데이터베이스를 다시 구성해야 합니다. 
- 데이터가 충분하지 않으면 권장 가격 계층이 표시되지 않습니다. 

| 작업 | 스크린샷 |
| :--- | :--- |
| 7. 업그레이드를 위해 서버를 준비하는 작업을 완료한 후 업그레이드할 서버의 이름을 입력하고 **확인**을 클릭합니다. | ![Confirm the server name to upgrade][7] |
| 8. 업그레이드 프로세스가 시작됩니다. 업그레이드를 수행하는 데 최대 24시간이 걸릴 수 있습니다. 이 시간 동안 이 서버의 모든 데이터베이스가 온라인 상태로 유지되지만 데이터베이스 관리 작업이 제한됩니다. 프로세스가 완료되면 **사용** 상태가 서버 블레이드에 표시됩니다. | ![Confirms preview features are enabled][8] |
 

# 관련 링크  #

-  [SQL 데이터베이스 업데이트 V12(미리 보기)의 새로운 기능](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [최신 SQL 데이터베이스 업데이트 V12(미리 보기)로 업그레이드 계획 및 준비](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)


<!--Image references-->
[1]: ./media/sql-database-preview-upgrade/firstscreenportal.png
[2]: ./media/sql-database-preview-upgrade/browse.png
[3]: ./media/sql-database-preview-upgrade/sqlserver.png
[4]: ./media/sql-database-preview-upgrade/sqlserverlist.png
[5]: ./media/sql-database-preview-upgrade/latestprview.png
[6]: ./media/sql-database-preview-upgrade/upgrade.png
[7]: ./media/sql-database-preview-upgrade/typeservername.png
[8]: ./media/sql-database-preview-upgrade/enabled.png
<!--HONumber=47-->
