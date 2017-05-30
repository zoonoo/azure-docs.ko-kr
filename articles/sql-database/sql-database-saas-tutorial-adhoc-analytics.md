---
title: "여러 Azure SQL 데이터베이스에 대해 임시 분석 쿼리 실행 | Microsoft Docs"
description: "Wingtip SaaS 다중 테넌트 앱에서 여러 SQL Database에 걸쳐 임시 분석 쿼리를 실행합니다."
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
ms.date: 05/22/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 31be50ca3f64cc183e516f1b0f06f5a4265f6103
ms.contentlocale: ko-kr
ms.lasthandoff: 05/25/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wingtip-saas-tenants"></a>모든 Wingtip SaaS 테넌트에서 임시 분석 쿼리 실행

이 자습서에는 임시 분석 데이터베이스를 만들고 모든 테넌트에서 쿼리를 실행합니다. 이러한 쿼리는 WTP 앱의 일일 운영 데이터에 담긴 정보를 추출할 수 있습니다.

임시 분석 쿼리를 실행하기 위해(여러 테넌트) Wingtip SaaS 앱은 분석 데이터베이스와 함께 [탄력적 쿼리](sql-database-elastic-query-overview.md)를 사용합니다.


이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]

> * 임시 분석 데이터베이스 배포
> * 모든 테넌트 데이터베이스에서 분산 쿼리 실행



이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Wingtip SaaS 앱이 배포되었습니다. 5분 내에 배포하려면 [Wingtip SaaS 응용 프로그램 배포 및 탐색](sql-database-saas-tutorial.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.


## <a name="ad-hoc-analytics-pattern"></a>임시 분석 패턴

SaaS 응용 프로그램을 사용했을 때의 큰 장점 중 하나는 클라우드 중앙에 저장된 광범위한 테넌트 데이터를 사용할 수 있다는 점입니다. 이 데이터를 사용하여 응용 프로그램의 작업 및 사용, 테넌트, 사용자 및 사용자의 선호 및 행동 등에 관한 정보를 확보할 수 있습니다. 이러한 정보는 기능 개발, 활용성 향상 및 기타 앱 및 서비스 투자를 유도할 수 있습니다.

단일 다중 테넌트 데이터베이스에 있을 때 이 데이터에 액세스하는 것은 쉽지만 잠재적인 수천 개의 데이터베이스 규모로 분산되는 경우는 그렇게 쉽지 않습니다. 한 가지 방법은 공통 스키마를 통해 분산된 데이터베이스 집합 전체에서 임시 쿼리를 구현하는 탄력적 쿼리를 사용하는 것입니다. 탄력적 쿼리는 분산된(테넌트) 데이터베이스에서 테이블 또는 뷰를 미러링하는 외부 테이블이 정의되는 단일 *헤드* 데이터베이스를 사용합니다. 이 헤드 데이터베이스에 제출된 쿼리는 분산 쿼리 계획을 생성하기 위해 컴파일되며 이 쿼리 중 일부는 필요에 따라 테넌트 데이터베이스에 푸시됩니다. 탄력적 쿼리는 카탈로그 데이터베이스에서 분할된 데이터베이스 맵을 사용하여 테넌트 데이터베이스의 위치를 제공합니다. 설정 및 쿼리는 일반 [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)을 사용하여 바로 진행되며 Power BI나 Excel 같은 도구에서의 임시 쿼리를 지원합니다.

## <a name="get-the-wingtip-application-scripts"></a>Wingtip 응용 프로그램 스크립트 가져오기

Wingtip SaaS 스크립트 및 응용 프로그램 소스 코드는 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github 리포지토리에서 사용할 수 있습니다. [Wingtip SaaS 스크립트를 다운로드하는 단계](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts).


## <a name="explore-the-global-views-in-the-tenant-databases"></a>테넌트 데이터베이스의 전역 뷰 탐색

Wingtip SaaS 응용 프로그램은 데이터베이스 당 테넌트 모델을 사용하여 빌드되므로 테넌트 데이터베이스 스키마는 단일 테넌트 관점에서 정의됩니다. 테넌트 전용 정보는 하나의 테이블 *Venue*에 존재하며 이 테이블에는 항상 하나의 행이 있고 따라서 기본 키가 없는 힙으로 설계됩니다.  정상적인 사용에서는 데이터가 속해 있는 테넌트에 의심의 여지가 없기 때문에 스키마의 다른 테이블은 *Venue* 테이블과 관련될 필요가 없습니다.  그러나 모든 데이터베이스에서 쿼리를 수행하면 데이터베이스의 테이블에 있는 데이터를 특정 테넌트로 상호 연결하는 것이 중요 해집니다. 이를 단순화하기 위해 테넌트 데이터베이스에 각 테넌트에 대한 '전역' 뷰를 제공하는 뷰가 추가되었습니다. 전역 뷰는 전역적으로 쿼리 될 각 테이블에 테넌트 ID를 투영합니다. 이렇게 하면 각 테넌트의 데이터를 쉽게 식별 할 수 있습니다. 편의상 이러한 뷰가 모든 테넌트 데이터베이스(골든 DB 포함, 새 테넌트가 프로비저닝 될 이러한 뷰를 사용할 수 있음)에 미리 작성되었습니다.

1. SSMS를 열고 [테넌트1-&lt;사용자&gt; 서버에 연결](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms)합니다.
1. **데이터베이스**를 확장하고 **contosoconcerthall**을 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.
1. 다음 쿼리를 실행하여 전역 뷰를 탐색합니다.

   ```T-SQL
   -- This is the base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   -- In the sample database we calculated an integer id from a hash of the Venue name,
   -- but any approach could be used to introduce a unique value.
   -- This is similar to how we create the tenant key in the catalog,
   -- but there is no requirement that the catalog key and this id be the same.
   SELECT * FROM Venues

   -- The base Events table which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

뷰를 점검하고 어떻게 생성되었는지 보려면:

1. **개체 탐색기**에서 **contosoconcethall** > **뷰**를 확장합니다.

   ![뷰](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

1. **dbo.Venues**를 마우스 오른쪽 단추로 클릭합니다.
1. **뷰 스크립팅** > **CREATE** > **새 쿼리 편집기 창**를 선택합니다.

어떻게 생성되었는지 보려면 모든 *뷰*에 이 작업을 수행합니다.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>임시 분석 쿼리에 사용되는 데이터베이스 배포

이 연습에서는 *adhocanalytics* 데이터베이스를 배포합니다. 이 데이터베이스는 모든 테넌트 데이터베이스를 쿼리하는 데 사용되는 스키마를 포함합니다. 데이터베이스는 모든 관리 관련 데이터베이스를 포함하는 서버 인 기존 카탈로그 서버에 배포됩니다.

1. *PowerShell ISE*에서 \\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1*를 열고 다음 갚을 설정합니다.
   * **$DemoScenario** = 2, **임시 분석 데이터베이스 배포**.

1. 스크립트에서 데이터베이스의 스키마가 포함된 SQL 스크립트까지 아래로 스크롤합니다.  스크립트를 검토하고 다음을 참고합니다.

   1. 탄력적인 쿼리는 데이터베이스 범위 자격 증명을 사용하여 각 테넌트 데이터베이스에 액세스합니다. 이 자격 증명은 모든 데이터베이스에서 사용할 수 있어야 하며 일반적으로 이러한 임시 쿼리를 사용하는 데 필요한 최소 권한이 부여되어야 합니다.
   1. 카탈로그 데이터베이스에서 테넌트 Shard Map을 사용하도록 정의된 외부 데이터 원본.  이것을 외부 데이터 원본으로 사용하면 쿼리가 발급되는 시점에 카탈로그에 등록된 모든 데이터베이스에 쿼리가 배포됩니다.
   1. 이전 섹션에서 설명한 전역 뷰를 참조하는 외부 테이블.
   1. 생성되고 채워진 로컬 테이블 *VenueTypes*.  이 참조 데이터 테이블은 모든 테넌트 데이터베이스에서 공통적으로 사용되기 때문에 일부 쿼리에서는 테넌트 데이터베이스와 *adhocanalytics* 데이터베이스간에 이동되는 데이터의 양을 줄일 수 있는 로컬 테이블로 나타낼 수 있습니다.

1. 스크립트를 실행하고 *adhocanalytics* 데이터베이스를 만들려면 **F5** 키를 누릅니다.

   *RPC 서버를 사용할 수 없습니다.*에 관련된 오류를 무시해도 좋습니다.


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

이제 [테넌트 분석 자습서](sql-database-saas-tutorial-tenant-analytics.md)를 시도해 보세요.

## <a name="additional-resources"></a>추가 리소스

* [초기 Wingtip SaaS 응용 프로그램 배포를 기반으로 작성된 추가 자습서](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [탄력적 쿼리](sql-database-elastic-query-overview.md)

