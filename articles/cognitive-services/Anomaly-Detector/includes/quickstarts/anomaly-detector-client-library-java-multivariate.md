---
title: Anomaly Detector 다변량 Java 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: 6c3285dddc559fbdf3da8d877ad7390403acd149
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114339404"
---
Java용 Anomaly Detector 다변량 클라이언트 라이브러리를 시작합니다. 서비스에서 제공하는 알고리즘을 사용하여 패키지 시작을 설치하려면 다음 단계를 따르세요. 새로운 다변량 변칙 검색 API를 통해 개발자는 기계 학습 기술 또는 레이블이 지정된 데이터 없이도 메트릭 그룹에서 변칙을 검색하는 고급 AI를 쉽게 통합할 수 있습니다. 서로 다른 신호 간의 종속성 및 상호 상관 관계는 자동으로 주요 요소로 계산됩니다. 이를 통해 복잡한 시스템의 오류로부터 사전에 보호할 수 있습니다.

Java용 Anomaly Detector 다변량 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 시계열 그룹에서 시스템 수준 이상 징후를 감지합니다.
* 개별 시계열이 많은 것을 알 수 없는 경우 문제를 감지하기 위해 모든 신호를 확인해야 합니다.
* 시스템 상태의 다양한 양상을 측정하는 수천 개 유형의 센서를 사용하여 고가의 물리적 자산을 사전 예방적으로 유지 관리.

[라이브러리 참조 설명서](/java/api/com.azure.ai.anomalydetector) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector) | [패키지(Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/) | [샘플 코드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector 리소스 만들기"  target="_blank">Anomaly Detector 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Anomaly Detector API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

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
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Java 파일 만들기

샘플 앱에 대한 폴더를 만듭니다. 작업 디렉터리에서 다음 명령을 실행합니다.

```console
mkdir -p src/main/java
```

새 폴더로 이동하여 *MetricsAdvisorQuickstarts.java* 라는 파일을 만듭니다. 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 `import` 문을 추가합니다.

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 예제 데이터 파일에 대한 다른 변수를 만듭니다.

> [!NOTE]
> 항상 두 키 중 하나를 사용할 수 있습니다. 이는 보안 키 회전을 허용하기 위한 것입니다. 이 빠른 시작의 목적을 위해 첫 번째 키를 사용합니다. 

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

Anomaly Detector 다변량 API를 사용하려면 먼저 고유한 모델을 학습해야 합니다. 학습 데이터는 다음 요구 사항을 충족하는 여러 시계열 세트입니다.

각 시계열은 헤더 행으로 "timestamp" 및 "value"(모두 소문자)라는 2개(및 2개만)의 열이 포함된 CSV 파일이어야 합니다. "timestamp" 값은 ISO 8601을 준수해야 합니다. "value"는 소수 자릿수가 있는 정수 또는 소수일 수 있습니다. 예를 들면 다음과 같습니다.

|timestamp | 값|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

각 CSV 파일의 이름을 모델 학습에 사용되는 다른 변수 뒤에 지정해야 합니다. 예를 들면 "temperature.csv" 및 "humidity.csv"입니다. 모든 CSV 파일은 하위 폴더 없이 하나의 zip 파일로 압축되어야 합니다. zip 파일에는 원하는 이름을 사용할 수 있습니다. zip 파일을 Azure Blob Storage에 업로드해야 합니다. zip 파일에 대한 Blob SAS(공유 액세스 서명) URL을 생성한 후에는 학습에 사용할 수 있습니다. Azure Blob Storage에서 SAS URL을 생성하는 방법은 이 문서를 참조하세요.

## <a name="code-examples"></a>코드 예제

이 코드 조각은 Node.js용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [모델 학습](#train-a-model)
* [변칙 검색](#detect-anomalies)
* [모델 내보내기](#export-model)
* [모델 삭제](#delete-model)

## <a name="authenticate-the-client"></a>클라이언트 인증

엔드포인트와 자격 증명을 사용하여 `anomalyDetectorClient` 개체를 인스턴스화합니다.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>모델 학습

### <a name="construct-a-model-result-and-train-model"></a>모델 결과 생성 및 모델 학습

먼저 모델 요청을 생성해야 합니다. 시작 및 종료 시간이 데이터 원본과 일치하는지 확인합니다.

Anomaly Detector 다변량 API를 사용하려면 검색을 사용하기 전에 고유한 모델을 학습해야 합니다. 학습에 사용되는 데이터는 시계열 일괄 처리입니다. 각 시계열은 열이 두 개( **"timestamp"** 및 **"value"** )만 있는 CSV 파일에 있어야 합니다. 열 이름은 정확하게 동일해야 합니다. 각 CSV 파일 이름은 시계열의 각 변수 다음에 지정되어야 합니다. 모든 시계열은 zip 파일 하나로 압축되어 [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs)에 업로드되어야 합니다. zip 파일 이름에 대한 요구 사항은 없습니다. 또는 변수의 이름을 .zip 파일 이름과 다르게 지정하려는 경우 추가 meta.js 파일을 zip 파일에 포함시킬 수 있습니다. [Blob SAS(공유 액세스 서명) URL](../../../../storage/common/storage-sas-overview.md)을 생성하면 학습을 위해 zip 파일에 대한 URL을 사용할 수 있습니다.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy().setAlignMode(alignMode).setFillNAMethod(fillNAMethod).setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
;
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
;
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo().setSlidingWindow(window).setAlignPolicy(alignPolicy).setSource(source).setStartTime(startTime).setEndTime(endTime).setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] model_ids = header.split("/");
UUID model_id = UUID.fromString(model_ids[model_ids.length - 1]);
System.out.println(model_id);

Integer skip = 0;
Integer top = 5;
PagedIterable<ModelSnapshot> response = anomalyDetectorClient.listMultivariateModel(skip, top);
Iterator<PagedResponse<ModelSnapshot>> ite = response.iterableByPage().iterator();

while (true) {
    Response<Model> response_model = anomalyDetectorClient.getMultivariateModelWithResponse(model_id, Context.NONE);
    UUID model = response_model.getValue().getModelId();
    System.out.println(response_model.getStatusCode());
    System.out.println(response_model.getValue().getModelInfo().getStatus());
    System.out.println(model);
    if (response_model.getValue().getModelInfo().getStatus() == ModelStatus.READY) {
        break;
    }
}
```

## <a name="detect-anomalies"></a>변칙 검색

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(model_id, detectionRequest, Context.NONE);
String result = detectAnomalyResponse.getDeserializedHeaders().getLocation();

String[] result_list = result.split("/");
UUID result_id = UUID.fromString(result_list[result_list.length - 1]);

while (true) {
    DetectionResult response_result = anomalyDetectorClient.getDetectionResult(result_id);
    if (response_result.getSummary().getStatus() == DetectionStatus.READY) {
        break;
    }
    else if(response_result.getSummary().getStatus() == DetectionStatus.FAILED){

    }
}
```

## <a name="export-model"></a>모델 내보내기

> [!NOTE]
> 내보내기 명령은 컨테이너화된 환경에서 Anomaly Detector 다변량 모델을 실행할 수 있도록 하는 데 사용됩니다. 이는 현재 다변량에 대해 지원되지 않지만 나중에 지원이 추가될 예정입니다.

학습된 모델을 내보내려면 `exportModelWithResponse`를 사용합니다.

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>모델 삭제

현재 리소스에 사용할 수 있는 기존 모델을 삭제하려면 `deleteMultivariateModelWithResponse` 함수를 사용합니다.

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>애플리케이션 실행

다음을 사용하여 앱을 빌드할 수 있습니다.

```console
gradle build
```
### <a name="run-the-application"></a>애플리케이션 실행

실행하기 전에 [전체 샘플 코드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)에서 코드를 확인하는 것이 도움이 될 수 있습니다.

`run` 목표를 사용하여 애플리케이션을 실행합니다.

```console
gradle run
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

* [Anomaly Detector API란?](../../overview-multivariate.md)
* [Anomaly Detector API를 사용하는 경우의 모범 사례입니다.](../../concepts/best-practices-multivariate.md)