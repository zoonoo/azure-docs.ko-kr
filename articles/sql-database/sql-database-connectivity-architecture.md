---
title: Azure SQL Database 및 SQL Data Warehouse로 Azure 트래픽 전달 | Microsoft Doc
description: 이 문서에서 또는 Azure 내에서 데이터베이스 연결에 대 한 Azcure SQL onnectivity 아키텍처를 설명 합니다. Azure 외부에서.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 04/03/2019
ms.openlocfilehash: 4ff6cc0ba18074f353eb5b99af7052edd658a80e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799272"
---
# <a name="azure-sql-connectivity-architecture"></a>Azure SQL 연결 아키텍처

이 문서에서는 Azure SQL Database 및 SQL Data Warehouse 연결 아키텍처와 다양한 구성 요소가 Azure SQL 인스턴스에 트래픽을 전달하는 방법을 설명합니다. 이러한 연결 구성 요소는 Azure 내에서 연결하는 클라이언트와 Azure 외부에서 연결하는 클라이언트를 사용하여 Azure SQL Database 또는 SQL Data Warehouse에 네트워크 트래픽을 전달합니다. 또한 이 문서에서는 연결되는 방법을 변경하는 스크립트 샘플 및 기본 연결 설정을 변경하는 데 관련된 고려 사항을 제공합니다.

## <a name="connectivity-architecture"></a>연결 아키텍처

다음 다이어그램은 Azure SQL Database 연결 아키텍처의 대략적인 개요를 제공합니다.

![아키텍처 개요](./media/sql-database-connectivity-architecture/connectivity-overview.png)

다음 단계에서는 Azure SQL Database에 연결하는 방법을 설명합니다.

- 클라이언트는 공용 IP 주소를 가지며 포트 1433에서 수신 대기하는 게이트웨이에 연결합니다.
- 효과적인 연결 정책에 따라 게이트웨이는 트래픽을 올바른 데이터베이스 클러스터로 리디렉션 또는 프록시합니다.
- 데이터베이스 내에서 클러스터 트래픽은 해당 Azure SQL Database로 전달됩니다.

## <a name="connection-policy"></a>연결 정책

Azure SQL Database는 SQL Database 서버의 연결 정책 설정에 대한 다음 세 가지 옵션을 지원합니다.

- **리디렉션(권장):** 클라이언트는 데이터베이스를 호스팅하는 노드로 직접 연결을 설정합니다. 클라이언트 연결을 사용 하려면 사용 하 여 네트워크 보안 그룹 (NSG)를 사용 하 여 지역에서 모든 Azure IP 주소에 아웃 바운드 방화벽 규칙을 허용 해야 합니다 [서비스 태그](../virtual-network/security-overview.md#service-tags)) 포트 11000-11999, 뿐 아니라 Azure SQL Database 게이트웨이 IP 포트 1433에서 주소입니다. 패킷은 데이터베이스로 직접 이동하므로 대기 시간 및 처리량의 성능이 향상됩니다.
- **프록시:** 이 모드에서 모든 연결은 Azure SQL Database 게이트웨이를 통해 프록시됩니다. 연결을 활성화하려면 클라이언트는 Azure SQL Database 게이트웨이 IP 주소만 허용하는 아웃바운드 방화벽 규칙이 있어야 합니다(일반적으로 지역당 두 개의 IP 주소). 이 모드를 선택하면 워크로드의 특성에 따라 더 높은 대기 시간 및 더 낮은 처리량이 발생할 수 있습니다. 가장 낮은 대기 시간 및 높은 처리량을 위해 `Proxy` 연결 정책을 통해 `Redirect` 연결 정책을 사용하는 것이 좋습니다.
- **기본값:** 연결 정책을 `Proxy` 또는 `Redirect` 중 하나로 명시적으로 변경하지 않는 한 생성 후 모든 서버에 적용되는 연결 정책입니다. 실제 정책은 Azure(`Redirect`) 내에서 또는 Azure(`Proxy`) 외부에서 연결이 발생하는지 여부에 따라 달라집니다.

## <a name="connectivity-from-within-azure"></a>Azure 내부에서 연결

Azure 내부에서 연결하는 경우 연결에는 기본적으로 `Redirect` 연결 정책이 있습니다. `Redirect` 정책의 경우 TCP 세션이 Azure SQL Database로 설정된 후에 대상 가상 IP가 Azure SQL Database 게이트웨이에서 클러스터의 대상 가상 IP로 변경되어 클라이언트 세션이 적절한 데이터베이스 클러스터로 리디렉션됩니다. 그런 다음, 모든 후속 패킷은 Azure SQL Database 게이트웨이를 우회하고 클러스터로 직접 흐릅니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![아키텍처 개요](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Azure 외부에서 연결

Azure 외부에서 연결하는 경우 연결에는 기본적으로 `Proxy` 연결 정책이 있습니다. `Proxy` 정책의 경우 TCP 세션이 Azure SQL Database 게이트웨이를 통해 설정되고 모든 후속 패킷이 게이트웨이를 통합니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![아키텍처 개요](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database 게이트웨이 IP 주소

온-프레미스 리소스에서 Azure SQL Database에 연결하려면 Azure 지역의 Azure SQL Database 게이트웨이에 아웃바운드 네트워크 트래픽을 허용하도록 해야 합니다. 온-프레미스 리소스에서 연결할 때 기본 설정인 `Proxy` 모드에서는 연결할 경우 연결은 게이트웨이를 통합니다.

다음 표에서는 모든 데이터 지역에 있는 Azure SQL Database 게이트웨이의 기본 및 보조 IP를 나열합니다. 일부 지역에는 두 개의 IP 주소가 있습니다. 이러한 지역에서 기본 IP 주소는 게이트웨이의 현재 IP 주소이고 두 번째 IP 주소는 장애 조치 IP 주소입니다. 장애 조치 주소는 높은 서비스 가용성을 유지하기 위해 서버를 이동할 수 있는 주소입니다. 이러한 지역의 경우 두 IP 주소에 아웃바운드를 허용하는 것이 좋습니다. 두 번째 IP 주소는 Microsoft가 소유하고 있으며 Azure SQL Database에서 연결을 허용하기 위해 활성화될 때까지 어떤 서비스도 수신하지 않습니다.

| 지역 이름 | 기본 IP 주소 | 보조 IP 주소 |
| --- | --- |--- |
| 오스트레일리아 동부 | 13.75.149.87 | 40.79.161.1 |
| 오스트레일리아 동남부 | 191.239.192.109 | 13.73.109.251 |
| 브라질 남부 | 104.41.11.5 | |
| 캐나다 중부 | 40.85.224.249 | |
| 캐나다 동부 | 40.86.226.166 | |
| 미국 중부 | 23.99.160.139 | 13.67.215.62 |
| 중국 동부 1 | 139.219.130.35 | |
| 중국 동부 2 | 40.73.82.1 | |
| 중국 북부 1 | 139.219.15.17 | |
| 중국 북부 2 | 40.73.50.0 | |
| 동아시아 | 191.234.2.139 | 52.175.33.150 |
| 미국 동부 1 | 191.238.6.43 | 40.121.158.30 |
| 미국 동부 2 | 191.239.224.107 | 40.79.84.180 * |
| 프랑스 중부 | 40.79.137.0 | 40.79.129.1 |
| 독일 중부 | 51.4.144.100 | |
| 독일 북동부 | 51.5.144.179 | |
| 인도 중부 | 104.211.96.159 | |
| 인도 남부 | 104.211.224.146 | |
| 인도 서부 | 104.211.160.80 | |
| 일본 동부 | 191.237.240.43 | 13.78.61.196 |
| 일본 서부 | 191.238.68.11 | 104.214.148.156 |
| 한국 중부 | 52.231.32.42 | |
| 한국 남부 | 52.231.200.86 |  |
| 미국 중북부 | 23.98.55.75 | 23.96.178.199 |
| 유럽 북부 | 191.235.193.75 | 40.113.93.91 |
| 미국 중남부 | 23.98.162.75 | 13.66.62.124 |
| 동남아시아 | 23.100.117.95 | 104.43.15.0 |
| 영국 남부 | 51.140.184.11 | |
| 영국 서부 | 51.141.8.11| |
| 미국 중서부 | 13.78.145.25 | |
| 서유럽 | 191.237.232.75 | 40.68.37.158 |
| 미국 서부 1 | 23.99.34.75 | 104.42.238.205 |
| 미국 서부 2 | 13.66.226.202 | |
||||

\* **참고:** *미국 동부 2*에는 `52.167.104.0`의 3차 IP 주소도 있습니다.

## <a name="change-azure-sql-database-connection-policy"></a>SQL Database 연결 정책 변경

Azure SQL Database 서버에 대한 Azure SQL Database 연결 정책을 변경하려면 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)를 사용합니다.

- 연결 정책을 `Proxy`로 설정한 경우 모든 네트워크 패킷은 Azure SQL Database 게이트웨이를 통합니다. 이 설정에서 Azure SQL Database 게이트웨이 IP로만 아웃바운드를 허용해야 합니다. `Proxy` 설정을 사용하면 `Redirect` 설정보다 대기 시간이 길어집니다.
- 연결 정책을 `Redirect`로 설정하는 경우 모든 네트워크 패킷은 데이터베이스 클러스터에 직접 전달됩니다. 이 설정에서 여러 IP에 대한 아웃바운드를 허용해야 합니다.

## <a name="script-to-change-connection-settings-via-powershell"></a>PowerShell을 통해 연결 설정을 변경하는 스크립트

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원 하지만 Az.Sql 모듈에 대 한 모든 향후 개발 됩니다. 이러한 cmdlet에 대 한 참조 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)합니다. Az 모듈에는 AzureRm 모듈의 명령에 대 한 인수를 실질적으로 동일합니다.

> [!IMPORTANT]
> 이 스크립트에는 [Azure PowerShell 모듈](/powershell/azure/install-az-ps)이 필요합니다.

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

다음 CLI 스크립트에서는 연결 정책을 변경하는 방법을 보여줍니다.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 서버에 대한 Azure SQL Database 연결 정책을 변경하는 방법에 대해서는 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)를 참조합니다.
- ADO.NET 4.5 이상 버전을 사용하는 클라이언트의 Azure SQL Database 연결 동작에 대한 자세한 정보는 [ADO.NET 4.5에 대한 1433 이외 포트](sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.
- 일반 애플리케이션 개발 개요 정보는 [SQL Database 애플리케이션 개발 개요](sql-database-develop-overview.md)를 참조하세요.
