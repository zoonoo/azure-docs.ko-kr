---
title: Apache Kafka 토픽 미러링 - Azure HDInsight
description: Apache Kafka의 미러링 기능으로 보조 클러스터에 토픽을 미러링하여 HDInsight 클러스터에서 Kafka 복제본을 유지하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: bdc393d041bd40fd27493ccc8f3c4f39adfa35b2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657121"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>MirrorMaker를 사용하여 HDInsight에서 Kafka와 함께 Apache Kafka 토픽 복제

Apache Kafka의 미러링 기능을 사용하여 토픽을 보조 클러스터로 복제하는 방법에 대해 알아봅니다. 미러링은 연속적 프로세스로 실행되거나 한 클러스터에서 다른 클러스터로 데이터를 마이그레이션하는 방법으로 단속적으로 사용될 수 있습니다.

이 예제에서 미러링은 두 HDInsight 클러스터 간에 항목을 복제하는 데 사용됩니다. 두 클러스터 모두 여러 데이터 센터에서 서로 다른 가상 네트워크의 경우

> [!WARNING]  
> 미러링이 내결함성을 달성하는 수단으로 간주되어서는 안됩니다. 토픽 내의 항목에 대 한 오프셋 기본 및 보조 클러스터 간에 다른 되므로 클라이언트는 둘을 서로 교환해 서 사용할 수 없습니다.
>
> 내결함성이 염려되는 경우 클러스터 내의 토픽에 대한 복제를 설정해야 합니다. 자세한 내용은 [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md)을 참조하세요.

## <a name="how-apache-kafka-mirroring-works"></a>Apache Kafka 미러링 작동 방식

사용 하 여 미러링 작동 합니다 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 도구 (Apache Kafka의 일부) 기본 클러스터의 토픽에서 레코드를 사용 하 고 보조 클러스터에서 로컬 복사본을 만든를 합니다. MirrorMaker 사용 하나 (또는 이상) *소비자* 기본 클러스터에서 읽은 *생산자* 로컬 (보조) 클러스터에 기록 하는 합니다.

재해 복구를 위한 가장 유용한 미러링 설치에서 다른 Azure 지역에서 Kafka 클러스터를 사용합니다. 이렇게 하려면 클러스터에 있는 가상 네트워크 피어 링 된 함께 합니다.

다음 다이어그램은 미러링 프로세스와 클러스터 간의 통신 흐름 방식을 보여 줍니다.

![미러링 프로세스 다이어그램](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

기본 및 보조 클러스터는 노드와 파티션 수가 다를 수 있습니다 하 고 토픽 내의 오프셋은 또한 다릅니다. 미러링은 분할에 사용되는 키 값을 유지하므로 레코드 순서는 키 기준으로 유지됩니다.

### <a name="mirroring-across-network-boundaries"></a>네트워크 경계를 넘은 미러링

다른 네트워크의 Kafka 클러스터 간에 미러링해야 하는 경우 다음과 같은 추가 고려 사항이 있습니다.

* **게이트웨이**: 네트워크는 TCP/IP 수준에서 통신할 수 있어야 합니다.

* **서버 주소를 지정**: 해당 IP 주소 또는 정규화 된 도메인 이름을 사용 하 여 클러스터 노드를 해결 하기 위해 선택할 수 있습니다.

    * **IP 주소**: IP 주소를 보급을 사용 하 여 Kafka 클러스터를 구성한 경우에 미러링 설정을 사용 하는 브로커 노드 및 zookeeper 노드의 IP 주소를 사용 하 여 진행할 수 있습니다.
    
    * **도메인 이름**: IP 주소를 보급을 위해 Kafka 클러스터를 구성 하지 않으면 클러스터는 정규화 된 도메인 이름 (Fqdn)을 사용 하 여 서로 연결할 수 있어야 합니다. 이를 다른 네트워크 요청을 전달 하도록 구성 된 각 네트워크의 도메인 이름 시스템 (DNS) 서버에 필요 합니다. Azure Virtual Network를 만들 때 네트워크에서 제공되는 자동 DNS를 사용하는 대신 사용자 지정 DNS 서버와 해당 서버의 IP 주소를 지정해야 합니다. 가상 네트워크를 만든 후에는 해당 IP 주소를 사용하는 Azure Virtual Machine을 만든 다음 DNS 소프트웨어를 설치하고 구성해야 합니다.

    > [!WARNING]  
    > HDInsight를 Virtual Network에 설치하기 전에 사용자 지정 DNS 서버를 만들고 구성합니다. Virtual Network에 구성된 DNS 서버를 사용하기 위해 HDInsight를 추가로 구성할 필요는 없습니다.

두 개의 Azure Virtual Network 연결에 대한 자세한 내용은 [VNet 간 연결 구성](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요.

## <a name="mirroring-architecture"></a>미러링 아키텍처

이 아키텍처는 다른 리소스 그룹 및 가상 네트워크에 2 개 클러스터 기능:는 **주** 하 고 **보조**합니다.

### <a name="creation-steps"></a>만들기 단계

1. 두 개의 새 리소스 그룹을 만듭니다.

    |리소스 그룹 | 위치 |
    |---|---|
    | kafka-primary-rg | 미국 중부 |
    | kafka-secondary-rg | 미국 중북부 |

1. 새 가상 네트워크를 만듭니다 **kafka-기본-vnet** 에 **kafka-기본-rg**합니다. 기본 설정을 유지 합니다.
1. 새 가상 네트워크를 만듭니다 **kafka 보조 vnet** 에 **kafka-보조-rg**, 기본 설정으로도 합니다.

1. 두 개의 새로운 Kafka 클러스터를 만듭니다.

    | 클러스터 이름 | 리소스 그룹 | Virtual Network | 스토리지 계정 |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. 가상 네트워크 피어 링을 만듭니다. 이 단계에서는 두 피어 링을 만듭니다:에서 하나 **kafka-기본-vnet** 하 **kafka 보조 vnet** 하나에서 다시 **kafka 보조 vnet** 에  **kafka-기본-vnet**합니다.
    1. 선택 된 **kafka-기본-vnet** 가상 네트워크.
    1. 클릭 **피어 링** 아래에서 **설정**합니다.
    1. **추가**를 클릭합니다.
    1. 에 **피어 링 추가** 화면에서 아래 스크린샷에 표시 된 것과 같이 세부 정보를 입력 합니다.

        ![vnet 피어 링 추가](./media/apache-kafka-mirroring/add-vnet-peering.png)

1. IP 보급을 구성 합니다.
    1. 기본 클러스터에 대 한 Ambari 대시보드로 이동: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`합니다.
    1. 클릭 **Services** > **Kafka**합니다. **Configs** 탭을 클릭합니다.
    1. 맨 아래에 다음 구성 줄을 추가 **kafka env 템플릿** 섹션입니다. **Save**을 클릭합니다.
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. 에 메모를 입력 합니다 **구성을 저장** 화면을 클릭 **저장**합니다.
    1. 구성 경고 메시지가 표시 되 면 클릭 **계속 진행**합니다.
    1. 클릭 **Ok** 에 **구성 변경 내용을 저장**합니다.
    1. 클릭 **다시 시작** > **Restart All Affected** 에 **다시 시작 필요** 알림. 클릭 **다시 시작 확인 모든**합니다.

        ![kafka 노드를 다시 시작](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. 모든 네트워크 인터페이스에서 수신 대기 하도록 Kafka를 구성 합니다.
    1. 유지 합니다 **Configs** 탭에서 **Services** > **Kafka**합니다. 에 **Kafka Broker** 설정 섹션을 **수신기** 속성을 `PLAINTEXT://0.0.0.0:9092`합니다.
    1. **Save**을 클릭합니다.
    1. 클릭 **다시 시작**, 및 **다시 시작 확인 모든**합니다.

1. Broker IP 주소 및 기본 클러스터에 대 한 Zookeeper 주소를 기록 합니다.
    1. 클릭 **호스트** Ambari 대시보드에서.
    1. Broker 및 Zookeeper에 대 한 IP 주소를 기록해 둡니다. 브로커 노드에 **wn** 노드에 있는 호스트 이름 및 zookeeper의 처음 두 문자로 **zk** 호스트 이름의 처음 두 문자로 합니다.

        ![ip 주소 보기](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. 두 번째 클러스터에 대 한 이전 세 단계를 반복 **kafka 보조 클러스터**: IP 보급 구성, 수신기를 설정 하 고, Broker 및 Zookeeper IP 주소를 기록해 둡니다.

## <a name="create-topics"></a>토픽 만들기

1. 에 연결 합니다 **기본** SSH를 사용 하 여 클러스터:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    **sshuser**은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. **BASENAME**은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

    자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 다음 명령을 사용 하 여 기본 클러스터에 대 한 Apache Zookeeper 호스트를 사용 하 여 변수를 만듭니다. 문자열 같은 `ZOOKEEPER_IP_ADDRESS1` 이전에 기록 된 실제 IP 주소로 바꾸어야와 같은 `10.23.0.11` 및 `10.23.0.7`합니다. 를 사용 하는 사용자 지정 DNS 서버를 사용 하 여 FQDN 확인을 사용 하는 경우에 따라 [이 단계](apache-kafka-get-started.md#getkafkainfo) broker 및 zookeeper 이름을 가져올 수 있습니다.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. `testtopic`이라는 토픽을 만들려면 다음 명령을 사용합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. 다음 명령을 사용하여 토픽이 만들어졌는지 확인합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    응답에 `testtopic`이 있습니다.

4. 이 대 한 Zookeeper 호스트 정보를 보려면 다음을 사용 하 여 (합니다 **기본**) 클러스터:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    이 명령은 다음 텍스트와 비슷한 정보를 반환합니다.

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    이 정보를 저장합니다. 해당 정보는 다음 섹션에서 사용됩니다.

## <a name="configure-mirroring"></a>미러링 구성

1. 에 연결 합니다 **보조** 다른 SSH 세션을 사용 하 여 클러스터:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    **sshuser**은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. 바꿉니다 **SECONDARYCLUSTER** 클러스터를 만들 때 사용한 이름입니다.

    자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. A `consumer.properties` 통신할 구성 파일을 사용 합니다 **기본** 클러스터. 파일을 만들려면 다음 명령을 사용합니다.

    ```bash
    nano consumer.properties
    ```

    `consumer.properties` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    바꿉니다 **PRIMARY_ZKHOSTS** Zookeeper IP 주소를 사용 하 여 합니다 **기본** 클러스터.

    이 파일에는 기본 Kafka 클러스터에서 읽을 때 사용할 소비자 정보를 설명 합니다. 소비자 구성에 대한 자세한 내용은 kafka.apache.org의 [소비자 구성](https://kafka.apache.org/documentation#consumerconfigs)(영문)을 참조하세요.

    파일을 저장하려면 **Ctrl + X**, **Y** 및 **Enter** 키를 사용합니다.

3. 보조 클러스터와 통신 하는 생산자를 구성 하기 전에 broker IP 주소에 대해 변수를 설정 합니다 **보조** 클러스터. 다음 명령을 사용 하 여이 변수를 만듭니다.

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    이 명령은 `echo $SECONDARY_BROKERHOSTS` 다음 텍스트와 유사한 정보가 반환 됩니다.

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. A `producer.properties` 파일은 통신에 사용 되는 **보조** 클러스터. 파일을 만들려면 다음 명령을 사용합니다.

    ```bash
    nano producer.properties
    ```

    `producer.properties` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    바꿉니다 **SECONDARY_BROKERHOSTS** 이전 단계에서 사용 되는 broker IP 주소를 사용 합니다.

    생산자 구성에 대한 자세한 내용은 kafka.apache.org의 [생산자 구성](https://kafka.apache.org/documentation#producerconfigs)(영문)을 참조하세요.

5. 다음 명령을 사용 하 여 보조 클러스터에 대 한 Zookeeper 호스트의 IP 주소를 사용 하 여 환경 변수를 만듭니다.

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. HDInsight의 Kafka에 대한 기본 구성이 토픽의 자동 만들기를 허용하지 않습니다. 미러링 프로세스를 시작하기 전에 다음 옵션 중 하나를 사용해야 합니다.

    * **보조 클러스터에 토픽 만들기**: 이 옵션을 사용하면 파티션 및 복제 요소 수를 설정할 수 있습니다.

        다음 명령을 사용하여 사전에 토픽을 만들 수 있습니다.

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        `testtopic`을 만들려는 토픽의 이름으로 바꿉니다.

    * **자동 토픽을 만들기 위한 클러스터 구성**: 이 옵션 하는 여러 파티션 또는 복제 계수로 기본 항목을 사용 하 여 발생할 수 있지만 자동으로 토픽을 만들려면 MirrorMaker를 허용 합니다.

        자동으로 토픽을 만들려면 보조 클러스터를 구성 하려면 다음이 단계를 수행 합니다.

        1. 보조 클러스터에 대 한 Ambari 대시보드로 이동: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`합니다.
        1. 클릭 **Services** > **Kafka**합니다. **Configs** 탭을 클릭합니다.
        5. __필터__ 필드에 `auto.create` 값을 입력합니다. 이렇게 하면 속성 목록이 필터링되고 `auto.create.topics.enable` 설정이 표시됩니다.
        6. `auto.create.topics.enable` 값을 true로 변경하고 __저장__을 선택합니다. 메모를 추가하고 __저장__을 다시 선택합니다.
        7. __Kafka__ 서비스를 선택하고 __다시 시작__을 선택한 후 __영향을 받는 모든 서비스 다시 시작__을 선택합니다. 메시지가 나타나면 __모두 다시 시작 확인__을 선택합니다.

        ![구성 항목 자동 생성](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>MirrorMaker 시작

1. 에 대 한 SSH 연결 합니다 **보조** 클러스터의 경우 다음 명령을 사용 하 여 MirrorMaker 프로세스를 시작 합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    이 예제에 사용된 매개 변수는 다음과 같습니다.

    * **--consumer.config**: 소비자 속성이 포함된 파일을 지정합니다. 이러한 속성에서 읽는 소비자를 만드는 데 사용 되는 *기본* Kafka 클러스터.

    * **--producer.config**: 생산자 속성이 포함된 파일을 지정합니다. 이러한 속성에 쓰는 생산자를 만드는 데 사용 되는 *보조* Kafka 클러스터.

    * **--whitelist**: MirrorMaker를 기본 클러스터에서 보조 데이터베이스에 복제 하는 항목의 목록입니다.

    * **--num.streams**: 생성할 소비자 스레드 수입니다.

    보조 노드에서 소비자가 메시지를 받을 대기 중입니다.

2. SSH 연결에서는 **기본** 클러스터의 경우 다음 명령을 사용 하 여 토픽에 메시지를 보내고 생산자를 시작 합니다.

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     커서로 빈 라인에 도달하면 몇 개의 텍스트 메시지를 입력합니다. 메시지 토픽에 전송 되는 **기본** 클러스터. 완료되면 **Ctrl + C**를 클릭하여 프로듀서 프로세스를 종료합니다.

3. 에 대 한 SSH 연결 합니다 **보조** 클러스터를 사용 하 여 **Ctrl + C** MirrorMaker 프로세스를 종료 합니다. 이 프로세스를 종료하는 데 몇 초 정도 걸릴 수 있습니다. 메시지를 보조 데이터베이스로 복제 된 있는지를 확인 하려면 다음 명령을 사용 합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    항목의 목록에 포함 `testtopic`에 mirrormaster 항목을 기본 클러스터에서 보조 하는 경우 생성 됩니다. 항목에서 검색 된 메시지는 기본 클러스터에서 입력 한 것과 동일 합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 단계에서 다른 Azure 리소스 그룹에 클러스터를 만들었습니다. 모든 만든 리소스를 삭제 하려면 만든 두 리소스 그룹을 삭제할 수 있습니다: **kafka-기본-rg** 하 고 **kafka secondary_rg**합니다. 리소스 그룹을 삭제 해도 클러스터, 가상 네트워크 및 저장소 계정을 포함 하 여이 문서에 따라 만든 리소스를 모두 제거 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)를 사용하여 [Apache Kafka](https://kafka.apache.org/) 클러스터 복제본을 만드는 방법을 알아보았습니다. Kafka를 사용하는 다른 방법을 찾으려면 다음 링크를 사용하세요.

* [Apache Kafka MirrorMaker 문서](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)(cwiki.apache.org)
* [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md)
* [HDInsight에서 Apache Spark 및 Apache Kafka 사용](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight에서 Apache Storm 및 Apache Kafka 사용](../hdinsight-apache-storm-with-kafka.md)
* [Azure Virtual Network를 통해 Apache Kafka에 연결](apache-kafka-connect-vpn-gateway.md)
