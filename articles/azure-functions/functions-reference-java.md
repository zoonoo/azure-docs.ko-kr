---
title: Azure Functions에 대한 Java 개발자 참조 | Microsoft Docs
description: Java로 함수를 개발하는 방법을 이해합니다.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure Functions, 함수, 이벤트 처리, 웹후크, 동적 계산, 서버를 사용하지 않는 아키텍처, Java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: 9e07cddb9d446ea24143d3a6dec5e310d3ed6f1c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802120"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 개발자 가이드

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>프로그래밍 모델 

Azure 함수는 입력을 처리하고 출력을 생성하는 상태 비저장 클래스 메서드여야 합니다. 인스턴스 메서드를 작성할 수는 있지만 함수가 클래스의 어떤 인스턴스 필드에도 종속되지 않아야 합니다. 모든 함수 메서드에는 `public` 액세스 한정자가 있어야 합니다.

## <a name="folder-structure"></a>폴더 구조

Java 프로젝트의 폴더 구조는 다음과 같습니다.

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

함수 앱을 구성하는 데 사용할 수 있는 공유 [host.json](functions-host-json.md) 파일이 있습니다. 각 함수에는 자체 코드 파일(.java)과 바인딩 구성 파일(function.json)이 있습니다.

하나의 프로젝트에 둘 이상의 함수를 넣을 수 있습니다. 함수를 별도의 jar에 넣지 않도록 하세요. 대상 디렉터리의 FunctionApp이 Azure의 함수 앱에 배포됩니다.

## <a name="triggers-and-annotations"></a>트리거 및 주석

 Azure 함수는 HTTP 요청, 타이머 또는 데이터 업데이트와 같은 트리거에 의해 호출됩니다. 함수는 해당 트리거 및 모든 다른 입력을 처리하고 하나 이상의 출력을 생성해야 합니다.

[com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 패키지에 포함된 Java 주석을 사용하여 입력 및 출력을 메서드에 바인딩합니다. 주석을 사용하는 샘플 코드는 각 주석에 대한 [Java 참조 문서](/java/api/com.microsoft.azure.functions.annotation)와 Azure Functions 바인딩 참조 설명서(예: [HTTP 트리거](/azure/azure-functions/functions-bindings-http-webhook)에 대한 문서)에서 사용할 수 있습니다.

트리거 입력 및 출력은 주석을 통하는 대신, 함수에 대한 [function.json](/azure/azure-functions/functions-reference#function-code)에서도 정의할 수 있습니다. 이 방법으로 주석 대신 `function.json`을 사용하는 것은 권장되지 않습니다.

> [!IMPORTANT] 
> Azure Storage Blob, 큐 또는 테이블 트리거를 로컬로 실행하려면 [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file)에서 Azure Storage 계정을 구성해야 합니다.

주석을 사용 하는 예제:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

주석 없이 작성된 동일한 함수:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

해당하는 `function.json` 사용:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
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

Java 함수 앱을 로컬에서 개발하는 데 사용할 수 있는 [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/)의 [Azul Zulu for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) JDK를 다운로드하여 사용합니다. JDK는 Windows, Linux 및 macOS용으로 제공되며, [적격 지원 플랜](https://azure.microsoft.com/support/plans/)을 보유한 경우 개발 중에 발생하는 문제에 대해 [Azure 지원](https://support.microsoft.com/en-us/help/4026305/sql-contact-microsoft-azure-support)을 받을 수 있습니다.

## <a name="third-party-libraries"></a>타사 라이브러리 

Azure Functions는 타사 라이브러리의 사용을 지원합니다. 기본적으로 프로젝트 `pom.xml` 파일에 지정된 모든 종속성은 `mvn package` 목표 중에 자동으로 번들됩니다. `pom.xml` 파일에 종속성으로 지정되지 않은 라이브러리의 경우 함수의 루트 디렉터리에 있는 `lib` 디렉터리에 배치합니다. `lib` 디렉터리에 배치된 종속성은 런타임에 시스템 클래스 로더에 추가됩니다.

`com.microsoft.azure.functions:azure-functions-java-library` 종속성은 classpath에서 기본적으로 제공되므로 `lib` 디렉터리에 포함하지 않아도 됩니다.

## <a name="data-type-support"></a>데이터 형식 지원

입력 및 출력 데이터에 대해 네이티브 형식, 사용자 지정 Java 형식 및 `azure-functions-java-library` 패키지에 정의된 Azure 특수 형식을 포함하여 Java의 모든 데이터 형식을 사용할 수 있습니다. Azure Functions 런타임에서는 받은 입력을 코드에서 요청한 형식으로 변환합니다.

### <a name="strings"></a>문자열

함수의 해당 입력 매개 변수 형식이 `String` 형식인 경우 함수 메서드에 전달된 값은 문자열로 캐스팅됩니다. 

### <a name="plain-old-java-objects-pojos"></a>이전의 일반 Java 개체(POJO)

함수의 입력 서명이 해당 Java 형식을 필요로 하는 경우 JSON 형식으로 지정된 문자열이 Java 형식으로 캐스팅됩니다. 이 변환을 통해 JSON으로 전달하고 Java 형식으로 작업할 수 있습니다.

함수에 대한 입력으로 사용되는 POJO 형식은 사용 중인 함수 메서드와 동일한 `public` 액세스 한정자여야 합니다. `public` POJO 클래스 필드를 선언할 필요가 없습니다. 예를 들어 `{ "x": 3 }` JSON 문자열은 다음 POJO 형식으로 변환될 수 있습니다.

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>이진 데이터

이진 데이터는 Azure Functions 코드에서 `byte[]`로 표시됩니다. function.json의 `dataType` 필드를 `binary`로 설정하여 이진 입력 또는 출력을 함수에 바인딩합니다.

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

그런 다음 함수 코드에서 사용합니다.

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

함수 인수로 빈 입력 값은 `null`일 수 있지만 잠재적 빈 값을 처리하기 위해 권장되는 방법은 `Optional<T>`을 사용하는 것입니다.


## <a name="function-method-overloading"></a>함수 메서드 오버로드

동일한 이름이지만 다른 형식의 함수 메서드를 오버로드할 수 있습니다. 예를 들어, 클래스에 `String echo(String s)` 및 `String echo(MyType s)`를 모두 포함할 수 있습니다. Azure Functions는 입력 형식에 따라 호출할 메서드를 결정합니다(HTTP 입력의 경우 MIME 형식 `text/plain`은 `String`으로 연결되는 반면 `application/json`은 `MyType`를 나타냄).

## <a name="inputs"></a>입력

입력은 Azure Functions에서 두 가지 범주로 나뉩니다. 즉 하나는 트리거 입력이고, 다른 하나는 추가 입력입니다. `function.json`에서는 사용법이 서로 다르지만, Java 코드에서는 동일합니다. 예를 들어 다음 코드 조각을 살펴보겠습니다.

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String in,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") MyObject obj
    ) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    public static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

이 함수가 트리거되면 HTTP 요청이 `String in`에 의해 함수로 전달됩니다. 항목은 경로 URL의 ID를 기반으로 Azure Table Storage에서 검색되고 함수 본문에서 `obj`로 사용됩니다.

## <a name="outputs"></a>outputs

출력은 반환 값 또는 출력 매개 변수 둘 다로 표현될 수 있습니다. 출력이 하나만 있는 경우 반환 값을 사용하는 것이 좋습니다. 다중 출력의 경우 출력 매개 변수를 사용해야 합니다.

반환 값은 가장 간단한 형식의 출력입니다. 즉 모든 형식의 값을 반환하고 Azure Functions 런타임에서 이 반환 값을 실제 형식(예: HTTP 응답)으로 다시 마샬링합니다.  반환 값 출력을 정의하기 위해 함수 메서드에 출력 주석을 적용할 수 있습니다(주석의 name 특성은 $return이어야 함).

여러 출력 값을 생성하려면 `azure-functions-java-library` 패키지에 정의된 `OutputBinding<T>` 형식을 사용합니다. HTTP 응답을 만들고 메시지도 큐로 푸시해야 하는 경우 다음과 같이 작성할 수 있습니다.

예를 들어, Blob 콘텐츠 복사 함수를 다음 코드로 정의할 수 있습니다. `@StorageAccount` 주석은 `@BlobTrigger` 및 `@BlobOutput` 모두에 대한 연결 속성 복제를 방지하기 위해 여기에 사용됩니다.

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

`OutputBinding<byte[]`>를 사용하여 이진 출력 값(매개 변수용)을 만들고 반환 값에는 `byte[]`를 사용합니다.

## <a name="specialized-types"></a>특수 형식

함수는 경우에 따라 입력과 출력을 자세히 제어해야 합니다. 요청 정보를 조작하고 HTTP 트리거의 반환 상태에 맞게 조정할 수 있도록 `azure-functions-java-core` 패키지의 특수 형식이 제공됩니다.

| 특수 형식      |       대상        | 일반적 용도                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 트리거     | 메서드, 헤더 또는 쿼리 가져오기 |
| `HttpResponseMessage<T>` | HTTP 출력 바인딩 | 200 이외의 반환 상태   |

> [!NOTE] 
> `@BindingName` 주석을 사용하여 HTTP 헤더 및 쿼리를 가져올 수도 있습니다. 예를 들어 `@BindingName("name") String query`는 HTTP 요청 헤더와 쿼리를 반복하고 해당 값을 메서드에 전달합니다. 예를 들어 요청 URL이 `http://example.org/api/echo?name=test`이면 `query`는 `"test"`가 됩니다.

### <a name="metadata"></a>Metadata

메타데이터는 HTTP 헤더, HTTP 쿼리 및 [트리거 메타데이터](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties)와 같은 다양한 원본에서 가져옵니다. 값을 얻으려면 메타데이터 이름과 함께 `@BindingName` 주석을 사용합니다.

예를 들어 요청된 URL이 `http://{example.host}/api/metadata?name=test`이면 다음 코드 조각의 `queryValue`는 `"test"`가 됩니다.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="execution-context"></a>실행 컨텍스트

`azure-functions-java-library` 패키지에 정의된 `ExecutionContext` 개체를 통해 Azure Functions 실행 환경과 상호 작용합니다. 코드에서 호출 정보와 함수 런타임 정보를 사용하려면 `ExecutionContext` 개체를 사용합니다.

### <a name="custom-logging"></a>사용자 지정 로깅

Azure Functions 런타임 로거에 대한 액세스는 `ExecutionContext` 개체를 통해 사용할 수 있습니다. 이 로거는 Azure 모니터에 연결되어 있으며, 함수를 실행하는 동안 발생하는 경고 및 오류에 대한 플래그를 지정할 수 있습니다.

다음 예제 코드에서는 받은 요청 본문이 비어 있을 때 경고 메시지를 기록합니다.

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

Azure CLI를 사용하여 다른 응용 프로그램 로깅뿐만 아니라 Java 표준 출력 및 오류 로깅을 스트리밍할 수 있습니다. 먼저 Azure CLI를 사용하여 응용 프로그램 로깅을 작성하는 함수 응용 프로그램을 구성합니다.

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Azure CLI를 사용하여 함수 앱에 대한 로깅 출력을 스트리밍하려면 새 명령 프롬프트, Bash 또는 터미널 세션을 열고 다음 명령을 입력합니다.

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[az webapp log tail](/cli/azure/webapp/log) 명령에는 `--provider` 옵션을 사용하여 출력을 필터링하는 옵션이 있습니다. 

Azure CLI를 사용하여 로그 파일을 단일 ZIP 파일로 다운로드하려면 새 명령 프롬프트, Bash 또는 터미널 세션을 열고 다음 명령을 입력합니다.

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

이 명령을 실행하기 전에 Azure Portal 또는 Azure CLI에 로그인하는 파일 시스템이 사용하도록 설정되어 있어야 합니다.

## <a name="environment-variables"></a>환경 변수

보안상의 이유로 소스 코드에 키 또는 토큰과 같은 비밀 정보가 들어가지 않도록 하세요. 환경 변수에서 키와 토큰을 읽는 방법으로 함수 코드에 키와 토큰을 사용하세요.

로컬로 Azure Functions를 실행할 때 환경 변수를 설정하려면 local.settings.json 파일에 이러한 변수를 추가하도록 선택할 수 있습니다. 변수가 함수 프로젝트의 루트 디렉터리에 없는 경우 새로 만들 수 있습니다. 파일은 다음과 같아야 합니다.

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

`values` 맵의 각 키/값 매핑은 런타임 시 환경 변수로 사용할 수 있고 `System.getenv("<keyname>")`(예: `System.getenv("AzureWebJobsStorage")`)를 호출하여 액세스할 수 있습니다. 추가 키/값 쌍을 추가하는 것이 좋습니다.

> [!NOTE]
> 이 방법을 사용하는 경우 파일을 커밋하지 않도록 local.settings.json 파일을 리포지토리에 추가하여 파일을 무시하도록 합니다.

이제 이러한 환경 변수에 따라 코드를 사용하면 로컬로 테스트할 때 및 Azure에 배포할 때 코드가 동등하게 작동되도록 Azure Portal에 로그인하여 함수 앱 설정에서 동일한 키/값 쌍을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Function Java 개발에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
- [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) 및 [Eclipse](functions-create-maven-eclipse.md)를 사용하여 로컬로 개발하고 디버그합니다. 
* [Visual Studio Code를 사용하여 Java Azure Functions 원격 디버그](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Azure Functions의 Maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) - `azure-functions:add` 목적을 통해 함수 생성을 간소화하고 [ZIP 파일 배포](deployment-zip-push.md)에 대한 준비 디렉터리를 준비합니다.
