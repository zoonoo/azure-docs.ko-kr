---
title: Azure HDInsight 클러스터에 노드를 추가할 수 없습니다.
description: Azure HDInsight에서 Apache Hadoop 클러스터에 노드를 추가할 수 없는 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 089eab563c83384bdb7e1681044fa89f5eb7f6c0
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289056"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>시나리오: Azure HDInsight 클러스터에 노드를 추가할 수 없음

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Azure HDInsight 클러스터에 노드를 추가할 수 없습니다.

## <a name="cause"></a>원인

이유는 다를 수 있습니다.

## <a name="resolution"></a>해결 방법

[클러스터 크기](../hdinsight-scaling-best-practices.md) 기능을 사용 하 여 클러스터에 필요한 추가 코어 수를 계산 합니다. 새 작업자 노드에 있는 총 코어 수를 기반으로 합니다. 그런 후 다음 단계 중 하나 이상을 시도 합니다.

* 클러스터의 위치에서 사용할 수 있는 코어가 있는지 확인 하십시오.

* 다른 위치에서 사용 가능한 코어의 수를 살펴봅니다. 사용 가능한 코어가 충분히 있는 다른 위치에서 클러스터를 다시 만드는 것이 좋습니다.

* 특정 위치에 대한 코어 할당량을 늘리려면, HDInsight 코어 할당량 증가를 위한 지원 티켓을 제출하십시오.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]