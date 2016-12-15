---
title: "PowerShell을 사용하여 Azure SQL Database 서버 수준 방화벽 규칙 구성 | Microsoft Docs"
description: "Azure SQL 데이터베이스에 액세스하는 IP 주소에 대한 방화벽을 구성하는 방법을 알아봅니다."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 30dcea72-61c1-48b6-8e1d-b1db2eb61567
ms.service: sql-database
ms.custom: auth and access
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 890ca8d9b7a3a76c6584f9d06cc8bdf3b36f87c0


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>PowerShell을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성
> [!div class="op_single_selector"]
> * [개요](sql-database-firewall-configure.md)
> * [Azure 포털](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Azure SQL 데이터베이스는 서버와 데이터베이스에 대한 연결을 허용하는 방화벽 규칙을 사용합니다. 선택적으로 데이터베이스에 대한 액세스를 허용하도록 SQL 데이터베이스 서버에서 master 데이터베이스 또는 사용자 데이터베이스에 대한 서버 수준 및 데이터베이스 수준 방화벽 설정을 정의할 수 있습니다.

> [!IMPORTANT]
> Azure에서 응용 프로그램 데이터베이스 서버에 연결할 수 있게 하려면 Azure 연결을 사용하도록 설정해야 합니다. 방화벽 규칙 및 Azure의 연결을 사용하도록 설정 하는 방법에 대한 자세한 내용은 [Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)을 참조하세요. Azure 클라우드 경계 내에서 연결하는 경우 일부 TCP 포트를 추가로 열어야 할 수도 있습니다. 자세한 내용은 [ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)의 "SQL 데이터베이스의 V12: 내부 vs 외부" 섹션을 참조하세요.
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>서버 방화벽 규칙 만들기
Azure PowerShell을 사용하여 서버 수준 방화벽 규칙을 생성, 업데이트, 삭제할 수 있습니다.

새 서버 수준 방화벽 규칙을 만들려면 [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) cmdlet을 실행합니다. 다음 예제는 Contoso 서버에서 일정 범위의 IP 주소를 사용하도록 설정합니다.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'        

기존의 서버 수준 방화벽 규칙을 수정하려면 [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx) cmdlet을 실행합니다. 다음 예제에서는 ContosoFirewallRule라고 명명된 규칙에 대한 허용 가능한 IP 주소의 범위를 변경 합니다.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –FirewallRuleName 'ContosoFirewallRule' –ServerName 'Contoso'

기존의 서버 수준 방화벽 규칙을 삭제하려면 [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx) cmdlet을 실행합니다. 다음 예제에서는 ContosoFirewallRule 이라는 규칙을 삭제 합니다.

    Remove-AzureRmSqlServerFirewallRule –FirewallRuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>PowerShell을 사용하여 방화벽 규칙 관리
PowerShell을 사용하여 방화벽 규칙을 관리할 수도 있습니다. 자세한 내용은 다음 항목을 참조하십시오.

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586\(v=azure.300\).aspx)

## <a name="next-steps"></a>다음 단계
Transact-SQL을 사용하여 서버 수준 및 데이터베이스 수준 방화벽 규칙을 만드는 방법에 대한 자세한 내용은 [T-SQL을 사용하여 Azure SQL 데이터베이스 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-tsql.md)을 참조하세요.

다른 방법을 사용하여 서버 수준 방화벽 규칙을 만드는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 포털을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings.md)
* [REST API를 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-rest.md)

데이터베이스를 만드는 방법에 대한 자습서는 [Azure 포털을 사용하여 빠르게 SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하세요.
오픈 소스 또는 타사 응용 프로그램에서 Azure SQL 데이터베이스에 연결하는 방법에 대한 도움말은 [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조하세요.
데이터베이스를 탐색하는 방법을 이해하려면 [데이터베이스 및 로그인 보안 관리](https://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스
* [데이터베이스 보안 설정](sql-database-security.md)
* [SQL Server 데이터베이스 엔진 및 Azure SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->



<!--HONumber=Dec16_HO2-->


