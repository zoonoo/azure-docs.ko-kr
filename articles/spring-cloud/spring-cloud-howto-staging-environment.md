---
title: Azure Spring Cloud에서 스테이징 환경 설정 | Microsoft Docs
description: Azure Spring Cloud에서 청록색 배포를 사용하는 방법 알아보기
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 8cae73e03fee0b59be0c7596f0783570ac14f6ee
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053112"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 스테이징 환경 설정

**이 문서는 다음에 적용됩니다.** ✔️ Java

이 문서에서는 Azure 스프링 클라우드의 파랑-녹색 배포 패턴을 사용 하 여 스테이징 배포를 설정 하는 방법을 설명 합니다. 파란색/녹색 배포는 새로운(녹색) 버전이 배포되는 동안 기존(파란색) 버전을 유지하도록 하는 Azure DevOps 지속적인 업데이트 패턴입니다. 이 문서에서는 프로덕션 배포를 직접 변경 하지 않고 스테이징 배포를 프로덕션 환경에 배치 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>전제 조건

* 실행 중인 응용 프로그램입니다.  [빠른 시작: 첫 번째 Azure 스프링 클라우드 응용 프로그램 배포를](spring-cloud-quickstart.md)참조 하세요.
* Azure CLI [asc 확장](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview)

이 예제에 다른 응용 프로그램을 사용 하려는 경우 응용 프로그램의 공용 부분을 간단히 변경 해야 합니다.  이러한 변경을 통해 스테이징 배포와 프로덕션을 구분합니다.

>[!TIP]
> Azure Cloud Shell은이 문서의 지침을 실행 하는 데 사용할 수 있는 무료 대화형 셸입니다.  최신 버전의 Git, JDK, Maven 및 Azure CLI를 포함 하는 미리 설치 된 공통 Azure 도구를 포함 합니다. Azure 구독에 로그인 하는 경우 [Azure Cloud Shell](https://shell.azure.com)를 시작 합니다.  자세히 알아보려면 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조 하세요.

Azure 스프링 클라우드에서 스테이징 환경을 설정 하려면 다음 섹션의 지침을 따르세요.

## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

다음 명령을 사용하여 Azure CLI용 Azure Spring Cloud 확장을 설치합니다.

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>앱 및 배포 보기

다음 절차를 사용 하 여 배포 된 앱을 봅니다.

1. Azure Portal에서 Azure 스프링 클라우드 인스턴스로 이동 합니다.

1. 왼쪽 탐색 창에서 **배포** 를 엽니다.

    [![배포-사용 중단](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. "앱" 블레이드를 열어 서비스 인스턴스에 대 한 앱을 봅니다.

    [![앱-대시보드](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. 앱을 클릭 하 고 세부 정보를 볼 수 있습니다.

    [![앱-개요](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. **배포** 블레이드를 열어 앱의 모든 배포를 확인 합니다. 배포 표에는 배포의 프로덕션 또는 스테이징 여부가 표시 됩니다.

    [![배포 대시보드](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. 배포의 이름을 클릭 하 여 배포 개요를 볼 수 있습니다. 이 경우 유일한 배포의 이름은 *Default* 입니다.

    [![배포 개요](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>스테이징 배포 만들기

1. 로컬 개발 환경에서 응용 프로그램을 약간 수정 해야 합니다. 이렇게 하면 두 배포를 쉽게 구분할 수 있습니다. Jar 패키지를 빌드하려면 다음 명령을 실행 합니다. 

    ```console
    mvn clean package -DskipTests
    ```

1. Azure CLI에서 새 배포를 만들고 스테이징 배포 이름 "green"을 지정 합니다.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app default -n green --jar-path gateway/target/gateway.jar
    ```

1. CLI 배포가 성공적으로 완료 되 면 **응용 프로그램 대시보드에서** 앱 페이지에 액세스 하 고 왼쪽의 **배포** 탭에서 모든 인스턴스를 확인 합니다.

   [![녹색 배포 후 배포 대시보드](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> 확인이 완료 되기 전에 트래픽이이 배포로 라우팅되지 않도록 검색 상태가 *OUT_OF_SERVICE* 됩니다.

## <a name="verify-the-staging-deployment"></a>스테이징 배포 확인

녹색 준비 개발이 작동 하는지 확인 하려면 다음을 수행 합니다.
1. **배포** 로 이동 하 여 `green` **스테이징 배포** 를 클릭 합니다.
1. **개요** 페이지에서 **테스트 끝점** 을 클릭 합니다.
1. 이렇게 하면 변경 내용을 보여 주는 준비 빌드가 열립니다.

>[!TIP]
> * 테스트 끝점이 슬래시 (/)로 끝나는지 확인 하 여 CSS 파일이 올바르게 로드 되는지 확인 합니다.  
> * 브라우저에서 페이지를 보기 위해 로그인 자격 증명을 입력해야 하는 경우, [URL 디코드](https://www.urldecoder.org/)를 사용하여 테스트 엔드포인트를 디코드합니다. Url 디코드는 url을 "https:// \<username> : test.azureapps.io/gateway/green" 형식으로 반환 \<password> @ \<cluster-name> 합니다.  이 형식을 사용 하 여 끝점에 액세스 합니다.

>[!NOTE]    
> 구성 서버 설정은 스테이징 환경과 프로덕션 모두에 적용 됩니다. 예를 들어 `server.servlet.context-path` 구성 서버에서 앱 게이트웨이의 컨텍스트 경로 ()를 *somepath* 로 설정 하면 녹색 배포 경로가 "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/green/somepath/..."로 변경 됩니다.
 
 이 시점에서 공용 앱 게이트웨이를 방문 하면 새 변경 없이 이전 페이지가 표시 됩니다.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>녹색 배포를 프로덕션 환경으로 설정

1. 스테이징 환경에서 변경 내용을 확인 한 후 프로덕션으로 푸시할 수 있습니다. **배포 관리** 로 돌아가서 현재에서 응용 프로그램을 선택 `Production` 합니다.

1. **등록 상태** 뒤의 줄임표를 클릭 하 고 프로덕션 빌드를로 설정 `staging` 합니다.

   [![배포에서 스테이징 배포 설정](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. **배포 관리** 페이지로 돌아갑니다.  `green`배포 배포 상태가 표시 되어야 합니다 . 이제 프로덕션 빌드를 실행 하 고 있습니다.

   [![배포 준비 배포 결과 설정](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. URL을 복사 하 여 새 브라우저 창에 붙여 넣고 새 응용 프로그램 페이지가 변경 내용과 함께 표시 되어야 합니다.

>[!NOTE]
> 녹색 배포를 프로덕션 환경으로 설정한 후에는 이전 배포가 스테이징 배포가 됩니다.

## <a name="modify-the-staging-deployment"></a>스테이징 배포 수정

변경에 만족 하지 않는 경우 응용 프로그램 코드를 수정 하 고 새 jar 패키지를 작성 한 다음 Azure CLI를 사용 하 여 녹색 배포에 업로드할 수 있습니다.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>스테이징 배포 삭제

Azure 포트에서 스테이징 배포를 삭제 하려면 스테이징 배포 페이지로 이동한 후 **삭제** 단추를 선택 합니다.

또는 다음 명령을 실행 하 여 Azure CLI에서 스테이징 배포를 삭제 합니다.

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>다음 단계

* [Azure 스프링 클라우드 용 CI/CD](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)