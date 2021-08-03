---
title: Apache Atlas Spark 커넥터의 메타데이터 및 계보
description: 이 문서에서는 Atlas Spark 커넥터를 사용하여 Spark에서 데이터 계보를 추출하는 방법을 설명합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/28/2021
ms.openlocfilehash: aebe227c8f2a278131d38143c43c38ee4687e701
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579056"
---
# <a name="how-to-use-apache-atlas-connector-to-collect-spark-lineage"></a>Apache Atlas 커넥터를 사용하여 Spark 계보를 수집하는 방법

Apache Atlas Spark 커넥터는 Spark SQL/DataFrame의 데이터 이동을 추적하고 Purview Atlas 엔드포인트에 메타데이터 변경 내용을 푸시하는 후크입니다. 

## <a name="supported-scenarios"></a>지원되는 시나리오

이 커넥터는 다음 추적 기능을 지원합니다.
1.  SQL DDL(예: “CREATE/ALTER DATABASE”, “CREATE/ALTER TABLE”)
2.  SQL DML(예: “CREATE TABLE HelloWorld AS SELECT”, “INSERT INTO...”, “LOAD DATA [LOCAL] INPATH”, “INSERT OVERWRITE [LOCAL] DIRECTORY”)
3.  입력 및 출력을 포함하는 DataFrame 이동.

이 커넥터는 쿼리 수신기를 사용하여 쿼리를 검색하고 영향을 검사합니다. 이는 Hive, HDFS와 같은 다른 시스템과의 상관관계를 사용하여 Atlas에서 데이터의 수명 주기를 추적합니다.
Purview는 Atlas API 및 Atlas 기본 후크를 지원하므로 Spark를 사용하여 구성한 후에는 커넥터에서 데이터 계보를 Purview에 보고할 수 있습니다. 커넥터는 작업별로 구성하거나 클러스터 기본 설정으로 구성할 수 있습니다. 

## <a name="configuration-requirement"></a>구성 요구 사항

커넥터에는 Spark 2.4.0 이상 버전이 필요합니다. 단, Spark 버전 3은 지원되지 않습니다. Spark는 설정이 필요한 3가지 유형의 수신기를 지원합니다.  

| 수신기 |    Spark 버전 최소 요구 사항|
| ------------------- | ------------------- | 
| spark.extraListeners | 1.3.0 |
| spark.sql.queryExecutionListeners | 2.3.0 |
| spark.sql.streaming.streamingQueryListeners | 2.4.0 |

Spark 클러스터 버전이 2.4.0 미만이면 스트림 쿼리 계보와 대부분의 쿼리 계보가 캡처되지 않습니다.

### <a name="step-1-prepare-spark-atlas-connector-package"></a>1단계. Spark Atlas 커넥터 패키지 준비
다음 단계는 DataBricks를 기준으로 문서화된 예제입니다.

1.  패키지 생성
    1. GitHub에서 코드 가져오기: https://github.com/hortonworks-spark/spark-atlas-connector
    2. [Windows용] spark-atlas-connector\pom.xml에서 **maven-enforcer-plugin** 을 주석으로 처리하여 Unix 종속성을 제거합니다.

    ```web
    <requireOS>
        <family>unix</family>
    </requireOS>
    ```
    
    다. 빌드할 프로젝트 루트에서 **mvn package -DskipTests** 명령을 실행합니다. 
    
    d. *~\spark-atlas-connector-assembly\target\spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar* 에서 jar 가져오기
    
    e. Spark 클러스터에서 액세스할 수 있는 패키지를 배치합니다. DataBricks 클러스터의 경우 패키지를 dbfs 폴더(예:/FileStore/jars)에 업로드할 수 있습니다.

2. 커넥터 구성 준비
    1. Purview 계정의 Azure Portal에서 Kafka 엔드포인트 및 자격 증명 가져오기
        1. 계정에 *“Purview Data Curator”* 사용 권한 제공 

        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/assign-purview-data-curator-role.png" alt-text="데이터 큐레이터 역할 할당 방법을 보여 주는 스크린샷" lightbox="./media/how-to-lineage-spark-atlas-connector/assign-purview-data-curator-role.png":::

        1. 엔드포인트: ‘Atlas Kafka 엔드포인트 기본 연결 문자열’에서 가져옵니다. 엔드포인트 부분
        1. 자격 증명: 전체 ‘Atlas Kafka 엔드포인트 기본 연결 문자열’
        
        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/atlas-kafka-endpoint.png" alt-text="Atlas kafka 엔드포인트를 보여 주는 스크린샷" lightbox="./media/how-to-lineage-spark-atlas-connector/atlas-kafka-endpoint.png":::        
        
    1. *atlas-application.properties* 파일을 준비하고, *atlas.kafka.sasl.jaas.config* 에서 *atlas.kafka.bootstrap.servers* 및 암호 값 변경

    ```script
    atlas.client.type=kafka
    atlas.kafka.sasl.mechanism=PLAIN
    atlas.kafka.security.protocol=SASL_SSL
    atlas.kafka.bootstrap.servers= atlas-46c097e6-899a-44aa-9a30-6ccd0b2a2a91.servicebus.windows.net:9093
    atlas.kafka.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="<connection string got from your Purview account>";
    ```
    
    다.  [위의 1단계, 패키지 생성 섹션](../purview/how-to-lineage-spark-atlas-connector.md#step-1-prepare-spark-atlas-connector-package)에서 생성된 드라이버의 클래스 경로에 atlas 구성 파일이 있어야 합니다. 클러스터 모드에서 이 구성 파일을 원격 드라이브 *--files atlas-application.properties* 에 제공


### <a name="step-2-prepare-your-purview-account"></a>2단계. Purview 계정 준비
Atlas Spark 모델 정의가 성공적으로 만들어지면 아래 단계를 수행합니다.
1. GitHub(https://github.com/apache/atlas/blob/release-2.1.0-rc3/addons/models/1000-Hadoop/1100-spark_model.json )에서 spark 유형 정의 가져오기

2. 역할 할당:
    1. Purview 계정으로 이동하여 액세스 제어(IAM) 선택 
    1. 사용자를 추가하고 서비스 주체 ‘Purview 데이터 원본 관리자’ 역할을 부여합니다.
3. 인증 토큰 가져오기:
    1. “postman” 또는 유사한 도구 열기 
    1. 이전 단계에서 사용한 서비스 주체를 사용하여 전달자 토큰을 가져옵니다.
        * 엔드포인트: https://login.windows.net/microsoft.com/oauth2/token
        * grant_type: client_credentials
        * client_id: {서비스 주체 ID}
        * client_secret: {서비스 주체 키}
        * resource: `https://purview.azure.net`

        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/postman-examples.png" alt-text="postman 예제를 보여 주는 스크린샷" lightbox="./media/how-to-lineage-spark-atlas-connector/postman-examples.png":::      

4. Purview 계정에 Spark Atlas의 모델 정의를 게시합니다.
    1.  Azure Portal의 속성 섹션에서 Purview 계정의 Atlas 엔드포인트를 가져옵니다.
    1. Purview 계정에 Spark 유형 정의를 게시합니다.
       * 게시: {{엔드포인트}}/api/atlas/v2/types/typedefs
       * 생성된 액세스 토큰 사용 
       * 본문: GitHub(https://github.com/apache/atlas/blob/release-2.1.0-rc3/addons/models/1000-Hadoop/1100-spark_model.json )에서 원시 항목을 선택하고 모든 콘텐츠 복사

:::image type="content" source="./media/how-to-lineage-spark-atlas-connector/postman-example-type-definition.png" alt-text="postman의 형식 정의 예제를 보여 주는 스크린샷" lightbox="./media/how-to-lineage-spark-atlas-connector/postman-example-type-definition.png":::

### <a name="step-3-prepare-spark-job"></a>3단계. Spark 작업 준비
1. Spark 작업을 정상적으로 작성
2. Spark 작업의 소스 코드에 커넥터 설정을 추가합니다. 다음과 같이 코드에서 *‘atlas.conf’* 시스템 속성 값을 설정하여 *atlas-application.properties* 파일을 찾을 수 있도록 합니다.

    **System.setProperty("atlas.conf", "/dbfs/FileStore/jars/")**

3. Spark 작업 소스 코드를 빌드하여 jar 파일을 생성합니다. 
4. Spark 애플리케이션 jar 파일을 클러스터에서 액세스할 수 있는 위치에 배치합니다. 예를 들어 jar 파일을 *"/dbfs/FileStore/jars/"DataBricks* 에 배치합니다. 

### <a name="step-4-prepare-to-run-job"></a>4단계. 작업 실행 준비
 
1. 아래 지침은 각 작업 설정에 대해 설명합니다. 특정 작업의 계보를 캡처하려면 spark-submit을 사용하여 해당 매개 변수에 따른 작업을 시작합니다. 

    작업 매개 변수 설정에서:
* 커넥터 jar 파일의 경로를 설정합니다. 
* extraListeners, queryExecutionListeners, streamingQueryListeners의 세 가지 수신기를 커넥터로 설정합니다. 

| 수신기 | 세부 정보 |
| ------------------- | ------------------- | 
| spark.extraListeners  | com.hortonworks.spark.atlas.SparkAtlasEventTracker|
| spark.sql.queryExecutionListeners | com.hortonworks.spark.atlas.SparkAtlasEventTracker
| spark.sql.streaming.streamingQueryListeners | com.hortonworks.spark.atlas.SparkAtlasStreamingQueryEventTracker |

* Spark 작업 애플리케이션 jar 파일의 경로를 설정합니다.

Databricks 작업 설정: 핵심 부분은 수신기를 올바르게 설정하고 spark-submit을 사용해 작업을 실행하는 것입니다. 작업 매개 변수에서 수신기 정보를 설정합니다.   

다음은 spark 작업에 대한 예제 매개 변수입니다.

```script
["--jars","dbfs:/FileStore/jars/spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar ","--conf","spark.extraListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker","--conf","spark.sql.queryExecutionListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker","--conf","spark.sql.streaming.streamingQueryListeners=com.hortonworks.spark.atlas.SparkAtlasStreamingQueryEventTracker","--class","com.microsoft.SparkAtlasTest","dbfs:/FileStore/jars/08cde51d_34d8_4913_a930_46f376606d7f-sparkatlas_1_6_SNAPSHOT-17452.jar"]
```

다음은 명령줄에서의 spark 전송 예입니다.

```script
spark-submit --class com.microsoft.SparkAtlasTest --master yarn --deploy-mode --files /data/atlas-application.properties --jars /data/ spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar 
--conf spark.extraListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker 
--conf spark.sql.queryExecutionListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker 
--conf spark.sql.streaming.streamingQueryListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker
/data/worked/sparkApplication.jar
```

2. 아래 지침은 클러스터 설정에 대해 설명합니다. 커넥터 jar 및 수신기 설정은 Spark 클러스터의: *conf/spark-defaults.conf* 에 배치해야 합니다. spark-submit은 *conf/spark-defaults.conf* 의 옵션을 읽어 애플리케이션에 전달합니다. 
 
### <a name="step-5-run-and-check-lineage-in-purview-account"></a>5단계. Purview 계정에서 실행하여 계보를 확인합니다.
Spark 작업을 시작하고 Purview 계정에서 계보 정보를 확인합니다. 

:::image type="content" source="./media/how-to-lineage-spark-atlas-connector/purview-with-spark-lineage.png" alt-text="purview에서 spark 계보를 보여 주는 스크린샷" lightbox="./media/how-to-lineage-spark-atlas-connector/purview-with-spark-lineage.png":::

## <a name="known-limitations-with-the-connector-for-spark-lineage"></a>Spark 계보의 커넥터에 대해 알려진 제한 사항
1. SQL/DataFrame API를 지원합니다. 즉, RDD를 지원하지 않습니다. 이 커넥터는 쿼리 수신기를 사용하여 쿼리를 검색하고 영향을 검사합니다.
    
2. 여러 쿼리의 모든 “입력” 및 “출력”은 단일 “spark_process” 엔터티로 결합됩니다.
    
    “spark_process”는 Spark의 “applicationId”에 매핑됩니다. 이를 통해 관리자는 애플리케이션에서 발생한 모든 변경 내용을 추적할 수 있습니다. 하지만 동시에 “spark_process”의 계보/관계 그래프가 복잡해지고 의미가 줄어듭니다.
3. 스트리밍 쿼리에서는 입력의 일부만 추적됩니다.

* Kafka source는 “패턴”을 사용한 구독을 지원하고 이 커넥터는 기존의 일치하는 토픽 또는 가능한 모든 토픽에서 일부만 열거합니다. 
 
* “실행된 계획”은 (마이크로) 일괄 처리 읽기 및 프로세스를 사용하여 실제 토픽을 제공합니다. 따라서 (마이크로) 일괄 처리에 참여하는 입력만 “spark_process” 엔터티의 “입력”으로 포함됩니다.
    
4. 이 커넥터는 열 수준 계보를 지원하지 않습니다.

5. 삭제된 테이블을 추적하지 않습니다(Spark 모델).

    Spark의 “테이블 삭제” 이벤트는 db 및 테이블 이름만 제공하므로 테이블을 인식하는 고유 키를 만드는 데에는 충분하지 않습니다.

    커넥터는 Spark 카탈로그를 읽어 테이블 정보를 가져오는 방법을 활용합니다. 이 커넥터가 테이블이 삭제됨을 확인하면 Spark에서 이미 테이블을 삭제했으므로 테이블 삭제는 이루어지지 않습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Purview의 데이터 계보에 대해 알아보기](catalog-lineage-user-guide.md)
- [Azure Data Factory를 연결하여 자동화된 계보 푸시](how-to-link-azure-data-factory.md)
