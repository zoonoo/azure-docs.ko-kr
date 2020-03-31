---
title: 아파치 사육사 서버는 Azure HDInsight에서 쿼럼을 형성하지 못합니다.
description: 아파치 사육사 서버는 Azure HDInsight에서 쿼럼을 형성하지 못합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250230"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>아파치 사육사 서버는 Azure HDInsight에서 쿼럼을 형성하지 못합니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 ZooKeeper 서버는 비정상, 증상포함 될 수 있습니다: 두 리소스 관리자 /이름 노드 대기 `zkFailoverController` 모드에, 간단한 HDFS 작업이 작동하지 않습니다, 중지 및 시작할 수 없습니다, 원사 / 스파크 / 리비 작업은 사육사 오류로 인해 실패. LLAP 데몬은 보안 스파크 또는 인터랙티브 하이브 클러스터에서 시작하지 못할 수도 있습니다. 다음과 유사한 오류 메시지가 표시될 수 있습니다.

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

사육사 서버에서 /var/log/zookeeper/zookeeper-zookeeper-server에서\*모든 사육사 호스트에 로그- .out, 당신은 또한 다음과 같은 오류가 표시 될 수 있습니다.

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>원인

스냅숏 파일의 볼륨이 크거나 스냅숏 파일이 손상된 경우 ZooKeeper 서버는 쿼럼을 구성하지 못하여 ZooKeeper 관련 서비스가 비정상입니다. ZooKeeper 서버는 데이터 디렉토리에서 이전 스냅 샷 파일을 제거하지 않습니다, 대신, 그것은 ZooKeeper의 건강을 유지하기 위해 사용자가 수행해야하는 정기적 인 작업입니다. 자세한 내용은 [사육사의 강점과 한계를](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)참조하십시오.

## <a name="resolution"></a>해결 방법

ZooKeeper 데이터 디렉토리를 `/hadoop/zookeeper/version-2` 확인하고 `/hadoop/hdinsight-zookeeper/version-2` 스냅샷 파일 크기가 큰지 확인합니다. 큰 스냅숏이 있는 경우 다음 단계를 수행합니다.

1. `/hadoop/zookeeper/version-2` 및 `/hadoop/hdinsight-zookeeper/version-2`에서 스냅샷을 백업합니다.

1. `/hadoop/zookeeper/version-2` 에서 스냅숏을 `/hadoop/hdinsight-zookeeper/version-2`정리합니다.

1. 아파치 암바리 UI에서 모든 사육사 서버를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

- 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

- 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
