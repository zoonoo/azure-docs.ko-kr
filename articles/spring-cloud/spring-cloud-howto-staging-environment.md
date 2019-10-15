---
title: Azure Spring Cloud에서 스테이징 환경 설정 | Microsoft Docs
description: Azure Spring Cloud에서 청록색 배포를 사용하는 방법 알아보기
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 454eeaa2568891ec35fe698cdb20c5448e10887e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038302"
---
# <a name="how-to-set-up-a-staging-environment"></a>스테이징 환경을 설정하는 방법

이 문서에서는 Azure Spring Cloud에서 청록색 배포 패턴을 사용하여 스테이징 배포를 활용하는 방법을 보여줍니다. 또한 프로덕션 배포를 직접 변경하지 않고 스테이징 배포를 프로덕션에 배치하는 방법도 보여줍니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 [애플리케이션 실행에 대한 자습서](spring-cloud-quickstart-launch-app-portal.md)에서 PiggyMetrics 애플리케이션을 이미 배포했다고 가정합니다. PiggyMetrics는 "gateway", "account-service" 및 "auth-service"라는 세 가지 애플리케이션으로 구성됩니다.  

이 예제에 사용하려는 다른 애플리케이션이 있으면 애플리케이션의 공개 부분에서 간단한 변경을 수행해야 합니다.  이러한 변경을 통해 스테이징 배포와 프로덕션을 구분합니다.

>[!NOTE]
> 이 빠른 시작을 시작하기 전에 Azure 구독에서 Azure Spring Cloud에 액세스할 수 있는지 확인합니다.  미리 보기 서비스이므로 사용자의 구독을 허용 목록에 추가할 수 있도록 Microsoft에 연락 주시기 바랍니다.  Azure Spring Cloud의 기능을 살펴보려면 이메일(azure-spring-cloud@service.microsoft.com)로 문의하시기 바랍니다.

>[!TIP]
> Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다.  최신 버전의 Git, JDK, Maven 및 Azure CLI를 포함하는 일반적인 Azure 도구가 미리 설치되어 있습니다. Azure 구독에 로그인한 경우 shell.azure.com에서 [Azure Cloud Shell](https://shell.azure.com)을 시작합니다.  Azure Cloud Shell에 대한 자세한 내용은 [설명서를 참조하세요](../cloud-shell/overview.md).

이 문서를 완료하려면 다음이 필요합니다.

1. [Git 설치](https://git-scm.com/)
1. [JDK 8 설치](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
1. [Maven 3.0 이상 설치](https://maven.apache.org/download.cgi)
1. [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
1. [Azure 구독에 가입](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

다음 명령을 사용하여 Azure CLI용 Azure Spring Cloud 확장 설치

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```
    
## <a name="view-all-deployments"></a>모든 배포 보기

Azure Portal에서 서비스 인스턴스로 이동하고 **배포 관리**를 선택하여 모든 배포를 확인합니다. 각 배포를 선택하면 자세한 내용을 확인할 수 있습니다.

## <a name="create-a-staging-deployment"></a>스테이징 배포 만들기

1. 로컬 개발 환경에서 Piggy Metric의 게이트웨이 애플리케이션을 약간 수정합니다. 예를 들어 `gateway/src/main/resources/static/css/launch.css`에서 색을 변경합니다. 이렇게 하면 두 배포를 쉽게 구분할 수 있습니다. 다음 명령을 실행하여 jar 패키지를 빌드합니다. 

    ```azurecli
    mvn clean package
    ```

1. Azure CLI를 사용하여 새 배포를 만들고, 스테이징 배포 이름을 "green"으로 지정합니다.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 배포가 완료되면, **애플리케이션 대시보드**에서 게이트웨이 페이지에 액세스하고 왼쪽에 있는 **앱 인스턴스** 탭에서 모든 인스턴스를 확인합니다.
  
> [!NOTE]
> 배포 상태는 "OUT_OF_SERVICE"입니다. 그래야 확인이 완료되기 전에 이 배포로 트래픽이 라우팅되지 않습니다.

## <a name="verify-the-staging-deployment"></a>스테이징 배포 확인

1.**배포 관리** 페이지로 돌아가서 새 배포를 선택합니다. 배포 상태가 **실행 중**으로 표시되어야 합니다. "도메인 할당/할당 취소" 단추는 스테이징 환경이므로 사용하지 않도록 설정됩니다.

1. **개요** 페이지에 **테스트 엔드포인트**가 보입니다. 이것을 복사하여 새 브라우저 페이지에 붙여넣으면 새 Piggy Metrics 페이지가 표시됩니다.

>[!TIP]
> * CSS가 올바르게 로드되도록 테스트 엔드포인트가 "/"로 끝나는지 확인합니다.  
> * 브라우저에서 페이지를 보기 위해 로그인 자격 증명을 입력해야 하는 경우, [URL 디코드](https://www.urldecoder.org/)를 사용하여 테스트 엔드포인트를 디코드합니다. URL 디코드는 "https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green" 형식의 URL을 반환합니다.  이것을 사용하여 엔드포인트에 액세스합니다.

>[!NOTE]    
> 구성 서버 설정은 스테이징 환경은 물론 프로덕션 환경에도 적용됩니다. 예를 들어, 구성 서버의 앱 게이트웨이에 대한 컨텍스트 경로(`server.servlet.context-path`)를 *somepath*로 설정하면, 녹색 배포에 대한 경로가 "https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/..."로 변경됩니다.
 
 이 시점에서 공개 앱 게이트웨이를 방문하면 새로운 변경 없이 이전 페이지가 표시됩니다.
    
## <a name="set-the-green-as-production-deployment"></a>녹색을 프로덕션 배포로 설정

1. 스테이징 환경에서 변경 내용을 확인한 후 프로덕션 환경으로 푸시할 수 있습니다. **배포 관리**로 돌아가서 "게이트웨이" 애플리케이션 앞에 있는 확인란을 선택합니다.
2. "배포 설정"을 선택합니다.
3. "프로덕션 배포" 메뉴에서 "녹색"을 선택하고 **적용**을 선택합니다.
4. 게이트웨이 애플리케이션 **개요** 페이지로 이동합니다. 게이트웨이 애플리케이션에 도메인을 이미 할당한 경우, URL이 **개요** 페이지에 표시됩니다. URL을 방문하면 수정된 Piggy Metrics 페이지가 보입니다.

>[!NOTE]
> 녹색 배포가 프로덕션 환경으로 설정되면 이전 배포는 스테이징 배포가 됩니다.

## <a name="modify-the-staging-deployment"></a>스테이징 배포 수정

변경 내용이 만족스럽지 않으면 애플리케이션 코드를 수정하고 새 jar 패키지를 빌드한 후 Azure CLI를 사용하여 녹색 배포에 업로드할 수 있습니다.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>스테이징 배포 삭제

스테이징 배포 페이지로 이동하고 **삭제** 단추를 선택하여 Azure 포트에서 스테이징 배포를 삭제합니다.

또는 다음 명령을 사용하여 Azure CLI에서 스테이징 배포를 삭제합니다.

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
