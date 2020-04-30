---
title: Apache ZooKeeper 서버가 Azure HDInsight에서 쿼럼을 형성 하지 못함
description: Apache ZooKeeper 서버가 Azure HDInsight에서 쿼럼을 형성 하지 못함
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 41ac109e5c5379e6085dd57a3fcd8119915558fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133283"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper 서버가 Azure HDInsight에서 쿼럼을 형성 하지 못함

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache ZooKeeper 서버가 비정상으로 나타날 수 있습니다 .이는 리소스 관리자/이름 노드가 모두 대기 모드이 고, 단순 HDFS 작업이 작동 하지 않거나 `zkFailoverController` , 중지 되 고, 시작할 수 없는 경우, Yarn/Spark/Livy 작업이 사육 아웃 오류로 인해 실패 합니다. LLAP 디먼 보안 Spark 또는 대화형 Hive 클러스터에서 시작 하지 못할 수도 있습니다. 다음과 유사한 오류 메시지가 표시 될 수 있습니다.

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

/Var/log/zookeeper/zookeeper-zookeeper-server-\*의 모든 사육 아웃 호스트에 있는 사육 아웃 서버 로그에서 다음 오류가 표시 될 수도 있습니다.

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>원인

스냅숏 파일의 볼륨이 크거나 스냅숏 파일이 손상 된 경우에는 사육 아웃 서버에서 쿼럼을 형성 하지 못하여,이로 인해 사육 사가 관련 서비스가 비정상 상태가 됩니다. 사육 아웃 서버는 데이터 디렉터리에서 이전 스냅숏 파일을 제거 하지 않고, 사용자가 온전성의 사육를 유지 관리 하기 위해 수행 해야 하는 정기적인 작업입니다. 자세한 내용은 [사육 사 장점 및 제한 사항](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)을 참조 하세요.

## <a name="resolution"></a>해결 방법

사육 사 데이터 디렉터리 `/hadoop/zookeeper/version-2` 를 `/hadoop/hdinsight-zookeeper/version-2` 확인 하 고 스냅숏 파일 크기가 큰지 확인 합니다. 규모가 많은 스냅숏이 있는 경우 다음 단계를 수행 합니다.

1. 동일한 쿼럼에 있는 다른 사육 아웃 서버 서버의 상태를 확인 하 여 "`echo stat | nc {zk_host_ip} 2181 (or 2182)`" 명령으로 정상적으로 작동 하는지 확인 합니다.  

1. 및 `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2`에서 문제가 있는 사육 사 호스트, 백업 스냅숏 및 트랜잭션 로그에 로그인 한 후 두 디렉터리에서 이러한 파일을 정리 합니다. 

1. Ambari 또는 사육 사 호스트에서 문제가 있는 사육 사 서버를 다시 시작 합니다. 그런 다음 문제가 있는 서비스를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

- 을 사용 [@AzureSupport](https://twitter.com/azuresupport) 하 여 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

- 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
