---
title: '자습서: Azure Databricks를 사용하여 스트리밍 데이터에 대한 변칙 검색'
description: 데이터에서 변칙을 모니터링 하는 비정상 탐지기 API 및 Azure Databricks를 사용 합니다.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 05/08/2019
ms.author: aahi
ms.openlocfilehash: a5790b5412023f06d9f9fd1d2ff61c11db4c53f3
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807471"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>자습서: Azure Databricks를 사용하여 스트리밍 데이터에 대한 변칙 검색

[Azure Databricks](https://azure.microsoft.com/services/databricks/) 는 빠르고, 쉽고, 공동 작업 Apache Spark 기반 분석 서비스입니다. 비정상 탐지기 API, Azure Cognitive Services의 일부는 시계열 데이터를 모니터링 하는 방법을 제공 합니다. 스트림의 데이터를 거의 실시간으로 변칙 검색을 실행 하려면이 자습서를 사용 하 여 Azure Databricks를 사용 하 여 합니다. Azure Event Hubs를 사용 하 여 twitter 데이터를 수집 하 고 Spark Event Hubs 커넥터를 사용 하 여 Azure Databricks로 가져와야 합니다. 그런 다음 스트리밍된 데이터에서 변칙을 검색 하는 API를 사용 합니다. 

다음 그림에서는 애플리케이션 흐름을 보여줍니다.

![Event Hubs 및 Cognitive Services를 사용하는 Azure Databricks](../media/tutorials/databricks-cognitive-services-tutorial.png "Event Hubs 및 Cognitive Services를 사용하는 Azure Databricks")

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Databricks 작업 영역 만들기
> * Azure Databricks에 Spark 클러스터 만들기
> * 스트리밍 데이터에 액세스하는 Twitter 앱 만들기
> * Azure Databricks에 노트북 만들기
> * Event Hubs 및 Twitter API에 대한 라이브러리 연결
> * 비정상 탐지기 리소스를 만들고 액세스 키 검색
> * Event Hubs에 트윗 보내기
> * Event Hubs에서 트윗 읽기
> * 트 윗에서 변칙 검색 실행

> [!Note]
> 이 자습서에서는 권장 구현 방식은 [솔루션 아키텍처](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) 비정상 탐지기 API에 대 한 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

> [!Note]
> 비정상 탐지기 API에 대 한 무료 평가판 키를 사용 하 여이 자습서를 완료할 수 없습니다. 무료 계정을 사용하여 Azure Databricks 클러스터를 만들려면 클러스터를 만들기 전에 프로필로 이동하고 구독을 **종량제**로 변경합니다. 자세한 내용은 [Azure 체험 계정](https://azure.microsoft.com/free/)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

- [Azure Event Hubs 네임 스페이스](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) 및 이벤트 허브입니다.

- 합니다 [연결 문자열](../../../event-hubs/event-hubs-get-connection-string.md) 에 Event Hubs 네임 스페이스에 액세스 합니다. 연결 문자열은 비슷한 형식에 있어야 합니다.

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- Event Hubs에 대 한 공유 액세스 정책 이름 및 정책 키입니다.

Azure Event Hubs를 참조 하세요 [퀵 스타트](../../../event-hubs/event-hubs-create.md) 네임 스페이스 및 이벤트 허브를 만드는 방법에 대 한 합니다.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks 작업 영역 만들기

이 섹션에서는 사용 하 여 Azure Databricks 작업 영역을 만들 합니다 [Azure portal](https://portal.azure.com/)합니다.

1. Azure Portal에서 **리소스 만들기** > **분석** > **Azure Databricks**를 차례로 선택합니다.

    ![Azure Portal의 Databricks](../media/tutorials/azure-databricks-on-portal.png "Azure Portal의 Databricks")

3. **Azure Databricks 서비스** 아래에서 다음 값을 입력하여 Databricks 작업 영역을 만듭니다.


    |자산  |설명  |
    |---------|---------|
    |**작업 영역 이름**     | Databricks 작업 영역의 이름을 제공합니다.        |
    |**구독**     | 드롭다운에서 Azure 구독을 선택합니다.        |
    |**리소스 그룹**     | 새 리소스 그룹을 만들지, 아니면 기존 그룹을 사용할지 여부를 지정합니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure Resource Manager 개요](../../../azure-resource-manager/resource-group-overview.md)를 참조하세요. |
    |**Location**:     | 선택 **미국 동부 2** 또는 다른 지역 중 하나입니다. 참조 [지역별 사용 가능한 Azure 서비스](https://azure.microsoft.com/regions/services/) 지역 가용성에 대 한 합니다.        |
    |**가격 책정 계층**     |  **표준** 또는 **프리미엄** 중에서 선택합니다. 선택 하지 마십시오 **평가판**합니다. 이러한 계층에 대한 자세한 내용은 [Databricks 가격 페이지](https://azure.microsoft.com/pricing/details/databricks/)를 참조하세요.       |

    **만들기**를 선택합니다.

4. 작업 영역 생성에는 몇 분 정도가 소요됩니다. 

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks에서 Spark 클러스터 만들기

1. Azure Portal에서 사용자가 만든 Databricks 작업 영역으로 이동한 다음, **작업 영역 시작**을 선택합니다.

2. Azure Databricks 포털로 리디렉션됩니다. 포털에서 선택 **새 클러스터**합니다.

    ![Azure의 Databricks](../media/tutorials/databricks-on-azure.png "Azure의 Databricks")

3. 에 **새 클러스터** 페이지에서 클러스터를 만드는 값을 제공 합니다.

    ![Azure에서 Databricks Spark 클러스터 만들기](../media/tutorials/create-databricks-spark-cluster.png "Azure에서 Databricks Spark 클러스터 만들기")

    다음 항목 이외의 다른 모든 기본값을 허용합니다.

   * 클러스터의 이름을 입력합니다.
   * 이 문서에서는 **5.2** 런타임을 사용하여 클러스터를 만듭니다. 선택 하지 마세요 **5.3** 런타임입니다.
   * 있는지 확인 합니다 **후 종료 \_ \_ 분** 확인란을 선택 합니다. 클러스터를 사용 하지 않을 경우 클러스터를 종료 하는 기간 (분)을 제공 합니다.

     **클러스터 만들기**를 선택합니다. 
4. 클러스터 만들기에는 몇 분 정도 소요 됩니다. 클러스터가 실행되면 노트북을 클러스터에 첨부하고 Spark 작업을 실행할 수 있습니다.

## <a name="create-a-twitter-application"></a>Twitter 애플리케이션 만들기

트윗 스트림을 받으려면 Twitter에 애플리케이션을 만들어야 합니다. 다음 단계에 따라 Twitter 애플리케이션을 만들고 이 자습서를 완료하는 데 필요한 값을 기록합니다.

1. 웹 브라우저에서 [Twitter 애플리케이션 관리](https://apps.twitter.com/)로 이동하고 **새 앱 만들기**를 선택합니다.

    ![Twitter 애플리케이션 만들기](../media/tutorials/databricks-create-twitter-app.png "Twitter 애플리케이션 만들기")

2. **애플리케이션 만들기** 페이지에서 새 앱에 대한 세부 정보를 제공한 다음, **Twitter 애플리케이션 만들기**를 선택합니다.

    ![Twitter 애플리케이션 세부 정보](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter 애플리케이션 세부 정보")

3. 애플리케이션 페이지에서 **키 및 액세스 토큰** 탭을 선택하고, **소비자 키** 및 **소비자 비밀**에 대한 값을 복사합니다. 또한 **내 액세스 토큰 만들기**를 선택하여 액세스 토큰을 생성합니다. **액세스 토큰** 및 **액세스 토큰 비밀**에 대한 값을 복사합니다.

    ![Twitter 애플리케이션 세부 정보](../media/tutorials/twitter-app-key-secret.png "Twitter 애플리케이션 세부 정보")

Twitter 애플리케이션에 대해 검색한 값을 저장합니다. 이러한 값은 자습서의 뒷부분에서 필요합니다.

## <a name="attach-libraries-to-spark-cluster"></a>Spark 클러스터에 라이브러리 연결

이 자습서에서는 Twitter API를 사용하여 트윗을 Event Hubs에 보냅니다. 또한 [Apache Spark Event Hubs 커넥터](https://github.com/Azure/azure-event-hubs-spark)를 사용하여 Azure Event Hubs에 데이터를 읽고 씁니다. 이러한 API를 클러스터의 일부로 사용하려면 Azure Databricks에 라이브러리로 추가한 다음, 이를 Spark 클러스터와 연결합니다. 다음 지침에서는 라이브러리를 추가 하는 방법을 합니다 **공유** 작업 영역에서 폴더입니다.

1. Azure Databricks 작업 영역에서 **작업 영역**을 선택한 다음, **공유**를 마우스 오른쪽 단추로 클릭합니다. 바로 가기 메뉴에서 **만들기** > **라이브러리**를 차례로 선택합니다.

   ![라이브러리 추가 대화 상자](../media/tutorials/databricks-add-library-option.png "라이브러리 추가 대화 상자")

2. 새 라이브러리 페이지에서에 대 한 **소스** 선택 **Maven**합니다. 에 대 한 **조정**를 추가 하려는 패키지에 대 한 좌표를 입력 합니다. 이 자습서에서 사용된 라이브러리에 대한 Maven 코디네이트는 다음과 같습니다.

   * Spark Event Hubs 커넥터 - `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API - `org.twitter4j:twitter4j-core:4.0.7`

     ![Maven 코디네이트 제공](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Maven 코디네이트 제공")

3. **만들기**를 선택합니다.

4. 라이브러리를 추가한 폴더를 선택한 다음, 라이브러리 이름을 선택합니다.

    ![추가할 라이브러리 선택](../media/tutorials/select-library.png "추가할 라이브러리 선택")

5. 라이브러리 페이지에 사용 가능한 클러스터 있으면 선택 **클러스터** 고 사용자가 만든 클러스터를 실행 합니다. 상태가 '실행 중' 표시 될 때까지 대기 하 고 라이브러리 페이지로 다시 이동 합니다.
라이브러리 페이지에서 라이브러리를 사용 하 고 클릭 하려는 클러스터를 선택 **설치**합니다. 라이브러리가 클러스터와 성공적으로 연결 되 면 상태를 즉시 변경 **설치 됨**합니다.

    ![클러스터에 라이브러리를 설치](../media/tutorials/databricks-library-attached.png "클러스터에 라이브러리 설치")

6. `twitter4j-core:4.0.7` Twitter 패키지에 대해 이러한 단계를 반복합니다.

## <a name="get-a-cognitive-services-access-key"></a>Cognitive Services 액세스 키 가져오기

이 자습서를 사용 하 여 합니다 [Azure Cognitive Services 비정상 탐지기 Api](../overview.md) 거의 실시간으로 트 윗 스트림을에서 변칙 검색을 실행 합니다. Api를 사용 하기 전에 이상 감지기 리소스를 Azure에 만들고 해야 비정상 탐지기 Api를 사용 하는 액세스 키를 검색 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **+ 리소스 만들기**를 선택합니다.

3. Azure Marketplace 아래에서 선택 **AI + Machine Learning** > **모두 보기** > **Cognitive Services-보다**  >  **이상 감지기**합니다. 또는 사용할 수 있습니다 [이 링크](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) 로 이동 하는 **만들기** 직접 대화 상자.

    ![비정상 탐지기 리소스 만들기](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "이상 감지기 만들 리소스")

4. **만들기** 대화 상자에서 다음 값을 제공합니다.

    |값 |Description  |
    |---------|---------|
    |이름     | 비정상 탐지기 리소스의 이름입니다.        |
    |구독     | Azure 구독 리소스와 연결 합니다.        |
    |Location     | Azure 위치입니다.        |
    |가격 책정 계층     | 서비스에 대 한 가격 책정 계층입니다. 비정상 탐지기 가격 책정에 대 한 자세한 내용은 참조 하세요. [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/)합니다.        |
    |리소스 그룹     | 새 리소스 그룹을 만들지, 아니면 기존 그룹을 선택할지 여부를 지정합니다.        |


     **만들기**를 선택합니다.

5. 리소스를 만든 후에서 합니다 **개요** 탭, 복사 및 저장 합니다 **끝점** 스크린샷에 표시 된 것과 같이 URL입니다. 선택한 **액세스 키 표시**합니다.

    ![액세스 키 표시](../media/tutorials/cognitive-services-get-access-keys.png "액세스 키 표시")

6. 아래 **키**, 사용 하려는 키에 대해 복사 아이콘을 선택 합니다. 액세스 키를 저장 합니다.

    ![액세스 키 복사](../media/tutorials/cognitive-services-copy-access-keys.png "액세스 키 복사")

## <a name="create-notebooks-in-databricks"></a>Databricks에 노트북 만들기

이 섹션에서는 다음 이름을 사용하여 Databricks 작업 영역에 두 개의 노트북을 만듭니다

- **SendTweetsToEventHub** - Twitter에서 트윗을 가져와서 Event Hubs로 스트리밍하는 데 사용하는 생산자 노트북입니다.
- **AnalyzeTweetsFromEventHub** -사용할 Event Hubs에서 트 윗을 읽고 변칙 검색을 실행 하는 소비자 노트북입니다.

1. Azure Databricks 작업 영역 선택 **작업 영역** 왼쪽된 창에서. **작업 영역** 드롭다운에서 **만들기**, **Notebook**을 차례로 선택합니다.

    ![Databricks에서 노트북 만들기](../media/tutorials/databricks-create-notebook.png "Databricks에서 노트북 만들기")

2. 에 **Notebook 만들기** 대화 상자에 입력 합니다 **SendTweetsToEventHub** 이름으로 선택 **Scala** 언어와 이전에 만든 Spark 클러스터를 선택 합니다.

    ![Databricks에서 노트북 만들기](../media/tutorials/databricks-notebook-details.png "Databricks에서 노트북 만들기")

    **만들기**를 선택합니다.

3. 단계를 반복하여 **AnalyzeTweetsFromEventHub** 노트북을 만듭니다.

## <a name="send-tweets-to-event-hubs"></a>Event Hubs에 트윗 보내기

**SendTweetsToEventHub** 노트북에서 다음 코드를 붙여넣고, 자리 표시자를 이전에 만든 Event Hubs 네임스페이스 및 Twitter 애플리케이션에 대한 값으로 바꿉니다. 이 노트는 "Azure" 키워드가 있는 트 윗에서 생성 시간 및 s "좋아요" 수를 추출 하 고 실시간으로 Event Hubs로 스트림 이벤트로.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

노트북을 실행하려면 **Shift+Enter**를 누릅니다. 다음 코드 조각과 같은 출력이 표시됩니다. 출력의 각 이벤트에 타임 스탬프의 조합 되어 "좋아요" s는 Event Hubs로 수집 된 횟수입니다.

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Event Hubs에서 트윗 읽기

에 **AnalyzeTweetsFromEventHub** 노트북에서 다음 코드를 붙여넣고 이전에 만든 이상 감지기 리소스에 대 한 값을 사용 하 여 자리 표시자를 바꿉니다. 이 노트북은 **SendTweetsToEventHub** 노트북을 사용하여 이전에 Event Hubs로 스트리밍한 트윗을 읽습니다.

먼저 이상 감지기를 호출 하는 클라이언트를 작성 합니다. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

노트북을 실행하려면 **Shift+Enter**를 누릅니다. 다음 코드 조각과 같은 출력이 표시됩니다.

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

그런 다음 향후 사용에 대 한 집계 함수를 준비 합니다.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

노트북을 실행하려면 **Shift+Enter**를 누릅니다. 다음 코드 조각과 같은 출력이 표시됩니다.

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

변칙 검색에 대 한 이벤트 허브에서 데이터를 로드 합니다. 이전에 만든 Azure Event Hubs에 대 한 값을 사용 하 여 자리 표시자를 대체 합니다.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

출력에는 이제 다음 이미지와 유사합니다. 사용자가 날짜 테이블의 다를 수 있습니다이 자습서에서는 날짜의 데이터를 실시간으로 참고 합니다.
![부하 데이터에서 이벤트 허브](../media/tutorials/load-data-from-eventhub.png "이벤트 허브에서 데이터 로드")

이제 Apache Spark용 Event Hubs 커넥터를 사용하여 거의 실시간으로 Azure Event Hubs의 데이터를 Azure Databricks로 스트림했습니다. Spark에 Event Hubs 커넥터를 사용하는 방법에 대한 자세한 내용은 [커넥터 설명서](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs)를 참조하세요.



## <a name="run-anomaly-detection-on-tweets"></a>트 윗에서 변칙 검색 실행

이 섹션에서는 이상 감지기 API를 사용 하 여 받은 트 윗에서 변칙 검색을 실행 합니다. 이 섹션에서는 코드 조각을 동일한 **AnalyzeTweetsFromEventHub** 노트북에 추가합니다.

이상 감지를 수행 하려면 먼저 시간별 메트릭 수를 집계 해야 합니다.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
이제 출력에는 다음 코드 조각은 유사합니다.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

그런 다음 델타를 집계 출력 결과를 가져옵니다. 변칙 검색을 더 기록 창을 필요로 하므로 기록 데이터를 검색 하려는 지점에 대 한 델타를 사용 하는 것입니다. 대체는 "[자리 표시자: 테이블 이름]"을 정규화 된 델타 테이블 이름 (예: "트 윗") 만들어야 합니다. 대체 "[자리 표시자: 검사점에 대 한 폴더 이름]"이이 코드를 실행 하는 고유한 각 시간 하는 문자열 값을 사용 하 여 (예를 들어, "etl-에서-eventhub-20190605").
Azure Databricks에서 델타 Lake에 대 한 자세한 내용은를 참조 하십시오 [델타 Lake 가이드](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

대체는 "[자리 표시자: 테이블 이름]" 위에서 선택한 동일한 델타 테이블 이름으로 합니다.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
아래와 같이 출력: 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

이제 집계 된 시계열 데이터는 델타로 지속적으로 수집 됩니다. 그런 다음 최신 지점의 변칙을 검색 하는 시간당 작업을 예약할 수 있습니다. 대체는 "[자리 표시자: 테이블 이름]" 위에서 선택한 동일한 델타 테이블 이름으로 합니다.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
아래와 같이 발생 합니다. 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+


That's it! Using Azure Databricks, you have successfully streamed data into Azure Event Hubs, consumed the stream data using the Event Hubs connector, and then run anomaly detection on streaming data in near real time.
Although in this tutorial, the granularity is hourly, you can always change the granularity to meet your need. 

## Clean up resources

After you have finished running the tutorial, you can terminate the cluster. To do so, in the Azure Databricks workspace, select **Clusters** from the left pane. For the cluster you want to terminate, move the cursor over the ellipsis under **Actions** column, and select the **Terminate** icon and then select **Confirm**.

![Stop a Databricks cluster](../media/tutorials/terminate-databricks-cluster.png "Stop a Databricks cluster")

If you don't manually terminate the cluster it will automatically stop, provided you selected the **Terminate after \_\_ minutes of inactivity** checkbox while creating the cluster. In such a case, the cluster will automatically stop if it has been inactive for the specified time.

## Next steps

In this tutorial, you learned how to use Azure Databricks to stream data into Azure Event Hubs and then read the streaming data from Event Hubs in real time. Advance to the next tutorial to learn how to call the Anomaly Detector API and visualize anomalies using Power BI desktop. 

> [!div class="nextstepaction"]
>[Batch anomaly detection with Power BI desktop](batch-anomaly-detection-powerbi.md)
