---
title: Azure HDInsight에서 리 듀 서 느림
description: 리 듀 서는 Azure HDInsight에서 가능한 데이터 기울이기의 속도가 느립니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 74e5214b304870b48e6c6f3ec34b7a5bae0e389a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288857"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 리 듀 서가 느림

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

쿼리 계획을 실행할 때 쿼리 `insert into table1 partition(a,b) select a,b,c from table2` 계획은 다양 한 리 듀 서을 시작 하지만 각 파티션의 데이터는 단일 리 듀 서로 이동 합니다. 이로 인해 가장 큰 파티션의 리 듀 서에서 수행 되는 시간 만큼 쿼리가 느려집니다.

## <a name="cause"></a>원인

[Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) 를 열고 set 값을 확인 `hive.optimize.sort.dynamic.partition` 합니다.

이 변수의 값은 데이터의 특성에 따라 true/false로 설정 됩니다.

입력 테이블의 파티션이 작은 경우 (10 보다 작은 경우) 출력 파티션 수와 변수를로 설정 하면 `true` 데이터를 전역적으로 정렬 하 고 파티션당 단일 리 듀 서를 사용 하 여 씁니다. 사용 가능한 리 듀 서 수가 더 클 경우에도 몇 가지 리 듀 서 데이터 기울이기로 인해 지연 수 있으며 최대 병렬 처리를 달성할 수 없습니다. 로 변경 된 경우 `false` 두 개 이상의 리 듀 서 단일 파티션을 처리할 수 있고 여러 개의 작은 파일이 기록 되어 삽입 속도가 빨라집니다. 이는 더 작은 파일의 존재 때문에 추가 쿼리에 영향을 줄 수 있습니다.

값은 `true` 파티션 수가 크고 데이터가 왜곡 되지 않는 경우에 적합 합니다. 이러한 경우에는 각 파티션이 단일 리 듀 서 처리 되어 이후의 쿼리 성능이 향상 되도록 맵 단계의 결과가 작성 됩니다.

## <a name="resolution"></a>해결 방법

1. 여러 파티션으로 정규화 되도록 데이터를 다시 분할 합니다.

1. #1를 사용할 수 없는 경우 beeline 세션에서 config 값을 false로 설정 하 고 쿼리를 다시 시도 하십시오. `set hive.optimize.sort.dynamic.partition=false`. 클러스터 수준에서 값을 false로 설정 하는 것은 권장 되지 않습니다. 값은 `true` 최적 이며 데이터 및 쿼리의 특성에 따라 필요에 따라 매개 변수를 설정 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]