---
title: Enterprise Security Package를 사용하여 HDInsight에서 Kafka 정책 구성 - Azure
description: Enterprise Security Package를 사용하여 Azure HDInsight에서 Kafka용 Apache Ranger 정책을 구성하는 방법을 알아봅니다.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 48cfba6f62d75470efd27e3a4cdcb995e716798b
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037144"
---
# <a name="tutorial-configure-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>자습서: Enterprise Security Package를 사용하여 HDInsight에서 Kafka 정책 구성(미리 보기)

ESP(Enterprise Security Package) Kafka 클러스터용 Apache Ranger 정책을 구성하는 방법을 알아봅니다. ESP 클러스터는 도메인에 연결되므로 사용자가 도메인 자격 증명을 사용하여 인증할 수 있습니다. 이 자습서에서는 `sales*` 및 `marketingspend` 항목 액세스를 제한하는 Range 정책 두 개를 만듭니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 도메인 사용자 만들기
> * Ranger 정책 만들기
> * Kafka 클러스터에서 항목 만들기
> * Ranger 정책 테스트

## <a name="before-you-begin"></a>시작하기 전에

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* [Azure Portal](https://portal.azure.com/)에 로그인합니다.

* [Enterprise Security Package를 사용하여 HDInsight Kafka 클러스터](apache-domain-joined-configure-using-azure-adds.md)를 만듭니다.

## <a name="connect-to-apache-ranger-admin-ui"></a>Apache Ranger 관리 UI에 연결

1. 브라우저에서 `https://<ClusterName>.azurehdinsight.net/Ranger/` URL을 사용하여 Range 관리 사용자 인터페이스에 연결합니다. `<ClusterName>`은 Kafka 클러스터 이름으로 변경해야 합니다.

    > [!NOTE] 
    > Ranger 자격 증명은 Hadoop 클러스터 자격 증명과는 다릅니다. 브라우저가 캐시된 Hadoop 자격 증명을 사용하지 않도록 하려면 새 InPrivate 브라우저 창을 사용하여 Ranger 관리 UI에 연결합니다.

2. Azure AD(Active Directory) 관리자 자격 증명을 사용하여 로그인합니다. Azure AD 관리자 자격 증명은 HDInsight 클러스터 자격 증명 또는 Linux HDInsight 노드 SSH 자격 증명과는 다릅니다.

   ![Apache Ranger 관리 UI](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>도메인 사용자 만들기

[Enterprise Security Package를 사용하여 HDInsight 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-domain-joined-hdinsight-cluster)를 방문하여 **sales_user** 및 **marketing_user** 도메인 사용자를 만드는 방법을 확인합니다. 프로덕션 시나리오에서는 Active Directory 테 넌트에서 도메인 사용자가 제공됩니다.

## <a name="create-ranger-policy"></a>Ranger 정책 만들기 

**sales_user** 및 **marketing_user** 사용자에 대해 Ranger 정책을 만듭니다.

1. **Ranger 관리 UI**를 엽니다.

2. **Kafka** 아래의 **\<ClusterName>_kafka**를 클릭합니다. 미리 구성된 정책 하나가 나열될 수 있습니다.

3. **새 정책 추가**를 클릭하고 다음 값을 입력합니다.

   |**설정**  |**제안 값**  |
   |---------|---------|
   |정책 이름  |  hdi sales* policy   |
   |항목   |  sales* |
   |사용자 선택  |  sales_user1 |
   |권한  | 게시, 사용, 만들기 |

   항목 이름에 다음 와일드카드를 포함할 수 있습니다.

   * ’*’는 문자가 0개 이상 나옴을 나타냅니다.
   * ’?‘는 문자 하나를 나타냅니다.

   ![Apache Ranger 관리 UI 정책 만들기](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE] 
   >**사용자 선택**에 도메인 사용자가 자동으로 입력되지 않으면 Ranger가 Azure AD와 동기화될 때까지 잠시 기다립니다.

4. **추가** 를 클릭하여 정책을 저장합니다.

5. **새 정책 추가**를 클릭하고 다음 값을 입력합니다.

   |**설정**  |**제안 값**  |
   |---------|---------|
   |정책 이름  |  hdi marketing policy   |
   |항목   |  marketingspend |
   |사용자 선택  |  marketing_user1 |
   |권한  | 게시, 사용, 만들기 |

   ![Apache Ranger 관리 UI 정책 만들기](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. **추가** 를 클릭하여 정책을 저장합니다.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>ESP를 사용하여 Kafka 클러스터에서 항목 만들기

**salesevents** 및 **marketingspend** 항목을 만들려면 다음 단계를 수행합니다.

1. 다음 명령을 사용하여 클러스터에 대한 SSH 연결을 엽니다.

   ```bash
   ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   `SSHUSER`은 클러스터의 SSH 사용자로, `CLUSTERNAME`은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자 계정의 암호를 입력합니다. HDInsight에서의 `scp` 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)을 참조하세요.

   프로덕션 시나리오에서는 클러스터 생성 중에 구성한 도메인 사용자가 클러스터로의 SSH를 수행할 수 있습니다.

2. 다음 명령을 사용하여 변수에 클러스터 이름을 저장하고 JSON 구문 분석 유틸리티 `jq`를 설치합니다. 메시지가 표시되면 Kafka 클러스터 이름을 입력합니다.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. 다음 명령을 사용하여 Kafka broker 호스트와 Zookeeper 호스트를 가져옵니다. 메시지가 표시되면 클러스터 관리 계정의 암호를 입력합니다.

   ```bash
   export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```
> [!Note]
> 아직 개발 환경을 설정하지 않은 경우 계속 진행하려면 개발 환경을 설정해야 합니다. Java JDK, Apache Maven, scp가 포함된 SSH 클라이언트 등의 구성 요소가 필요합니다. 자세한 내용은 [설치 지침](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)을 참조하세요.
1. [Apache Kafka 도메인 조인 생산자 소비자 예제](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)를 다운로드합니다.

1. [자습서: Apache Kafka 생산자 및 소비자 API 사용](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)의 **빌드 및 배포 예제**에서 2단계 및 3단계를 따릅니다.

1. 다음 명령을 실행합니다.

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

   >[!NOTE] 
   >Kafka 서비스의 프로세스 소유자(예: 루트)만이 Zookeeper znode `/config/topics`에 쓸 수 있습니다. 권한이 없는 사용자가 항목을 만들 때는 Ranger 정책이 적용되지 않습니다. `kafka-topics.sh` 스크립트는 Zookeeper와 직접 통신하여 항목을 만들기 때문입니다. 엔트리는 Zookeeper 노드에 추가되며 broker 쪽 Watcher가 항목을 적절하게 모니터링하고 생성합니다. Ranger 플러그 인을 통해 권한을 부여할 수는 없습니다. 위의 명령은 Kafka broker를 통해 `sudo`를 사용하여 실행됩니다.


## <a name="test-the-ranger-policies"></a>Ranger 정책 테스트

구성한 Ranger 정책에 따라 **sales_user**는 **salesevents** 항목은 생성/사용할 수 있지만 **marketingspend** 항목은 생성/사용할 수 없습니다. 반면 **marketing_user**는 **marketingspend** 항목은 생성/사용할 수 있지만 **salesevents** 항목은 생성/사용할 수 없습니다.

1. 클러스터에 대한 새 SSH 연결을 엽니다. 다음 명령을 사용하여 **sales_user1**로 로그인합니다.

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. 다음 명령을 실행합니다.

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. 이전 섹션의 broker 및 Zookeeper 이름을 사용하여 다음 환경 변수를 설정합니다.

   ```bash
   export KAFKABROKERS=<brokerlist>:9092 
   ```

   예제: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

   ```bash
   export KAFKAZKHOSTS=<zklist>:2181
   ```

   예제: `export KAFKAZKHOSTS=zk1-khdicl.contoso.com:2181,zk2-khdicl.contoso.com:2181`

4. **sales_user1**이 **salesevents** 항목을 생성할 수 있는지 확인합니다.
   
   다음 명령을 실행하여 **salesevents** 항목에 대해 console-producer를 시작합니다.

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic salesevents --security-protocol SASL_PLAINTEXT
   ```

   그런 다음 콘솔에서 메시지를 몇 개 입력합니다. **Ctrl+C**를 눌러 console-producer를 끝냅니다.

5. 다음 명령을 실행하여 **salesevents** 항목을 사용해 봅니다.

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic salesevents --security-protocol PLAINTEXTSASL --from-beginning
   ```
 
6. 이전 단계에서 입력한 메시지가 나타나며 **sales_user1**이 **marketingspend** 항목을 생성할 수 없는지 확인합니다.

   이전 단계와 같은 ssh 창에서 다음 명령을 실행하여 **marketingspend** 항목을 생성합니다.

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic marketingspend --security-protocol SASL_PLAINTEXT
   ```

   권한 부여 오류가 발생하지만 무시해도 됩니다. 

7. **marketing_user1**은 **salesevents** 항목을 사용할 수 없습니다.

   이번에는 **marketing_user1**로 위의 1-3단계를 반복합니다.

   다음 명령을 실행하여 **salesevents** 항목을 사용해 봅니다.

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic marketingspend --security-protocol PLAINTEXTSASL --from-beginning
   ```

   이전 메시지를 볼 수 없습니다.

8. Ranger UI에서 감사 액세스 이벤트를 확인합니다.

   ![Ranger UI 정책 감사](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>다음 단계

* [Kafka로의 Bring Your Own Key](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [Enterprise Security Package를 사용하는 Hadoop 보안 소개](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
