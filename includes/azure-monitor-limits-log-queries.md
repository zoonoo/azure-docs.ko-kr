---
title: 포함 파일
description: 포함 파일
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f007cf0d46d6cbee39a950b9784bbc9bde702ff5
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "69657822"
---
| 제한 | Description |
|:---|:---|
| 쿼리 언어 | Azure Monitor는 Azure 데이터 탐색기와 동일한 [Kusto 쿼리 언어](/azure/kusto/query/) 를 사용 합니다. Azure Monitor에서 지원 되지 않는 KQL 언어 요소에 대 한 [Azure Monitor 로그 쿼리 언어 차이점](../articles/azure-monitor/log-query/data-explorer-difference.md) 을 참조 하세요. |
| Azure 지역 | 로그가 여러 Azure 지역에서 Log Analytics 작업 영역에 걸쳐 있을 때 과도 한 오버 헤드가 발생할 수 있습니다. 자세한 내용은 [쿼리 제한](../articles/azure-monitor/log-query/scope.md#query-limits) 을 참조 하세요. |
| 리소스 간 쿼리 | 100으로 제한 된 단일 쿼리에서 Application Insights 리소스 및 Log Analytics 작업 영역의 최대 수입니다.<br>뷰 디자이너에서는 리소스 간 쿼리가 지원 되지 않습니다.<br>로그 경고의 리소스 간 쿼리는 새 scheduledQueryRules API에서 지원 됩니다.<br>자세한 내용은 [리소스 간 쿼리 제한](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) 을 참조 하세요. |