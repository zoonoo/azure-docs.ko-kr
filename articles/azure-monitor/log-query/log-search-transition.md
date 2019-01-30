---
title: Azure Log Analytics에서 데이터 확인 및 분석 | Microsoft Docs
description: Log Analytics 로그 검색 사용자가 Azure Monitor 로그 쿼리 환경을 사용하도록 지원합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: 89811e95ec24eb354020dd6384f6fdab6cee8c8f
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392574"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Log Analytics 로그 검색에서 Azure Monitor 로그로 전환
Log Analytics의 로그 검색은 Azure Monitor 로그를 분석하기 위한 새로운 환경으로 바뀌었습니다. 로그 검색 페이지는 이제 Azure Portal의 **Log Analytics 작업 영역**에 있는 **로그(클래식)** 메뉴 항목을 통해 계속 액세스할 수 있지만 이 페이지는 2019년 2월 15일에 제거될 예정입니다. 이 문서에서는 두 환경 간의 차이점을 설명하여 로그 검색에서 전환할 수 있도록 지원합니다. 

## <a name="extract-custom-fields"></a>사용자 지정 필드 추출 
로그 검색의 목록 보기에서 [사용자 지정 필드](../platform/custom-fields.md)를 추출합니다. 이 보기에서 필드의 메뉴에는 _테이블에서 필드 추출_ 작업이 포함되어 있습니다.

![로그 검색 필드 추출](media/log-search-transition/extract-fields-log-search.png)

Azure Monitor 로그의 테이블 보기에서 사용자 지정 필드를 추출합니다. 왼쪽에 있는 화살표를 클릭하여 레코드를 확장한 후 줄임표를 클릭하여 _필드 추출_ 작업에 액세스합니다.

![로그 필드 추출](media/log-search-transition/extract-fields-logs.png)

## <a name="filter-results-of-a-query"></a>쿼리 결과 필터링
로그 검색에서 검색 결과가 전달되면 필터 목록이 표시됩니다. 필터를 선택하고 **적용**을 클릭하여 선택한 필터로 쿼리를 실행합니다.

![로그 검색 필터](media/log-search-transition/filter-log-search.png)

Azure Monitor 로그에서 *필터(미리 보기)* 를 선택하여 필터를 표시합니다. 필터 아이콘을 클릭하여 추가 필터를 표시합니다. 필터를 선택하고 **적용 및 실행**을 클릭하여 선택한 필터로 쿼리를 실행합니다.

![로그 필터](media/log-search-transition/filter-logs.png)

## <a name="functions-and-computer-groups"></a>함수 및 컴퓨터 그룹
로그 검색에서 검색을 저장하려면 **저장된 검색** 및 **추가**를 선택하여 이름, 범주 및 쿼리 텍스트를 제공합니다. 함수 별칭을 추가하여 [컴퓨터 그룹](../platform/computer-groups.md)을 만듭니다.

![로그 검색 저장](media/log-search-transition/save-search-log-search.png)

Azure Monitor 로그에서 현재 쿼리를 저장하려면 **저장**을 선택합니다. **다른 이름으로 저장**을 _함수_로 변경하고 **함수 별칭**을 제공하여 [함수](functions.md)를 만듭니다.

![로그 쿼리 저장](media/log-search-transition/save-query-logs.png)

## <a name="saved-searches"></a>저장된 검색
로그 검색에서 저장한 검색은 작업 모음 항목 **저장된 검색**을 통해 사용할 수 있습니다. Azure Monitor 로그의 **쿼리 탐색기**에서 저장한 쿼리에 액세스합니다.

![쿼리 탐색기](media/log-search-transition/query-explorer.png)

## <a name="take-action"></a>작업 수행
로그 검색에서 **작업 수행**을 선택하여 검색 결과에서 [Runbook을 시작](take-action.md)할 수 있습니다.

![작업 수행](media/log-search-transition/take-action-log-search.png)

Azure Monitor 로그의 [로그 쿼리에서 경고를 만듭니다](../platform/alerts-log.md). 경고에 대한 응답으로 실행되는 하나 이상의 작업을 사용하여 작업 그룹을 구성합니다.

![작업 그룹](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>다음 단계

- 새 [Azure Monitor 로그 환경](get-started-portal.md)에 대해 자세히 알아봅니다.