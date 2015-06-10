<properties 
	pageTitle="SQL 데이터베이스 V12로 업그레이드" 
	description="Azure SQL 데이터베이스의 이전 버전에서 Azure SQL 데이터베이스 V12로 업그레이드하는 방법을 설명합니다." 
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
	ms.date="05/15/2015" 
	ms.author="sonalm"/>


# SQL 데이터베이스 V12로 업그레이드 준비


Microsoft Azure에서 차세대 SQL 데이터베이스를 활용하려면 SQL 데이터베이스 V12에 [등록](https://portal.azure.com)하세요. 먼저 Microsoft Azure를 구독해야 합니다. [무료 Azure 평가판](http://azure.microsoft.com/pricing/free-trial)에 등록하고 [가격](http://azure.microsoft.com/pricing/details/sql-database) 정보를 검토하세요.


## SQL 데이터베이스 V12로 업그레이드하는 단계


| 업그레이드 단계 | 스크린샷 |
| :--- | :--- |
| 1. [http://portal.azure.com/](http://portal.azure.com/)에 로그인합니다. | ![새 Azure 포털][1] |
| 2. **찾아보기**를 클릭합니다. | ![서비스 찾아보기][2] |
| 3. **SQL Server**를 클릭합니다. SQL Server 이름 목록이 표시됩니다. | ![SQL Server 서비스 선택][3] |
| 4. SQL 데이터베이스 업데이트를 사용하여 새 서버로 복사할 서버를 선택합니다. | ![SQL Server 목록 표시][4] |
| 5. **최신 SQL 데이터베이스 업데이트 V12**를 클릭합니다. | ![최신 미리 보기 기능][5] |
| 6. **이 서버 업그레이드**를 클릭합니다. | ![미리 보기로 SQL Server 업그레이드][6] |


> [AZURE.NOTE]업그레이드 옵션을 선택하면 서버와 해당 서버 내의 데이터베이스에서 SQL 데이터베이스 V12 기능이 사용되며, 이 설정은 되돌릴 수 없습니다. 서버를 SQL 데이터베이스 V12로 업그레이드하려면 기본, 표준 또는 프리미엄 서비스 계층이 필요합니다. 서비스 계층에 대한 자세한 내용은 [SQL 데이터베이스 Web/Business 데이터베이스를 새 서비스 계층으로 업그레이드](sql-database-upgrade-new-service-tiers.md)를 참조하세요.


> [AZURE.IMPORTANT]지역에서 복제는 SQL 데이터베이스 V12(미리 보기)에서 지원되지 않습니다. 자세한 내용은 [Azure SQL 데이터베이스 V12 미리 보기로 업그레이드 계획 및 준비](sql-database-v12-plan-prepare-upgrade.md)를 참조하세요.


**이 서버 업그레이드** 옵션을 클릭하면 열리는 블레이드에 유효성 검사 프로세스에 대한 메시지가 표시됩니다.


- 유효성 검사 프로세스에서는 데이터베이스의 서비스 계층을 확인하고 지역에서 복제를 사용할 수 있는지 여부를 확인합니다. 유효성 검사가 완료되면 블레이드에 결과가 표시됩니다. 
- 유효성 검사 프로세스가 완료된 후 SQL 데이터베이스 V12로의 업그레이드 요구 사항을 충족하기 위해 작업을 수행해야 하는 데이터베이스 이름 목록이 표시됩니다.
 - **각 데이터베이스에 대해 작업을 완료해야 SQL 데이터베이스 V12로 업그레이드할 수 있습니다**.
- 각 데이터베이스 이름을 클릭하면 새 블레이드에서 현재 사용을 기반으로 하여 권장 서비스 가격 계층을 제공합니다. 다양한 가격 계층을 찾아보고 환경에 가장 적합한 가격 계층을 선택할 수도 있습니다. 복제를 중지하려면 지역에서 복제가 설정된 모든 데이터베이스를 다시 구성해야 합니다. 
- 데이터가 충분하지 않으면 권장 가격 계층이 표시되지 않습니다. 


| 작업 | 스크린샷 |
| :--- | :--- |
| 7. 업그레이드를 위해 서버를 준비하는 작업을 완료한 후 업그레이드할 서버의 이름을 입력하고 **확인**을 클릭합니다. | ![업그레이드할 서버 이름 확인][7] |
| 8. 업그레이드 프로세스가 시작됩니다. 업그레이드를 수행하는 데 최대 24시간이 걸릴 수 있습니다. 이 시간 동안 이 서버의 모든 데이터베이스가 온라인 상태로 유지되지만 데이터베이스 관리 작업이 제한됩니다. 프로세스가 완료되면 **사용** 상태가 서버 블레이드에 표시됩니다. | ![미리 보기 기능이 활성화되었는지 확인][8] |


## Powershell cmdlet


Powershell cmdlet을 사용하여 V11 또는 기타 V12 이전 버전에서 Azure SQL 데이터베이스 V12로의 업그레이드를 시작, 중지 또는 모니터링할 수 있습니다.


이러한 Powershell cmdlet에 대한 참조 설명서는 다음을 참조하세요.


- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)


Stop- cmdlet은 취소를 의미하는 것으로, 일시 중지가 아닙니다. 처음부터 다시 시작하는 것 외에 업그레이드를 다시 시작할 수 있는 방법은 없습니다. Stop- cmdlet은 해당하는 모든 리소스를 정리하고 해제합니다.


## 관련 링크

-  [SQL 데이터베이스 V12의 새로운 기능](sql-database-v12-whats-new.md)
- [SQL 데이터베이스 V12로 업그레이드 계획 및 준비](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-preview-upgrade/firstscreenportal.png
[2]: ./media/sql-database-preview-upgrade/browse.png
[3]: ./media/sql-database-preview-upgrade/sqlserver.png
[4]: ./media/sql-database-preview-upgrade/sqlserverlist.png
[5]: ./media/sql-database-preview-upgrade/latestprview.png
[6]: ./media/sql-database-preview-upgrade/upgrade.png
[7]: ./media/sql-database-preview-upgrade/typeservername.png
[8]: ./media/sql-database-preview-upgrade/enabled.png

<!---HONumber=58-->