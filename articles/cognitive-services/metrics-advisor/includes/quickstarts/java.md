---
title: Metrics Advisor Java 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: ff8a09e32a44f51571cca93655f91080e5df9a50
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186963"
---
[참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src) | [아티팩트(Maven)](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor) | [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자
* Azure 구독이 있으면 Azure Portal에서 <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Metrics Advisor 리소스를 생성"  target="_blank">Metrics Advisor 리소스를 생성<span class="docon docon-navigate-external x-hidden-focus"></span></a>하여 Metrics Advisor 인스턴스를 배포합니다.  
* 시계열 데이터가 포함된 사용자 고유의 SQL 데이터베이스.
  
  
> [!TIP]
> * [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)에서 Java Metrics Advisor 샘플을 찾을 수 있습니다.
> * Metrics Advisor 리소스에서 사용할 서비스 인스턴스를 배포하는 데 10~30분 정도 걸릴 수 있습니다. 성공적으로 배포되면 **리소스로 이동** 을 클릭합니다. 배포 후에는 웹 포털과 REST API 모두를 사용하여 Metrics Advisor 인스턴스 사용을 시작할 수 있습니다. 
> * 리소스의 **개요** 섹션에서 Azure Portal의 REST API에 대한 URL을 찾을 수 있습니다. 다음과 같이 표시됩니다.
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>설치

### <a name="create-a-new-gradle-project"></a>새 Gradle 프로젝트 만들기

이 빠른 시작에서는 Gradle 종속성 관리자를 사용합니다. [Maven Central Repository](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)에서 추가 클라이언트 라이브러리 정보를 찾을 수 있습니다.

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

작업 디렉터리에서 `gradle init` 명령을 실행합니다. 이 명령은 *build.gradle.kts* 를 포함하여 런타임에 애플리케이션을 만들고 구성하는 데 사용되는 Gradle용 필수 빌드 파일을 만듭니다.

```console
gradle init --type basic
```

**DSL** 을 선택하라는 메시지가 표시되면 **Kotlin** 을 선택합니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

*build.gradle.kts* 를 찾고, 원하는 IDE 또는 텍스트 편집기에서 엽니다. 그런 다음, 이 빌드 구성에서 복사합니다. 프로젝트 종속성을 포함해야 합니다.

```kotlin
dependencies {
    compile("com.azure:azure-ai-metricsadvisor:1.0.0-beta.1")
}
```

### <a name="create-a-java-file"></a>Java 파일 만들기

샘플 앱에 대한 폴더를 만듭니다. 작업 디렉터리에서 다음 명령을 실행합니다.

```console
mkdir -p src/main/java
```

새 폴더로 이동하여 *MetricsAdvisorQuickstarts.java* 라는 파일을 만듭니다. 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 `import` 문을 추가합니다.

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

애플리케이션의 `MetricsAdvisorQuickstarts` 클래스에서 리소스의 키 및 엔드포인트에 대한 변수를 만듭니다.


> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Metrics Advisor 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 구독 키와 엔드포인트를 찾을 수 있습니다. <br><br>API 키를 검색하려면 [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net)으로 이동해야 합니다. 적절한 항목을 선택합니다. 리소스에 대한 **디렉터리** , **구독** 및 **작업 영역** 을 선택하고 **시작** 을 선택합니다. 그러면 [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key)에서 API 키를 검색할 수 있습니다.   
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 Cognitive Services [보안](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) 문서를 참조하세요.

```java
private static String SUBSCRIPTION_KEY = "<replace-with-your-metrics-advisor-subscription-key-here>";
private static String API_KEY = "<replace-with-your-metrics-advisor-api-key-here>"
private static String ENDPOINT = "<replace-with-your-metrics-advisor-endpoint-here>";
```

애플리케이션의 `Main()` 메서드에서 이 빠른 시작에 사용된 메서드에 대한 호출을 추가합니다. 나중에 만들 것입니다.

```java
static void Main(string[] args){

    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>개체 모델

Metrics Advisor Java SDK의 주요 기능 중 일부를 처리하는 클래스는 다음과 같습니다.

|이름|설명|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/MetricsAdvisorClient.html) | **용도** : <br> - 인시던트 나열 <br> - 인시던트의 근본 원인 나열 <br> - 서비스에 의해 보강된 원래 시계열 데이터와 시계열 데이터를 검색합니다. <br> - 경고 나열 <br> - 모델 조정을 위한 피드백 추가 |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/administration/MetricsAdvisorAdministrationClient.html)| **다음을 수행할 수 있습니다.** <br> - 데이터 피드 관리 <br> - 변칙 검색 구성 <br> - 변칙 경고 구성 <br> - 후크 관리  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/models/DataFeed.html)| **Metrics Advisor가 데이터 원본에서 수집하는 항목. `DataFeed`에는**  행이 포함됩니다. <br> - 타임스탬프 <br> - 0개 이상의 차원 <br> - 하나 이상의 측정값  |
| [메트릭](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/models/Metric.html) | `Metric`은 특정 비즈니스 프로세스의 상태를 모니터링하고 평가하는 데 사용되는 정량 측정값입니다. 차원으로 나눈 여러 개의 시계열 값의 조합일 수 있습니다. 예를 들어 웹 상태 메트릭은 사용자 수 및 en-us 시장의 차원을 포함할 수 있습니다. |

## <a name="code-examples"></a>코드 예제

이러한 코드 조각에서는 Java용 Metrics Advisor 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [데이터 피드 추가](#add-a-data-feed)
* [수집 상태 확인](#check-the-ingestion-status)
* [변칙 검색 구성](#configure-anomaly-detection)
* [후크 만들기](#create-a-hook)
* [경고 구성 만들기](#create-an-alert-configuration)
* [경고 쿼리](#query-the-alert)

## <a name="authenticate-the-client"></a>클라이언트 인증

### <a name="create-a-metrics-advisor-client-using-metricsadvisorkeycredential"></a>MetricsAdvisorKeyCredential을 사용하여 Metrics Advisor 클라이언트 만들기

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorClient metricsAdvisorClient = new MetricsAdvisorClientBuilder()
    .endpoint(ENDPOINT)
    .credential(credential)
    .buildClient();
```

### <a name="create-a-metrics-administration-client-using-metricsadvisorkeycredential"></a>MetricsAdvisorKeyCredential을 사용하여 Metrics Administration 클라이언트 만들기

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorAdministrationClient metricsAdvisorAdministrationClient =
    new MetricsAdvisorAdministrationClientBuilder()
        .endpoint(ENDPOINT)
        .credential(credential)
        .buildClient();

``` 

## <a name="add-a-data-feed"></a>데이터 피드 추가

`sql_server_connection_string`을 사용자 고유의 SQL 서버 연결 문자열로 바꾸고 `query`를 단일 타임스탬프에서 데이터를 반환하는 쿼리로 바꿉니다. 사용자 지정 데이터를 기반으로 `metric` 및 `dimension` 값도 조정해야 합니다.

> [!IMPORTANT]
> 각 타임스탬프에서 쿼리는 각 차원 조합에 대해 최대 하나의 레코드를 반환해야 합니다. 그리고 쿼리에서 반환된 모든 레코드의 타임스탬프는 동일해야 합니다. 메트릭 관리자는 각 타임스탬프에 대해 이 쿼리를 실행하여 데이터를 수집합니다. 자세한 정보와 예는 [쿼리에 대한 FAQ 섹션](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)을 참조하세요. 

```java
final DataFeed createdSqlDataFeed = metricsAdvisorAdministrationClient.createDataFeed(
    "My data feed name",
    new SQLServerDataFeedSource("sql_server_connection_string", "query"),
    new DataFeedGranularity().setGranularityType(DataFeedGranularityType.DAILY),
    new DataFeedSchema(Arrays.asList(
        new Metric().setName("cost"),
        new Metric().setName("revenue")))
        .setDimensions(Arrays.asList(
            new Dimension().setName("category"),
            new Dimension().setName("city"))),
    new DataFeedIngestionSettings(OffsetDateTime.parse("2020-01-01T00:00:00Z")),
    new DataFeedOptions()
        .setDescription("My data feed description")
        .setRollupSettings(
            new DataFeedRollupSettings()
                .setAutoRollup(DataFeedAutoRollUpMethod.SUM, Arrays.asList("cost"), "__CUSTOM_SUM__"))
        .setMissingDataPointFillSettings(
            new DataFeedMissingDataPointFillSettings()
                .setFillType(DataSourceMissingDataPointFillType.SMART_FILLING))
        .setAccessMode(DataFeedAccessMode.PUBLIC));

System.out.printf("Data feed Id : %s%n", createdSqlDataFeed.getId());
System.out.printf("Data feed name : %s%n", createdSqlDataFeed.getName());
System.out.printf("Is the query user is one of data feed administrator : %s%n", createdSqlDataFeed.isAdmin());
System.out.printf("Data feed created time : %s%n", createdSqlDataFeed.getCreatedTime());
System.out.printf("Data feed granularity type : %s%n",
    createdSqlDataFeed.getGranularity().getGranularityType());
System.out.printf("Data feed granularity value : %d%n",
    createdSqlDataFeed.getGranularity().getCustomGranularityValue());
System.out.println("Data feed related metric Ids:");
createdSqlDataFeed.getMetricIds().forEach(metricId -> System.out.println(metricId));
System.out.printf("Data feed source type: %s%n", createdSqlDataFeed.getSourceType());
createdSqlDataFeed.getSchema().getMetrics().forEach(metric -> {
    System.out.printf("metric name: %s metric id:%s", metric.getName(), metric.getId());
});

System.out.printf("Data feed sql server query: %s%n",
    ((SQLServerDataFeedSource) createdSqlDataFeed.getSource()).getQuery());
```

## <a name="check-the-ingestion-status"></a>수집 상태 확인

이 예제에서는 이전에 제공된 데이터 피드 원본의 수집 상태를 확인합니다.

```java
String dataFeedId = "<use-the-data-feed-id-from-createdSqlDataFeed.getId()"; 

metricsAdvisorAdministrationClient.listDataFeedIngestionStatus(
    dataFeedId,
    new ListDataFeedIngestionOptions(
        OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.parse("2020-09-09T00:00:00Z"))
).forEach(dataFeedIngestionStatus -> {
    System.out.printf("Message : %s%n", dataFeedIngestionStatus.getMessage());
    System.out.printf("Timestamp value : %s%n", dataFeedIngestionStatus.getTimestamp());
    System.out.printf("Status : %s%n", dataFeedIngestionStatus.getStatus());
});
```
## <a name="configure-anomaly-detection"></a>변칙 검색 구성 

이 예제에서는 사용자가 해당 데이터에 대한 변칙 검색 구성을 구성할 수 있는 방법을 보여줍니다.

```java
String metricId = "<metric-id-from-adding-data-feed>";

ChangeThresholdCondition changeThresholdCondition = new ChangeThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.BOTH)
    .setChangePercentage(20)
    .setShiftPoint(10)
    .setWithinRange(true)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

HardThresholdCondition hardThresholdCondition = new HardThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.DOWN)
    .setLowerBound(5.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(1));

SmartDetectionCondition smartDetectionCondition = new SmartDetectionCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.UP)
    .setSensitivity(10.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

final AnomalyDetectionConfiguration anomalyDetectionConfiguration =
    metricsAdvisorAdministrationClient.createMetricAnomalyDetectionConfiguration(
        metricId,
        new AnomalyDetectionConfiguration("My Anomaly detection configuration")
            .setDescription("anomaly detection config description")
            .setWholeSeriesDetectionCondition(
                new MetricWholeSeriesDetectionCondition()
                    .setChangeThresholdCondition(changeThresholdCondition)
                    .setHardThresholdCondition(hardThresholdCondition)
                    .setSmartDetectionCondition(smartDetectionCondition)
                    .setCrossConditionOperator(DetectionConditionsOperator.OR))
    );
```

## <a name="create-a-hook"></a>후크 만들기

이 예제에서는 변칙 인시던트 경고를 수신하는 이메일 후크를 만듭니다.

```java
Hook emailHook = new EmailHook("email hook")
    .setDescription("my email hook")
    .addEmailToAlert("alertme@alertme.com")
    .setExternalLink("https://adwiki.azurewebsites.net/articles/howto/alerts/create-hooks.html");

final Hook hook = metricsAdvisorAdministrationClient.createHook(emailHook);
EmailHook createdEmailHook = (EmailHook) hook;
System.out.printf("Hook Id: %s%n", createdEmailHook.getId());
System.out.printf("Hook Name: %s%n", createdEmailHook.getName());
System.out.printf("Hook Description: %s%n", createdEmailHook.getDescription());
System.out.printf("Hook External Link: %s%n", createdEmailHook.getExternalLink());
System.out.printf("Hook Emails: %s%n", String.join(",", createdEmailHook.getEmailsToAlert()));
```

##  <a name="create-an-alert-configuration"></a>경고 구성 만들기

이 예제에서는 사용자가 데이터에서 검색된 변칙에 대한 경고 구성을 구성할 수 있는 방법을 보여줍니다.

```java
String detectionConfigurationId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String detectionConfigurationId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

final AnomalyAlertConfiguration anomalyAlertConfiguration
    = metricsAdvisorAdministrationClient.createAnomalyAlertConfiguration(
        new AnomalyAlertConfiguration("My Alert config name")
            .setDescription("alert config description")
            .setMetricAlertConfigurations(
                Arrays.asList(
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId1,
                        MetricAnomalyAlertScope.forWholeSeries()),
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId2,
                        MetricAnomalyAlertScope.forWholeSeries())
                        .setAlertConditions(new MetricAnomalyAlertConditions()
                            .setSeverityCondition(new SeverityCondition()
                                .setMaxAlertSeverity(Severity.HIGH)))
                ))
            .setCrossMetricsOperator(MetricAnomalyAlertConfigurationsOperator.AND)
            .setIdOfHooksToAlert(Arrays.asList(hookId1, hookId2)));
```

## <a name="query-the-alert"></a>경고 쿼리

이 예제에서는 사용자가 경고 검색 구성에 대해 트리거된 경고를 쿼리하고 해당 경고에 대한 변칙을 가져올 수 있는 방법을 보여줍니다.

```java
String alertConfigurationId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
metricsAdvisorClient.listAlerts(
    alertConfigurationId,
    new ListAlertOptions(OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.now(),
        TimeMode.ANOMALY_TIME))
    .forEach(alert -> {
        System.out.printf("Alert Id: %s%n", alert.getId());
        System.out.printf("Alert created on: %s%n", alert.getCreatedTime());

        // List anomalies for returned alerts
        metricsAdvisorClient.listAnomaliesForAlert(
            alertConfigurationId,
            alert.getId())
            .forEach(anomaly -> {
                System.out.printf("Anomaly was created on: %s%n", anomaly.getCreatedTime());
                System.out.printf("Anomaly severity: %s%n", anomaly.getSeverity().toString());
                System.out.printf("Anomaly status: %s%n", anomaly.getStatus());
                System.out.printf("Anomaly related series key: %s%n", anomaly.getSeriesKey().asMap());
            });
    });
```

다음을 사용하여 앱을 빌드할 수 있습니다.

```console
gradle build
```
### <a name="run-the-application"></a>애플리케이션 실행

`run` 목표를 사용하여 애플리케이션을 실행합니다.

```console
gradle run
```