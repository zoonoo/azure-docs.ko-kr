---
title: 복제본에 대한 쿼리 읽기
description: Azure SQL Database는 읽기 전용 복제본(읽기 확장-축소)의 용량을 사용하여 읽기 전용 워크로드의 로드 균형을 맞추는 기능을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1a1b9907cd931716949d92d948a7d541fd2d5057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206948"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>읽기 전용 복제본을 사용하여 읽기 전용 쿼리 워크로드의 부하 분산

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[고가용성 아키텍처의](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)일부로 프리미엄 및 비즈니스 중요 서비스 계층의 각 데이터베이스는 주 복제본 및 여러 보조 복제본으로 자동으로 프로비전됩니다. 보조 복제본은 기본 복제본과 동일한 계산 크기로 프로비전됩니다. **읽기 확장 기능을** 사용하면 읽기-쓰기 복제본을 공유하는 대신 읽기 전용 복제본 중 하나의 용량을 사용하여 SQL Database 읽기 전용 워크로드를 로드밸런싱할 수 있습니다. 이러한 방식으로 읽기 전용 워크로드는 주 읽기-쓰기 작업에서 격리되고 해당 성능에 영향을 주지 않습니다. 이 기능은 분석과 같이 논리적으로 분리된 읽기 전용 워크로드를 포함하는 응용 프로그램을 위한 것입니다. 프리미엄 및 비즈니스 크리티컬 서비스 계층에서 응용 프로그램은 추가 비용 없이 이 추가 용량을 사용하여 성능 이점을 얻을 수 있습니다.

하나 이상의 보조 복제본을 만들 때 하이퍼스케일 서비스 계층에서도 **배율 조정 읽기** 기능을 사용할 수 있습니다. 읽기 전용 워크로드에 하나의 보조 복제본에서 사용할 수 있는 것보다 많은 리소스가 필요한 경우 여러 보조 복제본을 사용할 수 있습니다. 기본, 표준 및 범용 서비스 계층의 고가용성 아키텍처에는 복제본이 포함되지 않습니다. 이러한 서비스 계층에서는 **배율 조정 읽기** 기능을 사용할 수 없습니다.

다음 다이어그램은 비즈니스 크리티컬 데이터베이스를 사용하여 보여 줍니다.

![읽기 전용 복제본](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

새 프리미엄, 비즈니스 중요 및 하이퍼스케일 데이터베이스에서 기본적으로 배율 조정 읽기 기능이 활성화됩니다. 하이퍼스케일의 경우 새 데이터베이스에 대해 기본적으로 하나의 보조 복제본이 만들어집니다. SQL 연결 문자열이 구성된 `ApplicationIntent=ReadOnly`경우 응용 프로그램은 게이트웨이에서 해당 데이터베이스의 읽기 전용 복제본으로 리디렉션됩니다. 속성 사용 방법에 대한 자세한 내용은 [응용 프로그램 의도 지정](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)을 참조하십시오. `ApplicationIntent`

SQL 연결 문자열의 `ApplicationIntent` 설정에 관계없이 응용 프로그램이 기본 복제본에 연결되도록 하려면 데이터베이스를 만들 때 또는 구성을 변경할 때 읽기 축소를 명시적으로 사용하지 않도록 설정해야 합니다. 예를 들어 데이터베이스를 표준 또는 범용 계층에서 프리미엄, 비즈니스 크리티컬 또는 하이퍼스케일 계층으로 업그레이드하고 모든 연결이 기본 복제본으로 계속 이동하도록 하려는 경우 확장 읽기를 사용하지 않도록 설정합니다. 비활성화 하는 방법에 대 한 자세한 내용은 [참조 읽기 배율 확장](#enable-and-disable-read-scale-out)을 사용 하 고 사용 하지 않도록 설정 합니다.

> [!NOTE]
> 쿼리 데이터 저장소, 확장 이벤트 및 SQL 프로파일러 기능은 읽기 전용 복제본에서 지원되지 않습니다.

## <a name="data-consistency"></a>데이터 일관성

복제본의 이점 중 하나는 복제본이 트랜잭션 측면에서 항상 일관된 상태로 있지만 다른 시점에 서로 다른 복제본 간에 약간의 대기 시간이 있을 수 있다는 것입니다. 읽기 확장은 세션 수준 일관성을 지원합니다. 즉, 복제본 가용성으로 인한 연결 오류 후 읽기 전용 세션이 다시 연결되면 읽기-쓰기 복제본을 사용하여 100% 최신이 아닌 복제본으로 리디렉션될 수 있습니다. 마찬가지로 응용 프로그램이 읽기-쓰기 세션을 사용하여 데이터를 작성하고 읽기 전용 세션을 사용하여 즉시 읽는 경우 최신 업데이트가 복제본에 즉시 표시되지 않을 수 있습니다. 대기 시간은 비동기 트랜잭션 로그 다시 실행 작업으로 인해 발생합니다.

> [!NOTE]
> 지역 내의 복제 대기 시간은 낮으며 이 상황은 드물게 발생합니다.

## <a name="connect-to-a-read-only-replica"></a>읽기 전용 복제본에 연결

데이터베이스에 대한 읽기 확장을 사용하도록 설정하면 클라이언트에서 제공하는 연결 문자열의 `ApplicationIntent` 옵션은 연결이 쓰기 복제본 또는 읽기 전용 복제본으로 라우팅되는지 여부를 나타냅니다. 특히 `ApplicationIntent` 값이 `ReadWrite`(기본값)이면 연결이 데이터베이스의 읽기/쓰기 복제본으로 전달됩니다. 이는 기존 동작과 동일합니다. `ApplicationIntent` 값이 `ReadOnly`이면 연결이 읽기 전용 복제본으로 라우팅됩니다.

예를 들어 다음 연결 문자열은 클라이언트를 읽기 전용 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

다음 연결 문자열 중 하나는 클라이언트를 읽기/쓰기 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>읽기 전용 복제본에 대한 연결인지 확인

다음 쿼리를 실행하여 읽기 전용 복제본에 연결되어 있는지 여부를 확인할 수 있습니다. 읽기 전용 복제본에 연결된 경우 READ_ONLY가 반환됩니다.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> ReadOnly 세션에서는 항상 AlwaysON 복제본 중 하나만 액세스할 수 있습니다.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>읽기 전용 복제본 모니터링 및 문제 해결

읽기 전용 복제본에 연결하면 DMV를 사용하여 `sys.dm_db_resource_stats` 성능 메트릭에 액세스할 수 있습니다. 쿼리 계획 통계에 액세스하려면 `sys.dm_exec_query_stats` `sys.dm_exec_query_plan` 에서 `sys.dm_exec_sql_text` 및 DMV를 사용합니다.

> [!NOTE]
> 논리 마스터 `sys.resource_stats` 데이터베이스의 DMV는 기본 복제본의 CPU 사용량 및 저장소 데이터를 반환합니다.

## <a name="enable-and-disable-read-scale-out"></a>읽기 확장 사용 및 사용 안 함

읽기 확장 은 프리미엄, 비즈니스 중요 및 하이퍼 스케일 서비스 계층에서 기본적으로 활성화됩니다. 기본, 표준 또는 범용 서비스 계층에서는 읽기 배율 조정을 사용할 수 없습니다. 읽기 배율 조정은 0개의 복제본으로 구성된 하이퍼스케일 데이터베이스에서 자동으로 비활성화됩니다.

다음 방법을 사용하여 프리미엄 또는 비즈니스 크리티컬 서비스 계층에서 단일 데이터베이스 및 탄력적 풀 데이터베이스에서 확장 읽기를 사용하지 않도록 설정하고 다시 활성화할 수 있습니다.

> [!NOTE]
> 이전 버전과의 호환성을 위해 읽기 배율 조정을 사용하지 않도록 설정하는 기능이 제공됩니다.

### <a name="azure-portal"></a>Azure portal

**데이터베이스 구성** 블레이드에서 확장 읽기 설정을 관리할 수 있습니다.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> PowerShell Azure 리소스 관리자(RM) 모듈은 Azure SQL Database에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈용입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 받을 것입니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 거의 동일합니다. 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개를](/powershell/azure/new-azureps-module-az)참조하십시오.

Azure PowerShell에서 읽기 확장을 관리하려면 2016년 12월 Azure PowerShell 릴리스 이상이 필요합니다. 최신 PowerShell 버전은 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)을 참조하세요.

[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet을 호출하고 `Enabled` `Disabled` `-ReadScale` 매개 변수에 대해 원하는 값(또는 --

기존 데이터베이스에서 읽기 배율을 사용하지 않도록 설정하려면(각 괄호의 항목을 환경에 맞는 올바른 값으로 대체하고 각도 대괄호를 삭제)합니다.

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

새 데이터베이스에서 읽기 배율을 사용하지 않도록 설정하려면(각 괄호의 항목을 환경에 맞는 올바른 값으로 대체하고 각도 대괄호를 삭제)합니다.

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

기존 데이터베이스에서 읽기 배율을 다시 활성화하려면(각 괄호의 항목을 환경에 맞는 올바른 값으로 대체하고 각도 대괄호를 삭제)합니다.

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

읽기 배율 조정이 비활성화된 데이터베이스를 만들거나 기존 데이터베이스에 대한 설정을 변경하려면 `readScale` 아래 샘플 `Enabled` `Disabled` 요청또는 아래 와 같이 속성이 설정된 다음 방법을 사용합니다.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

자세한 내용은 [데이터베이스 - 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)를 참조하세요.

## <a name="using-tempdb-on-read-only-replica"></a>읽기 전용 복제본에서 TempDB 사용

TempDB 데이터베이스는 읽기 전용 복제본에 복제되지 않습니다. 각 복제본에는 복제본을 만들 때 생성되는 자체 버전의 TempDB 데이터베이스가 있습니다. 이를 통해 TempDB를 업데이트할 수 있으며 쿼리 실행 중에 수정할 수 있습니다. 읽기 전용 워크로드가 TempDB 개체 사용에 따라 달라지면 쿼리 스크립트의 일부로 이러한 개체를 만들어야 합니다.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>지역 복제 데이터베이스에서 읽기 확장 사용

크기 조정 읽기를 사용하여 지리적으로 복제된 데이터베이스에서 읽기 전용 워크로드를 로드하는 경우(예: 장애 조치 그룹의 구성원) 기본 데이터베이스와 지리적 복제보조 데이터베이스 모두에서 읽기 배율 조정이 활성화되어 있는지 확인합니다. 이 구성은 응용 프로그램이 장애 조치 후 새 주 에 연결할 때 동일한 로드 밸런싱 환경이 계속되도록 합니다. 읽기 확장을 사용할 수 있는 지역 복제된 보조 데이터베이스에 연결하는 경우, `ApplicationIntent=ReadOnly`가 있는 세션은 주 데이터베이스에서 연결을 라우팅하는 것과 동일한 방식으로 복제본 중 하나로 라우팅됩니다.  `ApplicationIntent=ReadOnly`가 없는 세션은 역시 읽기 전용인 지역 복제된 보조 데이터베이스의 주 복제본으로 라우팅됩니다. 지리적으로 복제된 보조 데이터베이스는 기본 데이터베이스와 다른 끝점을 가지고 있기 때문에, 역사적으로 `ApplicationIntent=ReadOnly`는 설정하지 않아도 된 보조 데이터베이스에 액세스합니다. 이전 버전과의 호환성을 보장하기 위해 `sys.geo_replication_links` DMV에 `secondary_allow_connections=2`(모든 클라이언트 연결이 허용됨)가 표시됩니다.

> [!NOTE]
> 라운드 로빈 또는 보조 데이터베이스의 로컬 복제본 간의 다른 로드 균형 라우팅은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- SQL 데이터베이스 하이퍼스케일 오퍼링에 대한 자세한 내용은 [하이퍼스케일 서비스 계층을](./sql-database-service-tier-hyperscale.md)참조하십시오.
