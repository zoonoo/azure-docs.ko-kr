---
title: Azure HDInsight 클러스터에 노드를 추가할 수 없음
description: Azure HDInsight에서 Apache Hadoop 클러스터에 노드를 추가할 수 없는 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: b11d1edef2f3a6fa0fb39c76d1f25ec05ff15d07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944322"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>시나리오: Azure HDInsight 클러스터에 노드를 추가할 수 없음

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Azure HDInsight 클러스터에 노드를 추가할 수 없음

## <a name="cause"></a>원인

이유는 다를 수 있습니다.

## <a name="resolution"></a>해결 방법

[클러스터 크기](../hdinsight-scaling-best-practices.md) 기능을 사용하여, 클러스터에 필요한 추가 코어의 수를 계산합니다. 새 작업자 노드에 있는 총 코어 수를 기반으로 합니다. 그런 후 다음 단계 중 하나 이상을 수행해 봅니다.

* 클러스터의 위치에 사용 가능한 코어가 있는지 확인합니다.

* 다른 위치에서 사용 가능한 코어의 수를 살펴봅니다. 사용 가능한 코어가 충분히 있는 다른 위치에서 클러스터를 다시 만드는 것이 좋습니다.

* 특정 위치에 대한 코어 할당량을 늘리려면, HDInsight 코어 할당량 증가를 위한 지원 티켓을 제출하십시오.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]