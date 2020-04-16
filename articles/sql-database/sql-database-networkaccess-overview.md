---
title: 네트워크 액세스 컨트롤
description: 액세스를 관리하고 단일 또는 풀풀 데이터베이스를 구성하기 위한 Azure SQL Database 및 데이터 웨어하우스의 네트워크 액세스 제어 개요입니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 8b4ee679b21d904f997f727f5f26275c86acc9c5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414406"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL 데이터베이스 및 데이터 웨어하우스 네트워크 액세스 제어

> [!NOTE]
> 이 문서는 Azure SQL 서버 및 Azure SQL 서버에서 생성된 SQL Database와 SQL Data Warehouse 데이터베이스에 적용됩니다. 간단히 하기 위해 SQL Database는 SQL Database와 SQL Data Warehouse를 참조할 때 사용됩니다.

> [!IMPORTANT]
> 이 문서는 **Azure SQL Database Managed Instance**에 적용되지 *않습니다*. 네트워킹 구성에 대한 자세한 내용은 [관리되는 인스턴스 에 연결 참조하세요.](sql-database-managed-instance-connect-app.md)

[Azure 포털에서](sql-database-single-database-get-started.md)새 Azure SQL Server를 만들 때 결과는 형식의 공용 끝점인 *yourservername.database.windows.net.*

다음 네트워크 액세스 컨트롤을 사용하여 공용 끝점을 통해 SQL Database에 대한 액세스를 선택적으로 허용할 수 있습니다.
- Azure 서비스 허용: ON으로 설정하면 Azure 경계 내의 다른 리소스(예: Azure 가상 컴퓨터)가 SQL 데이터베이스에 액세스할 수 있습니다.

- IP 방화벽 규칙: 이 기능을 사용하여 온-프레미스 컴퓨터와 같은 특정 IP 주소의 연결을 명시적으로 허용합니다.

다음을 통해 [가상 네트워크에서](../virtual-network/virtual-networks-overview.md) SQL 데이터베이스에 대한 개인 액세스를 허용할 수도 있습니다.
- 가상 네트워크 방화벽 규칙: 이 기능을 사용하여 Azure 경계 내의 특정 가상 네트워크의 트래픽을 허용합니다.

- 개인 링크: 이 기능을 사용하여 특정 가상 네트워크 내에서 Azure SQL Server용 개인 끝점을 만듭니다.



이러한 액세스 제어및 액세스 에 대한 자세한 내용은 아래 비디오를 참조하십시오.
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>Azure 서비스 허용 
[Azure 포털에서](sql-database-single-database-get-started.md)새 Azure SQL Server를 만드는 동안 이 설정은 선택되지 않은 상태로 남아 있습니다.



Azure SQL Server를 다음과 같이 만든 후 방화벽 창을 통해 이 설정을 변경할 수도 있습니다.
  
 ![서버 방화벽 관리 스크린샷][2]

**ON** Azure SQL Server로 설정하면 Azure 경계 내의 모든 리소스에서 통신할 수 있으며 구독의 일부일 수도 있습니다.

대부분의 경우 **ON** 설정은 대부분의 고객이 원하는 것보다 더 허용적입니다. 이 설정을 **OFF로** 설정하고 보다 제한적인 IP 방화벽 규칙 또는 가상 네트워크 방화벽 규칙으로 대체할 수 있습니다. 이렇게 하면 Azure의 VM에서 실행되는 다음 기능에 영향을 주며, 따라서 VNet의 일부가 아니므로 Azure IP 주소를 통해 Sql Database에 연결됩니다.

### <a name="import-export-service"></a>가져오기/내보내기 서비스
**Azure 서비스에 대한 액세스 허용이** **꺼짐으로**설정되어 있을 때 내보내기 서비스 가져오기가 작동하지 않습니다. 그러나 [Azure VM에서 sqlpackage.exe를 수동으로 실행하거나](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) DACFx API를 사용하여 코드에서 직접 내보내기를 수행하여 문제를 해결할 수 있습니다.

### <a name="data-sync"></a>데이터 동기화
Azure **서비스에 대한 액세스 허용을** **사용하여**데이터 동기화 기능을 사용하려면 개별 방화벽 규칙 항목을 만들어 **허브** 데이터베이스를 호스팅하는 지역의 Sql **서비스 태그에서** IP 주소를 [추가해야](sql-database-server-level-firewall-rule.md) 합니다.
**허브** 및 **멤버** 데이터베이스를 모두 호스팅하는 논리 서버에 이러한 서버 수준 방화벽 규칙을 추가합니다(다른 지역에 있을 수 있음).

다음 PowerShell 스크립트를 사용하여 미국 서부 지역의 Sql 서비스 태그에 해당하는 IP 주소를 생성합니다.
```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag 위치 매개 변수를 지정에도 불구 하 고 Sql 서비스 태그에 대 한 전역 범위를 반환 합니다. 동기화 그룹에서 사용하는 Hub 데이터베이스를 호스팅하는 지역으로 필터링해야 합니다.

PowerShell 스크립트의 출력은 클래스리스 도메인 라우팅(CIDR) 표기법의 표기형이며, 이 형식은 다음과 같이 [Get-IPrangeEnd.ps1를](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) 사용하여 시작 및 종료 IP 주소 형식으로 변환되어야 합니다.
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

CIDR에서 시작 및 종료 IP 주소 형식으로 모든 IP 주소를 변환하려면 다음 추가 단계를 수행합니다.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
이제 이러한 규칙을 고유한 방화벽 규칙으로 추가한 다음 **Azure 서비스가 서버에 OFF에 액세스할** 수 있도록 허용하도록 설정할 수 있습니다.


## <a name="ip-firewall-rules"></a>IP 방화벽 규칙
IP 기반 방화벽은 클라이언트 컴퓨터의 IP 주소를 명시적으로 추가할 때까지 데이터베이스 서버에 대한 모든 액세스를 방지하는 Azure SQL Server의 [기능입니다.](sql-database-server-level-firewall-rule.md)


## <a name="virtual-network-firewall-rules"></a>가상 네트워크 방화벽 규칙

Azure SQL Server 방화벽을 사용하면 IP 규칙 외에도 *가상 네트워크 규칙을*정의할 수 있습니다.  
자세한 내용은 [Azure SQL Database에 대한 가상 네트워크 서비스 끝점 및 규칙을](sql-database-vnet-service-endpoint-rule-overview.md) 참조하거나 이 비디오를 시청하십시오.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Azure 네트워킹 용어  
가상 네트워크 방화벽 규칙을 탐색할 때 다음 Azure 네트워킹 용어에 유의하십시오.

**가상 네트워크:** Azure 구독과 연결된 가상 네트워크를 가질 수 있습니다. 

**서브넷:** 가상 네트워크에 **서브넷**이 포함됩니다. 소유한 Azure VM(가상 머신)은 서브넷에 할당됩니다. 하나의 서브넷에 여러 VM 또는 다른 컴퓨팅 노드가 포함될 수 있습니다. 액세스를 허용하도록 보안을 구성해야 가상 네트워크 외부의 컴퓨팅 노드가 가상 네트워크에 액세스할 수 있습니다.

**가상 네트워크 서비스 엔드포인트:** [가상 네트워크 서비스 끝점은](../virtual-network/virtual-network-service-endpoints-overview.md) 속성 값에 하나 이상의 공식적인 Azure 서비스 유형 이름이 포함된 서브넷입니다. 이 문서에서는 SQL Database라는 Azure 서비스를 나타내는 **Microsoft.Sql**의 형식 이름을 살펴봅니다.

**가상 네트워크 규칙:** SQL Database 서버에 대한 가상 네트워크 규칙은 SQL Database 서버의 ACL(액세스 제어 목록)에 나열된 서브넷입니다. SQL Database에 대한 ACL에 나열되려면 서브넷에 **Microsoft.Sql** 형식 이름이 있어야 합니다. 가상 네트워크 규칙은 서브넷에 있는 모든 노드에서 보낸 통신을 수락하도록 SQL Database 서버에 지시합니다.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 와 가상 네트워크 방화벽 규칙

Azure SQL Server 방화벽을 사용하면 SQL Database에 통신이 허용되는 IP 주소 범위를 지정할 수 있습니다. 이 방법은 Azure 프라이빗 네트워크 외부에 있는 안정적인 IP 주소에 적합합니다. 그러나 Azure 개인 네트워크 내의 가상 컴퓨터(VM)는 *동적* IP 주소로 구성됩니다. 동적 IP 주소는 VM을 다시 시작할 때 변경될 수 있으며 IP 기반 방화벽 규칙이 무효화될 수 있습니다. 프로덕션 환경의 방화벽 규칙에서는 동적 IP 주소를 지정하면 안 됩니다.

VM에 대한 *정적* IP 주소를 획득하여 이러한 제한 사항을 해결할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 가상 머신에 대한 개인 IP 주소 구성](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)을 참조하세요. 그러나 정적 IP 방법은 관리가 어려워질 수 있고 대규모로 이루어질 경우 비용이 많이 듭니다. 

가상 네트워크 규칙은 VM을 포함하는 특정 서브넷에서 액세스를 설정하고 관리하는 것이 더 쉬운 대안입니다.

> [!NOTE]
> 아직 서브넷에 SQL Database가 있을 수 없습니다. Azure SQL Database 서버가 가상 네트워크에 있는 서브넷의 노드인 경우 가상 네트워크 내의 모든 노드가 SQL Database와 통신할 수 있습니다. 이 경우 VM은 가상 네트워크 규칙이나 IP 규칙이 없어도 SQL Database와 통신할 수 있습니다.

## <a name="private-link"></a>Private Link 
개인 링크를 사용하면 **개인 끝점을**통해 Azure SQL Server에 연결할 수 있습니다. 개인 끝점은 특정 [가상 네트워크](../virtual-network/virtual-networks-overview.md) 및 서브넷 내의 개인 IP 주소입니다.

## <a name="next-steps"></a>다음 단계

- 서버 수준 IP 방화벽 규칙을 만드는 빠른 시작은 [Azure SQL 데이터베이스 만들기](sql-database-single-database-get-started.md)를 참조하십시오.

- 서버 수준 Vnet 방화벽 규칙을 만드는 빠른 시작은 [Azure SQL Database에 대한 가상 네트워크 서비스 끝점 및 규칙을](sql-database-vnet-service-endpoint-rule-overview.md)참조하십시오.

- 오픈 소스 또는 타사 응용 프로그램에서 Azure SQL 데이터베이스에 연결하는 데 도움이 되는 경우 [클라이언트 빠른 시작 코드 샘플을](https://msdn.microsoft.com/library/azure/ee336282.aspx)SQL Database 에 참조하세요.

- 열어야 할 수 있는 추가 포트에 대한 자세한 내용은 [ADO.NET 4.5와 SQL Database에 대한 1433 이외 포트](sql-database-develop-direct-route-ports-adonet-v12.md)의 **SQL Database: 내부 및 외부** 섹션을 참조하세요.

- Azure SQL 데이터베이스 연결에 대한 개요는 [Azure SQL 연결 아키텍처를](sql-database-connectivity-architecture.md) 참조하십시오.

- Azure SQL Database 보안 개요는 [데이터베이스 보안 설정](sql-database-security-overview.md)을 참조하세요.

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

