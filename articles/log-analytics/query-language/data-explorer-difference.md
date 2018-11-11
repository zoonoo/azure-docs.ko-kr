---
title: Azure Monitor Log Analytics 언어 참조 | Microsoft Docs
description: Log Analytics에서 사용되는 Data Explorer 쿼리 언어에 대한 참조 정보입니다. Log Analytics와 관련된 추가 요소와 Log Analytics 쿼리에서 지원되지 않는 요소가 포함됩니다.
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
ms.date: 10/29/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: e6d097749dae49cf6f1d710bcf01cf99dcd98a4c
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243898"
---
# <a name="log-analytics-query-language-differences"></a>Log Analytics 쿼리 언어 차이점

[Log Analytics](../log-analytics-queries.md)는 [Azure Data Explorer](/azure//data-explorer)에서 빌드되고 [동일한 쿼리 언어](/azure/kusto/query)를 사용하는 반면, 언어의 버전에는 몇 가지 차이점이 있습니다. 이 문서에서는 Log Analytics 쿼리에 사용되는 버전과 Data Explorer에 사용되는 언어의 버전 간에 다른 요소를 식별합니다.

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Data Explorer 요소는 Log Analytics에서 지원되지 않습니다.
다음 섹션에서는 Log Analytics에서 지원되지 않는 Data Explorer 쿼리 언어의 요소를 설명합니다.

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
다음 연산자는 특정 Log Analytics 기능을 지원하며 Log Analytics 외에는 사용할 수 없습니다.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>다음 단계

<<<<<<< HEAD:articles/log-analytics/query-language/data-explorer-difference.md
- [Log Analytics 쿼리 작성을 위한 다른 리소스](kusto.md)에 대한 참조를 가져옵니다.
- [Data Explorer 쿼리 언어에 대한 전체 참조 설명서](/azure/kusto/query/)에 액세스합니다.
=======
- [Log Analytics](../log-analytics-queries.md)의 쿼리에 대해 알아봅니다.
- [Log Analytics 쿼리](/log-analytics/query-language/get-started-queries.md) 작성에 대한 단원을 살펴봅니다.
- [Kusto에 대한 전체 참조 설명서](/azure/kusto/query/)에 액세스합니다.
>>>>>>> 4bccab5ecb17c887658a4d2ed1bab6b22bf29ffd:articles/log-analytics/query-language/kusto.md
