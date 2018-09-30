---
title: Azure Resource Graph 쿼리 언어 이해
description: Azure Resource Graph 쿼리 언어의 작동 원리를 설명합니다.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 3600fc47a0fb318a49c1b37722cb7fffa51ec6f2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951953"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Azure Resource Graph 쿼리 언어 이해

Azure Resource Graph 쿼리 언어는 다양한 연산자 및 함수를 지원합니다. 각각은 KQL(Kusto Query Language)과 비슷한 장식으로 작동합니다. 하지만 Resource Graph의 쿼리 언어가 [KQL](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators)과 비슷하지만 동일하지는 않다는 것을 이해하는 것이 중요합니다.

> [!NOTE]
> KQL 설명서 링크를 사용하려면 인증이 필요할 수 있습니다.

Resource Graph에 사용되는 쿼리 언어에 대해 배우는 가장 좋은 방법은 언어의 구조 및 지원되는 다양한 연산자와 함수의 작동 원리를 설명하는 KQL 설명서를 시작하는 것입니다.

## <a name="supported-tabular-operators"></a>지원되는 테이블 형식 연산자

다음은 Resource Graph에서 지원되는 테이블 형식 연산자 목록입니다.

- [distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator)
- [extend](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator)
- [limit](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/limit-operator)
- [order by](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/order-operator)
- [project](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator)
- [project-away](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-away-operator)
- [sample](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-operator)
- [sample-distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-distinct-operator)
- [sort by](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
- [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator)
- [take](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
- [top](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator)
- [top-nested](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-nested-operator)
- [top-hitters](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-hitters-operator)
- [where](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

## <a name="supported-functions"></a>지원되는 함수

다음은 Resource Graph에서 지원되는 함수 목록입니다.

- [ago()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago%28%29)
- [buldschema()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/buildschema%28%29)
- [count()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/count%28%29)
- [strcat()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/strcat%28%29)
- [isnotempty()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/isnotempty%28%29_-notempty%28%29)
- [tostring()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/tostring%28%29)

## <a name="next-steps"></a>다음 단계

- [시작 쿼리](../samples/starter.md)에 사용되는 언어 알아보기
- [고급 쿼리](../samples/advanced.md)의 고급 사용법 알아보기
- [리소스 탐색](explore-resources.md)에 대해 알아보기