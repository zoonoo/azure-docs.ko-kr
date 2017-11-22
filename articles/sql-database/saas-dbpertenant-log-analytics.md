---
title: "SQL Database 다중 테넌트 앱과 함께 Log Analytics 사용 | Microsoft Docs"
description: "멀티 테넌트 Azure SQL Database SaaS 앱을 사용하여 Log Analytics(OMS) 설정 및 사용"
keywords: "SQL Database 자습서"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: billgib; sstein
ms.openlocfilehash: c036901bde5fff0d63ee2494de87e49410662099
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2017
---
# <a name="setup-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>멀티 테넌트 Azure SQL Database SaaS 앱을 사용하여 Log Analytics(OMS) 설정 및 사용

이 자습서에서는 탄력적 풀 및 데이터베이스 모니터링을 위해 *Log Analytics([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))*를 설정 및 사용하는 방법을 알아봅니다. 이 자습서는 [성능 모니터링 및 관리 자습서](saas-dbpertenant-performance-monitoring.md)를 기반으로 합니다. 이는 Azure Portal에서 제공된 모니터링 및 경고를 강화하기 위해 *Log Analytics*를 사용하는 방법을 보여줍니다. Log Analytics는 수백 개의 풀과 무수히 많은 데이터베이스를 지원하므로 일정 규모의 모니터링 및 경고에 적합합니다. 또한 여러 Azure 구독에서 다양한 응용 프로그램과 Azure 서비스의 모니터링을 통합할 수 있는 단일 모니터링 솔루션을 제공합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Log Analytics(OMS) 설치 및 구성
> * Log Analytics를 사용하여 풀 및 데이터베이스 모니터링

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Wingtip Tickets SaaS 테넌트별 데이터베이스 앱이 배포됩니다. 5분 내에 배포하려면 [Wingtip Tickets SaaS 테넌트별 데이터베이스 응용 프로그램 배포 및 탐색](saas-dbpertenant-get-started-deploy.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

SaaS 시나리오 및 패턴에 대한 논의와 모니터링 솔루션의 요구 사항에 미치는 영향은 [성능 모니터링 및 관리 자습서](saas-dbpertenant-performance-monitoring.md)를 참조하세요.

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Log Analytics(OMS)를 통한 성능 모니터링 및 관리

SQL Database의 경우 데이터베이스 및 풀에 대한 모니터링 및 경고가 가능합니다. 이 기본 제공 모니터링 및 경고는 특정 리소스에 적용되며 소규모 리소스에 간편하게 사용할 수 있으나, 대규모 설치나 다양한 리소스 및 구독에 대한 통합 보기를 제공하는 데는 적합하지 않습니다.

대규모 시나리오에서는 Log Analytics가 유용할 수 있습니다. 이것은 여러 서비스에서 원격 분석을 수집하고 쿼리 및 경고 설정에 사용할 수 있는 로그 분석 작업 영역에서 수집된 원격 분석에 대한 분석을 제공하는 개별 Azure 서비스입니다. Log Analytics는 기본 제공 쿼리 언어와 데이터 시각화 도구를 제공하므로 운영 데이터 분석과 시각화가 가능합니다. SQL Analytics 솔루션은 미리 정의된 여러 탄력적 풀과 데이터베이스 모니터링 및 경고 뷰와 쿼리를 제공하며 사용자가 필요에 따라 자체 임시 쿼리를 추가하고 저장할 수 있습니다. OMS도 사용자 지정 뷰 디자이너를 제공합니다.

Log Analytics 작업 영역과 분석 솔루션은 Azure Portal과 OMS에서 모두 열 수 있습니다. Azure Portal이 더 최신 액세스 방법이지만 일부 영역에서는 OMS 포털이 더 나을 수 있습니다.

### <a name="create-data-by-starting-the-load-generator"></a>로드 생성기를 시작하여 데이터 만들기 

1. **PowerShell ISE**에서 **Demo-PerformanceMonitoringAndManagement.ps1**을 엽니다. 이 자습서에서 몇 가지 부하 생성 시나리오를 실행할 수 있으므로 이 스크립트를 계속 열어 둡니다.
1. 테넌트가 5개 미만이면 더 흥미로운 모니터링 환경을 위해 테넌트 일괄 처리를 프로비전합니다.
   1. Set **$DemoScenario = 1,** **Provision a batch of tenants**
   1. 스크립트를 실행하려면 **F5** 키를 누릅니다.

1. Set **$DemoScenario** = 2, **Generate normal intensity load (approx 40 DTU)**
1. 스크립트를 실행하려면 **F5** 키를 누릅니다.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS 테넌트별 데이터베이스 응용 프로그램 스크립트 가져오기

Wingtip Tickets SaaS 테넌트별 데이터베이스 스크립트 및 응용 프로그램 소스 코드는 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 리포지토리에서 사용할 수 있습니다. 스크립트 파일은 [Learning Modules 폴더](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/tree/master/Learning%20Modules)에 있습니다. **Learning Modules** 폴더의 구조를 유지하면서 이 폴더를 로컬 컴퓨터로 다운로드합니다.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Log Analytics 및 Azure SQL Analytics 솔루션 설치 및 구성

Log Analytics는 구성이 필요한 별도의 서비스입니다. Log Analytics는 로그 분석 작업 영역에 로그 데이터 및 원격 분석과 메트릭을 수집합니다. 작업 영역은 Azure의 다른 리소스와 마찬가지로 리소스이며 생성이 필요합니다. 작업 영역을 자신이 모니터링하는 응용 프로그램과 동일한 리소스 그룹에서 만들 필요는 없지만 대부분의 경우 이렇게 하는 것이 적합합니다. Wingtip Tickets SaaS 테넌트별 데이터베이스 SaaS 앱의 경우 리소스 그룹을 삭제하면 간단하게 작업 영역을 응용 프로그램과 함께 삭제할 수 있습니다.

1. *PowerShell ISE*에서 **학습 모듈**성능 모니터링 및 관리\\Log Analytics\\\\Demo-LogAnalytics.ps1\\을 엽니다.
1. 스크립트를 실행하려면 **F5** 키를 누릅니다.

이 시점에는 Azure Portal(또는 OMS 포털)에서 Log Analytics를 열 수 있어야 합니다. Log Analytics 작업 영역에서 원격 분석을 수집하여 표시하는 데는 몇 분 정도 시간이 걸립니다. 시스템의 데이터 수집 시간이 길어질수록 더 흥미로운 경험이 될 것입니다. 이제 잠시 쉬면서 부하 생성기가 계속 실행 중인지 확인해 봅니다.


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Log Analytics 및 SQL Analytics 솔루션을 사용하여 풀 및 데이터베이스 모니터링


이 연습에서는 Log Analytics 및 OMS 포털을 열고 데이터베이스 및 풀에 대해 원격 분석이 수집되는 과정을 살펴봅니다.

1. [Azure Portal](https://portal.azure.com)로 이동하고 다른 서비스를 클릭하여 Log Analytics를 연 다음 Log Analytics를 검색합니다.

   ![Log Analytics 열기](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. 이름이 *wtploganalytics-&lt;USER&gt;*인 작업 영역을 선택합니다.

1. **개요**를 선택하여 Azure Portal에서 Log Analytics를 엽니다.
   ![overview-link](media/saas-dbpertenant-log-analytics/click-overview.png)

    **중요**: 솔루션이 활성화되려면 몇 분 정도 소요될 수 있습니다. 기다려 주십시오.

1. Azure SQL Analytics 타일을 클릭하여 엽니다.

    ![개요](media/saas-dbpertenant-log-analytics/overview.png)

    ![분석](media/saas-dbpertenant-log-analytics/analytics.png)

1. 솔루션 블레이드의 보기는 측면으로 스크롤되며 맨 아래에 자체 스크롤 막대가 있습니다(필요한 경우 블레이드 새로 고침).

1. 왼쪽 위의 시간 슬라이더를 사용할 수 잇는 드릴다운 탐색기가 열리는 보기나 개별 리소스를 클릭하거나, 더 좁은 시간 조각에 초점을 맞추는 세로 막대를 클릭하여 다양한 보기를 살펴봅니다. 이 보기를 통해 개별 데이터베이스나 풀을 선택하여 특정 리소스에 초점을 맞출 수 있습니다.

    ![차트](media/saas-dbpertenant-log-analytics/chart.png)

1. 솔루션 블레이드로 돌아가 맨 오른쪽으로 스크롤하면 몇 가지 저장된 쿼리가 표시됩니다. 이 쿼리를 클릭하여 열고 탐색할 수 있습니다. 쿼리를 수정하여 실험해 보고, 작성한 쿼리 중 흥미로운 것은 저장하여 나중에 다른 리소스에서 다시 열어 사용할 수 있습니다.

1. Log Analytics 작업 영역 블레이드로 돌아가 OMS 포털을 선택하여 해당 솔루션을 엽니다.

    ![OMS](media/saas-dbpertenant-log-analytics/oms.png)

1. OMS 포털에서 경고를 구성할 수 있습니다. 데이터베이스 DTU 보기에서 경고 부분을 선택합니다.

1. 표시되는 로그 검색 보기에는 표시할 메트릭의 막대 그래프가 표시됩니다.

    ![로그 검색](media/saas-dbpertenant-log-analytics/log-search.png)

1. 도구 모음에서 경고를 클릭하면 경고 구성을 확인하고 변경할 수 있습니다.

    ![경고 규칙 추가 ](media/saas-dbpertenant-log-analytics/add-alert.png)

Log Analytics 및 OMS의 모니터링 및 경고는 특정 리소스에 적용되는 각각의 리소스 블레이드와는 달리 작업 영역의 데이터에 관한 쿼리를 기준으로 합니다. 따라서 데이터베이스마다 하나씩 정의하는 대신 모든 데이터베이스를 살피는 경고를 정의할 수 있습니다. 또는 여러 리소스 유형에 대한 복합 쿼리를 사용하는 경고를 작성합니다. 쿼리는 작업 영역에서 사용 가능한 데이터를 통해서만 제한됩니다.

SQL Database용 Log Analytics는 작업 영역의 데이터 크기에 따라 과금됩니다. 이 자습서에서는 무료로 일일 500MB로 제한되는 작업 영역을 만들었습니다. 이 한도에 도달하면 더 이상 작업 영역에 데이터가 추가되지 않습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서 학습한 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Log Analytics(OMS) 설치 및 구성
> * Log Analytics를 사용하여 풀 및 데이터베이스 모니터링

[테넌트 분석 자습서](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>추가 리소스

* [초기 Wingtip Tickets SaaS 테넌트별 데이터베이스 응용 프로그램 배포를 기반으로 하는 추가 자습서 ](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
