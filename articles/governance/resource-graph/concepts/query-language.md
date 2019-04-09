---
title: 쿼리 언어 이해
description: 사용 가능한 Kusto 연산자 및 Azure 리소스 그래프를 사용 하 여 사용 가능한 함수를 설명 합니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 08e4f09665a3501073f55b7f5b82bf51cf508ea9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276680"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Azure Resource Graph 쿼리 언어 이해

Azure Resource Graph 쿼리 언어는 다양한 연산자 및 함수를 지원합니다. 각각 [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md)를 기반으로 작동합니다.

Resource Graph에서 사용하는 쿼리 언어를 알아보는 가장 좋은 방법은 Azure Data Explorer [쿼리 언어](/azure/kusto/query/index)의 설명서로 시작하는 것입니다. 이 설명서에서는 언어의 구조 및 지원되는 다양한 연산자와 함수의 작동 원리를 설명합니다.

## <a name="supported-tabular-operators"></a>지원되는 테이블 형식 연산자

다음은 Resource Graph에서 지원되는 테이블 형식 연산자 목록입니다.

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [기준으로 정렬](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [샘플](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [정렬 기준](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [top](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [상위 hitters](/azure/kusto/query/tophittersoperator)
- [여기서,](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>지원되는 함수

다음은 Resource Graph에서 지원되는 함수 목록입니다.

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="next-steps"></a>다음 단계

- [시작 쿼리](../samples/starter.md)에 사용되는 언어 알아보기
- [고급 쿼리](../samples/advanced.md)의 고급 사용법 알아보기
- [리소스 탐색](explore-resources.md)에 대해 알아보기