---
title: Azure HDInsight에서 클러스터 노드의 디스크 공간이 부족 합니다.
description: Azure HDInsight의 클러스터 노드 디스크 공간 문제 Apache Hadoop 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: a9d82e0465e555d4959e549e04565399d423c1ee
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087357"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 클러스터 노드의 디스크 공간이 부족 합니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

다음과 유사한 오류 메시지와 함께 작업이 실패할 수 있습니다.`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

또는와 유사한 Apache Ambari 경고를 `local-dirs usable space is below configured utilization percentage`받을 수 있습니다.

## <a name="cause"></a>원인

Apache Yarn 응용 프로그램 캐시가 사용 가능한 모든 디스크 공간을 사용 했을 수 있습니다. Spark 응용 프로그램이 비효율적으로 실행 될 가능성이 높습니다.

## <a name="resolution"></a>해결 방법

1. Ambari UI를 사용 하 여 디스크 공간이 부족 하 게 되는 노드를 확인 합니다.

1. 문제가 노드의 모든 디스크 공간에 적용 되는 폴더를 확인 합니다. 먼저 노드에 대 한 SSH를 실행 한 `df` 다음를 실행 하 여 모든 탑재에 대 한 디스크 사용량을 나열 합니다. `/mnt` 일반적으로이는 OSS에서 사용 되는 임시 디스크입니다. 폴더에를 입력 한 다음를 입력 `sudo du -hs` 하 여 폴더 아래에 요약 된 파일 크기를 표시할 수 있습니다. 와 유사한 `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`폴더가 표시 되는 경우이는 응용 프로그램이 여전히 실행 되 고 있음을 의미 합니다. RDD 지 속성 또는 중간 순서 섞기 파일이 원인일 수 있습니다.

1. 이 문제를 완화 하기 위해 응용 프로그램에서 사용 하는 디스크 공간을 해제 하는 응용 프로그램을 중지 합니다.

1. 궁극적으로 문제를 해결 하려면 응용 프로그램을 최적화 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
