---
title: "Jenkins 및 Azure Container Instances를 사용하여 Azure에서 프로젝트 빌드"
description: "Jenkins용 Azure 컨테이너 에이전트 플러그 인을 사용하여 Azure Container Instances를 통해 Azure에서 프로젝트를 빌드하는 방법 알아보기"
services: multiple
documentationcenter: 
author: tomarcher
manager: rloutlaw
editor: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 04a60bf021ec6e265a3880264386ad32ec7e8177
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2018
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Jenkins 및 Azure Container Instances를 사용하여 Azure에서 프로젝트 빌드

Azure Container Instances를 사용하면 가상 머신을 프로비전하거나 상위 수준 서비스를 도입하지 않고도 쉽게 시작하고 실행할 수 있습니다. Azure Container Instances는 필요한 용량에 따라 초당 청구를 제공하므로 빌드 수행과 같은 임시 워크로드에 적합한 옵션입니다.

다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure에서 Jenkins 서버 설치 및 구성
> * Jenkins용 Azure 컨테이너 에이전트 플러그 인 설치 및 구성
> * Azure Container Instances를 사용하여 [Spring PetClinic 샘플 응용 프로그램](https://github.com/spring-projects/spring-petclinic) 빌드

## <a name="prerequisites"></a>필수 구성 요소

- **Azure 구독** - Azure 구입 옵션에 대해 알아보려면 [Azure 구입 방법](https://azure.microsoft.com/pricing/purchase-options/) 또는 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- **Azure CLI 2.0 또는 Azure Cloud Shell** - Azure 명령을 입력할 다음 제품 중 하나를 설치합니다.

    - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) - 명령 또는 터미널 창에서 Azure 명령을 실행할 수 있습니다.
    - [Azure Cloud Shell](/azure/cloud-shell/quickstart.md) - 브라우저 기반 셸 환경입니다. Cloud Shell은 Azure 관리 작업을 사용하여 빌드된 브라우저 기반 명령줄 환경에 액세스할 수 있습니다.

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Jenkins Marketplace 이미지를 사용하여 Azure에 Jenkins 서버 설치

Jenkins는 단일 Jenkins 설치가 많은 수의 프로젝트를 호스트하거나 빌드 또는 테스트에 필요한 다양한 환경을 제공할 수 있도록 하나 이상의 에이전트에 작업을 위임하는 모델을 지원합니다. 이 섹션의 단계는 Azure에서 Jenkins 서버를 설치 및 구성하는 과정을 안내합니다.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Azure에서 실행 중인 Jenkins 서버에 연결

Azure에 Jenkins를 설치한 후 Jenkins에 연결해야 합니다. 다음 단계에서는 Azure에서 실행 중인 Jenkins VM에 대한 SSH 연결을 설정하는 과정을 안내합니다. 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Jenkins DNS 업데이트

Jenkins는 다시 자신을 가리키는 링크를 만들 경우 자신의 URL을 알고 있어야 합니다. 예를 들어 Jenkins가 빌드 결과에 대한 직접 링크를 포함하는 메일을 보내는 경우 URL을 사용해야 합니다. 

이 섹션에서는 Jenkins URL을 설정하는 과정을 안내합니다.

1. 브라우저에서 `http://localhost:8080`의 Jenkins 대시보드로 이동합니다.

1. **Jenkins 관리**를 선택합니다.

    ![Jenkins 대시보드에서 Jenkins 옵션 관리](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. **시스템 구성**을 선택합니다.

    ![Jenkins 대시보드에서 Jenkins 플러그 인 옵션 관리](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. **Jenkins 위치** 아래에 Jenkins 서버의 URL을 입력합니다.

1. **저장**을 선택합니다.

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>JNLP(Java Network Launch Protocol)를 허용하도록 Jenkins 업데이트

Jenkins 에이전트는 JNLP(Java Network Launch Protocol)를 통해 Jenkins 서버와 연결합니다. 이 섹션에서는 Jenkins 서버와 통신할 때 사용할 JNLP 에이전트의 포트를 지정하는 방법을 설명합니다.

1. Jenkins 대시보드에서 **Jenkins 관리**를 선택합니다.

    ![Jenkins 대시보드에서 Jenkins 옵션 관리](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. **전역 보안 구성**을 선택합니다.

    ![Jenkins 대시보드에서 전역 보안 구성](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. **에이전트**에서 **고정**을 선택하고 포트를 입력합니다. 스크린샷에는 12345의 포트 값이 예로 표시됩니다. 사용자 환경에 적합한 포트를 지정해야 합니다.

    ![JNLP를 허용하도록 Jenkins 전역 보안 설정 업데이트](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. **저장**을 선택합니다.

1. Azure CLI 2.0 또는 Cloud Shell을 통해 다음 명령을 입력하여 Jenkins 네트워크 보안 그룹에 대한 인바운드 규칙을 만듭니다.

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Azure 서비스 주체를 만들고 Jenkins 자격 증명에 추가

Azure에 배포하려면 Azure 서비스 주체가 필요합니다. 다음 단계에서는 서비스 주체를 만들고(아직 없는 경우) 서비스 주체로 Jenkins를 업데이트하는 프로세스를 안내합니다.

1. 이미 서비스 주체가 있고 해당 구독 ID, 테넌트, appId 및 암호를 아는 경우 이 단계를 건너뛸 수 있습니다. 보안 주체를 만들어야 하는 경우 [Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)를 참조하세요. 주체를 만드는 동안 구독 ID, 테넌트, appId 및 암호의 값을 기록해 둡니다.

1. **자격 증명**을 선택합니다.

    ![Jenkins 대시보드에서 자격 증명 옵션 관리](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. **자격 증명**에서 **시스템**을 선택합니다.

    ![Jenkins 대시보드에서 시스템 자격 증명 옵션 관리](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. **전역 자격 증명(제한 없음)**을 선택합니다.

    ![Jenkins 대시보드에서 전역 시스템 자격 증명 옵션 관리](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. **일부 자격 증명 추가**를 선택합니다.

    ![Jenkins 대시보드에서 자격 증명 추가](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. **종류** 드롭다운 목록에서 **Microsoft Azure 서비스 주체**를 선택하여 페이지에 서비스 주체 추가에 관련된 필드를 표시합니다. 그러고 나서 다음과 같이 요청된 값을 제공합니다.

    - **범위** - **전역(Jenkins, 노드, 항목, 모든 하위 항목 등)**에 대한 옵션을 선택합니다.
    - **구독 ID** - `az account set`을 실행할 때 지정한 Azure 구독 ID를 사용합니다.
    - **클라이언트 ID** - `az ad sp create-for-rbac`에서 반환된 `appId` 값을 사용합니다.
    - **클라이언트 암호** - `az ad sp create-for-rbac`에서 반환된 `password` 값을 사용합니다.
    - **테넌트 ID** - `az ad sp create-for-rbac`에서 반환된 `tenant` 값을 사용합니다.
    - **Azure 환경** - `Azure`를 선택합니다.
    - **ID** - `myTestSp`를 입력합니다. 이 값은 이 학습서의 뒷부분에서 다시 사용됩니다.
    - **설명** - (선택 사항) 이 주체에 대한 설명 값을 입력합니다.

    ![Jenkins 대시보드에서 새 서비스 주체 속성 지정](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. 주체를 정의하는 정보를 입력하면 선택적으로 **서비스 주체 확인**을 선택하여 모든 것이 올바르게 작동하는지 확인할 수 있습니다. 서비스 주체가 올바르게 정의된 경우 “Microsoft Azure 서비스 주체를 성공적으로 확인했습니다.”라는 메시지가 **설명** 필드 아래에 표시됩니다.

1. 완료되면 **확인**을 선택하여 Jenkins에 주체를 추가합니다. Jenkins 대시보드의 **전역 자격 증명** 페이지에 새로 추가된 주체가 표시됩니다.

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Azure Container Instances에 대한 Azure 리소스 그룹 만들기

Azure Container Instances는 Azure 리소스 그룹에 배치되어야 합니다. Azure 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다.

Azure CLI 2.0 또는 Cloud Shell을 통해 다음 명령을 입력하여 `eastus` 위치에 리소스 그룹 `JenkinsAciResourceGroup`을 만듭니다.

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

완료되면 `az group create` 명령은 다음 예제와 유사한 결과를 표시합니다.

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Jenkins용 Azure 컨테이너 에이전트 플러그 인 설치

Azure 컨테이너 에이전트 플러그 인을 이미 설치한 경우 이 섹션을 건너뛸 수 있습니다.

Jenkins 에이전트에 대해 Azure 리소스 그룹을 만든 후 다음 단계에서는 Azure 컨테이너 에이전트 플러그 인을 설치하는 방법을 설명합니다.

1. Jenkins 대시보드에서 **Jenkins 관리**를 선택합니다.

    ![Jenkins 대시보드에서 Jenkins 옵션 관리](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. **플러그 인 관리**를 선택합니다.

    ![Jenkins 대시보드에서 Jenkins 플러그 인 옵션 관리](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. **사용 가능**을 선택합니다.

    ![Jenkins 대시보드에서 사용 가능한 Jenkins 플러그 인 옵션 보기](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. **필터** 텍스트 상자에 `Azure Container Agents`를 입력합니다. (텍스트를 입력하면 목록이 필터링됩니다.)

    ![Jenkins 대시보드에서 사용 가능한 Jenkins 플러그 인 필터링](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. **Azure 컨테이너 에이전트** 플러그 인 옆에 있는 확인란과 설치 옵션 중 하나를 선택합니다. 이 데모의 경우 **다시 시작하지 않고 설치** 옵션을 선택했습니다.

    ![Jenkins 대시보드에서 Azure 컨테이너 에이전트 플러그 인 설치](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  원하는 플러그 인을 설치하는 옵션을 선택한 후 Jenkins 대시보드는 설치 중인 항목의 상태를 자세히 설명하는 페이지를 표시합니다.

    ![Jenkins 대시보드에서 Azure 컨테이너 에이전트 플러그 인 설치하는 작업의 설치 상태](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Jenkins 대시보드의 주 페이지를 반환하려면 **상위 페이지로 돌아가기**를 선택합니다.

## <a name="configure-the-azure-container-agents-plugin"></a>Azure 컨테이너 에이전트 플러그 인 구성

Azure 컨테이너 에이전트 플러그 인이 설치된 후 이 섹션에서는 Jenkins 대시보드 내에서 플러그 인을 구성하는 과정을 안내합니다.

1. Jenkins 대시보드에서 **Jenkins 관리**를 선택합니다.

    ![Jenkins 대시보드에서 Jenkins 옵션 관리](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. **시스템 구성**을 선택합니다.

    ![Jenkins 대시보드에서 Jenkins 플러그 인 옵션 관리](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. 페이지 아래쪽에서 **클라우드** 섹션을 찾고 **새 클라우드 추가** 드롭다운 목록에서 **Azure Container Instances**를 선택합니다.

    ![Jenkins 대시보드에서 새 클라우드 공급자 추가](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. **Azure Container Instances** 섹션에서 다음 값을 지정합니다.

    - **클라우드 이름** - (선택 사항이며 이 값은 기본적으로 자동 생성된 이름으로 설정됩니다.) 이 인스턴스의 이름을 지정합니다. 
    - **Azure 자격 증명** - 드롭다운 화살표를 선택한 후 이전에 만든 Azure 서비스 주체를 식별하는 `myTestSp` 항목을 선택합니다.
    - **리소스 그룹** - 드롭다운 화살표를 선택한 후 이전에 생성한 Azure Container Instances 리소스 그룹을 식별하는 `JenkinsAciResourceGroup` 항목을 선택합니다.

    ![Azure Container Instances 속성 정의](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. **컨테이너 템플릿 추가** 드롭다운 화살표를 선택하고 **Aci 컨테이너 템플릿**을 선택합니다.

1. **Aci 컨테이너 템플릿** 섹션에서 다음 값을 지정합니다.

    - **이름** - `ACI-container`를 입력합니다.
    - **레이블** - `ACI-container`를 입력합니다.
    - **Docker 이미지** - `cloudbees/jnlp-slave-with-java-build-tools`를 입력합니다.

    ![Azure Container Instances 이미지 속성 정의](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. **고급**을 선택합니다.

1. **보존 전략** 드롭다운 화살표를 선택하고 **컨테이너 유휴 보존 전략**을 선택합니다. 이 옵션을 선택하면 에이전트에서 새 작업이 실행되지 않고 지정된 유휴 시간이 경과할 때까지 Jenkins가 에이전트를 유지합니다.

    ![Azure Container Instances 보존 전략 정의](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. **저장**을 선택합니다.

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>Jenkins에서 Spring PetClinic 응용 프로그램 작업 만들기

다음 단계에서는 Jenkins 작업을 프리스타일 프로젝트로 만들어 Spring PetClinic 응용 프로그램을 빌드하는 과정을 안내합니다.

1. Jenkins 대시보드에서 **새 항목**을 선택합니다.

    ![Jenkins 대시보드의 새 항목 메뉴 옵션](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. 항목 이름에 `myPetClinicProject`를 입력하고 **프리스타일 프로젝트**를 선택합니다.

    ![Jenkins 대시보드의 새 프리스타일 프로젝트](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. **확인**을 선택합니다.

1. **일반** 탭을 선택하고 다음 값을 지정합니다.

    - **프로젝트를 실행할 수 있는 위치 제한** - 이 옵션을 선택하세요.
    - **레이블 식** - `ACI-container`를 입력합니다. 필드를 종료하면 이전 단계에서 만든 클라우드 구성에 의해 레이블이 처리된다는 확인 메시지가 표시됩니다.

    ![Jenkins 대시보드의 새 프리스타일 프로젝트에 대한 일반 설정](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. **소스 코드 관리** 탭을 선택하고 다음 값을 지정합니다.

    - **소스 코드 관리** - **Git**을 선택합니다.
    - **리포지토리 URL** - Spring PetClinic 샘플 응용 프로그램 GitHub 리포지토리에 대해 URL `https://github.com/spring-projects/spring-petclinic.git`을 입력합니다.

1. **빌드** 탭을 선택하고 다음 작업을 수행합니다.

    - **빌드 단계 추가** 드롭다운 화살표를 선택하고 **최상위 Maven 대상 호출**을 선택합니다.

    - **목표**의 경우 `package`를 입력합니다.

1. **저장**을 선택하여 새 프로젝트 정의를 저장합니다.

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>Jenkins에서 Spring PetClinic 응용 프로그램 작업 빌드

이제 프로젝트를 빌드할 수 있습니다. 이 섹션에서는 Jenkins 대시보드에서 프로젝트를 빌드하는 방법을 설명합니다.

1. Jenkins 대시보드에서 `myPetClinicProject`를 선택합니다.

    ![Jenkins 대시보드에서 빌드할 프로젝트를 선택합니다.](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. **지금 빌드**를 선택합니다. 

    ![Jenkins 대시보드에서 프로젝트를 빌드합니다.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. Jenkins에서 빌드를 시작하면 빌드가 큐에 추가됩니다. Azure 컨테이너 에이전트의 경우 Azure 컨테이너 에이전트가 시작되어 온라인 상태가 될 때까지 빌드를 실행할 수 없습니다. 이 시점까지 에이전트 이름 및 빌드가 보류 중임을 나타내는 메시지가 표시됩니다. (이 프로세스는 5분 정도 걸리지만 빌드에 에이전트를 처음 사용할 때만 필요합니다. 이 시점에는 에이전트가 온라인 상태이므로 후속 빌드가 훨씬 더 빠릅니다.)

    ![에이전트가 만들어지고 온라인 상태가 될 때까지 빌드는 큐에 있습니다.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    빌드가 시작되면 빌드가 실행 중임을 나타내는 바버 폴(barber pole) 진행률 표시줄이 표시됩니다.

    ![바버 폴 진행률 표시줄이 나타나면 빌드가 실행 중인 것입니다.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. 바버 폴 진행률 표시줄이 사라지면 빌드 번호 옆에 있는 화살표를 선택합니다. 상황에 맞는 메뉴에서 **콘솔 출력**을 선택합니다.

    ![[콘솔 로그] 메뉴 항목을 클릭하여 빌드 정보를 확인합니다.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. 빌드 프로세스에서 내보낸 콘솔 로그 정보입니다. 모든 빌드 정보(직접 만든 Azure 에이전트에서 원격으로 수행되는 빌드에 대한 정보 포함)를 보려면 **전체 로그**를 선택합니다.

    ![[전체 로그] 링크를 클릭하여 자세한 빌드 정보를 확인합니다.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    전체 로그에는 자세한 빌드 정보가 표시됩니다.

    ![전체 로그에는 자세한 빌드 정보가 표시됩니다.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. 빌드 처리 상태를 보려면 로그 아래쪽으로 스크롤합니다.

    ![빌드 처리는 빌드 로그의 아래쪽에 표시됩니다.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Azure 리소스 정리

이 자습서에서는 두 개의 Azure 리소스 그룹에 포함된 리소스를 만들었습니다. 
    - `JenkinsResourceGroup` - Jenkins 서버의 Azure 리소스를 포함합니다.
    - `JenkinsAciResourceGroup` - Jenkins 에이전트의 Azure 리소스를 포함합니다.
    
Azure 리소스 그룹의 리소스를 더 이상 사용할 필요가 없는 경우 다음과 같이 `az group delete` 명령을 사용하여 리소스 그룹을 삭제할 수 있습니다(&lt;resourceGroup> 자리 표시자를 삭제할 리소스 그룹의 이름으로 바꾸기).

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [최신 문서 및 샘플을 보려면 Azure 허브의 Jenkins를 방문하세요.](https://docs.microsoft.com/azure/jenkins/)