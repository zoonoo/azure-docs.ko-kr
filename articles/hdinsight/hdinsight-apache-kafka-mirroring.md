---
title: "HDInsight 클러스터에서 Apache Kafka 미러링 | Microsoft 문서"
description: "Kafka의 미러링 기능으로 보조 클러스터에 토픽을 미러링하여 HDInsight 클러스터에서 Kafka 복제본을 유지하는 방법에 대해 알아봅니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 281901045723266128db9069a6f244acb183ae80
ms.lasthandoff: 03/25/2017

---
# <a name="use-mirrormaker-to-create-a-replica-of-a-kafka-on-hdinsight-cluster-preview"></a>MirrorMaker를 사용하여 HDInsight 클러스터에서 Kafka 복제본 만들기(미리 보기)

Apache Kafka에는 한 Kafka 클러스터에서 다른 Kafka 클러스터로 토픽을 복제할 수 있는 미러링 기능이 있습니다. 예를 들어 다른 Azure 영역의 Kafka 클러스터 간에 레코드를 복제합니다.

미러링은 연속적 프로세스로 실행되거나 한 클러스터에서 다른 클러스터로 데이터를 마이그레이션하는 방법으로 단속적으로 사용될 수 있습니다.

> [!WARNING]
> 미러링이 내결함성을 달성하는 수단으로 간주되어서는 안됩니다. 토픽 내의 항목에 대한 오프셋은 원본 및 대상 클러스터 간에 서로 다르므로 클라이언트에서는 이 두 가지를 서로 교환하여 사용할 수 없습니다.
> 
> 내결함성이 염려되는 경우 클러스터 내의 토픽에 대한 복제를 설정해야 합니다. 자세한 내용은 [HDInsight에서 Kafka 시작](hdinsight-apache-kafka-get-started.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure Virtual Network: 원본 및 대상 Kafka 클러스터는 서로 직접 통신할 수 있어야 합니다. HDInsight에서는 Kafka API를 공개적으로 인터넷에 공개하지 않기 때문에 원본 및 대상 클러스터가 모두 동일한 Azure Virtual Network에 있어야 합니다.

* 2개 Kafka 클러스터: 이 문서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Virtual Network에서 2개의 Kafka를 HDInsight 클러스터에 만듭니다.

## <a name="how-does-mirroring-work"></a>미러링 작동 방식

미러링은 MirrorMaker 도구(Apache Kafka의 일부)를 사용하여 원본 클러스터의 토픽에서 레코드를 소비한 다음 대상 클러스터에 로컬 복사본을 만듭니다. MirrorMaker는 원본 클러스터에서 읽은 *소비자*(0개 이상)와 로컬(대상) 클러스터에 쓰는 *생산자*(1개)를 사용합니다.

미러링 프로세스를 보여 주는 다이어그램은 다음과 같습니다.

![미러링 프로세스 다이어그램](./media/hdinsight-apache-kafka-mirroring/kafka-mirroring.png)

원본 및 대상 클러스터는 노드와 파티션 수에 따라 다를 수 있으며 토픽 내의 오프셋도 다릅니다. 미러링은 분할에 사용되는 키 값을 유지하므로 레코드 순서는 키 기준으로 유지됩니다.

### <a name="mirroring-between-networks"></a>네트워크 간 미러링

다른 네트워크의 Kafka 클러스터 간에 미러링해야 하는 경우 다음과 같은 추가 고려 사항이 있습니다.

* **게이트웨이**: 네트워크는 TCPIP 수준에서 통신할 수 있어야 합니다.

* **이름 확인**: 각 네트워크의 Kafka 클러스터는 호스트 이름을 사용하여 서로 연결할 수 있어야 합니다. 이렇게 하려면 요청을 다른 네트워크로 전달하도록 구성된 DNS(도메인 이름 시스템) 서버가 각 네트워크에 필요할 수 있습니다. 
  
    Azure Virtual Network를 만들 때 네트워크에서 제공되는 자동 DNS를 사용하는 대신 사용자 지정 DNS 서버와 해당 서버의 IP 주소를 지정해야 합니다. 가상 네트워크를 만든 후에는 해당 IP 주소를 사용하는 Azure Virtual Network를 만든 다음 DNS 소프트웨어를 설치하고 구성해야 합니다.
  
    > [!WARNING]
    > HDInsight를 Virtual Network에 설치하기 전에 사용자 지정 DNS 서버를 만들고 구성합니다. Virtual Network에 구성된 DNS 서버를 사용하기 위해 HDInsight를 추가로 구성할 필요는 없습니다.

두 개의 Azure Virtual Network 연결에 대한 자세한 내용은 [VNet 간 연결 구성](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요.

## <a name="create-kafka-clusters"></a>Kafka 클러스터 만들기

HDInsight의 Apache Kafka는 공용 인터넷을 통한 액세스를 Kafka 서비스에 제공하지 않습니다. Kafka와 통신하는 대상은 Kafka 클러스터의 노드와 동일한 Azure 가상 네트워크에 있어야 합니다. 이 예제에서는 Kafka 원본 및 대상 클러스터가 모두 동일한 Azure 가상 네트워크에 있습니다. 클러스터 간의 통신 흐름을 보여 주는 다이어그램은 다음과 같습니다.

![Azure 가상 네트워크에 있는 원본 및 대상 Kafka 클러스터 다이어그램](./media/hdinsight-apache-kafka-mirroring/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 자체는 가상 네트워크 내의 통신으로 제한되지만, 클러스터의 다른 서비스(예: SSH, Ambari)는 인터넷을 통해 액세스할 수 있습니다. HDInsight에서 사용할 수 있는 공용 포트에 대한 자세한 내용은 [HDInsight에서 사용하는 포트 및 URI](hdinsight-hadoop-port-settings-for-services.md)를 참조하세요.

Azure 가상 네트워크와 Kafka 클러스터를 수동으로 만들 수 있지만 Azure Resource Manager 템플릿을 사용하는 것이 더 쉽습니다. 다음 단계에 따라 Azure 가상 네트워크와 두 Kafka 클러스터를 Azure 구독에 배포합니다.

1. Azure에 로그인하고 Azure Portal에서 템플릿을 열려면 다음 단추를 사용합니다.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager 템플릿은 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet.json**에 있습니다.

2. 다음 정보를 사용하여 **사용자 지정 배포** 블레이드의 항목을 채웁니다.
    
    ![HDInsight 사용자 지정 배포](./media/hdinsight-apache-kafka-mirroring/parameters.png)
    
    * **리소스 그룹**: 그룹을 만들거나 기존 그룹을 선택합니다. 이 그룹에는 HDInsight 클러스터가 포함됩니다.

    * **위치**: 지리적으로 가까운 위치를 선택합니다. 이 위치는 __SETTINGS__ 섹션의 위치와 일치해야 합니다.
     
    * **기본 클러스터 이름**: 이 값은 Kafka 클러스터의 기본 이름으로 사용됩니다. 예를 들어 **hdi**를 입력하면 **source-hdi** 및 **dest-hdi** 클러스터가 만들어집니다.

    * **클러스터 로그인 사용자 이름**: 원본 및 대상 Kafka 클러스터의 관리자 사용자 이름입니다.

    * **클러스터 로그인 암호**: 원본 및 대상 Kafka 클러스터의 관리자 사용자 암호입니다.

    * **SSH 사용자 이름**: 원본 및 대상 Kafka 클러스터에 만들 SSH 사용자입니다.

    * **SSH 암호**: 원본 및 대상 Kafka 클러스터에 대한 SSH 사용자의 암호입니다.

    * **위치**: 클러스터가 만들어지는 지역입니다.

3. **사용 약관**을 읽은 다음 **위에 명시된 사용 약관에 동의함**을 선택합니다.

4. 마지막으로 **대시보드에 고정**을 선택한 다음 **구매**를 선택합니다. 클러스터를 만드는 데 약 20분이 걸립니다.

리소스를 만들면 클러스터 및 웹 대시보드를 포함하는 리소스 그룹에 대한 블레이드로 리디렉션됩니다.

![vnet 및 클러스터에 대한 리소스 그룹 블레이드](./media/hdinsight-apache-kafka-mirroring/groupblade.png)

> [!IMPORTANT]
> HDInsight 클러스터의 이름은 **source-BASENAME** 및 **dest-BASENAME**입니다. 여기서 BASENAME은 템플릿에 제공된 이름입니다. 이후 단계에서 클러스터에 연결할 때 이러한 이름을 사용합니다.

## <a name="create-topics"></a>토픽 만들기

1. SSH를 사용하여 **원본** 클러스터에 연결합니다.
   
        ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
   
    **sshuser**은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. **BASENAME**은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.
   
    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 다음 명령을 사용하여 Zookeeper 호스트를 찾고 `SOURCE_ZKHOSTS` 변수를 설정한 다음 `testtopic`이라는 새 토픽을 여러 개 만듭니다.
   
    ```bash
    SOURCE_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. 다음 명령을 사용하여 토픽이 만들어졌는지 확인합니다.
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```
   
    응답에 `testtopic`이 있습니다.

4. 다음 명령을 사용하여 이 클러스터(**원본**)에 대한 Zookeeper 호스트 정보를 봅니다.
   
    ```bash
    echo $SOURCE_ZKHOSTS
    ```
   
 이 명령은 다음 텍스트와 비슷한 정보를 반환합니다.
   
       zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk6-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181
   
 이 정보를 저장합니다. 해당 정보는 다음 섹션에서 사용됩니다.

## <a name="configure-mirroring"></a>미러링 구성

1. 다른 SSH 세션을 사용하여 **대상** 클러스터에 연결합니다.
   
        ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
   
    **sshuser**은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. **BASENAME**은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.
   
    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 다음 명령을 사용하여 **원본** 클러스터와 통신하는 방법을 설명하는 `consumer.properties` 파일을 만듭니다.
   
    ```bash
    nano consumer.properties
    ```
   
    `consumer.properties` 파일의 내용으로 다음 텍스트를 사용합니다.
   
        zookeeper.connect=SOURCE_ZKHOSTS
        group.id=mirrorgroup
   
    **SOURCE_ZKHOSTS**를 **원본** 클러스터의 Zookeeper 호스트 정보로 바꿉니다.
   
    이 파일은 원본 Kafka 클러스터에서 읽을 때 사용할 소비자 정보를 설명합니다. 소비자 구성에 대한 자세한 내용은 kafka.apache.org의 [소비자 구성](https://kafka.apache.org/documentation#consumerconfigs)(영문)을 참조하세요.
   
    **Ctrl + X**, **Y** 및 Enter 키를 사용하여 파일을 저장합니다.

3. 대상 클러스터와 통신하는 생산자를 구성하기 전에 **대상** 클러스터에 대한 broker 호스트를 찾아야 합니다. 다음 명령을 사용하여 이 정보를 검색합니다.
   
    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    echo $DEST_BROKERHOSTS
    ```
   
    이러한 명령은 다음과 비슷한 정보를 반환합니다.
   
        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. 다음을 사용하여 **대상** 클러스터와 통신하는 방법을 설명하는 `producer.properties` 파일을 만듭니다.

    ```bash
    nano producer.properties
    ```

    `producer.properties` 파일의 내용으로 다음 텍스트를 사용합니다.
   
        bootstrap.servers=DEST_BROKERS
        compression.type=none
   
    **DEST_BROKERS**를 이전 단계의 broker 정보로 바꿉니다. 
   
    생산자 구성에 대한 자세한 내용은 kafka.apache.org의 [생산자 구성](https://kafka.apache.org/documentation#producerconfigs)(영문)을 참조하세요.

## <a name="start-mirrormaker"></a>MirrorMaker 시작

1. **대상** 클러스터에 대한 SSH 연결에서 다음 명령을 사용하여 MirrorMaker 프로세스를 시작합니다.
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```
   
    이 예제에 사용된 매개 변수는 다음과 같습니다.
   
    * **--consumer.config**: 소비자 속성이 포함된 파일을 지정합니다. 이러한 속성은 *원본* Kafka 클러스터에서 읽는 소비자를 만드는 데 사용됩니다.

    * **--producer.config**: 생산자 속성이 포함된 파일을 지정합니다. 이러한 속성은 *대상* Kafka 클러스터에 쓰는 생산자를 만드는 데 사용됩니다.

    * **--whitelist**: MirrorMaker를 통해 원본 클러스터에서 대상 클러스터로 복제하는 항목의 목록입니다.
    
    * **--num.streams**: 생성할 소비자 스레드 수입니다.
     
    시작할 때 MirrorMaker는 다음 텍스트와 비슷한 정보를 반환합니다.
        
    ```{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}  metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}  metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. From the SSH connection to the **source** cluster, use the following command to start a producer and send messages to the topic:
    
    ```bash
    sudo apt -y install jq
    SOURCE_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

 커서로 빈 라인에 도달하면 몇 개의 텍스트 메시지를 입력합니다. 이러한 메시지는 **원본** 클러스터의 토픽으로 보내집니다. 완료되면 **Ctrl + C**를 클릭하여 프로듀서 프로세스를 종료합니다.

3. **대상** 클러스터에 대한 SSH 연결에서 **Ctrl + C**를 사용하여 MirrorMaker 프로세스를 종료합니다. 그런 다음, 다음 명령을 사용하여 `testtopic` 항목이 만들어졌으며 항목의 해당 데이터가 이 미러로 복제되었는지 확인합니다.
    
    ```bash
    DEST_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $DEST_ZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```
    
  이제 항목 목록에 MirrorMaster가 원본 클러스터에서 대상으로 토픽을 미러링할 때 만들어진 `testtopic`이(가) 포함됩니다. 이 항목에서 검색한 메시지는 원본 클러스터에 입력한 것과 동일합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 단계를 수행하면 동일한 Azure 리소스 그룹에 두 클러스터가 모두 만들어지므로 Azure Portal에서 해당 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹을 삭제하면 이 문서의 단계를 수행하여 만들어진 모든 리소스, Azure Virtual Network 및 클러스터에서 사용하는 저장소 계정이 제거됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 MirrorMaker를 사용하여 Kafka 클러스터 복제본을 만드는 방법을 알아봤습니다. Kafka를 사용하는 다른 방법을 찾으려면 다음 링크를 사용하세요.

* [Apache Kafka MirrorMaker 문서](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)(cwiki.apache.org)
* [HDInsight에서 Apache Kafka 시작](hdinsight-apache-kafka-get-started.md)
* [HDInsight의 Kafka에서 Apache Spark 사용](hdinsight-apache-spark-with-kafka.md)
* [HDInsight의 Kafka에서 Apache Storm 사용](hdinsight-apache-storm-with-kafka.md)


