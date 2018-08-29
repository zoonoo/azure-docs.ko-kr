---
title: Azure Log Analytics에서 데이터 확인 및 분석 | Microsoft Docs
description: 이 문서에서는 로그 분석을 만들고 편집하는 데 Azure Log Analytics에서 사용할 수 있는 포털을 설명합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: 386aad94461fa3f2ceafb7564342797eefa2f086
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246705"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Log Analytics에서 데이터 확인 및 분석
Azure Portal에서 Log Analytics에 저장된 데이터를 분석하고 임시 분석용 쿼리를 만드는 방법은 두 가지가 있습니다. 이러한 포털을 사용하여 만드는 쿼리는 경고 및 대시보드와 같은 다른 기능에 사용될 수 있습니다.

## <a name="log-analytics-page-preview"></a>Log Analytics 페이지(미리 보기)
Log Analytics 메뉴의 **로그(미리 보기)** 에서 Log Analytics 페이지를 엽니다. 로그 데이터를 사용하고 쿼리를 만들 수 있는 새로운 환경입니다. [Azure Portal에서 Log Analytics 페이지 시작](query-language/get-started-analytics-portal.md)에서 이 포털에 대한 소개를 보고 기능을 살펴볼 수 있습니다.

Log Analytics 페이지는 [로그 검색](#log-search) 환경보다 개선된 다음과 같은 기능을 제공합니다.

* 여러 개의 탭 – 개별 탭을 만들어 여러 쿼리를 작업할 수 있습니다.
* 풍부한 시각화 – 다양한 차트 옵션이 제공됩니다.
* Intellisense 및 언어 자동 완성 기능이 향상되었습니다.
* 구문 강조 표시 – 쿼리의 가독성을 개선합니다. 
* 쿼리 탐색기 – 저장된 쿼리와 함수에 액세스합니다.
* 스키마 보기 – 쿼리를 작성할 때 데이터 구조를 참고할 수 있어 유용합니다.
* 공유 - 쿼리에 대한 링크를 만들거나 원하는 공유 Azure 대시보드에 쿼리를 고정합니다.
* 스마트 분석 - 차트의 스파이크를 파악하고 원인을 빠르게 분석합니다.
* 열 선택 – 쿼리 결과의 열을 정렬하고 그룹화합니다.

> [!NOTE]
> Log Analytics 페이지에는 Azure Portal 외부 도구인 고급 분석 포털과 동일한 기능이 있습니다. 고급 분석 포털을 계속 사용할 수 있지만 Azure Portal의 링크 및 기타 관련 참조는 이러한 새 페이지로 바뀝니다.

![고급 분석 포털](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


### <a name="firewall-requirements"></a>방화벽 요구 사항
Log Analytics 페이지 및 Advanced Analytics 포털에 액세스하려면 브라우저가 다음 주소에 액세스해야 합니다.  브라우저가 방화벽을 통해 Azure Portal에 액세스하는 경우 이 주소에 대한 액세스를 활성화해야 합니다.

| Uri | IP | 포트 |
|:---|:---|:---|
| portal.loganalytics.io | 동적 | 80,443 |
| api.loganalytics.io    | 동적 | 80,443 |
| docs.loganalytics.io   | 동적 | 80,443 |


## <a name="log-search"></a>로그 검색
Log Analytics 메뉴의 **로그** 또는 Azure Monitor 메뉴의 **Log Analytics**에서 로그 검색 페이지를 엽니다. 이는 기본 쿼리를 사용하여 로그 데이터를 분석하는 데 적합합니다. 이는 쿼리 언어에 대한 완전한 지식을 갖추지 않고도 사용할 수 있는 쿼리 편집을 위한 여러 기능을 제공합니다.  [로그 검색을 사용하는 Azure Log Analytics에서 로그 검색 만들기](log-analytics-log-search-log-search-portal.md)에서 이러한 기능에 대한 요약을 확인할 수 있습니다. 


![로그 검색 페이지](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>다음 단계

- 쿼리 언어를 사용하여 쿼리를 작성하는 방법은 [로그 검색](log-analytics-tutorial-viewdata.md) 사용에 대한 자습서를 살펴보세요.
- Log Analytics 페이지와 동일한 환경을 제공하는 [Advanced Analytics 포털 사용법 설명](query-language/get-started-analytics-portal.md)을 살펴보세요.

