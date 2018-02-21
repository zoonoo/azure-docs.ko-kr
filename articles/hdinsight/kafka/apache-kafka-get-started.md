---
title: "Apache Kafka 시작 - Azure HDInsight | Microsoft 문서 도구"
description: "Azure HDInsight의 Apache Kafka 클러스터를 만드는 방법에 대해 알아봅니다. 토픽, 구독자 및 소비자를 만드는 방법에 대해 알아봅니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2018
ms.author: larryfr
ms.openlocfilehash: 639adb2fdc5a7d76c11397b5027199626a0a4016
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>HDInsight에서 Apache Kafka 시작

Azure HDInsight의 [Apache Kafka](https://kafka.apache.org) 클러스터를 만들고 사용하는 방법에 대해 알아봅니다. Kafka는 HDInsight와 함께 제공되는 오픈 소스 분산형 스트리밍 플랫폼입니다. 게시-구독 메시지 큐와 유사한 기능을 제공하므로 메시지 브로커로 자주 사용됩니다. Kafka는 자주 Apache Spark 및 Apache Storm과 함께 사용됩니다.

> [!NOTE]
> 현재 HDInsight에는 두 가지 버전의 Kafka, 즉 0.9.0(HDInsight 3.4) 및 0.10.0(HDInsight 3.5 및 3.6)이 제공됩니다. 이 문서의 단계에서는 HDInsight 3.6에서 Kafka를 사용하는 것으로 가정합니다.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Kafka 클러스터 만들기

다음 단계를 사용하여 HDInsight 클러스터에 Kafka를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 **+ 리소스 만들기**, **데이터 + 분석**, **HDInsight**를 차례로 선택합니다.
   
    ![HDInsight 클러스터 만들기](./media/apache-kafka-get-started/create-hdinsight.png)

2. **기본**에서 다음 정보를 입력합니다.

    * **클러스터 이름**: HDInsight 클러스터의 이름입니다.
    * **구독**: 사용할 구독을 선택합니다.
    * **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**: HTTPS를 통해 클러스터에 액세스할 때 로그인입니다. 이러한 자격 증명을 사용하여 Ambari Web UI 또는 REST API와 같은 서비스에 액세스합니다.
    * **SSH(보안 셸) 사용자 이름**: SSH를 통해 클러스터에 액세스할 때 사용되는 로그인입니다. 기본적으로 암호는 클러스터 로그인 암호와 동일합니다.
    * **리소스 그룹**: 클러스터를 만들어 놓은 리소스 그룹입니다.
    * **위치**: 클러스터를 만들어 놓은 Azure 지역입니다.

        > [!IMPORTANT]
        > 데이터의 고가용성을 위해 __세 개의 장애 도메인__을 포함하는 위치(영역) 선택을 권장합니다. 자세한 내용은 [데이터 고가용성](#data-high-availability) 섹션을 참조하세요.
   
 ![구독 선택](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. **클러스터 유형**을 선택한 다음 **클러스터 구성**에서 다음 값을 설정합니다.
   
    * **클러스터 유형**: Kafka
    * **버전**: Kafka 0.10.0(HDI 3.6)

    마지막으로 **선택** 단추를 사용하여 이러한 설정을 저장합니다.
     
 ![클러스터 유형 선택](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

4. 클러스터 유형을 선택한 후 __선택__ 단추를 사용하여 클러스터 유형을 설정합니다. 그 다음, __다음__ 단추를 사용하여 기본 구성을 완료합니다.

5. **Storage**에서 Storage 계정을 선택하거나 만듭니다. 이 문서의 단계에서는 다른 필드를 기본값으로 둡니다. __다음__ 단추를 사용하여 저장소 구성을 저장합니다.

    ![HDInsight에 대한 저장소 계정 설정 지정](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

6. __응용 프로그램(선택 사항)__에서 __다음__을 선택하여 계속합니다. 이 예제에는 응용 프로그램이 필요하지 않습니다.

7. __클러스터 크기__에서 __다음__을 선택하여 계속합니다.

    > [!WARNING]
    > HDInsight에서 Kafka의 사용 가능성을 보장하려면 클러스터에 작업자 노드가 3개 이상 포함되어야 합니다. 자세한 내용은 [데이터 고가용성](#data-high-availability) 섹션을 참조하세요.

    ![Kafka 클러스터 크기 설정](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > **작업자 노드 항목당 디스크**에 따라 HDInsight에서 Kafka의 확장성이 제어됩니다. HDInsight의 Kafka는 클러스터에서 가상 머신의 로컬 디스크를 사용합니다. Kafka는 입출력이 많으므로 높은 처리량을 제공하고 노드당 더 많은 저장소를 제공하기 위해 [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md)를 사용합니다. 관리 디스크 유형은 __표준__(HDD) 또는 __프리미엄__(SSD)일 수 있습니다. 프리미엄 디스크는 DS 및 GS 시리즈 VM에 사용됩니다. 다른 모든 VM 유형은 표준을 사용합니다.

8. __고급 설정__에서 __다음__을 선택하여 계속합니다.

9. **요약**에서 클러스터에 대한 구성을 검토합니다. __편집__ 링크를 사용하여 올바르지 않은 설정을 변경합니다. 마지막으로 __만들기__ 단추를 사용하여 클러스터를 만듭니다.
   
    ![클러스터 구성 요약](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > 클러스터를 만드는 데 최대 20분이 걸릴 수 있습니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

> [!IMPORTANT]
> 다음 단계를 수행하는 경우에 SSH 클라이언트를 사용해야 합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md) 문서를 참조하세요.

클라이언트에서 SSH를 다음과 같이 사용하여 클러스터에 연결합니다.

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

**SSHUSER**는 클러스터를 만드는 중에 제공한 SSH 사용자 이름으로 바꿉니다. **CLUSTERNAME**은 클러스터 이름으로 바꿉니다.

메시지가 표시되면 SSH 계정에 사용한 암호를 입력합니다.

자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a id="getkafkainfo"></a>Zookeeper 및 Broker 호스트 정보 가져오기

Kafka를 사용할 때는 두 가지 호스트, 즉 *Zookeeper* 호스트와 *Broker* 호스트의 값을 알고 있어야 합니다. 이러한 호스트는 Kafka API 및 Kafka와 함께 제공되는 다양한 유틸리티에서 사용됩니다.

다음 단계를 사용하여 호스트 정보를 포함하는 환경 변수를 만듭니다. 이러한 환경 변수는 이 문서의 단계에서 사용됩니다.

1. 클러스터에 대한 SSH 연결에서 다음 명령을 사용하여 `jq` 유틸리티를 설치합니다. 이 유틸리티는 JSON 문서를 구문 분석하는 데 사용되며 broker 호스트 정보 검색에 유용합니다.
   
    ```bash
    sudo apt -y install jq
    ```

2. Ambari에서 검색한 정보로 환경 변수를 설정하려면 다음 명령을 사용합니다.

    ```bash
    CLUSTERNAME='your cluster name'
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    > [!IMPORTANT]
    > `CLUSTERNAME=`을 Kafka 클러스터의 이름으로 설정합니다. 메시지가 표시되면 클러스터 로그인(관리자) 계정에 대한 암호를 입력합니다.

    다음 텍스트는 `$KAFKAZKHOSTS` 내용의 예제입니다.
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    다음 텍스트는 `$KAFKABROKERS` 내용의 예제입니다.
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

    > [!NOTE]
    > `cut` 명령은 호스트 목록을 두 개의 호스트 항목으로 자르는 데 사용됩니다. Kafka 소비자 또는 생산자를 만들 때 호스트의 전체 목록을 제공할 필요가 없습니다.
   
    > [!WARNING]
    > 항상 정확하도록 이 세션에서 반환된 정보에 의존하지 마세요. 클러스터를 확장하면 새 broker가 추가되거나 제거됩니다. 오류가 발생하고 노드가 대체되면 노드의 호스트 이름을 변경할 수 있습니다.
    >
    > Zookeeper와 broker 호스트 정보는 올바른 정보를 얻기 위해 사용하기 직전에 검색해야 합니다.

## <a name="create-a-topic"></a>토픽 만들기

Kafka는 *토픽*이라는 범주에 데이터 스트림을 저장합니다. 클러스터 헤드 노드에 대한 SSH 연결에서 Kafka와 함께 제공된 다음 스크립트를 사용하여 토픽을 만듭니다.

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

이 명령은 `$KAFKAZKHOSTS`에 저장된 호스트 정보를 사용하여 Zookeeper에 연결한 다음 **test**라는 Kafka 토픽을 만듭니다. 토픽을 나열하는 다음 스크립트를 사용하면 해당 토픽이 만들어졌는지 확인할 수 있습니다.

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

이 명령의 출력에서 **test** 토픽이 포함된 Kafka 토픽이 나열됩니다.

## <a name="produce-and-consume-records"></a>레코드 생성 및 소비

Kafka는 토픽에 *레코드*를 저장합니다. *생산자*에서 레코드를 생성하고, *소비자*에서 이 레코드를 소비합니다. 생산자는 Kafka *brokers*에 레코드를 생성합니다. HDInsight 클러스터의 각 작업자 노드는 Kafka broker입니다.

앞에서 만든 test 토픽에 레코드를 저장한 다음 소비자를 통해 레코드를 읽으려면 다음 단계를 사용합니다.

1. SSH 세션에서 Kafka와 함께 제공되는 스크립트를 사용하여 토픽에 레코드를 기록합니다.
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    이 명령 이후에는 프롬프트로 돌아가지 않습니다. 그 대신 몇 가지 텍스트 메시지를 입력한 다음 **Ctrl+C**를 사용하여 토픽 전송을 중지합니다. 각 행은 별도의 레코드로 전송됩니다.

2. Kafka와 함께 제공된 다음 스크립트를 사용하여 토픽에서 레코드를 읽습니다.
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    이 명령은 토픽에서 레코드를 검색하여 표시합니다. `--from-beginning`을 사용하면 스트림 시작 부분부터 시작하도록 소비자에 지시하여 모든 레코드를 검색합니다.

    > [!NOTE]
    > Kafka의 이전 버전을 사용하는 경우 `--bootstrap-server $KAFKABROKERS`를 `--zookeeper $KAFKAZKHOSTS`로 대체해야 합니다.

3. __Ctrl+C__를 사용하여 소비자를 중지합니다.

또한 프로그래밍 방식으로 생산자와 소비자를 만들 수 있습니다. 이 API를 사용하는 예제는 [HDInsight의 Kafka 생산자 및 소비자 API](apache-kafka-producer-consumer-api.md) 문서를 참조하세요.

## <a name="data-high-availability"></a>데이터 고가용성

각 Azure 지역(위치)은 _장애 도메인_을 제공합니다. 장애 도메인은 Azure 데이터 센터에 있는 기본 하드웨어의 논리적 그룹입니다. 장애 도메인마다 공통 전원과 네트워크 스위치를 공유합니다. HDInsight 클러스터 내의 노드를 구현하는 가상 머신과 관리 디스크는 이러한 장애 도메인에 분산되어 있습니다. 이 아키텍처에서는 실제 하드웨어 오류의 잠재적 영향을 제한합니다.

영역에서 장애 도메인의 수에 대한 자세한 내용은 [Linux 가상 머신의 가용성](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) 문서를 참조하세요.

> [!IMPORTANT]
> 3개의 장애 도메인을 포함하는 Azure 지역을 사용하고 복제 계수로 3을 사용하는 것이 좋습니다.

2개의 장애 도메인만 포함하는 지역을 사용해야 하는 경우 복제 계수로 4를 사용하여 두 장애 도메인에 복제본을 동일하게 분산합니다.

### <a name="kafka-and-fault-domains"></a>Kafka 및 장애 도메인

Kafka는 장애 도메인을 인식하지 않습니다. 항목에 대한 파티션 복제본을 만들 때 고가용성에 대해 복제본을 제대로 배포하지 않을 수 있습니다. 고가용성을 보장하려면 [Kafka 파티션 재조정 도구](https://github.com/hdinsight/hdinsight-kafka-tools)를 사용합니다. 이 도구는 SSH 세션에서 Kafka 클러스터의 헤드 노드로 실행되어야 합니다.

가장 높은 Kafka 데이터 가용성을 보장하려면 다음과 같은 경우에 토픽에 대한 파티션 복제본의 부하를 다시 조정해야 합니다.

* 새 토픽 또는 파티션을 만들 때

* 클러스터를 확장할 때

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](../hdinsight-administer-use-portal-linux.md#create-clusters)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서 HDInsight에서 Apache Kafka를 사용할 때 필요한 기본 사항을 알아보았습니다. Kafka 작업에 대해 자세히 알아보려면 다음을 사용하세요.

* [Kafka 로그 분석](apache-kafka-log-analytics-operations-management.md)
* [Kafka 클러스터 간 데이터 복제](apache-kafka-mirroring.md)
* [HDInsight의 Kafka 생산자 및 소비자 API](apache-kafka-producer-consumer-api.md)
* [HDInsight의 Kafka 스트림 API](apache-kafka-streams-api.md)
* [HDInsight의 Kafka에서 Apache Spark 스트리밍(DStream) 사용](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight의 Kafka에서 Apache Spark 구조적 스트리밍 사용](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Apache Spark 구조적 스트리밍을 사용하여 HDInsight의 Kafka에서 Cosmos DB로 데이터 이동](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [HDInsight의 Kafka에서 Apache Storm 사용](../hdinsight-apache-storm-with-kafka.md)
* [Azure Virtual Network를 통해 Kafka에 연결](apache-kafka-connect-vpn-gateway.md)
* [Azure Container Service에서 Kafka 사용](apache-kafka-azure-container-services.md)
* [Azure 함수 앱에서 Kafka 사용](apache-kafka-azure-functions.md)
