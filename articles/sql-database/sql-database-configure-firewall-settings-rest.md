---
title: "REST API: Azure SQL Database 서버 수준 방화벽 규칙 | Microsoft Docs"
description: "REST API를 사용하여 Azure SQL 데이터베이스에 액세스하는 IP 주소에 대한 서버 수준 방화벽 규칙을 구성하는 방법에 대해 알아봅니다."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: fc874f3c-c623-4924-8cb7-32a8c31e666c
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: 6ac9bdf34e8cf0a8ca8e06ae3ed6cd2dd1cf408b


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>REST API를 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성
> [!div class="op_single_selector"]
> * [개요](sql-database-firewall-configure.md)
> * [Azure 포털](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Microsoft Azure SQL 데이터베이스 서버와 데이터베이스에 대한 연결을 허용 하도록 방화벽 규칙을 사용 합니다. 선택적으로 데이터베이스에 대한 액세스를 허용 하도록 Azure SQL 데이터베이스 서버에서 master 데이터베이스 또는 사용자 데이터베이스에 대한 서버 수준 및 데이터베이스 수준 방화벽 설정을 정의할 수 있습니다.

> [!IMPORTANT]
> Azure에서 응용 프로그램 데이터베이스 서버에 연결할 수 있게 하려면 Azure 연결을 사용하도록 설정해야 합니다. 방화벽 규칙 및 Azure의 연결을 사용하도록 설정 하는 방법에 대한 자세한 내용은 [Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)을 참조하세요. Azure 클라우드 경계 내에서 연결하는 경우 일부 TCP 포트를 추가로 열어야 할 수도 있습니다. 자세한 내용은 **ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트** 의 [SQL 데이터베이스의 V12: 내부 vs 외부](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="manage-server-level-firewall-rules-through-rest-api"></a>REST API를 통해 서버 수준 방화벽 규칙 관리
1. 관리 인증된 REST API를 통해 방화벽 규칙 관리하기. 자세한 내용은 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](../azure-resource-manager/resource-manager-api-authentication.md)를 참조하세요.
2. REST API를 사용하여 서버 수준 규칙을 생성, 업데이트, 삭제 할 수 있습니다.
   
    서버 수준 방화벽 규칙을 생성하거나 업데이트 하려면 다음을 사용하여 PUT 메서드를 실행합니다.
   
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
   
    요청 본문
   
        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 

    서버 수준 방화벽 규칙을 삭제하려면 다음을 사용하여 DELETE 메서드를 실행합니다.

        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>REST API를 사용하여 방화벽 규칙 관리
* [방화벽 규칙 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [방화벽 규칙 삭제](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [방화벽 규칙 가져오기](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [모든 방화벽 규칙 나열](https://msdn.microsoft.com/library/azure/mt604478.aspx)

## <a name="next-steps"></a>다음 단계
Transact-SQL을 사용하여 서버 수준 및 데이터베이스 수준 방화벽 규칙을 만드는 방법에 대한 방법 문서를 보려면 [T-SQL을 사용하여 Azure SQL 데이터베이스 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-tsql.md)을 참조하세요. 

다른 방법을 사용한 서버 수준 방화벽 규칙 만들기에 대한 방법 문서를 보려면 다음을 참조하세요. 

* [Azure 포털을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings.md)
* [PowerShell을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-powershell.md)

데이터베이스를 만드는 방법에 대한 자습서는 [Azure 포털을 사용하여 빠르게 SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하세요.
오픈 소스 또는 타사 응용 프로그램에서 Azure SQL 데이터베이스에 연결하는 방법에 대한 도움말은 [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조하세요.
데이터베이스를 탐색하는 방법을 이해하려면 [데이터베이스 및 로그인 보안 관리](https://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스
* [데이터베이스 보안 설정](sql-database-security-overview.md)
* [SQL Server 데이터베이스 엔진 및 Azure SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->





<!--HONumber=Feb17_HO2-->


