---
title: "Log Analytics의 Azure SQL Analytics 솔루션 | Microsoft Docs"
description: "Azure SQL Analytics 솔루션을 통해 Azure SQL Database를 관리할 수 있습니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 0184e95ca56e4bc4ffbe860da2b7a5cae9b5a043
ms.lasthandoff: 03/11/2017


---


# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Log Analytics에 Azure SQL Analytics(미리 보기)를 사용하여 Azure SQL Database 모니터링

Azure Log Analytics의 Azure SQL 모니터링 솔루션은 중요한 SQL Azure 성능 메트릭을 수집하여 시각화합니다. 솔루션으로 수집한 메트릭을 사용하여 사용자 지정 모니터링 규칙 및 경고를 만들 수 있습니다. 그리고 여러 Azure 구독 및 탄력적 풀 간에 Azure SQL Database 및 탄력적 풀 메트릭을 모니터링하고 이를 시각화할 수 있습니다. 또한 솔루션은 응용 프로그램 스택의 각 계층에서 문제를 식별할 수도 있습니다.  [Azure 진단 메트릭](log-analytics-azure-storage.md)과 Log Analytics 뷰를 함께 사용하여 모든 Azure SQL Databases 및 탄력적 풀에 대한 데이터를 단일 Log Analytics 작업 영역에 표시합니다.

현재, 이 미리 보기 솔루션은 작업 영역당 최대 150,000개의 Azure SQL Database 및 5,000개의 SQL 탄력적 풀을 지원합니다.

Log Analytics에 대해 제공되는 다른 도구처럼 Azure SQL 모니터링 솔루션은 Azure 리소스의 상태에 대한 알림(이 경우, Azure SQL Database)을 모니터링하고 수신할 수 있습니다. Microsoft Azure SQL Database는 Azure 클라우드에서 실행되는 응용 프로그램에 친숙한 SQL Server와 유사한 기능을 제공하는 확장성 있는 관계형 데이터베이스 서비스입니다. Log Analytics를 통해 구조적 및 비구조적 데이터를 수집하고, 상관 관계를 지정하며 시각화할 수 있습니다.

## <a name="connected-sources"></a>연결된 소스

Azure SQL 모니터링 솔루션은 Log Analytics 서비스에 연결하는 데 에이전트를 사용하지 않습니다.

다음 표는 이 솔루션이 지원하는 연결된 소스를 설명합니다.

| 연결된 소스 | 지원 | 설명 |
| --- | --- | --- |
| [Windows 에이전트](log-analytics-windows-agents.md) | 아니요 | 직접 Windows 에이전트는 솔루션에 사용되지 않습니다. |
| [Linux 에이전트](log-analytics-linux-agents.md) | 아니요 | 직접 Linux 에이전트는 솔루션에 사용되지 않습니다. |
| [SCOM 관리 그룹](log-analytics-om-agents.md) | 아니요 | SCOM 에이전트에서 Log Analytics로 직접 연결은 솔루션에 사용되지 않습니다. |
| [Azure 저장소 계정](log-analytics-azure-storage.md) | 아니요 | Log Analytics는 저장소 계정의 데이터를 읽지 않습니다. |
| [Azure 진단](log-analytics-azure-storage.md) | 예 | Azure 메트릭 데이터는 Azure에 의해 직접 Log Analytics에 전송됩니다. |

## <a name="prerequisites"></a>필수 조건

1. Azure 구독. 없는 경우 [무료](https://azure.microsoft.com/free/) 구독을 하나 만들 수 있습니다.
2. Log Analytics 작업 영역. 기존 항목을 사용하거나 이 솔루션 사용을 시작하기 전에 [새로 만들 수 있습니다.](log-analytics-get-started.md)
3. Azure SQL Database 및 탄력적 풀에 대한 Azure 진단을 사용하도록 설정하고 [Log Analytics에 데이터를 보내도록 구성](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)합니다.

## <a name="configuration"></a>구성

Azure SQL 모니터링 솔루션을 작업 영역에 추가하려면 다음 단계를 수행합니다.

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview)에서 또는 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명하는 프로세스를 사용하여 작업 영역에 Azure SQL 분석 솔루션을 추가합니다.
2. Azure Portal에서 **새로 만들기**(+ 기호)를 클릭한 후 리소스 목록에서 **모니터링 + 관리**를 선택합니다.  
    ![모니터링 + 관리](./media/log-analytics-azure-sql/monitoring-management.png)
3. **모니터링 + 관리** 목록에서 **모두 표시**를 클릭합니다.
4. **권장** 목록에서 **자세히**를 클릭한 후 새 목록에서 **Azure SQL Analytics(미리 보기)**를 찾아 선택합니다.  
    ![Azure SQL Analytics 솔루션](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. **Azure SQL Analytics(미리 보기)** 블레이드에서 **만들기**를 클릭합니다.  
    ![만들기](./media/log-analytics-azure-sql/portal-create.png)
6. **새 솔루션 만들기** 블레이드에서 솔루션을 추가할 작업 영역을 선택한 후 **만들기**를 클릭합니다.  
    ![작업 영역에 추가](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Azure 구독을 여러 개 구성하려면

여러 구독을 지원하려면 [PowerShell을 사용하여 Azure 리소스 메트릭 로깅 사용](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)에서 PowerShell 스크립트를 사용합니다. 하나의 Azure 구독에서 다른 Azure 구독의 작업 영역으로 진단 데이터를 전송하는 스크립트를 실행할 때 작업 영역 리소스 ID를 매개 변수로 제공합니다.

**예제**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>솔루션 사용

솔루션을 작업 영역에 추가하면 Azure SQL Analytics 타일이 작업 영역에 추가되고 개요에 표시됩니다. 타일은 솔루션이 연결된 Azure SQL 데이터베이스 및 Azure SQL 탄력적 풀 수를 보여 줍니다.

![Azure SQL Analytics 타일](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-monitoring-data"></a>Azure SQL 모니터링 데이터 보기

**Azure SQL 모니터링** 타일을 클릭하여 Azure SQL Analytics 대시보드를 엽니다. 대시보드는 다음 표의 열을 포함하고 있습니다. 각 열은 지정된 범위 및 시간 범위에 대한 열의 기준과 일치하는 최대 열 개의 항목을 나열합니다. 열 아래쪽의 **모두 보기**를 클릭하거나 열 헤더를 클릭하여 모든 레코드를 반환하는 로그 검색을 실행할 수 있습니다.

[서비스 계층에 대한 SQL Database 옵션 및 성능](../sql-database/sql-database-service-tiers.md)에 대해 자세히 읽어보세요.



![Azure SQL Analytics 대시보드](./media/log-analytics-azure-sql/azure-sql-dash-01.png)



![Azure SQL Analytics 대시보드](./media/log-analytics-azure-sql/azure-sql-dash-02.png)

| 열 | 설명 |
| --- | --- |
| **Azure SQL Database Analytics** | &nbsp; |
| DTU 사용률 &gt; 90%인 상위 N개 데이터베이스 | 이 패널에는 선택한 시간 동안 DTU 사용률이 90%를 초과하는 Azure SQL Database 수가 표시됩니다. 맨 위 타일에는 Log Analytics 내에서 모니터링 중인 모든 데이터베이스의 동일한 지정된 시간 중에 할당된 총 DTU 가용성 중 90% 이상을 사용한 데이터베이스 수가 표시됩니다.  작업 영역에서 모니터링하는 다른 모든 항목과 데이터베이스의 DTU 사용률을 비교하는 꺾은선형 차트를 보여 주는 로그 검색을 실행하려면 데이터베이스 이름을 클릭합니다. |
| CPU 사용률 &gt; 90%인 상위 N개 데이터베이스 | 이 패널에는 선택한 시간 동안 CPU 사용률이 90%를 초과하는 Azure SQL Database 수가 표시됩니다. 맨 위 타일에는 Log Analytics 내에서 모니터링 중인 모든 데이터베이스의 동일한 지정된 시간 중에 할당된 총 CPU 가용성 중 90% 이상을 사용한 데이터베이스 수가 표시됩니다.  작업 영역에서 모니터링하는 다른 모든 항목과 데이터베이스의 CPU 사용률을 비교하는 꺾은선형 차트를 보여 주는 로그 검색을 실행하려면 데이터베이스 이름을 클릭합니다. |
| 저장소 사용량 &gt; 90%인 상위 N개 데이터베이스 | 이 패널에는 선택한 시간에 대한 저장소 할당 중 90% 이상을 사용한 Azure SQL Database 수가 표시됩니다. 맨 위 타일에는 Log Analytics 내에서 모니터링 중인 모든 데이터베이스의 동일한 지정된 시간 중에 임계값을 90% 위반한 데이터베이스 수가 표시됩니다.  작업 영역에서 모니터링하는 다른 모든 항목과 데이터베이스 저장소 사용률을 비교하는 꺾은선형 차트를 보여 주는 로그 검색을 실행하려면 데이터베이스 이름을 클릭합니다. |
| **Azure SQL 탄력적 풀** | &nbsp; |
| DTU &gt; 90%인 상위 N개 탄력적 풀 | 이 패널에는 선택한 시간에 대한 총 DTU 할당 중 90% 이상을 사용한 Azure SQL 탄력적 풀 수가 표시됩니다. 맨 위 타일에는 Log Analytics 내에서 모니터링 중인 모든 Azure SQL 탄력적 풀의 동일한 지정된 시간 중에 임계값을 90% 위반한 탄력적 풀 수가 표시됩니다.  작업 영역에서 모니터링하는 다른 모든 항목과 탄력적 풀 저장소 사용률을 비교하는 꺾은선형 차트를 보여 주는 로그 검색을 실행하려면 탄력적 풀 이름을 클릭합니다. |
| CPU &gt; 90%인 상위 N개 탄력적 풀 | 이 패널에는 선택한 시간 동안 CPU 사용률이 90%를 초과하는 Azure SQL 탄력적 풀 수가 표시됩니다. 맨 위 타일에는 Log Analytics 내에서 모니터링 중인 모든 Azure SQL 탄력적 풀의 동일한 지정된 시간 중에 임계값을 90% 위반한 탄력적 풀 수가 표시됩니다.  작업 영역에서 모니터링하는 다른 모든 항목과 탄력적 풀 CPU 사용률을 비교하는 꺾은선형 차트를 보여 주는 로그 검색을 실행하려면 탄력적 풀 이름을 클릭합니다. |
| 저장소 사용량 &gt; 90%인 상위 N개 탄력적 풀 | 이 패널에는 선택한 시간에 대한 저장소 할당 중 90% 이상을 사용한 Azure SQL 탄력적 풀 수가 표시됩니다. 맨 위 타일에는 Log Analytics 내에서 모니터링 중인 모든 탄력적 풀의 동일한 지정된 시간 중에 임계값을 90% 위반한 탄력적 풀 수가 표시됩니다.  작업 영역에서 모니터링하는 다른 모든 항목과 탄력적 풀 저장소 사용률을 비교하는 꺾은선형 차트를 보여 주는 로그 검색을 실행하려면 탄력적 풀 이름을 클릭합니다. |
| **Azure SQL 활동 로그** | &nbsp; |
| SQL Azure 활동 감사 | 이 패널에는 선택한 시간 동안 Azure SQL과 관련된 Azure 활동 레코드 수가 표시됩니다. 항목에 대한 추가 세부 정보를 보여 주는 로그 검색을 실행하려면 항목을 클릭합니다. |



### <a name="analyze-data-and-create-alerts"></a>데이터 분석 및 경고 만들기

이 솔루션에는 데이터를 분석하는 데 유용한 쿼리가 포함됩니다. 오른쪽으로 스크롤하면 대시보드는 일반 쿼리 여러 개를 나열하며, 이 쿼리를 클릭하면 Azure SQL 데이터에 대한 [로그 검색](log-analytics-log-searches.md)을 수행할 수 있습니다.

![쿼리](./media/log-analytics-azure-sql/azure-sql-queries.png)

이 솔루션에는 일부 *경고 기반 쿼리*가 포함되며 위와 같이 Azure SQL Database와 탄력적 풀에 대한 특정 임계값을 경고하는 데 사용할 수 있습니다.

#### <a name="to-configure-an-alert-for-your-workspace"></a>작업 영역에 대해 경고를 구성하려면

1. [OMS 포털](http://mms.microsoft.com/)로 이동하고 로그인합니다.
2. 솔루션에 대해 구성된 작업 영역을 엽니다.
3. 개요 페이지에서 **Azure SQL Analytics(미리 보기)** 타일을 클릭합니다.
4. 오른쪽으로 스크롤한 다음 쿼리를 클릭하여 경고 만들기를 시작합니다.  
![경고 쿼리](./media/log-analytics-azure-sql/alert-query.png)
5. 로그 검색에서 **경고**를 클릭합니다.  
![검색에서 경고 만들기](./media/log-analytics-azure-sql/create-alert01.png)
6. **경고 규칙 추가** 페이지에서 원하는 적절한 속성과 특정 임계값을 구성한 후 **저장**을 클릭합니다.  
![경고 규칙 추가](./media/log-analytics-azure-sql/create-alert02.png)

### <a name="act-on-azure-sql-monitoring-data"></a>Azure SQL 모니터링 데이터 작업

예를 들어, 수행할 수 있는 가장 유용한 쿼리 중 하나는 모든 Azure 구독에서 모든 Azure SQL 탄력적 풀에 대한 DTU 사용률을 비교하는 것입니다. DTU(데이터베이스 처리량 단위)는 Basic, Standard 및 Premium 데이터베이스 및 풀의 성능 수준에 대한 상대적인 용량을 설명하는 방법을 제공합니다. DTU는 CPU, 메모리, 읽기 및 쓰기의 혼합 측정값을 기반으로 합니다. DTU가 늘어나면 성능 수준에서 제공하는 기능도 증가합니다. 예를 들어 5 DTU의 성능 수준은 1 DTU 성능 수준보다 5배 많은 기능을 제공합니다. 각 서버 및 탄력적 풀에는 최대 DTU 할당량이 적용됩니다.

다음 로그 검색 쿼리를 실행하여 SQL Azure 탄력적 풀의 사용률이 높고 낮음을 쉽게 알릴 수 있습니다.

```
Type=AzureMetrics ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource | display LineChart
```

다음 예제를 보면 1개의 탄력적 풀이 거의 100% DTU의 높은 사용량을 보이는 반면 나머지는 사용량이 매우 적습니다. Azure 활동 로그를 사용하여 사용자 환경에서 잠재적인 최근 변경 내용을 추가로 조사할 수 있습니다.

![로그 검색 결과 - 높은 사용률](./media/log-analytics-azure-sql/log-search-high-util.png)

## <a name="see-also"></a>참고 항목

- Log Analytics의 [로그 검색](log-analytics-log-searches.md)을 사용하여 자세한 Azure SQL 데이터를 확인합니다.
- Azure SQL 데이터를 보여 주는 [사용자 고유의 대시보드 만들기](log-analytics-dashboards.md).
- 특정 Azure SQL 이벤트가 발생하는 경우의 [경고 만들기](log-analytics-alerts.md).

