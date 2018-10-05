---
title: Azure Monitor Log Analytics 언어 참조 | Microsoft Docs
description: Log Analytics에서 사용되는 Kusto 언어에 대한 참조 정보입니다. Log Analytics와 관련된 추가 요소와 Log Analytics 쿼리에서 지원되지 않는 요소가 포함됩니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 5173790436a29fa9947346d711da1a2ddb32bf62
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451071"
---
# <a name="log-analytics-query-language-reference"></a>Log Analytics 쿼리 언어 참조
[Log Analytics 쿼리](../log-analytics-queries.md)는 [Azure 데이터 탐색기](/azure/data-explorer/)에서 사용된 것과 동일한 쿼리 언어와 엔진을 사용합니다. [Kusto 언어 참조](/azure/kusto/query) 위치에서 언어에 대한 언어 참조 및 기타 세부 정보에 액세스할 수 있습니다.



## <a name="kusto-elements-not-support-in-log-analytics"></a>Log Analytics에서 지원되지 않는 Kusto 요소
Log Analytics 쿼리는 Kusto의 구현을 사용하지만 다음 섹션에서 설명하는 대로 지원되지 않는 일부 Kusto 요소가 있습니다.

### <a name="statements-not-supported-in-log-analytics"></a>Log Analytics에서 지원되지 않는 명령문

* [별칭](/kusto/query/aliasstatement)
* [쿼리 매개 변수](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-log-analytics"></a>Log Analytics에서 지원되지 않는 함수

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-log-analytics"></a>Log Analytics에서 지원되지 않는 연산자

* [클러스터 간 조인](/azure/kusto/query/joincrosscluster)
* [externaldata 연산자](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-log-analytics"></a>Log Analytics에서 지원되지 않는 플러그 인

* [sql_request 플러그 인](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-log-analytics"></a>Log Analytics의 추가 연산자
특정 Log Analytics 기능을 지원하기 위해 Log Analytics 외부에서 사용할 수 없는 다음 Kusto 연산자가 추가로 제공됩니다. 

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>다음 단계

- [Log Analytics](../log-analytics-queries.md)의 쿼리에 대해 알아봅니다.
- [Log Analytics 쿼리](/log-analytics/query-language/get-started-queries.md) 작성에 대한 단원을 살펴봅니다.
- [Kusto에 대한 전체 참조 설명서](/azure/kusto/query/)에 액세스합니다.