---
title: Apache Kafka 생산자 및 소비자 API 사용 - Azure HDInsight | Microsoft Docs
description: HDInsight의 Kafka에서 Apache Kafka 생산자 및 소비자 API를 사용하는 방법에 대해 알아봅니다. 이러한 API를 사용하여 Apache Kafka에서 읽고 쓰는 응용 프로그램을 개발할 수 있습니다.
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
ms.openlocfilehash: 01592401c4c88adeed49b11df4e7963e27b1bcee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>Apache Kafka 생산자 및 소비자 API

HDInsight의 Kafka에서 Kafka 생산자 및 소비자 API를 사용하는 응용 프로그램을 만드는 방법을 알아봅니다.

API에 대한 설명서는 [생산자 API](https://kafka.apache.org/documentation/#producerapi) 및 [소비자 API](https://kafka.apache.org/documentation/#consumerapi)를 참조하세요.

## <a name="set-up-your-development-environment"></a>개발 환경 설정

개발 환경에 다음 구성 요소가 설치되어 있어야 합니다.

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 또는 이와 동등한 프로그램(예: OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* SSH 클라이언트 및 `scp` 명령입니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md) 문서를 참조하세요.

## <a name="set-up-your-deployment-environment"></a>배포 환경 설정

이 예제에서는 HDInsight 3.6의 Kafka가 필요합니다. HDInsight 클러스터에서 Kafka를 만드는 방법을 알아보려면 [HDInsight에서 Kafka 시작](apache-kafka-get-started.md) 설명서를 참조하세요.

## <a name="build-and-deploy-the-example"></a>예제 빌드 및 배포

1. [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)에서 예제를 다운로드합니다.

2. 디렉터리를 `Producer-Consumer` 디렉터리 위치로 변경한 후 다음 명령을 사용합니다.

    ```
    mvn clean package
    ```

    이 명령은 `kafka-producer-consumer-1.0-SNAPSHOT.jar`라는 파일이 포함된 `target`이라는 디렉터리를 만듭니다.

3. 다음 명령을 사용하여 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 파일을 HDInsight 클러스터에 복사합니다.
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    **SSHUSER**는 클러스터의 SSH 사용자로 바꾸고, **CLUSTERNAME**은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자의 암호를 입력합니다.

## <a id="run"></a> 예제 실행

1. 클러스터에 대한 SSH 연결을 열려면 다음 명령을 사용합니다.

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    **SSHUSER**는 클러스터의 SSH 사용자로 바꾸고, **CLUSTERNAME**은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자 계정의 암호를 입력합니다. HDInsight에서의 `scp` 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 이 예제에서 사용되는 Kafka 토픽을 만들려면 다음 명령을 사용합니다.

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    __클러스터 이름__을 HDInsight 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 HDInsight 클러스터 로그인 계정에 대한 암호를 입력합니다.

    > [!NOTE]
    > 클러스터 로그인이 `admin`의 기본값과 다른 경우 이전 명령의 `admin` 값을 클러스터 로그인 이름으로 바꿉니다.

3. 생산자를 실행하고 토픽에 데이터를 쓰려면 다음 명령을 사용합니다.

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. 생산자가 완료되면 다음 명령을 사용하여 토픽에서 읽습니다.
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    레코드 수와 함께 읽은 레코드가 표시됩니다.

5. __Ctrl+C__를 사용하여 소비자를 종료합니다.

### <a name="multiple-consumers"></a>여러 소비자

Kafka 소비자는 레코드를 읽을 때 소비자 그룹을 사용합니다. 여러 소비자와 같은 그룹을 사용하면 부하가 분산되어 토픽에서 읽습니다. 그룹의 각 소비자는 레코드의 일부를 받습니다.

소비자 응용 프로그램은 그룹 ID로 사용되는 매개 변수를 허용합니다. 예를 들어, 다음 명령은 그룹 ID `mygroup`을 사용하여 소비자를 시작합니다.
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

동작 중인 이 프로세스를 확인하려면 다음 단계를 사용합니다.

1. [예제 실행](#run) 섹션에서 1 및 2단계를 반복하여 새 SSH 세션을 엽니다.

2. 두 SSH 세션에서 다음 명령을 입력합니다.

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > 두 명령을 동시에 입력하여 동시에 실행되도록 합니다.

    메시지 읽기 수는 소비자가 하나만 있던 이전 섹션과 같지 않습니다. 대신, 메시지 읽기는 인스턴스 간에 분할됩니다.

동일한 그룹 내에서 클라이언트에 의한 소비는 토픽에 대한 파티션을 통해 처리됩니다. 앞에서 만든 `test` 토픽에는 8개의 파티션이 있습니다. 8개 SSH 세션을 열고 모든 세션에서 소비자를 시작하면 각 소비자는 해당 토픽에 대한 단일 파티션에서 레코드를 읽습니다.

> [!IMPORTANT]
> 소비자 그룹에는 파티션보다 더 많은 소비자 인스턴스가 있을 수 없습니다. 이 예제에서 하나의 소비자 그룹은 토픽의 파티션 수이기 때문에 최대 8개 소비자를 포함할 수 있습니다. 또는 소비자가 8개 이하인 소비자 그룹이 여러 개 있을 수 있습니다.

Kafka에 저장된 레코드는 파티션에서 받은 순서대로 저장됩니다. *파티션 내의* 레코드에 대해 순서대로 전달하려면 파티션 수와 일치하는 소비자 인스턴스가 있는 소비자 그룹을 만듭니다. *토픽 내의* 레코드에 대해 순서대로 전달하려면 하나의 소비자 인스턴스만 사용하는 소비자 그룹을 만듭니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight의 Kafka에서 Kafka 생산자 및 소비자 API를 사용하는 방법에 대해 알아보았습니다. Kafka 작업에 대해 자세히 알아보려면 다음을 사용하세요.

* [Kafka 로그 분석](apache-kafka-log-analytics-operations-management.md)
* [Kafka 클러스터 간 데이터 복제](apache-kafka-mirroring.md)
* [HDInsight의 Kafka 스트림 API](apache-kafka-streams-api.md)
* [HDInsight의 Kafka에서 Apache Spark 스트리밍(DStream) 사용](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight의 Kafka에서 Apache Spark 구조적 스트리밍 사용](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Apache Spark 구조적 스트리밍을 사용하여 HDInsight의 Kafka에서 Cosmos DB로 데이터 이동](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [HDInsight의 Kafka에서 Apache Storm 사용](../hdinsight-apache-storm-with-kafka.md)
* [Azure Virtual Network를 통해 Kafka에 연결](apache-kafka-connect-vpn-gateway.md)