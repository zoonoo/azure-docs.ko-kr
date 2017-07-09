---
title: "성능 문제 해결 및 데이터베이스 최적화 | Microsoft Docs"
description: "SQL Database에 성능 권장 사항을 적용하고 데이터베이스에 대해 실행되는 쿼리의 성능에 대한 정보를 얻는 방법을 알아봅니다."
metakeywords: azure sql database performance monitoring recommendation
services: sql-database
documentationcenter: 
manager: jhubbard
author: jan-eng
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,monitor & tune
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: f9ae96cdc80c347593f229cb2fce3f2d4d8e7caf
ms.contentlocale: ko-kr
ms.lasthandoff: 06/15/2017


---
# <a name="troubleshoot-performance-issues-and-optimize-your-database"></a>성능 문제 해결 및 데이터베이스 최적화

인덱스 누락 및 최적화되지 않은 쿼리는 데이터베이스 성능을 저하시키는 일반적인 원인입니다. 이 자습서에서는 다음에 대해 알아봅니다.
> [!div class="checklist"]
> * 성능 향상 권장 사항 검토, 적용 및 되돌리기
> * 높은 리소스 사용률을 나타내는 쿼리 찾기
> * 장기 실행 쿼리 찾기

> 성능 문제(예를 들어, 권장 사항을 받기 위한 인덱스 누락)가 있는 데이터베이스에서 지속적인 워크로드가 필요합니다.
>

## <a name="log-in-to-the-azure-portal"></a>Azure 포털에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="review-and-apply-a-recommendation"></a>권장 사항 검토 및 적용

다음 단계에 따라 데이터베이스에 대한 시스템에서 권장 사항을 적용합니다.

1. 데이터베이스 블레이드에서 **쿼리 권장 사항** 메뉴를 클릭합니다.

    ![성능 권장 사항](./media/sql-database-performance-tutorial/perf_recommendations.png)

2. 권장 사항 목록에서 사용 중인 권장 사항을 선택합니다. 이 예제에서 인덱스를 만듭니다.

    ![권장 사항 선택](./media/sql-database-performance-tutorial/create_index.png)

3. **적용** 단추를 클릭하여 권장 사항을 적용합니다. 필요에 따라 권장 사항 정보를 검토하고 **스크립트 보기** 단추를 클릭하여 실행할 T-SQL 스크립트를 표시합니다.

    ![권장 사항 적용](./media/sql-database-performance-tutorial/apply.png)

4. [선택 사항] 권장 사항이 자동으로 적용되도록 하려면 자동 조정을 사용합니다.

    ![자동 조정](./media/sql-database-performance-tutorial/auto_tuning.png)

## <a name="revert-a-recommendation"></a>권장 사항 되돌리기

Database Advisor는 구현된 모든 권장 사항을 모니터링합니다. 권장 사항으로 워크로드가 개선되지 않으면 자동으로 되돌려집니다. 수동으로 권장 사항 되돌리기가 가능하지만 대부분의 경우 반드시 그런 것은 아닙니다. 권장 사항을 되돌리려면

1. 성능 권장 사항 메뉴로 이동하고 적용된 권장 사항 중 하나를 선택합니다.

    ![권장 사항 선택](./media/sql-database-performance-tutorial/select.png)

2. 세부 정보 보기에서 **되돌리기**를 클릭합니다.

    ![권장 사항 되돌리기](./media/sql-database-performance-tutorial/revert.png)

## <a name="find-the-query-that-consumes-the-most-resources"></a>대부분의 리소스를 사용하는 쿼리 찾기

다음 단계에 따라 대부분의 리소스를 사용하는 쿼리를 찾습니다.

1. 데이터베이스 블레이드에서 **Query Performance Insight** 메뉴를 클릭합니다.

    ![쿼리 인사이트](./media/sql-database-performance-tutorial/query_perf_insights.png)

2. 리소스 종류를 선택합니다.

    ![쿼리 인사이트](./media/sql-database-performance-tutorial/select_resource_type.png)

3. 테이블에서 첫 번째 쿼리를 선택합니다.

    ![쿼리 인사이트](./media/sql-database-performance-tutorial/select_query.png)

4. 쿼리 세부 정보를 검토합니다.

    ![쿼리 인사이트](./media/sql-database-performance-tutorial/query_details.png)

## <a name="find-the-longest-running-query"></a>가장 오래 실행되는 쿼리 찾기

1. Query Performance Insight로 이동하고 **장기 실행 쿼리** 탭을 선택합니다.

    ![쿼리 인사이트](./media/sql-database-performance-tutorial/long_running.png)

3. 테이블에서 첫 번째 쿼리를 선택합니다.

    ![쿼리 인사이트](./media/sql-database-performance-tutorial/select_first_query.png)

4. 쿼리 세부 정보를 검토합니다.

    ![쿼리 인사이트](./media/sql-database-performance-tutorial/review_query_details.png)



## <a name="next-steps"></a>다음 단계 
인덱스 누락 및 최적화되지 않은 쿼리는 데이터베이스 성능을 저하시키는 일반적인 원인입니다. 이 자습서에서는 다음에 대해 알아보았습니다.
> [!div class="checklist"]
> * 성능 향상 권장 사항 검토, 적용 및 되돌리기
> * 높은 리소스 사용률을 나타내는 쿼리 찾기
> * 장기 실행 쿼리 찾기

[SQL 데이터베이스 성능 튜닝 팁](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-performance)

