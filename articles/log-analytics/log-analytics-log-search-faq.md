---
title: "Log Analytics 새 로그 검색 질문과 대답 | Microsoft 문서"
description: "이 문서에서는 Log Analytics를 새 쿼리 언어로 업그레이드하는 과정과 관련된 질문과 대답을 제공합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 2de9a87390da11a034c6cebaa37d4cc89edf3cad
ms.contentlocale: ko-kr
ms.lasthandoff: 08/09/2017

---

# <a name="log-analytics-new-log-search-frequently-asked-questions"></a>Log Analytics 새 로그 검색 질문과 대답

이 문서에서는 [Log Analytics를 새 쿼리 언어로](log-analytics-log-search-upgrade.md) 업그레이드하는 과정과 관련된 질문과 대답을 제공합니다.



## <a name="upgrade-process"></a>업그레이드 프로세스

### <a name="i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>작업 영역이 여러 개 있습니다. 모든 작업 영역을 동시에 업그레이드할 수 있나요?  
A: 아니요.  각 업그레이드는 단일 작업 영역에 적용됩니다. 현재는 여러 작업 영역을 한 번에 업그레이드할 수 있는 방법이 없습니다. 업그레이드된 작업 영역의 다른 사용자도 영향을 받게 됩니다.  

### <a name="will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>업그레이드하면 작업 영역에 수집되어 있는 기존 로그 데이터가 수정되나요?  
아니요. 작업 영역 검색에 사용할 수 있는 로그 데이터는 업그레이드의 영향을 받지 않습니다. 저장된 검색, 경고 및 보기는 새 검색 언어로 자동 변환됩니다.  

### <a name="what-happens-if-i-dont-upgrade-my-workspace"></a>작업 영역을 업그레이드하지 않으면 어떻게 되나요?  
향후 몇 개월 이내에 레거시 로그 검색은 사용되지 않을 예정입니다. 이 시점까지 업그레이드하지 않은 작업 영역은 자동으로 업그레이드됩니다.

### <a name="i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>업그레이드를 선택하지 않았는데도 작업 영역이 업그레이드되었습니다. 어떻게 된 건가요?  
해당 작업 영역의 다른 관리자의 작업 영역을 업그레이드했을 수 있습니다. 새 언어가 일반 공급 상태가 되면 모든 작업 영역은 자동으로 업그레이드됩니다.  

### <a name="i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>실수로 업그레이드를 해서 취소하고 모든 항목을 다시 복원해야 합니다. 어떻게 해야 하나요?  
걱정하실 필요가 없습니다.  업그레이드 전에 작업 영역의 스냅숏이 생성되었으므로 해당 스냅숏을 복원하면 됩니다. 하지만 업그레이드 후에 저장한 검색, 경고 또는 보기는 손실됩니다.  작업 영역 환경을 복원하려면 [업그레이드 후에 다시 원래대로 되돌릴 수 있나요?](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade)의 절차를 따르세요.



## <a name="log-searches"></a>로그 검색

### <a name="i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>업그레이드한 작업 영역 외부에 저장된 검색이 있습니다. 이러한 검색을 새 검색 언어로 자동 변환할 수 있나요?
로그 검색 페이지의 언어 변환기 도구를 사용하면 각 검색을 변환할 수 있습니다.  작업 영역을 업그레이드하지 않고 여러 검색을 자동으로 변환하는 방법은 없습니다.


## <a name="alerts"></a>경고

### <a name="i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>경고 규칙이 매우 많습니다. 업그레이드 후에 새 언어로 규칙을 다시 만들어야 하나요?  
아니요, 경고 규칙은 업그레이드 중에 새 검색 언어로 자동 변환됩니다.  

## <a name="power-bi"></a>Power BI

### <a name="does-anything-change-with-powerbi-integration"></a>PowerBI 통합에서 변경되는 사항이 있나요?
예.  작업 영역이 업그레이드되면 Log Analytics 데이터를 Power BI로 내보내는 프로세스가 더 이상 작동하지 않습니다.  그러면 업그레이드 전에 만든 모든 기존 일정을 사용할 수 없게 됩니다.  업그레이드 후 Azure Log Analytics는 Application Insights와 같은 플랫폼을 사용하며, [Application Insights 쿼리를 Power BI로 내보내는 프로세스](../application-insights/app-insights-export-power-bi.md#export-analytics-queries)와 같은 프로세스를 사용하여 Log Analytics 쿼리를 Power BI로 내보냅니다.

## <a name="dashboards"></a>대시보드

### <a name="can-i-still-use-dashboards"></a>대시보드를 계속 사용할 수 있습니까?
작업 영역을 업그레이드하기 전에 만든 대시보드를 계속 사용할 수 있습니다. 하지만 해당 대시보드를 편집하거나 새로 만들 수는 없습니다.  [뷰 디자이너](log-analytics-view-designer.md)를 사용하여 계속 뷰를 만들고 편집할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [새 Log Analytics 쿼리 언어로 작업 영역 업그레이드](log-analytics-log-search-upgrade.md)에 대해 자세히 알아봅니다.

