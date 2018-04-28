---
title: Apache Kafka Streams API 사용 - Azure HDInsight | Microsoft Docs
description: HDInsight의 Kafka에서 Apache Kafka Streams API를 사용하는 방법에 대해 알아봅니다. 이 API를 사용하면 Kafka에서 토픽 간 스트림 처리를 수행할 수 있습니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 36d67cdb99871f3948db1f6497b1a4638df4f3f1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-streams-api"></a>Apache Kafka Streams API

Kafka Streams API를 사용하는 응용 프로그램을 만들고 HDInsight의 Kafka에서 이를 실행하는 방법을 알아봅니다.

Apache Kafka로 작업할 때 스트림 처리는 종종 Apache Spark 또는 Storm을 사용하여 수행됩니다. Kafka 0.10.0 버전(HDInsight 3.5 및 3.6)에 Kafka Streams API가 도입되었습니다. 이 API를 사용하면 Kafka에서 실행되는 응용 프로그램을 사용하여 입력 및 출력 토픽 간의 데이터 스트림을 변환할 수 있습니다. 경우에 따라 Spark 또는 Storm 스트리밍 솔루션을 만드는 대신 이 방법을 사용할 수 있습니다. Kafka Streams에 대한 자세한 내용은 Apache.org의 [Streams 소개](https://kafka.apache.org/10/documentation/streams/) 문서를 참조하세요.

## <a name="set-up-your-development-environment"></a>개발 환경 설정

개발 환경에 다음 구성 요소가 설치되어 있어야 합니다.

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 또는 이와 동등한 프로그램(예: OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* SSH 클라이언트 및 `scp` 명령입니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md) 문서를 참조하세요.

## <a name="set-up-your-deployment-environment"></a>배포 환경 설정

이 예제에서는 HDInsight 3.6의 Kafka가 필요합니다. HDInsight 클러스터에서 Kafka를 만드는 방법을 알아보려면 [HDInsight에서 Kafka 시작](apache-kafka-get-started.md) 설명서를 참조하세요.

## <a name="build-and-deploy-the-example"></a>예제 빌드 및 배포

프로젝트를 빌드하고 HDInsight 클러스터의 Kafka에 배포하려면 다음 단계를 사용합니다.

1. [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)에서 예제를 다운로드합니다.

2. 디렉터리를 `Streaming` 디렉터리로 변경한 후 다음 명령을 사용하여 jar 패키지를 만듭니다.

    ```bash
    mvn clean package
    ```

    이 명령은 `kafka-streaming-1.0-SNAPSHOT.jar`라는 파일이 포함된 `target`이라는 디렉터리를 만듭니다.

3. 다음 명령을 사용하여 `kafka-streaming-1.0-SNAPSHOT.jar` 파일을 HDInsight 클러스터에 복사합니다.
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    **SSHUSER**는 클러스터의 SSH 사용자로 바꾸고, **CLUSTERNAME**은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자 계정의 암호를 입력합니다. HDInsight에서의 `scp` 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="run-the-example"></a>예제 실행

1. 클러스터에 대한 SSH 연결을 열려면 다음 명령을 사용합니다.

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    **SSHUSER**는 클러스터의 SSH 사용자로 바꾸고, **CLUSTERNAME**은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자 계정의 암호를 입력합니다. HDInsight에서의 `scp` 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

4. 이 예제에서 사용되는 Kafka 토픽을 만들려면 다음 명령을 사용합니다.

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    __클러스터 이름__을 HDInsight 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 HDInsight 클러스터 로그인 계정에 대한 암호를 입력합니다.

    > [!NOTE]
    > 클러스터 로그인이 `admin`의 기본값과 다른 경우 이전 명령의 `admin` 값을 클러스터 로그인 이름으로 바꿉니다.

5. 이 예제를 실행하려면 다음 세 가지를 수행해야 합니다.

    * `kafka-streaming.jar`에 포함된 Streams 솔루션을 시작합니다.
    * `test` 토픽에 쓰는 생산자를 시작합니다.
    * `wordcounts` 토픽에 쓴 출력을 볼 수 있도록 소비자를 시작합니다.

    > [!NOTE]
    > Kafka Broker 구성 파일에서 `auto.create.topics.enable` 속성이 `true`로 설정되어 있는지 확인해야 합니다. Ambari 웹 UI를 사용하여 고급 Kafka Broker 구성 파일에서 이 속성을 확인 및 수정할 수 있습니다. 그렇지 않은 경우 다음 명령을 사용하여 이 예제를 실행하기 전에 수동으로 중간 `RekeyedIntermediateTopic` 항목을 만들어야 합니다.
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic  --zookeeper $KAFKAZKHOSTS
    ```
    
    3개의 SSH 세션을 열어 이러한 작업을 수행할 수 있습니다. 그러나 각 SSH 세션의 이 섹션에서 4단계를 실행하여 각각에 대해 `$KAFKABROKERS` 및 `$KAFKAZKHOSTS`를 설정해야 합니다. 더 쉬운 솔루션은 현재 SSH 디스플레이를 여러 섹션으로 나눌 수 있는 `tmux` 유틸리티를 사용하는 것입니다. `tmux`를 사용하여 스트림, 생산자, 소비자를 시작하려면 다음 명령을 사용합니다.

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    이 명령은 SSH 디스플레이를 세 가지 섹션으로 분할합니다.

    * 왼쪽 섹션은 콘솔 소비자를 실행하여 `wordcounts` 토픽에서 메시지를 읽습니다. `/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > `--property` 매개 변수는 콘솔 소비자에게 개수(값)와 함께 키(단어)를 인쇄하도록 지시합니다. 또한 이 매개 변수는 Kafka에서 이 값을 읽을 때 사용할 역직렬 변환기를 구성합니다.

    * 오른쪽 위의 섹션은 Streams API 솔루션을 실행합니다. `java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * 오른쪽 아래의 섹션은 콘솔 생산자를 실행하고 `test` 토픽에 보낼 메시지 입력을 기다립니다. `/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. `tmux` 명령으로 디스플레이가 분할되면 커서가 오른쪽 아래 섹션에 있습니다. 문장 입력을 시작합니다. 각 문장이 끝나면 왼쪽 창에 고유 단어 수가 표시됩니다. 다음 텍스트와 유사하게 출력됩니다.
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > 단어가 발생할 때마다 개수가 증가합니다.

7. __Ctrl + C__를 사용하여 생산자를 종료합니다. __Ctrl + C__를 한 번 더 사용하여 응용 프로그램 및 소비자를 종료합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight의 Kafka에서 Kafka Streams API를 사용하는 방법에 대해 알아보았습니다. Kafka 작업에 대해 자세히 알아보려면 다음을 사용하세요.

* [Kafka 로그 분석](apache-kafka-log-analytics-operations-management.md)
* [Kafka 클러스터 간 데이터 복제](apache-kafka-mirroring.md)
* [HDInsight의 Kafka 생산자 및 소비자 API](apache-kafka-producer-consumer-api.md)
* [HDInsight의 Kafka에서 Apache Spark 스트리밍(DStream) 사용](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight의 Kafka에서 Apache Spark 구조적 스트리밍 사용](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Apache Spark 구조적 스트리밍을 사용하여 HDInsight의 Kafka에서 Cosmos DB로 데이터 이동](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [HDInsight의 Kafka에서 Apache Storm 사용](../hdinsight-apache-storm-with-kafka.md)
* [Azure Virtual Network를 통해 Kafka에 연결](apache-kafka-connect-vpn-gateway.md)
