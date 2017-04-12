---
title: "HDInsight에서 Apache Kafka 시작 | Microsoft 문서"
description: "HDInsight에서 Kafka 클러스터를 만들고 사용할 때 필요한 기본 사항에 대해 알아봅니다."
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
ms.date: 02/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 1c7a95354358d91859ce9d2019cef93b6662811f
ms.lasthandoff: 03/25/2017

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>HDInsight에서 Apache Kafka(미리 보기) 시작

[Apache Kafka](https://kafka.apache.org)는 HDInsight와 함께 제공되는 오픈 소스 분산형 스트리밍 플랫폼입니다. 게시-구독 메시지 큐와 유사한 기능을 제공하므로 메시지 브로커로 자주 사용됩니다. 이 문서에서는 HDInsight 클러스터에서 Kafka를 만든 다음 Java 응용 프로그램에서 데이터를 보내고 받는 방법에 대해 알아봅니다.

> [!NOTE]
> 현재 HDInsight에는 두 가지 버전의 Kafka, 즉 0.9.0(HDInsight 3.4) 및 0.10.0(HDInsight 3.5)이 제공됩니다. 이 문서의 단계에서는 HDInsight 3.5에서 Kafka를 사용하는 것으로 가정합니다.

## <a name="prerequisite"></a>필수 요소

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 Apache Kafka 자습서를 성공적으로 완료하려면 다음 항목이 필요합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* **SSH 및 SCP 사용 경험**. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 또는 이와 동등한 프로그램(예: OpenJDK)

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>액세스 제어 요구 사항

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>Kafka 클러스터 만들기

다음 단계를 사용하여 HDInsight 클러스터에 Kafka를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 **+ 새로 만들기**, **인텔리전스 + 분석**, **HDInsight**를 차례로 선택합니다.
   
    ![HDInsight 클러스터 만들기](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. **기본** 블레이드에서 다음 정보를 입력합니다.

    * **클러스터 이름**: HDInsight 클러스터의 이름입니다.
    * **구독**: 사용할 구독을 선택합니다.
    * **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**: HTTPS를 통해 클러스터에 액세스할 때 로그인입니다. 이러한 자격 증명을 사용하여 Ambari Web UI 또는 REST API와 같은 서비스에 액세스합니다.
    * **SSH(보안 셸) 사용자 이름**: SSH를 통해 클러스터에 액세스할 때 사용되는 로그인입니다. 기본적으로 암호는 클러스터 로그인 암호와 동일합니다.
    * **리소스 그룹**: 클러스터를 만들어 놓은 리소스 그룹입니다.
    * **위치**: 클러스터를 만들어 놓은 Azure 지역입니다.
   
    ![구독 선택](./media/hdinsight-apache-kafka-get-started/hdinsight-basic-configuration.png)

3. **클러스터 유형**을 선택한 다음 **클러스터 구성** 블레이드에서 다음 값을 설정합니다.
   
    * **클러스터 유형**: Kafka

    * **버전**: Kafka 0.10.0(HDI 3.5)

    * **클러스터 계층**: 표준
     
    마지막으로 **선택** 단추를 사용하여 이러한 설정을 저장합니다.
     
    ![클러스터 유형 선택](./media/hdinsight-apache-kafka-get-started/set-hdinsight-cluster-type.png)

    > [!NOTE]
    > Azure 구독에 Kafka 미리 보기에 대한 액세스 권한이 없는 경우 미리 보기 액세스 권한을 얻는 방법에 대한 지침이 표시됩니다. 표시되는 지침은 다음 이미지와 유사합니다.
    >
    > ![미리 보기 메시지: HDInsight에서 관리되는 Apache Kafka 클러스터를 배포하려는 경우에는 미리 보기 액세스 권한을 요청하는 전자 메일을 보내 주세요.](./media/hdinsight-apache-kafka-get-started/no-kafka-preview.png)

4. 클러스터 유형을 선택한 후 __선택__ 단추를 사용하여 클러스터 유형을 설정합니다. 그 다음, __다음__ 단추를 사용하여 기본 구성을 완료합니다.

5. **저장소** 블레이드에서 저장소 계정을 선택하거나 만듭니다. 이 문서의 단계에서는 이 블레이드의 다른 필드를 기본값으로 둡니다. __다음__ 단추를 사용하여 저장소 구성을 저장합니다.

    ![HDInsight에 대한 저장소 계정 설정 지정](./media/hdinsight-apache-kafka-get-started/set-hdinsight-storage-account.png)

6. **요약** 블레이드에서 클러스터에 대한 구성을 검토합니다. __편집__ 링크를 사용하여 올바르지 않은 설정을 변경합니다. 마지막으로 __만들기__ 단추를 사용하여 클러스터를 만듭니다.
   
    ![클러스터 구성 요약](./media/hdinsight-apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > 클러스터를 만드는 데 최대 20분이 걸릴 수 있습니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

클라이언트에서 SSH를 사용하여 클러스터에 연결합니다. Windows 10에서 Linux, Unix, MacOS 또는 Bash를 사용하는 경우 다음 명령을 사용합니다.

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

**SSHUSER**는 클러스터를 만드는 중에 제공한 SSH 사용자 이름으로 바꿉니다. **CLUSTERNAME**은 클러스터 이름으로 바꿉니다.

메시지가 표시되면 SSH 계정에 사용한 암호를 입력합니다.

자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

##<a id="getkafkainfo"></a>Zookeeper 및 Broker 호스트 정보 가져오기

Kafka를 사용할 때는 두 가지 호스트, 즉 *Zookeeper* 호스트와 *Broker* 호스트의 값을 알고 있어야 합니다. 이러한 호스트는 Kafka API 및 Kafka와 함께 제공되는 다양한 유틸리티에서 사용됩니다.

다음 단계를 사용하여 호스트 정보를 포함하는 환경 변수를 만듭니다. 이러한 환경 변수는 이 문서의 단계에서 사용됩니다.

1. 클러스터에 대한 SSH 연결에서 다음 명령을 사용하여 `jq` 유틸리티를 설치합니다. 이 유틸리티는 JSON 문서를 구문 분석하는 데 사용되며 broker 호스트 정보 검색에 유용합니다.
   
    ```bash
    sudo apt -y install jq
    ```

2. 다음 명령을 사용하여 Ambari에서 검색한 정보로 환경 변수를 설정합니다. __KAFKANAME__은 Kafka 클러스터의 이름으로 바꿉니다. __PASSWORD__는 클러스터를 만들 때 사용한 로그인(관리자) 암호로 바꿉니다.

    ```bash
    export KAFKAZKHOSTS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

    export KAFKABROKERS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    다음 텍스트는 `$KAFKAZKHOSTS` 내용의 예제입니다.
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    다음 텍스트는 `$KAFKABROKERS` 내용의 예제입니다.
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
    > [!WARNING]
    > 항상 정확하도록 이 세션에서 반환된 정보에 의존하지 마세요. 클러스터를 확장하면 새 broker가 추가되거나 제거됩니다. 오류가 발생하고 노드가 대체되면 노드의 호스트 이름을 변경할 수 있습니다. 
    > 
    > Zookeeper와 broker 호스트 정보는 올바른 정보를 얻기 위해 사용하기 직전에 검색해야 합니다.

## <a name="create-a-topic"></a>토픽 만들기

Kafka는 *토픽*이라는 범주에 데이터 스트림을 저장합니다. 클러스터 헤드 노드에 대한 SSH 연결에서 Kafka와 함께 제공된 다음 스크립트를 사용하여 토픽을 만듭니다.

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

이 명령은 `$KAFKAZKHOSTS`에 저장된 호스트 정보를 사용하여 Zookeeper에 연결한 다음 **test**라는 Kafka 토픽을 만듭니다. 토픽을 나열하는 다음 스크립트를 사용하면 해당 토픽이 만들어졌는지 확인할 수 있습니다.

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

이 명령의 출력에서 **test** 토픽이 포함된 Kafka 토픽이 나열됩니다.

## <a name="produce-and-consume-records"></a>레코드 생성 및 소비

Kafka는 토픽에 *레코드*를 저장합니다. *생산자*에서 레코드를 생성하고, *소비자*에서 이 레코드를 소비합니다. 생산자는 Kafka *broker*로부터 레코드를 검색합니다. HDInsight 클러스터의 각 작업자 노드는 Kafka broker입니다.

앞에서 만든 test 토픽에 레코드를 저장한 다음 소비자를 통해 레코드를 읽으려면 다음 단계를 사용합니다.

1. SSH 세션에서 Kafka와 함께 제공되는 스크립트를 사용하여 토픽에 레코드를 기록합니다.
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    이 명령 이후에는 프롬프트로 돌아가지 않습니다. 그 대신 몇 가지 텍스트 메시지를 입력한 다음 **Ctrl+C**를 사용하여 토픽 전송을 중지합니다. 각 행은 별도의 레코드로 전송됩니다.

2. Kafka와 함께 제공된 다음 스크립트를 사용하여 토픽에서 레코드를 읽습니다.
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
    ```
   
    이렇게 하면 토픽에서 레코드를 검색하여 표시합니다. `--from-beginning`을 사용하면 스트림 시작 부분부터 시작하도록 소비자에 지시하여 모든 레코드를 검색합니다.

3. __Ctrl+C__를 사용하여 소비자를 중지합니다.

## <a name="producer-and-consumer-api"></a>생산자 및 소비자 API

[Kafka API](http://kafka.apache.org/documentation#api)를 사용하면 프로그래밍 방식으로 레코드를 생성하고 소비할 수도 있습니다. Java 기반 생산자 및 소비자를 다운로드하고 빌드하려면 다음 단계를 사용합니다.

1. [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)에서 예제를 다운로드합니다. 생산자/소비자 예제의 경우 `Producer-Consumer` 디렉터리의 프로젝트를 사용합니다. 이 예제에는 다음과 같은 클래스가 포함되어 있습니다.
   
    * **Run** - 소비자 또는 생산자 중 하나를 시작합니다.

    * **Producer** - 1,000,000개 레코드를 토픽에 저장합니다.

    * **Consumer** - 토픽에서 레코드를 읽습니다.

2. 개발 환경의 명령줄에서 예제의 `Producer-Consumer` 디렉터리 위치로 디렉터리를 변경한 후 다음 명령을 사용하여 jar 패키지를 만듭니다.
   
    ```
    mvn clean package
    ```
   
    이 명령은 `kafka-producer-consumer-1.0-SNAPSHOT.jar`라는 파일이 포함된 `target`이라는 디렉터리를 만듭니다.

3. 다음 명령을 사용하여 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 파일을 HDInsight 클러스터에 복사합니다.
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    **SSHUSER**는 클러스터의 SSH 사용자로 바꾸고, **CLUSTERNAME**은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자의 암호를 입력합니다.

4. `scp` 명령으로 파일 복사를 완료하면 SSH를 사용하여 클러스터에 연결한 후 다음을 사용하여 이전에 만든 test 토픽에 레코드를 기록합니다.
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS
    ```
   
    이 명령은 생산자를 시작하고 레코드를 기록합니다. 기록된 레코드 수를 확인할 수 있도록 카운터가 표시됩니다.

    > [!NOTE]
    > 권한 거부 오류가 표시되면 다음 명령을 사용하여 파일을 실행하도록 만듭니다. ```chmod +x kafka-producer-consumer.jar```

5. 프로세스가 완료되면 다음 명령을 사용하여 토픽에서 읽습니다.
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    레코드 수와 함께 읽은 레코드가 표시됩니다. 이전 단계의 스크립트를 사용하여 몇 가지 레코드를 토픽으로 보내면 1,000,000개 이상이 표시될 수 있습니다.

6. __Ctrl+C__를 사용하여 소비자를 종료합니다.

### <a name="multiple-consumers"></a>여러 소비자

Kafka의 중요한 개념은 소비자에서 레코드를 읽을 때 그룹 ID로 정의된 소비자 그룹을 사용한다는 것입니다. 여러 소비자와 같은 그룹을 사용하면 부하가 분산되어 토픽에서 읽습니다. 그룹의 각 소비자는 레코드의 일부를 받습니다. 동작 중인 이 프로세스를 확인하려면 다음 단계를 사용합니다.

1. 클러스터에 대한 새로운 SSH 세션을 열어 두 세션을 갖도록 합니다. 각 세션에서 다음을 사용하여 동일한 소비자 그룹 ID를 가진 소비자를 시작합니다.
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```

    > [!NOTE]
    > 이 세션은 새로운 SSH 세션이므로 [Zookeeper 및 Broker 호스트 정보 가져오기](#getkafkainfo) 섹션의 명령을 사용하여 `$KAFKABROKERS`를 설정해야 합니다.

2. 각 세션에서 토픽으로부터 받은 레코드를 계산하는 것을 지켜봅니다. 두 세션의 합계는 앞서 한 소비자로부터 받은 것과 같아야 합니다.

동일한 그룹 내에서 클라이언트에 의한 소비는 토픽에 대한 파티션을 통해 처리됩니다. 앞에서 만든 `test` 토픽에는 8개의 파티션이 있습니다. 8개 SSH 세션을 열고 모든 세션에서 소비자를 시작하면 각 소비자는 해당 토픽에 대한 단일 파티션에서 레코드를 읽습니다.

> [!IMPORTANT]
> 소비자 그룹에는 파티션보다 더 많은 소비자 인스턴스가 있을 수 없습니다. 이 예제에서 하나의 소비자 그룹은 토픽의 파티션 수이기 때문에 최대 8개 소비자를 포함할 수 있습니다. 또는 소비자가 8개 이하인 소비자 그룹이 여러 개 있을 수 있습니다.

Kafka에 저장된 레코드는 파티션에서 받은 순서대로 저장됩니다. *파티션 내의* 레코드에 대해 순서대로 전달하려면 파티션 수와 일치하는 소비자 인스턴스가 있는 소비자 그룹을 만듭니다. *토픽 내의* 레코드에 대해 순서대로 전달하려면 하나의 소비자 인스턴스만 사용하는 소비자 그룹을 만듭니다.

## <a name="streaming-api"></a>스트리밍 API

스트리밍 API 버전 0.10.0이 Kafka에 추가되었습니다. 이전 버전은 스트림 처리를 위해 Apache Spark 또는 Storm에 의존합니다.

1. 아직 그렇게 하지 않았으면 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)에서 개발 환경으로 예제를 다운로드합니다. 스트리밍 예제의 경우 `streaming` 디렉터리의 프로젝트를 사용합니다.
   
    이 프로젝트에는 이전에 만들어진 `test` 토픽에서 레코드를 읽는 `Stream` 클래스 하나만 있습니다. 읽은 단어를 집계하여 각 단어를 내보내고 `wordcounts`라는 토픽에 집계합니다. `wordcounts` 토픽은 이 섹션의 이후 단계에서 만들어집니다.

2. 개발 환경의 명령줄에서 `Streaming` 디렉터리 위치로 디렉터리를 변경한 후 다음 명령을 사용하여 jar 패키지를 만듭니다.
   
    ```
    mvn clean package
    ```
   
    이 명령은 `kafka-streaming-1.0-SNAPSHOT.jar`라는 파일이 포함된 `target`이라는 디렉터리를 만듭니다.

3. 다음 명령을 사용하여 `kafka-streaming-1.0-SNAPSHOT.jar` 파일을 HDInsight 클러스터에 복사합니다.
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    **SSHUSER**는 클러스터의 SSH 사용자로 바꾸고, **CLUSTERNAME**은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자의 암호를 입력합니다.

4. `scp` 명령으로 파일 복사를 완료하면 SSH를 사용하여 클러스터에 연결한 후 다음 명령을 사용하여 `wordcounts` 토픽을 만듭니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

5. 그런 후 다음 명령을 사용하여 스트리밍 프로세스를 시작합니다.
   
    ```bash
    ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
    ```
   
    이 명령은 백그라운드에서 스트리밍 프로세스를 시작합니다.

6. 다음 명령을 사용하여 `test` 토픽으로 메시지를 보냅니다. 스트리밍 예제에서 처리되는 이러한 메시지는 다음과 같습니다.
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &
    ```

7. 다음 명령을 사용하여 스트리밍 프로세스에 의해 `wordcounts` 토픽에 기록된 출력을 봅니다.
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
    ```
   
    > [!NOTE]
    > 데이터를 보려면 소비자에게 키를 인쇄하고 역직렬 변환기에 키와 값을 사용하도록 알려주어야 합니다. 키 이름은 단어이며, 키 값에는 개수가 포함됩니다.
   
    다음 텍스트와 유사하게 출력됩니다.
   
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
        a       13805
        snow    13637
   
    > [!NOTE]
    > 단어가 발생할 때마다 개수가 증가합니다.

7. __Ctrl+C__를 사용하여 소비자를 종료한 다음 `fg` 명령을 사용하여 스트리밍 백그라운드 작업을 다시 포그라운드 상태로 만듭니다. 마찬가지로 __Ctrl+C__를 사용하여 종료합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서 HDInsight에서 Apache Kafka를 사용할 때 필요한 기본 사항을 알아보았습니다. Kafka 작업에 대해 자세히 알아보려면 다음을 사용하세요.

* kafka.apache.org의 [Apache Kafka 문서](http://kafka.apache.org/documentation.html)
* [MirrorMaker를 사용하여 HDInsight에 Kafka 복제본 만들기](hdinsight-apache-kafka-mirroring.md)
* [HDInsight의 Kafka에서 Apache Storm 사용](hdinsight-apache-storm-with-kafka.md)
* [HDInsight의 Kafka에서 Apache Spark 사용](hdinsight-apache-spark-with-kafka.md)


