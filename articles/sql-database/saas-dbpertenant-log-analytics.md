---
title: SQL Database 다중 테넌트 앱과 함께 Log Analytics 사용 | Microsoft Docs
description: 멀티 테넌트 Azure SQL Database SaaS 앱을 사용하여 Log Analytics(OMS) 설정 및 사용
keywords: SQL Database 자습서
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: b141ca521ae9c4d9bf6a4be620bc8e5432c52f83
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>멀티 테넌트 Azure SQL Database SaaS 앱을 사용하여 Log Analytics(OMS) 설정 및 사용

이 자습서에서는 탄력적 풀 및 데이터베이스 모니터링을 위해 *Log Analytics([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))*를 설정 및 사용하는 방법을 알아봅니다. 이 자습서는 [성능 모니터링 및 관리 자습서](saas-dbpertenant-performance-monitoring.md)를 기반으로 합니다. 이는 Azure Portal에서 제공된 모니터링 및 경고를 강화하기 위해 *Log Analytics*를 사용하는 방법을 보여줍니다. Log Analytics를 사용하면 수천 개의 탄력적 풀과 수십만 개의 데이터베이스를 모니터링할 수 있습니다. 또한 여러 Azure 구독에서 다양한 응용 프로그램과 Azure 서비스의 모니터링을 통합할 수 있는 단일 모니터링 솔루션을 제공합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Log Analytics(OMS) 설치 및 구성
> * Log Analytics를 사용하여 풀 및 데이터베이스 모니터링

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Wingtip Tickets SaaS Database Per Tenant 앱이 배포됩니다. 5분 내에 배포하려면 [Wingtip Tickets SaaS Database Per Tenant 응용 프로그램 배포 및 탐색](saas-dbpertenant-get-started-deploy.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

SaaS 시나리오 및 패턴에 대한 논의와 모니터링 솔루션의 요구 사항에 미치는 영향은 [성능 모니터링 및 관리 자습서](saas-dbpertenant-performance-monitoring.md)를 참조하세요.

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>Log Analytics 또는 Operations Management Suite(OMS)를 사용하여 데이터베이스와 탄력적 풀의 성능 모니터링 및 관리

SQL Database의 경우 Azure Portal에서 데이터베이스 및 풀에 대한 모니터링 및 경고 기능을 사용할 수 있습니다. 기본 제공되는 모니터링 및 경고 기능은 매우 편리하지만 리소스마다 다르게 작동하므로 대규모 설치 환경에서 또는 여러 리소스와 구독을 한눈에 보기 위한 용도로는 적합하지 않습니다.

대규모 시나리오에서는 모니터링 및 경고를 위해 Log Analytics를 사용하는 것이 좋습니다. 별도의 Azure 서비스인 Log Analytics를 사용하면 다수의 서버에 있는 작업 영역에서 수집된 진단 로그와 원격 분석에 대해 분석을 수행할 수 있습니다. Log Analytics에서 기본 제공되는 쿼리 언어와 데이터 시각화 도구를 사용하여 운영 데이터 분석을 수행할 수 있습니다. SQL Analytics 솔루션은 탄력적 풀과 데이터베이스에 대한 몇 가지 사전 정의된 모니터링 및 경고 보기와 쿼리를 제공합니다. OMS도 사용자 지정 뷰 디자이너를 제공합니다.

Log Analytics 작업 영역과 분석 솔루션은 Azure Portal과 OMS에서 모두 열 수 있습니다. Azure Portal이 더 최신 액세스 방법이지만 일부 영역에서는 OMS 포털이 더 나을 수 있습니다.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>테넌트에서 워크로드를 시뮬레이션하여 성능 진단 데이터 만들기 

1. **PowerShell ISE**에서 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\**Demo-PerformanceMonitoringAndManagement.ps1***을 엽니다. 이 자습서에서 몇 가지 부하 생성 시나리오를 실행할 수 있으므로 이 스크립트를 계속 열어 둡니다.
1. 더욱 유용한 모니터링 환경을 만들기 위해 일군의 테넌트를 프로비전합니다. 이 작업에는 몇 분이 소요됩니다.
   1. **$DemoScenario = 1**, _Provision a batch of tenants_를 설정합니다.
   1. **F5** 키를 눌러 스크립트를 실행하여 추가로 17개의 테넌트를 배포합니다.  

1. 이제 로드 생성기를 시작하여 모든 테넌트에서 시뮬레이션된 로드를 실행합니다.  
    1. **$DemoScenario = 2**, _Generate normal intensity load (approx. 30 DTU)_를 설정합니다.
    1. 스크립트를 실행하려면 **F5** 키를 누릅니다.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS Database Per Tenant 응용 프로그램 스크립트 가져오기

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 응용 프로그램 소스 코드는 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 리포지토리에서 확인할 수 있습니다. Wingtip Tickets PowerShell 스크립트를 다운로드하고 차단을 해제하는 방법은 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 확인하세요.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Log Analytics 및 Azure SQL Analytics 솔루션 설치 및 구성

Log Analytics는 구성이 필요한 별도의 서비스입니다. Log Analytics는 로그 분석 작업 영역에서 로그 데이터, 원격 분석과 메트릭을 수집합니다. 로그 분석 작업 영역은 Azure의 다른 리소스와 마찬가지로 하나의 리소스이며 생성이 필요합니다. 작업 영역을 작업 영역이 모니터링하는 응용 프로그램과 동일한 리소스 그룹에 만들 필요는 없지만, 대부분의 경우 이렇게 하는 것이 바람직합니다. Wingtip Tickets 앱의 경우 하나의 리소스 그룹을 사용하면 작업 영역이 응용 프로그램과 함께 삭제되도록 할 수 있습니다.

1. **PowerShell ISE**에서, open *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\**Demo-LogAnalytics.ps1***을 엽니다.
1. 스크립트를 실행하려면 **F5** 키를 누릅니다.

이 시점에는 Azure Portal(또는 OMS 포털)에서 Log Analytics를 열 수 있어야 합니다. Log Analytics 작업 영역에서 원격 분석을 수집하여 표시하는 데는 몇 분 정도 시간이 걸립니다. 시스템의 데이터 수집 시간이 길어질수록 더 유용한 데이터가 수집됩니다. 이제 잠시 쉬면서 부하 생성기가 계속 실행 중인지 확인해 봅니다.

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Log Analytics 및 SQL Analytics 솔루션을 사용하여 풀 및 데이터베이스 모니터링


이 연습에서는 Log Analytics 및 OMS 포털을 열고 데이터베이스 및 풀에 대해 원격 분석이 수집되는 과정을 살펴봅니다.

1. [Azure Portal](https://portal.azure.com)로 이동하고 **모든 서비스**를 클릭하여 Log Analytics를 연 다음 Log Analytics를 검색합니다.

   ![Log Analytics 열기](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. 이름이 _wtploganalytics-&lt;user&gt;_인 작업 영역을 선택합니다.

1. **개요**를 선택하여 Azure Portal에서 Log Analytics를 엽니다.

   ![overview-link](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > 솔루션이 활성화되려면 몇 분 정도 소요될 수 있습니다. 기다려 주십시오.

1. Azure SQL Analytics 타일을 클릭하여 엽니다.

    ![개요](media/saas-dbpertenant-log-analytics/overview.png)

    ![분석](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. 이 솔루션의 보기는 가로로 스크롤됩니다. 하단에 자체 스크롤 막대가 있습니다. (필요한 경우 페이지를 새로 고칩니다.)

1. 타일이나 개별 데이터베이스를 클릭하여 드릴 다운 탐색기를 열어서 요약 페이지를 살펴봅니다.

1. 필터 설정을 변경하여 시간 범위를 수정합니다. 여기서는 _지난 1시간_을 선택하겠습니다.

    ![시간 필터](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. 데이터베이스를 하나 선택하여 데이터베이스의 쿼리 사용률과 메트릭을 살펴봅니다.

    ![데이터베이스 분석](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. 사용률 메트릭을 보려면 분석 페이지를 오른쪽으로 스크롤합니다.
 
     ![데이터베이스 메트릭](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. 분석 페이지를 왼쪽으로 스크롤한 다음, 리소스 정보 목록에 있는 서버 타일을 클릭합니다. 이렇게 하면 서버의 풀과 데이터베이스를 보여주는 페이지가 열립니다. 

     ![리소스 정보](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![server with pools and databases](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. 서버의 풀과 데이터베이스를 보여주는 서버 페이지에서 풀을 클릭합니다.  풀 페이지에서 오른쪽으로 스크롤하여 풀 메트릭을 봅니다.  

     ![풀 메트릭](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. Log Analytics 작업 영역으로 돌아가서 **OMS Portal**을 선택하여 작업 영역을 엽니다.

    ![OMS](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

OMS Portal에서 작업 영역의 로그와 메트릭 데이터를 자세히 살펴볼 수 있습니다.  

Log Analytics와 OMS의 모니터링 및 경고는 Azure Portal의 경우 각 리소스에 대해 경고가 정의되는 것과 달리 작업 영역에 있는 데이터에 대한 쿼리를 바탕으로 합니다. 쿼리가 경고를 바탕으로 하기 때문에 데이터베이스마다 일일이 경고를 설정하는 대신 모든 데이터베이스를 대상으로 하는 하나의 경고를 정의할 수 있습니다. 쿼리는 작업 영역에서 사용 가능한 데이터를 통해서만 제한됩니다.

OMS를 사용하여 쿼리를 실행하고 경고를 설정하는 자세한 방법은 [Log Analytics에서 경고 규칙 작업](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)을 참조하세요.

SQL Database용 Log Analytics는 작업 영역의 데이터 크기에 따라 과금됩니다. 이 자습서에서는 일일 500MB로 제한되는 무료 작업 영역을 만들었습니다. 이 한도에 도달하면 작업 영역에 더 이상 데이터가 추가되지 않습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Log Analytics(OMS) 설치 및 구성
> * Log Analytics를 사용하여 풀 및 데이터베이스 모니터링

[테넌트 분석 자습서](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>추가 리소스

* [초기 Wingtip Tickets SaaS 테넌트별 데이터베이스 응용 프로그램 배포를 기반으로 하는 추가 자습서 ](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
