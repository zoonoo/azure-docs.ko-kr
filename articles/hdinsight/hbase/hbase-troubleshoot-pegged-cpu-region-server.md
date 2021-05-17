---
title: Apache HBase 클러스터의 CPU 고정 - Azure HDInsight
description: Azure HDInsight에서 Apache HBase 클러스터의 지역 서버에서 CPU 고정 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 7d0cf139f06bb296b486d2932d95b53fc1167a5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98937028"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache HBase 클러스터의 지역 서버에서 CPU 고정 문제 발생

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache HBase 지역 서버 프로세스가 200%에 가까운 CPU를 차지하기 시작하면서 HBase Master 프로세스 및 클러스터가 전체 용량에서 작동하지 못한다는 경고가 발생합니다.

## <a name="cause"></a>원인

HBase 클러스터 v3.4를 실행하는 경우 jdk를 버전 1.7.0_151로 업그레이드할 때 발생하는 잠재적 버그로 인한 문제일 수 있습니다. 나타나는 증상은 지역 서버 프로세스가 200%에 가까운 CPU를 차지하기 시작하는 것입니다. 이를 확인하기 위해 `top` 명령을 실행합니다. 200%에 가까운 CPU를 차지하는 프로세스가 있으면 해당 pid를 확인하고 `ps -aux | grep`를 실행하여 지역 서버 프로세스인지 알아봅니다.

## <a name="resolution"></a>해결 방법

1. 아래와 같이 클러스터의 모든 노드에 jdk 1.8을 설치합니다.

    * 스크립트 작업 `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`를 실행합니다. 모든 노드에서 실행할 옵션을 선택해야 합니다.

    * 또는 모든 개별 노드에 로그인하고 `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` 명령을 실행할 수 있습니다.

1. Ambari UI `https://<clusterdnsname>.azurehdinsight.net`로 이동합니다.

1. **HBase->구성->고급->고급** `hbase-env configs`로 이동한 후 변수 `JAVA_HOME`을 `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`로 변경합니다. 구성 변경 내용을 저장합니다.

1. [선택 사항이지만 권장됨] [클러스터의 모든 테이블을 플러시합니다](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. Ambari UI에서 다시 시작해야 하는 모든 HBase 서비스를 다시 시작합니다.

1. 클러스터의 데이터에 따라 클러스터가 안정적인 상태에 도달하는 데 몇 분 정도 걸릴 수 있습니다. 클러스터가 안정적인 상태에 도달하는지 확인하는 방법은 Ambari(최신)에서 HMaster UI를 확인하거나(모든 지역 서버는 활성 상태여야 함) 헤드 노드에서 HBase 셸을 실행한 후 status 명령을 실행하는 것입니다.

업그레이드에 성공했는지 확인하려면 적절한 java 버전을 사용하여 관련 HBase 프로세스가 시작되었는지 확인합니다. 예를 들어 지역 서버를 확인하려면 다음과 같이 입력합니다.

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]