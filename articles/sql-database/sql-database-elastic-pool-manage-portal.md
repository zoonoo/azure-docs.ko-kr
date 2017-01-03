---
title: "Azure Portal을 사용하여 탄력적 데이터베이스 풀 모니터링 및 관리 | Microsoft Docs"
description: "Azure 포털 및 SQL 데이터베이스의 기본 제공 인텔리전스를 사용하여 성능을 최적화하고 비용을 관리하기 위해 확장성 있는 탄력적 데이터베이스 풀을 관리, 모니터링, 적절히 조정하는 방법에 대해 알아봅니다."
keywords: 
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: cgronlun
ms.assetid: 3dc9b7a3-4b10-423a-8e44-9174aca5cf3d
ms.service: sql-database
ms.devlang: NA
ms.date: 11/17/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 37249c24c3e74ff9f4fcf9362b6053d1ba7b0cbd
ms.openlocfilehash: 10bb7d9672824b00a1e3ba02e0e3d1db4260b128


---
# <a name="monitor-and-manage-an-elastic-database-pool-with-the-azure-portal"></a>Azure 포털을 사용하여 탄력적 데이터베이스 풀 모니터링 및 관리
> [!div class="op_single_selector"]
> * [Azure 포털](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
>
>

Azure Portal을 사용하여 풀에서 탄력적 풀 및 데이터베이스를 모니터링하고 관리할 수 있습니다. 포털에서 탄력적 풀 및 해당 풀 내의 데이터베이스의 사용률을 모니터링할 수 있습니다. 탄력적 풀에 일련의 내용을 변경하는 동시에 모든 변경 내용을 전송할 수도 있습니다. 이러한 변경 내용에는 데이터베이스 추가 또는 제거, 탄력적 풀 설정 변경, 데이터베이스 설정 변경이 포함됩니다.

아래 그림에서는 탄력적 풀 예제를 보여 줍니다. 보기에는 다음이 포함됩니다.

*  탄력적 풀과 풀에 포함된 데이터베이스 모두의 리소스 사용을 모니터링하는 차트
*  탄력적 풀을 변경하는 **구성** 풀 단추
*  새 데이터베이스를 만들고 현재 탄력적 풀에 추가하는 **데이터베이스 만들기** 단추
*  목록의 모든 데이터베이스에 대해 Transact SQL 스크립트를 실행하여 많은 데이터베이스를 관리하는 데 도움이 되는 탄력적 작업

![풀 보기][2]

이 문서의 단계를 진행하려면 하나 이상의 탄력적 풀 및 데이터베이스가 있는 Azure의 SQL server가 필요합니다. 탄력적 풀이 없으면 [풀 만들기](sql-database-elastic-pool-create-portal.md)를 참조하고 데이터베이스가 없으면 [SQL 데이터베이스 자습서](sql-database-get-started.md)를 참조하세요.

## <a name="elastic-pool-monitoring"></a>탄력적 풀 모니터링

특정 풀로 이동하여 해당 리소스 사용률을 확인할 수 있습니다. 기본적으로 풀은 지난 1시간 동안 저장소 및 eDTU 사용량을 표시하도록 구성됩니다. 다양한 시간 창에 다양한 메트릭이 표시되도록 차트를 구성할 수 있습니다.

1. 작업하려는 풀을 선택합니다.
2. **탄력적 풀 모니터링**에는 차트 레이블이 지정된 **리소스 사용률**이 있습니다. 차트를 클릭합니다.

    ![탄력적 풀 모니터링][3]

    **메트릭** 블레이드가 열리면 지정된 시간 창에 지정된 메트릭의 자세히 보기를 보여 줍니다.   

    ![메트릭 블레이드][9]

### <a name="to-customize-the-chart-display"></a>차트 표시를 사용자 지정하려면

차트 및 메트릭 블레이드를 편집하여 CPU 비율, 데이터 IO 비율, 사용되는 로그 IO 백분율 등 다른 메트릭을 표시할 수 있습니다.

2. 메트릭 블레이드에서 **편집**을 클릭합니다.

    ![편집 클릭][6]

- **차트 편집** 블레이드에서 새 시간 범위(지난 시간, 오늘 또는 지난 주)를 선택하거나 **사용자 지정**을 클릭하여 지난 두 주 동안의 데이터 범위를 선택합니다. 차트 종류(가로 막대형 또는 꺾은선형)를 선택하고 모니터링할 리소스를 선택합니다.

> [!Note]
> 측정 단위가 동일한 메트릭만 차트에 동시에 표시될 수 있습니다. 예를 들어, “eDTU 백분율”을 선택하면 측정 단위로 다른 백분율 매트릭만 선택할 수 있게 됩니다.
>
>

    ![Click edit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- 그런 후 **OK**를 클릭합니다.


## <a name="elastic-database-monitoring"></a>탄력적 데이터베이스 모니터링

문제 발생 가능성에 대한 개별 데이터베이스를 모니터링할 수도 있습니다.

1. **탄력적 데이터베이스 모니터링**에는 다섯 개의 데이터베이스에 대한 메트릭을 표시하는 차트가 있습니다. 기본적으로 차트는 지난 한 시간 동안 평균 eDTU별로 풀의 상위 5개 데이터베이스를 표시합니다. 차트를 클릭합니다.

    ![탄력적 풀 모니터링][4]

2. **데이터베이스 리소스 사용률** 블레이드가 나타납니다. 풀에서 데이터베이스 사용량의 세부 정보 보기를 제공합니다. 블레이드의 아래쪽에 있는 표를 사용하여 차트(최대 5개의 데이터베이스)에서 사용량을 표시하도록 풀에서 모든 데이터베이스를 선택할 수 있습니다. **차트 편집**을 클릭하여 차트에 표시되는 메트릭 및 시간 창을 사용자 지정할 수도 있습니다.

    ![데이터베이스 리소스 사용률 블레이드][8]

### <a name="to-customize-the-view"></a>보기를 사용자 지정하려면

1. **데이터베이스 리소스 사용률** 블레이드에서 **차트 편집**을 클릭합니다.

    ![차트 편집 클릭](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. **차트** 편집 블레이드에서 새 시간 범위(지난 시간 또는 지난 24시간)를 선택하거나 **사용자 지정**을 클릭하여 지난 2주 동안에서 표시할 다른 날을 선택합니다.

    ![사용자 지정 클릭](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. **데이터베이스 비교 기준** 드롭다운을 클릭하여 데이터베이스를 비교할 경우 사용할 다른 메트릭을 선택합니다.

    ![차트 편집](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>모니터링할 데이터베이스를 선택하려면

데이터베이스 목록의 **데이터베이스 리소스 사용률** 블레이드에서 목록에 있는 페이지를 탐색하거나 데이터베이스의 이름을 입력하여 특정 데이터베이스를 찾을 수 있습니다. 확인란을 사용하여 데이터베이스를 선택합니다.

![모니터링할 데이터베이스 검색][7]


## <a name="add-an-alert-to-a-pool-resource"></a>리소스 풀에 경고 추가

리소스가 설정한 사용률 임계값에 도달하면 사람 또는 URL 끝점에 대한 경고 문자열에 전자 메일을 보내는 리소스에 대한 규칙을 추가할 수 있습니다.

> [!IMPORTANT]
> 탄력적 풀에 대한 리소스 사용률 모니터링은 20분 이상 지연됩니다. 탄력적 풀에 대한 경고를 30분 미만으로 설정하는 것은 현재 지원되지 않습니다. 탄력적 풀에 대해 30분 미만의 기간으로 설정한 경고(PowerShell API에서 "-WindowSize"라는 매개 변수 사용)는 트리거되지 않을 수 있습니다. 탄력적 풀에 대해 정의하는 모든 경고는 30분 이상의 기간(WindowSize)을 사용해야 합니다.
>
>

**리소스에 경고 추가:**

1. **리소스 사용률** 차트를 클릭하여 **메트릭** 블레이드를 열고 **경고 추가**를 클릭한 다음 **경고 규칙 추가** 블레이드에 정보를 입력합니다(**리소스**는 자동으로 작업 중인 풀로 설정됨).
2. 사용자 및 받는 사람에 대한 경고를 식별하는 **이름** 및 **설명**을 입력합니다.
3. 목록에서 경고하려는 **메트릭** 을 선택합니다.

    차트는 동적으로 임계값을 선택할 수 있도록 해당 메트릭에 대한 리소스 사용률을 보여줍니다.

4. **조건**(보다 큼, 보다 작음 등) 및 **임계값**을 선택합니다.
5. **확인**을 클릭합니다.



## <a name="move-a-database-into-an-elastic-pool"></a>탄력적 풀로 데이터베이스 이동

기존 풀에서 데이터베이스를 제거하거나 추가할 수 있습니다. 데이터베이스가 다른 풀에 있을 수 있습니다. 그러나 동일한 논리 서버에 있는 데이터베이스만 추가할 수 있습니다.

1. 풀에 대한 블레이드에서, **Elastic Database** 아래에서 **풀 구성**을 클릭합니다.

    ![풀 구성 클릭][1]

2. **풀 구성** 블레이드에서 **풀에 추가**를 클릭합니다.

    ![풀에 추가 클릭](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. **데이터베이스 추가** 블레이드에서 풀에 추가할 데이터베이스를 하나 이상 선택합니다. 그런 다음 **선택**을 클릭합니다.

    ![추가할 데이터베이스 선택](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    이제 **풀 구성** 블레이드에 추가하려고 선택한 데이터베이스가 **보류 중**으로 상태가 설정되어 나열됩니다.

    ![보류 중인 풀 추가](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. **풀 구성 블레이드**에서 **저장**을 클릭합니다.

    ![저장을 클릭합니다.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>탄력적 풀 외부로 데이터베이스 이동

1. **풀 구성** 블레이드에서 제거할 데이터베이스를 하나 이상 선택합니다.

    ![데이터베이스 나열](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. **풀에서 제거**를 클릭합니다.

    ![데이터베이스 나열](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    이제 **풀 구성** 블레이드에 제거하려고 선택한 데이터베이스가 **보류 중**으로 상태가 설정되어 나열됩니다.

    ![데이터베이스 추가 및 제거 미리 보기](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. **풀 구성 블레이드**에서 **저장**을 클릭합니다.

    ![저장을 클릭합니다.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-a-pool"></a>풀의 성능 설정 변경

풀의 리소스 사용률을 모니터링하면서 일부 조정이 필요함을 알 수 있습니다. 어쩌면 풀에서 성능이나 저장소 제한을 변경해야 합니다. 풀에서 데이터베이스 설정을 변경하려고 할 수도 있습니다. 언제든지 풀의 설치 프로그램을 변경하여 적절한 성능 및 비용을 얻을 수 있습니다. 자세한 내용은 [탄력적 데이터베이스 풀을 사용해야 하는 경우](sql-database-elastic-pool-guidance.md) 를 참조하세요.

**풀당 eDTU 또는 저장소 제한 및 데이터베이스당 eDTU를 변경하려면:**

1. **풀 구성** 블레이드를 엽니다.

    **탄력적 데이터베이스 풀 설정**에서 슬라이더를 사용하여 풀 설정을 변경합니다.

    ![탄력적 풀 리소스 사용률](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 설정이 변경되면 디스플레이에 변경의 예상된 월별 비용이 표시됩니다.

    ![풀 및 새 월별 비용 업데이트](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## <a name="create-and-manage-elastic-jobs"></a>탄력적 작업 만들기 및 관리

탄력적인 작업을 통해 개수에 관계없이 풀에 있는 데이터베이스에 대해 Transact-SQL 스크립트를 실행할 수 있습니다. 포털을 사용하여 새 작업을 만들거나 기존 작업을 관리할 수 있습니다.

![탄력적 작업 만들기 및 관리][5]


작업을 사용하기 전에 탄력적 작업 구성 요소를 설치하고 자격 증명을 제공합니다. 자세한 내용은 [탄력적 데이터베이스 작업 개요](sql-database-elastic-jobs-overview.md)를 참조하세요.

[Azure SQL 데이터베이스를 사용하여 규모 확장](sql-database-elastic-scale-introduction.md)참조: 탄력적 데이터베이스 도구를 사용하여 규모를 확장하거나 데이터를 이동하거나 쿼리 또는 트랜잭션을 만듭니다.



## <a name="additional-resources"></a>추가 리소스

- [SQL 데이터베이스 탄력적 풀](sql-database-elastic-pool.md)
- [포털을 사용한 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-csharp.md)
- [PowerShell을 사용한 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-powershell.md)
- [C#을 사용한 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-csharp.md)
- [탄력적 데이터베이스 풀에 대한 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png



<!--HONumber=Nov16_HO3-->


