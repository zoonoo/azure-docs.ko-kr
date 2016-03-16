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
	ms.date="02/04/2016"
	ms.author="rickbyh"/>


# 방법: REST API를 사용하여 SQL 데이터베이스에 방화벽 설정 구성


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL 데이터베이스 서버와 데이터베이스에 대한 연결을 허용 하도록 방화벽 규칙을 사용 합니다. 선택적으로 데이터베이스에 대한 액세스를 허용 하도록 Azure SQL 데이터베이스 서버에서 master 데이터베이스 또는 사용자 데이터베이스에 대한 서버 수준 및 데이터베이스 수준 방화벽 설정을 정의할 수 있습니다.

> [AZURE.IMPORTANT] Azure에서 응용 프로그램 데이터베이스 서버에 연결할 수 있게 하려면 Azure 연결을 사용하도록 설정해야 합니다. 방화벽 규칙 및 Azure의 연결을 사용하도록 설정 하는 방법에 대한 자세한 내용은 [Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)을 참조하세요. Azure 클라우드 경계 내에서 연결하는 경우 일부 TCP 포트를 추가로 열어야 할 수도 있습니다. 자세한 내용은 [ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)의 **SQL 데이터베이스의 V12: 내부 vs 외부** 섹션을 참조하세요.


## REST API를 통해 서버 수준 방화벽 규칙 관리
1. 관리 인증된 REST API를 통해 방화벽 규칙 관리하기. 더 자세한 정보는 관리인증 서비스 관리 요청을 참조합니다.
2. REST API를 사용하여 서버 수준 규칙을 생성, 업데이트, 삭제 할 수 있습니다.

	서버 수준 방화벽 규칙을 생성하거나 업데이트 하려면 다음을 사용하여 POST 메서드를 실행합니다.
 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules
	
	요청 본문

		<ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
		  <Name>ContosoFirewallRule</Name>
		  <StartIPAddress>192.168.1.4</StartIPAddress>
		  <EndIPAddress>192.168.1.10</EndIPAddress>
		</ServiceResource>
 

	서버 수준 방화벽 규칙을 삭제하려면 다음을 사용하여 DELETE 메서드를 실행합니다.
	 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule


## 서비스 관리 REST API를 사용하여 방화벽 규칙 관리

* [방화벽 규칙 만들기](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [방화벽 규칙 삭제](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [방화벽 규칙 가져오기](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [방화벽 규칙 나열](https://msdn.microsoft.com/library/azure/dn505715.aspx)
 
## 다음 단계

데이터베이스를 만드는 방법에 대한 자습서는 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하세요. 오픈 소스 또는 타사 응용 프로그램에서 Azure SQL 데이터베이스에 연결하는 방법에 대한 도움말은 [프로그래밍 방식으로 Azure SQL 데이터베이스에 연결하기 위한 지침](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조하세요. 데이터베이스를 탐색하는 방법을 이해하려면 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](https://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_0211_2016-->