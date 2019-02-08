---
title: Azure SQL Database - 복제본에 대한 쿼리 읽기 | Microsoft Docs
description: Azure SQL Database는 읽기 확장이라는 읽기 전용 복제본의 용량을 사용하여 읽기 전용 작업의 부하를 분산하는 기능을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: cf32f3998e254e8f4a9c347980718dbc8d0b13c4
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461647"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>읽기 전용 복제본을 사용하여 읽기 전용 쿼리 작업의 부하 분산(미리 보기)

**읽기 확장**을 사용하면 읽기 전용 복제본 하나의 용량을 사용하여 Azure SQL Database 읽기 전용 작업의 부하를 분산할 수 있습니다.

프리미엄 계층([DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)) 또는 중요 비즈니스용 계층([vCore 기반 구매 모델](sql-database-service-tiers-vcore.md))의 각 데이터베이스는 가용성 SLA를 지원하기 위해 여러 개의 AlwaysON 복제본에 자동으로 프로비전됩니다.

![읽기 전용 복제본](media/sql-database-managed-instance/business-critical-service-tier.png)

이러한 복제본은 일반 데이터베이스 연결에서 사용되는 읽기/쓰기 복제본과 동일한 계산 크기로 프로비전됩니다. **읽기 확장** 기능을 사용하면 읽기/쓰기 복제본을 공유하는 대신 읽기 전용 복제본 중 하나의 용량을 사용하여 SQL Database 읽기 전용 작업의 부하를 분산할 수 있습니다. 이러한 방식으로 읽기 전용 워크로드는 주 읽기-쓰기 작업에서 격리되고 해당 성능에 영향을 주지 않습니다. 기능은 분석과 같은 논리적으로 구분된 읽기 전용 워크로드를 포함하는 애플리케이션을 위한 것이므로 추가 비용 없이 이 추가 용량을 사용하여 성능 혜택을 활용할 수 있습니다.

특정 데이터베이스에서 읽기 확장 기능을 사용하려면, 데이터베이스를 만들 때 또는 나중에 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) 또는 [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlet을 호출하는 PowerShell을 사용하거나 [데이터베이스 - 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) 방법을 사용하는 Azure Resource Manager REST API를 통해 해당 구성을 변경하여 명시적으로 사용하도록 설정해야 합니다.

데이터베이스에 대해 읽기 확장을 사용하도록 설정하면, 애플리케이션의 연결 문자열에 구성된 `ApplicationIntent` 속성에 따라 해당 데이터베이스에 연결하는 애플리케이션이 해당 데이터베이스의 읽기-쓰기 복제본 또는 읽기 전용 복제본으로 전달됩니다. `ApplicationIntent` 속성에 대한 자세한 내용은 [애플리케이션 의도 지정](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)을 참조하세요.

읽기 스케일 아웃을 사용할 수 없거나 지원되지 않는 서비스 계층에서 ReadScale 속성을 설정한 경우 모든 연결은 `ApplicationIntent` 속성과 독립적으로 읽기/쓰기 복제본으로 이동됩니다.

> [!NOTE]
> 미리 보기 동안에는 쿼리 데이터 저장소 및 확장 이벤트가 읽기 전용 복제본에서 지원되지 않습니다.

## <a name="data-consistency"></a>데이터 일관성

복제본의 이점 중 하나는 복제본이 트랜잭션 측면에서 항상 일관된 상태로 있지만 다른 시점에 서로 다른 복제본 간에 약간의 대기 시간이 있을 수 있다는 것입니다. 읽기 확장은 세션 수준 일관성을 지원합니다. 즉, 복제본 비가용성으로 인해 연결 오류가 발생한 후 읽기 전용 세션이 다시 연결되면, 읽기/쓰기 복제본이 포함된 100% 최신이 아닌 복제본으로 리디렉션될 수 있습니다. 마찬가지로 애플리케이션에서 읽기/쓰기 세션을 사용하여 데이터를 쓰고 읽기 전용 세션을 사용하여 즉시 읽는 경우 최신 업데이트가 즉시 표시되지 않을 수도 있습니다. 이는 복제본에 대한 트랜잭션 로그 다시 실행이 비동기적이기 때문입니다.

> [!NOTE]
> 지역 내의 복제 대기 시간은 낮으며 이 상황은 드물게 발생합니다.

## <a name="connect-to-a-read-only-replica"></a>읽기 전용 복제본에 연결

데이터베이스에 대한 읽기 확장을 사용하도록 설정하면 클라이언트에서 제공하는 연결 문자열의 `ApplicationIntent` 옵션은 연결이 쓰기 복제본 또는 읽기 전용 복제본으로 라우팅되는지 여부를 나타냅니다. 특히 `ApplicationIntent` 값이 `ReadWrite`(기본값)이면 연결이 데이터베이스의 읽기/쓰기 복제본으로 전달됩니다. 이는 기존 동작과 동일합니다. `ApplicationIntent` 값이 `ReadOnly`이면 연결이 읽기 전용 복제본으로 라우팅됩니다.

예를 들어 다음 연결 문자열은 클라이언트를 읽기 전용 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

다음 연결 문자열 중 하나는 클라이언트를 읽기/쓰기 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>읽기 전용 복제본에 대한 연결인지 확인

다음 쿼리를 실행하여 읽기 전용 복제본에 연결되어 있는지 여부를 확인할 수 있습니다. 읽기 전용 복제본에 연결된 경우 READ_ONLY가 반환됩니다.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> ReadOnly 세션에서는 항상 AlwaysON 복제본 중 하나만 액세스할 수 있습니다.

## <a name="enable-and-disable-read-scale-out"></a>읽기 확장 사용 및 사용 안 함

읽기 스케일 아웃은 [Managed Instance](sql-database-managed-instance.md) 중요 비즈니스용 계층에 기본적으로 사용됩니다. [SQL Database 서버](sql-database-servers.md) Premium 및 Business Critical 계층에 배치된 데이터베이스에서는 읽기 확장을 명시적으로 사용하도록 설정해야 합니다. 읽기 확장을 사용 및 사용하지 않도록 설정하는 방법은 여기에 설명되어 있습니다.

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell: 읽기 확장 사용 및 사용 안 함

Azure PowerShell에서 읽기 확장을 관리하려면 2016년 12월 Azure PowerShell 릴리스 이상이 필요합니다. 최신 PowerShell 버전은 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)을 참조하세요.

Azure PowerShell에서 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) cmdlet을 호출하고 `-ReadScale` 매개 변수에 대해 원하는 값(`Enabled` 또는 `Disabled`)을 전달하여 읽기 확장을 사용하거나 사용하지 않도록 설정합니다. 또는 [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlet을 사용하여 읽기 확장을 사용하도록 설정된 새 데이터베이스를 만들 수 있습니다.

예를 들어 기존 데이터베이스에 대해 읽기 확장을 사용하도록 설정하려면 다음을 수행합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

기존 데이터베이스에 대해 읽기 확장을 사용하지 않도록 설정하려면 다음을 수행합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

읽기 확장을 사용하지 않도록 설정된 새 데이터베이스를 만들려면 다음을 수행합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>REST API: 읽기 확장 사용 및 사용 안 함

읽기 확장을 사용하도록 설정된 데이터베이스를 만들거나 기존 데이터베이스에 대해 읽기 확장을 사용하거나 사용하지 않도록 설정하려면, 아래 샘플 요청과 같이 `readScale` 속성이 `Enabled` 또는 `Disabled`로 설정된 해당 데이터베이스 엔터티를 만들거나 업데이트합니다.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

자세한 내용은 [데이터베이스 - 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)를 참조하세요.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>지역 복제 데이터베이스에서 읽기 확장 사용

읽기 확장을 사용하여 지역 복제된(예: 장애 조치(failover) 그룹의 구성원으로) 데이터베이스에 대한 읽기 전용 워크로드의 부하를 분산하는 경우, 주 데이터베이스와 지역 복제된 보조 데이터베이스 둘 다에서 읽기 확장이 사용하도록 설정되었는지 확인합니다. 이렇게 하면 장애 조치(failover) 후에 애플리케이션이 새로운 주 데이터베이스에 연결할 때 동일한 부하 분산이 적용됩니다. 읽기 확장을 사용할 수 있는 지역 복제된 보조 데이터베이스에 연결하는 경우, `ApplicationIntent=ReadOnly`가 있는 세션은 주 데이터베이스에서 연결을 라우팅하는 것과 동일한 방식으로 복제본 중 하나로 라우팅됩니다.  `ApplicationIntent=ReadOnly`가 없는 세션은 역시 읽기 전용인 지역 복제된 보조 데이터베이스의 주 복제본으로 라우팅됩니다. 지역 복제된 보조 데이터베이스에는 주 데이터베이스와 다른 끝점이 있으므로 지금까지 보조 데이터베이스에 액세스하기 위해 `ApplicationIntent=ReadOnly`를 설정할 필요는 없었습니다. 이전 버전과의 호환성을 보장하기 위해 `sys.geo_replication_links` DMV에 `secondary_allow_connections=2`(모든 클라이언트 연결이 허용됨)가 표시됩니다.

> [!NOTE]
> 미리 보기 기간에는 보조 데이터베이스의 로컬 복제본 간에 라운드 로빈 또는 기타 부하 분산 라우팅이 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- PowerShell을 사용하여 읽기 확장을 설정하는 방법에 대한 자세한 내용은 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) 또는 [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlet을 참조하세요.
- REST API를 사용하여 읽기 확장을 설정하는 방법에 대한 내용은 [데이터베이스 - 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)를 참조하세요.
