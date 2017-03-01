---
title: "SQL Database 방화벽 규칙 개요 | Microsoft 문서"
description: "서버 수준 및 데이터베이스 수준 방화벽 규칙으로 SQL 데이터베이스 방화벽을 구성하여 액세스를 관리하는 방법에 대해 알아봅니다."
keywords: "데이터베이스 방화벽"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/09/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 09bc875449ecdf48c2570f1029df68d28ae5e798
ms.lasthandoff: 02/17/2017


---
# <a name="overview-of-azure-sql-database-firewall-rules"></a>Azure SQL Database 방화벽 규칙 개요 

Microsoft Azure SQL 데이터베이스는 Azure 및 기타 인터넷 기반 응용 프로그램의 관계형 데이터베이스 서비스를 제공합니다. 데이터를 보호하기 위해 방화벽은 권한이 있는 컴퓨터를 지정할 때까지 데이터베이스 서버에 대한 모든 액세스를 금지합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다.

방화벽을 구성하려면 허용 가능한 IP 주소 범위를 지정하는 방화벽 규칙을 생성해야 합니다. 서버 및 데이터베이스 수준의 방화벽 규칙을 만들 수 있습니다.

* **서버 수준 방화벽 규칙:** 이 규칙은 모든 Azure SQL 서버, 즉, 동일한 논리 서버 내의 모든 데이터베이스에 클라이언트가 액세스할 수 있도록 합니다. 이 규칙은 **master** 데이터베이스에 저장됩니다. 포털 또는 Transact-SQL 문을 사용하여 서버 수준 방화벽 규칙을 구성할 수 있습니다. Azure Portal 또는 PowerShell을 사용하여 서버 수준 방화벽 규칙을 만들려면 구독 소유자 또는 구독 참가자여야 합니다. Transact-SQL을 사용하여 서버 수준 방화벽 규칙을 만들려면 서버 수준 주체 로그인 또는 Azure Active Directory 관리자로 SQL Database 인스턴스에 연결해야 합니다. 다시 말하면 Azure 수준 권한을 가진 사용자가 먼저 서버 수준 방화벽 규칙을 만들어야 합니다.
* **데이터베이스 수준 방화벽 규칙:** 이 규칙은 Azure SQL Database 서버 내의 개별 데이터베이스에 클라이언트가 액세스할 수 있도록 합니다. 각 데이터베이스에 대해 이러한 규칙을 만들 수 있으며 규칙은 개별 데이터베이스에 저장됩니다. (**master** 데이터베이스에 대해 데이터베이스 수준 방화벽 규칙을 만들 수 있습니다.) 이 규칙은 동일 논리 서버 내의 특정(보안) 데이터베이스에 액세스를 제한할 때 유용합니다. Transact-SQL 문을 사용해야만 데이터베이스 수준 방화벽 규칙을 구성할 수 있습니다.

   > [!NOTE]
   > 데이터베이스 수준 방화벽 사용을 보여주는 자습서는 [SQL 인증 및 권한 부여](sql-database-control-access-sql-authentication-get-started.md)를 참조하세요.
   >

**권장 사항:** Microsoft는 보안을 강화하고 데이터베이스의 휴대성이 높아질수록 데이터베이스 수준 방화벽을 사용하도록 권장합니다. 관리자의 경우 서버 수준 방화벽 규칙을 사용하면 동일한 액세스를 요구하는 데이터베이스가 많을 때 각 데이터베이스를 개별적으로 구성할 필요가 없습니다.

> [!Note]
> 비즈니스 연속성의 맥락에서 휴대용 데이터베이스에 대한 자세한 내용은 [재해 복구를 위한 인증 요구 사항](sql-database-geo-replication-security-config.md)을 참조하세요.
>

## <a name="firewall-overview"></a>방화벽 개요
먼저, Azure SQL 서버에 대한 모든 Transact-SQL 액세스는 방화벽에 의해 차단됩니다. Azure SQL 서버를 사용하려면 Azure 포털로 가서 Azure SQL 서버로 액세스를 가능하게 하는 하나 이상의 서버 수준 방화벽 규칙을 꼭 지정해야 합니다. 방화벽 규칙을 사용하여 인터넷이 허용하는 IP 주소 범위 및 Azure 응용 프로그램 Azure SQL 서버의 연결 시도 가능 여부를 지정할 수 있습니다.

선택적으로 Azure SQL Server에 있는 데이터베이스 중 하나에 대해서만 액세스 권한을 부여하려면 필수 데이터베이스에 대해 데이터베이스 수준 규칙을 만들어야 합니다. 서버 수준 방화벽 규칙에 지정된 IP 주소 범위 밖의 데이터베이스 방화벽 규칙의 IP 주소 범위를 지정한 경우, 클라이언트의 IP 주소가 데이터베이스 수준 규칙에 지정된 범위에 속하는지 확인합니다.

인터넷과 Azure로부터의 연결 시도는 다음 다이어그램과 같이 Azure SQL Server 또는 SQL Database에 연결하기 전에 먼저 방화벽을 통과해야 합니다.

   ![방화벽 구성을 설명하는 다이어그램입니다.][1]

## <a name="connecting-from-the-internet"></a>인터넷에서 연결하기

컴퓨터가 인터넷의 데이터베이스에 연결을 시도할 때, 방화벽은 먼저 연결을 요청하는 데이터베이스를 위해 데이터베이스 수준 방화벽 규칙에 반하는 요청의 본래 IP 주소를 확인합니다.

* 요청 IP 주소가 데이터베이스 수준 방화벽 규칙의 지정된 범위 안에 있을 경우, 규칙을 포함한 SQL Database 서버 연결이 허용됩니다.
* 요청된 IP 주소가 데이터베이스 수준 방화벽 규칙의 지정된 범위 안에 없을 경우, 서버 수준 방화벽 규칙을 확인합니다. 요청된 IP 주소가 서버 수준 방화벽 규칙의 지정된 범위 안에 있을 경우, 연결이 허용됩니다. 서버 수준 방화벽 규칙은 Azure SQL Server에 있는 모든 SQL Database에 적용됩니다.  
* 요청 IP 주소가 데이터베이스 수준 또는 서버 수준 방화벽 규칙의 지정된 범위 안에 없을 경우, 연결 요청이 실패합니다.

> [!NOTE]
> 로컬 컴퓨터에서 Azure SQL Database로 액세스하려면, 네트워크의 방화벽과 로컬 컴퓨터가 TCP 포트 1433으로 나가는 통신을 허용하는지 확인합니다.
> 

## <a name="connecting-from-azure"></a>Azure에서 연결
Azure에서 응용 프로그램을 Azure SQL Server에 연결할 수 있게 하려면 Azure 연결을 사용하도록 설정해야 합니다. Azure의 응용 프로그램이 데이터베이스 서버로 연결을 시도할 때, 방화벽은 Azure 연결이 허용되는지 확인합니다. 0.0.0.0으로 된 시작 및 끝 주소가 포함된 방화벽 설정은 연결이 허용됨을 나타냅니다. 연결 시도가 허용되지 않는 경우, 요청은 Azure SQL Database 서버로 도달되지 않습니다.

> [!IMPORTANT]
> 이 옵션은 다른 고객 구독에서의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.
> 

> [!NOTE]
>  자세한 내용은 **ADO.NET 4.5 및 SQL Database에 대한 1433 이외의 포트**의 [SQL Database: 외부 대 내부](sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.
>  

## <a name="creating-the-first-server-level-firewall-rule"></a>첫 번째 서버 수준 방화벽 규칙 만들기
첫 번째 서버 수준 방화벽 설정은 [Azure 포털](https://portal.azure.com/) 을 사용하거나 REST API 또는 Azure PowerShell을 사용하여 프로그램에 따라 만들 수 있습니다. 후속 서버 수준 방화벽 규칙은 이러한 방법과 Transact-SQL을 사용하여 만들고 관리 할 수 있습니다. 성능 향상을 위해 서버 수준 방화벽 규칙이 데이터베이스 수준에서 일시적으로 캐시됩니다. 캐시를 새로 고치려면 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)를 참조하세요. 서버 수준 방화벽 규칙에 대한 자세한 내용은 [방법: Azure 포털을 사용하여 Azure SQL 서버 방화벽 설정](sql-database-configure-firewall-settings.md)을 참조하세요.

## <a name="creating-database-level-firewall-rules"></a>데이터베이스 수준 방화벽 규칙 만들기.
첫 번째 수준 방화벽을 구성한 후, 특정 데이터베이스에 대한 액세스를 제한하고 싶을 수도 있습니다. 서버 수준 방화벽 규칙 내 지정된 범위 밖의 데이터베이스 수준 방화벽 규칙의 IP 주소 범위를 지정한 경우, 데이터베이스 수준 범위 내에 IP 주소를 가진 클라이언트만이 데이터베이스에 액세스할 수 있습니다. 데이터베이스에 대해 최대 128개의 데이터베이스 수준 방화벽 규칙을 가질 수 있습니다. 마스터 및 사용자 데이터베이스에 대한 데이터베이스 수준 방화벽 규칙은 Transact-SQL을 통해 생성 및 관리됩니다. 데이터베이스 수준의 방화벽 규칙 구성에 대한 자세한 내용은 [sp_set_database_firewall_rule(Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx)을 참조하세요.

## <a name="programmatically-managing-firewall-rules"></a>방화벽 규칙을 프로그래밍 방식으로 관리
Azure 포털 외에도 Transact-SQL, REST API 및 Azure PowerShell을 사용하여 방화벽 규칙을 프로그래밍 방식으로 관리할 수 있습니다. 다음 표는 각 메서드에 사용 가능한 명령의 집합을 보여 줍니다.

### <a name="transact-sql"></a>Transact-SQL
| 카탈로그 뷰 또는 저장된 프로시저 | 수준 | 설명 |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |서버 |현재 서버 수준 방화벽 규칙 표시 |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |서버 |서버 수준 방화벽 규칙 생성 및 업데이트 |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |서버 |서버 수준 방화벽 규칙 제거 |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |데이터베이스 |현재 데이터베이스 수준 방화벽 규칙 표시 |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |데이터베이스 |데이터베이스 수준 방화벽 규칙 생성 및 업데이트 |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |데이터베이스 |데이터베이스 수준 방화벽 규칙 제거 |

### <a name="rest-api"></a>REST API
| API | 수준 | 설명 |
| --- | --- | --- |
| [방화벽 규칙 나열](https://msdn.microsoft.com/library/azure/dn505715.aspx) |서버 |현재 서버 수준 방화벽 규칙 표시 |
| [방화벽 규칙 만들기](https://msdn.microsoft.com/library/azure/dn505712.aspx) |서버 |서버 수준 방화벽 규칙 생성 및 업데이트 |
| [방화벽 규칙 설정](https://msdn.microsoft.com/library/azure/dn505707.aspx) |서버 |기존 서버 수준 방화벽 규칙 속성 업데이트 |
| [방화벽 규칙 삭제](https://msdn.microsoft.com/library/azure/dn505706.aspx) |서버 |서버 수준 방화벽 규칙 제거 |

### <a name="azure-powershell"></a>Azure PowerShell
| Cmdlet | 수준 | 설명 |
| --- | --- | --- |
| [AzureSqlDatabaseServerFirewallRule가져오기](https://msdn.microsoft.com/library/azure/dn546731.aspx) |서버 |현재 서버 수준 방화벽 규칙 반환 |
| [신규 AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) |서버 |새 서버 수준 방화벽 규칙 만들기 |
| [AzureSqlDatabaseServerFirewallRule집합](https://msdn.microsoft.com/library/azure/dn546739.aspx) |서버 |기존 서버 수준 방화벽 규칙 속성 업데이트 |
| [AzureSqlDatabaseServerFirewallRule삭제](https://msdn.microsoft.com/library/azure/dn546727.aspx) |서버 |서버 수준 방화벽 규칙 제거 |

> [!NOTE]
> 방화벽 설정의 변경 내용이 적용되기까지는&5;분 정도의 시간이 소요될 수 있습니다.
> 
> 

## <a name="troubleshooting-the-database-firewall"></a>데이터베이스 방화벽 문제 해결
Microsoft Azure SQL 데이터베이스 서비스로의 연결이 예상대로 작동되지 않는 경우 다음 사항을 고려하세요.

* **로컬 방화벽 구성:** 사용자의 컴퓨터가 Azure SQL 데이터베이스에 액세스할 수 있게 되기 전에, 사용자의 컴퓨터에 TCP 포트 1433에 대한 방화벽 예외를 만드는 것이 필요할 수 있습니다. Azure 클라우드 경계 내에서 연결하는 경우 포트를 추가로 열어야 할 수도 있습니다. 자세한 내용은 **ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트** 의 [SQL 데이터베이스의 V12: 내부 vs 외부](sql-database-develop-direct-route-ports-adonet-v12.md)섹션을 참조하세요.
* **NAT(Network Address Translation):** NAT로 인해 Azure SQL 데이터베이스로 연결할 때 컴퓨터에서 사용하는 IP 주소는 컴퓨터 IP 구성 설정에서 나타나는 IP 주소와 다를 수도 있습니다. Azure에 연결할 때 컴퓨터에서 사용하는 IP 주소를 보려면 포털에 로그인하고 데이터베이스를 호스트하는 서버의 **구성** 탭을 탐색합니다. **허용된 IP 주소** 섹션에 **현재 클라이언트 IP 주소**가 표시됩니다. **허용된 IP 주소**에 **추가**를 클릭하여 이 컴퓨터가 서버에 액세스할 수 있도록 합니다.
* **허용 목록의 변경사항이 아직 적용되지 않았습니다.** Azure SQL 데이터베이스 방화벽 구성에 변경 내용이 적용되려면 최대&5;분 정도 걸릴 수 있습니다.
* **로그인이 올바르지 않거나 암호가 올바르지 않습니다.** 로그인에 Azure SQL Database 서버에 대한 권한이 없거나 사용한 암호가 틀렸을 경우 Azure SQL Database 서버에 대한 연결이 거부됩니다. 방화벽 설정은 클라이언트에게 서버에 연결을 시도할 수 있는 기회를 제공합니다. 각 클라이언트는 꼭 필요한 보안 자격 증명을 제공해야 합니다. 로그인 준비에 대한 자세한 내용은 Azure SQL Database에서 데이터베이스, 로그인, 사용자 관리를 참조하세요. 
* **동적 IP 주소:** 동적 IP 주소를 통해 인터넷에 연결되어 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도할 수 있습니다.
  
  * 인터넷 서비스 공급자(ISP)는 Azure SQL Database 서버에 연결될 클라이언트에 할당된 IP 주소 범위를 요청하고, 방화벽 규칙에 따라 IP 주소 범위를 추가합니다.
  * 클라이언트 컴퓨터 대신 고정 IP 주소를 얻고, 방화벽 규칙에 따라 IP 주소 범위를 추가합니다.

## <a name="next-steps"></a>다음 단계
서버 수준 및 데이터베이스 수준 방화벽 규칙 만들기에 대한 문서를 보려면 다음을 참조하세요.

* [Azure 포털을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings.md)
* [T-SQL을 사용하여 Azure SQL 데이터베이스 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-tsql.md)
* [PowerShell을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-powershell.md)
* [REST API를 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-rest.md)

데이터베이스를 만드는 방법에 대한 자습서는 [첫 Azure SQL Database](sql-database-get-started.md)를 참조하세요.
오픈 소스 또는 타사 응용 프로그램에서 Azure SQL 데이터베이스에 연결하는 방법에 대한 도움말은 [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조하세요.
데이터베이스를 탐색하는 방법을 이해하려면 [데이터베이스 및 로그인 보안 관리](https://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스
* [데이터베이스 보안 설정](sql-database-security-overview.md)
* [SQL Server 데이터베이스 엔진 및 Azure SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

