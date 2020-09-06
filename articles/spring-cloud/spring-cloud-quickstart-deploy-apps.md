---
title: 빠른 시작 - Azure Spring Cloud에 앱 빌드 및 배포
description: Azure Spring Cloud에 앱 배포에 대해 설명합니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8931c22c3656cf9708756153268ab1d9d87b8343
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050831"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>빠른 시작: Azure Spring Cloud에 앱 빌드 및 배포

이 문서에서는 다음을 사용하여 마이크로서비스 애플리케이션을 빌드하고 Azure Spring Cloud에 배포하는 방법을 설명합니다.
* Azure CLI
* Maven 플러그 인
* Intellij

Azure CLI 또는 Maven을 사용하여 배포하기 전에 [Azure Spring Cloud의 인스턴스를 프로비저닝](spring-cloud-quickstart-provision-service-instance.md)하고 [구성 서버를 설정](spring-cloud-quickstart-setup-config-server.md)하는 예제를 완료합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [JDK 8 설치](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Azure 구독에 가입](https://azure.microsoft.com/free/)
* (선택 사항) [Azure CLI 버전 2.0.67 이상을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)하고 다음 명령을 사용하여 Azure Spring Cloud 확장을 설치합니다. `az extension add --name spring-cloud`
* (선택 사항) [Azure Toolkit for IntelliJ를 설치](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/)하고 [로그인](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)합니다.

## <a name="deployment-procedures"></a>배포 절차

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>로컬에서 마이크로서비스 애플리케이션 빌드

1. 샘플 앱 리포지토리를 Azure Cloud 계정에 복제합니다.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. 디렉터리를 변경하고 프로젝트를 빌드합니다.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

프로젝트를 컴파일하는 데는 약 5분이 걸립니다. 완료되면 각 서비스에 대한 개별 JAR 파일이 해당 폴더에 포함되어야 합니다.

### <a name="create-and-deploy-the-apps"></a>앱 만들기 및 배포

1. 다음 명령을 사용하여 기본 리소스 그룹 이름 및 클러스터 이름을 설정합니다.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. 이전 단계에서 빌드된 JAR 파일을 사용하여 Azure Spring Cloud 마이크로서비스를 만듭니다. **gateway**, **auth-service** 및 **account-service**라는 3개의 앱을 만들게 됩니다.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. 이전 단계에서 만든 애플리케이션을 Azure에 배포해야 합니다. 다음 명령을 사용하여 3개 애플리케이션을 모두 배포합니다.

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>게이트웨이에 공용 엔드포인트 할당

웹 브라우저를 통해 애플리케이션에 액세스할 방법이 필요합니다. 게이트웨이 애플리케이션에는 공용 엔드포인트가 필요합니다.

1. 다음 명령을 사용하여 엔드포인트를 할당합니다.

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. 애플리케이션이 실행 중인지 확인할 수 있도록 **게이트웨이** 애플리케이션에 공용 IP를 쿼리합니다.

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>샘플 애플리케이션 리포지토리 복제 및 빌드

1. 다음 명령을 실행하여 Git 리포지토리를 복제합니다.

    ```
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. 다음 명령을 실행하여 디렉터리를 변경하고 프로젝트를 빌드합니다.

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>구성 생성 및 Azure Spring Cloud에 배포

1. 부모 POM을 포함하는 PiggyMetrics의 루트 폴더에서 다음 명령을 실행하여 구성을 생성합니다. Azure CLI를 사용하여 이미 로그인한 경우 이 명령은 자격 증명을 자동으로 선택합니다. 그렇지 않으면 프롬프트 명령을 사용하여 로그인합니다. 자세한 내용은 [Wiki 페이지](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)를 참조하세요.

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    다음 중 선택하라는 메시지가 표시됩니다.
    * **모듈:** `gateway`, `auth-service` 및 `account-service`를 선택합니다.
    * **구독:** Azure Spring Cloud 인스턴스를 만드는 데 사용되는 구독입니다.
    * **서비스 인스턴스:** Azure Spring Cloud 인스턴스의 이름입니다.
    * **퍼블릭 엔드포인트:** 제공된 프로젝트 목록에서 `gateway`에 해당하는 숫자를 입력합니다.  공용 액세스 권한을 부여합니다.

1. 이제 POM에는 플러그 인 종속성 및 구성이 포함됩니다. 다음 명령을 사용하여 앱을 배포합니다. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>IntelliJ에서 샘플 프로젝트 가져오기

1. 이 자습서의 원본 리포지토리를 다운로드하여 압축을 풀거나 Git: `git clone https://github.com/Azure-Samples/piggymetrics`를 사용하여 복제합니다. 

1. IntelliJ **시작** 대화 상자를 열고 **프로젝트 가져오기**를 선택하여 가져오기 마법사를 엽니다.

1. `piggymetric` 폴더를 선택합니다.

    ![프로젝트 가져오기](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Azure Spring Cloud에 게이트웨이 앱 배포
Azure에 배포하려면 Azure Toolkit for IntelliJ를 사용하여 Azure 계정으로 로그인하고 구독을 선택해야 합니다. 로그인 세부 정보는 [설치 및 로그인](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)을 참조하세요.

1. IntelliJ 프로젝트 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure** -> **Azure Spring Cloud에 배포**를 선택합니다.

    ![Azure 1에 배포](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. **이름** 필드에서 *:게이트웨이*를 구성을 참조하는 기존 **이름**에 추가합니다.
1. **아티팩트** 텍스트 상자에서 *com.piggymetrics:gateway:1.0-SNAPSHOT*을 선택합니다.
1. **구독** 텍스트 상자에서 구독을 확인합니다.
1. **Spring Cloud** 텍스트 상자에서 [Azure Spring Cloud 인스턴스 프로비저닝](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance)에서 만든 Azure Spring Cloud의 인스턴스를 선택합니다.
1. **퍼블릭 엔드포인트**를 *사용*으로 설정합니다.
1. **App:** 텍스트 상자에서 **앱 만들기...** 를 선택합니다.
1. *게이트웨이*를 입력한 다음, **확인**을 클릭합니다.

    ![Azure OK에 배포](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. 대화 상자의 **시작하기 전에** 섹션에서 *Maven 목표 실행*을 두 번 클릭합니다.
1. **작업 디렉터리** 텍스트 상자에서 *piggymetrics/gateway* 폴더로 이동합니다.
1. **명령줄** 텍스트 상자에 *package -DskipTests*를 입력합니다. **확인**을 클릭합니다.
1. **Azure Spring Cloud 앱 배포** 대화 상자 아래쪽에 있는 **실행** 단추를 클릭하여 배포를 시작합니다. 플러그 인은 `gateway` 앱에서 `mvn package` 명령을 실행하고 `package` 명령으로 생성된 jar을 배포합니다.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Azure Spring Cloud에 auth-service 및 account-service 앱 배포
위의 단계를 반복하여 `auth-service` 및 `account-service` 앱을 Azure Spring Cloud에 배포할 수 있습니다.

1. **이름** 및 **아티팩트**를 수정하여 `auth-service` 앱을 식별합니다.
1. **App:** 텍스트 상자에서 **앱 만들기...** 를 선택하여 `auth-service` 앱을 만듭니다.
1. **퍼블릭 엔드포인트** 옵션이 *사용 안 함*으로 설정되어 있는지 확인합니다.
1. 대화 상자의 **시작하기 전에** 섹션에서 **작업 디렉터리**를 *piggymetrics/auth-service* 폴더로 전환합니다.
1. **Azure Spring Cloud 앱 배포** 대화 상자 아래쪽에 있는 **실행** 단추를 클릭하여 배포를 시작합니다. 
1. `account-service`를 구성하고 배포하려면 다음 절차를 반복합니다.
---

이전 단계의 출력에서 제공하는 URL로 이동하여 PiggyMetrics 애플리케이션에 액세스합니다. 예: `https://<service instance name>-gateway.azuremicroservices.io`

![PiggyMetrics에 액세스](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Azure Portal로 이동하여 URL을 찾을 수도 있습니다. 
1. 서비스로 이동
2. **앱** 선택
3. **게이트웨이** 선택

    ![앱 탐색](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. **게이트웨이 | 개요** 페이지에서 URL 찾기

    ![앱 두 번째 탐색](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>리소스 정리
이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것으로 예상되는 경우 포털에서 리소스 그룹을 삭제하거나 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다.
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
이전 단계에서는 기본 리소스 그룹 이름도 설정합니다. 해당 기본값을 지우려면 Cloud Shell에서 다음 명령을 실행합니다.
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [로그, 메트릭 및 추적](spring-cloud-quickstart-logs-metrics-tracing.md)
