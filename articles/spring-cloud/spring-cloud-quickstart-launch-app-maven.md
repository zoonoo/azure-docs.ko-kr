---
title: '빠른 시작: Maven을 사용하여 애플리케이션 시작 - Azure Spring Cloud'
description: Maven을 사용하여 샘플 애플리케이션 시작
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: 01140e94e8d0cc47570824970801bdd0043324d7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166507"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>빠른 시작: Maven 플러그 인을 사용하여 Azure Spring Cloud 앱 시작

Azure Spring Cloud의 Maven 플러그 인을 사용하면 Azure Spring Cloud 서비스 애플리케이션을 쉽게 만들고 업데이트할 수 있습니다. 구성을 미리 정의하여 기존 Azure Spring Cloud 서비스에 애플리케이션을 배포할 수 있습니다. 이 문서에서는 PiggyMetrics라는 샘플 애플리케이션을 사용하여 이 기능을 보여줍니다.

>[!Note]
> 이 빠른 시작을 시작하기 전에 Azure 구독에서 Azure Spring Cloud에 액세스할 수 있는지 확인합니다.  미리 보기 서비스이므로 사용자의 구독을 허용 목록에 추가할 수 있도록 Microsoft에 연락 주시기 바랍니다.  Azure Spring Cloud의 기능을 살펴보려면 [이 양식을 작성](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
)하세요.

>[!TIP]
> Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다.  최신 버전의 Git, JDK, Maven 및 Azure CLI를 포함하는 일반적인 Azure 도구가 미리 설치되어 있습니다. Azure 구독에 로그인한 경우 shell.azure.com에서 [Azure Cloud Shell](https://shell.azure.com)을 시작합니다.  Azure Cloud Shell에 대한 자세한 내용은 [설명서를 참조하세요](../cloud-shell/overview.md).

이 빠른 시작을 완료하려면 다음이 필요합니다.

1. [Git 설치](https://git-scm.com/)
2. [JDK 8 설치](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Maven 3.0 이상 설치](https://maven.apache.org/download.cgi)
4. [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Azure 구독에 가입](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

다음 명령을 사용하여 Azure CLI용 Azure Spring Cloud 확장 설치

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Azure Portal에서 서비스 인스턴스 프로비저닝

1. 웹 브라우저에서 [Azure Portal](https://portal.azure.com) 을 열고 계정에 로그인합니다.

1. **Azure Spring Cloud**를 검색하고 선택하여 개요 페이지로 이동합니다. **만들기** 단추를 선택하여 시작합니다.

1. 다음 지침을 고려하여 양식을 작성합니다.
    - 서비스 이름: 서비스 인스턴스의 이름을 지정합니다.  이름은 4-32자 사이여야 하며, 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있습니다.  서비스 이름의 첫 글자는 문자여야 하며 마지막 문자는 문자 또는 숫자여야 합니다.
    - 구독: 이 리소스 대한 요금이 청구될 구독을 선택합니다.  이 구독이 Azure Spring Cloud의 허용 목록에 추가되었는지 확인합니다.
    - 리소스 그룹: 새 리소스에 대한 리소스 그룹을 새로 만드는 것이 가장 좋습니다.
    - 위치: 서비스 인스턴스에 대한 위치를 선택합니다. 현재 지원되는 위치는 미국 동부, 미국 서부 2, 서유럽 및 동남 아시아입니다.
    
서비스를 배포하는 데 약 5분이 걸립니다.  배포되면 서비스 인스턴스에 대한 **개요** 페이지가 나타납니다.

## <a name="set-up-your-configuration-server"></a>구성 서버 설정

1. 서비스 **개요** 페이지로 이동하여 **구성 서버**를 선택합니다.
1. **기본 리포지토리** 섹션에서 **URI**를 "https://github.com/Azure-Samples/piggymetrics"로 설정하고 **LABEL**을 "config"로 설정하고 **적용**을 선택하여 변경 내용을 저장합니다.

## <a name="clone-and-build-the-sample-application-repository"></a>샘플 애플리케이션 리포지토리 복제 및 빌드

1. 다음 명령을 실행하여 Git 리포지토리를 복제합니다.

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. 다음 명령을 실행하여 디렉터리를 변경하고 프로젝트를 빌드합니다.

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Azure Spring Cloud 구성 생성 및 배포

1. Maven이 Azure Spring Cloud에서 작동하도록 다음 내용을 `pom.xml` 또는 `settings.xml`에 추가합니다.

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. 다음 명령을 실행하여 구성을 생성합니다.

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. `gateway`,`auth-service` 및 `account-service` 모듈을 선택합니다.

    1. 구독 및 Azure Spring Cloud 서비스 클러스터를 선택합니다.

    1. 제공된 프로젝트 목록에서 `gateway`에 해당하는 숫자를 입력하여 공용 액세스 권한을 부여합니다.
    
    1. 구성을 확인합니다.

1. 다음 명령을 사용하여 앱을 배포합니다.

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. 이전 명령의 출력에 제공된 URL을 사용하여 PiggyMetrics에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Maven 리포지토리에서 Spring Cloud 애플리케이션을 배포했습니다.  Azure Spring Cloud에 대해 자세히 알아보려면 배포용 앱 준비에 대한 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [배포용 Azure Spring Cloud 애플리케이션 준비](spring-cloud-tutorial-prepare-app-deployment.md)
