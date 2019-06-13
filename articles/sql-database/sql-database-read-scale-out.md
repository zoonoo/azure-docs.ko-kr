---
title: Azure SQL Database - 복제본에 대한 쿼리 읽기 | Microsoft Docs
description: Azure SQL Database는 읽기 확장 이라는 읽기 전용 복제본의 용량을 사용 하 여 읽기 전용 작업 부하를 분산 하는 기능을 제공 합니다.
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
ms.date: 06/03/2019
ms.openlocfilehash: 1b452fb0bac91429793f8d55e439c36c70784722
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492717"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>부하 분산 읽기 전용 쿼리 워크 로드를 읽기 전용 복제본을 사용 하 여

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

일부로 합니다 [고가용성 아키텍처](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), Premium, 업무용 또는 하이퍼 스케일 서비스 계층의 각 데이터베이스는 주 복제본과 여러 보조 복제본을 사용 하 여 자동으로 프로 비전 합니다. 보조 복제본은 주 복제본과 동일한 계산 크기로 프로 비전 됩니다. 합니다 **읽기 스케일 아웃** 기능 부하를 분산 SQL Database 읽기 전용 워크 로드 읽기 / 쓰기 복제본 공유 하지 않고 용량을 읽기 전용 복제본 중 하나를 사용 하 여 수 있습니다. 이러한 방식으로 읽기 전용 워크로드는 주 읽기-쓰기 작업에서 격리되고 해당 성능에 영향을 주지 않습니다. 이 기능은 논리적으로 분리 된 읽기 전용 워크 로드 분석 등을 포함 하는 응용 프로그램에 대 한 합니다. 이 추가 용량을 사용 하 여 없이 성능 이점을 얻을 수 있습니다 이러한 추가 비용입니다.

다음 다이어그램에서는 업무용 데이터베이스를 사용 하 여 보여 줍니다.

![읽기 전용 복제본](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

읽기 확장 기능은 새로운 프리미엄, 비즈니스 중요 한 대규모 데이터베이스에서 기본적으로 사용 됩니다. SQL 연결 문자열에 구성 된 경우 `ApplicationIntent=ReadOnly`, 응용 프로그램 게이트웨이에서 해당 데이터베이스의 읽기 전용 복제본으로 리디렉션됩니다. 사용 하는 방법에 대 한 정보에 대 한 합니다 `ApplicationIntent` 속성을 참조 하세요 [응용 프로그램 의도 지정](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)합니다.

응용 프로그램에 관계 없이 주 복제본에 연결 되는지 확인 하려는 경우는 `ApplicationIntent` SQL 연결 문자열에 설정, 명시적으로 해제 해야 읽기 스케일 아웃 데이터베이스를 만들 때 또는 해당 구성을 변경 하는 경우. 예를 들어, Premium, 업무용 또는 대규모 계층을 표준 또는 범용 계층에서 데이터베이스를 업그레이드 하 고 모든 연결이 계속 주 복제본으로 이동 해야 하는 경우 읽기 확장을 비활성화 합니다. 이 사용 하지 않도록 설정 하는 방법에 대 한 세부 정보를 참조 하세요 [을 사용 하도록 설정 하 고 읽기 확장을 사용 하지 않도록 설정](#enable-and-disable-read-scale-out)합니다.

> [!NOTE]
> 쿼리 데이터 저장소, 확장 이벤트, SQL Profiler 및 감사 기능 읽기 전용 복제본에서 지원 되지 않습니다. 

## <a name="data-consistency"></a>데이터 일관성

복제본의 이점 중 하나는 복제본이 트랜잭션 측면에서 항상 일관된 상태로 있지만 다른 시점에 서로 다른 복제본 간에 약간의 대기 시간이 있을 수 있다는 것입니다. 읽기 확장은 세션 수준 일관성을 지원합니다. 즉, 복제본 비 가용성으로 인해 연결 오류가 발생 한 후 읽기 전용 세션이 다시 연결을 100% 읽기 / 쓰기 복제본을 사용 하 여 최신 없는 복제본으로 리디렉션될 수 있습니다. 마찬가지로, 응용 프로그램 읽기-쓰기 세션을 사용 하 여 데이터를 기록 하 고 읽기 전용 세션을 사용 하 여 즉시 읽는 경우 최신 업데이트는 복제본에 즉시 표시 되지 않습니다. 비동기 트랜잭션 로그 다시 실행 작업 대기 시간이 발생 합니다.

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

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>모니터링 및 읽기 전용 복제본 문제 해결

읽기 전용 복제본에 연결할 때 사용 하 여 성능 메트릭을 액세스할 수 있습니다는 `sys.dm_db_resource_stats` DMV 합니다. 쿼리 계획 통계에 액세스 하려면 사용 합니다 `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` 및 `sys.dm_exec_sql_text` Dmv 합니다.

> [!NOTE]
> DMV `sys.resource_stats` 논리적 master 데이터베이스에서 주 복제본의 CPU 사용량 및 저장소 데이터를 반환 합니다.


## <a name="enable-and-disable-read-scale-out"></a>읽기 확장 사용 및 사용 안 함

읽기 확장 Premium, 중요 한 비즈니스 및 대규모 서비스 계층에서 기본으로 사용 됩니다. Basic, Standard 또는 범용 서비스 계층에서 읽기 확장을 사용할 수 없습니다. 읽기 확장 복제본으로 구성 된 대규모 데이터베이스에 자동으로 비활성화 됩니다. 

사용 하지 않도록 설정 하 고 단일 데이터베이스에 다음 메서드를 사용 하는 프리미엄 또는 중요 비즈니스용 서비스 계층에서 탄력적 풀 데이터베이스 읽기 확장을 다시 활성화 수 있습니다.

> [!NOTE]
> 이전 버전과 호환성에 대 한 읽기 확장을 사용 하지 않도록 설정 하는 기능이 제공 됩니다.

### <a name="azure-portal"></a>Azure portal

읽기 Sacle 스케일 아웃 설정을 관리할 수 있습니다 합니다 **구성** 데이터베이스 블레이드입니다. 

### <a name="powershell"></a>PowerShell

Azure PowerShell에서 읽기 확장을 관리하려면 2016년 12월 Azure PowerShell 릴리스 이상이 필요합니다. 최신 PowerShell 버전은 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)을 참조하세요.

다시 호출 하 여 Azure PowerShell에서 읽기 확장을 사용 하도록 설정 하거나 사용 하지 않도록 설정할 수 있습니다는 [집합 AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet 및 원하는 값 전달 `Enabled` 또는 `Disabled` -에 대 한를 `-ReadScale` 매개 변수입니다. 

기존 데이터베이스 (꺾쇠 괄호 안의 항목을 사용자 환경에 대 한 올바른 값으로 바꾸고 꺾쇠 괄호는 삭제)에 읽기 확장을 사용 하지 않도록 설정 합니다.

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
새 데이터베이스 (꺾쇠 괄호 안의 항목을 사용자 환경에 대 한 올바른 값으로 바꾸고 꺾쇠 괄호는 삭제)에 읽기 확장을 사용 하지 않도록 설정 합니다.

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

다시 활성화 하려면 기존 데이터베이스 (꺾쇠 괄호 안의 항목을 사용자 환경에 대 한 올바른 값으로 바꾸고 꺾쇠 괄호는 삭제)에 읽기 확장:

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

사용 하 여 다음 메서드를 사용 하 여 읽기 확장 비활성화를 사용 하 여 데이터베이스 만들기 또는 기존 데이터베이스에 대 한 설정을 변경 하는 `readScale` 속성이로 설정 `Enabled` 또는 `Disabled` 에서처럼는 아래 샘플 요청 합니다.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

자세한 내용은 [데이터베이스 - 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)를 참조하세요.

## <a name="using-tempdb-on-read-only-replica"></a>읽기 전용 복제본에 TempDB를 사용합니다.

TempDB 데이터베이스를 읽기 전용 복제본에 복제 되지 않습니다. 각 복제본에 복제본이 만들어질 때 생성 되는 TempDB 데이터베이스의 고유한 버전이 있습니다. 되도록 TempDB 업데이트가 가능 하며 쿼리 실행 하는 동안 수정할 수 있습니다. 읽기 전용 워크 로드에 TempDB 개체를 사용 하 여 종속 하는 경우 쿼리 스크립트의 일부로 이러한 개체를 만들어야 합니다. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>지역 복제 데이터베이스에서 읽기 확장 사용

읽기 확장 (예를 들어 장애 조치 그룹의 구성원)으로 지리적으로 복제 되는 데이터베이스에서 부하를 분산 읽기 전용 워크 로드를 사용 하는 경우 주 및 지역 복제 보조 데이터베이스에서 해당 읽기 확장을 사용할 수 있는지 확인 합니다. 이 구성은 응용 프로그램 장애 조치 후 새 주 데이터베이스에 연결 하는 경우 동일한 부하 분산 환경을 계속 되도록 해야 합니다. 읽기 확장을 사용할 수 있는 지역 복제된 보조 데이터베이스에 연결하는 경우, `ApplicationIntent=ReadOnly`가 있는 세션은 주 데이터베이스에서 연결을 라우팅하는 것과 동일한 방식으로 복제본 중 하나로 라우팅됩니다.  `ApplicationIntent=ReadOnly`가 없는 세션은 역시 읽기 전용인 지역 복제된 보조 데이터베이스의 주 복제본으로 라우팅됩니다. 지리적 복제 보조 데이터베이스에 주 데이터베이스 보다 다른 끝점에 지금까지 보조 복제본에 액세스 하려면이 되지 않았습니다 하는 데 필요한 설정 `ApplicationIntent=ReadOnly`합니다. 이전 버전과의 호환성을 보장하기 위해 `sys.geo_replication_links` DMV에 `secondary_allow_connections=2`(모든 클라이언트 연결이 허용됨)가 표시됩니다.

> [!NOTE]
> 라운드 로빈 또는 모든 다른 부하 분산 된 라우팅 보조 데이터베이스의 로컬 복제본 간에 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- SQL Database 대규모 제품에 대 한 내용은 [하이퍼 스케일 서비스 계층](./sql-database-service-tier-hyperscale.md)합니다.
