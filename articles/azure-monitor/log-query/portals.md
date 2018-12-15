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
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: bwren
ms.openlocfilehash: 9a5472a6dfc944eb793e863704897c92b1a7572e
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183355"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Log Analytics에서 데이터 확인 및 분석
Azure Portal에서 Log Analytics에 저장된 데이터를 분석하고 임시 분석용 쿼리를 만드는 방법은 두 가지가 있습니다. 이러한 포털을 사용하여 만드는 쿼리는 경고 및 대시보드와 같은 다른 기능에 사용될 수 있습니다.

## <a name="log-analytics-page"></a>Log Analytics 페이지
Log Analytics 메뉴의 **로그**에서 Log Analytics 페이지를 엽니다. 로그 데이터를 사용하고 쿼리를 만들 수 있는 새로운 환경입니다. [Azure Portal에서 Log Analytics 페이지 시작](../../azure-monitor/log-query/get-started-portal.md)에서 이 포털에 대한 소개를 보고 기능을 살펴볼 수 있습니다.

Log Analytics 페이지는 [로그 검색(클래식)](#log-search-classic) 환경보다 개선된 다음과 같은 기능을 제공합니다.

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

![고급 분석 포털](media/portals/advanced-analytics-portal.png)

### <a name="resource-logs"></a>리소스 로그
새 Log Analytics 환경은 Virtual Machines와 같은 다양한 Azure 리소스와 통합됩니다. 즉, Azure Monitor 또는 Log Analytics로 전환하여 리소스 컨텍스트를 잃지 않고도 리소스의 모니터링 메뉴를 통해 Log Analytics 페이지를 직접 열 수 있습니다. **로그**는 아직 일부 Azure 리소스에 대해서만 사용할 수 있지만, 다양한 리소스 종류에 대한 포털 메뉴에 표시될 예정입니다.

특정 리소스에서 Log Analytics를 열면 범위가 자동으로 해당 리소스의 로그 레코드로만 제한됩니다.   다른 레코드를 포함하는 쿼리를 작성하려면 Log Analytics 또는 Azure Monitor 메뉴에서 열어야 합니다.

다음 옵션은 아직 Log Analytics의 리소스 보기를 통해 사용할 수 없습니다.

- 저장
- 경고 설정
- 쿼리 탐색기
- 다른 작업 영역/리소스로 전환(현재 계획되지 않음)


### <a name="firewall-requirements"></a>방화벽 요구 사항
Log Analytics 페이지 및 Advanced Analytics 포털에 액세스하려면 브라우저가 다음 주소에 액세스해야 합니다.  브라우저가 방화벽을 통해 Azure Portal에 액세스하는 경우 이 주소에 대한 액세스를 활성화해야 합니다.

| Uri | IP | 포트 |
|:---|:---|:---|
| portal.loganalytics.io | 동적 | 80,443 |
| api.loganalytics.io    | 동적 | 80,443 |
| docs.loganalytics.io   | 동적 | 80,443 |


## <a name="log-search-classic"></a>로그 검색(클래식)
Log Analytics 메뉴의 **로그(클래식)** 또는 Azure Monitor 메뉴의 **Log Analytics**에서 로그 검색 페이지를 엽니다. Log Analytics 쿼리 작업에 사용되는 클래식 페이지로, 위에 나열된 [Log Analytics 페이지](#log-analytics-page)의 추가 기능이 없습니다.



![로그 검색 페이지](media/portals/log-search-portal.png)


## <a name="next-steps"></a>다음 단계

- 쿼리 언어를 사용하여 쿼리를 작성하는 방법은 [로그 검색](../../azure-monitor/learn/tutorial-viewdata.md) 사용에 대한 자습서를 살펴보세요.
- Log Analytics 페이지와 동일한 환경을 제공하는 [Advanced Analytics 포털 사용법 설명](../../azure-monitor/log-query/get-started-portal.md)을 살펴보세요.

