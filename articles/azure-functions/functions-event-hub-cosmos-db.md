---
title: '자습서: Azure Cosmos DB 및 Event Hubs에서 Java 함수 사용'
description: 이 자습서에서는 Java로 작성된 함수를 사용하여 Event Hubs에서 이벤트를 통해 Azure Cosmos DB에서 업데이트하는 방법을 보여 줍니다.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: ef2b774195f522be3520850d2e6e7193e70dd4fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87372453"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>자습서: Java에서 Event Hub 트리거 및 Azure Cosmos DB 출력 바인딩을 사용하여 함수 만들기

이 자습서에서는 Azure Functions를 사용하여 지속적인 온도 및 압력 데이터 스트림을 분석하는 Java 함수를 만드는 방법을 보여 줍니다. 센서 판독값을 나타내는 이벤트 허브 이벤트에서 이 함수를 트리거합니다. 이 함수는 이벤트 데이터를 처리한 다음, 상태 항목을 Azure Cosmos DB에 추가합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure 리소스를 만들고 구성합니다.
> * 이러한 리소스와 상호 작용하는 Java 함수를 만들고 테스트합니다.
> * 함수를 Azure에 배포하고, Application Insights를 사용하여 모니터링합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다.

* [Java Developer Kit](https://aka.ms/azure-jdks), 버전 8
* [Apache Maven](https://maven.apache.org), 버전 3.0 이상
* [Azure CLI](/cli/azure/install-azure-cli)(Cloud Shell을 사용하지 않으려는 경우)
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) 버전 2.6.666 이상

> [!IMPORTANT]
> 이 자습서를 완료하려면 `JAVA_HOME` 환경 변수를 JDK의 설치 위치로 설정해야 합니다.

이 자습서의 코드를 직접 사용하려면 [java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) 샘플 리포지토리를 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Azure 리소스 만들기

이 자습서에 필요한 리소스는 다음과 같습니다.

* 다른 리소스를 포함할 리소스 그룹
* Event Hubs 네임스페이스, 이벤트 허브 및 권한 부여 규칙
* Cosmos DB 계정, 데이터베이스 및 컬렉션
* 함수 앱 및 이를 호스팅하는 스토리지 계정

다음 섹션에서는 Azure CLI를 사용하여 이러한 리소스를 만드는 방법을 보여 줍니다.

### <a name="log-in-to-azure"></a>Azure에 로그인

Cloud Shell을 사용하지 않는 경우 Azure CLI를 로컬로 사용하여 계정에 액세스해야 합니다. Bash 프롬프트의 `az login` 명령을 사용하여 브라우저 기반 로그인 환경을 시작합니다. 둘 이상의 Azure 구독에 액세스할 수 있는 경우 기본값을 구독 ID가 뒤에 나오는 `az account set --subscription`으로 설정합니다.

### <a name="set-environment-variables"></a>환경 변수 설정

다음으로, 만들 리소스의 이름과 위치에 대한 몇 가지 환경 변수를 만듭니다. 다음 명령을 사용하여 `<value>` 자리 표시자를 선택한 값으로 바꿉니다. 값은 [Azure 리소스에 대한 명명 규칙 및 제한 사항](/azure/architecture/best-practices/resource-naming)을 준수해야 합니다. `LOCATION` 변수의 경우 `az functionapp list-consumption-locations` 명령으로 생성되는 값 중 하나를 사용합니다.

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

이 자습서의 나머지 부분에서는 이러한 변수를 사용합니다. 이러한 변수는 현재 Azure CLI 또는 Cloud Shell 세션이 지속되는 동안에만 유지됩니다. 다른 로컬 터미널 창을 사용하거나 Cloud Shell 세션 시간이 초과되면 이러한 명령을 다시 실행해야 합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure는 리소스 그룹을 사용하여 계정의 모든 관련 리소스를 수집합니다. 이렇게 하면 작업이 완료될 때 그룹에 수집된 이러한 리소스를 하나의 단위로 간주하여 단일 명령으로 삭제할 수 있습니다.

다음 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>이벤트 허브 만들기

다음으로, 다음 명령을 사용하여 Azure Event Hubs 네임스페이스, 이벤트 허브 및 권한 부여 규칙을 만듭니다.

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

Event Hubs 네임스페이스에는 실제 이벤트 허브 및 해당 권한 부여 규칙이 포함되어 있습니다. 권한 부여 규칙을 사용하면 함수에서 허브로 메시지를 보내고 해당 이벤트를 수신 대기할 수 있습니다. 한 함수에서 원격 분석 데이터를 나타내는 메시지를 보냅니다. 다른 함수에서는 이벤트를 수신 대기하고, 이벤트 데이터를 분석하고, 결과를 Azure Cosmos DB에 저장합니다.

### <a name="create-an-azure-cosmos-db"></a>Azure Cosmos DB 만들기

다음으로, 다음 명령을 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 컬렉션을 만듭니다.

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

`partition-key-path` 값은 각 항목의 `temperatureStatus` 값을 기준으로 데이터를 분할합니다. 파티션 키를 사용하면 Cosmos DB에서 데이터를 독립적으로 액세스할 수 있는 고유한 하위 세트로 분할하여 성능을 향상시킬 수 있습니다.

### <a name="create-a-storage-account-and-function-app"></a>스토리지 계정 및 함수 앱 만들기

다음으로, Azure Functions에 필요한 Azure Storage 계정을 만든 다음, 함수 앱을 만듭니다. 다음 명령을 사용합니다.

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

`az functionapp create` 명령은 함수 앱을 만들 때 동일한 이름의 Application Insights 리소스도 만듭니다. 함수 앱은 Application Insights에 연결하는 `APPINSIGHTS_INSTRUMENTATIONKEY`라는 설정을 통해 자동으로 구성됩니다. 이 자습서의 뒷부분에서 설명한 대로 함수가 Azure에 배포되면 앱 원격 분석을 볼 수 있습니다.

## <a name="configure-your-function-app"></a>함수 앱 구성

함수 앱이 제대로 작동하려면 다른 리소스에 액세스해야 합니다. 다음 섹션에서는 로컬 머신에서 실행할 수 있도록 함수 앱을 구성하는 방법을 보여 줍니다.

### <a name="retrieve-resource-connection-strings"></a>리소스 연결 문자열 검색

다음 명령을 사용하여 스토리지, 이벤트 허브 및 Cosmos DB 연결 문자열을 검색하고 환경 변수에 저장합니다.

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

이러한 변수는 Azure CLI 명령에서 검색된 값으로 설정됩니다. 각 명령은 JMESPath 쿼리를 사용하여 반환된 JSON 페이로드에서 연결 문자열을 추출합니다. 연결 문자열도 `echo`를 사용하여 표시되므로 성공적으로 검색되었는지 확인할 수 있습니다.

### <a name="update-your-function-app-settings"></a>함수 앱 설정 업데이트

다음으로, 다음 명령을 사용하여 연결 문자열 값을 Azure Functions 계정의 앱 설정으로 전송합니다.

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

이제 Azure 리소스가 만들어지고, 모두 제대로 작동하도록 구성되었습니다.

## <a name="create-and-test-your-functions"></a>함수 만들기 및 테스트

다음으로, 프로젝트를 로컬 머신에 만들고, Java 코드를 추가하고, 테스트합니다. Azure Functions Plugin for Maven 및 Azure Functions Core Tools에서 작동하는 명령을 사용합니다. 함수는 로컬로 실행되지만, 만든 클라우드 기반 리소스를 사용합니다. 로컬로 작동하는 함수를 가져오면 Maven을 사용하여 클라우드에 배포하고 누적되는 데이터와 분석을 볼 수 있습니다.

Cloud Shell을 사용하여 리소스를 만든 경우 Azure에 로컬로 연결되지 않습니다. 이 경우 `az login` 명령을 사용하여 브라우저 기반 로그인 프로세스를 시작합니다. 그런 다음, 필요한 경우 기본 구독을 구독 ID가 뒤에 나오는 `az account set --subscription`으로 설정합니다. 마지막으로, 다음 명령을 실행하여 로컬 머신에서 일부 환경 변수를 다시 만듭니다. `<value>` 자리 표시자를 이전에 사용한 것과 동일한 값으로 바꿉니다.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>로컬 함수 프로젝트 만들기

다음 Maven 명령을 사용하여 함수 프로젝트를 만들고, 필요한 종속성을 추가합니다.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

이 명령은 `telemetry-functions` 폴더 내에 다음과 같은 여러 파일을 생성합니다.

* Maven에서 사용할 `pom.xml` 파일
* 로컬 테스트에 대한 앱 설정을 저장할 `local.settings.json` 파일
* 데이터 분석 함수의 Cosmos DB 출력 바인딩에 필요한 Azure Functions 확장 번들을 사용하도록 설정하는 `host.json` 파일
* 기본 함수 구현을 포함한 `Function.java` 파일
* 이 자습서에 필요하지 않은 몇 가지 테스트 파일

컴파일 오류를 방지하려면 테스트 파일을 삭제해야 합니다. 다음 명령을 실행하여 새 프로젝트 폴더로 이동하고, test 폴더를 삭제합니다.

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>로컬에서 사용할 함수 앱 설정 검색

로컬 테스트의 경우 함수 프로젝트에는 이 자습서의 앞부분에서 Azure의 함수 앱에 추가한 연결 문자열이 필요합니다. 다음 Azure Functions Core Tools 명령을 사용합니다. 이 명령은 클라우드에 저장된 모든 함수 앱 설정을 검색하여 `local.settings.json` 파일에 추가합니다.

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Java 코드 추가

다음으로, `Function.java` 파일을 열고 내용을 다음 코드로 바꿉니다.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

여기서 볼 수 있듯이 이 파일에는 `generateSensorData` 및 `processSensorData`의 두 가지 함수가 포함되어 있습니다. `generateSensorData` 함수는 온도 및 압력 판독값을 이벤트 허브에 보내는 센서를 시뮬레이션합니다. 타이머 트리거는 10초마다 이 함수를 실행하고, 이벤트 허브 출력 바인딩은 반환 값을 이벤트 허브에 보냅니다.

이벤트 허브에서 메시지를 받으면 이벤트가 생성됩니다. `processSensorData` 함수는 이벤트를 받으면 실행됩니다. 그런 다음, 이벤트 데이터를 처리하고, Azure Cosmos DB 출력 바인딩을 사용하여 결과를 Azure Cosmos DB에 보냅니다.

이러한 함수에서 사용하는 데이터는 구현해야 할 `TelemetryItem`이라는 클래스를 사용하여 저장됩니다. `Function.java`와 동일한 위치에 `TelemetryItem.java`라는 새 파일을 만들고, 다음 코드를 추가합니다.

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>로컬 실행

이제 함수를 로컬로 빌드하고 실행할 수 있으며, Azure Cosmos DB에 데이터가 표시되는 것을 볼 수 있습니다.

다음 Maven 명령을 사용하여 함수를 빌드하고 실행합니다.

```bash
mvn clean package
mvn azure-functions:run
```

일부 빌드 및 시작 메시지 후에는 함수가 실행될 때마다 다음 예제와 비슷한 출력이 표시됩니다.

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

그런 다음, [Azure Portal](https://portal.azure.com), Azure Cosmos DB 계정으로 차례로 이동할 수 있습니다. **데이터 탐색기**를 선택하고, **TelemetryInfo**를 펼친 다음, **항목**을 선택하여 데이터가 도착하면 이를 확인합니다.

![Cosmos DB Data Explorer](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Azure에 배포 및 앱 원격 분석 보기

마지막으로, 앱을 Azure에 배포하고, 로컬에서와 동일한 방식으로 계속 작동하는지 확인할 수 있습니다.

다음 명령을 사용하여 프로젝트를 Azure에 배포합니다.

```bash
mvn azure-functions:deploy
```

이제 함수가 Azure에서 실행되고, 데이터를 Azure Cosmos DB에 계속 누적합니다. 다음 스크린샷과 같이 Azure Portal에서 배포된 함수 앱을 보고, 연결된 Application Insights 리소스를 통해 앱 원격 분석을 볼 수 있습니다.

**라이브 메트릭 스트림:**

![Application Insights 라이브 메트릭 스트림](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**성능:**

![Application Insights 성능 블레이드](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Azure 리소스가 완료되면 다음 명령을 사용하여 해당 리소스를 삭제할 수 있습니다.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Event Hub 이벤트를 처리하고 Cosmos DB를 업데이트하는 Azure 함수를 만드는 방법을 알아보았습니다. 자세한 내용은 [Azure Functions Java 개발자 가이드](./functions-reference-java.md)를 참조하세요. 사용되는 주석에 대한 자세한 내용은 [com.microsoft.azure.functions.annotation](/java/api/com.microsoft.azure.functions.annotation) 참조를 참조하세요.

이 자습서에서는 환경 변수와 애플리케이션 설정을 사용하여 연결 문자열과 같은 비밀을 저장했습니다. 이러한 비밀을 Azure Key Vault에 저장하는 방법에 대한 자세한 내용은 [App Service 및 Azure Functions에 Key Vault 참조 사용](../app-service/app-service-key-vault-references.md)을 참조하세요.

다음으로, 자동화된 배포에 Azure Pipelines CI/CD를 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Functions에 Java 빌드 및 배포](/azure/devops/pipelines/ecosystems/java-function)
