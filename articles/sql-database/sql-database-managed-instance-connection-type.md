---
title: 관리되는 인스턴스 연결 유형
description: 관리되는 인스턴스 연결 유형에 대해 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819468"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Azure SQL Database 관리 인스턴스 연결 형식

이 문서에서는 클라이언트가 연결 유형에 따라 Azure SQL Database 관리 인스턴스에 연결하는 방법을 설명합니다. 기본 연결 설정 변경과 관련된 고려 사항과 함께 연결 유형을 변경하는 스크립트 샘플이 아래에 제공됩니다.

## <a name="connection-types"></a>연결 형식

Azure SQL Database 관리 인스턴스는 다음 두 가지 연결 유형을 지원합니다.

- **리디렉션(권장):** 클라이언트는 데이터베이스를 호스팅하는 노드로 직접 연결을 설정합니다. 리디렉션을 사용하여 연결을 사용하려면 포트 1433 및 11000-11999에서 액세스를 허용하려면 방화벽 및 NSG(네트워크 보안 그룹)를 열어야 합니다. 패킷은 데이터베이스로 직접 이동하므로 프록시를 통한 리디렉션을 사용하여 대기 시간 및 처리량 성능이 향상됩니다.
- **프록시(기본값):** 이 모드에서는 모든 연결이 프록시 게이트웨이 구성 요소를 사용하고 있습니다. 연결을 사용하려면 개인 네트워크의 경우 포트 1433과 공용 연결의 경우 포트 3342만 열어야 합니다. 이 모드를 선택하면 워크로드의 특성에 따라 더 높은 대기 시간 및 더 낮은 처리량이 발생할 수 있습니다. 가장 낮은 대기 시간 및 높은 처리량을 위해 프록시 연결 정책을 통해 리디렉션 연결 정책을 사용하는 것이 좋습니다.

## <a name="redirect-connection-type"></a>연결 유형 리디렉션

리디렉션 연결 유형은 TCP 세션이 SQL 엔진에 설정된 후 클라이언트 세션이 로드 밸런서에서 가상 클러스터 노드의 대상 가상 IP를 가져오는 것을 의미합니다. 후속 패킷은 게이트웨이를 우회하여 가상 클러스터 노드로 직접 전달됩니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![리디렉션.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> 리디렉션 연결 유형은 현재 전용 끝점에서만 작동합니다. 연결 유형 설정에 관계없이 공용 끝점을 통해 들어오는 연결은 프록시를 통해 전달됩니다.

## <a name="proxy-connection-type"></a>프록시 연결 유형

프록시 연결 유형은 게이트웨이를 사용하여 TCP 세션이 설정되고 이후의 모든 패킷이 이를 통해 흐르도록 합니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![프록시.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>PowerShell을 사용하여 연결 유형 설정을 변경하는 스크립트

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 PowerShell 스크립트에서는 관리되는 인스턴스의 연결 유형을 리디렉션으로 변경하는 방법을 보여 주며 있습니다.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>다음 단계

- [관리되는 인스턴스에 데이터베이스 복원](sql-database-managed-instance-get-started-restore.md)
- [관리되는 인스턴스에서 공용 끝점을 구성하는](sql-database-managed-instance-public-endpoint-configure.md) 방법 알아보기
- [관리형 인스턴스 연결 아키텍처에](sql-database-managed-instance-connectivity-architecture.md) 대해 자세히 알아보기