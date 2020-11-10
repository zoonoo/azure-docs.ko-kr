---
title: Azure SQL Database 및 Azure Synapse Analytics에 대 한 연결 설정
description: 이 문서에서는 TLS (Transport Layer Security) 버전 선택 및 Azure SQL Database 및 Azure Synapse Analytics에 대 한 프록시 및 리디렉션 설정에 대해 설명 합니다.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 9856d71a6398bcea5b979788846afce17e7955f7
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412986"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL 연결 설정
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

이 문서에서는 Azure SQL Database 및 Azure Synapse Analytics 용 서버에 대 한 연결을 제어 하는 설정을 소개 합니다. 이러한 설정은 서버와 연결 된 모든 SQL Database 및 Azure Synapse Analytics 데이터베이스에 적용 됩니다.

> [!IMPORTANT]
> 이 문서는 Azure SQL Managed Instance에는 적용 되지 않습니다.

연결 설정은 다음 스크린샷에 표시 된 것 처럼 **방화벽 및 가상 네트워크** 화면에서 액세스할 수 있습니다.

 ![연결 설정 창의 스크린샷][1]

> [!NOTE]
> 이러한 설정은 적용 되 고 나면 즉시 적용 됩니다. 각 설정에 대 한 요구 사항을 충족 하지 않는 경우에는 고객의 연결 손실이 발생할 수 있습니다.

## <a name="deny-public-network-access"></a>퍼블릭 네트워크 액세스 거부

**공용 네트워크 액세스 거부** 가 **예** 로 설정 되 면 개인 끝점을 통한 연결만 허용 됩니다. 이 설정이 **아니요** (기본값) 인 경우 고객은 [네트워크 액세스 개요](network-access-controls-overview.md)에 설명 된 대로 공용 끝점 (IP 기반 방화벽 규칙 또는 가상 네트워크 기반 방화벽 규칙을 사용 하 여) 또는 개인 끝점 (Azure 개인 링크 사용)을 사용 하 여 연결할 수 있습니다.

 ![공용 네트워크 액세스 거부를 예로 설정 하 고 공용 네트워크 액세스 거부가 아니요로 설정 된 경우 연결을 보여 주는 다이어그램][2]

논리 서버에서 기존 개인 끝점 없이 **공용 네트워크 액세스 거부** 를 **예** 로 설정 하려고 하면 다음과 같은 오류 메시지와 함께 실패 합니다.  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> 이미 개인 끝점으로 구성 된 논리 서버에서 가상 네트워크 방화벽 규칙을 정의 하려면 **공용 네트워크 액세스 거부** 를 **아니요** 로 설정 합니다.

**공용 네트워크 액세스 거부** 가 **예** 로 설정 되 면 개인 끝점을 통한 연결만 허용 됩니다. 공용 끝점을 통한 모든 연결은 다음과 유사한 오류 메시지와 함께 거부 됩니다.  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

**공용 네트워크 액세스 거부** 가 **예** 로 설정 된 경우 다음과 유사한 오류 메시지와 함께 방화벽 규칙을 추가 하거나 업데이트 하려는 모든 시도는 거부 됩니다.

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>PowerShell을 통해 공용 네트워크 액세스 변경

> [!IMPORTANT]
> Azure SQL Database은 PowerShell Azure Resource Manager 모듈을 계속 지원 하지만 앞으로 모든 개발은 Az. Sql 모듈에 대 한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈이](/powershell/azure/install-az-ps)필요 합니다.

다음 PowerShell 스크립트는 `Get` `Set` 서버 수준에서 및 **공용 네트워크 액세스** 속성을 보여 줍니다.

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>CLI를 통해 공용 네트워크 액세스 변경

> [!IMPORTANT]
> 이 섹션의 모든 스크립트에 [Azure CLI](/cli/azure/install-azure-cli)필요 합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Bash 셸에서 Azure CLI

다음 CLI 스크립트는 Bash 셸에서 **공용 네트워크 액세스** 설정을 변경 하는 방법을 보여 줍니다.

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>최소 TLS 버전 

최소 [tls (전송 계층 보안)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 버전 설정을 사용 하면 고객이 SQL database에서 사용 하는 tls 버전을 선택할 수 있습니다.

현재 TLS 1.0, 1.1 및 1.2을 지원 합니다. 최소 TLS 버전을 설정 하면 최신 TLS 버전이 지원 됩니다. 예를 들어 1.1 보다 큰 TLS 버전을 선택 하면 TLS 1.1 및 1.2에 대 한 연결만 허용 되며 TLS 1.0을 사용한 연결은 거부 됩니다. 테스트를 통해 응용 프로그램이 지원 되는지 확인 한 후 최소 TLS 버전을 1.2로 설정 하는 것이 좋습니다. 이 버전에는 이전 버전의 취약성에 대 한 수정 프로그램이 포함 되어 있으며, Azure SQL Database에서 지원 되는 가장 높은 버전의 TLS입니다.

> [!IMPORTANT]
> 최소 TLS 버전의 기본값은 모든 버전을 허용 하는 것입니다. TLS 버전을 적용 한 후에는 기본값으로 되돌릴 수 없습니다.

이전 버전의 TLS를 사용 하는 응용 프로그램을 사용 하는 고객의 경우 응용 프로그램의 요구 사항에 따라 최소 TLS 버전을 설정 하는 것이 좋습니다. 암호화 되지 않은 연결을 사용 하 여 연결 하는 응용 프로그램을 사용 하는 고객의 경우 최소 TLS 버전을 설정 하지 않는 것이 좋습니다.

자세한 내용은 [SQL Database 연결에 대 한 TLS 고려 사항](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)을 참조 하세요.

최소 TLS 버전을 설정한 후에는 최소 TLS 버전의 서버 보다 낮은 TLS 버전을 사용 하는 고객의 로그인 시도가 실패 하 고 다음 오류가 표시 됩니다.

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>PowerShell을 통해 최소 TLS 버전 설정

> [!IMPORTANT]
> Azure SQL Database은 PowerShell Azure Resource Manager 모듈을 계속 지원 하지만 앞으로 모든 개발은 Az. Sql 모듈에 대 한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈이](/powershell/azure/install-az-ps)필요 합니다.

다음 PowerShell 스크립트는 `Get` `Set` 논리 서버 수준에서 및 **최소 TLS 버전** 속성을 보여 줍니다.

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>Azure CLI를 통해 최소 TLS 버전을 설정 합니다.

> [!IMPORTANT]
> 이 섹션의 모든 스크립트에 [Azure CLI](/cli/azure/install-azure-cli)필요 합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Bash 셸에서 Azure CLI

다음 CLI 스크립트는 Bash 셸에서 **최소 TLS 버전** 설정을 변경 하는 방법을 보여 줍니다.

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>연결 정책 변경

[연결 정책은](connectivity-architecture.md#connection-policy) 고객이 Azure SQL Database에 연결 하는 방법을 결정 합니다.

## <a name="change-the-connection-policy-via-powershell"></a>PowerShell을 통해 연결 정책 변경

> [!IMPORTANT]
> Azure SQL Database은 PowerShell Azure Resource Manager 모듈을 계속 지원 하지만 앞으로 모든 개발은 Az. Sql 모듈에 대 한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈이](/powershell/azure/install-az-ps)필요 합니다.

다음 PowerShell 스크립트는 PowerShell을 사용 하 여 연결 정책을 변경 하는 방법을 보여 줍니다.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>Azure CLI를 통해 연결 정책 변경

> [!IMPORTANT]
> 이 섹션의 모든 스크립트에 [Azure CLI](/cli/azure/install-azure-cli)필요 합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Bash 셸에서 Azure CLI

다음 CLI 스크립트는 Bash 셸에서 연결 정책을 변경 하는 방법을 보여 줍니다.

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>Windows 명령 프롬프트에서 Azure CLI

다음 CLI 스크립트는 Windows 명령 프롬프트에서 연결 정책을 변경 하는 방법을 보여 줍니다 (Azure CLI 설치 됨).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>다음 단계

- Azure SQL Database에서 연결이 작동 하는 방식에 대 한 개요는 [연결 아키텍처](connectivity-architecture.md)를 참조 하세요.
- 서버에 대 한 연결 정책을 변경 하는 방법에 대 한 자세한 내용은 [conn 정책](/cli/azure/sql/server/conn-policy)을 참조 하십시오.

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
