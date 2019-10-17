---
title: Azure HDInsight에서 Apache Kafka에 대 한 SSL 암호화 및 인증 설정
description: Kafka 클라이언트와 kafka 브로커 간의 통신에 대해 SSL 암호화를 설정 하 고 Kafka broker 간에 통신을 설정 합니다. 클라이언트의 SSL 인증을 설정 합니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 19a817124afb9afcee25b5f2bff73b8a17e16519
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72431284"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Kafka에 대 한 SSL(Secure Sockets Layer) (SSL) 암호화 및 인증 설정

이 문서에서는 Apache Kafka 클라이언트와 Apache Kafka broker 간에 SSL 암호화를 설정 하는 방법을 보여 줍니다. 또한 클라이언트의 인증을 설정 하는 방법을 보여 줍니다 (양방향 SSL이 라고도 함).

> [!Important]
> Kafka 응용 프로그램에 사용할 수 있는 두 가지 클라이언트는 Java 클라이언트와 콘솔 클라이언트입니다. Java 클라이언트 `ProducerConsumer.java`만 생성 및 소비 둘 다에 SSL을 사용할 수 있습니다. 콘솔 생산자 클라이언트 `console-producer.sh`은 SSL에서 작동 하지 않습니다.

## <a name="apache-kafka-broker-setup"></a>Apache Kafka Broker 설치

Kafka SSL broker 설치 프로그램은 다음과 같은 방식으로 4 개의 HDInsight 클러스터 Vm을 사용 합니다.

* 헤드 노드 0-CA (인증 기관)
* 작업자 노드 0, 1 및 2-broker

> [!Note] 
> 이 가이드에서는 자체 서명된 인증서를 사용하지만, 가장 안전한 솔루션은 신뢰할 수 있는 CA에서 발급한 인증서를 사용하는 것입니다.

Broker 설치 프로세스의 요약은 다음과 같습니다.

1. 세 개의 작업자 노드 각각에 대해 다음 단계가 반복 됩니다.

    1. 인증서를 생성 합니다.
    1. 인증서 서명 요청을 만듭니다.
    1. 인증서 서명 요청을 CA (인증 기관)로 보냅니다.
    1. CA에 로그인 하 고 요청에 서명 합니다.
    1. 서명 된 인증서를 작업자 노드에 다시 SCP 합니다.
    1. SCP CA의 공용 인증서를 작업자 노드에 대 한 합니다.

1. 인증서가 모두 있으면 인증서를 인증서 저장소에 넣습니다.
1. Ambari으로 이동 하 여 구성을 변경 합니다.

다음 세부 지침을 사용 하 여 broker 설치를 완료 합니다.

> [!Important]
> 다음 코드 조각에서는 세 개의 작업자 노드 중 하나에 대 한 약어 이며 적절 한 `wn0`, `wn1` 또는 `wn2`로 대체 되어야 합니다. `WorkerNode0_Name` 및 `HeadNode0_Name`은 `wn0-abcxyz` 또는 `hn0-abcxyz`과 같은 해당 컴퓨터의 이름으로 대체 되어야 합니다.

1. 헤드 노드 0에 대 한 초기 설치를 수행 합니다 .이 경우 HDInsight는 CA (인증 기관)의 역할을 채웁니다.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 각 브로커 (작업자 노드 0, 1, 2)에서 동일한 초기 설정을 수행 합니다.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 각 작업자 노드에서 아래 코드 조각을 사용 하 여 다음 단계를 실행 합니다.
    1. 키 저장소을 만들고 새 개인 인증서로 채웁니다.
    1. 인증서 서명 요청을 만듭니다.
    1. SCP CA에 대 한 인증서 서명 요청 (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. CA 컴퓨터로 변경 하 고 수신 된 모든 인증서 서명 요청을 서명 합니다.

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. 서명 된 인증서를 CA에서 작업자 노드로 다시 보냅니다 (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. 각 작업자 노드에 CA의 공용 인증서를 보냅니다.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. 각 작업자 노드에서 Ca 공용 인증서를 truststore 및 키 저장소에 추가 합니다. 그런 다음 키 저장소에 작업자 노드의 자체 서명 된 인증서를 추가 합니다.

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>SSL을 사용하도록 Kafka 구성 업데이트 및 브로커 다시 시작

이제 키 저장소 및 truststore를 사용 하 여 각 Kafka broker를 설정 하 고 올바른 인증서를 가져왔습니다. 다음으로, Ambari를 사용하여 관련된 Kafka 구성 속성을 수정하고 Kafka 브로커를 다시 시작합니다.

구성 수정을 완료하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인하고 Azure HDInsight Apache Kafka 클러스터를 선택합니다.
1. **클러스터 대시보드**에서 **Ambari 홈**을 클릭하여 Ambari UI로 이동합니다.
1. **Kafka 브로커**에서 **수신기** 속성을 `PLAINTEXT://localhost:9092,SSL://localhost:9093`로 설정합니다.
1. **고급 kafka-broker**에서 **security.inter.broker.protocol** 속성을 `SSL`로 설정합니다.

    ![Ambari에서 Kafka SSL 구성 속성 편집](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. **사용자 지정 kafka-broker**에서 **ssl.client.auth** 속성을 `required`로 설정합니다. 이 단계는 인증 및 암호화를 설정 하는 경우에만 필요 합니다.

    ![Ambari에서 kafka SSL 구성 속성 편집](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. 아래 명령을 실행 하 여 FQDN (정규화 된 도메인 이름)이 아닌 IP 주소를 보급 하도록 Kafka `server.properties` 파일에 구성 속성을 추가 합니다.

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. 이전 변경 내용이 올바르게 적용되었는지 확인하기 위해 필요한 경우 Kafka `server.properties` 파일에 다음 줄이 있는지 확인할 수 있습니다.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

1. 모든 Kafka 브로커를 다시 시작합니다.
1. 생산자와 소비자 옵션으로 관리 클라이언트를 시작 하 여 생산자와 소비자가 포트 9093에서 작동 하는지 확인 합니다.

## <a name="client-setup-with-authentication"></a>클라이언트 설정(인증 포함)

> [!Note]
> 다음 단계는 SSL 암호화 **및** 인증을 둘 다 설정하는 경우에만 필요합니다. 암호화만 설정하는 경우 [클라이언트 설정(인증 제외)](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)에서 계속 진행하세요.

클라이언트 설정을 완료 하려면 다음 단계를 완료 합니다.

1. 클라이언트 컴퓨터 (h n 1)에 로그인 합니다.
1. Java 키 저장소를 만들고 서명된 브로커용 인증서를 가져옵니다. 그런 다음, CA가 실행 중인 VM에 인증서를 복사합니다.
1. 클라이언트 인증서에 서명 하려면 CA 컴퓨터 (hn0)로 전환 합니다.
1. 클라이언트 머신(hn1)으로 이동한 다음, `~/ssl` 폴더로 이동합니다. 서명된 인증서를 클라이언트 머신에 복사합니다.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

마지막으로 `cat client-ssl-auth.properties` 명령을 사용하여 `client-ssl-auth.properties` 파일을 확인합니다. 파일에 다음 줄이 있어야 합니다.

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>클라이언트 설정(인증 제외)

인증이 필요 하지 않은 경우 SSL 암호화를 설정 하는 단계는 다음과 같습니다.

1. 클라이언트 컴퓨터(hn1)에 로그인한 다음 `~/ssl` 폴더로 이동합니다.
1. CA 컴퓨터(wn0)에서 클라이언트 컴퓨터로 서명된 인증서를 복사합니다.
1. 신뢰 저장소로 CA 인증서를 가져옵니다.
1. 키 저장소로 CA 인증서를 가져옵니다.

다음 코드 조각에 이러한 단계가 나와 있습니다.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

마지막으로 `cat client-ssl-auth.properties` 명령을 사용하여 `client-ssl-auth.properties` 파일을 확인합니다. 파일에 다음 줄이 있어야 합니다.

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Apache Kafka란?](apache-kafka-introduction.md)
