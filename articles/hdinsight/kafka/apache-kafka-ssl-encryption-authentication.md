---
title: SSL 암호화 및 Azure HDInsight에서 Apache Kafka에 대 한 인증 설정
description: Kafka 클라이언트가 Kafka 브로커 간에 Kafka 브로커를도 사이의 통신에 대 한 SSL 암호화를 설정 합니다. 클라이언트의 SSL 인증을 설정 합니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: e526908f5ba9feea53b1c1abebbbfc1bd9a51c54
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147956"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Secure Sockets Layer (SSL) 암호화 및 Azure HDInsight에서 Apache Kafka에 대 한 인증 설정

이 문서에서는 Apache Kafka 클라이언트 및 Apache Kafka 브로커 사이 SSL 암호화를 설정 하는 방법을 보여 줍니다. 또한 클라이언트 (양방향 SSL이 라고도 함)의 인증을 설정 하는 방법을 보여 줍니다.

> [!Important]
> Kafka 응용 프로그램에 대해 사용할 수 있는 두 클라이언트가: Java 클라이언트와 콘솔 클라이언트입니다. Java 클라이언트만 `ProducerConsumer.java` 생성 및 사용에 대 한 SSL을 사용할 수 있습니다. 콘솔의 생산자 클라이언트 `console-producer.sh` SSL 작동 하지 않습니다.

## <a name="apache-kafka-broker-setup"></a>Apache Kafka Broker 설치

SSL Kafka broker 설치는 다음과 같이 4 개의 HDInsight 클러스터 Vm을 사용 합니다.

* 헤드 노드 0-인증 기관 (CA)
* 작업자 노드 0, 1 및 2-broker

> [!Note] 
> 이 가이드에서는 자체 서명된 인증서를 사용하지만, 가장 안전한 솔루션은 신뢰할 수 있는 CA에서 발급한 인증서를 사용하는 것입니다.

Broker 설치 프로세스의 요약 아래와 같습니다.

1. 3 명의 작업자 노드 각각에서 다음 단계를 반복 됩니다.

    1. 인증서를 생성 합니다.
    1. 인증서 서명 요청을 만듭니다.
    1. 인증 기관 (CA)에 요청을 서명 인증서를 보냅니다.
    1. CA에 로그인 하 고 요청을 서명 합니다.
    1. SCP를 작업자 노드로 다시 서명 된 인증서입니다.
    1. SCP의 작업자 노드에 CA 공용 인증서입니다.

1. 있다면 모든 인증서를 인증서 저장소에 인증서를 배치 합니다.
1. Ambari로 이동한 후 구성을 변경 합니다.

Broker 설치를 완료 하려면 다음 자세한 지침을 사용 합니다.

> [!Important]
> 다음 코드 조각에서 wnX 3 명의 작업자 노드 중 하나에 대 한 약어 및 사용 하 여 대체 되어야 함 `wn0`하십시오 `wn1` 또는 `wn2` 적절 하 게 합니다. `WorkerNode0_Name` 및 `HeadNode0_Name` 와 같은 각 컴퓨터의 이름으로 대체 되어야 함 `wn0-abcxyz` 또는 `hn0-abcxyz`합니다.

1. HDInsight에 대 한 인증 기관 (CA)의 역할 입력 된 헤드 노드 0, 초기 설치를 수행 합니다.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl

    # Export
    export SRVPASS=MyServerPassword123
    ```

1. 브로커 (작업자 노드 0, 1, 2)의 각 동일한 초기 설치를 수행 합니다.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl

    # Export
    export MyServerPassword123=MyServerPassword123
    ```

1. 작업자 노드 각각에 아래 코드 조각을 사용 하 여 다음 단계를 실행 합니다.
    1. 키 저장소를 만들고 새 개인 인증서를 사용 하 여 채웁니다.
    1. 인증서 서명 요청을 만듭니다.
    1. SCP는 인증서 서명 요청을 CA (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. CA 컴퓨터에 변경 하 고 모든 수신된 요청을 서명 인증서를 서명 합니다.

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. CA (headnode0)에서 작업자 노드로 다시 서명 된 인증서를 보냅니다.

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. 각 작업자 노드에 CA의 공개 인증서를 보냅니다.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. 각 작업자 노드에서 Ca 공용 인증서 truststore 및 키 저장소에 추가 합니다. 그런 다음 작업자 노드 자체 서명 된 인증서를 키 저장소에 추가

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>SSL을 사용하도록 Kafka 구성 업데이트 및 브로커 다시 시작

Truststore를 키 저장소와 각 Kafka broker를 설정 이제 있고 올바른 인증서를 가져올 수도 있습니다. 다음으로, Ambari를 사용하여 관련된 Kafka 구성 속성을 수정하고 Kafka 브로커를 다시 시작합니다.

구성 수정을 완료하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인하고 Azure HDInsight Apache Kafka 클러스터를 선택합니다.
1. **클러스터 대시보드**에서 **Ambari 홈**을 클릭하여 Ambari UI로 이동합니다.
1. **Kafka 브로커**에서 **수신기** 속성을 `PLAINTEXT://localhost:9092,SSL://localhost:9093`로 설정합니다.
1. **고급 kafka-broker**에서 **security.inter.broker.protocol** 속성을 `SSL`로 설정합니다.

    ![Ambari에서 Kafka SSL 구성 속성 편집](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. **사용자 지정 kafka-broker**에서 **ssl.client.auth** 속성을 `required`로 설정합니다. 이 단계는만 인증 및 암호화를 설정한 경우 필요 합니다.

    ![Ambari에서 kafka SSL 구성 속성 편집](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. FQDN(정규화된 도메인 이름) 대신 IP 주소를 보급하기 위해 Kafka `server.properties` 파일에 구성 속성을 추가합니다.

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. 이전 변경 내용이 올바르게 적용되었는지 확인하기 위해 필요한 경우 Kafka `server.properties` 파일에 다음 줄이 있는지 확인할 수 있습니다.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. 모든 Kafka 브로커를 다시 시작합니다.

## <a name="client-setup-with-authentication"></a>클라이언트 설정(인증 포함)

> [!Note]
> 다음 단계는 SSL 암호화 **및** 인증을 둘 다 설정하는 경우에만 필요합니다. 암호화만 설정하는 경우 [클라이언트 설정(인증 제외)](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)에서 계속 진행하세요.

클라이언트 설정을 완료 하려면 다음 단계를 완료 합니다.

1. 클라이언트 컴퓨터 (hn1)에 로그인 합니다.
1. 클라이언트 암호를 내보냅니다. `<client_password>`를 Kafka 클라이언트 머신의 실제 관리자 암호로 바꿉니다.
1. Java 키 저장소를 만들고 서명된 브로커용 인증서를 가져옵니다. 그런 다음, CA가 실행 중인 VM에 인증서를 복사합니다.
1. 클라이언트 인증서 서명 CA 컴퓨터 (hn0)으로 전환 합니다.
1. 클라이언트 머신(hn1)으로 이동한 다음, `~/ssl` 폴더로 이동합니다. 서명된 인증서를 클라이언트 머신에 복사합니다.

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

마지막으로 `cat client-ssl-auth.properties` 명령을 사용하여 `client-ssl-auth.properties` 파일을 확인합니다. 파일에 다음 줄이 있어야 합니다.

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=<client_password>
ssl.key.password=<client_password>
```

## <a name="client-setup-without-authentication"></a>클라이언트 설정(인증 제외)

인증 필요가 없는 경우에 SSL 암호화를 설정 하는 단계는:

1. 클라이언트 컴퓨터(hn1)에 로그인한 다음 `~/ssl` 폴더로 이동합니다.
1. 클라이언트 암호를 내보냅니다. `<client_password>`를 Kafka 클라이언트 머신의 실제 관리자 암호로 바꿉니다.
1. CA 컴퓨터(wn0)에서 클라이언트 컴퓨터로 서명된 인증서를 복사합니다.
1. 신뢰 저장소로 CA 인증서를 가져옵니다.
1. 키 저장소로 CA 인증서를 가져옵니다.

다음 코드 조각에 이러한 단계가 나와 있습니다.

```bash
export CLIPASS=<client_password>
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

마지막으로 `cat client-ssl-auth.properties` 명령을 사용하여 `client-ssl-auth.properties` 파일을 확인합니다. 파일에 다음 줄이 있어야 합니다.

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
```

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Apache Kafka란?](apache-kafka-introduction.md)
