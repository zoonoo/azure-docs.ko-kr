---
title: Azure SQL Managed Instance에 대 한 서비스-사용 서브넷 구성 사용
description: Azure SQL Managed Instance에 대 한 서비스-사용 서브넷 구성 사용
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: b88740c71db6ae56621410ef41975a4616ff8ecd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711377"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대 한 서비스-사용 서브넷 구성 사용
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

서비스 제공 서브넷 구성은 관리 되는 인스턴스를 호스트 하는 서브넷에 대해 자동화 된 네트워크 구성 관리를 제공 합니다. 서비스와 관련 된 서브넷 구성 사용자는 관리 되는 인스턴스가 SLA를 충족 하기 위해 지속적으로 관리 트래픽의 흐름을 유지 하는 동안 데이터 (TDS 트래픽 흐름)에 대 한 모든 액세스 권한을 유지 합니다.

자동으로 구성 된 네트워크 보안 그룹 및 경로 테이블 규칙은 고객에 게 표시 되 고 접두사 _Microsoft. .sql-managedInstances_UseOnly__로 주석이 추가 됩니다.

리소스 공급자에 대 한 [서브넷 위임을](../../virtual-network/subnet-delegation-overview.md) 켜면 서비스 사용자 구성이 자동으로 활성화 됩니다 `Microsoft.Sql/managedInstances` .

> [!IMPORTANT] 
> 서브넷 위임이 설정 된 후에는 서브넷에서 마지막 가상 클러스터를 제거할 때까지이 기능을 해제할 수 없습니다. 가상 클러스터를 삭제 하는 방법에 대 한 자세한 내용은 다음 [문서](virtual-cluster-delete.md#delete-a-virtual-cluster-from-the-azure-portal)를 참조 하세요.

> [!NOTE] 
> 서비스 제공 서브넷 구성은 SLA를 유지 관리 하기 위한 필수 기능이 며, 2020 년 5 월 1 일부 터 관리 되는 인스턴스 리소스 공급자에 게 위임 되지 않은 서브넷에 관리 되는 인스턴스를 배포할 수 없습니다. 2020 7 월 1 일에 관리 되는 인스턴스를 포함 하는 모든 서브넷은 관리 되는 인스턴스 리소스 공급자에 자동으로 위임 됩니다. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>새 배포에 대 한 서브넷 위임 사용
에서 관리 되는 인스턴스를 빈 서브넷에 배포 하려면 `Microsoft.Sql/managedInstances` 다음 [문서](../../virtual-network/manage-subnet-delegation.md)에 설명 된 대로 리소스 공급자에 위임 해야 합니다. _참조 된 문서에서는 `Microsoft.DBforPostgreSQL/serversv2` 리소스 공급자를 사용 합니다 (예:). `Microsoft.Sql/managedInstances`대신 리소스 공급자를 사용 해야 합니다._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>기존 배포에 대 한 서브넷 위임 사용

기존 관리 되는 인스턴스 배포에 대 한 서브넷 위임을 사용 하도록 설정 하려면 가상 네트워크 서브넷이 배치 되는 위치를 확인 해야 합니다. 

이에 대 한 자세한 내용은 `Virtual network/subnet` `Overview` 관리 되는 인스턴스의 포털 블레이드에서 확인할 수 있습니다.

또는 다음 PowerShell 명령을 실행 하 여이를 배울 수 있습니다. **구독 id** 를 구독 id로 바꿉니다. 또한 **rg 이름** 을 관리 되는 인스턴스의 리소스 그룹으로 바꾸고 **mi 이름** 을 관리 되는 인스턴스의 이름으로 바꿉니다.

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

관리 되는 인스턴스 서브넷을 찾은 후 `Microsoft.Sql/managedInstances` 에는 다음 [문서](../../virtual-network/manage-subnet-delegation.md)에 설명 된 대로 리소스 공급자에 위임 해야 합니다. _참조 된 문서에서는 `Microsoft.DBforPostgreSQL/serversv2` 리소스 공급자를 사용 합니다 (예:). `Microsoft.Sql/managedInstances`대신 리소스 공급자를 사용 해야 합니다._


> [!IMPORTANT]
> 서비스 사용 구성을 사용 하도록 설정 하면 서브넷에 이미 있는 관리 되는 인스턴스에 대해 장애 조치 (failover) 또는 연결이 중단 되지 않습니다.
