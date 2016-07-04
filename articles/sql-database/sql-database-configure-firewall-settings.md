<properties
	pageTitle="SQL 데이터베이스 서버 수준 방화벽 규칙 구성 | Microsoft Azure"
	description="Azure SQL 서버에 액세스하는 IP 주소에 방화벽을 구성하는 방법을 알아봅니다."
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
	ms.date="06/10/2016"
	ms.author="rickbyh;carlrab"/>


# Azure 포털을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성


> [AZURE.SELECTOR]
- [개요](sql-database-firewall-configure.md)
- [Azure 포털](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)

Azure SQL Server는 서버와 데이터베이스에 대한 연결을 허용하는 방화벽 규칙을 사용합니다. 선택적으로 데이터베이스에 대한 액세스를 허용하도록 Azure SQL Server 논리 서버에서 마스터 데이터베이스 또는 사용자 데이터베이스에 대한 서버 수준 및 데이터베이스 수준 방화벽 설정을 정의할 수 있습니다. 이 항목에서는 서버 수준 방화벽 규칙에 대해 설명합니다.

> [AZURE.IMPORTANT] Azure에서 응용 프로그램을 Azure SQL Server에 연결할 수 있게 하려면 Azure 연결을 사용하도록 설정해야 합니다. 방화벽 규칙의 작동 방식을 이해하려면 [Azure SQL server 방화벽을 구성하는 방법 - 개요](sql-database-firewall-configure.md)를 참조하세요. Azure 클라우드 경계 내에서 연결하는 경우 일부 TCP 포트를 추가로 열어야 할 수도 있습니다. 자세한 내용은 [ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)의 **SQL 데이터베이스의 V12: 내부 vs 외부** 섹션을 참조하세요.

**권장 사항:** 관리자의 경우 서버 수준 방화벽 규칙을 사용하면 동일한 액세스를 요구하는 데이터베이스가 많을 때 각 데이터베이스를 개별적으로 구성할 필요가 없습니다. Microsoft는 보안을 강화하고 데이터베이스의 휴대성이 높아질수록 데이터베이스 수준 방화벽을 사용하도록 권장합니다.

[AZURE.INCLUDE [SQL 데이터베이스 데이터베이스 만들기](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Azure 포털을 통해 기존 서버 수준 방화벽 규칙 관리

서버 수준 방화벽 규칙을 관리하는 단계를 반복합니다.

- 현재 컴퓨터를 추가하려면 클라이언트 IP 추가를 클릭합니다.
- 추가 IP 주소를 추가 하려면 규칙 이름, 시작 IP 주소 및 끝 IP 주소를 입력 합니다.
- 기존 규칙을 수정 하려면 규칙의 필드 중 하나를 클릭 후 변경 합니다.
- 기존 규칙을 삭제 하려면 행의 끝에서 X가 나타날 때까지 규칙 위로 가져갑니다. 규칙을 제거 하려면 X를 클릭 합니다.

**저장**을 클릭하여 변경 내용을 저장합니다.

## 다음 단계

Transact-SQL을 사용하여 서버 수준 및 데이터베이스 수준 방화벽 규칙을 만드는 방법에 대한 방법 문서를 보려면 [T-SQL을 사용하여 Azure SQL 데이터베이스 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-tsql.md)을 참조하세요.

다른 방법을 사용한 서버 수준 방화벽 규칙 만들기에 대한 방법 문서를 보려면 다음을 참조하세요.

- [PowerShell을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-powershell.md)
- [REST API를 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-rest.md)

데이터베이스를 만드는 방법에 대한 자습서는 [Azure 포털을 사용하여 빠르게 SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하세요. 오픈 소스 또는 타사 응용 프로그램에서 Azure SQL 데이터베이스에 연결하는 방법에 대한 도움말은 [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조하세요. 데이터베이스를 탐색하는 방법을 이해하려면 [데이터베이스 및 로그인 보안 관리](https://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.


## 추가 리소스

- [데이터베이스 보안 설정](sql-database-security.md)
- [SQL Server 데이터베이스 엔진 및 Azure SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0622_2016-->