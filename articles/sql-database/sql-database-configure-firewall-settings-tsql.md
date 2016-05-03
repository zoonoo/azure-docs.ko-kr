<properties
	pageTitle="방법: SQL 데이터베이스 방화벽 구성 | Microsoft Azure"
	description="Azure SQL 데이터베이스에 액세스하는 IP 주소에 대한 방화벽을 구성하는 방법을 알아봅니다."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="02/04/2016"
	ms.author="rickbyh"/>


# 방법: TSQL을 사용하여 Azure SQL 데이터베이스 방화벽 구성


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL 데이터베이스 서버와 데이터베이스에 대한 연결을 허용 하도록 방화벽 규칙을 사용 합니다. 선택적으로 데이터베이스에 대한 액세스를 허용 하도록 Azure SQL 데이터베이스 서버에서 master 데이터베이스 또는 사용자 데이터베이스에 대한 서버 수준 및 데이터베이스 수준 방화벽 설정을 정의할 수 있습니다.

> [AZURE.IMPORTANT] Azure에서 응용 프로그램 데이터베이스 서버에 연결할 수 있게 하려면 Azure 연결을 사용하도록 설정해야 합니다. 방화벽 규칙 및 Azure의 연결을 사용하도록 설정 하는 방법에 대한 자세한 내용은 [Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)을 참조하세요. Azure 클라우드 경계 내에서 연결하는 경우 일부 TCP 포트를 추가로 열어야 할 수도 있습니다. 자세한 내용은 [ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)의 **SQL 데이터베이스의 V12: 내부 vs 외부** 섹션을 참조하세요.


## Transact-SQL을 통해 서버 수준 방화벽 규칙 관리

1. 클래식 포털 또는 SQL Server Management Studio를 통해 쿼리 창을 시작 합니다.
2. Master 데이터베이스에 연결을 확인 합니다.
3. 서버 수준 방화벽 규칙은 쿼리 창 내에서 선택, 생성, 업데이트 또는 삭제할 수 있습니다.
4. 0서버 수준 방화벽 규칙을 만들거나 업데이트 하려면 sp\_set\_firewall 규칙 저장 프로시저를 실행 합니다. 다음 예제는 Contoso 서버에서 일정 범위의 IP 주소를 사용하도록 설정합니다.<br/>먼저 이미 존재하는 규칙을 확인합니다.

		SELECT * FROM sys.firewall_rules ORDER BY name;

	그런 다음 방화벽 규칙을 추가합니다.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	서버 수준 방화벽 규칙을 삭제 하려면 sp\_delete\_firewall\_rule 저장 프로시저를 실행 합니다. 다음 예제에서는 ContosoFirewallRule 이라는 규칙을 삭제 합니다.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 
## 데이터베이스 수준 방화벽 규칙

1. 사용자의 IP 주소에 대한 서버 수준 방화벽을 만든 후 클래식 포털 또는 SQL Server Management Studio를 통해 쿼리 창을 시작합니다.
2. 데이터베이스 수준 방화벽 규칙을 만들려는 데이터베이스에 연결 합니다.

	기존 데이터베이스 수준 방화벽 규칙을 새로 만들거나 업데이트 하려면 sp\_set\_database\_firewall\_rule 저장 프로시저를 실행 합니다. 다음 예제에서는 ContosoFirewallRule 이라는 새 방화벽 규칙을 만듭니다.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	기존 데이터베이스 수준 방화벽 규칙을 삭제 하려면 sp\_delete\_database\_firewall\_rule 저장 프로시저를 실행 합니다. 다음 예제에서는 ContosoFirewallRule 이라는 규칙을 삭제 합니다.
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'


## 다음 단계

데이터베이스를 만드는 방법에 대한 자습서는 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하세요. 오픈 소스 또는 타사 응용 프로그램에서 Azure SQL 데이터베이스에 연결하는 방법에 대한 도움말은 [프로그래밍 방식으로 Azure SQL 데이터베이스에 연결하기 위한 지침](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조하세요. 데이터베이스를 탐색하는 방법을 이해하려면 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](https://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.

<!---HONumber=AcomDC_0420_2016-->