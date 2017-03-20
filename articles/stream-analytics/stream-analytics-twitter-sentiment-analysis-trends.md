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
ms.date: 03/03/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 214bd6ca8abb3adc9447536215f28f478959be07
ms.lasthandoff: 03/07/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure Stream Analytics에서 실시간 Twitter 감정 분석

실시간 Twitter 이벤트를 Azure Event Hubs로 가져와서 소셜 미디어 분석을 위한 감정 분석 솔루션을 구축하는 방법을 알아봅니다. 데이터를 분석하기 위해 Azure Stream Analytics 쿼리를 작성합니다. 다음 자세히 확인하기 위해 결과를 저장하거나 대시보드 및 [Power BI](https://powerbi.com/)를 사용하여 실시간으로 통찰력을 제공할 수 있습니다.

소셜 미디어 분석 도구는 조직이 소셜 미디어에서 대량의 게시물을 사용하여 추세 항목, 의미 있는 주제 및 자세를 이해하도록 돕습니다. *의견 마이닝*이라는 감정 분석은 소셜 미디어 분석 도구를 사용하여 제품, 아이디어 등에 대한 자세를 결정합니다. 실시간 Twitter 추세 분석은 아주 좋은 예입니다. 특정 키워드를 수신하고 피드에 대한 감정 분석을 개발할 수 있는 해시 태그 구독 모델을 사용하기 때문입니다.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>시나리오: 소셜 미디어 실시간 감정 분석

뉴스 미디어 웹 사이트를 보유하는 기업은 독자와 직접적으로 관련이 있는 사이트 콘텐츠를 부각시켜 경쟁 우위를 확보하는 데 관심이 있습니다. 이러한 기업은 Twitter 데이터에 대해 실시간으로 감정을 분석하여 독자와 관련된 항목에 대한 소셜 미디어 정보를 사용합니다. 특히 Twitter에서 실시간으로 추세를 분석할 토픽을 식별하기 위해 기업에서는 주요 토픽에 대한 트윗 볼륨 및 감정에 대한 실시간 분석이 필요합니다. 따라서 기본적으로 이 소셜 미디어 피드를 기반으로 하는 감정 분석 엔진이 필요합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독
* Twitter 계정 및 [OAuth 액세스 토큰](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* GitHub의 [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) 파일입니다.
* 선택 사항: [Github](https://aka.ms/azure-stream-analytics-twitterclient)twitter 클라이언트에 대한 소스 코드

## <a name="create-an-event-hub-input"></a>이벤트 허브 입력 만들기

샘플 응용 프로그램은 이벤트를 생성하여 Event Hubs 인스턴스(간단히 이벤트 허브라고 함)에 푸시합니다. Service Bus 이벤트 허브는 Stream Analytics를 위해 이벤트를 수집하는 기본 방법입니다. 자세한 내용은 [Azure Service Bus 설명서](/azure/service-bus/)에서 이벤트 허브 설명서를 검토하십시오.

### <a name="use-the-following-steps-to-create-an-event-hub"></a>다음 단계에 따라 이벤트 허브를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 **새로 만들기**를 클릭하고 *이벤트 허브*를 입력한 다음 검색 결과에서 **이벤트 허브**를 선택합니다. 그런 다음 **Create**를 클릭합니다.
2. 이벤트 허브에 대한 이름을 제공하고 리소스 그룹을 만듭니다. `socialtwitter-eh` 및 `socialtwitter-rg`를 각각 지정했습니다. 대시보드 옆에 계정을 고정하려면 이 상자를 선택한 다음 **만들기** 단추를 클릭합니다.
3. 배포가 완료되면 이벤트 허브를 클릭한 다음 **엔터티**에서 **이벤트 허브**를 클릭합니다.
4. **+ 이벤트 허브** 버튼을 클릭하여 이벤트 허브를 만듭니다. 이름(`socialtwitter-eh`)을 다시 입력하고 **만들기**를 클릭합니다.
5. 이벤트 허브에 대한 액세스 권한을 부여하려면 공유 액세스 정책을 만들어야 합니다. 이벤트 허브를 클릭한 다음 **설정**에서 **공유 액세스 정책**을 클릭합니다.
6. **공유 액세스 정책**에서 **+ 추가**를 클릭하여 **관리** 권한을 사용하여 새 정책을 만듭니다. 정책에 이름을 지정하고 **관리**를 선택한 다음 **만들기**를 클릭합니다.
7. 생성되면 새 정책을 클릭한 다음 **연결 문자열 - 기본 키** 엔터티에 대한 복사 단추를 클릭합니다. 연습에서 더 자세히 살펴 보겠습니다. 그런 다음 대시보드로 돌아갑니다.

![공유 액세스 정책 끝점](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>Twitter 클라이언트 응용 프로그램 구성 및 시작

Microsoft에서는 매개 변수화된 항목 집합에 대한 트윗 이벤트를 수집하기 위해 [Twitter의 스트리밍 API](https://dev.twitter.com/streaming/overview)를 통해 Twitter 데이터에 연결하는 클라이언트 응용 프로그램을 제공합니다. [Sentiment140](http://help.sentiment140.com/) 공개 소스 도구는 각 트윗에 감정 값을 할당하는 데 사용됩니다.

* 0 = 부정적
* 2 = 중립
* 4 = 긍정적

그런 다음 트윗 이벤트가 이벤트 허브로 푸시됩니다.  

### <a name="follow-these-steps-to-set-up-the-application"></a>다음 단계에 따라 응용 프로그램을 설치합니다.

1. [TwitterWPFClient.zip을 다운로드](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip)하고 압축을 풉니다.
2. `TwitterWPFClient.exe` 응용 프로그램을 실행하고 Twitter API 키 및 암호, Twitter 액세스 토큰 및 암호는 물론, Azure 이벤트 허브 정보에 대한 데이터를 입력합니다. 마지막으로 정서를 나타내는 키워드를 정의합니다. 둘 이상의 단어를 지정(쉼표를 사용하여 여러 값 정의)하고 ANY를 원하는 경우에는 컨트롤을 "Match ANY"로 뒤집어야 합니다.

   [OAuth 액세스 토큰을 생성하는 단계](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   토큰을 생성하는 빈 응용 프로그램을 만들어야 합니다.  

3. TwitterWpfClient.exe.config의 EventHubConnectionString 및 EventHubName 값을 이벤트 허브의 연결 문자열 및 이름으로 바꿉니다. 앞에서 복사한 연결 문자열을 사용하면 이벤트 허브의 연결 문자열과 이름이 모두 제공되어 이를 구분하고 올바른 필드에 각각 추가할 수 있습니다. 예를 들어 다음 연결 문자열을 고려해 보겠습니다.  
   
   `Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub`
   
   TwitterWpfClient.exe.config 파일에는 다음 예제와 같은 설정이 포함되어야 합니다.
   
   ```
     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
     add key="EventHubName" value="yourhub"
   ```
   
   EventHubName 값에는 텍스트 "EntityPath="가 나타나지 **않습니다**.
   
   또한 Twitter 및 Azure 연결 정보에 대한 값을 클라이언트에 직접 입력할 수도 있습니다. "EntityPath="를 사용하지 않는 경우에도 동일한 논리가 적용됩니다.
   
   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

4. *선택 사항:* 검색할 키워드를 조정합니다.  기본적으로 이 응용 프로그램은 일부 게임 키워드를 찾습니다.  필요한 경우 TwitterWpfClient.exe.config에서 **twitter_keywords** 값을 조정할 수 있습니다.
5. TwitterWpfClient.exe를 실행하고 녹색 시작 단추를 클릭하여 소셜 정서를 수집합니다. **CreatedAt**, **Topic** 및 **SentimentScore** 값이 이벤트 허브로 전송 중인 트윗 이벤트가 표시됩니다.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

이제 Twitter에서 실시간으로 트윗 이벤트 스트림을 만들었으므로 이러한 이벤트를 실시간으로 분석하도록 Stream Analytics job 작업을 설정할 수 있습니다.

### <a name="provision-a-stream-analytics-job"></a>Stream Analytics 작업 프로비전

[Azure Portal](https://portal.azure.com/)에서 **새로 만들기**를 클릭한 다음 **STREAM ANALYTICS**를 입력하고 Stream Analytics 타일 결과를 클릭합니다. 다음 값을 지정하고 **만들기**를 클릭합니다.

   * **작업 이름**: 작업 이름을 입력합니다.
   * **리소스 그룹**: "기존 항목 사용" 옵션에서 이 연습 앞부분에서 만든 리소스 그룹을 선택합니다.
   * **Storage 계정**: 이 하위 지역 내에서 실행되는 모든 Stream Analytics 작업에 대한 모니터링 데이터를 저장하는 데 사용하려는 Azure Storage 계정을 선택합니다. 기존 저장소 계정을 선택하거나 새 계정을 만들 수 있습니다.   

![새 작업](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

작업이 만들어지면 Azure Portal에서 작업이 열립니다.

## <a name="specify-the-job-input"></a>작업 입력 지정

Stream Analytics 작업에서 작업 토폴로지의 작업 창 중간의 **입력**을 클릭한 다음 **추가**를 클릭합니다. 그러면 포털에서 아래 나열된 일부 정보를 요청합니다. 대부분의 기본 값만으로도 충분하지만 사용자의 정보에 대 한 아래에 정의되어 있습니다.
  
   * **입력 별칭**: 이 작업 입력에 대한 친숙한 이름(예: `TwitterStream`)을 입력합니다. 이 이름은 나중에 쿼리에서 사용하게 됩니다.
   * **이벤트 허브 이름**: 이벤트 허브의 이름을 선택합니다.
   * **이벤트 허브 정책 이름**: 이 자습서의 앞부분에서 만든 이벤트 허브 정책을 선택합니다.

**만들기** 단추를 클릭합니다.

## <a name="specify-job-query"></a>작업 쿼리 지정

Stream Analytics는 변환을 설명하는 간단하고 선언적인 쿼리 모델을 지원합니다. 이 언어에 대한 자세한 내용은 [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)를 참조하세요.  이 자습서에서는 Twitter 데이터에 대해 여러 쿼리를 작성하고 테스트하도록 도와줍니다.

항목 간의 멘션 수를 비교하기 위해 [Tumbling Window](https://msdn.microsoft.com/library/azure/dn835055.aspx)를 활용하여&5;초마다 항목별 멘션 수를 수집합니다.

코드 편집기의 쿼리를 아래 코드로 변경한 다음 **저장**을 클릭합니다.

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

이 쿼리에서는 **TIMESTAMP BY** 키워드를 사용하여 임시 계산에서 사용할 페이로드에 타임스탬프 필드를 지정합니다. 이 필드를 지정하지 않으면 각 이벤트가 이벤트 허브에 도착한 시간을 사용하여 창 작업이 수행됩니다.  자세한 내용은 [Stream Analytics 쿼리 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)에서 “도착 시간과 응용 프로그램 시간” 섹션을 참조하세요.

또한 이 쿼리는 **System.Timestamp** 속성을 사용하여 각 창 끝부분의 타임스탬프에도 액세스합니다.

![쿼리 상자](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-output-sink"></a>출력 싱크 만들기

이제 이벤트 스트림, 이벤트를 수집할 이벤트 허브 입력 및 스트림 변환을 수행할 쿼리를 정의했으므로 마지막 단계는 작업의 출력 싱크를 정의하는 것입니다.  작업 쿼리에서 집계된 트윗 이벤트를 Azure Blob Storage에 작성합니다.  또한 특정 응용 프로그램 요구 사항에 따라 Azure SQL Database, Azure Table Storage 또는 Event Hubs에 결과를 푸시할 수 있습니다.

## <a name="specify-job-output"></a>작업 출력 지정

Stream Analytics 작업에서 **작업 토폴로지**의 **출력**을 클릭한 다음 **추가**를 클릭합니다. 그런 다음 해당 창에서 다음 값을 입력하거나 선택합니다.
   
   * **출력 별칭**: 이 작업 출력의 이름을 입력합니다. 이 데모에서는 `Output`을 사용합니다.
   * **싱크**: Blob 저장소를 선택합니다.
   * **저장소 계정**: "새 저장소 계정 만들기"를 선택합니다.
    * **저장소 계정**: 새 저장소 계정에 이름을 지정합니다(이 예에서는 `socialtwitter`).
    * **컨테이너**: 새 컨테이너에 이름을 지정합니다(이 예에서는 `socialtwitter`).

기본 값으로는 항목의 나머지 부분을 그대로 둡니다. 마지막으로, **만들기**를 클릭합니다.

## <a name="start-the-job"></a>작업 시작

작업 입력, 쿼리 및 출력을 모두 지정했으므로 이제 Stream Analytics 작업을 시작할 준비가 완료되었습니다.

작업 개요 창에서 페이지 맨 위에 있는 **시작**을 클릭합니다.

표시되는 대화 상자에서 **작업 시작 시간**을 클릭하고 대화 상자 아래쪽에 있는 **확인** 단추를 클릭합니다. 작업 상태가 **시작 중**으로 변경되었다가 곧 **실행 중**으로 변경됩니다.

![작업 실행 중](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>정서 분석에 대한 출력 보기

작업이 실시간 Twitter 스트림을 실행 및 처리하면 감정 분석에 대한 출력을 보려면 원하는 형식을 선택합니다. [Azure Storage Explorer](https://http://storageexplorer.com/) 또는 [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction)와 같은 도구를 사용하여 작업 출력을 실시간으로 볼 수 있습니다. 여기에서 [Power BI](https://powerbi.com/)를 사용하여 다음 스크린샷과 같은 사용자 지정된 대시보드를 포함하도록 응용 프로그램을 확장할 수 있습니다.

![powerbi](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="another-query-of-interest--in-this-scenario"></a>이 시나리오의 또 다른 관심 쿼리

이 시나리오에 대해 만든 또 다른 관심 쿼리는 [슬라이딩 윈도우](https://msdn.microsoft.com/library/azure/dn835051.aspx)에 기반합니다. 인기 항목을 식별하기 위해 지정된 기간 동안 멘션의 임계값을 초과한 항목을 찾아봅니다. 이 자습서에서는 마지막 5초 이내에 20번 넘게 멘션된 항목을 확인합니다.

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="get-support"></a>지원 받기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-get-started.md)
* [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)


