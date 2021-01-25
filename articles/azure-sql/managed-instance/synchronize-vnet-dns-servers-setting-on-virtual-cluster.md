---
title: SQL Managed Instance 가상 클러스터의 가상 네트워크 DNS 서버 설정 동기화
description: SQL Managed Instance 가상 클러스터에서 가상 네트워크 DNS 서버를 동기화 하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 0da38475c0e3c766cabbf765ea89dc5714a5b830
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747573"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>SQL Managed Instance 가상 클러스터의 가상 네트워크 DNS 서버 설정 동기화
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 SQL Managed Instance 가상 클러스터에서 가상 네트워크 DNS 서버 설정을 동기화 하는 시기 및 방법에 대해 설명 합니다.

## <a name="when-to-synchronize-the-dns-setting"></a>DNS 설정을 동기화 할 때

프라이빗 호스트 이름이 SQL Managed Instance에서 확인되어야 하는 몇 가지 시나리오(예: db 메일, 클라우드 또는 하이브리드 환경의 다른 SQL Server 인스턴스에 연결된 서버)가 있습니다. 이 경우에 Azure 내에서 사용자 지정 DNS를 구성해야 합니다. 자세한 내용은 [AZURE SQL Managed Instance에 대 한 사용자 지정 DNS 구성](custom-dns-configure.md) 을 참조 하세요.

[가상 클러스터](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) 호스팅 Managed Instance를 만든 후에이 변경 내용이 구현 된 경우 가상 네트워크 구성과 가상 클러스터의 DNS 서버 설정을 동기화 해야 합니다.

> [!IMPORTANT]
> DNS 서버 동기화 설정은 가상 클러스터에서 호스트 되는 모든 관리 되는 인스턴스에 영향을 줍니다.

## <a name="how-to-synchronize-the-dns-setting"></a>DNS 설정을 동기화 하는 방법

### <a name="azure-rbac-permissions-required"></a>Azure RBAC 권한 필요

사용자 동기화 DNS 서버 구성에는 다음 Azure 역할 중 하나가 있어야 합니다.

- 구독 소유자 역할 또는
- Managed Instance 참가자 역할 또는
- 다음 권한이 있는 사용자 지정 역할:
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Azure PowerShell 사용

DNS 서버 설정이 업데이트 된 가상 네트워크를 가져옵니다.

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
PowerShell 명령 [AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) 를 사용 하 여 서브넷에 있는 모든 가상 클러스터에 대 한 DNS 서버 구성을 동기화 합니다.

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Azure CLI 사용

DNS 서버 설정이 업데이트 된 가상 네트워크를 가져옵니다.

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

Azure CLI 명령 [az resource invoke-작업](/cli/azure/resource?view=azure-cli-latest#az_resource_invoke_action) 을 사용 하 여 서브넷에 있는 모든 가상 클러스터에 대 한 DNS 서버 구성을 동기화 합니다.

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>다음 단계

- 사용자 지정 DNS 구성에 대 한 자세한 내용은 [AZURE SQL Managed Instance에 대 한 사용자 지정 Dns 구성](custom-dns-configure.md)을 확인 하세요.
- 개요는 [AZURE SQL Managed Instance?](sql-managed-instance-paas-overview.md)을 참조 하세요.
