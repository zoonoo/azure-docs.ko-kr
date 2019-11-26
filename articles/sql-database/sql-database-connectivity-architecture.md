---
title: Connectivity Architecture
description: This document explains the Azure SQL connectivity architecture for database connections from within Azure or from outside of Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/02/2019
ms.openlocfilehash: 0ac9247f5156eb1b766aec7403b2dc8473114659
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483722"
---
# <a name="azure-sql-connectivity-architecture"></a>Azure SQL 연결 아키텍처

이 문서에서는 Azure SQL Database 및 SQL Data Warehouse 연결 아키텍처와 다양한 구성 요소가 Azure SQL 인스턴스에 트래픽을 전달하는 방법을 설명합니다. 이러한 연결 구성 요소는 Azure 내에서 연결하는 클라이언트와 Azure 외부에서 연결하는 클라이언트를 사용하여 Azure SQL Database 또는 SQL Data Warehouse에 네트워크 트래픽을 전달합니다. 또한 이 문서에서는 연결되는 방법을 변경하는 스크립트 샘플 및 기본 연결 설정을 변경하는 데 관련된 고려 사항을 제공합니다.

## <a name="connectivity-architecture"></a>연결 아키텍처

다음 다이어그램은 Azure SQL Database 연결 아키텍처의 대략적인 개요를 제공합니다.

![아키텍처 개요](./media/sql-database-connectivity-architecture/connectivity-overview.png)

다음 단계에서는 Azure SQL 데이터베이스에 연결하는 방법을 설명합니다.

- 클라이언트는 공용 IP 주소를 가지며 포트 1433에서 수신 대기하는 게이트웨이에 연결합니다.
- 효과적인 연결 정책에 따라 게이트웨이는 트래픽을 올바른 데이터베이스 클러스터로 리디렉션 또는 프록시합니다.
- 데이터베이스 내에서 클러스터 트래픽은 해당 Azure SQL 데이터베이스로 전달됩니다.

## <a name="connection-policy"></a>연결 정책

Azure SQL Database는 SQL Database 서버의 연결 정책 설정에 대한 다음 세 가지 옵션을 지원합니다.

- **Redirect (recommended):** Clients establish connections directly to the node hosting the database, leading to reduced latency and improved throughout. For connections to use this mode clients need to
   - Allow inbound and outbound communication from the client to all Azure IP addresses in the region on ports in the range of 11000 11999.  
   - Allow inbound and outbound communication from the client to Azure SQL Database gateway IP addresses on port 1433.

- **Proxy:** In this mode, all connections are proxied via the Azure SQL Database gateways,leading to increased latency and reduced throughout. For connections to use this mode clients need to allow inbound and outbound communication from the client to Azure SQL Database gateway IP addresses on port 1433.

- **Default:** This is the connection policy in effect on all servers after creation unless you explicitly alter the connection policy to either `Proxy` or `Redirect`. The default policy is`Redirect` for all client connections originating inside of Azure (e.g. from an Azure Virtual Machine) and `Proxy`for all client connections originating inside ( e.g. connections from your local workstation)

 We highly recommend the `Redirect` connection policy over the `Proxy` connection policy for the lowest latency and highest throughput.However, you will need to meet the additional requirements for allowing network traffic as outlined above. If the client is an Azure Virtual Machine you can accomplish this using Network Security Groups (NSG) with [service tags](../virtual-network/security-overview.md#service-tags). If the client is connecting from a workstation on-premises then you may need to work with your network admin to allow network traffic through your corporate firewall.

## <a name="connectivity-from-within-azure"></a>Azure 내부에서 연결

Azure 내부에서 연결하는 경우 연결에는 기본적으로 `Redirect` 연결 정책이 있습니다. `Redirect` 정책의 경우 TCP 세션이 Azure SQL 데이터베이스로 설정된 후에 대상 가상 IP가 Azure SQL Database 게이트웨이에서 클러스터의 대상 가상 IP로 변경되어 클라이언트 세션이 적절한 데이터베이스 클러스터로 리디렉션됩니다. 그런 다음, 모든 후속 패킷은 Azure SQL Database 게이트웨이를 우회하고 클러스터로 직접 흐릅니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![아키텍처 개요](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Azure 외부에서 연결

Azure 외부에서 연결하는 경우 연결에는 기본적으로 `Proxy` 연결 정책이 있습니다. `Proxy` 정책의 경우 TCP 세션이 Azure SQL Database 게이트웨이를 통해 설정되고 모든 후속 패킷이 게이트웨이를 통합니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![아키텍처 개요](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Additionally open ports 14000-14999 to enable [Connecting with DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database 게이트웨이 IP 주소

The table below lists the IP Addresses of Gateways by region. To connect to an Azure SQL Database, you need to allow network traffic to & from **all** Gateways for the region.

Details of how traffic shall be migrated to new Gateways in specific regions are in the following article: [Azure SQL Database traffic migration to newer Gateways](sql-database-gateway-migration.md)


| 지역 이름          | Gateway IP Addresses |
| --- | --- |
| 오스트레일리아 중부    | 20.36.105.0 |
| Australia Central2   | 20.36.113.0 |
| 오스트레일리아 동부       | 13.75.149.87, 40.79.161.1 |
| 오스트레일리아 동남부 | 191.239.192.109, 13.73.109.251 |
| 브라질 남부         | 104.41.11.5, 191.233.200.14 |
| 캐나다 중부       | 40.85.224.249      |
| 캐나다 동부          | 40.86.226.166      |
| 미국 중부           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| 중국 동부           | 139.219.130.35     |
| 중국 동부 2         | 40.73.82.1         |
| 중국 북부          | 139.219.15.17      |
| 중국 북부 2        | 40.73.50.0         |
| 동아시아            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| 미국 동부              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| 미국 동부 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0,  191.239.224.107, 104.208.150.3 | 
| 프랑스 중부       | 40.79.137.0, 40.79.129.1 |
| 독일 중부      | 51.4.144.100       |
| 독일 북동부   | 51.5.144.179       |
| 인도 중부        | 104.211.96.159     |
| 인도 남부          | 104.211.224.146    |
| 인도 서부           | 104.211.160.80     |
| 일본 동부           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| 일본 서부           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| 한국 중부        | 52.231.32.42       |
| 한국 남부          | 52.231.200.86      |
| 미국 중북부     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| 북유럽         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| 남아프리카 공화국 북부   | 102.133.152.0      |
| 남아프리카 공화국 서부    | 102.133.24.0       |
| 미국 중남부     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| 동남아시아      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| 아랍에미리트 중부          | 20.37.72.64        |
| 아랍에미리트 북부            | 65.52.248.0        |
| 영국 남부             | 51.140.184.11      |
| 영국 서부              | 51.141.8.11        |
| 미국 중서부      | 13.78.145.25       |
| 서유럽          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| 미국 서부              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| 미국 서부 2            | 13.66.226.202      |
|                      |                    |

## <a name="change-azure-sql-database-connection-policy"></a>SQL Database 연결 정책 변경

Azure SQL Database 서버에 대한 Azure SQL Database 연결 정책을 변경하려면 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)를 사용합니다.

- 연결 정책을 `Proxy`로 설정한 경우 모든 네트워크 패킷은 Azure SQL Database 게이트웨이를 통합니다. 이 설정에서 Azure SQL Database 게이트웨이 IP로만 아웃바운드를 허용해야 합니다. `Proxy` 설정을 사용하면 `Redirect` 설정보다 대기 시간이 길어집니다.
- 연결 정책을 `Redirect`로 설정하는 경우 모든 네트워크 패킷은 데이터베이스 클러스터에 직접 전달됩니다. 이 설정에서 여러 IP에 대한 아웃바운드를 허용해야 합니다.

## <a name="script-to-change-connection-settings-via-powershell"></a>PowerShell을 통해 연결 설정을 변경하는 스크립트

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. The following script requires the [Azure PowerShell module](/powershell/azure/install-az-ps).

다음 PowerShell 스크립트에서는 연결 정책을 변경하는 방법을 보여줍니다.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Azure CLI를 통해 연결 설정을 변경하는 스크립트

> [!IMPORTANT]
> 이 스크립트에는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)가 필요합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in a bash shell

> [!IMPORTANT]
> 이 스크립트에는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)가 필요합니다.

The following CLI script shows how to change the connection policy in a bash shell.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI from a Windows command prompt

> [!IMPORTANT]
> 이 스크립트에는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)가 필요합니다.

The following CLI script shows how to change the connection policy from a Windows command prompt (with Azure CLI installed).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 서버에 대한 Azure SQL Database 연결 정책을 변경하는 방법에 대해서는 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)를 참조합니다.
- ADO.NET 4.5 이상 버전을 사용하는 클라이언트의 Azure SQL Database 연결 동작에 대한 자세한 정보는 [ADO.NET 4.5에 대한 1433 이외 포트](sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.
- 일반 애플리케이션 개발 개요 정보는 [SQL Database 애플리케이션 개발 개요](sql-database-develop-overview.md)를 참조하세요.
