---
title: Apache HBase 클러스터의 해석 CPU-Azure HDInsight
description: Azure HDInsight에서 Apache HBase 클러스터의 지역 서버에서 CPU 해석 문제 해결
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: ab9aa6d7be83faa0c26951b2c45092bbcb063d79
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540213"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache HBase 클러스터의 지역 서버에서 CPU 해석

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache HBase 지역 서버 프로세스가 200%의 CPU에 가까운 시간 동안 발생 하 여 경고를 HBase Master 프로세스 및 클러스터에서 실행 하 여 전체 용량에서 작동 하지 않습니다.

## <a name="cause"></a>원인

HBase 클러스터 v 3.4를 실행 하는 경우 jdk를 버전 1.7.0 _151로 업그레이드 하 여 발생 하는 잠재적 버그로 인해 적중 했을 수 있습니다. 표시 되는 증상은 200% CPU에 가까운 위치에서 시작 되는 것을 확인 하는 것입니다 .이는 명령을 실행 하는 것을 확인 하기 위한 것입니다 `top` . 200% cpu에 근접 하는 프로세스가 있는 경우 해당 pid를 받고를 실행 하 여 지역 서버 프로세스 인지 확인 `ps -aux | grep` 합니다.

## <a name="resolution"></a>해결 방법

1. 아래와 같이 클러스터의 모든 노드에 jdk 1.8을 설치 합니다.

    * 스크립트 동작을 실행 `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` 합니다. 모든 노드에서 실행 하는 옵션을 선택 해야 합니다.

    * 또는 모든 개별 노드에 로그인 하 고 명령을 실행할 수 있습니다 `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` .

1. Ambari UI-로 이동 `https://<clusterdnsname>.azurehdinsight.net` 합니다.

1. **HBase->Configs->고급->고급** 으로 이동 하 `hbase-env configs` 여 변수를 `JAVA_HOME` 로 변경 `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` 합니다. 구성 변경 내용을 저장 합니다.

1. [선택 사항 이지만 권장 됨] [클러스터의 모든 테이블을 플러시합니다](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. Ambari UI에서 다시 시작 해야 하는 모든 HBase 서비스를 다시 시작 합니다.

1. 클러스터의 데이터에 따라 클러스터가 안정적인 상태에 도달 하는 데 몇 분 정도 걸릴 수 있습니다. 클러스터가 안정적인 상태에 도달 하는 것을 확인 하는 방법은 Ambari (새로 고침)에서 HMaster UI (모든 지역 서버는 활성 상태 여야 함)를 선택 하거나 헤드 노드에서 HBase 셸 실행 후 status 명령을 실행 하는 것입니다.

업그레이드에 성공 했는지 확인 하려면 적절 한 java 버전을 사용 하 여 관련 HBase 프로세스가 시작 되었는지 확인 합니다. 예를 들어 다음과 같이 지역 서버를 확인 합니다.

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.