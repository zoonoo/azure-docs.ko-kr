---
title: '빠른 시작: 첫 번째 Java 쿼리'
description: 이 빠른 시작에서는 Java용 Resource Graph Maven 패키지를 사용하도록 설정하고 첫 번째 쿼리를 실행하는 단계를 수행합니다.
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 04e32a3e147ed68248f7fead488a1f630ffcdb4d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108751754"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>빠른 시작: Java를 사용하여 첫 번째 Resource Graph 쿼리 실행

Azure Resource Graph를 사용하는 첫 번째 단계는 필요한 Java용 Maven 패키지가 설치되었는지 확인하는 것입니다. 이 빠른 시작에서는 설치된 Java에 Maven 패키지를 추가하는 과정을 안내합니다.

이 과정이 끝나면 설치된 Java에 Maven 패키지가 추가되고 첫 번째 Resource Graph 쿼리를 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- 최신 Azure CLI가 설치되어 있는지 확인합니다(**2.21.0** 이상). 아직 설치되지 않았으면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

  > [!NOTE]
  > 다음 예제에서 **CLI 기반 인증** 을 사용하기 위해 Java용 Azure SDK를 사용하도록 설정하려면 Azure CLI가 필요합니다. 다른 옵션에 대한 자세한 정보는 [Java용 Azure ID 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)를 참조하세요.

- [Java 개발자 키트](/azure/developer/java/fundamentals/java-jdk-long-term-support) 버전
  8.

- [Apache Maven](https://maven.apache.org/) 버전 3.6 이상

## <a name="create-the-resource-graph-project"></a>Resource Graph 프로젝트 만들기

Azure Resource Graph를 쿼리하기 위해 Java를 사용하도록 설정하려면 Maven이 포함된 새 애플리케이션을 만들고 구성하고, 필수 Maven 패키지를 설치합니다.

1. [Maven 원형](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)을 사용하여 “argQuery”라는 새 Java 애플리케이션을 초기화합니다.

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. 새 프로젝트 폴더 `argQuery`로 디렉터리를 변경하고 즐겨 사용하는 편집기에서 `pom.xml`을 엽니다. 기존 `<dependencies>` 노드 아래에 다음 `<dependency>` 노드를 추가합니다.

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. `pom.xml` 파일에서 기본 `<project>` 노드 아래에 다음 `<properties>` 노드를 추가하여 원본 및 대상 버전을 업데이트합니다.

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. `pom.xml` 파일에서 기본 `<project>` 노드 아래에 다음 `<build>` 노드를 추가하여 실행할 프로젝트의 목표 및 주 클래스를 구성합니다.

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. `\argQuery\src\main\java\com\Fabrikam`에서 기본 `App.java`를 다음 코드로 바꾸고 업데이트된 파일을 저장합니다.

   ```java
   package com.Fabrikam;

   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;

   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];

           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));

           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);

           QueryResponse response = manager.resourceProviders().resources(queryRequest);

           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. `argQuery` 콘솔 애플리케이션을 빌드합니다.

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>첫 번째 Resource Graph 실행

Java 콘솔 애플리케이션을 빌드하고 간단한 Resource Graph 쿼리를 시도해 봅니다. 쿼리는 각 리소스의 **이름** 및 **리소스 형식** 과 함께 처음 5개 Azure 리소스를 반환합니다.

`argQuery`를 호출할 때마다 다음 변수를 각자 해당하는 값으로 바꿔야 합니다.

- `{subscriptionId}` - 사용자의 구독 ID로 대체
- `{query}` - Azure Resource Graph 쿼리 대체

1. Azure CLI를 사용하여 `az login`으로 인증합니다.

1. 디렉터리를 이전 `mvn -B archetype:generate` 명령으로 만든 `argQuery` 프로젝트 폴더로 변경합니다.

1. 먼저 Maven을 사용하여 Azure Resource Graph 쿼리를 실행하여 콘솔 애플리케이션을 컴파일하고 인수를 전달합니다. `exec.args` 속성은 공백으로 인수를 식별합니다. 단일 인수로 쿼리를 식별하려면 작은 따옴표(`'`)로 래핑합니다.

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > 이 쿼리 예제에서는 `order by`와 같은 정렬 한정자를 제공하지 않으므로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 세트가 생성될 수 있습니다.

1. 인수를 `argQuery.exe`로 변경하고 쿼리를 **Name** 속성 `order by`로 변경합니다.

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > 첫 번째 쿼리와 마찬가지로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다. 쿼리 명령의 순서는 중요합니다. 이 예제에서 `order by`는 `limit` 뒤에 옵니다. 이 명령 순서는 먼저 쿼리 결과를 제한한 다음, 정렬합니다.

1. 마지막 매개 변수를 `argQuery.exe`로 변경하고, 먼저 **Name** 속성을 기준으로 `order by`한 후 상위 5개 결과로 `limit`하도록 쿼리를 변경합니다.

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

최종 쿼리가 여러 번 실행될 때 환경이 전혀 변경되지 않는다고 가정하면 반환되는 결과는 **Name** 속성을 기준으로 일관되고 정렬되지만 여전히 상위 5개 결과로 제한됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Java 콘솔 애플리케이션 및 설치된 패키지를 제거하려면 `argQuery` 프로젝트 폴더를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 필요한 Resource Graph 패키지가 설치된 Java 콘솔 애플리케이션을 만들고 첫 번째 쿼리를 실행했습니다. 리소스 그래프 언어에 대한 자세한 내용을 보려면 쿼리 언어 정보 페이지로 이동하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](./concepts/query-language.md)
