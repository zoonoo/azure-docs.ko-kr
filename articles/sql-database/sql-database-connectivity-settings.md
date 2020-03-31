---
title: Azure SQL 데이터베이스 및 데이터 웨어하우스에 대한 연결 설정
description: 이 문서에서는 Azure SQL에 대한 TLS 버전 선택 및 프록시 대 리디렉션 설정에 대해 설명합니다.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366090"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL 연결 설정
> [!NOTE]
> 이 문서는 Azure SQL 서버 및 Azure SQL 서버에서 생성된 SQL Database와 SQL Data Warehouse 데이터베이스에 적용됩니다. 간단히 하기 위해 SQL Database는 SQL Database와 SQL Data Warehouse를 참조할 때 사용됩니다.

> [!IMPORTANT]
> 이 문서는 Azure **SQL 데이터베이스 관리 인스턴스에는** 적용되지 *않습니다.*

이 문서에서는 서버 수준에서 Azure SQL Database에 대한 연결을 제어하는 설정을 소개합니다. 이러한 설정은 서버와 연결된 **모든** SQL 데이터베이스 및 SQL 데이터 웨어하우스 데이터베이스에 적용됩니다.

> [!NOTE]
> 이러한 설정이 적용되면 **즉시 적용되며** 각 설정에 대한 요구 사항을 충족하지 않으면 클라이언트의 연결이 끊어질 수 있습니다.

연결 설정은 아래 스크린샷과 같이 방화벽 및 가상 네트워크 블레이드에서 액세스할 수 **있습니다.**

 ![연결 설정 의 스크린 샷][1]


## <a name="deny-public-network-access"></a>공용 네트워크 액세스 거부
Azure 포털에서 공용 **네트워크 액세스 거부** 설정이 **예로**설정된 경우 개인 끝점을 통한 연결만 허용됩니다. 이 설정이 **아니요로**설정되면 클라이언트는 개인 또는 공용 끝점을 사용하여 연결할 수 있습니다.

공용 **네트워크 액세스 거부를** **Yes로**설정한 후 공용 끝점을 사용하는 클라이언트의 로그인 시도가 다음과 같은 오류로 실패합니다.

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>PowerShell을 통해 공용 네트워크 액세스 변경
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 리소스 관리자 모듈은 Azure SQL Database에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈용입니다. 이러한 cmdlet에 대 한 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조 합니다. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 거의 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈이](/powershell/azure/install-az-ps)필요합니다.

다음 PowerShell 스크립트는 `Get` 논리 `Set` 서버 수준에서 **공용 네트워크 액세스** 속성및 공용 네트워크 액세스 방법을 보여 주며 다음과 같습니다.

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>CLI를 통해 공용 네트워크 액세스 변경
> [!IMPORTANT]
> 이 섹션의 모든 스크립트에는 [Azure CLI가](https://docs.microsoft.com/cli/azure/install-azure-cli)필요합니다.

### <a name="azure-cli-in-a-bash-shell"></a>bash 셸의 Azure CLI
다음 CLI 스크립트는 bash 셸에서 **공용 네트워크 액세스를** 변경하는 방법을 보여 주며 다음과 같습니다.

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>연결 정책
[연결 정책은](sql-database-connectivity-architecture.md#connection-policy) 클라이언트가 Azure SQL Server에 연결하는 방법을 결정합니다. 

## <a name="change-connection-policy-via-powershell"></a>PowerShell을 통해 연결 정책 변경
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 리소스 관리자 모듈은 Azure SQL Database에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈용입니다. 이러한 cmdlet에 대 한 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조 합니다. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 거의 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈이](/powershell/azure/install-az-ps)필요합니다.

다음 PowerShell 스크립트는 PowerShell을 사용하여 연결 정책을 변경하는 방법을 보여 주며 다음과 같습니다.

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

## <a name="change-connection-policy-via-azure-cli"></a>Azure CLI를 통한 연결 정책 변경
> [!IMPORTANT]
> 이 섹션의 모든 스크립트에는 [Azure CLI가](https://docs.microsoft.com/cli/azure/install-azure-cli)필요합니다.

### <a name="azure-cli-in-a-bash-shell"></a>bash 셸의 Azure CLI
다음 CLI 스크립트에서는 bash 셸에서 연결 정책을 변경하는 방법을 보여 주며 있습니다. 

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
다음 CLI 스크립트는 Azure CLI가 설치된 Windows 명령 프롬프트에서 연결 정책을 변경하는 방법을 보여 주며 있습니다.

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>다음 단계
- Azure SQL Database에서 연결이 작동하는 방식에 대한 개요는 [Azure SQL Connectivity 아키텍처를](sql-database-connectivity-architecture.md) 참조하십시오.
- Azure SQL Database 서버에 대한 Azure SQL Database 연결 정책을 변경하는 방법에 대해서는 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)를 참조합니다.

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
