---
title: "HDInsight의 Storm에서 Apache Kafka 사용 | Microsoft 문서"
description: "Apache Kafka는 HDInsight에 Apache Storm과 함께 설치됩니다. Storm에서 제공하는 KafkaBolt 및 KafkaSpout 구성 요소를 사용하여 Kafka에(서) 쓰고 읽는 방법에 대해 알아봅니다. 또한 Flux 프레임워크를 사용하여 Storm 토폴로지를 정의하고 제출하는 방법도 알아봅니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: dcda5e27cbcadff054c8085b72a1b6fb1c07b889
ms.lasthandoff: 03/25/2017

---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>HDInsight의 Storm에서 Apache Kafka(미리 보기) 사용

Apache Kafka는 HDInsight에서 사용할 수 있는 게시-구독 메시징 솔루션이며, Apache Storm은 실시간으로 데이터를 분석하는 데 사용할 수 있는 분산 시스템입니다. 이 문서에서는 HDInsight에서 Storm을 사용하여 HDInsight에서 Kafka의 데이터를 읽고 처리하는 방법을 보여 줍니다. 이 문서의 예제는 Apache Storm에서 사용할 수 있는 Kafka spout 및 bolt 구성 요소에 속한 Java 기반 Storm 토폴로지를 사용합니다.

> [!NOTE]
> 이 문서의 단계는 HDInsight의 Storm과 HDInsight의 Kafka 클러스터를 모두 포함하는 Azure 리소스 그룹을 만듭니다. 이러한 클러스터는 모두 Azure Virtual Network에 있으며, 여기서는 Storm 클러스터와 Kafka 클러스터 간에 직접 통신할 수 있습니다.
> 
> 이 문서의 단계를 완료하는 경우 과도한 요금이 청구되지 않도록 클러스터를 삭제해야 합니다.

## <a name="prerequisites"></a>필수 조건

* [JDK Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.8 이상 또는 이와 동등한 프로그램(예: [OpenJDK](http://openjdk.java.net/))
  
    > [!NOTE]
    > 이 문서의 단계에서는 Java 8을 사용하는 HDInsight 3.5 클러스터를 사용합니다.

* [Maven 3.x](http://maven.apache.org/) - Java 응용 프로그램용 빌드 관리 패키지

* 텍스트 편집기 또는 Java IDE

* SSH 클라이언트(`ssh` 및 `scp` 명령 필요) - 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조합니다.

## <a name="create-the-clusters"></a>클러스터 만들기

HDInsight의 Apache Kafka는 공용 인터넷을 통한 액세스를 Kafka broker에 제공하지 않습니다. Kafka와 통신하는 대상은 Kafka 클러스터의 노드와 동일한 Azure 가상 네트워크에 있어야 합니다. 여기서는 Kafka 클러스터와 Storm 클러스터가 모두 Azure 가상 네트워크에 있습니다. 클러스터 간의 통신 흐름을 보여 주는 다이어그램은 다음과 같습니다.

![Azure 가상 네트워크에 있는 Storm 및 Kafka 클러스터 다이어그램](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Kafka 자체는 가상 네트워크 내의 통신으로 제한되지만, 클러스터의 다른 서비스(예: SSH, Ambari)는 인터넷을 통해 액세스할 수 있습니다. HDInsight에서 사용할 수 있는 공용 포트에 대한 자세한 내용은 [HDInsight에서 사용하는 포트 및 URI](hdinsight-hadoop-port-settings-for-services.md)를 참조하세요.


Azure 가상 네트워크, Kafka 클러스터 및 Storm 클러스터를 수동으로 만들 수 있지만 Azure Resource Manager 템플릿을 사용하는 것이 더 쉽습니다. 다음 단계에 따라 Azure 가상 네트워크, Kafka 클러스터 및 Storm 클러스터를 Azure 구독에 배포합니다.

1. Azure에 로그인하고 Azure Portal에서 템플릿을 열려면 다음 단추를 사용합니다.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager 템플릿은 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet.json**에 있습니다.

2. 다음 지침을 사용하여 **사용자 지정 배포** 블레이드의 항목을 채웁니다.
   
    ![HDInsight 사용자 지정 배포](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **리소스 그룹**: 그룹을 만들거나 기존 그룹을 선택합니다. 이 그룹에는 HDInsight 클러스터가 포함됩니다.
   
    * **위치**: 지리적으로 가까운 위치를 선택합니다. 이 위치는 __SETTINGS__ 섹션의 위치와 일치해야 합니다.

    * **기본 클러스터 이름**: 이 값은 Storm 및 Kafka 클러스터의 기본 이름으로 사용됩니다. 예를 들어 **hdi**를 입력하면 **storm-hdi**라는 Storm 클러스터와 **kafka-hdi**라는 Kafka 클러스터가 만들어집니다.
   
    * **클러스터 로그인 사용자 이름**: Storm 및 Kafka 클러스터의 관리자 이름입니다.
   
    * **클러스터 로그인 암호**: Storm 및 Kafka 클러스터의 관리자 사용자 암호입니다.
    
    * **SSH 사용자 이름**: Storm 및 Kafka 클러스터에 만들 SSH 사용자입니다.
    
    * **SSH 암호**: Storm 및 Kafka 클러스터에 대한 SSH 사용자의 암호입니다.
    
    * **위치**: 클러스터가 만들어지는 지역입니다.

3. **사용 약관**을 읽은 다음 **위에 명시된 사용 약관에 동의함**을 선택합니다.

4. 마지막으로 **대시보드에 고정**을 선택한 다음 **구매**를 선택합니다. 클러스터를 만드는 데 약 20분이 걸립니다.

리소스를 만들면 클러스터 및 웹 대시보드를 포함하는 리소스 그룹에 대한 블레이드로 리디렉션됩니다.

![vnet 및 클러스터에 대한 리소스 그룹 블레이드](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> HDInsight 클러스터의 이름은 **storm-BASENAME** 및 **kafka-BASENAME**이며, 여기서 BASENAME은 템플릿에 제공된 이름입니다. 이후 단계에서 클러스터에 연결할 때 이러한 이름을 사용합니다.

## <a name="get-the-code"></a>코드 가져오기

이 문서에서 설명하는 예제 코드는 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka)에서 제공됩니다.

## <a name="understanding-the-code"></a>코드 이해

이 프로젝트에는 다음 두 가지 토폴로지가 있습니다.

* **KafkaWriter**: **writer.yaml** 파일에 정의된 토폴로지이며, Apache Storm에서 제공하는 KafkaBolt를 사용하여 Kafka에 임의의 문장을 씁니다.
  
    이 토폴로지는 사용자 지정 **SentenceSpout** 구성 요소를 사용하여 임의의 문장을 생성합니다.

* **KafkaReader**: **reader.yaml** 파일에 정의된 토폴로지이며, Apache Storm에서 제공하는 KafkaSpout을 사용하여 Kafka에서 데이터를 읽은 다음 stdout에 데이터를 로깅합니다.
  
    이 토폴로지는 사용자 지정 **PrinterBolt** 구성 요소를 사용하여 Kafka에서 읽은 데이터를 로깅합니다.

### <a name="flux"></a>Flux

토폴로지는 [Flux](https://storm.apache.org/releases/1.0.1/flux.html)를 사용하여 정의됩니다. Flux는 Storm 0.10.x에서 소개되었으며, 토폴로지 구성을 코드에서 분리할 수 있습니다. Flux 프레임워크를 사용하는 토폴로지의 경우 토폴로지는 YAML 파일에 정의됩니다. YAML 파일은 토폴로지의 일부로 포함되거나 토폴로지를 Storm 서버에 제출할 때 지정할 수 있습니다. Flux는 런타임 시 변수 대체도 지원하며, 이 예제에서 사용됩니다.

두 토폴로지에는 다음과 같은 환경 변수가 필요합니다.

* **KAFKATOPIC**: 토폴로지에서 읽고 쓰는 Kafka 토픽의 이름입니다.

* **KAFKABROKERS**: Kafka broker가 실행되는 호스트입니다. broker 정보는 Kafka에 쓸 때 KafkaBolt에서 사용합니다.

* **KAFKAZKHOSTS**: Zookeeper가 실행되는 호스트입니다.

이 문서의 단계는 이러한 환경 변수를 설정하는 방법을 보여 줍니다.

## <a name="create-a-kafka-topic"></a>Kafka 토픽 만들기

1. SSH를 사용하여 Kafka 클러스터에 연결합니다. `USERNAME`은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. `BASENAME`은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.
   
        ssh USERNAME@kafka-BASENAME-ssh.azurehdinsight.net
   
    메시지가 표시되면 클러스터를 만들 때 사용한 암호를 입력합니다.
   
    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. SSH 연결에서 Kafka 클러스터로 다음 명령을 사용하여 HTTP 로그인 및 클러스터 이름에 대한 변수를 설정합니다. 이 섹션의 다른 단계에서 이러한 값을 사용합니다.

  ```bash
  ADMIN='admin' #replace with the name of the admin account for the cluster
  PASSWORD='password' #replace with the password for the admin account
  ```

3. 다음 명령을 사용하여 `jq` 유틸리티를 설치하고 클러스터 이름을 검색하고 `KAFKAZKHOSTS` 변수를 설정합니다.

  ```bash
  sudo apt -y install jq
  CLUSTERNAME=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name'`
  KAFKAZKHOSTS=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`
  ```

    다음 명령을 사용하여 클러스터 이름을 검색합니다.

  ```bash
  echo $CLUSTERNAME
  ```

    이 명령의 출력은 다음 예제와 비슷합니다.

  ```bash
  kafka-myhdi
  ```

    다음 명령을 사용하여 `KAFKAZKHOSTS`이 올바로 설정되어 있는지 확인합니다.

  ```bash
  echo $KAFKAZKHOSTS
  ```

    이 명령의 출력은 다음 예제와 비슷합니다.

  ```bash
  zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181
  ```

    Storm 클러스터에서 토폴로지를 시작할 때 이러한 값이 사용되므로 Kafka 클러스터 이름 및 Zookeeper 호스트 정보를 저장합니다.

    > [!NOTE]
    > 이전 명령은 Ambari에 직접 연결되는 __http://headnodehost:8080/__을 사용합니다. 이 정보를 클러스터 외부에서 인터넷을 통해 검색해야 하는 경우 __https://kafka-BASENAME.azurehdinsight.net/__을 대신으로 사용해야 합니다.

4. 다음 명령을 사용하여 Kafka에 토픽을 만듭니다.

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic stormtest --zookeeper $KAFKAZKHOSTS
  ```

    이 명령은 `$KAFKAZKHOSTS`에 저장된 호스트 정보를 사용하여 Zookeeper에 연결한 다음 **stormtest**라는 Kafka 토픽을 만듭니다. 토픽을 나열하는 다음 명령을 사용하면 해당 토픽이 만들어졌는지 확인할 수 있습니다.

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
  ```

    이 명령의 출력에서 Kafka 토픽을 나열하는데, 새로운 **stormtest** 토픽이 포함되어 있어야 합니다.

Kafka 클러스터에 대한 SSH 연결은 Storm 토폴로지에서 메시지를 토픽에 쓰는지 확인하는 데 사용할 수 있으므로 활성 상태로 유지합니다.

## <a name="download-and-compile-the-project"></a>프로젝트 다운로드 및 컴파일

1. 개발 환경에서 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka)로부터 프로젝트를 다운로드하고, 명령줄을 열어 다운로드한 위치로 디렉터리를 변경합니다.

    잠시 시간을 내어 코드를 살펴보고 프로젝트의 작동 방식을 이해합니다.

2. **hdinsight-storm-java-kafka** 디렉터리에서 다음 명령을 사용하여 프로젝트를 컴파일하고 배포할 패키지를 만듭니다.

  ```bash
  mvn clean package
  ```

    패키지 프로세스는 `target` 디렉터리에 `KafkaTopology-1.0-SNAPSHOT.jar`라는 파일을 만듭니다.

3. 다음 명령을 사용하여 HDInsight 클러스터의 Storm에 패키지를 복사합니다. **USERNAME**은 클러스터의 SSH 사용자 이름으로 바꿉니다. **BASENAME**은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    메시지가 표시되면 클러스터를 만들 때 사용한 암호를 입력합니다.

4. 다음 명령을 사용하여 프로젝트의 `scripts` 디렉터리에서 Storm 클러스터로 `set-env-variables.sh` 파일을 복사합니다.

  ```bash
  scp ./scripts/set-env-variables.sh USERNAME@storm-BASENAME-ssh.azurehdinsight.net:set-env-variables.sh
  ```

    이 스크립트는 Storm 토폴로지에서 Kafka 클러스터와 통신하는 데 사용하는 환경 변수를 설정하는 데 사용됩니다.

## <a name="start-the-writer"></a>기록기 시작

1. 다음을 사용하여 SSH를 사용하는 Storm 클러스터에 연결합니다. **USERNAME**은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. **BASENAME**은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    메시지가 표시되면 클러스터를 만들 때 사용한 암호를 입력합니다.
   
    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. Storm 클러스터에 대한 SSH 연결에서 다음 명령을 사용하여 `set-env-variables.sh` 스크립트를 실행합니다.

  ```bash
  chmod +x set-env-variables.sh
  . ./set-env-variables.sh KAFKACLUSTERNAME PASSWORD
  ```

    __KAFKACLUSTERNAME__은 Kafka 클러스터의 이름으로 바꿉니다. __PASSWORD__는 Kafka 클러스터의 관리자 로그인 암호로 바꿉니다.

    이 스크립트는 Kafka 클러스터에 연결하여 Kafka broker 및 Zookeeper 호스트의 목록을 검색합니다. 그런 다음 해당 정보를 Storm 토폴로지에서 사용하는 환경 변수에 저장합니다.

    스크립트의 출력은 다음 예제와 비슷합니다.

        Checking for jq: install ok installed
        Exporting variables:
        $KAFKATOPIC=stormtest
        $KAFKABROKERS=wn0-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092
        $KAFKAZKHOSTS=zk1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk3-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk5-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181

3. Storm 클러스터에 대한 SSH 연결에서 다음 명령을 사용하여 기록기 토폴로지를 시작합니다.

        storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml -e

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * `org.apache.storm.flux.Flux`: Flux를 사용하여 이 토폴로지를 구성하고 실행합니다.

    * `--remote`: Nimbus에 토폴로지를 제출합니다. 토폴로지가 클러스터의 작업자 노드에 분산됩니다.

    * `-R /writer.yaml`: `writer.yaml` 파일을 사용하여 토폴로지를 구성합니다. `-R`은 이 리소스가 jar 파일에 포함되어 있는지를 나타내며, jar의 루트에 있으므로 `/writer.yaml`이 그 경로입니다.

    * `-e`: 환경 변수 대체를 사용합니다. Flux는 이전에 설정한 $KAFKABROKERS 및 $KAFKATOPIC 값을 선택하고 `${ENV-KAFKABROKER}` 및 `${ENV-KAFKATOPIC}` 항목 대신 reader.yaml 파일에서 이 값을 사용합니다.

5. 토폴로지가 시작되면 Kafka 클러스터에 대한 SSH 연결로 전환하고, 다음 명령을 사용하여 **stormtest** 토픽에 기록된 메시지를 봅니다.

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtest
  ```

    이 명령은 Kafka에서 제공하는 스크립트를 사용하여 토픽을 모니터링합니다. 잠시 후에 토픽에 기록된 임의의 문장을 반환하기 시작합니다. 다음 예제와 유사하게 출력됩니다.

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Ctrl+C를 사용하여 스크립트를 중지합니다.

## <a name="start-the-reader"></a>판독기 시작

1. Storm 클러스터에 대한 SSH 세션에서 다음 명령을 사용하여 판독기 토폴로지를 시작합니다.

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml -e
  ```

2. 토폴로지가 시작되면 Storm UI를 엽니다. 이 웹 UI는 https://storm-BASENAME.azurehdinsight.net/stormui에 있습니다. __BASENAME__은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다. 

    메시지가 표시되면 클러스터를 만들 때 사용한 관리자 로그인 이름(기본값: `admin`)과 암호를 사용합니다. 다음 이미지와 비슷한 웹 메시지가 표시됩니다.

    ![Storm UI](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Storm UI의 __토폴로지 요약__ 섹션에서 __kafka-reader__ 링크를 선택하여 __kafka-reader__ 토폴로지에 대한 정보를 표시합니다.

    ![Storm 웹 UI의 토폴로지 요약 섹션](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. logger-bolt 구성 요소의 인스턴스에 대한 정보를 표시하려면 __Bolt(모든 시간)__ 섹션에서 __logger-bolt__ 링크를 선택합니다.

    ![Bolt 섹션의 logger-bolt 링크](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. __실행자__ 섹션에서 __포트__ 열의 링크를 선택하여 구성 요소의 이 인스턴스에 대한 로깅 정보를 표시합니다.

    ![실행자 링크](./media/hdinsight-apache-storm-with-kafka/executors.png)

    로그에는 Kafka 토픽에서 읽은 데이터의 로그가 포함되어 있습니다. 로그 정보는 다음과 비슷합니다.

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>토폴로지 중지

Storm 클러스터에 대한 SSH 세션에서 다음 명령을 사용하여 Storm 토폴로지를 중지합니다.

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 단계를 수행하면 동일한 Azure 리소스 그룹에 두 클러스터가 모두 만들어지므로 Azure Portal에서 해당 리소스 그룹을 삭제할 수 있습니다. 이렇게 하면 이 문서의 단계를 수행하여 만들어진 모든 리소스, Azure Virtual Network 및 클러스터에서 사용하는 저장소 계정이 제거됩니다.

## <a name="next-steps"></a>다음 단계

HDInsight의 Storm에서 사용할 수 있는 더 많은 예제 토폴로지는 [예제 Storm 토폴로지 및 구성 요소](hdinsight-storm-example-topology.md)를 참조하세요.

Linux 기반 HDInsight에서 토폴로지 배포 및 모니터링에 대한 정보는 [Linux 기반 HDInsight에서 Apache Storm 토폴로지 배포 및 관리](hdinsight-storm-deploy-monitor-topology-linux.md)를 참조하세요.


