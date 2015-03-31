<properties 
	pageTitle="최신 SQL 데이터베이스 업데이트 V12(미리 보기)에서 데이터베이스 만들기" 
	description="최신 SQL 데이터베이스 업데이트 V12(미리 보기)에서 데이터베이스 만들기" 
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


# 최신 SQL 데이터베이스 업데이트 V12(미리 보기)에서 데이터베이스 만들기

Microsoft Azure에서 차세대 SQL 데이터베이스를 활용하려면 최신 SQL 데이터베이스 업데이트 V12에 [등록](https://portal.azure.com)하세요. 작업을 시작하려면 Microsoft Azure를 구독해야 합니다. [무료 Azure 평가판](http://azure.microsoft.com/pricing/free-trial)에 등록하고 [가격](http://azure.microsoft.com/pricing/details/sql-database) 정보를 검토하세요. 


| 데이터베이스 만들기 | 스크린샷 |
| :--- | :--- |
| 1. [http://portal.azure.com/](http://portal.azure.com/)에 로그인합니다. | ![New Azure Portal][1] |
| 2. 페이지 맨 아래 왼쪽에서 **새로 만들기**를 클릭합니다. | ![Initiate New service][2]|
| 3. **SQL 데이터베이스**를 클릭합니다.| ![Different services to select from][3] |
| 4. **SQL 데이터베이스** 블레이드가 열립니다. **이름** 필드에서 데이터베이스 이름을 지정합니다. | ![Name the database][4] |
| 5. **SQL 데이터베이스 블레이드**에서 **서버**를 클릭합니다. 두 가지 선택 사항을 제공하는 **서버** 블레이드가 열립니다. 새 서버를 만들거나 기존 서버를 사용할 수 있습니다.| ![select type of server][4] |
|5a. **기존 서버 사용** 옵션을 선택하는 경우 원하는 서버를 선택하고 **선택**을 클릭합니다. 그런 다음 6단계부터 모든 작업을 완료합니다.| ![select a server from the list][5]| 
|5b.   **새 서버 만들기**를 선택하면 **새 서버** 블레이드가 열립니다. 서버 이름, 서버 관리자 로그인 및 암호를 지정합니다. **위치**를 클릭하여   서버 위치를 선택합니다. | ![Complete create new server options][9]| 
|5c. **새 서버** 블레이드에서는 V12 업데이트를 사용하여 새 서버를 만드는 옵션이 제공됩니다. V12 서버의 기능에 대한 자세한 내용은 [Azure SQL 데이터베이스의 새로운 기능](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)을 검토하세요.| ![Select V12 server][6]|
|5d. **새 서버** 블레이드에서 원하는 항목을 선택하고 **확인**을 클릭합니다. 그러면 **SQL 데이터베이스** 블레이드가 다시 표시되고, 여기서 나머지 작업을 완료해 데이터베이스를 만들 수 있습니다. | ![Complete New Server blade actions][8]|
|6. **소스 선택**을 클릭합니다. 데이터베이스를 만들기 위해 선택할 수 있는 소스 형식은 빈 데이터베이스, 샘플 데이터베이스 또는 데이터베이스의 백업입니다.| ![Select the source for the database][10]|
|7. 다음으로 **SQL 데이터베이스** 블레이드에서 **가격 계층**을 클릭합니다. 권장 가격 계층을 선택하거나 사용 가능한 모든 가격 계층을 찾을 수 있습니다. 항목을 선택한 후 **선택**을 클릭합니다. <p> 가격 계층에 대한 자세한 내용은 [SQL 데이터베이스 Web/Business 데이터베이스를 새 서비스 계층으로 업그레이드](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) 및 [Azure SQL 데이터베이스 서비스 계층 및 성능 수준](http://msdn.microsoft.com/library/azure/dn741336.aspx)을 참조하세요. |![Select a pricing tier][7]
| 8. 다음으로 **SQL 데이터베이스** 블레이드에서 **옵션 구성**을 클릭하고 원하는 항목을 선택한 후에 **확인**을 클릭합니다. 
| 9. **SQL 데이터베이스** 블레이드에서 **리소스 그룹**을 클릭합니다. 새 리소스 그룹을 만들거나 목록에서 기존 리소스 그룹을 선택합니다. **확인**을 클릭합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups)를 검토하세요.|![Specify Resource group][11]
| 10. **SQL 데이터베이스** 블레이드에서 **구독**을 클릭합니다. 그러면 **구독** 블레이드가 열립니다. 선택한 서버를 지원하는 Azure 구독을 선택합니다. 선택한 구독에 따라 서버 설정은 변경될 수도 있습니다.| ![Select subscription.][13]
| 11. **만들기**를 클릭합니다. SQL 데이터베이스 업데이트 V12 기능이 있는 새 데이터베이스가 생성됩니다. |![Creates a new database][12]

# 관련 링크  #

-  [Azure SQL 데이터베이스의 새로운 기능](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Azure SQL 데이터베이스 V12로 업그레이드 계획 및 준비](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)

<!--Image references-->
[1]: ./media/sql-database-preview-create/firstscreenportal.png
[2]: ./media/sql-database-preview-create/new.png
[3]: ./media/sql-database-preview-create/sqldatabase.png
[4]: ./media/sql-database-preview-create/databasename.png
[5]: ./media/sql-database-preview-create/useexistingserver.PNG
[6]: ./media/sql-database-preview-create/v12server.PNG
[7]: ./media/sql-database-preview-create/pricingtierdetails.png
[8]: ./media/sql-database-preview-create/finishnewserverblade.png
[9]: ./media/sql-database-preview-create/createnewserver.png
[10]: ./media/sql-database-preview-create/selectsource.png
[11]: ./media/sql-database-preview-create/resourcegroup.png
[12]: ./media/sql-database-preview-create/create.png
[13]: ./media/sql-database-preview-create/subscription.PNG


<!--HONumber=47-->
