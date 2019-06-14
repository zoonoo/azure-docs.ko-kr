---
title: Azure Functions에 대한 Java 개발자 참조 | Microsoft Docs
description: Java로 함수를 개발하는 방법을 이해합니다.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure Functions, 함수, 이벤트 처리, 웹후크, 동적 계산, 서버리스 아키텍처, Java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: d88fda62c59d01a3703fdb583e0881aa8478a6cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050772"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 개발자 가이드

Azure Functions 런타임에서 지원 [Java SE 8 LTS (zulu8.31.0.2 jre8.0.181 win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/)합니다. 이 가이드는 Java 사용 하 여 Azure 함수를 작성 하는 복잡성에 대 한 정보를 포함 합니다.

Java 함수는 `public` 메서드를 주석으로 데코 레이트 `@FunctionName`합니다. 이 메서드는 Java 함수에 대 한 항목을 정의 하 고 특정 패키지에서 고유 해야 합니다. 

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다. 또한 첫 번째 함수를 사용 하 여 만드는 Functions 빠른 시작을 완료 해야 [Visual Studio Code](functions-create-first-function-vs-code.md) 하거나 [Maven](functions-create-first-java-maven.md)합니다.

## <a name="programming-model"></a>프로그래밍 모델 

[트리거 및 바인딩](functions-triggers-bindings.md)의 개념은 Azure Functions의 기본입니다. 트리거는 코드 실행을 시작합니다. 바인딩을 사용하면 사용자 지정 데이터 액세스 코드를 작성하지 않고도 데이터를 함수에 전달하고 함수에서 데이터를 반환할 수 있습니다.

## <a name="folder-structure"></a>폴더 구조

Azure Functions Java 프로젝트의 폴더 구조는 다음과 같습니다.

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

공유를 사용할 수 있습니다 [host.json](functions-host-json.md) 파일을 함수 앱을 구성 합니다. 각 함수에는 자체 코드 파일(.java)과 바인딩 구성 파일(function.json)이 있습니다.

하나의 프로젝트에 둘 이상의 함수를 넣을 수 있습니다. 함수를 별도의 jar에 넣지 않도록 하세요. `FunctionApp` 대상에서 디렉터리는 Azure에서 함수 앱으로 배포 항목을 가져옵니다.

## <a name="triggers-and-annotations"></a>트리거 및 주석

 함수는 HTTP 요청, 타이머 또는 데이터에 대 한 업데이트와 같은 트리거에 의해 호출 됩니다. 함수, 트리거 및 하나 이상의 출력을 생성 하는 데 다른 입력을 처리 해야 합니다.

[com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 패키지에 포함된 Java 주석을 사용하여 입력 및 출력을 메서드에 바인딩합니다. 자세한 내용은 참조는 [Java 참조 문서](/java/api/com.microsoft.azure.functions.annotation)합니다.

> [!IMPORTANT] 
> Azure Storage 계정을 구성 해야 합니다 하 [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) Azure Blob storage, Azure Queue storage 또는 Azure Table storage 트리거를 로컬로 실행 합니다.

예제:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

여기서는 생성 된 해당 `function.json` 여는 [azure functions-maven 플러그인](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>JDK 런타임 사용 가능성 및 지원 

Java 함수 앱의 로컬 개발을 위해 다운로드 하 고 사용 합니다 [Azure 용 Azul Zulu Enterprise](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) 에서 Java 8 Jdk [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/)합니다. Azure Functions는 클라우드에 함수 앱을 배포할 때 Azul Java 8 JDK 런타임을 사용합니다.

[Azure 지원](https://azure.microsoft.com/support/) Jdk 및 함수를 사용 하 여 문제에 대 한 앱은 사용할 수는 [정규화 된 지원 플랜](https://azure.microsoft.com/support/plans/)합니다.

## <a name="customize-jvm"></a>JVM을 사용자 지정

함수를 사용 하면 Java 함수를 실행 하는 데 Java 가상 머신 (JVM)를 사용자 지정할 수 있습니다. 합니다 [다음과 같은 JVM 옵션](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) 기본적으로 사용 됩니다.

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

라는 앱 설정에 추가 인수를 제공할 수 있습니다 `JAVA_OPTS`합니다. Azure portal 또는 Azure CLI에서 Azure에 배포 된 함수 앱에 앱 설정을 추가할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

에 [Azure portal](https://portal.azure.com)를 사용 하 여는 [응용 프로그램 설정 탭](functions-how-to-use-azure-function-app-settings.md#settings) 추가할를 `JAVA_OPTS` 설정.

### <a name="azure-cli"></a>Azure CLI

사용할 수는 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 설정 하는 명령 `JAVA_OPTS`다음 예제와 같이:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
이 예제는 헤드리스 모드를 사용 합니다. 바꿉니다 `<APP_NAME>` 함수 앱의 이름 및 `<RESOURCE_GROUP> ` 리소스 그룹을 사용 하 여 합니다.

> [!WARNING]  
> 에 [소비 계획](functions-scale.md#consumption-plan)를 추가 해야 합니다는 `WEBSITE_USE_PLACEHOLDER` 값을 사용 하 여 설정 `0`.  
이 설정은 Java 함수에 대 한 콜드 시작 시간이 늘어나지 합니다.

## <a name="third-party-libraries"></a>타사 라이브러리 

Azure Functions는 타사 라이브러리의 사용을 지원합니다. 기본적으로 프로젝트에 지정 된 모든 종속성 `pom.xml` 파일 중 자동으로 묶이는 합니다 [ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) 목표입니다. `pom.xml` 파일에 종속성으로 지정되지 않은 라이브러리의 경우 함수의 루트 디렉터리에 있는 `lib` 디렉터리에 배치합니다. 종속성을 배치 합니다 `lib` 디렉터리는 런타임에 시스템 클래스 로더에 추가 됩니다.

합니다 `com.microsoft.azure.functions:azure-functions-java-library` 종속성 classpath에 기본적으로 제공 되는 및에 포함 될 필요가 없습니다를 `lib` 디렉터리입니다. 또한 [azure functions-java 작업자](https://github.com/Azure/azure-functions-java-worker) 나열 된 종속성 추가 [여기](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) 클래스 경로에 있습니다.

## <a name="data-type-support"></a>데이터 형식 지원

Plain old Java object (Pojo)를 사용할 수 있습니다, 형식에 정의 된 `azure-functions-java-library`, 또는 입력 또는 출력 바인딩을 바인딩할 문자열 및 정수 등의 기본 데이터 형식입니다.

### <a name="pojos"></a>Pojo

POJO에 입력된 데이터를 변환 하기 위한 [azure functions-java 작업자](https://github.com/Azure/azure-functions-java-worker) 사용 하는 [gson](https://github.com/google/gson) 라이브러리. 함수에 대한 입력으로 사용되는 POJO 형식은 `public`이어야 합니다.

### <a name="binary-data"></a>이진 데이터

이진 입력 또는 출력 바인딩 `byte[]`를 설정 하 여 합니다 `dataType` 필드를 function.json의 `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

사용 하 여 null 값을 원하는 경우 `Optional<T>`합니다.

## <a name="bindings"></a>바인딩

입력 및 출력 바인딩은 코드 내에서 데이터에 연결하는 선언적 방법을 제공합니다. 함수에는 여러 개의 입력 및 출력 바인딩이 있을 수 있습니다.

### <a name="input-binding-example"></a>입력된 바인딩 예제

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

HTTP 요청을 사용 하 여이 함수를 호출 하면 됩니다. 
- HTTP 요청 페이로드에서로 전달 되는 `String` 인수에 대 한 `inputReq`합니다.
- 하나의 항목 테이블 저장소에서 검색 되 고으로 전달 됩니다 `TestInputData` 인수에 `inputData`입니다.

입력 일괄 처리를 받으려면에 바인딩할 수 있습니다 `String[]`, `POJO[]`하십시오 `List<String>`, 또는 `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

이 함수는 구성된 이벤트 허브에 새 데이터가 있을 때마다 트리거됩니다. 때문에 합니다 `cardinality` 로 설정 된 `MANY`, 이벤트 허브에서 메시지 일괄 처리를 수신 하는 함수입니다. `EventData` 이벤트에서 허브 변환 `TestEventData` 함수 실행에 대 한 합니다.

### <a name="output-binding-example"></a>출력 바인딩 예제

사용 하 여 출력 바인딩을 반환 값에 바인딩할 수 있습니다 `$return`합니다. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

여러 개의 출력 바인딩이 있으면 둘 중 하나에 대한 반환 값을 사용합니다.

여러 출력 값을 보내려면 `azure-functions-java-library` 패키지에 정의된 `OutputBinding<T>`를 사용합니다. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

이 함수는 HttpRequest에 호출할 수 있습니다. Queue storage에 여러 값을 씁니다.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 및 HttpResponseMessage

 에 정의 된 이러한 `azure-functions-java-library`합니다. 이들은 HttpTrigger 함수로 작업 하려면 도우미 형식입니다.

| 특수 형식      |       대상        | 일반적인 용도                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 트리거     | 메서드, 헤더 또는 쿼리를 가져옵니다. |
| `HttpResponseMessage` | HTTP 출력 바인딩 | 200 이외의 상태를 반환합니다.   |

## <a name="metadata"></a>Metadata

입력 데이터와 함께 [트리거 메타데이터](/azure/azure-functions/functions-triggers-bindings)를 보내는 트리거는 거의 없습니다. 주석을 사용할 수 있습니다 `@BindingName` 트리거 메타 데이터를 바인딩합니다.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
앞의 예제에는 `queryValue` 쿼리 문자열 매개 변수에 바인딩된 `name` http 요청 URL에서 `http://{example.host}/api/metadata?name=test`합니다. 여기에 바인딩하는 방법을 보여 주는 또 다른 예는 `Id` 큐 트리거 메타 데이터에서 합니다.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> 에 주석이 지정 된 이름과 메타 데이터 속성과 일치 해야 합니다.

## <a name="execution-context"></a>실행 컨텍스트

`ExecutionContext`에 정의 된는 `azure-functions-java-library`, functions 런타임은와 통신 하는 도우미 메서드가 포함 되어 있습니다.

### <a name="logger"></a>로거

사용 하 여 `getLogger`에 정의 된 `ExecutionContext`함수 코드에서 로그를 기록할 수 있습니다.

예제:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>로그 보기 및 추적

다른 응용 프로그램 로깅 뿐만 아니라 스트림 Java stdout 및 stderr 로깅, Azure CLI를 사용할 수 있습니다. 

Azure CLI를 사용 하 여 응용 프로그램 로깅을 작성 하 여 함수 앱을 구성 하는 방법을 다음과 같습니다.

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Azure CLI를 사용 하 여 함수 앱에 대 한 로깅 출력을 스트림 새 명령 프롬프트, Bash 또는 터미널 세션을 열고 다음 명령을 입력 합니다.

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[az webapp log tail](/cli/azure/webapp/log) 명령에 사용 하 여 출력을 필터링 하는 옵션을 `--provider` 옵션입니다. 

Azure CLI를 사용 하 여 단일 ZIP 파일로 로그 파일을 다운로드, 새 명령 프롬프트, Bash 또는 터미널 세션을 열고 다음 명령을 입력 합니다.

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

이 명령을 실행 하기 전에 Azure portal 또는 Azure CLI에 로그인 하는 파일 시스템을 설정 있어야 합니다.

## <a name="environment-variables"></a>환경 변수

Functions에서 [앱 설정](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. 를 사용 하 여 이러한 설정에 액세스할 수 있습니다 `System.getenv("AzureWebJobsStorage")`합니다.

예를 들어, 추가할 수 있습니다 [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings), 이름의 `testAppSetting` 값 `testAppSettingValue`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>다음 단계

Azure Functions Java 개발에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* 로컬 개발 및 사용 하 여 디버그할 [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)하십시오 [IntelliJ](functions-create-maven-intellij.md), 및 [Eclipse](functions-create-maven-eclipse.md)
* [Visual Studio Code를 사용하여 Java Azure Functions 원격 디버그](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Azure Functions의 maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* 통해 함수 생성을 간소화 합니다 `azure-functions:add` 목표에 대 한 스테이징 디렉터리를 준비 하 고 [ZIP 파일 배포](deployment-zip-push.md)합니다.
