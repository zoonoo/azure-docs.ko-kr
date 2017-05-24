---
title: "여러 Azure SQL 데이터베이스에 대해 임시 분석 쿼리 실행 | Microsoft Docs"
description: "다중 테넌트 응용 프로그램에서 여러 데이터베이스에 대해 임시 분석 쿼리 실행"
keywords: "sql 데이터베이스 자습서"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: dd41e7f1f131f6c18e03d2434982c3d681342b8b
ms.contentlocale: ko-kr
ms.lasthandoff: 05/12/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wtp-tenants"></a>모든 WTP테넌트에 대해 임시 분석 쿼리 실행

이 자습서에는 임시 분석 데이터베이스를 만들고 모든 테넌트에서 쿼리를 실행합니다. 이러한 쿼리는 WTP 앱의 일일 운영 데이터에 담긴 정보를 추출할 수 있습니다.

임시 분석 쿼리를 실행하기 위해(여러 테넌트) WTP 앱은 분석 데이터베이스와 함께 [탄력적 쿼리](sql-database-elastic-query-overview.md) 분석을 사용합니다.


이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]

> * 임시 분석 데이터베이스 배포
> * 모든 테넌트 데이터베이스에서 분산 쿼리 실행



이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* WTP 앱이 배포되었습니다. 5분 내에 배포하려면 [WTP SaaS 응용 프로그램 배포 및 탐색](sql-database-saas-tutorial.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.


## <a name="ad-hoc-analytics-pattern"></a>임시 분석 패턴

SaaS 응용 프로그램을 사용했을 때의 큰 장점 중 하나는 클라우드에 중앙 저장된 광범위한 테넌트 데이터를 사용할 수 있다는 점입니다. 이 데이터를 사용하여 응용 프로그램의 작업 및 사용, 테넌트, 사용자 및 사용자의 선호 및 행동에 관한 정보를 확보할 수 있습니다. 확인한 정보는 기능 개발, 활용성 향상 및 기타 앱 및 서비스 투자를 위한 지표가 될 수 있습니다.

단일 다중 테넌트 데이터베이스에 있을 때 이 데이터에 액세스하는 것은 쉽지만 잠재적인 수천 개의 데이터베이스 규모로 분산되는 경우는 그렇게 쉽지 않습니다. 한 가지 방법은 공통 스키마를 통해 분산된 데이터베이스 집합 전체에서 임시 쿼리를 구현하는 탄력적 쿼리를 사용하는 것입니다. 탄력적 쿼리는 분산된(테넌트) 데이터베이스에서 테이블을 미러링하는 외부 테이블이 정의되는 단일 *헤드* 데이터베이스를 사용합니다. 이 헤드 데이터베이스에 제출된 쿼리는 분산 쿼리 계획을 생성하기 위해 컴파일되며 이 쿼리 중 일부는 필요에 따라 테넌트 데이터베이스에 푸시됩니다. 탄력적 쿼리는 카탈로그 데이터베이스에서 분할된 데이터베이스 맵을 사용하여 테넌트 데이터베이스의 위치를 제공합니다. 설정 및 쿼리는 일반 T-SQL을 사용하여 바로 진행되며 Power BI나 Excel 같은 도구에서의 임시 쿼리를 지원합니다.

## <a name="get-the-wingtip-application-scripts"></a>Wingtip 응용 프로그램 스크립트 가져오기

Wingtip Tickets 스크립트 및 응용 프로그램 소스 코드는 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github 리포지토리에서 사용할 수 있습니다. 스크립트 파일은 [Learning Modules 폴더](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)에 있습니다. **Learning Modules** 폴더의 구조를 유지하면서 이 폴더를 로컬 컴퓨터로 다운로드합니다.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>임시 분석 쿼리에 사용되는 데이터베이스 배포

이 연습에서는 모든 테넌트 데이터베이스를 망라하는 임시 쿼리를 실행하는 데 사용되는 스키마가 포함된 임시 분석 데이터베이스를 배포합니다.

1. *PowerShell ISE*에서 \\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1*를 열고 다음 갚을 설정합니다.
   * **$DemoScenario** = 2, **임시 분석 데이터베이스 배포**.

1. **F5**를 눌러 스크립트를 실행하고 임시 분석을 위한 새 SQL Database를 만든 다음 WTP 카탈로그에 추가합니다. TicketPurchases, Tickets 및 Venues 테이블이 쿼리 가능한 외부 테이블로 추가됩니다.
   여기서 *RPC 서버를 사용할 수 없다는* 경고가 발생해도 괜찮습니다.


이제 분산된 쿼리를 실행하고 모든 테넌트에서 정보를 수집하는 데 사용할 수 있는 *adhocanalytics* 데이터베이스가 있습니다.

## <a name="run-ad-hoc-analytics-queries"></a>임시 분석 쿼리 실행

이 연습에서는 임시 분석 쿼리를 실행하여 WTP 응용 프로그램에서 테넌트 정보를 밝힙니다.

1. SSMS에서 \\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql*을 엽니다.
1. **adhocanalytics** 데이터베이스에 연결되었는지 확인합니다.
1. 실행할 개별 쿼리를 선택하고 **F5**를 누릅니다.

    ![쿼리](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>다음 단계

이 자습서에서 학습한 방법은 다음과 같습니다.

> [!div class="checklist"]

> * 임시 분석 데이터베이스 배포
> * 모든 테넌트 데이터베이스에서 분산 쿼리 실행

[Log Analytics(OMS) 자습서](sql-database-saas-tutorial-log-analytics.md)

## <a name="additional-resources"></a>추가 리소스

* [초기 WTP(Wingtip Tickets Platform) 응용 프로그램 배포를 기반으로 하는 추가 자습서](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [탄력적 쿼리](sql-database-elastic-query-overview.md)

