---
title: "Azure SQL Data Warehouse의 계산 능력 관리(개요) | Microsoft Docs"
description: "Azure SQL 데이터 웨어하우스의 성능 확장 기능입니다. 또는 DWU를 조정하거나 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 16d3db2737db70119c75991388c8c763208ad1fa


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Azure SQL 데이터 웨어하우스의 계산 능력 관리(개요)
> [!div class="op_single_selector"]
> * [개요](sql-data-warehouse-manage-compute-overview.md)
> * [포털](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST (영문)](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

SQL 데이터 웨어하우스는 저장소와 계산을 분리하여 각각의 성능을 독립적으로 조정할 수 있습니다. 따라서 성능을 확장하는 동시에 필요한 성능에 대해서만 지불하여 비용을 절감할 수 있습니다. 

이 개요는 SQL 데이터 웨어하우스에 대한 다음과 같은 성능 확장 기능에 대해 설명하고 사용 방법 및 시기에 대한 권장 사항을 제공합니다. 

* [DWU(데이터 웨어하우스 단위)][DWU(데이터 웨어하우스 단위)]를 조정하여 계산 능력 조정
* 계산 리소스 일시 중지 또는 다시 시작

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>성능 조정
SQL 데이터 웨어하우스에서 CPU의 계산 리소스, 메모리, I/O 대역폭을 늘리거나 줄여 성능을 빠르게 확장 또는 축소할 수 있습니다. 성능을 조정할 경우 SQL 데이터 웨어하우스에서 데이터베이스에 할당하는 [DWU(데이터 웨어하우스 단위)][DWU(데이터 웨어하우스 단위)] 수를 조정하기만 하면 됩니다. SQL 데이터 웨어하우스가 빠르게 변경하고 하드웨어 또는 소프트웨어의 모든 기본 변경 사항을 처리합니다.

데이터 웨어하우스의 성능을 높이기 위해 어떤 유형의 프로세서, 얼마나 많은 메모리, 어떤 유형의 저장소가 필요한지 조사해야 했던 시절은 지나갔습니다. 클라우드에 데이터 웨어하우스를 배치함으로써 더 이상 하위 수준의 하드웨어 문제를 처리하지 않아도 됩니다. 대신 SQL 데이터 웨어하우스는 다음과 같은 질문을 합니다. 데이터를 얼마나 빨리 분석하고 싶나요? 

### <a name="how-do-i-scale-performance"></a>성능을 조정하려면 어떻게 해야 합니까?
계산 성능을 탄력적으로 증가 또는 감소하려면 데이터베이스의 [DWU(데이터 웨어하우스 단위)][DWU(데이터 웨어하우스 단위)]를 변경하기만 하면 됩니다. 더 많은 DWU를 추가하면 성능이 연속해서 증가합니다.  높은 DWU 수준에서 성능이 크게 향상되었음을 감지하려면 DWU를 100개 이상 추가해야 합니다. 의미 있는 DWU 증가분을 선택할 때에는 최고의 결과를 제공하는 DWU 수준을 참조할 수 있습니다.

DWU를 조정할 경우 다음과 같은 개별 방법을 사용할 수 있습니다.

* [Azure 포털을 사용하여 계산 능력 조정][Azure 포털을 사용하여 계산 능력 조정]
* [PowerShell을 사용하여 계산 능력 조정][PowerShell을 사용하여 계산 능력 조정]
* [REST API를 사용하여 계산 능력 조정][REST API를 사용하여 계산 능력 조정]
* [TSQL을 사용하여 계산 능력 조정][TSQL을 사용하여 계산 능력 조정]

### <a name="how-many-dwus-should-i-use"></a>얼마나 많은 DWU를 사용해야 합니까?
SQL 데이터 웨어하우스의 성능은 선형적으로 조정되며 하나의 계산 규모에서 다른 규모로의 변경은(예를 들어 100DWU에서 2000DWU로) 몇 초 안에 실행됩니다. 따라서 시나리오에 가장 적합한 설정을 결정할 때까지 다양한 DWU 설정을 유연하게 실험해볼 수 있습니다.

사용자에게 이상적인 DWU가 무엇인지 파악하기 위해서는 규모를 키우거나 줄이고 데이터를 로드한 후에 몇 가지 쿼리를 실행해 봅니다. 규모 조정이 신속하게 이뤄지기 때문에 한 시간 내에 다양한 수준의 성능을 시도해 볼 수 있습니다. SQL 데이터 웨어하우스 는 많은 양의 데이터를 처리하고 크기 조정을 위한 실제 용량을 확인하도록 설계되었습니다. 특히 더 큰 규모를 제공하면 1TB에 근접하거나 초과하는 큰 데이터 집합을 사용합니다.

워크로드에 가장 적합한 DWU를 찾는 방법에 대한 권장 사항:

1. 개발 중인 데이터 웨어하우스의 경우 가장 먼저 적은 수의 DWU를 선택합니다.  적합한 시작 지점은 DW400 또는 DW200입니다.
2. 응용 프로그램 성능을 모니터링하여 선택한 DWU 수와 관찰한 성능을 비교합니다.
3. 선형 크기 조정을 가정하여 요구 사항에 맞는 최적 성능 수준을 달성하기 위해 성능이 얼마나 더 빠르거나 느려야 하는지 확인합니다.
4. 원하는 워크로드 성능에 비례하여 DWU 수를 늘리거나 줄입니다. 그러면 서비스가 신속하게 응답하고 새로운 DWU 요구 사항에 맞게 계산 리소스를 조정합니다.
5. 비즈니스 요구 사항에 맞는 최적 성능 수준에 도달할 때까지 계속 조정합니다.

### <a name="when-should-i-scale-dwus"></a>언제 DWU를 조정해야 하나요?
결과가 빨리 필요하면, DWU를 높이고 더 높은 성능에 대한 비용을 지불합니다.  계산 파워가 덜 필요하면, DWU를 낮추고 필요한 것에만 비용을 지불합니다. 

DWU 성능 조정 시기에 대한 권장 사항:

1. 응용 프로그램 워크로드에 변동이 많은 경우 최대치와 최저치에 맞게 DWU 수준을 늘리거나 줄입니다. 예를 들어 일반적으로 월말에 워크로드가 최대치에 도달하는 경우 이 시기에 DWU를 추가하고 이 시기가 끝나면 다시 축소하는 계획을 세웁니다.
2. 대량의 데이터 로딩 또는 변환 작업을 수행하기 전에 데이터를 더욱 빠르게 사용할 수 있도록 DWU를 확장합니다.

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>계산 일시 중지
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

데이터베이스를 일시 중지하려면 다음과 같은 개별 방법을 사용합니다.

* [Azure Portal을 사용하여 계산 일시 중지][Azure Portal을 사용하여 계산 일시 중지]
* [PowerShell을 사용하여 계산 일시 중지][PowerShell을 사용하여 계산 일시 중지]
* [REST API를 사용하여 계산 일시 중지][REST API를 사용하여 계산 일시 중지]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>계산 다시 시작
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

데이터베이스를 다시 시작하려면 다음과 같은 개별 방법을 사용합니다.

* [Azure Portal을 사용하여 계산 다시 시작][Azure Portal을 사용하여 계산 다시 시작]
* [PowerShell을 사용하여 계산 다시 시작][PowerShell을 사용하여 계산 다시 시작]
* [REST API를 사용하여 계산 다시 시작][REST API를 사용하여 계산 다시 시작]

## <a name="permissions"></a>권한
데이터베이스 크기를 조정하려면 [ALTER DATABASE][ALTER DATABASE]에 설명된 권한이 필요합니다.  일시 중지 및 다시 시작을 위해서는 [SQL DB 참여자][SQL DB 참여자] 권한, 특히 Microsoft.Sql/servers/databases/action이 필요합니다.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>다음 단계
일부 추가적인 주요 성능 개념을 이해하는 데 도움이 필요하다면 다음 문서들을 참조하십시오.

* [워크로드 및 동시성 관리][워크로드 및 동시성 관리]
* [테이블 디자인 개요][테이블 디자인 개요]
* [테이블 배포][테이블 배포]
* [테이블 인덱싱][테이블 인덱싱]
* [테이블 분할][테이블 분할]
* [테이블 통계][테이블 통계]
* [모범 사례][모범 사례]

<!--Image reference-->

<!--Article references-->
[DWU(데이터 웨어하우스 단위)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Azure 포털을 사용하여 계산 능력 조정]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[PowerShell을 사용하여 계산 능력 조정]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[REST API를 사용하여 계산 능력 조정]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[TSQL을 사용하여 계산 능력 조정]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[용량 제한]: ./sql-data-warehouse-service-capacity-limits.md

[Azure Portal을 사용하여 계산 일시 중지]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[PowerShell을 사용하여 계산 일시 중지]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[REST API를 사용하여 계산 일시 중지]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Azure Portal을 사용하여 계산 다시 시작]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[PowerShell을 사용하여 계산 다시 시작]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[REST API를 사용하여 계산 다시 시작]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[워크로드 및 동시성 관리]: ./sql-data-warehouse-develop-concurrency.md
[테이블 디자인 개요]: ./sql-data-warehouse-tables-overview.md
[테이블 배포]: ./sql-data-warehouse-tables-distribute.md
[테이블 인덱싱]: ./sql-data-warehouse-tables-index.md
[테이블 분할]: ./sql-data-warehouse-tables-partition.md
[테이블 통계]: ./sql-data-warehouse-tables-statistics.md
[모범 사례]: ./sql-data-warehouse-best-practices.md 
[개발 개요]: ./sql-data-warehouse-overview-develop.md

[SQL DB 참여자]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[쉬운 테이블]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->


