---
title: HDInsight의 Storm에서 Apache Kafka 사용 - Azure | Microsoft Docs
description: Apache Kafka는 HDInsight에 Apache Storm과 함께 설치됩니다. Storm에서 제공하는 KafkaBolt 및 KafkaSpout 구성 요소를 사용하여 Kafka에(서) 쓰고 읽는 방법에 대해 알아봅니다. 또한 Flux 프레임워크를 사용하여 Storm 토폴로지를 정의하고 제출하는 방법도 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: larryfr
ms.openlocfilehash: be62705ce0217235b75ec5ad220ad6f32dfd3c10
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-apache-kafka-with-storm-on-hdinsight"></a>HDInsight의 Storm에서 Apache Kafka 사용

Apache Storm을 사용하여 Apache Kafka에서 읽고 쓰는 방법에 대해 알아봅니다. 이 예제에서는 HDInsight를 사용하여 Storm 토폴로지의 데이터를 HDFS 호환 파일 시스템에 저장하는 방법도 설명합니다.

> [!NOTE]
> 이 문서의 단계는 HDInsight의 Storm과 HDInsight의 Kafka 클러스터를 모두 포함하는 Azure 리소스 그룹을 만듭니다. 이러한 클러스터는 모두 Azure Virtual Network에 있으며, 여기서는 Storm 클러스터와 Kafka 클러스터 간에 직접 통신할 수 있습니다.
> 
> 이 문서의 단계를 완료하는 경우 과도한 요금이 청구되지 않도록 클러스터를 삭제해야 합니다.

## <a name="get-the-code"></a>코드 가져오기

이 문서에 사용된 예제에 대한 코드는 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka)에서 지원됩니다.

이 프로젝트를 컴파일하기 위해 개발 환경에 필요한 구성 요소는 다음과 같습니다.

* [Java JDK 1.8](http://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) 이상 - HDInsight 3.5 이상에는 Java 8이 필요합니다.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* SSH 클라이언트(`ssh` 및 `scp` 명령 필요) - 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조합니다.

* 텍스트 편집기 또는 IDE

Java 및 JDK를 설치할 때 사용자의 개발 워크스테이션에 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* `JAVA_HOME` - JDK가 설치된 디렉터리를 가리켜야 합니다.
* `PATH` - 다음 경로를 포함해야 합니다.
  
    * `JAVA_HOME`(또는 이와 동등한 경로)
    * `JAVA_HOME\bin`(또는 이와 동등한 경로)
    * Maven이 설치된 디렉터리

## <a name="create-the-clusters"></a>클러스터 만들기

HDInsight의 Apache Kafka는 공용 인터넷을 통한 액세스를 Kafka broker에 제공하지 않습니다. Kafka와 통신하는 대상은 Kafka 클러스터의 노드와 동일한 Azure 가상 네트워크에 있어야 합니다. 여기서는 Kafka 클러스터와 Storm 클러스터가 모두 Azure 가상 네트워크에 있습니다. 클러스터 간의 통신 흐름을 보여 주는 다이어그램은 다음과 같습니다.

![Azure 가상 네트워크에 있는 Storm 및 Kafka 클러스터 다이어그램](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> SSH 및 Ambari와 같은 클러스터의 다른 서비스는 인터넷을 통해 액세스할 수 있습니다. HDInsight에서 사용할 수 있는 공용 포트에 대한 자세한 내용은 [HDInsight에서 사용하는 포트 및 URI](hdinsight-hadoop-port-settings-for-services.md)를 참조하세요.

Azure 가상 네트워크, Kafka 클러스터 및 Storm 클러스터를 수동으로 만들 수 있지만 Azure Resource Manager 템플릿을 사용하는 것이 더 쉽습니다. 다음 단계에 따라 Azure 가상 네트워크, Kafka 클러스터 및 Storm 클러스터를 Azure 구독에 배포합니다.

1. Azure에 로그인하고 Azure Portal에서 템플릿을 열려면 다음 단추를 사용합니다.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager 템플릿의 위치는 **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json**입니다. 이 템플릿은 다음 리소스를 만듭니다.
    
    * Azure 리소스 그룹
    * Azure Virtual Network
    * Azure Storage 계정
    * HDInsight 버전 3.6의 Kafka(작업자 노드 3개)
    * HDInsight 버전 3.6의 Storm(작업자 노드 3개)

  > [!WARNING]
  > HDInsight에서 Kafka의 사용 가능성을 보장하려면 클러스터에 작업자 노드가 3개 이상 포함되어야 합니다. 이 템플릿은 세 개의 작업자 노드를 포함하는 Kafka 클러스터를 만듭니다.

2. 다음 지침을 사용하여 **사용자 지정 배포** 섹션의 항목을 채웁니다.
   
    ![HDInsight 사용자 지정 배포](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **리소스 그룹**: 그룹을 만들거나 기존 그룹을 선택합니다. 이 그룹에는 HDInsight 클러스터가 포함됩니다.
   
    * **위치**: 지리적으로 가까운 위치를 선택합니다.

    * **기본 클러스터 이름**: 이 값은 Storm 및 Kafka 클러스터의 기본 이름으로 사용됩니다. 예를 들어 **hdi**를 입력하면 **storm-hdi**라는 Storm 클러스터와 **kafka-hdi**라는 Kafka 클러스터가 만들어집니다.
   
    * **클러스터 로그인 사용자 이름**: Storm 및 Kafka 클러스터의 관리자 이름입니다.
   
    * **클러스터 로그인 암호**: Storm 및 Kafka 클러스터의 관리자 사용자 암호입니다.
    
    * **SSH 사용자 이름**: Storm 및 Kafka 클러스터에 만들 SSH 사용자입니다.
    
    * **SSH 암호**: Storm 및 Kafka 클러스터에 대한 SSH 사용자의 암호입니다.

3. **사용 약관**을 읽은 다음 **위에 명시된 사용 약관에 동의함**을 선택합니다.

4. 마지막으로 **대시보드에 고정**을 선택한 다음 **구매**를 선택합니다. 클러스터를 만드는 데 약 20분이 걸립니다.

리소스를 만들면 리소스 그룹의 섹션이 표시됩니다.

![VNet 및 클러스터에 대한 리소스 그룹 섹션](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> HDInsight 클러스터의 이름은 **storm-BASENAME** 및 **kafka-BASENAME**이며, 여기서 BASENAME은 템플릿에 제공된 이름입니다. 이후 단계에서 클러스터에 연결할 때 이러한 이름을 사용합니다.

## <a name="understanding-the-code"></a>코드 이해

이 프로젝트에는 다음 두 가지 토폴로지가 있습니다.

* **KafkaWriter**: **writer.yaml** 파일에 정의된 토폴로지이며, Apache Storm에서 제공하는 KafkaBolt를 사용하여 Kafka에 임의의 문장을 씁니다.

    이 토폴로지는 사용자 지정 **SentenceSpout** 구성 요소를 사용하여 임의의 문장을 생성합니다.

* **KafkaReader**: **reader.yaml** 파일에 정의된 토폴로지이며, Apache Storm에서 제공하는 KafkaSpout을 사용하여 Kafka에서 데이터를 읽은 다음 stdout에 데이터를 로깅합니다.

    이 토폴로지는 Storm HdfsBolt를 사용하여 Storm 클러스터의 기본 저장소에 데이터를 씁니다.
### <a name="flux"></a>Flux

토폴로지는 [Flux](https://storm.apache.org/releases/1.1.2/flux.html)를 사용하여 정의됩니다. Flux는 Storm 0.10.x에서 소개되었으며, 토폴로지 구성을 코드에서 분리할 수 있습니다. Flux 프레임워크를 사용하는 토폴로지의 경우 토폴로지는 YAML 파일에 정의됩니다. YAML 파일은 토폴로지의 일부로 포함할 수 있습니다. 또는 토폴로지를 전송할 때 독립 실행형 파일로 사용할 수도 있습니다. Flux는 런타임 시 변수 대체도 지원하며, 이 예제에서 사용됩니다.

이러한 토폴로지에 대해 런타임에 다음 매개 변수가 설정됩니다.

* `${kafka.topic}`: 토폴리지에서 읽고 쓰는 Kafka 토픽의 이름입니다.

* `${kafka.broker.hosts}`: Kafka broker가 실행되는 호스트입니다. broker 정보는 Kafka에 쓸 때 KafkaBolt에서 사용합니다.

* `${kafka.zookeeper.hosts}`: Kafka 클러스터에서 Zookeeper가 실행되는 호스트입니다.

Flux 토폴로지에 대한 자세한 내용은 [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html)을 참조하세요.

## <a name="download-and-compile-the-project"></a>프로젝트 다운로드 및 컴파일

1. 개발 환경에서 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka)로부터 프로젝트를 다운로드하고, 명령줄을 열어 다운로드한 위치로 디렉터리를 변경합니다.

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

## <a name="configure-the-topology"></a>토폴로지 구성

1. 다음 방법 중 하나를 사용하여 HDInsight 클러스터에서 **Kafka**에 대한 Kafka broker 호스트를 검색합니다.

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]
    > 다음 Bash 예제에서는 `$CLUSTERNAME`에 __Kafka__ 클러스터 이름이 포함되어 있다고 가정합니다. 또한 [jq](https://stedolan.github.io/jq/) 버전 1.5 이상이 설치되어 있다고 가정합니다. 메시지가 표시되면 클러스터 로그인 계정에 대한 암호를 입력합니다.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    반환되는 값은 다음 텍스트와 유사합니다.

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > 클러스터에 대해 두 개 이상의 브로커 호스트가 있을 수 있습니다. 클라이언트에 대한 모든 호스트의 전체 목록을 제공할 필요는 없습니다. 하나 또는 두 개로도 충분합니다.

2. 다음 방법 중 하나를 사용하여 HDInsight 클러스터에서 __Kafka__에 대한 Zookeeper 호스트를 검색합니다.

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]
    > 다음 Bash 예제에서는 `$CLUSTERNAME`에 __Kafka__ 클러스터가 포함되어 있다고 가정합니다. [jq](https://stedolan.github.io/jq/)도 설치되어 있다고 가정합니다. 메시지가 표시되면 클러스터 로그인 계정에 대한 암호를 입력합니다.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    반환되는 값은 다음 텍스트와 유사합니다.

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > 두 개 이상의 Zookeeper 노드가 있습니다. 클라이언트에 대한 모든 호스트의 전체 목록을 제공할 필요는 없습니다. 하나 또는 두 개로도 충분합니다.

    나중에 사용하므로 이 값을 저장합니다.

3. 프로젝트의 루트에서 `dev.properties` 파일을 편집합니다. 이 파일에서 일치하는 줄에 __Kafka__ 클러스터에 대한 Broker 및 Zookeeper 호스트 정보를 추가합니다. 다음 예제는 이전 단계의 예제 값을 사용하여 구성됩니다.

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. `dev.properties` 파일을 저장하고 다음 명령을 사용하여 **Storm** 클러스터로 업로드합니다.

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    **USERNAME**은 클러스터의 SSH 사용자 이름으로 바꿉니다. **BASENAME**은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

## <a name="start-the-writer"></a>기록기 시작

> [!IMPORTANT]
> 이 섹션의 단계에서는 이 문서의 Azure Resource Manager 템플릿 링크를 사용하여 Storm 및 Kafka 클러스터를 만든다고 가정합니다. 이 템플릿을 사용하여 Kafka 클러스터에 대한 토픽을 자동으로 만들 수 있습니다.
>
> 기본적으로 HDInsight의 Kafka는 토픽의 자동 만들기를 허용하지 않으므로 다른 방법으로 Kafka 클러스터를 만든 경우 토픽을 수동으로 만들어야 합니다. 토픽을 수동으로 만드는 방법에 대한 내용은 [HDInsight의 Kafka로 시작](./kafka/apache-kafka-get-started.md) 문서를 참조하세요.

1. 다음을 사용하여 SSH를 통해 **Storm** 클러스터에 연결합니다. **USERNAME**은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. **BASENAME**은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    메시지가 표시되면 클러스터를 만들 때 사용한 암호를 입력합니다.
   
    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. Storm 클러스터에 대한 SSH 연결에서 다음 명령을 사용하여 기록기 토폴로지를 시작합니다.

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * `org.apache.storm.flux.Flux`: Flux를 사용하여 이 토폴로지를 구성하고 실행합니다.

    * `--remote`: Nimbus에 토폴로지를 제출합니다. 토폴로지가 클러스터의 작업자 노드에 분산됩니다.

    * `-R /writer.yaml`: `writer.yaml` 파일을 사용하여 토폴로지를 구성합니다. `-R`은 이 리소스가 jar 파일에 포함되어 있는지를 나타내며, jar의 루트에 있으므로 `/writer.yaml`이 그 경로입니다.

    * `--filter`: `dev.properties` 파일의 값을 사용하여 `writer.yaml` 토폴로지의 항목을 입력합니다. 예를 들어 파일에서 `kafka.topic` 항목의 값으로 토폴로지 정의의 `${kafka.topic}` 항목을 바꿉니다.

5. 토폴로지가 시작되면 다음 명령을 사용하여 Kafka 토픽에 데이터를 쓰는지 확인합니다.

    > [!IMPORTANT]
    > `$KAFKAZKHOSTS`를 __Kafka__ 클러스터에 대한 Zookeeper 호스트 정보로 바꿉니다.

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
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

> [!NOTE]
> Storm UI에서 판독기를 볼 때 __토폴로지 spout 지연 오류__ 섹션이 표시될 수 있습니다. 이 예제에서는 이 오류를 무시할 수 있습니다.

1. Storm 클러스터에 대한 SSH 세션에서 다음 명령을 사용하여 판독기 토폴로지를 시작합니다.

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. 토폴로지가 시작되면 Storm UI를 엽니다. 이 웹 UI는 `https://storm-BASENAME.azurehdinsight.net/stormui`에 있습니다. __BASENAME__은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다. 

    메시지가 표시되면 클러스터를 만들 때 사용한 관리자 로그인 이름(기본값: `admin`)과 암호를 사용합니다. 다음 이미지와 비슷한 웹 페이지가 표시됩니다.

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

이 문서의 단계를 수행하면 동일한 Azure 리소스 그룹에 두 클러스터가 모두 만들어지므로 Azure Portal에서 해당 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹을 삭제하면 이 문서에 따라 만든 리소스가 모두 제거됩니다.

## <a name="next-steps"></a>다음 단계

HDInsight의 Storm에서 사용할 수 있는 더 많은 예제 토폴로지는 [예제 Storm 토폴로지 및 구성 요소](storm/apache-storm-example-topology.md)를 참조하세요.

Linux 기반 HDInsight에서 토폴로지 배포 및 모니터링에 대한 정보는 [Linux 기반 HDInsight에서 Apache Storm 토폴로지 배포 및 관리](storm/apache-storm-deploy-monitor-topology-linux.md)를 참조하세요.
