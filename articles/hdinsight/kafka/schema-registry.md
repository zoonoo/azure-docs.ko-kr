---
title: Azure HDInsight에서 Confluent 스키마 레지스트리 Apache Kafka
description: Virtual Network 내에 지 노드를 사용 하 여 HDInsight 관리 Kafka 클러스터를 배포한 다음에 지 노드에 Confluent 스키마 레지스트리를 설치 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: bb46bc18469638416ff76f84516498e0076c85fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500325"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Azure HDInsight에서 Confluent 스키마 레지스트리 Apache Kafka

Kafka Schema 레지스트리는 Avro 형식으로 전송 되는 Kafka 메시지를 검색 하 고 메시지 스키마 저장소를 처리 하는 Apache Kafka 클라이언트에 연결 하는 serializer를 제공 합니다. Confluent를 사용 하 여 OSS 프로젝트 이지만 이제는 [Confluent community 라이선스로](https://www.confluent.io/blog/license-changes-confluent-platform/)사용 됩니다. 스키마 레지스트리는 다음과 같은 용도로 추가로 제공 됩니다.

* 생산자와 소비자에 대 한 스키마를 저장 하 고 검색 합니다.
* 항목에 대해 뒤로/앞으로/full 호환성을 적용 합니다.
* Kafka으로 전송 된 페이로드의 크기를 줄입니다.

HDInsight 관리 Kafka 클러스터에서 스키마 레지스트리는 일반적으로 헤드 노드의 계산 분리를 허용 하기 위해에 지 노드에 배포 됩니다.

다음은 HDInsight 클러스터에서 스키마 레지스트리를 배포 하는 방법의 대표적인 아키텍처입니다. 스키마 레지스트리는 해당 작업에 대 한 REST API를 기본적으로 노출 합니다.  생산자와 소비자는 VNet 내에서 또는 [Kafka REST 프록시](rest-proxy.md)를 사용 하 여 스키마 레지스트리와 상호 작용할 수 있습니다.

![HDInsight Kafka 스키마 레지스트리](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>Confluent Schema Registry를 사용 하 여 HDInsight 관리 Kafka 배포

이 섹션에서는 가상 네트워크 내에 지 노드를 사용 하 여 HDInsight 관리 Kafka 클러스터를 배포한 후에 지 노드에 Confluent 스키마 레지스트리를 설치 합니다.  

1. 아래 **Azure에 배포** 단추를 선택하여 Azure에 로그인하고 Resource Manager 템플릿을 엽니다.

    [![Azure에 배포](./media/schema-registry/hdi-deploy-to-azure1.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json)

1. 사용자 지정 배포 템플릿에서 아래에 설명 된 대로 필드를 채웁니다.

    |속성 |설명 |
    |---|---|
    |Subscription|드롭다운 목록에서 클러스터에 사용할 Azure 구독을 선택합니다.|
    |Resource group|드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택합니다.|
    |지역|드롭다운 목록에서 클러스터를 만들 지역을 선택합니다.|
    |클러스터 이름|전역적으로 고유한 이름을 입력합니다. 또는 기본 이름을 사용 하려면 그대로 둡니다.|
    |클러스터 로그인 사용자 이름|사용자 이름을 입력합니다. 기본값은 **admin** 입니다.|
    |클러스터 로그인 암호|암호를 입력합니다.|
    |SSH 사용자 이름|사용자 이름을 제공 합니다. 기본값은 **sshuser** 입니다.|
    |SSH 암호|암호를 입력합니다.|

    다른 필드는 그대로 둡니다. **검토 + 만들기** 를 선택하여 계속 진행합니다.

1. 배포 세부 정보를 검토 한 다음 **만들기** 를 선택 하 여 배포를 초기화 합니다. 배포를 완료 하는 데 45 분 정도 걸릴 수 있습니다.

## <a name="configure-the-confluent-schema-registry"></a>Confluent 스키마 레지스트리 구성

이 섹션에서는에 지 노드에 설치한 Confluent Kafka 스키마 레지스트리를 구성 합니다. Confluent 스키마 레지스트리는에 있으며  `/etc/schema-registry/schema-registry.properties` 서비스 실행 파일을 시작 하 고 중지 하는 메커니즘은 폴더에 있습니다  `/usr/bin/` .

스키마 레지스터가 HDInsight Kafka 클러스터와 상호 작용할 수 있는지 확인 해야 합니다. 아래 단계에 따라 사육 사 쿼럼에 대 한 세부 정보를 가져옵니다.

1. [Ssh 명령을](../hdinsight-hadoop-linux-use-ssh-unix.md) 사용 하 여 클러스터에 지 노드에 연결 합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 암호 변수를 설정합니다. PASSWORD를 클러스터 로그인 암호로 바꾸고 명령을 입력 합니다.

    ```bash
    export password='PASSWORD'
    ```

1. 대/소문자가 올바르게 지정된 클러스터 이름을 추출합니다. 다음 명령을 실행합니다.

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. Kafka 사육 사육 호스트를 추출 합니다. 다음 명령을 실행합니다.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    나중에 사용 되므로이 값을 기록해 둡니다.

1. Kafka broker 호스트의 압축을 풉니다. 다음 명령을 실행합니다.

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. 편집 모드에서 스키마 레지스트리 속성 파일을 엽니다. 다음 명령을 실행합니다.

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. 의 값을 `kafkastore.connection.url` 앞에서 식별 된 사육 사 문자열로 업데이트 합니다.
    1. 의 값을 `debug` 로 업데이트 `true` 합니다.

    속성 파일은 다음과 유사 하 게 표시 됩니다.  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. 파일을 저장하려면 **Ctrl + X**, **Y** 및 **Enter** 키를 사용합니다.

1. 스키마 레지스트리를 시작 하 고 업데이트 된 스키마 레지스트리 속성 파일을 사용 하도록 가리킵니다. 다음 명령을 실행합니다.

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. 하나의 SSH 세션에서 실행 되는 스키마 레지스트리를 사용 하 여 다른 SSH 창을 시작 합니다.  "Kafka 키" 라는 제목의 새 버전의 스키마를 등록 하 고 출력을 확인 합니다.

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. "Kafka-value" 라는 제목의 새 버전의 스키마를 등록 하 고 출력을 확인 합니다.

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. 모든 주제를 나열 하 고 출력을 확인 합니다.

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

[여기에 나열 된 다른 고급 명령을](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart)사용해 볼 수 있습니다.

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>Schema registry를 사용 하 여 Kafka에서 Avro 데이터 전송 및 사용

이 섹션에서는 표준 입력에서 데이터를 읽고 형식으로 Kafka 토픽에 씁니다. 그런 다음 Avro 포맷터가 있는 소비자를 사용 하 여 Kafka 토픽에서 데이터를 읽고 해당 데이터를 읽을 수 있는 형식으로 변환 합니다.

1. Kafka 토픽을 만듭니다 `agkafkaschemareg` .

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. **Kafka Avro 콘솔 공급자** 를 사용 하 여 스키마를 만들고, 토픽에 스키마를 할당 하 고, 데이터를 Avro 형식으로 항목에 보내기 시작 합니다. 이전 단계의 Kafka 항목이 성공적으로 만들어지고 **$KAFKABROKERS** 에 값이 있는지 확인 합니다.

    전송 중인 스키마는 키: 값 쌍입니다.

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    다음 명령을 사용 하 여 **Kafka Avro 콘솔 생산자** 를 시작 합니다.

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. 생산자가 메시지를 받아들일 준비가 되 면 미리 정의 된 Avro 스키마 형식으로 메시지 보내기를 시작 합니다. 키와 값 사이에 간격을 만들려면 Tab 키를 사용 합니다.

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. 콘솔 생산자에 스키마가 아닌 임의의 데이터를 입력 하 여 생산자가 미리 정의 된 Avro 스키마를 준수 하지 않는 모든 데이터를 허용 하는지 확인 하세요.

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. 다른 화면에서 Kafka Avro 콘솔 소비자를 시작 합니다.

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    아래 출력이 표시 되기 시작 해야 합니다.

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>다음 단계

* [Apache Kafka 생산자 및 소비자 Api 사용](apache-kafka-producer-consumer-api.md)