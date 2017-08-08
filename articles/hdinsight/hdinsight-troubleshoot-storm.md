---
title: "Storm 문제 해결 - Azure HDInsight | Microsoft Docs"
description: "Storm FAQ를 사용하여 Azure HDInsight 플랫폼의 Storm 대한 질문과 대답을 알아봅니다."
keywords: "Azure HDInsight, Storm, FAQ, 문제 해결 가이드, 일반적인 문제"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b52462096ce977b94ff9514df650607b05e17030
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="storm-troubleshooting"></a>Storm 문제 해결

이 문서에서는 Apache Ambari에서 Storm 페이로드를 사용할 때의 주요 문제 및 해결 방법에 대해 설명합니다.

## <a name="how-do-i-access-storm-ui-on-a-cluster"></a>클러스터에서 Storm UI에 액세스하는 방법

### <a name="issue"></a>문제:
브라우저에서 Storm UI에 액세스하는 두 가지 방법은 다음과 같습니다.

#### <a name="ambari-ui"></a>Ambari UI
1. Ambari 대시보드로 이동합니다.
1. 왼쪽의 서비스 목록에서 Storm을 선택합니다.
1. [빠른 연결] 드롭다운 메뉴에서 Storm UI 옵션을 선택합니다.

#### <a name="direct-link"></a>직접 링크
Storm UI는 다음 URL에서 액세스할 수 있습니다.

https://\<ClusterDnsName\>/stormui

예: https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another"></a>한 토폴로지에서 다른 토폴로지로 Storm EventHub Spout 검사점 정보를 보내는 방법

### <a name="issue"></a>문제:
HDInsight의 Storm EventHub Spout jar를 사용하여 이벤트 허브에서 읽는 토폴로지를 개발할 때, 이전 클러스터의 Zookeeeper에 커밋된 검사점 데이터를 유지하는 한편 새 클러스터에 동일한 이름의 토폴로지를 어떻게 배포할 수 있습니까?

#### <a name="where-is-checkpoint-data-stored"></a>검사점 데이터 저장 위치
오프셋에 대한 검사점 데이터는 EventHub Spout를 통해 Zookeeper의 다음 두 루트 경로 아래에 저장됩니다.
- 비트랜잭션 Spout 검사점 데이터는 /eventhubspout 아래에 저장됩니다.
- 트랜잭션 Spout 검사점 데이터는 /transactional 아래에 저장됩니다.

#### <a name="how-to-restore"></a>복원하는 방법
Zookeeper에서 데이터를 내보내고 새 이름으로 다시 가져오는 스크립트 및 라이브러리는 https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0에서 찾을 수 있습니다.

lib 폴더에는 가져오기/내보내기 작업에 대한 구현이 포함된 Jar 파일이 있습니다.
bash 폴더에는 이전 클러스터의 Zookeeper 서버에서 데이터를 내보내고 새 클러스터의 Zookeeper 서버로 다시 가져오는 방법에 대한 예제 스크립트가 있습니다.

Zookeeper 노드에서 [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) 스크립트를 실행하여 데이터를 가져오거나 내보내야 합니다.
스크립트를 업데이트하여 HDP 버전 문자열을 수정해야 합니다.
(HDInsight는 이러한 스크립트를 일반화하는 작업을 수행하므로 최종 사용자가 수정하지 않고도 클러스터의 모든 노드에서 해당 스크립트를 실행할 수 있습니다.)

내보내기 명령은 지정한 위치의 HDFS 경로(BLOB 또는 ADLS 저장소)에 메타데이터를 씁니다.

### <a name="examples"></a>예

##### <a name="export-offset-metadata"></a>오프셋 메타데이터 내보내기
1. 검사점 오프셋을 내보내야 하는 이전 클러스터의 Zookeeper 클러스터에 SSH로 연결합니다.
1. 아래 명령을 실행하여(hdp 버전 문자열을 업데이트한 후) Zookeeper 오프셋 데이터를 /stormmetadta/zkdata HDFS 경로로 내보냅니다.

```apache   
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
```

##### <a name="import-offset-metadata"></a>오프셋 메타데이터 가져오기
1. 검사점 오프셋을 내보내야 하는 이전 클러스터의 Zookeeper 클러스터에 SSH로 연결합니다.
1. 아래 명령을 실행하여(hdp 버전 문자열을 업데이트한 후) /stormmetadata/zkdata HDFS 경로에서 대상 클러스터의 Zookeeper 서버로 zookeeper 오프셋 데이터를 가져옵니다.

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
```
   
##### <a name="delete-offset-metadata-so-topologies-can-start-processing-data-from-either-beginning-or-timestamp-of-user-choice"></a>토폴로지에서 데이터 처리를 시작할 수 있도록 오프셋 메타데이터 삭제(사용자 선택의 시작 또는 타임스탬프)
1. 검사점 오프셋을 내보내야 하는 이전 클러스터의 Zookeeper 클러스터에 SSH로 연결합니다.
1. 아래 명령을 실행하여(hdp 버전 문자열을 업데이트한 후) 현재 클러스터에 대한 모든 Zookeeper 오프셋 데이터를 삭제합니다.

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>클러스터에서 Storm 이진 파일을 찾는 방법

### <a name="issue"></a>문제:
 HDInsight 클러스터의 Storm 서비스에 대한 이진 파일의 위치를 알고 있습니다.

### <a name="resolution-steps"></a>해결 단계:

현재 HDP 스택에 대한 Storm 이진 파일은 /usr/hdp/current/storm-client에서 찾을 수 있습니다.

이 위치는 헤드 노드와 작업자 노드에서 동일합니다.
 
/usr/hdp 아래에 여러 개의 HDP 버전별 이진 파일이 있을 수 있습니다(예: /usr/hdp/2.5.0.1233/storm)

그러나 /usr/hdp/current/storm-client는 클러스터에서 실행되는 최신 버전과 기호화된 링크로 연결되어 있습니다.

### <a name="further-reading"></a>추가 참고 자료:
 [SSH를 사용하여 HDInsight 클러스터에 연결](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) [Storm](http://storm.apache.org/)
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Storm 클러스터의 배포 토폴로지를 확인하는 방법
 
### <a name="issue"></a>문제:
 
HDInsight Storm과 함께 설치된 모든 구성 요소를 식별합니다.
 
Storm 클러스터는 다음 4개의 노드 범주로 구성됩니다.
1. 게이트웨이
1. 헤드 노드
1. Zookeeper 노드
1. 작업자 노드
 
#### <a name="gateway-nodes"></a>게이트웨이 노드
활성 Ambari 관리 서비스에 공용으로 액세스할 수 있는 게이트웨이 및 역방향 프록시 서비스에서 Ambari 리더 투표를 처리합니까?
 
#### <a name="zookeeper-nodes"></a>Zookeeper 노드
HDInsight는 3 노드 Zookeeper 쿼럼을 제공합니다.
쿼럼 크기는 고정되어 있으며 구성할 수 없습니다.
 
클러스터의 Storm 서비스는 ZK 쿼럼을 자동으로 사용하도록 구성됩니다.
 
#### <a name="head-nodes"></a>헤드 노드
Storm 헤드 노드에서 실행하는 서비스는 다음과 같습니다.
1. Nimbus
1. Ambari 서버
1. Ambari 메트릭 서버
1. Ambari 메트릭 수집기
 
#### <a name="worker-nodes"></a>작업자 노드
 Storm 작업자 노드에서 실행하는 서비스는 다음과 같습니다.
1. 감독자
1. 토폴로지를 실행하기 위한 작업자 JVM
1. Ambari 에이전트
 
## <a name="how-do-i-locate-storm-eventhub-spout-binaries-for-development"></a>개발용 Storm-EventHub-Spout 이진 파일을 찾는 방법
 
### <a name="issue"></a>문제:
내 토폴로지와 함께 사용하기 위해 Storm EventHub Spout jar를 사용하는 방법에 대해 자세히 알아 보려면 어떻게 해야 합니까?
 
#### <a name="msdn-articles-on-how-to"></a>사용법 관련 MSDN 문서
 
##### <a name="java-based-topology"></a>Java 기반 토폴로지
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology
 
##### <a name="c-based-topology-using-mono-on-hdi-34-linux-storm-clusters"></a>C# 기반 토폴로지(HDI 3.4 이상의 Linux Storm 클러스터에서 Mono 사용)
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology
 
##### <a name="latest-storm-eventhub-spout-binaries-for-hdi35-linux-storm-clusters"></a>HDI3.5 이상의 Linux Storm 클러스터에 대한 최신 Storm EventHub Spout 이진 파일
HDI3.5 이상의 Linux Storm 클러스터에서 작동하는 최신 Storm EventHub Spout를 사용하는 방법은 https://github.com/hdinsight/mvn-repo/blob/master/README.md를 검토합니다.
 
##### <a name="source-code-examples"></a>소스 코드 예제:
https://github.com/Azure-Samples/hdinsight-java-storm-eventhub
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>클러스터에서 Storm Log4J 구성 파일을 찾는 방법
 
### <a name="issue"></a>문제:
 
Storm 서비스에 대한 Log4J 구성 파일을 식별해야 합니다.
 
#### <a name="on-headnodes"></a>헤드 노드:
Nimbus Log4J 구성 파일은 /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml에서 읽을 수 있습니다.
 
#### <a name="worker-nodes"></a>작업자 노드
감독자 Log4J 구성 파일은 /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml에서 읽을 수 있습니다.
 
작업자 Log4J 구성 파일은 /usr/hdp/<HDPVersion>/storm/log4j2/worker.xml에서 읽을 수 있습니다.
 
예: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml







