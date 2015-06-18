<properties
   pageTitle="실시간으로 Twitter 데이터 및 추세 분석 | Microsoft Azure"
   description="스트림 분석을 사용하여 Twitter의 데이터 및 추세를 실시간으로 분석하는 방법에 대해 알아봅니다. 이 자습서에는 이벤트 생성부터 라이브 대시보드의 데이터에 이르는 단계가 포함되어 있습니다."
   services="stream-analytics"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="stream-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/28/2015"
   ms.author="jeffstok"/>


# 소셜 미디어 분석: 실시간 Twitter 데이터 분석

이 자습서에서는 Twitter 이벤트를 이벤트 허브로 가져오고 스트림 분석 쿼리를 작성하여 데이터를 분석한 후 결과를 저장하거나 대시보드를 통해 정보를 실시간으로 제공하여 솔루션을 빌드하는 방법에 대해 알아봅니다.

## 시나리오

뉴스 미디어 웹 사이트는 독자와 직접적으로 관련이 있는 사이트 콘텐츠를 부각시켜 경쟁 우위를 확보하는 데 관심이 있습니다. 이러한 웹 사이트에서는 Twitter 데이터를 실시간으로 분석하여 독자와 관련된 항목에 대한 소셜 미디어 정보를 사용합니다. 특히 인기 항목을 파악하기 위해 주요 항목의 트윗 양 및 데이터에 대한 실시간 분석을 필요로 합니다.

## 필수 조건
1.	이 자습서에는 Twitter 계정이 필요합니다.  
2.	이 연습에서는 GitHub에 있는 이벤트 생성기를 활용합니다. [여기](https://github.com/streamanalytics/samples/tree/master/TwitterClient)에서 다운로드하여 아래 단계에 따라 솔루션을 설정합니다.

## 이벤트 허브 입력 및 소비자 그룹 만들기

샘플 응용 프로그램은 이벤트를 생성하여 이벤트 허브 인스턴스(간단히 이벤트 허브라고 함)에 푸시합니다. 서비스 버스 이벤트 허브는 스트림 분석을 위해 이벤트를 수집하는 기본 방법입니다. 이벤트 허브 설명서는 [서비스 버스 설명서](/documentation/services/service-bus/)를 참조하세요.

아래 단계에 따라 이벤트 허브를 만듭니다.

1.	Azure 포털에서 **새로 만들기** > **앱 서비스** > **서비스 버스** > **이벤트 허브** > **빠른 생성**을 클릭하고 이름, 지역 및 새 네임스페이스 또는 기존 네임스페이스를 제공하여 새 이벤트 허브를 만듭니다.  
2.	각 스트림 분석 작업이 단일 이벤트 허브 소비자 그룹에서 읽는 것이 가장 좋습니다. 소비자 그룹을 만드는 프로세스는 다음과 같습니다. 소비자 그룹을 만들려면 새로 만든 이벤트 허브로 이동하여 **소비자 그룹** 탭을 클릭한 다음 페이지 아래쪽에서 **만들기**를 클릭하고 소비자 그룹의 이름을 입력합니다.
3.	이벤트 허브에 대한 액세스 권한을 부여하려면 공유 액세스 정책을 만들어야 합니다. 이벤트 허브의 **구성** 탭을 클릭합니다.
4.	**공유 액세스 정책**에서 **관리** 권한을 사용하여 새 정책을 만듭니다.



  ![관리 권한을 사용하여 정책을 만들 수 있는 공유 액세스 정책](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.	페이지 아래쪽에서 **저장**을 클릭합니다.
6.	**대시보드**로 이동하여 페이지 아래쪽에서 **연결 정보**를 클릭하고 연결 정보를 복사 및 저장합니다. 검색 아이콘 아래에 표시된 복사 아이콘을 사용하세요.

## 이벤트 생성기 응용 프로그램 구성 및 시작

Microsoft에서는 매개 변수화된 항목 집합에 대한 트윗 이벤트를 수집하기 위해 [Twitter의 REST API](https://dev.twitter.com/rest/public)를 통해 Twitter 데이터를 가져오는 클라이언트 응용 프로그램을 제공합니다. 타사 오픈 소스 도구인 [Sentiment140](http://help.sentiment140.com/)은 데이터 값을 각 트윗에 할당(0: 부정, 2: 중립, 4: 긍정)하는 데 사용되며, 이 값이 할당되면 트윗 이벤트가 이벤트 허브로 푸시됩니다.

다음 단계에 따라 응용 프로그램을 설치합니다.

1.	[TwitterClient 솔루션을 다운로드](https://github.com/streamanalytics/samples/tree/master/TwitterClient)합니다.
2.	App.config를 열고 oauth_consumer_key, oauth_consumer_secret, oauth_token, oauth_token_secret을 원하는 값의 Twitter 토큰으로 바꿉니다.  

	[OAuth 액세스 토큰을 생성하는 단계](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)

	토큰을 생성하는 빈 응용 프로그램을 만들어야 합니다.
3.	App.config의 EventHubConnectionString 및 EventHubName 값을 이벤트 허브 연결 문자열 및 이름으로 바꿉니다.
4.	*선택 사항:* 검색할 키워드를 조정합니다. 기본적으로 이 응용 프로그램은 "Azure,Skype,XBox,Microsoft,Seattle"을 찾습니다. 필요한 경우 App.config에서 twitter_keywords 값을 조정할 수 있습니다.
5.	솔루션을 빌드합니다.
6.	응용 프로그램을 시작합니다. CreatedAt, Topic 및 SentimentScore 값이 이벤트 허브로 전송 중인 트윗 이벤트가 표시됩니다.

	![이벤트 허브로 전송되는 SentimentScore 값](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## 스트림 분석 작업 만들기

이제 트윗 이벤트 스트림을 만들었으므로 이러한 이벤트를 실시간으로 분석하도록 스트림 분석 작업을 설정할 수 있습니다.

### 스트림 분석 작업 프로비전

1.	[Azure 포털](https://manage.windowsazure.com/)에서 **새로 만들기** > **데이터 서비스** > **스트림 분석** > **빠른 생성**을 클릭합니다.
2.	다음 값을 지정하고 **스트림 분석 작업 만들기**를 클릭합니다.

	* **작업 이름**: 작업 이름을 입력합니다.
	* **지역**: 작업을 실행할 지역을 선택합니다. 더 나은 성능을 보장하고 비용 부담 없이 지역 간에 데이터를 전송하려면 동일한 지역에 작업 및 이벤트 허브를 배치하는 것이 좋습니다.
	* **저장소 계정**: 이 지역 내에서 실행되는 모든 스트림 분석 작업에 대한 모니터링 데이터를 저장하는 데 사용할 저장소 계정을 선택합니다. 기존 저장소 계정을 선택하거나 새 계정을 만들 수 있습니다.

3.	왼쪽 창에서 **스트림 분석**을 클릭하여 스트림 분석 작업을 표시합니다. ![스트림 분석 서비스 아이콘](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

4.	새 작업이 **생성됨** 상태로 표시됩니다. 페이지 아래쪽에 있는 **시작** 단추는 사용할 수 없게 설정됩니다. 작업을 시작하려면 먼저 작업 입력, 출력 및 쿼리를 구성해야 합니다.


### 작업 입력 지정
1.	스트림 분석 작업의 페이지 위쪽에서 **입력**을 클릭하고 **입력 추가**를 클릭합니다. 열리는 대화 상자에서 다양한 단계를 진행하면서 입력을 설정하게 됩니다.
2.	**데이터 스트림**을 선택한 후 오른쪽 단추를 클릭합니다.
3.	**이벤트 허브**를 선택한 후 오른쪽 단추를 클릭합니다.
4.	세 번째 페이지에서 다음 값을 입력하거나 선택합니다.

	* **입력 별칭** - 이 작업 입력의 이름(예: TwitterStream)을 입력합니다. 이 이름은 나중에 쿼리에서 사용하게 됩니다. **이벤트 허브**: 만든 이벤트 허브가 스트림 분석 작업과 동일한 구독에 포함된 경우 이벤트 허브가 있는 네임스페이스를 선택합니다.

		If your event hub is in a different subscription, select **Use Event Hub from Another Subscription**, and then manually enter information for **SERVICE BUS NAMESPACE**, **EVENT HUB NAME**, **EVENT HUB POLICY NAME**, **EVENT HUB POLICY KEY**, and **EVENT HUB PARTITION COUNT**.

	* **이벤트 허브 이름**: 이벤트 허브의 이름을 선택합니다.
	* **이벤트 허브 정책 이름**: 이 자습서의 앞부분에서 만든 이벤트 허브 정책을 선택합니다.
	* **이벤트 허브 소비자 그룹**: 이 자습서의 앞부분에서 만든 소비자 그룹을 입력합니다.
5.	오른쪽 단추를 클릭합니다.
6.	다음 값을 지정합니다.

	* **이벤트 직렬 변환기 형식**: JSON
	* **인코딩**: UTF8

7.	확인 단추를 클릭하여 이 소스를 추가하고 스트림 분석이 이벤트 허브에 성공적으로 연결될 수 있는지 확인합니다.

### 작업 쿼리 지정

스트림 분석은 변환을 설명하는 간단하고 선언적인 쿼리 모델을 지원합니다. 이 언어에 대한 자세한 내용은 [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)를 참조하세요. 이 자습서에서는 Twitter 데이터에 대해 여러 쿼리를 작성하고 테스트하도록 도와줍니다.

#### 샘플 데이터 입력

실제 작업 데이터에 대해 쿼리 유효성을 검사하기 위해 샘플 데이터 기능을 사용하여 스트림에서 이벤트를 추출하고 테스트할 이벤트의 .JSON 파일을 만들 수 있습니다.

1.	이벤트 허브 입력을 선택하고 페이지 아래쪽에서 **샘플 데이터**를 클릭합니다.
2.	대화 상자가 표시되면 데이터 수집을 시작할 **시작 시간** 및 소비할 추가 데이터의 양에 대한 **기간**을 지정합니다.
3.	**세부 정보** 단추를 클릭한 다음 **여기를 클릭** 링크를 클릭하여 생성된 .JSON 파일을 다운로드하고 저장합니다.

#### 통과 쿼리
먼저 이벤트의 모든 필드를 프로젝션하는 간단한 통과 쿼리를 수행해 보겠습니다.

1.	스트림 분석 작업 페이지의 위쪽에서 **쿼리**를 클릭합니다.
2.	코드 편집기에서 초기 쿼리 템플릿을 다음으로 바꿉니다.

		SELECT * FROM TwitterStream

	입력 소스의 이름이 앞에서 지정한 입력의 이름과 일치하는지 확인합니다.

3.	쿼리 편집기에서 **테스트**를 클릭합니다.
4.	샘플 .JSON 파일을 찾습니다.
5.	확인 단추를 클릭하고 쿼리 정의 아래에 표시되는 결과를 확인합니다.

	![쿼리 정의 아래에 표시된 결과](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### 항목별 트윗 수: 집계가 포함된 연속 창

항목 간의 멘션 수를 비교하기 위해 [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx)를 활용하여 5초마다 항목별 멘션 수를 수집합니다.

1.	코드 편집기에서 쿼리를 다음으로 변경합니다.

			SELECT System.Timestamp as Time, Topic, COUNT(*)
			FROM TwitterStream TIMESTAMP BY CreatedAt
			GROUP BY TUMBLINGWINDOW(s, 5), Topic

	이 쿼리에서는 **TIMESTAMP BY** 키워드를 사용하여 임시 계산에서 사용할 페이로드에 타임스탬프 필드를 지정합니다. 이 필드를 지정하지 않으면 각 이벤트가 이벤트 허브에 도착한 시간을 사용하여 창 작업이 수행됩니다. 자세한 내용은 [스트림 분석 쿼리 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)에서 “도착 시간과 응용 프로그램 시간”을 참조하세요.

	또한 이 쿼리는 **System.Timestamp**를 사용하여 각 창의 종료 타임스탬프에 액세스합니다.

2.	쿼리 편집기에서 **다시 실행**을 클릭하여 쿼리 결과를 확인합니다.

#### 인기 항목 식별: 슬라이딩 윈도우

인기 항목을 식별하기 위해 지정된 기간 동안 멘션의 임계값을 초과한 항목을 찾아봅니다. 이 자습서에서는 [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx)를 사용하여 5초 이내에 20번 넘게 멘션된 항목을 확인합니다.

1.	코드 편집기에서 쿼리를 다음으로 변경합니다.

			SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
			FROM TwitterStream TIMESTAMP BY CreatedAt
			GROUP BY SLIDINGWINDOW(s, 5), topic
			HAVING COUNT(*) > 20

2.	쿼리 편집기에서 **다시 실행**을 클릭하여 쿼리 결과를 확인합니다.

	![슬라이딩 윈도우 쿼리 출력](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### 멘션 및 데이터 수: 집계가 포함된 연속 창

테스트할 마지막 쿼리에서는 TumblingWindow를 사용하여 멘션 수와 각 항목에 대한 5초 간격 데이터 점수의 평균, 최소값, 최대값 및 표준 편차를 가져옵니다.

1.	코드 편집기에서 쿼리를 다음으로 변경합니다.

			SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
	    	Max(SentimentScore), STDEV(SentimentScore)
			FROM TwitterStream TIMESTAMP BY CreatedAt
			GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.	쿼리 편집기에서 **다시 실행**을 클릭하여 쿼리 결과를 확인합니다.
3.	이 쿼리를 대시보드에 사용합니다. 페이지 아래쪽에서 **저장**을 클릭합니다.


## 출력 싱크 만들기

이제 이벤트 스트림, 이벤트를 수집할 이벤트 허브 입력 및 스트림 변환을 수행할 쿼리를 정의했으므로 마지막 단계는 작업의 출력 싱크를 정의하는 것입니다. 작업 쿼리에서 집계된 트윗 이벤트를 Azure Blob에 작성합니다. 또한 응용 프로그램 요구 사항에 따라 SQL 데이터베이스, 테이블 저장소 또는 이벤트 허브에 결과를 푸시할 수 있습니다.

Blob 저장소의 컨테이너가 없는 경우 아래 단계에 따라 만듭니다.

1.	기존 저장소 계정을 사용하거나, **새로 만들기** > **데이터 서비스** > **저장소** > **빠른 생성**을 클릭하고 화면의 지침에 따라 새 저장소 계정을 만듭니다.
2.	저장소 계정을 선택하고 페이지 위쪽에서 **컨테이너**를 클릭한 다음 **추가**를 클릭합니다.
3.	컨테이너 **이름**을 지정하고 해당 **액세스**를 공용 Blob으로 설정합니다.

## 작업 출력 지정

1.	스트림 분석 작업의 페이지 위쪽에서 **출력**을 클릭하고 **출력 추가**를 클릭합니다. 열리는 대화 상자에서 다양한 단계를 진행하면서 출력을 설정하게 됩니다.
2.	**Blob 저장소**를 선택하고 오른쪽 단추를 클릭합니다.
3.	세 번째 페이지에서 다음 값을 입력하거나 선택합니다.

	* **출력 별칭** - 이 작업 출력의 이름을 입력합니다.
	* **구독**: 만든 Blob 저장소가 스트림 분석 작업과 동일한 구독에 있으면 **현재 구독에서 저장소 계정 사용**을 선택합니다. 저장소가 다른 구독에 있으면 **다른 구독에서 저장소 계정 사용**을 선택하고 **저장소 계정**, **저장소 계정 키**, **컨테이너**에 대한 정보를 직접 입력합니다.
	* **저장소 계정**: 저장소 계정의 이름을 선택합니다.
	* **컨테이너**: 컨테이너의 이름을 선택합니다.
	* **파일 이름 접두사**: Blob 출력을 작성할 때 사용할 파일 접두사를 입력합니다.

4.	오른쪽 단추를 클릭합니다.
5.	다음 값을 지정합니다.
	* **이벤트 직렬 변환기 형식**: JSON
	* **인코딩**: UTF8
6.	확인 단추를 클릭하여 이 소스를 추가하고 스트림 분석이 저장소 계정에 성공적으로 연결될 수 있는지 확인합니다.

## 작업 시작

작업 입력, 쿼리 및 출력을 모두 지정했으므로 이제 스트림 분석 작업을 시작할 준비가 완료되었습니다.

1.	작업 **대시보드**의 페이지 아래쪽에서 **시작**을 클릭합니다.
2.	대화 상자가 표시되면 **작업 시작 시간**을 선택하고 대화 상자 아래쪽에 있는 확인 표시 단추를 클릭합니다. 작업 상태가 **시작 중**으로 변경되었다가 곧 **실행 중**으로 변경됩니다.


## 출력 보기

[Azure 저장소 탐색기](https://azurestorageexplorer.codeplex.com/) 또는 [Azure 탐색기](http://www.cerebrata.com/products/azure-explorer/introduction)와 같은 도구를 사용하여 작업 출력을 실시간으로 볼 수 있습니다. 여기에서 출력에 대해 아래의 [Power BI](https://powerbi.com/)와 같은 사용자 지정 대시보드를 포함하도록 응용 프로그램을 확장할 수 있습니다.

![Power BI 대시보드의 스트림 분석 출력](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## 지원 받기
추가적인 도움이 필요하면 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)을 참조하세요.


## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석을 사용하여 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=52-->
 