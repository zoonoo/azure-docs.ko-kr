---
title: Azure Spring Cloud에서 스테이징 환경 설정 | Microsoft Docs
description: Azure Spring Cloud에서 청록색 배포를 사용하는 방법 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471033"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Azure 스프링 클라우드에서 준비 환경 설정

이 문서에서는 Azure Spring Cloud에서 파란색-녹색 배포 패턴을 사용하여 스테이징 배포를 설정하는 방법에 대해 설명합니다. 파란색/녹색 배포는 새로운(녹색) 버전이 배포되는 동안 기존(파란색) 버전을 유지하도록 하는 Azure DevOps 지속적인 업데이트 패턴입니다. 이 문서에서는 프로덕션 배포를 직접 변경하지 않고 해당 스테이징 배포를 프로덕션에 배치하는 방법을 보여 주며 이 문서에서는 해당 스테이징 배포를 프로덕션에 직접 배치하는 방법을 보여 주며 이 문서에서는 해당 스테이징 배포를 프로덕션에 직접 배치하는 방법을 보여 주며, 이 문서에서는 해당 스테이

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure Spring 클라우드 응용 프로그램 [시작에 대한 자습서에서](spring-cloud-quickstart-launch-app-portal.md)PiggyMetrics 응용 프로그램을 이미 배포했다고 가정합니다. PiggyMetrics는 "게이트웨이", "계정 서비스" 및 "인증 서비스"의 세 가지 응용 프로그램으로 구성됩니다.  

이 예제에 다른 응용 프로그램을 사용하려면 응용 프로그램의 공용 부분을 간단하게 변경해야 합니다.  이러한 변경을 통해 스테이징 배포와 프로덕션을 구분합니다.

>[!TIP]
> Azure Cloud Shell은 이 문서의 지침을 실행하는 데 사용할 수 있는 무료 대화형 셸입니다.  Git, JDK, Maven 및 Azure CLI의 최신 버전을 포함하여 사전 설치된 일반적인 Azure 도구가 있습니다. Azure 구독에 로그인한 경우 [Azure 클라우드 셸을](https://shell.azure.com)시작합니다.  자세한 내용은 [Azure 클라우드 셸의 개요를](../cloud-shell/overview.md)참조하십시오.

Azure Spring Cloud에서 준비 환경을 설정하려면 다음 섹션의 지침을 따릅니다.

## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

다음 명령을 사용하여 Azure CLI용 Azure Spring Cloud 확장을 설치합니다.

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>모든 배포 보기

Azure 포털의 서비스 인스턴스로 이동하여 **배포 관리를** 선택하여 모든 배포를 봅니다. 자세한 내용을 보려면 각 배포를 선택할 수 있습니다.

## <a name="create-a-staging-deployment"></a>스테이징 배포 만들기

1. 로컬 개발 환경에서 PiggyMetrics 게이트웨이 응용 프로그램을 약간 수정합니다. 예를 들어 *게이트웨이/src/주/리소스/정적/css/launch.css* 파일의 색상을 변경합니다. 이렇게 하면 두 배포를 쉽게 구분할 수 있습니다. jar 패키지를 빌드하려면 다음 명령을 실행합니다. 

    ```console
    mvn clean package
    ```

1. Azure CLI에서 새 배포를 만들고 스테이징 배포 이름을 "녹색"으로 지정합니다.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 배포가 성공적으로 완료되면 **응용 프로그램 대시보드에서**게이트웨이 페이지에 액세스하고 왼쪽의 **앱 인스턴스** 탭에서 모든 인스턴스를 봅니다.
  
> [!NOTE]
> 확인이 완료되기 전에 트래픽이 이 배포로 라우팅되지 않도록 검색 상태가 *OUT_OF_SERVICE.*

## <a name="verify-the-staging-deployment"></a>스테이징 배포 확인

1. **배포 관리** 페이지로 돌아가서 새 배포를 선택합니다. 배포 상태가 *실행 중*으로 표시되어야 합니다. 환경이 스테이징 환경이므로 **도메인 할당/할당 해제** 단추는 회색으로 표시되어야 합니다.

1. **개요** 창에 **테스트 끝점이**표시됩니다. 복사하여 새 브라우저 창에 붙여넣은 경우 새 PiggyMetrics 페이지가 표시됩니다.

>[!TIP]
> * CSS 파일이 올바르게 로드되었는지 확인하기 위해 테스트 끝점이 슬래시(/)로 끝나는지 확인합니다.  
> * 브라우저에서 페이지를 보기 위해 로그인 자격 증명을 입력해야 하는 경우, [URL 디코드](https://www.urldecoder.org/)를 사용하여 테스트 엔드포인트를 디코드합니다. URL 디코드는 "https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green" 형식의 URL을 반환합니다.  이 양식을 사용하여 끝점에 액세스합니다.

>[!NOTE]    
> 구성 서버 설정은 스테이징 환경과 프로덕션 모두에 적용됩니다. 예를 들어 구성 서버의 앱`server.servlet.context-path`게이트웨이에 대한 컨텍스트 경로()를 *일부 경로로*설정하면 녹색\<배포 경로가\<"https://\<사용자 이름>: 암호>@ 클러스터 이름>.azureapps.io/gateway/green/somepath/..."로 변경됩니다.
 
 이 시점에서 공용 앱 게이트웨이를 방문하는 경우 새 변경 없이 이전 페이지가 표시됩니다.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>녹색 배포를 프로덕션 환경으로 설정

1. 스테이징 환경에서 변경 사항을 확인한 후 프로덕션 환경에 푸시할 수 있습니다. 배포 **관리로**돌아가고 **게이트웨이** 응용 프로그램 확인란을 선택합니다.

2. **배포 설정을**선택합니다.
3. 프로덕션 **배포** 목록에서 **녹색을**선택한 다음 **적용을**선택합니다.
4. 게이트웨이 애플리케이션 **개요** 페이지로 이동합니다. 게이트웨이 응용 프로그램에 대한 도메인을 이미 할당한 경우 **URL이 개요** 창에 표시됩니다. 수정된 PiggyMetrics 페이지를 보려면 URL을 선택하고 사이트로 이동합니다.

>[!NOTE]
> 녹색 배포를 프로덕션 환경으로 설정한 후 이전 배포는 스테이징 배포가 됩니다.

## <a name="modify-the-staging-deployment"></a>스테이징 배포 수정

변경 사항이 마음에 드시지 않으면 Azure CLI를 사용하여 응용 프로그램 코드를 수정하고 새 jar 패키지를 빌드한 다음 녹색 배포에 업로드할 수 있습니다.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>스테이징 배포 삭제

Azure 포트에서 스테이징 배포를 삭제하려면 스테이징 배포 페이지로 이동한 다음 **삭제** 단추를 선택합니다.

또는 다음 명령을 실행하여 Azure CLI에서 스테이징 배포를 삭제합니다.

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
