---
title: Azure SQL Database 및 데이터 웨어하우스의 연결 설정
description: 이 문서에서는 Azure SQL에 대 한 TLS 버전 선택 및 프록시 및 리디렉션 설정에 대해 설명 합니다.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: cd239106bfd3ac785cffbf1365f298da565179ec
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366090"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL 연결 설정
> [!NOTE]
> 기능은 **미국 서 부 2, 미국 동부, 미국 서 부, 미국 동부 중부** 에서 다른 지역에 제공 될 예정입니다.

> [!NOTE]
> 이 문서는 Azure SQL 서버 및 Azure SQL 서버에서 생성된 SQL Database와 SQL Data Warehouse 데이터베이스에 적용됩니다. 간단히 하기 위해 SQL Database는 SQL Database와 SQL Data Warehouse를 참조할 때 사용됩니다.

> [!IMPORTANT]
> 이 문서는에 적용 *되지* 않습니다 **Azure SQL Database Managed Instance**

이 문서에서는 서버 수준에서 Azure SQL Database에 대 한 연결을 제어 하는 설정을 소개 합니다. 이러한 설정은 서버와 연결 된 **모든** SQL Database 및 SQL Data Warehouse 데이터베이스에 적용 됩니다.

> [!NOTE]
> 이러한 설정이 적용 되 면 **즉시 적용** 되며 각 설정에 대 한 요구 사항을 충족 하지 않는 경우 클라이언트에 대 한 연결 손실이 발생할 수 있습니다.

연결 설정은 아래 스크린샷에 표시 된 것 처럼 **방화벽 및 가상 네트워크** 블레이드에서 액세스할 수 있습니다.

 ![연결 설정의 스크린샷][1]


## <a name="deny-public-network-access"></a>공용 네트워크 액세스 거부
Azure Portal에서 **공용 네트워크 액세스 거부** 설정을 **예**로 설정 하면 개인 끝점을 통한 연결만 허용 됩니다. 이 설정이 **아니요**로 설정 된 경우 클라이언트는 개인 또는 공용 끝점을 사용 하 여 연결할 수 있습니다.

**공용 네트워크 액세스 거부** 를 **예**로 설정한 후 공용 끝점을 사용 하는 클라이언트의 로그인 시도가 실패 하 고 다음 오류가 발생 합니다.

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>PowerShell을 통해 공용 네트워크 액세스 변경
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원하지만 모든 향후 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대 한 자세한 내용은 [AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)를 참조 하세요. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈이](/powershell/azure/install-az-ps)필요 합니다.

다음 PowerShell 스크립트는 논리 서버 수준에서 **공용 네트워크 액세스** 속성을 `Get` 하 고 `Set` 하는 방법을 보여 줍니다.

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>CLI를 통해 공용 네트워크 액세스 변경
> [!IMPORTANT]
> 이 섹션의 모든 스크립트에는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)필요 합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Bash 셸에서 Azure CLI
다음 CLI 스크립트는 bash 셸에서 **공용 네트워크 액세스** 를 변경 하는 방법을 보여 줍니다.

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>연결 정책
[연결 정책은](sql-database-connectivity-architecture.md#connection-policy) 클라이언트가 Azure SQL Server에 연결 하는 방법을 결정 합니다. 

## <a name="change-connection-policy-via-powershell"></a>PowerShell을 통해 연결 정책 변경
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원하지만 모든 향후 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대 한 자세한 내용은 [AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)를 참조 하세요. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈이](/powershell/azure/install-az-ps)필요 합니다.

다음 PowerShell 스크립트는 PowerShell을 사용 하 여 연결 정책을 변경 하는 방법을 보여 줍니다.

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

## <a name="change-connection-policy-via-azure-cli"></a>Azure CLI를 통해 연결 정책 변경
> [!IMPORTANT]
> 이 섹션의 모든 스크립트에는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)필요 합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Bash 셸에서 Azure CLI
다음 CLI 스크립트는 bash 셸에서 연결 정책을 변경 하는 방법을 보여 줍니다. 

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
- Azure SQL Database에서 연결이 작동 하는 방식에 대 한 개요는 [AZURE SQL 연결 아키텍처](sql-database-connectivity-architecture.md) 를 참조 하세요.
- Azure SQL Database 서버에 대한 Azure SQL Database 연결 정책을 변경하는 방법에 대해서는 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)를 참조합니다.

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
