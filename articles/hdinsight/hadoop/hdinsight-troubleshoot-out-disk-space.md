---
title: Azure HDInsight에서 클러스터 노드가 디스크 공간이 부족합니다.
description: Azure HDInsight에서 아파치 하두롭 클러스터 노드 디스크 공간 문제 해결.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894120"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 클러스터 노드의 디스크 공간이 부족합니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

다음과 유사한 오류 메시지로 작업이 실패할 수 있습니다.`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

또는 다음과 유사한 아파치 암바리 경고를 `local-dirs usable space is below configured utilization percentage`받을 수 있습니다.

## <a name="cause"></a>원인

아파치 원사 응용 프로그램 캐시는 사용 가능한 모든 디스크 공간을 사용했을 수 있습니다. Spark 응용 프로그램이 비효율적으로 실행되고 있는 것 일 수 있습니다.

## <a name="resolution"></a>해결 방법

1. Ambari UI를 사용하여 디스크 공간이 부족한 노드를 확인합니다.

1. 문제가 되는 노드의 폴더가 대부분의 디스크 공간에 기여하는지 확인합니다. 먼저 노드에 SSH를 `df` 실행한 다음 모든 마운트에 대한 디스크 사용량을 나열하도록 실행합니다. 일반적으로 OSS에서 `/mnt` 사용하는 임시 디스크입니다. 폴더에 입력한 다음 입력하여 `sudo du -hs` 폴더 아래에 요약된 파일 크기를 표시할 수 있습니다. `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`과 유사한 폴더가 표시되면 응용 프로그램이 계속 실행 중임을 의미합니다. 이는 RDD 지속성 또는 중간 셔플 파일 때문일 수 있습니다.

1. 문제를 완화하려면 해당 응용 프로그램에서 사용하는 디스크 공간을 해제하는 응용 프로그램을 죽입니다.

1. 궁극적으로 문제를 해결하려면 응용 프로그램을 최적화합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
