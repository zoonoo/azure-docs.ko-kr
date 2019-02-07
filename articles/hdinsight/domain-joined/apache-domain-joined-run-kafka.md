---
title: Enterprise Security Package를 사용하여 HDInsight에서 Apache Kafka 정책 구성 - Azure
description: Enterprise Security Package를 사용하여 Azure HDInsight에서 Kafka용 Apache Ranger 정책을 구성하는 방법을 알아봅니다.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 01/14/2019
ms.openlocfilehash: 381958b462b668447a6548029a1be616df7a3a4b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750225"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>자습서: Enterprise Security Package를 사용하여 HDInsight에서 Apache Kafka 정책 구성(미리 보기)

ESP(Enterprise Security Package) Apache Kafka 클러스터용 Apache Ranger 정책을 구성하는 방법을 알아봅니다. ESP 클러스터는 도메인에 연결되므로 사용자가 도메인 자격 증명을 사용하여 인증할 수 있습니다. 이 자습서에서는 `sales*` 및 `marketingspend` 항목 액세스를 제한하는 Range 정책 두 개를 만듭니다.

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

[Enterprise Security Package를 사용하여 HDInsight 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)를 방문하여 **sales_user** 및 **marketing_user** 도메인 사용자를 만드는 방법을 확인합니다. 프로덕션 시나리오에서는 Active Directory 테 넌트에서 도메인 사용자가 제공됩니다.

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

두 개의 토픽, `salesevents` 및 `marketingspend`를 만들려면:

1. 다음 명령을 사용하여 클러스터에 대한 SSH 연결을 엽니다.

   ```bash
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   `DOMAINADMIN`을 [클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-hdinsight-cluster-with-esp) 중 구성된 클러스터에 대한 관리 사용자로 바꾸고, `CLUSTERNAME`을 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 관리 사용자 계정의 암호를 입력합니다. HDInsight에서의 `SSH` 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)을 참조하세요.

2. 다음 명령을 사용하여 변수에 클러스터 이름을 저장하고 JSON 구문 분석 유틸리티 `jq`를 설치합니다. 메시지가 표시되면 Kafka 클러스터 이름을 입력합니다.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. 다음 명령을 사용하여 Kafka broker 호스트를 가져옵니다. 메시지가 표시되면 클러스터 관리 계정의 암호를 입력합니다.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   > [!Note]  
   > 아직 개발 환경을 설정하지 않은 경우 계속 진행하려면 개발 환경을 설정해야 합니다. Java JDK, Apache Maven, scp가 포함된 SSH 클라이언트 등의 구성 요소가 필요합니다. 자세한 내용은 [설치 지침](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)을 참조하세요.
   
1. [Apache Kafka 도메인 조인 생산자 소비자 예제](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)를 다운로드합니다.

1. **예제 빌드 및 배포** 아래의 2단계와 3단계를 수행합니다([자습서: Apache Kafka 생산자 및 소비자 API 사용](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)).

1. 다음 명령을 실행합니다.

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Ranger 정책 테스트

구성한 Ranger 정책에 따라 **sales_user**는 `salesevents` 토픽은 생성/사용할 수 있지만 `marketingspend` 토픽은 생성/사용할 수 없습니다. 반면 **marketing_user**는 `marketingspend` 토픽은 생성/사용할 수 있지만 `salesevents` 토픽은 생성/사용할 수 없습니다.

1. 클러스터에 대한 새 SSH 연결을 엽니다. 다음 명령을 사용하여 **sales_user1**로 로그인합니다.

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. 다음 명령을 실행합니다.

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. 이전 섹션의 broker 이름을 사용하여 다음 환경 변수를 설정합니다.

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   예제: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. 을 sales_user가 사용할 수 있는지 확인하려면 **예제 빌드 및 배포** 아래의 3단계를 수행합니다([자습서: Apache Kafka 생산자 및 소비자 API 사용](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)).

5. 다음 명령을 실행하여 **sales_user1**이 `salesevents` 토픽을 생성할 수 있는지 확인합니다.

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. 다음 명령을 실행하여 `salesevents` 토픽을 사용해 봅니다.

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   메시지를 읽을 수 있는지 확인합니다.

7. **sales_user1**이 동일한 ssh 창에서 다음을 실행하여 `marketingspend` 항목을 생성할 수 없는지 확인합니다.

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   권한 부여 오류가 발생하지만 무시해도 됩니다.

8. **marketing_user1**은 `salesevents` 토픽을 사용할 수 없습니다.

   이번에는 **marketing_user1**로 위의 1-4단계를 반복합니다.

   다음 명령을 실행하여 `salesevents` 토픽을 사용해 봅니다.

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   이전 메시지를 볼 수 없습니다.

9. Ranger UI에서 감사 액세스 이벤트를 확인합니다.

   ![Ranger UI 정책 감사](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>다음 단계

* [Apache Kafka로의 Bring Your Own Key](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [Enterprise Security Package를 사용하여 Apache Hadoop 보안 소개](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
