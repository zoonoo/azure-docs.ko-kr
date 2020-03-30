---
title: 저감기는 Azure HDInsight에서 느립니다.
description: Azure HDInsight에서 가능한 데이터 왜곡으로 인한 감속기 속도가 느립니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895155"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 감속기 속도가 느립니다.

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

쿼리 계획과 같은 `insert into table1 partition(a,b) select a,b,c from table2` 쿼리를 실행할 때 감속기의 무리를 시작 하지만 각 파티션에서 데이터는 단일 감속기로 이동 합니다. 이렇게 하면 가장 큰 파티션의 감속기에서 보내는 시간만큼 쿼리 속도가 느려집니다.

## <a name="cause"></a>원인

[비라인을](../hadoop/apache-hadoop-use-hive-beeline.md) 열고 세트값을 `hive.optimize.sort.dynamic.partition`확인합니다.

이 변수의 값은 데이터의 특성에 따라 true/false로 설정됩니다.

입력 테이블의 파티션이 10개 미만이고 출력 파티션 수가 작으면 변수가 `true`설정되면 파티션당 단일 감속기를 사용하여 데이터가 전역적으로 정렬되고 기록됩니다. 사용 가능한 감속기 수가 더 많더라도 데이터 스큐로 인해 몇 개의 감속기가 뒤쳐질 수 있으며 최대 병렬 처리는 달성할 수 없습니다. `false`로 변경하면 하나 이상의 감속기에서 단일 파티션을 처리할 수 있으며 여러 개의 작은 파일이 기록되어 삽입 속도가 빨라집니다. 이 때문에 작은 파일의 존재의 추가 쿼리에 영향을 미칠 수 있습니다.

파티션 `true` 수가 크고 데이터가 왜곡되지 않는 경우 값이 의미가 있습니다. 이러한 경우 맵 단계의 결과가 기록되어 각 파티션이 단일 감속기에 의해 처리되어 후속 쿼리 성능이 향상됩니다.

## <a name="resolution"></a>해결 방법

1. 데이터를 다시 분할하여 여러 파티션으로 정규화해 보십시오.

1. #1 수없는 경우 beeline 세션에서 구성값값을 false로 설정하고 쿼리를 다시 시도하십시오. `set hive.optimize.sort.dynamic.partition=false`. 클러스터 수준에서 값을 false로 설정하는 것은 권장되지 않습니다. `true` 값은 최적이고 데이터 및 쿼리의 특성에 따라 필요에 따라 매개 변수를 설정합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
