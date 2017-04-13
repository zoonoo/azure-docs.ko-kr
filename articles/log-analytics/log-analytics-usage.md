---
title: "Log Analytics에서 데이터 사용 현황 분석 | Microsoft Docs"
description: "Log Analytics에서 사용량 대시보드를 사용하여 OMS 서비스로 전송되는 데이터의 양을 볼 수 있습니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/12/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 7e3d4b83fefdc70f292cf85b682cf8ed756bf4c5
ms.openlocfilehash: e7f04df679604f274c8ad9bf4daddc63c8b5418a
ms.lasthandoff: 02/22/2017


---
# <a name="analyze-data-usage-in-log-analytics"></a>Log Analytics에서 데이터 사용 현황 분석
Log Analytics는 데이터를 수집하여 주기적으로 OMS 서비스에 보냅니다.  **Log Analytics 사용량** 대시보드를 사용하여 OMS 서비스로 전송되는 데이터의 양을 볼 수 있습니다. 이 대시보드에는 솔루션에서 전송되는 데이터의 양 및 서버에서 데이터를 보내는 빈도도 표시됩니다.

> [!NOTE]
> 무료 계정이 있는 경우, 매일 OMS 서비스에 500MB의 데이터를 전송하도록 제한됩니다. 일일 한도에 도달한 경우 데이터 분석이 중지되고 다음 날이 시작될 때 재개됩니다. 이 경우 OMS에서 허용 또는 처리되지 않은 모든 데이터를 다시 전송해야 합니다.

일일 사용량 한도를 초과하거나 근접한 경우, 선택적으로 솔루션을 제거하여 OMS 서비스에 보내는 데이터의 크기를 줄일 수 있습니다. 솔루션 제거에 대한 자세한 내용은 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)를 참조하세요.

![사용량 대시보드](./media/log-analytics-usage/usage-dashboard01.png)

**Log Analytics 사용량** 대시보드에는 다음 정보가 표시됩니다.

- 데이터 볼륨
    - (현재 시간 범위에 기반) 시간에 따른 데이터 볼륨
    - 솔루션별 데이터 볼륨
    - 컴퓨터와 연결되지 않은 데이터
- 컴퓨터
    - 데이터를 전송하는 컴퓨터
    - 지난 24시간 동안 데이터가 없는 컴퓨터
- 제품
    - 이해 및 분석 노드
    - 자동화 및 제어 노드
    - 보안 노드
- 성능
    - 데이터 수집 및 인덱싱 소요 시간
- 쿼리 목록

## <a name="understanding-nodes-for-oms-offers"></a>OMS 제품에 대한 노드 이해

*노드당(OM)* 가격 책정 계층에 있는 경우 활성화한 노드 및 솔루션의 수에 따라 요금이 청구됩니다. 사용 대시보드의 *제품* 섹션에서 사용 중인 각 제품의 노드 수를 볼 수 있습니다.

![사용량 대시보드](./media/log-analytics-usage/log-analytics-usage-offerings.png)

## <a name="to-work-with-usage-data"></a>사용률 데이터를 작업하려면
1. [Azure Portal](https://portal.azure.com)에 아직 로그인하지 않은 경우 Azure 구독을 사용하여 로그인합니다.
2. **허브** 메뉴에서 **추가 서비스**를 클릭하고 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 클릭합니다.  
    ![Azure 허브](./media/log-analytics-usage/hub.png)
3. **Log Analytics** 대시보드는 작업 영역 목록을 표시합니다. 작업 영역을 선택합니다.
4. *작업 영역* 대시보드에서 **Log Analytics 사용량**을 클릭합니다.
5. **Log Analytics 사용량** 대시보드에서 **시간: 최근 24시간**을 클릭하여 시간 간격을 변경합니다.  
    ![시간 간격](./media/log-analytics-usage/time.png)
6. 관심이 있는 영역을 표시하는 사용량 범주 블레이드를 확인합니다. [로그 검색](log-analytics-log-searches.md)에서 블레이드를 선택하고 더 자세히 보려는 항목을 클릭합니다.  
    ![예제 데이터 사용량 블레이드](./media/log-analytics-usage/blade.png)
7. 로그 검색 대시보드에서 검색에서 반환되는 결과를 검토합니다.  
    ![예제 사용량 로그 검색](./media/log-analytics-usage/usage-log-search.png)


## <a name="next-steps"></a>다음 단계
* 기능 및 솔루션에 따라 수집되어 OMS로 전송되는 상세 정보를 확인하려면 [Log Analytics의 로그 검색](log-analytics-log-searches.md)을 참조하세요.

