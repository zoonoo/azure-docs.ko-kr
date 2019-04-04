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
ms.openlocfilehash: fec72a4fac6baa3869928c0203aeb29e53ce5ea4
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648472"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>빠른 시작: App Configuration을 사용하여 Java Spring 앱 만들기

Azure App Configuration은 Azure의 관리형 구성 서비스로서, 코드와 분리된 한 곳에서 모든 애플리케이션 설정을 쉽게 저장하고 관리할 수 있습니다. 이 빠른 시작은 Java Spring 앱으로 서비스를 통합하는 방법을 보여줍니다.

이 빠른 시작의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 수행하려면 [JDK(Java Development Kit)](https://aka.ms/azure-jdks)(버전 8) 및 [Apache Maven](https://maven.apache.org/)(버전 3.0 이상)을 설치합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>앱 구성 저장소 만들기

1. 새 앱 구성 저장소를 만들려면 [Azure Portal](https://aka.ms/azconfig/portal)에 로그인합니다. 페이지의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다. **Marketplace 검색** 상자에서 **App Configuration**을 입력하고 Enter 키를 누릅니다.

    ![App Configuration 검색](./media/quickstarts/azure-app-configuration-new.png)

2. 검색 결과에서 **App Configuration**, **만들기**를 차례로 선택합니다.

3. **App Configuration** > **만들기** 페이지에서 다음 설정을 입력합니다.

    | 설정 | 제안 값 | 설명 |
    |---|---|---|
    | **리소스 이름** | 전역적으로 고유한 이름 | 앱 구성 저장소 리소스에 사용할 고유한 리소스 이름을 입력합니다. 이름은 1~63자의 문자열로, 숫자, 영문자 및 `-` 문자만 포함할 수 있습니다. 이름은 `-` 문자로 시작하거나 끝날 수 없고 연속된 `-` 문자는 유효하지 않습니다.  |
    | **구독** | 사용자의 구독 | App Configuration을 테스트하는 데 사용할 Azure 구독을 선택합니다. 계정에 구독이 하나만 있으면 해당 구독이 자동으로 선택되며 **구독** 드롭다운은 표시되지 않습니다. |
    | **리소스 그룹** | *AppConfigTestResources* | 앱 구성 저장소 리소스에 대한 리소스 그룹을 선택하거나 만듭니다. 이 그룹은 리소스 그룹을 삭제하여 여러 리소스를 동시에 삭제할 수 있도록 구성하는 데 유용합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요. |
    | **위치**: | *미국 중부* | **위치**를 사용하여 SignalR 리소스가 호스트되는 지리적 위치를 지정합니다. 최상의 성능을 얻으려면 애플리케이션의 다른 구성 요소와 동일한 지역에 리소스를 만듭니다. |

    ![앱 구성 저장소 만들기](./media/quickstarts/azure-app-configuration-create.png)

4. **만들기**를 선택합니다. 배포를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

5. 배포가 완료되면 **설정** > **액세스 키**를 차례로 선택합니다. 기본 읽기 전용 또는 기본 읽기-쓰기 키 연결 문자열 중 하나를 적어 두세요. 이 연결 문자열은 나중에 사용자가 만든 앱 구성 저장소와 통신하도록 애플리케이션을 구성하는 데 사용합니다. 연결 문자열은 다음과 같은 형식입니다.

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    애플리케이션에서 전체 문자열을 사용합니다.

6. **키/값 탐색기** > **+ 만들기**를 차례로 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | /application/config.message | 안녕하세요. |

    지금은 **레이블**과 **콘텐츠 형식**을 비워 두세요.

## <a name="create-a-spring-boot-app"></a>Spring Boot 앱 만들기

[Spring Initializr](https://start.spring.io/)를 사용하여 새 Spring Boot 프로젝트를 만듭니다.

1. <https://start.spring.io/>로 이동합니다.

2. 다음 옵션을 지정합니다.

   * **Java**를 사용하는 **Maven** 프로젝트를 생성합니다.
   * **Spring Boot** 버전 2.0 이상을 지정합니다.
   * 애플리케이션에 대한 **그룹** 및 **아티팩트** 이름을 지정합니다.
   * **Web** 종속성 추가

3. 이전 옵션을 지정한 후 **프로젝트 생성**을 선택합니다. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

## <a name="connect-to-an-app-configuration-store"></a>앱 구성 저장소에 연결

1. 로컬 시스템에서 파일의 압축을 풀면 간단한 Spring Boot 애플리케이션을 편집할 수 있습니다. 앱의 루트 디렉터리에서 *pom.xml* 파일을 찾습니다.

2. 텍스트 편집기에서 *pom.xml* 파일을 열고 `<dependencies>` 목록에 Spring Cloud Azure Config starter를 추가합니다.

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M3</version>
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

6. 앱의 리소스 디렉터리 아래에 `bootstrap.properties`라는 새 파일을 만들고, 다음 줄을 파일에 추가합니다. 샘플 값을 앱 구성 저장소에 대한 해당 속성으로 바꿉니다.

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
    앱 구성 저장소에 입력한 메시지가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 앱 구성 저장소를 만들고, Java Spring 앱에서 사용했습니다. 자세한 내용은 [Azure의 Spring](https://docs.microsoft.com/java/azure/spring-framework/)을 참조하세요.

App Configuration을 사용하는 방법을 자세히 알아보려면 인증에 대해 설명하는 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure 리소스 통합용 관리 ID](./integrate-azure-managed-service-identity.md)
