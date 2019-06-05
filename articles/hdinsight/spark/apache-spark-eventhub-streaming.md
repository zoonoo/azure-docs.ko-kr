---
title: '자습서: Azure HDInsight의 Apache Spark로 Azure Event Hubs에서 데이터 처리 '
description: Azure HDInsight의 Apache Spark를 Azure Event Hubs에 연결하고 스트리밍 데이터를 처리합니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: 0bdcc253a57fb55d610d67acd9b6a50182a699e3
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257841"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>자습서: HDInsight에서 Azure Event Hubs 및 Apache Spark를 사용하여 트윗 처리

이 자습서를 만드는 방법을 알아봅니다는 [Apache Spark](https://spark.apache.org/) 스트리밍 응용 프로그램 이벤트 허브에서 트 윗을 읽을 수 있는 다른 응용 프로그램을 만들고 Azure 이벤트 허브에 트 윗을 보냅니다. Spark 스트리밍에 대한 자세한 내용은 [Apache Spark 스트리밍 개요](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview)를 참조하세요. HDInsight는 Azure에서 Spark 클러스터에 동일한 스트리밍 기능을 제공합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure 이벤트 허브에 메시지 전송
> * Azure 이벤트 허브에서 메시지 읽기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight의 Apache Spark 클러스터입니다. [Apache Spark 클러스터 만들기](./apache-spark-jupyter-spark-sql-use-portal.md)를 참조하세요.

* HDInsight의 Spark에서 Jupyter Notebook을 사용하는 방법 이해. 자세한 내용은 [데이터를 로드 하 고 HDInsight에서 Apache Spark를 사용 하 여 쿼리를 실행](./apache-spark-load-data-run-query.md)합니다.

* A [Twitter 계정](https://twitter.com/i/flow/signup)합니다.

## <a name="create-a-twitter-application"></a>Twitter 애플리케이션 만들기

트윗 스트림을 받으려면 Twitter에 애플리케이션을 만듭니다. 다음 지침에 따라 Twitter 애플리케이션을 만들고 이 자습서를 완료하는 데 필요한 값을 기록합니다.

1. [Twitter 애플리케이션 관리](https://apps.twitter.com/)로 이동합니다.

1. **새 앱 만들기**를 선택합니다.

1. 다음 값을 제공합니다.

    |자산 |값 |
    |---|---|
    |이름|응용 프로그램 이름을 제공 합니다. 이 자습서에 사용되는 값은 **HDISparkStreamApp0423**입니다. 이 이름은 고유한 이름이어야 합니다.|
    |설명|응용 프로그램에 대 한 간단한 설명을 제공 합니다. 이 자습서에 사용되는 값은 **간단한 HDInsight Spark 스트리밍 애플리케이션**입니다.|
    |웹 사이트|응용 프로그램의 웹 사이트를 제공 합니다. 유효한 웹 사이트를 입력하지 않아도 됩니다.  이 자습서에 사용되는 값은 **http://www.contoso.com** 입니다.|
    |콜백 URL|비워 둘 수 있습니다.|

1. **예, Twitter 개발자 계약을 읽고 동의합니다**를 선택한 다음, **Twitter 애플리케이션 만들기**를 선택합니다.

1. **Keys and Access Tokens** 탭을 선택합니다.

1. 페이지의 끝에서 **내 액세스 토큰 만들기**를 선택합니다.

1. 페이지에서 다음 값을 기록합니다.  자습서의 뒷부분에서 이러한 값이 필요합니다.

    - **소비자 키(API 키)**    
    - **소비자 비밀(API 비밀)**  
    - **액세스 토큰**
    - **액세스 토큰 비밀**   

## <a name="create-an-azure-event-hubs-namespace"></a>Azure Event Hubs 네임스페이스 만들기

이 이벤트 허브를 사용하여 트윗을 저장합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.  

3. **사물 인터넷** 아래에서 **Event Hubs**를 선택합니다. 

    ![Spark 스트리밍 예제에 대한 이벤트 허브 만들기](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Spark 스트리밍 예제에 대한 이벤트 허브 만들기")

4. 선택 **+ 추가**합니다.

5. 새 Event Hubs 네임스페이스에 다음 값을 입력합니다.

    |자산 |값 |
    |---|---|
    |이름|이벤트 허브의 이름을 입력합니다.  이 자습서에 사용되는 값은 **myeventhubns20180403**입니다.|
    |가격 책정 계층 |**표준**을 선택합니다.|
    |구독|내게 적합한 구독을 선택합니다.|
    |리소스 그룹|드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기**를 선택하여 새 리소스 그룹을 만듭니다.|
    |Location|대기 시간 및 비용을 줄이려면 HDInsight의 Apache Spark 클러스터와 동일한 **위치**를 선택합니다.|
    |자동 확장 사용 (선택 사항) |자동 팽창 트래픽에 할당 된 처리량 단위의 용량을 초과 하는 경우에 Event Hubs Namespace에 할당 된 처리량 단위 수가 자동으로 조정 합니다.  |
    |(선택 사항) 최대 처리량 단위 자동 확장|확인 하는 경우에이 슬라이더 나타납니다 **사용 자동 팽창**합니다.  |

    ![Spark 스트리밍 예제에 대한 이벤트 허브 이름 제공](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Spark 스트리밍 예제에 대한 이벤트 허브 이름 제공")

6. **만들기**를 선택하여 네임스페이스를 만듭니다.  몇 분 안에 배포가 완료됩니다.

## <a name="create-an-azure-event-hub"></a>Azure 이벤트 허브 만들기
Event Hubs 네임스페이스가 배포된 후에 이벤트 허브를 만듭니다.  포털에서 다음을 수행합니다.

1. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.  

1. **사물 인터넷** 아래에서 **Event Hubs**를 선택합니다.  

1. 목록에서 Event Hubs 네임스페이스를 선택합니다.  

1. **Event Hubs 네임스페이스** 페이지에서 **+ Event Hub**를 선택합니다.  
1. **Event Hubs 만들기** 페이지에 다음 값을 입력합니다.

    - **이름**: Event Hubs의 이름을 지정합니다. 
 
    - **파티션 수**: 10.  

    - **메시지 보존**: 1.   
   
      ![Spark 스트리밍 예제에 대한 이벤트 허브 세부 정보 제공](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Spark 스트리밍 예제에 대한 이벤트 허브 세부 정보 제공")

1. **만들기**를 선택합니다.  몇 초 내에 배포가 완료되고 Event Hubs 네임스페이스 페이지로 돌아갑니다.

1. **설정**에서 **공유 액세스 정책**을 선택합니다.

1. **RootManageSharedAccessKey**를 선택합니다.
    
     ![Spark 스트리밍 예제에 대한 이벤트 허브 정책 설정](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Spark 스트리밍 예제에 대한 이벤트 허브 정책 설정")

1. **기본 키** 및 **연결 문자열-기본 키**의 값을 저장하여 자습서의 뒷부분에서 사용합니다.

     ![Spark 스트리밍 예제에 대한 이벤트 허브 정책 키 보기](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Spark 스트리밍 예제에 대한 이벤트 허브 정책 키 보기")


## <a name="send-tweets-to-the-event-hub"></a>이벤트 허브에 트윗 보내기

Jupyter Notebook을 만들고, **SendTweetsToEventHub**라는 이름을 지정합니다. 

1. 다음 코드를 실행하여 외부 Apache Maven 라이브러리를 추가합니다.

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. 대체 하 여 아래 코드를 편집할 `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`를 `<ACCESS TOKEN>`, 및 `<TOKEN SECRET>` 적절 한 값을 사용 하 여 합니다. 이벤트 허브에 트 윗 보내기 편집한 코드를 실행 합니다.

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Azure Portal에서 이벤트 허브를 엽니다.  **개요**에 이벤트 허브로 전송된 메시지를 보여주는 일부 차트가 표시됩니다.

## <a name="read-tweets-from-the-event-hub"></a>이벤트 허브에서 트윗 읽기

또 다른 Jupyter Notebook을 만들고, **ReadTweetsFromEventHub**라는 이름을 지정합니다. 

1. 다음 코드를 실행하여 외부 Apache Maven 라이브러리를 추가합니다.

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```

2. 대체 하 여 아래 코드를 편집할 `<Event hub name>`, 및 `<Event hub namespace connection string>` 적절 한 값을 사용 하 여 합니다. 이벤트 허브에서 트 윗을 읽습니다 편집된 코드를 실행 합니다.

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>리소스 정리

HDInsight를 사용하면 데이터가 Azure Storage 또는 Azure Data Lake Storage에 저장되므로 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 다음 자습서에서 즉시 작업하려면 클러스터를 유지하는 것이 좋습니다. 그렇지 않으면 계속 진행하여 클러스터를 삭제하는 것이 좋습니다.

Azure Portal에서 클러스터를 열고 **삭제**를 선택합니다.

![HDInsight 클러스터 삭제](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight 클러스터 삭제")

또한 리소스 그룹 이름을 선택하여 리소스 그룹 페이지를 연 다음, **리소스 그룹 삭제**를 선택할 수도 있습니다. 리소스 그룹을 삭제하여 HDInsight Spark 클러스터와 기본 저장소 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Apache Spark 스트리밍 응용 프로그램을 Azure 이벤트 허브에 트 윗 보내기을 만드는 방법을 학습 하 고 이벤트 허브에서 트 윗을 읽을 수 있는 다른 응용 프로그램을 생성 합니다.  기계 학습 애플리케이션을 만들 수 있는지 보려면 다음 문서를 진행합니다.

> [!div class="nextstepaction"]
> [기계 학습 애플리케이션 만들기](./apache-spark-ipython-notebook-machine-learning.md)