---
title: Azure Monitor Log Analytics 쿼리 언어 | Microsoft Docs
description: Log Analytics에서 쿼리를 작성하는 방법을 위한 리소스에 대한 참조입니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: bwren
ms.openlocfilehash: 32e64ce7772d562ea34a0d74afbd737be27d247d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968871"
---
# <a name="log-analytics-query-language"></a>Log Analytics 쿼리 언어
Log Analytics는 Azure Monitor를 위한 로그 수집 및 분석을 제공합니다. Azure Data Explorer를 기반으로 하며 동일한 쿼리 언어 버전을 사용합니다. [Azure Data Explorer 쿼리 언어 설명서](/azure/kusto/query)에는 언어용 전체 세부 정보가 나와 있으며, Log Analytics 쿼리 작성을 위한 기본 리소스로 참조할 수 있습니다. 이 페이지에는 쿼리를 쓰는 방법 및 언어의 Log Analytics 구현과의 차이점을 알아보는 다른 리소스에 대한 링크가 나와 있습니다.

## <a name="getting-started"></a>시작

- [Azure Portal에서 Log Analytics 시작](get-started-portal.md)은 Azure Portal에서 쿼리를 작성하고 결과를 사용하기 위한 단원입니다.
-  [Log Analytics에서 쿼리 시작](get-started-queries.md)은 Log Analytics 데이터를 사용하여 쿼리를 작성하기 위한 단원입니다.

## <a name="concepts"></a>개념
- [Azure Monitor에서 Log Analytics 데이터 분석](../../azure-monitor/log-query/log-query-overview.md)은 로그 쿼리에 대한 간략한 개요를 제공하며 Log Analytics 데이터의 구조화 방식을 설명합니다.
- [Log Analytics에서 데이터 보기 및 분석](../../azure-monitor/log-query/portals.md)은 Log Analytics 쿼리를 만들고 실행하는 포털을 설명합니다.

## <a name="reference"></a>참고 자료

- [쿼리 언어 참조](/azure/kusto/query)는 Data Explorer 쿼리 언어에 대한 전체 언어 참조입니다.
- [Log Analytics 쿼리 언어 차이](data-explorer-difference.md)는 Data Explorer 쿼리 언어의 버전 간 차이점을 설명합니다.
- [Log Analytics 레코드의 표준 속성](../../azure-monitor/platform/log-standard-properties.md)은 모든 Log Analytics 데이터의 표준이 되는 속성을 설명합니다.
- [Log Analytics에서 리소스 간 로그 검색 수행](../../azure-monitor/log-query/cross-workspace-query.md)은 여러 Log Analytics 작업 영역 및 Application Insights 응용 프로그램에서 데이터를 사용하는 쿼리를 작성하는 방법을 설명합니다.


## <a name="examples"></a>예

- [Log Analytics 쿼리 예제](examples.md)는 Log Analytics 데이터를 사용한 예제 쿼리를 제공합니다.



## <a name="lessons"></a>단원

- [Log Analytics 쿼리에서 문자열 사용](string-operations.md)은 문자열 데이터로 작업하는 방법을 설명합니다.
- [Log Analytics 쿼리에서 날짜/시간 값 사용](datetime-operations.md)은 날짜 및 시간 데이터로 작업하는 방법을 설명합니다. 
- [Log Analytics 쿼리의 집계](aggregations.md) 및 [Log Analytics 쿼리에서 고급 집계](advanced-aggregations.md)는 데이터를 집계하고 요약하는 방법을 설명합니다.
- [Log Analytics 쿼리의 조인](joins.md)은 여러 테이블에서 데이터를 조인하는 방법을 설명합니다.
- [Log Analytics 쿼리에서 JSON 및 데이터 구조 사용](json-data-structures.md)은 json 데이터를 구문 분석하는 방법을 설명합니다.
- [Log Analytics에서 고급 쿼리 작성](advanced-query-writing.md)은 복잡한 쿼리를 만들고 코드를 다시 사용하기 위한 전략을 설명합니다.
- [Log Analytics 쿼리에서 차트 및 다이어그램 만들기](charts.md)는 쿼리에서 데이터를 시각화하는 방법을 설명합니다.

## <a name="cheatsheets"></a>Cheatsheets

-  [SQL-Log Analytics 쿼리 언어 참고 자료](sql-cheatsheet.md)는 이미 SQL에 익숙한 사용자를 지원합니다.
-  [Splunk-Log Analytics 쿼리 언어 참고 자료](sql-cheatsheet.md)는 이미 Splunk에 익숙한 사용자를 지원합니다.
 
## <a name="next-steps"></a>다음 단계

- [Data Explorer 쿼리 언어에 대한 전체 참조 설명서](/azure/kusto/query/)에 액세스합니다.