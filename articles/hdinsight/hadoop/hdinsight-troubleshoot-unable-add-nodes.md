---
title: Azure HDInsight 클러스터에 노드를 추가할 수 없습니다.
description: Azure HDInsight에서 Apache Hadoop 클러스터에 노드를 추가할 수 없는 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: 0c8fdf11f435cc459dfb8475f8983b29dfbc5d9f
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810559"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>시나리오: Azure HDInsight 클러스터에 노드를 추가할 수 없습니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

Azure HDInsight 클러스터에 노드를 추가할 수 없습니다.

## <a name="cause"></a>원인

이유는 다를 수 있습니다.

## <a name="resolution"></a>해결 방법

[클러스터 크기](../hdinsight-scaling-best-practices.md) 기능을 사용 하 여 클러스터에 필요한 추가 코어 수를 계산 합니다. 새 작업자 노드에 있는 총 코어 수를 기반으로 합니다. 그런 후 다음 단계 중 하나 이상을 시도 합니다.

* 클러스터의 위치에서 사용할 수 있는 코어가 있는지 확인 하십시오.

* 다른 위치에서 사용 가능한 코어의 수를 살펴봅니다. 사용 가능한 코어가 충분히 있는 다른 위치에서 클러스터를 다시 만드는 것이 좋습니다.

* 특정 위치에 대한 코어 할당량을 늘리려면, HDInsight 코어 할당량 증가를 위한 지원 티켓을 제출하십시오.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
