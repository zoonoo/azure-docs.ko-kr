---
title: Azure SQL 데이터베이스 관리 형 인스턴스에 대한 서비스 지원 서브넷 구성 사용
description: Azure SQL 데이터베이스 관리 형 인스턴스에 대한 서비스 지원 서브넷 구성 사용
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533269"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Azure SQL 데이터베이스 관리 형 인스턴스에 대한 서비스 지원 서브넷 구성 사용
서비스 지원 서브넷 구성은 관리되는 인스턴스를 호스팅하는 서브넷에 대해 자동화된 네트워크 구성 관리를 제공합니다. 서비스 지원 서브넷 구성을 통해 사용자는 TDS 트래픽 흐름에 대한 액세스를 완전히 제어할 수 있으며 관리되는 인스턴스는 SLA를 충족하기 위해 관리 트래픽의 중단 없는 흐름을 보장할 책임이 있습니다.

자동으로 구성된 네트워크 보안 그룹 및 경로 테이블 규칙은 고객에게 표시되며 _Microsoft.Sql-managedInstances_UseOnly__접두사로 추가됩니다.

리소스 공급자에 대한 `Microsoft.Sql/managedInstances` [서브넷 위임을](../virtual-network/subnet-delegation-overview.md) 켜면 서비스 지원 구성이 자동으로 활성화됩니다.

> [!IMPORTANT] 
> 서브넷 위임이 켜지면 서브넷에서 마지막 가상 클러스터를 제거할 때까지 이 것을 끌 수 없습니다. 가상 클러스터를 삭제하는 방법에 대한 자세한 내용은 다음 [문서를](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)참조하십시오.

> [!NOTE] 
> 2020년 5월 1일부터 SLA를 유지하는 데 서비스 지원 서브넷 구성이 필수적이기 때문에 관리되는 인스턴스 리소스 공급자에게 위임되지 않은 서브넷에 관리되는 인스턴스를 배포할 수 없습니다. 2020년 7월 1일에 관리되는 인스턴스를 포함하는 모든 서브넷이 관리되는 인스턴스 리소스 공급자에게 자동으로 위임됩니다. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>새 배포를 위한 서브넷 위임 사용
관리되는 인스턴스를 빈 서브넷에 배포하려면 `Microsoft.Sql/managedInstances` 다음 [문서에](../virtual-network/manage-subnet-delegation.md)설명된 대로 리소스 공급자에게 위임해야 합니다. _참조된 문서는 예를 `Microsoft.DBforPostgreSQL/serversv2` 들어 리소스 공급자를 사용합니다. 대신 리소스 공급자를 `Microsoft.Sql/managedInstances` 사용해야 합니다._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>기존 배포에 대한 서브넷 위임 사용

기존 관리되는 인스턴스 배포에 대한 서브넷 위임을 사용하려면 가상 네트워크 서브넷이 배치된 위치를 찾아야 합니다. 

이를 알아보려면 관리되는 `Virtual network/subnet` `Overview` 인스턴스의 포털 블레이드에서 확인할 수 있습니다.

또는 다음 PowerShell 명령을 실행하여 이를 알아볼 수 있습니다. **구독 ID를 구독 ID로** 바꿉습니다. 또한 **rg 이름을** 관리되는 인스턴스의 리소스 그룹으로 바꾸고 **mi 이름을** 관리되는 인스턴스의 이름으로 바꿉니다.

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

관리되는 인스턴스 서브넷을 찾으면 다음 `Microsoft.Sql/managedInstances` [문서에](../virtual-network/manage-subnet-delegation.md)설명된 대로 리소스 공급자에게 위임해야 합니다. _참조된 문서는 예를 `Microsoft.DBforPostgreSQL/serversv2` 들어 리소스 공급자를 사용합니다. 대신 리소스 공급자를 `Microsoft.Sql/managedInstances` 사용해야 합니다._


> [!IMPORTANT]
> 서비스 지원 구성을 사용하도록 설정해도 서브넷에 이미 있는 관리되는 인스턴스에 대한 장애 조치 또는 연결 중단이 발생하지 않습니다.
