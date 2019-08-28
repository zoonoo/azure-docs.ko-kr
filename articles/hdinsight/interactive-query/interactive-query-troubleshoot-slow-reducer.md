---
title: Azure HDInsight에서 리 듀 서 느림
description: 리 듀 서는 Azure HDInsight에서 가능한 데이터 기울이기의 속도가 느립니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5fe1b2c720bbc55e8e245f9592755e046e0b04a3
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968437"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 리 듀 서 느림

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

쿼리 계획을 실행할 `insert into table1 partition(a,b) select a,b,c from table2` 때 쿼리 계획은 다양 한 리 듀 서을 시작 하지만 각 파티션의 데이터는 단일 리 듀 서로 이동 합니다. 이로 인해 가장 큰 파티션의 리 듀 서에서 수행 되는 시간 만큼 쿼리가 느려집니다.

## <a name="cause"></a>원인

[Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) 를 열고 set `hive.optimize.sort.dynamic.partition`값을 확인 합니다.

이 변수의 값은 데이터의 특성에 따라 true/false로 설정 됩니다.

입력 테이블의 파티션이 작은 경우 (10 보다 작은 경우) 출력 파티션 수와 변수를로 `true`설정 하면 데이터를 전역적으로 정렬 하 고 파티션당 단일 리 듀 서를 사용 하 여 씁니다. 사용 가능한 리 듀 서 수가 더 클 경우에도 몇 가지 리 듀 서 데이터 기울이기로 인해 지연 수 있으며 최대 병렬 처리를 달성할 수 없습니다. 로 `false`변경 된 경우 두 개 이상의 리 듀 서 단일 파티션을 처리할 수 있고 여러 개의 작은 파일이 기록 되어 삽입 속도가 빨라집니다. 이는 더 작은 파일의 존재 때문에 추가 쿼리에 영향을 줄 수 있습니다.

값 `true` 은 파티션 수가 크고 데이터가 왜곡 되지 않는 경우에 적합 합니다. 이러한 경우에는 각 파티션이 단일 리 듀 서 처리 되어 이후의 쿼리 성능이 향상 되도록 맵 단계의 결과가 작성 됩니다.

## <a name="resolution"></a>해결 방법

1. 여러 파티션으로 정규화 되도록 데이터를 다시 분할 합니다.

1. #1를 사용할 수 없는 경우 beeline 세션에서 config 값을 false로 설정 하 고 쿼리를 다시 시도 하십시오. `set hive.optimize.sort.dynamic.partition=false`. 클러스터 수준에서 값을 false로 설정 하는 것은 권장 되지 않습니다. 값 `true` 은 최적 이며 데이터 및 쿼리의 특성에 따라 필요에 따라 매개 변수를 설정 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
