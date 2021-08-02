---
title: Azure SQL Managed Instance에 대한 서비스 지원 서브넷 구성 사용
description: Azure SQL Managed Instance에 대한 서비스 지원 서브넷 구성 사용
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: 26f9dd2991f575669f580fc92cfc29259ca54be6
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110707223"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대한 서비스 지원 서브넷 구성 사용
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

서비스 지원 서브넷 구성은 관리형 인스턴스를 호스팅하는 서브넷에 대해 자동화된 네트워크 구성 관리를 제공합니다. 서비스 지원 서브넷 구성을 통해 사용자는 데이터에 대한 액세스(TDS 트래픽 흐름)를 완전히 제어할 수 있는 반면 관리형 인스턴스는 SLA를 충족하기 위해 중단 없는 관리 트래픽 흐름을 보장해야 합니다.

자동으로 구성된 네트워크 보안 그룹 및 경로 테이블 규칙은 고객에게 표시되고 접두사 _Microsoft. .Sql-managedInstances_UseOnly_ _로 주석이 달립니다.

`Microsoft.Sql/managedInstances` 리소스 공급자에 대해 [서브넷 위임](../../virtual-network/subnet-delegation-overview.md)을 설정하면 서비스 사용자 구성이 자동으로 활성화됩니다.

> [!IMPORTANT] 
> 서브넷 위임이 설정된 후에는 서브넷에서 마지막 가상 클러스터를 제거할 때까지 해제할 수 없습니다. 가상 클러스터를 삭제하는 방법에 대한 자세한 내용은 다음 [문서](virtual-cluster-delete.md#delete-a-virtual-cluster-from-the-azure-portal)를 참조하세요.

> [!NOTE] 
> 서비스 지원 서브넷 구성은 SLA를 유지 관리하기 위한 필수 기능으로, 2020년 5월 1일부터 관리형 인스턴스 리소스 공급자에게 위임되지 않은 서브넷에는 관리형 인스턴스를 배포할 수 없습니다. 2020년 7월 1일에 관리형 인스턴스를 포함하는 모든 서브넷은 관리형 인스턴스 리소스 공급자에게 자동으로 위임됩니다. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>새 배포에 대해 서브넷 위임 사용
관리형 인스턴스를 빈 서브넷에 배포하려면 다음 [문서](../../virtual-network/manage-subnet-delegation.md)에 설명된 대로 `Microsoft.Sql/managedInstances` 리소스 공급자에게 위임해야 합니다. _예를 들어 참조 문서는 `Microsoft.DBforPostgreSQL/serversv2` 리소스 공급자를 사용합니다. 대신 `Microsoft.Sql/managedInstances` 리소스 공급자를 사용해야 합니다._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>기존 배포에 대해 서브넷 위임 사용

기존 관리형 인스턴스 배포에 대해 서브넷 위임을 사용하도록 설정하려면 가상 네트워크 서브넷이 배치되는 위치를 확인해야 합니다. 

이를 알아보려면 관리형 인스턴스의 `Overview` 포털 블레이드에서 `Virtual network/subnet`을 확인하면 됩니다.

또는 다음 PowerShell 명령을 실행하여 이를 알아볼 수 있습니다. **subscription-id** 를 구독 ID로 바꿉니다. 또한 **rg-name** 을 관리형 인스턴스의 리소스 그룹으로 바꾸고 **mi-name** 을 관리형 인스턴스의 이름으로 바꿉니다.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

관리형 인스턴스 서브넷을 찾으면 다음 [문서](../../virtual-network/manage-subnet-delegation.md)에 설명된 대로 `Microsoft.Sql/managedInstances` 리소스 공급자에게 위임해야 합니다. _예를 들어 참조 문서는 `Microsoft.DBforPostgreSQL/serversv2` 리소스 공급자를 사용합니다. 대신 `Microsoft.Sql/managedInstances` 리소스 공급자를 사용해야 합니다._


> [!IMPORTANT]
> 서비스 지원 구성을 사용하도록 설정해도 서브넷에 이미 있는 관리형 인스턴스에 대한 장애 조치(failover) 또는 연결이 중단되지 않습니다.
