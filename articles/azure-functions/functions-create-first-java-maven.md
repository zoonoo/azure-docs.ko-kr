---
title: Java 및 Maven을 사용하여 Azure에서 첫 번째 함수 만들기 | Microsoft Docs
description: Java 및 Maven을 사용하여 Azure에 간단한 HTTP 트리거 함수를 만들어 게시합니다.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure 함수, 함수, 이벤트 처리, 계산, 서버리스 아키텍처
ms.service: azure-functions
ms.devlang: java
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: ab705b6131bd43a7ab70bab16cef81d33f07c055
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827407"
---
# <a name="create-your-first-function-with-java-and-maven"></a>Java 및 Maven을 사용하여 첫 번째 함수 만들기

이 문서에서는 Maven 명령줄 도구를 사용하여 Java 함수를 만들어 Azure Functions에 게시하는 방법을 안내합니다. 완료되면 함수 코드가 Azure의 [사용 플랜](functions-scale.md#consumption-plan)에서 실행되고 HTTP 요청을 사용하여 트리거될 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

Java를 사용하여 함수를 개발하려면 다음을 설치해야 합니다.

- [Java Developer Kit](https://aka.ms/azure-jdks), 버전 8
- [Apache Maven](https://maven.apache.org), 버전 3.0 이상
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2) 버전 2.6.666 이상

> [!IMPORTANT]
> 이 퀵 스타트를 완료하려면 JAVA_HOME 환경 변수를 JDK 설치 위치로 설정해야 합니다.

## <a name="generate-a-new-functions-project"></a>새 Functions 프로젝트 생성

빈 폴더에서 다음 명령을 실행하여 [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)으로부터 Functions 프로젝트를 생성합니다.

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> 명령 실행에 문제가 발생하는 경우 `maven-archetype-plugin` 버전이 사용되는지 살펴보시기 바랍니다. `.pom` 파일이 없는 빈 디렉터리에서 명령을 실행하고 있으므로 이전 버전에서 Maven을 업그레이드한 경우 `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`에서 이전 버전의 플러그 인을 사용을 시도할 수도 있습니다. 그렇다면 `maven-archetype-plugin` 디렉터리를 삭제하고 명령을 다시 실행해보세요.

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Maven이 프로젝트 생성을 완료하기 위해 필요한 값을 요청합니다. _groupId_, _artifactId_ 및 _version_ 값은 [Maven 명명 규칙](https://maven.apache.org/guides/mini/guide-naming-conventions.html) 참고를 참조하세요. _appName_ 값은 Azure 전체에서 고유해야 하므로 기본적으로 Maven이 이전에 입력한 _artifactId_ 를 기준으로 앱 이름을 생성합니다. _packageName_ 값은 생성된 함수 코드에 대한 Java 패키지를 결정합니다.

아래의 `com.fabrikam.functions` 및 `fabrikam-functions` 식별자는 예제로 사용되며 이 빠른 시작의 이후 단계를 좀 더 쉽게 읽는 데 도움이 됩니다. 이 단계에서는 Maven에 각자 고유의 값을 제공하시기 바랍니다.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven은 이름이 _artifactId_ 인 새 폴더에 프로젝트 파일을 만드는데, 이 예제에서는 `fabrikam-functions`입니다. 이 프로젝트에서 생성된 즉시 실행 가능 코드는 요청의 본문을 에코하는 간단한 [HTTP 트리거](/azure/azure-functions/functions-bindings-http-webhook) 함수입니다.

```java
public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
        } else {
            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        }
    }
}

```

## <a name="reference-bindings"></a>바인딩 참조

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

디렉터리를 새로 만든 프로젝트 폴더로 변경하고 Maven으로 함수를 실행합니다.

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Java 9에서 `javax.xml.bind.JAXBException` 예외가 발생하면 [GitHub](https://github.com/jOOQ/jOOQ/issues/6477)에서 해결 방법을 참조하세요.

함수가 시스템에서 로컬로 실행되고 HTTP 요청에 응답할 준비가 완료되면 이 출력이 보입니다.

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

새 터미널 창에서 curl을 사용하여 명령줄에서 함수를 트리거합니다.

```
curl -w "\n" http://localhost:7071/api/hello -d LocalFunction
```

```Output
Hello LocalFunction!
```

터미널에서 `Ctrl-C`를 사용하여 함수 코드를 중지합니다.

## <a name="deploy-the-function-to-azure"></a>Azure에 함수 배포

Azure Functions에 대한 배포 프로세스는 Azure CLI의 계정 자격 증명을 사용합니다. 계속하려면 [Azure CLI에 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)합니다.

```azurecli
az login
```

`azure-functions:deploy` Maven 대상을 사용하여 새 함수 앱에 코드를 배포합니다.

> [!NOTE]
> Visual Studio Code를 사용하여 함수 앱을 배포하는 경우 체험판이 아닌 구독을 선택해야 합니다. 그렇지 않으면 오류가 표시됩니다. IDE의 왼쪽에 구독이 표시됩니다.

```
mvn azure-functions:deploy
```

배포가 완료되면 Azure 함수 앱에 액세스하는 데 사용할 수 있는 URL이 표시됩니다.

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

`cURL`을 사용하여 Azure에서 실행 중인 함수 앱을 테스트합니다. 아래 예제에서는 이전 단계에서 만든 사용자 고유의 함수 앱에 배포한 URL과 일치하도록 URL을 변경해야 합니다.

> [!NOTE]
> **액세스 권한**을 `Anonymous`로 설정해야 합니다. 기본 수준 `Function`을 선택하면 함수 엔드포인트에 액세스하기 위해 요청에 [함수 키](../azure-functions/functions-bindings-http-webhook.md#authorization-keys)를 제공해야 합니다.

```
curl -w "\n" https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>변경 및 재배포

생성된 프로젝트에서 `src/main.../Function.java` 원본 파일을 편집하여 함수 앱에서 반환된 텍스트를 변경합니다. 다음과 같이 변경합니다.

```java
return request.createResponse(200, "Hello, " + name);
```

다음으로:

```java
return request.createResponse(200, "Hi, " + name);
```

변경 내용을 저장합니다. 이전처럼 터미널에서 `azure-functions:deploy`를 실행하여 mvn 정리 패키지를 실행하고 다시 배포합니다. 함수 앱이 업데이트되고 이 요청은 다음을 갖습니다.

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

업데이트된 출력은 다음과 같습니다.

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>다음 단계

간단한 HTTP 트리거가 있는 Java 함수 앱을 만들어 Azure Functions에 배포했습니다.

- Java 함수 개발에 대한 자세한 내용은 [Java 함수 개발자 가이드](functions-reference-java.md)를 참조합니다.
- `azure-functions:add` Maven 대상을 사용하여 프로젝트에 다른 트리거가 있는 다른 함수를 추가합니다.
- [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) 및 [Eclipse](functions-create-maven-eclipse.md)를 사용하여 로컬로 함수를 작성하고 디버그합니다. 
- Visual Studio Code를 사용하여 Azure에 배포된 함수를 디버그합니다. 자세한 지침은 Visual Studio Code [서버 없는 Java 애플리케이션](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) 설명서를 참조하세요.
