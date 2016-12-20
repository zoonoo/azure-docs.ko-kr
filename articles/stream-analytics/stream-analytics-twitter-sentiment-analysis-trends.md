---
title: "Stream Analytics을 사용한 실시간 Twitter 정서 분석 | Microsoft Docs"
description: "실시간 Twitter 정서 분석에 대한 Stream Analytics을 사용하는 방법에 대해 알아봅니다. 이벤트 생성부터 라이브 대시보드의 데이터에 이르는 단계별 지침이 포함되어 있습니다."
keywords: "실시간 twitter 분석 추세, 정서 분석, 소셜 미디어 분석, 추세 분석 예제"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/14/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 3c97604b17636f011ddb2acda40fbc77afeab590
ms.openlocfilehash: 9f7e9008f29b2b1a3a0422133e15871c4ce7cca8


---
# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>소셜 미디어 분석: Azure Stream Analytics에서 실시간 Twitter 정서 분석
실시간 Twitter 이벤트를 Azure Event Hubs로 가져와서 소셜 미디어 분석을 위한 감정 분석 솔루션을 구축하는 방법을 알아봅니다. 데이터를 분석하는 Azure Stream Analytics 쿼리를 작성합니다. 다음 자세히 확인하기 위해 결과를 저장하거나 대시보드 및 [Power BI](https://powerbi.com/)를 사용하여 실시간으로 통찰력을 제공할 수 있습니다.

소셜 미디어 분석 도구는 조직이 소셜 미디어에서 대량의 게시물을 사용하여 추세 항목, 의미 있는 주제 및 자세를 이해하도록 돕습니다. *의견 마이닝*이라는 감정 분석은 소셜 미디어 분석 도구를 사용하여 제품, 아이디어 등에 대한 자세를 결정합니다. 실시간 Twitter 추세 분석은 아주 좋은 예입니다. 특정 키워드를 수신하고 피드에 대한 감정 분석을 개발할 수 있는 해시 태그 구독 모델을 사용하기 때문입니다.

## <a name="scenario-sentiment-analysis-in-real-time"></a>시나리오: 실시간 감정 분석
뉴스 미디어 웹 사이트를 보유하는 기업은 독자와 직접적으로 관련이 있는 사이트 콘텐츠를 부각시켜 경쟁 우위를 확보하는 데 관심이 있습니다. 이러한 기업은 Twitter 데이터에 대해 실시간으로 감정을 분석하여 독자와 관련된 항목에 대한 소셜 미디어 정보를 사용합니다. 특히 Twitter에서 실시간으로 추세를 분석할 토픽을 식별하기 위해 기업에서는 주요 토픽에 대한 트윗 볼륨 및 감정에 대한 실시간 분석이 필요합니다. 따라서 기본적으로 이 소셜 미디어 피드를 기반으로 하는 감정 분석 엔진이 필요합니다.

## <a name="prerequisites"></a>필수 조건
* Twitter 계정 및 [OAuth 액세스 토큰](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) 
* 선택 사항: [Github](https://aka.ms/azure-stream-analytics-twitterclient)twitter 클라이언트에 대한 소스 코드

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>이벤트 허브 입력 및 소비자 그룹 만들기
샘플 응용 프로그램은 이벤트를 생성하여 Event Hubs 인스턴스(간단히 이벤트 허브라고 함)에 푸시합니다. Service Bus 이벤트 허브는 Stream Analytics를 위해 이벤트를 수집하는 기본 방법입니다. [Azure Service Bus 설명서](/azure/service-bus/)에서 Event Hubs 설명서를 참조하세요.

다음 단계에 따라 이벤트 허브를 만듭니다.

1. Azure 포털에서 **새로 만들기** > **App Services** > **Service Bus** > **Event Hubs** > **빨리 만들기**를 클릭하고 이름, 하위 지역 및 새 네임스페이스 또는 기존 네임스페이스를 제공합니다.  
2. 각 Stream Analytics 작업이 단일 Event Hubs 소비자 그룹에서 읽는 것이 가장 좋습니다. 나중에 소비자 그룹을 만드는 과정을 단계별로 안내합니다. [Azure Event Hubs 개요](https://msdn.microsoft.com/library/azure/dn836025.aspx)에서 소비자 그룹에 대해 자세히 알아볼 수 있습니다. 소비자 그룹을 만들려면 새로 만든 이벤트 허브로 이동하여 **소비자 그룹** 탭을 클릭한 다음 페이지 아래쪽에서 **만들기**를 클릭하고 소비자 그룹의 이름을 입력합니다.
3. 이벤트 허브에 대한 액세스 권한을 부여하려면 공유 액세스 정책을 만들어야 합니다. 이벤트 허브의 **구성** 탭을 클릭합니다.
4. **공유 액세스 정책**에서 **관리** 권한을 사용하여 새 정책을 만듭니다.

   ![관리 권한을 사용하여 정책을 만들 수 있는 공유 액세스 정책입니다.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)
5. 페이지 아래쪽에서 **저장** 을 클릭합니다.
6. **대시보드**로 이동하여 페이지 아래쪽에서 **연결 정보**를 클릭한 다음 연결 정보를 복사 및 저장합니다. 검색 아이콘 아래에 표시된 복사 아이콘을 사용하세요.

## <a name="configure-and-start-the-twitter-client-application"></a>Twitter 클라이언트 응용 프로그램 구성 및 시작
Microsoft에서는 매개 변수화된 항목 집합에 대한 트윗 이벤트를 수집하기 위해 [Twitter의 스트리밍 API](https://dev.twitter.com/streaming/overview)를 통해 Twitter 데이터에 연결하는 클라이언트 응용 프로그램을 제공합니다. [Sentiment140](http://help.sentiment140.com/) 공개 소스 도구는 각 트윗에 감정 값을 할당하는 데 사용됩니다.

* 0 = 부정적
* 2 = 중립
* 4 = 긍정적

그런 다음 트윗 이벤트가 이벤트 허브로 푸시됩니다.  

다음 단계에 따라 응용 프로그램을 설치합니다.

1. [TwitterClient 솔루션을 다운로드합니다](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2. TwitterClient.exe.config를 열고 oauth_consumer_key, oauth_consumer_secret, oauth_token, oauth_token_secret을 원하는 값의 Twitter 토큰으로 바꿉니다.  

   [OAuth 액세스 토큰을 생성하는 단계](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   토큰을 생성하는 빈 응용 프로그램을 만들어야 합니다.  
3. TwitterClient.exe.config의 EventHubConnectionString 및 EventHubName 값을 이벤트 허브의 연결 문자열 및 이름으로 바꿉니다. 앞에서 복사한 연결 문자열을 사용하면 이벤트 허브의 연결 문자열과 이름이 모두 제공되어 이를 구분하고 올바른 필드에 각각 추가할 수 있습니다. 예를 들어 다음 연결 문자열을 고려해 보겠습니다.

     Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

   TwitterClient.exe.config 파일에는 다음 예제와 같은 설정이 포함되어야 합니다.

     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"   add key="EventHubName" value="yourhub"

   EventHubName 값에는 텍스트 "EntityPath="가 나타나지 **않습니다**.
4. *선택 사항:* 검색할 키워드를 조정합니다.  기본적으로 이 응용 프로그램은 "Azure,Skype,XBox,Microsoft,Seattle"을 찾습니다.  필요한 경우 TwitterClient.exe.config에서 **twitter_keywords** 값을 조정할 수 있습니다.
5. TwitterClient.exe를 실행하여 응용 프로그램을 시작합니다. **CreatedAt**, **Topic** 및 **SentimentScore** 값이 이벤트 허브로 전송 중인 트윗 이벤트가 표시됩니다.

   ![정서 분석: 이벤트 허브로 전송되는 SentimentScore 값](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기
이제 Twitter에서 실시간으로 트윗 이벤트 스트림을 만들었으므로 이러한 이벤트를 실시간으로 분석하도록 Stream Analytics job 작업을 설정할 수 있습니다.

### <a name="provision-a-stream-analytics-job"></a>Stream Analytics 작업 프로비전
1. [Azure Portal](https://manage.windowsazure.com/)에서 **새로 만들기** > **Data Services** > **Stream Analytics** > **빨리 만들기**를 클릭합니다.
2. 다음 값을 지정하고 **Stream Analytics 작업 만들기**를 클릭합니다.

   * **작업 이름**: 작업 이름을 입력합니다.
   * **지역**: 작업을 실행할 지역을 선택합니다. 더 나은 성능을 보장하고 비용 부담 없이 지역 간에 데이터를 전송하려면 동일한 지역에 작업 및 이벤트 허브를 배치하는 것이 좋습니다.
   * **Storage 계정**: 이 하위 지역 내에서 실행되는 모든 Stream Analytics 작업에 대한 모니터링 데이터를 저장하는 데 사용하려는 Azure Storage 계정을 선택합니다. 기존 저장소 계정을 선택하거나 새 계정을 만들 수 있습니다.
3. 왼쪽 창에서 **Stream Analytics** 을 클릭하여 Stream Analytics 작업을 표시합니다.  
   ![Stream Analytics 서비스 아이콘](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

   새 작업이 **생성됨**상태로 표시됩니다. 페이지 아래쪽에 있는 **시작** 단추는 사용할 수 없게 설정됩니다. 작업을 시작하려면 먼저 작업 입력, 출력 및 쿼리를 구성해야 합니다.

### <a name="specify-job-input"></a>작업 입력 지정
1. Stream Analytics 작업의 페이지 위쪽에서 **입력**을 클릭하고 **입력 추가**를 클릭합니다. 열리는 대화 상자에서 다양한 단계를 진행하면서 입력을 설정하게 됩니다.
2. **데이터 스트림**을 클릭한 후 오른쪽 단추를 클릭합니다.
3. **이벤트 허브**를 클릭한 후 오른쪽 단추를 클릭합니다.
4. 세 번째 페이지에서 다음 값을 입력하거나 선택합니다.

   * **입력 별칭**: 이 작업 입력의 이름(예: *TwitterStream*)을 입력합니다. 이 이름은 나중에 쿼리에서 사용하게 됩니다.
     **이벤트 허브**: 만든 이벤트 허브가 Stream Analytics 작업과 동일한 구독에 포함된 경우 이벤트 허브가 있는 네임스페이스를 선택합니다.

     이벤트 허브가 다른 구독에 있으면 **다른 구독의 이벤트 허브 사용**을 클릭하고 **네임스페이스**, **이벤트 허브 이름**, **이벤트 허브 정책 이름**, **이벤트 허브 정책 키** 및 **이벤트 허브 파티션 수**에 대한 정보를 수동으로 입력합니다.
   * **이벤트 허브 이름**: 이벤트 허브의 이름을 선택합니다.
   * **이벤트 허브 정책 이름**: 이 자습서의 앞부분에서 만든 이벤트 허브 정책을 선택합니다.
   * **이벤트 허브 소비자 그룹**: 이 자습서의 앞부분에서 만든 소비자 그룹의 이름을 입력합니다.
5. 오른쪽 단추를 클릭합니다.
6. 다음 값을 지정합니다.

   * **이벤트 직렬 변환기 형식**: JSON
   * **인코딩**: UTF8
7. **확인** 단추를 클릭하여 이 소스를 추가하고 Stream Analytics가 이벤트 허브에 성공적으로 연결될 수 있는지 확인합니다.

### <a name="specify-job-query"></a>작업 쿼리 지정
Stream Analytics는 변환을 설명하는 간단하고 선언적인 쿼리 모델을 지원합니다. 이 언어에 대한 자세한 내용은 [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)를 참조하세요.  이 자습서에서는 Twitter 데이터에 대해 여러 쿼리를 작성하고 테스트하도록 도와줍니다.

#### <a name="sample-data-input"></a>샘플 데이터 입력
실제 작업 데이터에 대해 쿼리 유효성을 검사하기 위해 **샘플 데이터** 기능을 사용하여 스트림에서 이벤트를 추출하고 테스트할 이벤트의 .json 파일을 만들 수 있습니다.

1. 이벤트 허브 입력을 선택하고 페이지 아래쪽에서 **샘플 데이터**를 클릭합니다.
2. 대화 상자가 표시되면 데이터 수집을 시작할 **시작 시간** 및 소비할 추가 데이터의 양에 대한 **기간**을 지정합니다.
3. **세부 정보** 단추를 클릭한 다음 **여기를 클릭** 링크를 클릭하여 생성된 .json 파일을 다운로드하고 저장합니다.

#### <a name="pass-through-query"></a>통과 쿼리
먼저 이벤트의 모든 필드를 프로젝션하는 간단한 통과 쿼리를 수행해 보겠습니다.

1. Stream Analytics 작업 페이지의 위쪽에서 **쿼리**를 클릭합니다.
2. 코드 편집기에서 초기 쿼리 템플릿을 다음으로 바꿉니다.

     SELECT * FROM TwitterStream

   입력 원본의 이름이 앞에서 지정한 입력의 이름과 일치하는지 확인합니다.
3. 쿼리 편집기에서 **테스트** 를 클릭합니다.
4. 샘플 .json 파일로 이동합니다.
5. **확인** 단추를 클릭하고 쿼리 정의 아래의 결과를 확인합니다.

   ![쿼리 정의 아래에 표시된 결과](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>항목별 트윗 수: 집계가 포함된 연속 창
항목 간의 멘션 수를 비교하기 위해 [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx)를 활용하여 5초마다 항목별 멘션 수를 수집합니다.

1. 코드 편집기에서 쿼리를 다음으로 변경합니다.

     SELECT System.Timestamp as Time, Topic, COUNT(*)   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY TUMBLINGWINDOW(s, 5), Topic

   이 쿼리에서는 **TIMESTAMP BY** 키워드를 사용하여 임시 계산에서 사용할 페이로드에 타임스탬프 필드를 지정합니다. 이 필드를 지정하지 않으면 각 이벤트가 이벤트 허브에 도착한 시간을 사용하여 창 작업이 수행됩니다.  자세한 내용은 [Stream Analytics 쿼리 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)에서 “도착 시간과 응용 프로그램 시간” 섹션을 참조하세요.

   또한 이 쿼리는 **System.Timestamp** 속성을 사용하여 각 창 끝부분의 타임스탬프에도 액세스합니다.
2. 쿼리 편집기에서 **다시 실행** 을 클릭하여 쿼리 결과를 확인합니다.

#### <a name="identify-trending-topics-sliding-window"></a>인기 항목 식별: 슬라이딩 윈도우
인기 항목을 식별하기 위해 지정된 기간 동안 멘션의 임계값을 초과한 항목을 찾아봅니다. 이 자습서에서는 [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx)를 사용하여 마지막 5초 이내에 20번 넘게 멘션된 항목을 확인합니다.

1. 코드 편집기의 쿼리를 다음과 같이 변경합니다. SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY SLIDINGWINDOW(s, 5), topic   HAVING COUNT(*) > 20
2. 쿼리 편집기에서 **다시 실행** 을 클릭하여 쿼리 결과를 확인합니다.

   ![슬라이딩 윈도우 쿼리 출력](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>멘션 및 데이터 수: 집계가 포함된 연속 창
테스트할 마지막 쿼리에서는 **TumblingWindow**를 사용하여 멘션 수와 각 항목에 대한 5초 간격 데이터 점수의 평균, 최소값, 최대값 및 표준 편차를 가져옵니다.

1. 코드 편집기에서 쿼리를 다음으로 변경합니다.

     SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),   Max(SentimentScore), STDEV(SentimentScore)   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY TUMBLINGWINDOW(s, 5), Topic
2. 쿼리 편집기에서 **다시 실행** 을 클릭하여 쿼리 결과를 확인합니다.
3. 이 쿼리를 대시보드에 사용합니다.  페이지 아래쪽에서 **저장** 을 클릭합니다.

## <a name="create-output-sink"></a>출력 싱크 만들기
이제 이벤트 스트림, 이벤트를 수집할 이벤트 허브 입력 및 스트림 변환을 수행할 쿼리를 정의했으므로 마지막 단계는 작업의 출력 싱크를 정의하는 것입니다.  작업 쿼리에서 집계된 트윗 이벤트를 Azure Blob Storage에 작성합니다.  또한 특정 응용 프로그램 요구 사항에 따라 Azure SQL Database, Azure Table Storage 또는 Event Hubs에 결과를 푸시할 수 있습니다.

Blob Storage의 컨테이너가 없는 경우 아래 단계에 따라 만듭니다.

1. 기존 Storage 계정을 사용하거나, **새로 만들기** > **Data Services** > **Storage** > **빨리 만들기**를 클릭하고 화면의 지침에 따라 새 Storage 계정을 만듭니다.
2. Storage 계정을 선택하고 페이지 위쪽에서 **컨테이너**를 클릭한 다음 **추가**를 클릭합니다.
3. 컨테이너 **이름**을 지정하고 해당 **액세스**를 **공용 Blob**으로 설정합니다.

## <a name="specify-job-output"></a>작업 출력 지정
1. Stream Analytics 작업의 페이지 위쪽에서 **출력**을 클릭하고 **출력 추가**를 클릭합니다. 열리는 대화 상자에서 일부 단계를 진행하면서 출력을 설정하게 됩니다.
2. **Blob Storage**를 클릭하고 오른쪽 단추를 클릭합니다.
3. 세 번째 페이지에서 다음 값을 입력하거나 선택합니다.

   * **출력 별칭**: 이 작업 출력의 이름을 입력합니다.
   * **구독**: 만든 Blob Storage가 Stream Analytics 작업과 동일한 구독에 있으면 **현재 구독에서 Storage 계정 사용**을 선택합니다. Storage가 다른 구독에 있으면 **다른 구독에서 Storage 계정 사용**을 클릭하고 ** 계정**, **Storage 계정 키** 및 **컨테이너**에 대한 정보를 직접 입력합니다.
   * **저장소 계정**: 저장소 계정의 이름을 선택합니다.
   * **컨테이너**: 컨테이너의 이름을 선택합니다.
   * **파일 이름 접두사**: Blob 출력을 작성할 때 사용할 파일 접두사를 입력합니다.
4. 오른쪽 단추를 클릭합니다.
5. 다음 값을 지정합니다.
   * **이벤트 직렬 변환기 형식**: JSON
   * **인코딩**: UTF8
6. **확인** 단추를 클릭하여 이 소스를 추가하고 Stream Analytics가 Storage 계정에 성공적으로 연결될 수 있는지 확인합니다.

## <a name="start-job"></a>작업 시작
작업 입력, 쿼리 및 출력을 모두 지정했으므로 이제 Stream Analytics 작업을 시작할 준비가 완료되었습니다.

1. 작업 **대시보드**의 페이지 아래쪽에서 **시작**을 클릭합니다.
2. 표시되는 대화 상자에서 **작업 시작 시간**을 클릭하고 대화 상자 아래쪽에 있는 **확인** 단추를 클릭합니다. 작업 상태가 **시작 중**으로 변경되었다가 곧 **실행 중**으로 변경됩니다.

## <a name="view-output-for-sentiment-analysis"></a>정서 분석에 대한 출력 보기
작업이 실시간 Twitter 스트림을 실행 및 처리하면 감정 분석에 대한 출력을 보려면 원하는 형식을 선택합니다. [Azure Storage 탐색기](https://azurestorageexplorer.codeplex.com/) 또는 [Azure 탐색기](http://www.cerebrata.com/products/azure-explorer/introduction)와 같은 도구를 사용하여 작업 출력을 실시간으로 볼 수 있습니다. 여기에서 [Power BI](https://powerbi.com/)를 사용하여 다음 스크린샷과 같은 사용자 지정된 대시보드를 포함하도록 응용 프로그램을 확장할 수 있습니다.

![소셜 미디어 분석: Power BI 대시보드에 정서 분석(의견 마이닝) 출력을 Stream Analytics합니다.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>지원 받기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-get-started.md)
* [Azure Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


