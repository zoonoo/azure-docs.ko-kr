---
title: "Azure Portal: SQL Database 탄력적 풀 만들기 및 관리 | Microsoft Docs"
description: "Azure Portal 및 SQL Database의 기본 제공 인텔리전스를 사용하여 데이터베이스 성능을 최적화하고 비용을 관리하기 위해 확장성 있는 탄력적 풀을 관리하고, 모니터링하고, 적정 크기로 조정하는 방법에 대해 알아봅니다."
keywords: 
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: cgronlun
ms.assetid: 3dc9b7a3-4b10-423a-8e44-9174aca5cf3d
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 11/17/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 637171b775d01e16cec1a7e9ef6fad73875eac69
ms.openlocfilehash: 40f04d92acce3096baec251824cb23a70b52ff83


---
# <a name="create-and-manage-an-elastic-pool-with-the-azure-portal"></a>Azure Portal을 사용하여 탄력적 풀 만들기 및 관리
이 항목에서는 [Azure Portal](https://portal.azure.com/)을 사용하여 확장 가능한 [탄력적 풀](sql-database-elastic-pool.md)을 만들고 관리하는 방법을 보여 줍니다. [PowerShell](sql-database-elastic-pool-manage-powershell.md), REST API 또는 [C#][C#을 사용하여 탄력적 풀 만들기 및 관리](sql-database-elastic-pool-manage-csharp.md)를 사용하여 Azure 탄력적 풀을 만들고 관리할 수도 있습니다. [Transact-SQL](sql-database-elastic-pool-manage-tsql.md)을 사용하여 탄력적 풀을 만들고 여기에 데이터베이스를 넣거나 뺄 수도 있습니다.

## <a name="create-an-elastic-pool"></a>탄력적 풀 만들기 

두 가지 방법으로 탄력적 풀을 만들 수 있습니다. 원하는 풀 설정을 알고 있는 경우 처음부터 수행하거나 서비스에서 권장 내용으로 시작할 수 있습니다. SQL Database에는 데이터베이스에 대한 과거 사용량 원격 분석을 기반으로 보다 비용 효율적인 경우 탄력적 풀 설정을 권장하는 기본 제공 인텔리전스가 있습니다.

서버에 풀을 여러 개 만들 수 있지만 다른 서버에 속하는 데이터베이스를 동일한 풀에 추가할 수 없습니다. 

> [!NOTE]
> 탄력적 풀은 현재 미리 보기 상태인 인도 서부를 제외한 모든 Azure 지역에서 일반 공급(GA) 상태입니다.  이 영역에서 탄력적 풀의 GA는 가능한 한 빨리 수행될 예정입니다.
>
>

### <a name="step-1-create-an-elastic-pool"></a>1단계: 탄력적 풀 만들기

포털의 기존 **서버** 블레이드에서 탄력적 풀을 만드는 것은 기존 데이터베이스를 탄력적 풀로 이동하는 가장 쉬운 방법입니다.

> [!NOTE]
> **Marketplace**에서 **SQL 탄력적 풀**을 검색하거나 **SQL 탄력적 풀** 찾아보기 블레이드에서 **+추가**를 클릭하여 탄력적 풀을 만들 수도 있습니다. 이 풀 프로비저닝 워크플로를 통해 새 서버 또는 기존 서버를 지정할 수 있습니다.
>
>

1. [Azure Portal](http://portal.azure.com/)에서 **더 많은 서비스** **>** **SQL Server**를 클릭한 다음 탄력적 풀에 추가할 데이터베이스를 포함하는 서버를 클릭합니다.
2. **새 풀**을 클릭합니다.

    ![서버에 풀 추가](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **또는**

    서버에 대해 권장되는 탄력적 풀이 있다는 메시지를 볼 수도 있습니다(V12에만 해당). 메시지를 클릭하여 기록 데이터베이스 사용량 원격 분석에 따라 권장된 풀을 확인한 다음 계층을 클릭하여 자세한 내용을 보고 풀을 사용자 지정합니다. 권장 사항을 만드는 방법은 이 항목의 뒷부분에서 [풀 권장 사항 이해](#understand-pool-recommendations) 를 참조하세요.

    ![권장되는 풀](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

3. 풀에 대한 설정을 지정하는 **탄력적 풀** 블레이드가 나타납니다. 이전 단계에서 **새 풀**을 클릭한 경우 가격 책정 계층은 기본적으로 **표준**이며 선택한 데이터베이스가 없습니다. 빈 풀을 만들거나 해당 서버에서 풀로 이동할 기존 데이터베이스의 집합을 지정할 수 있습니다. 권장되는 풀을 만드는 경우 권장되는 가격 책정 계층, 성능 설정 및 데이터베이스 목록이 채워지지만 변경할 수 있습니다.

    ![탄력적 풀 구성](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

4. 탄력적 풀의 이름을 지정하거나 기본값을 그대로 둡니다.

### <a name="step-2-choose-a-pricing-tier"></a>2단계: 가격 책정 계층 선택

풀의 가격 책정 계층에 따라 풀에 있는 탄력적 데이터베이스에서 사용 가능한 기능과 각 데이터베이스에서 사용 가능한 최대 eDTU 수(eDTU MAX) 및 저장소(GB)가 결정됩니다. 자세한 내용은 서비스 계층을 참조하세요.

풀에 대한 가격 책정 계층을 변경하려면 **가격 책정 계층**, 원하는 가격 책정 계층, **선택**을 차례로 클릭합니다.

> [!IMPORTANT]
> 가격 책정 계층을 선택하고 마지막 단계에서 **확인** 을 클릭하여 변경 내용을 적용한 후에는 풀의 가격 책정 계층을 변경할 수 없습니다. 기존 탄력적 풀의 가격 책정 계층을 변경하려면 원하는 가격 책정 계층에서 탄력적 풀을 만들고 데이터베이스를 이 새로운 풀로 마이그레이션합니다.
>
>

![가격 책정 계층 선택](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

### <a name="step-3-configure-the-pool"></a>3단계: 풀 구성

가격 책정 계층을 설정한 후에, 데이터베이스를 추가할 풀의 구성을 클릭하고, 풀 eDTU 및 저장소(풀 GB), 그리고 풀의 탄력적 데이터베이스에 대한 최대 및 최소 eDTU를 설정할 위치를 설정합니다.

1. **풀 구성**
2. 풀에 추가하려는 데이터베이스를 선택합니다. 이 단계는 풀을 만드는 과정의 선택 사항입니다. 데이터베이스는 풀이 생성된 후에 추가될 수 있습니다.
    데이터베이스를 추가하려면 **데이터베이스 추가**, 추가하려는 데이터베이스, **선택** 단추를 차례로 클릭합니다.

    ![데이터베이스 추가](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    사용하고 있는 데이터베이스에 충분한 기록 사용량 원격 분석이 있는 경우 **예상되는 eDTU 및 GB 사용량** 그래프 및 **실제 eDTU 사용량** 막대형 차트는 구성을 결정할 수 있도록 업데이트됩니다. 또한 서비스가 적정 크기의 풀을 만들도록 권장 사항 메시지를 제공할 수 있습니다. [동적 권장 사항](#dynamic-recommendations)을 참조하세요.

3. **풀 구성** 페이지에서 제어를 사용하여 설정을 탐색하고 풀을 구성합니다. 각 서비스 계층의 제한에 대한 자세한 내용은 [탄력적 풀 제한](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)을 참조하고 적정한 탄력적 풀의 크기에 대한 자세한 지침은 [탄력적 풀에 대한 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요. 풀 설정에 대한 자세한 내용은 [탄력적 풀 속성](sql-database-elastic-pool.md#elastic-pool-properties)을 참조하세요.

    ![탄력적 풀 구성](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. **선택** in the **Configure Pool** 을 클릭합니다.
5. **확인** 을 클릭하여 풀을 만듭니다.


## <a name="understand-elastic-pool-recommendations"></a>탄력적 풀 권장 사항 이해

SQL 데이터베이스 서비스는 사용 기록을 평가하고 단일 데이터베이스를 사용하는 경우보다 비용 효율적인 경우 하나 이상의 풀을 권장합니다. 각 권장 사항은 풀에 가장 적합한 서버 데이터베이스의 고유한 하위 집합으로 구성됩니다.

![권장되는 풀](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

풀 권장 사항은 다음으로 구성됩니다.

- 풀에 대한 가격 책정 계층(Basic, Standard 또는 Premium)
- 적절한 **풀 eDTU** (풀당 최대 eDTU)
- 데이터베이스당 **eDTU 최대** 및 **eDTU 최소**
- 풀에 대한 권장 데이터베이스 목록

> ![중요] 서비스는 풀을 권장할 때 최근 30일간의 원격 분석을 고려합니다. 데이터베이스가 탄력적 풀의 후보로 간주되려면 7일 이상 존재해야 합니다. 이미 탄력적 풀에 있는 데이터베이스는 탄력적 풀 권장 사항에 대한 후보로 간주되지 않습니다.
>

서비스는 리소스 요구와 각 서비스 계층에 있는 단일 데이터베이스를 동일한 계층의 풀로 이동하는 경우 비용 효율성을 평가합니다. 예를 들어 서버의 모든 Standard 데이터베이스는 표준 탄력적 풀에 적합한지 평가됩니다. 즉, 서비스는 Standard 데이터베이스를 Premium 풀로 이동하는 경우와 같은 계층 간 권장 사항을 제공하지 않습니다.

풀에 데이터베이스를 추가한 후에는, 사용자가 선택한 데이터베이스의 기존 사용 정보를 기반으로 권장 사항이 동적으로 생성됩니다. 권장 사항은 eDTU 및 GB 사용 현황 차트는 물론 **풀 구성** 블레이드 상단의 권장 사항 배너에도 표시됩니다. 권장 사항은 특정 데이터베이스에 최적화된 탄력적 풀을 만드는 데 도움을 주기 위해 제공됩니다.

![동적 권장 사항](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## <a name="manage-and-monitor-an-elastic-pool"></a>탄력적 풀 관리 및 모니터링

Azure Portal을 사용하여 풀에서 탄력적 풀 및 데이터베이스를 모니터링하고 관리할 수 있습니다. 포털에서 탄력적 풀 및 해당 풀 내의 데이터베이스의 사용률을 모니터링할 수 있습니다. 탄력적 풀에 일련의 내용을 변경하는 동시에 모든 변경 내용을 전송할 수도 있습니다. 이러한 변경 내용에는 데이터베이스 추가 또는 제거, 탄력적 풀 설정 변경, 데이터베이스 설정 변경이 포함됩니다.

다음 그림에서는 탄력적 풀 예제를 보여 줍니다. 보기에는 다음이 포함됩니다.

*  탄력적 풀과 풀에 포함된 데이터베이스 모두의 리소스 사용을 모니터링하는 차트
*  탄력적 풀을 변경하는 **구성** 풀 단추
*  데이터베이스를 만들고 현재 탄력적 풀에 추가하는 **데이터베이스 만들기** 단추
*  목록의 모든 데이터베이스에 대해 Transact SQL 스크립트를 실행하여 많은 데이터베이스를 관리하는 데 도움이 되는 탄력적 작업

![풀 보기][2]

특정 풀로 이동하여 해당 리소스 사용률을 확인할 수 있습니다. 기본적으로 풀은 지난&1;시간 동안 저장소 및 eDTU 사용량을 표시하도록 구성됩니다. 다양한 시간 창에 다양한 메트릭이 표시되도록 차트를 구성할 수 있습니다.

1. 사용할 탄력적 풀을 선택합니다.
2. **탄력적 풀 모니터링**에는 차트 레이블이 지정된 **리소스 사용률**이 있습니다. 차트를 클릭합니다.

    ![탄력적 풀 모니터링][3]

    **메트릭** 블레이드가 열리고 지정된 시간 창에 지정된 메트릭의 자세히 보기를 보여 줍니다.   

    ![메트릭 블레이드][9]

### <a name="to-customize-the-chart-display"></a>차트 표시를 사용자 지정하려면

차트 및 메트릭 블레이드를 편집하여 CPU 비율, 데이터 IO 비율, 사용되는 로그 IO 백분율 등 다른 메트릭을 표시할 수 있습니다.

1. 메트릭 블레이드에서 **편집**을 클릭합니다.

    ![편집 클릭][6]

2. **차트 편집** 블레이드에서 시간 범위(지난 시간, 오늘 또는 지난 주)를 선택하거나 **사용자 지정**을 클릭하여 지난 두 주 동안의 데이터 범위를 선택합니다. 차트 종류(가로 막대형 또는 꺾은선형)를 선택하고 모니터링할 리소스를 선택합니다.

   > [!Note]
   > 측정 단위가 동일한 메트릭만 차트에 동시에 표시될 수 있습니다. 예를 들어, “eDTU 백분율”을 선택하면 측정 단위로 다른 백분율 메트릭만 선택할 수 있습니다.
   >

    ![편집 클릭](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

    

3. 그런 후 **OK**를 클릭합니다.

## <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>탄력적 풀의 데이터베이스 관리 및 모니터링

문제 발생 가능성에 대한 개별 데이터베이스를 모니터링할 수도 있습니다.

1. **탄력적 데이터베이스 모니터링**에는 다섯 개의 데이터베이스에 대한 메트릭을 표시하는 차트가 있습니다. 기본적으로 차트는 지난 한 시간 동안 평균 eDTU별로 풀의 상위 5개 데이터베이스를 표시합니다. 차트를 클릭합니다.

    ![탄력적 풀 모니터링][4]

2. **데이터베이스 리소스 사용률** 블레이드가 나타납니다. 풀에서 데이터베이스 사용량의 세부 정보 보기를 제공합니다. 블레이드의 아래쪽에 있는 표를 사용하여 차트(최대 5개의 데이터베이스)에서 사용량을 표시하도록 풀에서 모든 데이터베이스를 선택할 수 있습니다. **차트 편집**을 클릭하여 차트에 표시되는 메트릭 및 시간 창을 사용자 지정할 수도 있습니다.

    ![데이터베이스 리소스 사용률 블레이드][8]

### <a name="to-customize-the-view"></a>보기를 사용자 지정하려면

1. **데이터베이스 리소스 사용률** 블레이드에서 **차트 편집**을 클릭합니다.

    ![차트 편집 클릭](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. **차트** 편집 블레이드에서 시간 범위(지난 시간 또는 지난 24시간)를 선택하거나 **사용자 지정**을 클릭하여 지난 2주 동안에서 표시할 다른 날을 선택합니다.

    ![사용자 지정 클릭](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. **데이터베이스 비교 기준** 드롭다운을 클릭하여 데이터베이스를 비교할 경우 사용할 다른 메트릭을 선택합니다.

    ![차트 편집](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>모니터링할 데이터베이스를 선택하려면

데이터베이스 목록의 **데이터베이스 리소스 사용률** 블레이드에서 목록에 있는 페이지를 탐색하거나 데이터베이스의 이름을 입력하여 특정 데이터베이스를 찾을 수 있습니다. 확인란을 사용하여 데이터베이스를 선택합니다.

![모니터링할 데이터베이스 검색][7]


## <a name="add-an-alert-to-an-elastic-pool-resource"></a>탄력적 풀 리소스에 경고 추가

탄력적 풀에 규칙을 추가하여 탄력적 풀이 설정한 사용률 임계값에 도달할 경우 사용자에게 전자 메일을 보내거나 URL 끝점에 경고 문자열을 보낼 수 있습니다.

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

## <a name="change-performance-settings-of-an-elastic-pool"></a>탄력적 풀의 성능 설정 변경

탄력적 풀의 리소스 사용률을 모니터링하면서 일부 조정이 필요함을 알 수 있습니다. 어쩌면 풀에서 성능이나 저장소 제한을 변경해야 합니다. 풀에서 데이터베이스 설정을 변경하려고 할 수도 있습니다. 언제든지 풀의 설치 프로그램을 변경하여 적절한 성능 및 비용을 얻을 수 있습니다. 자세한 내용은 [탄력적 풀을 사용해야 하는 경우](sql-database-elastic-pool-guidance.md)를 참조하세요.

풀당 eDTU 또는 저장소 제한 및 데이터베이스당 eDTU를 변경하려면:

1. **풀 구성** 블레이드를 엽니다.

    **탄력적 풀 설정**에서 슬라이더를 사용하여 풀 설정을 변경합니다.

    ![탄력적 풀 리소스 사용률](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 설정이 변경되면 디스플레이에 변경의 예상된 월별 비용이 표시됩니다.

    ![탄력적 풀 및 새 월별 비용 업데이트](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## <a name="latency-of-elastic-pool-operations"></a>탄력적 풀 작업의 대기 시간
* 데이터베이스당 최소 eDTU 또는 데이터베이스당 최대 eDTU를 변경하는 작업은 일반적으로 5분 이내에 완료됩니다.
* 풀당 eDTU를 변경하는 작업은 풀에 있는 모든 데이터베이스에서 사용한 공간의 전체 크기에 따라 달라집니다. 변경 시간은 100GB당 평균 90분 이하입니다. 예를 들어 풀에 있는 모든 데이터베이스에서 사용 중인 총 공간이 200GB일 경우, 풀당 풀 eDTU를 변경하는 예상 대기 시간은 3시간 이하입니다.

## <a name="next-steps"></a>다음 단계

- 탄력적 풀을 이해하려면 [SQL Database 탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.
- 탄력적 풀 사용에 대한 지침을 보려면 [탄력적 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요.
- 탄력적 작업을 사용하여 개수에 관계없이 풀에 있는 데이터베이스에 대해 Transact-SQL 스크립트를 실행하려면 [탄력적 작업 개요](sql-database-elastic-jobs-overview.md)를 참조하세요.
- 개수에 관계없이 풀에 있는 데이터베이스를 쿼리하려면 [탄력적 쿼리 개요](sql-database-elastic-query-overview.md)를 참조하세요.
- 개수에 관계없는 풀의 데이터베이스 트랜잭션의 경우 [탄력적 트랜잭션](sql-database-elastic-transactions-overview.md)을 참조하세요.


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



<!--HONumber=Feb17_HO2-->


