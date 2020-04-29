---
title: Azure Monitor 로그 쿼리 언어 차이점 | Microsoft Docs
description: Azure Monitor에서 사용되는 Kusto 쿼리 언어에 대한 참조 정보입니다. Azure Monitor와 관련된 추가 요소와 Azure Monitor 로그 쿼리에 지원되지 않는 요소가 포함됩니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: 265179909c8ae4a6fa630b835bc9993f042d6460
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585705"
---
# <a name="azure-monitor-log-query-language-differences"></a>Azure Monitor 로그 쿼리 언어 차이점

[Azure Monitor 로그](log-query-overview.md)는 [Azure Data Explorer](/azure/data-explorer)를 토대로 빌드되고 동일한 [Kusto 쿼리 언어](/azure/kusto/query)를 사용하는 반면, 언어 버전에 몇 가지 차이점이 있습니다. 이 문서에서는 Azure Monitor 로그 쿼리에 사용되는 버전과 데이터 탐색기에 사용되는 언어의 버전 간에 다른 요소를 식별합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 KQL 요소
다음 섹션에서는 Azure Monitor에서 지원되지 않는 Kusto 쿼리 언어의 요소를 설명합니다.

### <a name="statements-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 명령문

* [앤티앨리어스](/azure/kusto/query/aliasstatement)
* [쿼리 매개 변수](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 함수

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [데이터베이스 ()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 연산자

* [클러스터 간 조인](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 플러그 인

* [Python 플러그 인](/azure/kusto/query/pythonplugin)
* [sql_request 플러그 인](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Azure Monitor의 추가 연산자
다음 연산자는 특정 Azure Monitor 기능을 지원하며 Azure Monitor 외에는 사용할 수 없습니다.

* [앱 ()](app-expression.md)
* [작업 영역 ()](workspace-expression.md)

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 로그 쿼리 작성을 위한 다른 리소스](query-language.md)에 대한 참조를 가져옵니다.
- [Kusto 쿼리 언어에 대한 전체 참조 문서](/azure/kusto/query/)에 액세스합니다.
