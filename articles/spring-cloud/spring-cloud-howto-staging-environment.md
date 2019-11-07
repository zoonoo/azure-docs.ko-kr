---
title: Azure 스프링 클라우드에서 스테이징 환경 설정 | Microsoft Docs
description: Azure 스프링 클라우드에서 blue-녹색 배포를 사용 하는 방법을 알아봅니다.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 24ce4dee04e4daf3eaee4144f8dc56de5867bbca
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607220"
---
# <a name="how-to-set-up-a-staging-environment"></a>스테이징 환경을 설정 하는 방법

이 문서에서는 Azure 스프링 클라우드의 파랑-녹색 배포 패턴을 사용 하 여 스테이징 배포를 활용 하는 방법을 보여 줍니다. 또한 프로덕션 배포를 직접 변경 하지 않고 스테이징 배포를 프로덕션에 배치 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 [응용 프로그램 시작에 대 한 자습서](spring-cloud-quickstart-launch-app-portal.md)에서 PiggyMetrics 응용 프로그램을 이미 배포 했다고 가정 합니다. PiggyMetrics는 "게이트웨이", "계정-서비스" 및 "인증-서비스"의 세 가지 응용 프로그램으로 구성 됩니다.  

이 예제에 사용 하려는 다른 응용 프로그램이 있는 경우 응용 프로그램의 공용 부분에서 간단한 변경을 수행 해야 합니다.  이 변경으로 프로덕션에서 스테이징 배포를 구별 합니다.

>[!TIP]
> Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다.  최신 버전의 Git, JDK, Maven 및 Azure CLI를 포함 하는 일반적인 Azure tools가 미리 설치 되어 있습니다. Azure 구독에 로그인 한 경우 shell.azure.com에서 [Azure Cloud Shell](https://shell.azure.com) 를 시작 합니다.  [설명서](../cloud-shell/overview.md) 를 참조 하 여 Azure Cloud Shell에 대해 자세히 알아볼 수 있습니다.

이 문서를 완료 하려면 다음을 수행 합니다.


## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

다음 명령을 사용 하 여 Azure CLI에 대 한 Azure 스프링 클라우드 확장을 설치 합니다.

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>모든 배포 보기

Azure Portal에서 서비스 인스턴스로 이동 하 고 **배포 관리** 를 선택 하 여 모든 배포를 확인 합니다. 각 배포를 선택 하 여 자세한 내용을 확인할 수 있습니다.

## <a name="create-a-staging-deployment"></a>스테이징 배포 만들기

1. 로컬 개발 환경에서 피기백 메트릭의 게이트웨이 응용 프로그램을 약간 수정 해야 합니다. 예를 들어 `gateway/src/main/resources/static/css/launch.css`색을 변경 합니다. 이렇게 하면 두 배포를 쉽게 구분할 수 있습니다. 다음 명령을 실행 하 여 jar 패키지를 빌드합니다. 

    ```azurecli
    mvn clean package
    ```

1. Azure CLI를 사용 하 여 새 배포를 만들어 스테이징 배포 이름 "green"을 제공 합니다.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 배포가 성공적으로 완료 되 면 **응용 프로그램 대시보드에서** 게이트웨이 페이지에 액세스 하 고 왼쪽의 **앱 인스턴스** 탭에서 모든 인스턴스를 확인 합니다.
  
> [!NOTE]
> 검색 상태는 "OUT_OF_SERVICE" 이므로 확인이 완료 되기 전에 트래픽이이 배포로 라우팅되지 않습니다.

## <a name="verify-the-staging-deployment"></a>스테이징 배포 확인

1. **배포 관리** 페이지로 돌아가서 새 배포를 선택 합니다. 배포 상태는 **실행 중**으로 표시 되어야 합니다. "도메인 할당/할당 취소" 단추는 스테이징 환경 이므로 사용 하지 않도록 설정 됩니다.

1. **개요** 페이지에는 **테스트 끝점이**표시 됩니다. 새 브라우저 페이지에 복사 하 여 붙여 넣으면 새 피기백 메트릭 페이지가 표시 됩니다.

>[!TIP]
> * 테스트 끝점이 "/"로 끝나는지 확인 하 여 CSS가 올바르게 로드 되는지 확인 합니다.  
> * 브라우저에서 로그인 자격 증명을 입력 하 여 페이지를 표시 해야 하는 경우 [URL 디코드](https://www.urldecoder.org/) 를 사용 하 여 테스트 끝점을 디코딩합니다. URL 디코드는 URL을 "https://\<username >:\<password > @\<cluster-name >/gateway/green" 형식으로 반환 합니다.  이를 사용 하 여 끝점에 액세스 합니다.

>[!NOTE]    
> 구성 서버 설정은 스테이징 환경 및 프로덕션 환경에 적용 됩니다. 예를 들어 구성 서버에서 앱 게이트웨이의 컨텍스트 경로 (`server.servlet.context-path`)를 *somepath*로 설정 하는 경우 녹색 배포에 대 한 경로가 변경 됩니다. "https://\<username >:\<password > @\<cluster-name >. test.azureapps.io/gateway/green/somepath/
 
 이 시점에서 공용 앱 게이트웨이를 방문 하면 새 변경 없이 이전 페이지가 표시 됩니다.
    
## <a name="set-the-green-as-production-deployment"></a>녹색을 프로덕션 배포로 설정

1. 스테이징 환경에서 변경 내용을 확인 한 후 프로덕션으로 푸시할 수 있습니다. **배포 관리** 로 돌아가서 "게이트웨이" 응용 프로그램 앞에 있는 확인란을 선택 합니다.
2. "배포 설정"을 선택 합니다.
3. "프로덕션 배포" 메뉴에서 "녹색"을 선택 하 고 **적용** 을 선택 합니다.
4. 게이트웨이 응용 프로그램 **개요** 페이지로 이동 합니다. 게이트웨이 응용 프로그램에 대 한 도메인을 이미 할당 한 경우 URL은 **개요** 페이지에 표시 됩니다. URL을 방문 하면 수정 된 피기백 메트릭 페이지가 표시 됩니다.

>[!NOTE]
> 녹색 배포가 프로덕션 환경으로 설정 되 면 이전 배포는 스테이징 배포가 됩니다.

## <a name="modify-the-staging-deployment"></a>스테이징 배포 수정

변경에 만족 하지 않는 경우 응용 프로그램 코드를 수정 하 고 새 jar 패키지를 작성 한 다음 Azure CLI를 사용 하 여 녹색 배포에 업로드할 수 있습니다.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>스테이징 배포 삭제

스테이징 배포 페이지로 이동 하 고 **삭제** 단추를 선택 하 여 Azure 포트에서 스테이징 배포를 삭제 합니다.

또는 다음 명령을 사용 하 여 Azure CLI에서 스테이징 배포를 삭제 합니다.

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
