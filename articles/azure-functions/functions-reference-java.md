---
title: Azure 함수에 대한 Java 개발자 참조
description: Java로 함수를 개발하는 방법을 이해합니다.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4b1f39ff4fd48a3ed99b34391e9cc6efdad86a5d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672997"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 개발자 가이드

Azure 함수 런타임은 [Java SE 8 LTS(zulu8.31.0.2-jre8.0.181-win_x64)를](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/)지원합니다. 이 가이드에는 Java를 통해 Azure 함수를 작성하는 복잡한 기능에 대한 정보가 포함되어 있습니다.

다른 언어에서 발생하는 것처럼 함수 앱에는 하나 이상의 함수가 있을 수 있습니다. Java 함수는 `public` 어노팅으로 `@FunctionName`장식된 방법입니다. 이 메서드는 Java 함수에 대한 항목을 정의하며 특정 패키지에서 고유해야 합니다. Java로 작성된 하나의 함수 앱에는 에 추가된 여러 `@FunctionName`공용 메서드가 있는 여러 클래스가 있을 수 있습니다.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다. 또한 [Visual Studio 코드](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) 또는 [Maven을](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java)사용하여 함수 퀵스타트를 완료하여 첫 번째 함수를 만들어야 합니다.

## <a name="programming-model"></a>프로그래밍 모델 

[트리거 및 바인딩](functions-triggers-bindings.md)의 개념은 Azure Functions의 기본입니다. 트리거는 코드 실행을 시작합니다. 바인딩을 사용하면 사용자 지정 데이터 액세스 코드를 작성하지 않고도 데이터를 함수에 전달하고 함수에서 데이터를 반환할 수 있습니다.

## <a name="create-java-functions"></a>Java 함수 만들기

Java 함수를 쉽게 만들 수 있도록 미리 정의된 Java 템플릿을 사용하여 특정 함수 트리거를 사용하여 프로젝트를 만드는 데 도움이 되는 Maven 기반 도구 및 아키타입이 있습니다.    

### <a name="maven-based-tooling"></a>메이븐 기반 툴링

다음 개발자 환경에는 Java 함수 프로젝트를 만들 수 있는 Azure Functions 도구가 있습니다. 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [(예: Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

위의 문서 링크는 선택한 IDE를 사용하여 첫 번째 함수를 만드는 방법을 보여 줍니다. 

### <a name="project-scaffolding"></a>프로젝트 스캐폴딩

터미널에서 명령줄 개발을 선호하는 경우 Java 기반 함수 프로젝트를 스캐폴드하는 `Apache Maven` 가장 간단한 방법은 아키타입을 사용하는 것입니다. Azure 함수에 대한 Java Maven 아키타입은 다음 _그룹Id_:_artifactId_: [com.microsoft.azure:azure 함수-아키타입 에 게시됩니다.](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/) 

다음 명령은 이 아키타입을 사용하여 새 Java 함수 프로젝트를 생성합니다.

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

이 아키타입 사용을 시작하려면 [Java 빠른 시작을](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java)참조하십시오. 

## <a name="create-kotlin-functions-preview"></a>코틀린 함수 만들기(미리 보기)

코틀린 함수를 생성하는 메이븐 아키타입도 있습니다. 현재 미리 보기 상태인 이 아키타입은 다음 _groupId_:_artifactId_: [com.microsoft.azure:azure 함수-kotlin-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/). 

다음 명령은 이 아키타입을 사용하여 새 Java 함수 프로젝트를 생성합니다.

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

이 아키타입사용을 시작하려면 [Kotlin 퀵스타트를](functions-create-first-kotlin-maven.md)참조하십시오.

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

_* 코틀린 프로젝트는 여전히 메이븐이기 때문에 매우 유사합니다._

공유 [host.json](functions-host-json.md) 파일을 사용하여 함수 앱을 구성할 수 있습니다. 각 함수에는 자체 코드 파일(.java)과 바인딩 구성 파일(function.json)이 있습니다.

하나의 프로젝트에 둘 이상의 함수를 넣을 수 있습니다. 함수를 별도의 jar에 넣지 않도록 하세요. `FunctionApp` 대상 디렉터리에서 Azure의 함수 앱에 배포됩니다.

## <a name="triggers-and-annotations"></a>트리거 및 주석

 함수는 HTTP 요청, 타이머 또는 데이터 업데이트와 같은 트리거에 의해 호출됩니다. 함수는 하나 이상의 출력을 생성하기 위해 해당 트리거 및 기타 입력을 처리해야 합니다.

[com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 패키지에 포함된 Java 주석을 사용하여 입력 및 출력을 메서드에 바인딩합니다. 자세한 내용은 Java [참조 문서를](/java/api/com.microsoft.azure.functions.annotation)참조하십시오.

> [!IMPORTANT] 
> Azure Blob 저장소, Azure 큐 저장소 또는 Azure 테이블 저장소 트리거를 로컬로 실행하려면 [local.settings.json에서](/azure/azure-functions/functions-run-local#local-settings-file) Azure 저장소 계정을 구성해야 합니다.

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

다음은 `function.json` [azure-함수-maven-플러그인에](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)의해 해당 생성 된:

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

Java 함수 앱의 로컬 개발을 위해 [Azul Systems의](https://www.azul.com/downloads/azure-only/zulu/)Azure Java 8 JDK에 [대한 Azul Zulu 엔터프라이즈를](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) 다운로드하여 사용합니다. Azure Functions는 클라우드에 함수 앱을 배포할 때 Azul Java 8 JDK 런타임을 사용합니다.

JDK 및 기능 앱 문제에 대한 [Azure 지원은](https://azure.microsoft.com/support/) [정규지원 플랜에서](https://azure.microsoft.com/support/plans/)사용할 수 있습니다.

## <a name="customize-jvm"></a>JVM 사용자 지정

함수를 사용하면 Java 함수를 실행하는 데 사용되는 JVM(Java 가상 머신)을 사용자 지정할 수 있습니다. [다음 JVM 옵션은](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) 기본적으로 사용됩니다.

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

`JAVA_OPTS`에서라는 앱 설정에서 추가 인수를 제공할 수 있습니다. Azure 포털 또는 Azure CLI에서 Azure에 배포된 함수 앱에 앱 설정을 추가할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure [포털에서](https://portal.azure.com)응용 [프로그램 설정 탭을](functions-how-to-use-azure-function-app-settings.md#settings) 사용하여 설정을 추가합니다. `JAVA_OPTS`

### <a name="azure-cli"></a>Azure CLI

[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 명령을 `JAVA_OPTS`사용하여 다음 예제와 같이 설정할 수 있습니다.

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
이 예제는 헤드리스 모드를 활성화합니다. 함수 `<APP_NAME>` 앱의 `<RESOURCE_GROUP>` 이름으로 바꾸고 리소스 그룹으로 바꿉니다.

> [!WARNING]  
> 소비 [계획에서](functions-scale.md#consumption-plan)값으로 `WEBSITE_USE_PLACEHOLDER` 설정을 추가해야 `0`합니다.  
이 설정은 Java 함수의 콜드 시작 시간을 증가시게 합니다.

## <a name="third-party-libraries"></a>타사 라이브러리 

Azure Functions는 타사 라이브러리의 사용을 지원합니다. 기본적으로 프로젝트 `pom.xml` 파일에 지정된 모든 종속성은 [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) 목표 중에 자동으로 번들로 제공됩니다. `pom.xml` 파일에 종속성으로 지정되지 않은 라이브러리의 경우 함수의 루트 디렉터리에 있는 `lib` 디렉터리에 배치합니다. `lib` 디렉터리에 배치된 종속성은 런타임시 시스템 클래스 로더에 추가됩니다.

종속성은 `com.microsoft.azure.functions:azure-functions-java-library` 기본적으로 클래스 경로에 제공되며 `lib` 디렉터리에 포함할 필요가 없습니다. 또한 [azure 함수 자바-워커](https://github.com/Azure/azure-functions-java-worker) 클래스 경로에 [여기에](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) 나열된 종속성을 추가합니다.

## <a name="data-type-support"></a>데이터 형식 지원

일반 이전 Java 개체(POJOs), `azure-functions-java-library`에 정의된 형식 또는 문자열 및 정수와 같은 기본 데이터 형식을 사용하여 입력 또는 출력 바인딩에 바인딩할 수 있습니다.

### <a name="pojos"></a>Pojo

입력 데이터를 POJO로 변환하기 위해 [azure 함수-java-worker는](https://github.com/Azure/azure-functions-java-worker) [gson](https://github.com/google/gson) 라이브러리를 사용합니다. 함수에 대한 입력으로 사용되는 POJO 형식은 `public`이어야 합니다.

### <a name="binary-data"></a>이진 데이터

함수에서 `byte[]` `dataType` 필드를 설정하여 이진 입력 또는 출력을 에 `binary`바인딩합니다.

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

null 값을 예상하는 `Optional<T>`경우 을 사용합니다.

## <a name="bindings"></a>바인딩

입력 및 출력 바인딩은 코드 내에서 데이터에 연결하는 선언적 방법을 제공합니다. 함수에는 여러 개의 입력 및 출력 바인딩이 있을 수 있습니다.

### <a name="input-binding-example"></a>입력 바인딩 예제

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

HTTP 요청으로 이 함수를 호출합니다. 
- HTTP 요청 페이로드는 `String` 인수에 `inputReq`대한 로 전달됩니다.
- 하나의 항목은 테이블 저장소에서 검색되고 `TestInputData` 인수로 `inputData`전달됩니다.

입력 일괄 처리를 `String[]`받으려면 에 `POJO[]` `List<String>` `List<POJO>`바인딩할 수 있습니다.

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

이 함수는 구성된 이벤트 허브에 새 데이터가 있을 때마다 트리거됩니다. 이 `cardinality` 함수가 `MANY`로 설정되므로 함수는 이벤트 허브에서 메시지 일괄 처리를 받습니다. `EventData`에서 이벤트 허브는 `TestEventData` 함수 실행을 위해 변환됩니다.

### <a name="output-binding-example"></a>출력 바인딩 예제

을 사용하여 `$return`반환 값에 출력 바인딩을 바인딩할 수 있습니다. 

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

HttpRequest에서 이 함수를 호출합니다. 큐 저장소에 여러 값을 씁니다.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 및 HttpResponseMessage

 에 정의되어 `azure-functions-java-library`있습니다. HttpTrigger 함수에서 작동하는 도우미 유형입니다.

| 특수 형식      |       대상        | 일반적인 사용법                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 트리거     | 메서드, 헤더 또는 쿼리 가져옵니다. |
| `HttpResponseMessage` | HTTP 출력 바인딩 | 200 이외의 상태를 반환합니다.   |

## <a name="metadata"></a>메타데이터

입력 데이터와 함께 [트리거 메타데이터](/azure/azure-functions/functions-triggers-bindings)를 보내는 트리거는 거의 없습니다. 추가를 `@BindingName` 사용하여 메타데이터를 트리거하기 위해 바인딩할 수 있습니다.


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
앞의 예제에서 `queryValue` 는 HTTP 요청 URL의 `name` 쿼리 문자열 매개 `http://{example.host}/api/metadata?name=test`변수에 바인딩됩니다. 다음은 큐 트리거 메타데이터에서 바인딩하는 `Id` 방법을 보여 주는 또 다른 예입니다.

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
> 추가 되지 에 제공 된 이름은 메타 데이터 속성과 일치 해야 합니다.

## <a name="execution-context"></a>실행 컨텍스트

`ExecutionContext`에 정의된 `azure-functions-java-library`에는 함수 런타임과 통신하는 도우미 메서드가 포함되어 있습니다.

### <a name="logger"></a>로거

에 `getLogger`정의된 `ExecutionContext`을 사용하여 함수 코드에서 로그를 작성합니다.

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

Azure CLI를 사용하여 Java stdout 및 stderr 로깅뿐만 아니라 다른 응용 프로그램 로깅을 스트리밍할 수 있습니다. 

Azure CLI를 사용하여 응용 프로그램 로깅을 작성하도록 함수 앱을 구성하는 방법은 다음과 같습니다.

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Azure CLI를 사용하여 함수 앱에 대한 로깅 출력을 스트리밍하려면 새 명령 프롬프트인 Bash 또는 터미널 세션을 열고 다음 명령을 입력합니다.

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[az webapp 로그 테일](/cli/azure/webapp/log) 명령에는 옵션을 `--provider` 사용하여 출력을 필터링하는 옵션이 있습니다. 

Azure CLI를 사용하여 로그 파일을 단일 ZIP 파일로 다운로드하려면 새 명령 프롬프트 인 Bash 또는 터미널 세션을 열고 다음 명령을 입력합니다.

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

이 명령을 실행하기 전에 Azure 포털 또는 Azure CLI에서 파일 시스템 로깅을 사용하도록 설정해야 합니다.

## <a name="environment-variables"></a>환경 변수

Functions에서 [앱 설정](functions-app-settings.md)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. `System.getenv("AzureWebJobsStorage")`을 사용하여 이러한 설정에 액세스할 수 있습니다.

다음 예제에서는 [다음과](functions-how-to-use-azure-function-app-settings.md#settings)같은 키를 `myAppSetting`가진 응용 프로그램 설정을 가져옵니다.

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>다음 단계

Azure Functions Java 개발에 대한 자세한 내용은 다음 리소스를 참조하십시오.

* [Azure 기능에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure 함수 트리거 및 바인딩](functions-triggers-bindings.md)
* [비주얼 스튜디오 코드,](https://code.visualstudio.com/docs/java/java-azurefunctions) [IntelliJ](functions-create-maven-intellij.md)및 [이클립스를](functions-create-maven-eclipse.md) 통해 로컬 개발 및 디버깅
* [Visual Studio Code를 사용하여 Java Azure Functions 원격 디버그](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Azure 기능에 대 한 메이븐 플러그인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* 목표를 통해 함수 `azure-functions:add` 생성을 간소화하고 ZIP [파일 배포를](deployment-zip-push.md)위한 스테이징 디렉터리를 준비합니다.
