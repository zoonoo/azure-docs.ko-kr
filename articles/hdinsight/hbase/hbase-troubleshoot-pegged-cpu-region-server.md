---
title: 아파치 HBase 클러스터에서 페깅 CPU - Azure HDInsight
description: Azure HDInsight의 아파치 HBase 클러스터의 지역 서버에서 페깅 CPU 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887311"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 아파치 HBase 클러스터의 리전 서버에서 페깅 CPU

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 HBase 지역 서버 프로세스는 200% CPU에 가깝게 점유하기 시작하여 HBase 마스터 프로세스 및 클러스터에서 최대 용량으로 작동하지 않는 경고가 발생합니다.

## <a name="cause"></a>원인

HBase 클러스터 v3.4를 실행 하는 경우 jdk 버전 1.7.0_151로 업그레이드로 인해 발생할 수 있는 버그에 의해 맞았을 수 있습니다. 우리가 볼 수있는 증상은 지역 서버 프로세스가 200 % CPU에 `top` 가까운 점유 시작입니다 (이 명령을 실행 확인하려면; 200 % CPU에 가까운 `ps -aux | grep` 점유 프로세스가있는 경우 pid를 얻고 실행하여 지역 서버 프로세스인지 확인).

## <a name="resolution"></a>해결 방법

1. 아래와 같이 클러스터의 모든 노드에 jdk 1.8을 설치하십시오.

    * 스크립트 작업을 `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`실행합니다. 모든 노드에서 실행할 옵션을 선택해야 합니다.

    * 또는 모든 개별 노드에 로그인하여 명령을 `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`실행할 수 있습니다.

1. Ambari UI로 `https://<clusterdnsname>.azurehdinsight.net`이동 - .

1. **HBase->구성->고급 >** `hbase-env configs` 고급으로 이동 하 `JAVA_HOME` `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`고 변수를 변경 합니다. 구성 변경 을 저장합니다.

1. 【선택 사항이지만 추천】 [클러스터의 모든 테이블을 플러시합니다.](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)

1. Ambari UI에서 다시 시작해야 하는 모든 HBase 서비스를 다시 시작합니다.

1. 클러스터의 데이터에 따라 클러스터가 안정적인 상태에 도달하는 데 몇 분에서 최대 1시간이 걸릴 수 있습니다. 클러스터가 안정 상태에 도달하는지 확인하는 방법은 Ambari(새로 고침) 또는 헤드노드 실행 HBase 셸에서 HMaster UI(모든 지역 서버가 활성화되어 있어야 함)를 확인한 다음 상태 명령을 실행하는 것입니다.

업그레이드가 성공했는지 확인하려면 관련 HBase 프로세스가 적절한 java 버전(예: 지역 서버 검사)을 사용하여 시작되었는지 확인합니다.

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
