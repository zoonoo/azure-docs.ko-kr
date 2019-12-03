---
title: Azure App Configuration 사용 방법을 배우기 위한 빠른 시작 | Microsoft Docs
description: Java Spring 앱 지원 Azure App Configuration 사용 빠른 시작
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: e8f6f9ca610c515deca6ed1bdbee54f40cacf427
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184931"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>빠른 시작: Azure App Configuration을 사용하여 Java Spring 앱 만들기

이 빠른 시작에서는 Azure App Configuration을 Java Spring 앱에 통합하여 코드와 별도로 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- 버전 8 이상이 설치된 지원되는 [JDK(Java Development Kit)](https://docs.microsoft.com/java/azure/jdk)
- [Apache Maven](https://maven.apache.org/download.cgi) 버전 3.0 이상

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **구성 탐색기** >  **+ 만들기**를 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | /application/config.message | 안녕하세요. |

    지금은 **레이블**과 **콘텐츠 형식**을 비워 두세요.

## <a name="create-a-spring-boot-app"></a>Spring Boot 앱 만들기

[Spring Initializr](https://start.spring.io/)를 사용하여 새 Spring Boot 프로젝트를 만듭니다.

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

2. 다음 옵션을 지정합니다.

   * **Java**를 사용하는 **Maven** 프로젝트를 생성합니다.
   * **Spring Boot** 버전 2.0 이상을 지정합니다.
   * 애플리케이션에 대한 **그룹** 및 **아티팩트** 이름을 지정합니다.
   * **Web** 종속성 추가

3. 이전 옵션을 지정한 후 **프로젝트 생성**을 선택합니다. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

## <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결

1. 로컬 시스템에서 파일의 압축을 풀면 간단한 Spring Boot 애플리케이션을 편집할 수 있습니다. 앱의 루트 디렉터리에서 *pom.xml* 파일을 찾습니다.

2. 텍스트 편집기에서 *pom.xml* 파일을 열고 `<dependencies>` 목록에 Spring Cloud Azure Config starter를 추가합니다.

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M5</version>
    </dependency>
    ```

3. 앱의 패키지 디렉터리에 *MessageProperties.java*라는 새 Java 파일을 만듭니다. 다음 줄을 추가합니다.

    ```java
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. 앱의 패키지 디렉터리에 *HelloController.java*라는 새 Java 파일을 만듭니다. 다음 줄을 추가합니다.

    ```java
    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. 기본 애플리케이션 Java 파일을 열고 `@EnableConfigurationProperties`를 추가하여 이 기능을 활성화합니다.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. 앱의 리소스 디렉터리 아래에 `bootstrap.properties`라는 새 파일을 만들고, 다음 줄을 파일에 추가합니다. 샘플 값을 App Configuration 저장소에 대한 해당 속성으로 바꿉니다.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다. 예를 들어 다음과 같습니다.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. 애플리케이션이 실행되면 *curl*을 사용하여 애플리케이션을 테스트합니다. 예를 들어 다음과 같습니다.

      ```shell
      curl -X GET http://localhost:8080/
      ```
    App Configuration 저장소에 입력한 메시지가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 App Configuration 저장소를 만들고, Java Spring 앱에서 사용했습니다. 자세한 내용은 [Azure의 Spring](https://docs.microsoft.com/java/azure/spring-framework/)을 참조하세요. Azure 관리 ID를 사용하여 App Configuration에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
