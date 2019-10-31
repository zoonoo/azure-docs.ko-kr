---
title: '빠른 시작: 소스 코드에서 스프링 클라우드 응용 프로그램 시작'
description: 소스 코드에서 직접 Azure 스프링 클라우드 응용 프로그램을 시작 하는 방법을 알아봅니다.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: jeconnoc
ms.openlocfilehash: 573baa242c06868326568a82bc358e136f1ece2c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177965"
---
# <a name="launch-your-spring-cloud-application-from-source-code"></a>소스 코드에서 스프링 클라우드 응용 프로그램 시작

Azure Spring Cloud를 사용하면 Azure에서 Spring Cloud 기반 마이크로서비스 애플리케이션을 쉽게 실행할 수 있습니다.

Azure 스프링 클라우드를 사용 하면 java 소스 코드나 미리 작성 된 JAR에서 직접 응용 프로그램을 시작할 수 있습니다. 이 문서에서는 필요한 단계를 안내 합니다.

이 빠른 시작을 따라 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 서비스 인스턴스 프로비저닝
> * 인스턴스에 대한 구성 서버 설정
> * 로컬에서 마이크로서비스 애플리케이션 빌드
> * 각 마이크로서비스 배포
> * 애플리케이션에 대한 공용 엔드포인트 할당

## <a name="prerequisites"></a>전제 조건

>[!Note]
> 이 빠른 시작을 시작하기 전에 Azure 구독에서 Azure Spring Cloud에 액세스할 수 있는지 확인합니다.  미리 보기 서비스이므로 사용자의 구독을 허용 목록에 추가할 수 있도록 Microsoft에 연락 주시기 바랍니다.  Azure Spring Cloud의 기능을 살펴보려면 [이 양식을 작성](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u)하세요.  Azure 스프링 클라우드가 미리 보기로 제공 되는 동안 Microsoft는 SLA 없이 제한 된 지원을 제공 합니다.  미리 보기 중 지원에 대 한 자세한 내용은이 [지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조 하십시오.

시작 하기 전에 Azure 구독에 필요한 종속성이 있는지 확인 합니다.

1. [Git 설치](https://git-scm.com/)
2. [JDK 8 설치](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Maven 3.0 이상 설치](https://maven.apache.org/download.cgi)
4. [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Azure 구독에 가입](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다.  최신 버전의 Git, JDK, Maven 및 Azure CLI를 포함하는 일반적인 Azure 도구가 미리 설치되어 있습니다. Azure 구독에 로그인한 경우 shell.azure.com에서 [Azure Cloud Shell](https://shell.azure.com)을 시작합니다.  Azure Cloud Shell에 대한 자세한 내용은 [설명서를 참조하세요](../cloud-shell/overview.md).

## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

다음 명령을 사용 하 여 Azure CLI에 대 한 Azure 스프링 클라우드 확장을 설치 합니다.

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Azure CLI를 사용 하 여 서비스 인스턴스 프로 비전

Azure CLI에 로그인하고 활성 구독을 선택합니다. Azure Spring Cloud의 허용 목록에 추가된 활성 구독을 선택해야 합니다.

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Azure CLI 창을 열고 다음 명령을 실행하여 Azure Spring Cloud의 인스턴스를 프로비저닝합니다. 여기서는 Azure 스프링 클라우드에 공용 도메인을 할당 하는 것도 설명 합니다.

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

서비스 인스턴스를 배포 하는 데 약 5 분이 걸립니다.

다음 명령을 사용하여 기본 리소스 그룹 이름 및 클러스터 이름을 설정합니다.

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>스프링 클라우드 응용 프로그램 만들기

다음 명령은 구독에 스프링 클라우드 응용 프로그램을 만듭니다.  그러면 응용 프로그램을 업로드할 수 있는 빈 스프링 클라우드 서비스가 만들어집니다.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>스프링 클라우드 응용 프로그램 배포

미리 작성 된 JAR 또는 Gradle 또는 Maven 리포지토리에서 응용 프로그램을 배포할 수 있습니다.  아래 각 사례에 대 한 지침을 찾습니다.

### <a name="deploy-a-built-jar"></a>빌드된 JAR 배포

로컬 컴퓨터에 빌드된 JAR에서 배포 하려면 빌드가 [fat](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)를 생성 하는지 확인 합니다.

활성 배포에 fat-JAR을 배포 하려면

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

특정 배포에 fat-JAR을 배포 하려면

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>소스 코드에서 배포

Azure 스프링 클라우드는 [kpack](https://github.com/pivotal/kpack) 을 사용 하 여 프로젝트를 빌드합니다.  Azure CLI를 사용 하 여 소스 코드를 업로드 하 고, kpack을 사용 하 여 프로젝트를 빌드하고, 대상 응용 프로그램에 배포할 수 있습니다.

> [!WARNING]
> 프로젝트는 `target`의 `MANIFEST.MF`에서 `main-class` 항목을 사용 하 여 하나의 JAR 파일만 생성 해야 합니다 (Maven 배포의 경우 또는 `build/libs` (Gradle 배포의 경우)).  `main-class` 항목을 포함 하는 여러 JAR 파일이 있으면 배포가 실패 합니다.

단일 모듈 Maven/Gradle 프로젝트의 경우:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

여러 모듈이 있는 Maven/Gradle 프로젝트의 경우 각 모듈에 대해를 반복 합니다.

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>배포 로그 표시

다음 명령을 사용 하 여 kpack 빌드 로그를 검토 합니다.

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Kpack 로그는 해당 배포가 kpack을 사용 하 여 원본에서 빌드된 경우에만 최신 배포를 표시 합니다.

## <a name="assign-a-public-endpoint-to-gateway"></a>게이트웨이에 공용 엔드포인트 할당

1. **애플리케이션 대시보드** 페이지를 엽니다.
2. `gateway` 애플리케이션을 선택하여 **애플리케이션 세부 정보** 페이지를 표시합니다.
3. **도메인 할당**을 선택하여 게이트웨이에 공용 엔드포인트를 할당합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다. 
4. 브라우저에 할당된 공용 IP를 입력하여 실행 중인 애플리케이션을 봅니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 서비스 인스턴스 프로비저닝
> * 인스턴스에 대한 구성 서버 설정
> * 로컬에서 마이크로서비스 애플리케이션 빌드
> * 각 마이크로서비스 배포
> * 응용 프로그램에 대 한 환경 변수 편집
> * 응용 프로그램 게이트웨이에 대 한 공용 IP 할당

> [!div class="nextstepaction"]
> [배포용 Azure Spring Cloud 애플리케이션 준비](spring-cloud-tutorial-prepare-app-deployment.md)
