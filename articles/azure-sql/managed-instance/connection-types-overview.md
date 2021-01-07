---
title: 연결 유형
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 연결 형식에 대 한 자세한 정보
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f1c4fe8268d24026609f55d76a102a5c9a4e8295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91356319"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Azure SQL Managed Instance 연결 형식
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 클라이언트에서 연결 유형에 따라 Azure SQL Managed Instance에 연결 하는 방법을 설명 합니다. 연결 유형을 변경 하는 스크립트 예제는 기본 연결 설정 변경과 관련 된 고려 사항과 함께 아래에 제공 됩니다.

## <a name="connection-types"></a>연결 유형

Azure SQL Managed Instance는 다음과 같은 두 가지 연결 유형을 지원 합니다.

- **리디렉션 (권장):** 클라이언트는 데이터베이스를 호스팅하는 노드에 대 한 연결을 직접 설정 합니다. 리디렉션을 사용하여 연결하려면 포트 1433 및 11000-11999에서 액세스할 수 있도록 방화벽 및 NSG(네트워크 보안 그룹)를 열어야 합니다. 패킷은 데이터베이스로 직접 이동하므로 프록시를 통한 리디렉션을 사용하여 대기 시간 및 처리량 성능이 향상됩니다.
- **프록시 (기본값):** 이 모드에서 모든 연결은 프록시 게이트웨이 구성 요소를 사용 합니다. 연결을 사용하도록 설정하려면 프라이빗 네트워크용 포트 1433과 공용 연결용 포트 3342만 열어야 합니다. 이 모드를 선택하면 워크로드의 특성에 따라 더 높은 대기 시간 및 더 낮은 처리량이 발생할 수 있습니다. 가장 낮은 대기 시간 및 높은 처리량을 위해 프록시 연결 정책을 통해 리디렉션 연결 정책을 사용하는 것이 좋습니다.

## <a name="redirect-connection-type"></a>리디렉션 연결 유형

리디렉션 연결 형식에서 TCP 세션이 SQL 엔진에 설정 된 후 클라이언트 세션은 부하 분산 장치에서 가상 클러스터 노드의 대상 가상 IP를 가져옵니다. 이후 패킷은 게이트웨이를 우회 하 여 가상 클러스터 노드로 직접 전달 됩니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![다이어그램은 Azure virtual network의 게이트웨이에 연결 된 리디렉션-검색 db가 있는 온-프레미스 네트워크와 가상 네트워크의 데이터베이스 주 노드에 연결 된 리디렉션 쿼리를 보여 줍니다.](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> 리디렉션 연결 유형은 현재 개인 끝점에 대해서만 작동 합니다. 연결 형식 설정에 관계 없이 공용 끝점을 통해 들어오는 연결은 프록시를 통해 발생 합니다.

## <a name="proxy-connection-type"></a>프록시 연결 형식

프록시 연결 형식에서는 게이트웨이를 사용 하 여 TCP 세션이 설정 되 고 모든 후속 패킷이이를 통과 합니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![다이어그램은 Azure virtual network의 게이트웨이에 연결 된 프록시가 있는 온-프레미스 네트워크를 보여 주며, 가상 네트워크의 데이터베이스 주 노드 옆에 연결 합니다.](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>연결 형식 변경

- **포털 사용:** Azure Portal를 사용 하 여 연결 유형을 변경 하려면 Virtual Network 페이지를 열고 **연결 유형** 설정을 사용 하 여 연결 유형을 변경 하 고 변경 내용을 저장 합니다.

- **PowerShell을 사용 하 여 연결 형식 설정을 변경 하는 스크립트:**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

다음 PowerShell 스크립트에서는 관리 되는 인스턴스의 연결 형식을로 변경 하는 방법을 보여 줍니다 `Redirect` .

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

- [SQL Managed Instance로 데이터베이스 복원](restore-sample-database-quickstart.md)
- [SQL Managed Instance에서 공용 끝점을 구성](public-endpoint-configure.md) 하는 방법에 대해 알아봅니다.
- [SQL Managed Instance 연결 아키텍처](connectivity-architecture-overview.md) 에 대해 알아보기
