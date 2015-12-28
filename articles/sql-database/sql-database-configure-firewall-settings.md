<properties
	pageTitle="방법: 방화벽 설정 구성 | Microsoft Azure"
	description="Azure SQL 데이터베이스에 액세스하는 IP 주소에 대한 방화벽을 구성하는 방법을 알아봅니다."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="12/16/2015"
	ms.author="rickbyh"/>


# 방법: Azure 포털을 사용하여 SQL 데이터베이스에 방화벽 설정 구성


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


SQL 데이터베이스는 서버와 데이터베이스에 대한 연결을 허용하는 방화벽 규칙을 사용합니다. 선택적으로 데이터베이스에 대한 액세스를 허용하도록 SQL 데이터베이스 논리 서버에서 master 데이터베이스 또는 사용자 데이터베이스에 대한 서버 수준 및 데이터베이스 수준 방화벽 설정을 정의할 수 있습니다.

> [AZURE.IMPORTANT]Azure에서 응용 프로그램 데이터베이스 서버에 연결할 수 있게 하려면 Azure 연결을 사용하도록 설정해야 합니다. 방화벽 규칙 및 Azure의 연결을 사용하도록 설정 하는 방법에 대한 자세한 내용은 [Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)을 참조하세요. Azure 클라우드 경계 내에서 연결하는 경우 일부 TCP 포트를 추가로 열어야 할 수도 있습니다. 자세한 내용은 [ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)의 **SQL 데이터베이스의 V12: 내부 vs 외부** 섹션을 참조하세요.


### Azure 포털을 통해 서버 수준 방화벽 규칙 관리


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## 서버 수준 방화벽 규칙 관리 

1. Azure 포털에서 **SQL 데이터베이스**를 클릭합니다. 모든 데이터베이스 및 해당 하는 서버가 여기에 나열 됩니다.
2. 페이지 위쪽에서 **서버**를 클릭합니다.
3. 방화벽 규칙을 관리하려는 서버 옆의 화살표를 클릭 합니다.
4. 페이지 위쪽에서 **구성**을 클릭합니다.

	*  현재 컴퓨터를 추가 하려면 허용된 IP 주소에 추가를 클릭 합니다.
	*  추가 IP 주소를 추가 하려면 규칙 이름, 시작 IP 주소 및 끝 IP 주소를 입력 합니다.
	*  기존 규칙을 수정 하려면 규칙의 필드 중 하나를 클릭 후 변경 합니다.
	*  기존 규칙을 삭제 하려면 행의 끝에서 X가 나타날 때까지 규칙 위로 가져갑니다. 규칙을 제거 하려면 X를 클릭 합니다.
5. 변경 내용을 저장하려면 페이지 맨 아래에서 **저장**을 클릭합니다.


## 다음 단계

데이터베이스 만들기에 대한 자습서는 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하세요. 오픈 소스 또는 타사 응용 프로그램에서 Azure SQL 데이터베이스에 연결하는 방법에 대한 도움말은 [프로그래밍 방식으로 Azure SQL 데이터베이스에 연결하기 위한 지침](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조하세요. 데이터베이스를 탐색하는 방법을 이해하려면 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](https://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_1217_2015-->