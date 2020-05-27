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
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74796034"
---
| 제한 | 설명 |
|:---|:---|
| 쿼리 언어 | Azure 모니터는 Azure 데이터 탐색기와 동일한 [Kusto 쿼리 언어를](/azure/kusto/query/) 사용합니다. Azure Monitor에서 지원되지 않는 KQL 언어 요소에 대한 Azure [모니터 로그 쿼리](../articles/azure-monitor/log-query/data-explorer-difference.md) 언어 차이를 참조하십시오. |
| Azure 지역 | 데이터가 여러 Azure 리전에서 로그 분석 작업 영역에 걸쳐 있을 때 로그 쿼리는 과도한 오버헤드를 경험할 수 있습니다. 자세한 내용은 [쿼리 제한을](../articles/azure-monitor/log-query/scope.md#query-limits) 참조하십시오. |
| 리소스 쿼리 교차 | 단일 쿼리에서 최대 애플리케이션 인사이트 리소스 및 로그 분석 작업 영역의 수는 100개로 제한됩니다.<br>리소스 간 쿼리는 보기 디자이너에서 지원되지 않습니다.<br>로그 경고의 리소스 간 쿼리는 새 scheduledQueryRules API에서 지원됩니다.<br>자세한 내용은 [리소스 간 쿼리 제한을](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) 참조하십시오. |
| 쿼리 제한 | 사용자는 작업 영역 수에 관계없이 30초당 200개의 쿼리로 제한됩니다. 이 제한은 프로그래밍 방식 쿼리 또는 Azure 대시보드 및 Log Analytics 작업 영역 요약 페이지와 같은 시각화 부분에서 시작된 쿼리에 적용됩니다. |
