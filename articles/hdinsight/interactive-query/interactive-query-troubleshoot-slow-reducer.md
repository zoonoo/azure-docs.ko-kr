---
title: Azure HDInsight에서 Reducer의 속도 저하
description: 가능한 데이터 기울이기로 인하여 Azure HDInsight에서 Reducer의 속도 저하
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: f9b6e0d862d17badb1caa672852214cdd86abb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930813"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Reducer의 속도 저하

이 문서에서는 Azure HDInsight 클러스터에서 Interactive Query 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

`insert into table1 partition(a,b) select a,b,c from table2`와 같은 쿼리를 실행할 때 쿼리 계획은 다양한 Reducer를 시작하지만 각 파티션의 데이터는 단일 Reducer로 이동합니다. 이로 인해 가장 큰 파티션의 Reducer에서 소요되는 시간만큼 쿼리가 느려집니다.

## <a name="cause"></a>원인

[Beeline](../hadoop/apache-hadoop-use-hive-beeline.md)을 열고 설정 값을 확인`hive.optimize.sort.dynamic.partition`합니다.

해당 변수의 값은 데이터의 특성에 따라 true/false로 설정됩니다.

입력 테이블의 파티션이 작고(가령 10보다 작은 경우) 출력 파티션의 수도 마찬가지로 작으며 변수가 `true`로 설정된 경우, 데이터는 파티션당 하나의 Reducer를 사용하여 전체적으로 정렬되고 작성됩니다. 사용 가능한 Reducer 수가 더 클 경우에도 데이터 기울이기로 인해 몇 개의 Reducer가 지연되어 최대 병렬 처리를 달성하지 못할 수 있습니다. `false`로 변경된 경우, 두 개 이상의 Reducer가 단일 파티션을 처리할 수 있고 여러 개의 작은 파일이 작성되어 삽입 속도가 빨라집니다. 다만, 이는 더 작은 파일의 존재 때문에 추가 쿼리에 영향을 줄 수 있습니다.

`true` 값은 파티션 수가 크고 데이터가 기울지 않은 경우에 적합합니다. 해당 경우에는 각 파티션이 단일 Reducer에 의해 처리되어 차후의 쿼리 성능이 향상되도록 맵 단계의 결과가 작성됩니다.

## <a name="resolution"></a>해결 방법

1. 여러 파티션으로 정규화되도록 데이터를 다시 분할합니다.

1. 첫 번째 방법을 사용할 수 없는 경우 beeline 세션에서 config 값을 false로 설정하고 쿼리를 다시 시도하세요. `set hive.optimize.sort.dynamic.partition=false`. 클러스터 수준에서 값을 false로 설정하는 것은 권장하지 않습니다. `true` 값은 최적이며 데이터 및 쿼리의 특성에 따라 매개 변수를 필수로 설정합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]