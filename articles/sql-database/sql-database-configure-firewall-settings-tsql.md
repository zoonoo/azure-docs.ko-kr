---
title: "T-SQL: Azure SQL Database 방화벽 규칙 | Microsoft Docs"
description: "Transact-SQL을 사용하여 Azure SQL 데이터베이스에 액세스하는 IP 주소에 대한 서버 수준 및 데이터베이스 수준 방화벽 규칙을 구성하는 방법에 대해 알아봅니다."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/07/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 84efba61fbb0248edef3036de702e3a1aa8ae903
ms.lasthandoff: 02/17/2017


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>T-SQL을 사용하여 Azure SQL 데이터베이스 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성

Microsoft Azure SQL 데이터베이스 서버와 데이터베이스에 대한 연결을 허용 하도록 방화벽 규칙을 사용 합니다. 선택적으로 데이터베이스에 대한 액세스를 허용 하도록 Azure SQL 데이터베이스 서버에서 master 데이터베이스 또는 사용자 데이터베이스에 대한 서버 수준 및 데이터베이스 수준 방화벽 설정을 정의할 수 있습니다.

> [!IMPORTANT]
> Azure에서 응용 프로그램 데이터베이스 서버에 연결할 수 있게 하려면 Azure 연결을 사용하도록 설정해야 합니다. 방화벽 규칙 및 Azure의 연결을 사용하도록 설정 하는 방법에 대한 자세한 내용은 [Azure SQL 데이터베이스 방화벽](sql-database-firewall-configure.md)을 참조하세요. Azure 클라우드 경계 내에서 연결하는 경우 일부 TCP 포트를 추가로 열어야 할 수도 있습니다. 자세한 내용은 **ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트** 의 [SQL 데이터베이스의 V12: 내부 vs 외부](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="server-level-firewall-rules"></a>서버 수준 방화벽 규칙
Azure SQL 서버 관리자 로그인 또는 Azure Active Directory 관리자만이 Transact-SQL을 사용하여 서버 수준 방화벽 규칙을 만들 수 있습니다.

1. SQL Server Management Studio를 사용하여 쿼리 창을 시작하고 가상 마스터 데이터베이스에 연결합니다.
2. 서버 수준 방화벽 규칙은 쿼리 창 내에서 선택, 생성, 업데이트 또는 삭제할 수 있습니다.
3. 서버 수준 방화벽 규칙을 만들거나 업데이트하려면 `sp_set_firewall_rule` 저장 프로시저를 실행합니다. 다음 예제는 Contoso 서버에서 일정 범위의 IP 주소를 사용하도록 설정합니다.<br/>먼저 이미 존재하는 규칙을 확인합니다.
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    그런 다음 방화벽 규칙을 추가합니다.
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    서버 수준 방화벽 규칙을 삭제 하려면 sp_delete_firewall_rule 저장 프로시저를 실행 합니다. 다음 예제에서는 ContosoFirewallRule 이라는 규칙을 삭제 합니다.
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   이러한 저장 프로시저에 대한 자세한 내용은 [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) 및 [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)을 참조하세요.

## <a name="database-level-firewall-rules"></a>데이터베이스 수준 방화벽 규칙
데이터베이스에 대한 **제어** 권한이 있는 데이터베이스 사용자(예: 데이터베이스 소유자)만이 데이터베이스 수준 방화벽 규칙을 만들 수 있습니다.

1. IP 주소에 대한 서버 수준 방화벽을 만든 후 Azure Portal 또는 SQL Server Management Studio를 통해 쿼리 창을 시작합니다.
2. 데이터베이스 수준 방화벽 규칙을 만들려는 데이터베이스에 연결 합니다.
   
    데이터베이스 수준 방화벽 규칙을 새로 만들거나 기존 규칙을 업데이트하려면 `sp_set_database_firewall_rule` 저장 프로시저를 실행합니다. 다음 예제에서는 ContosoFirewallRule 이라는 새 방화벽 규칙을 만듭니다.
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    기존 데이터베이스 수준 방화벽 규칙을 삭제하려면 `sp_delete_database_firewall_rule` 저장 프로시저를 실행합니다. 다음 예제에서는 ContosoFirewallRule 이라는 규칙을 삭제 합니다.
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

이러한 저장 프로시저에 대한 자세한 내용은 [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) 및 [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx)을 참조하세요.

> [!NOTE]
> 데이터베이스 수준 방화벽 사용을 보여주는 자습서는 [SQL 인증 및 권한 부여](sql-database-control-access-sql-authentication-get-started.md)를 참조하세요.
>


## <a name="next-steps"></a>다음 단계
다른 방법을 사용한 서버 수준 방화벽 규칙 만들기에 대한 문서는 다음을 참조하세요. 

* [Azure 포털을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings.md)
* [PowerShell을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-powershell.md)
* [REST API를 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-rest.md)

데이터베이스를 만드는 방법에 대한 자습서는 [첫 Azure SQL Database](sql-database-get-started.md)를 참조하세요.
오픈 소스 또는 타사 응용 프로그램에서 Azure SQL 데이터베이스에 연결하는 방법에 대한 도움말은 [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조하세요.
데이터베이스를 탐색하는 방법을 이해하려면 [데이터베이스 및 로그인 보안 관리](https://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스
* [데이터베이스 보안 설정](sql-database-security-overview.md)
* [SQL Server 데이터베이스 엔진 및 Azure SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589)


