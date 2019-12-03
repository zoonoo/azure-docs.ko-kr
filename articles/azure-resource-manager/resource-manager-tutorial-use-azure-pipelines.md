---
title: Azure Pipelines를 사용한 연속 통합
description: Azure Resource Manager 템플릿을 지속적으로 빌드, 테스트 및 배포하는 방법을 알아봅니다.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 457d2a8868283eed2e211ff26ce77156eee68e01
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326404"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>자습서: Azure Pipelines를 사용한 Azure Resource Manager 템플릿의 지속적인 통합

Azure Pipelines를 사용하여 Azure Resource Manager 템플릿을 지속적으로 빌드하고 배포하는 방법을 알아봅니다.

Azure DevOps는 팀이 작업을 계획하고, 협업을 통해 코드를 개발하고, 애플리케이션을 빌드하여 배포할 수 있도록 지원하는 개발자 서비스를 제공합니다. 개발자는 Azure DevOps Services를 사용하여 클라우드에서 작업할 수 있습니다. Azure DevOps는 웹 브라우저 또는 IDE 클라이언트를 통해 액세스할 수 있는 통합 기능 세트를 제공합니다. Azure 파이프라인은 이러한 기능 중 하나입니다. Azure Pipelines는 모든 기능을 갖춘 CI(지속적인 통합) 및 CD(지속적인 업데이트) 서비스입니다. 선호하는 Git 공급자와 함께 작동하며 대부분의 주요 클라우드 서비스에 배포할 수 있습니다. 그런 다음, 코드를 빌드하고, 테스트하고, Microsoft Azure, Google Cloud Platform 또는 Amazon Web Services에 배포하는 프로세스를 자동화할 수 있습니다.

이 자습서는 Azure DevOps Services 및 Azure Pipelines를 처음 접하는 Azure Resource Manager 템플릿 개발자를 위해 설계되었습니다. 이미 GitHub 및 DevOps에 익숙한 분들은 [파이프라인 만들기](#create-a-pipeline)를 건너뛰어도 됩니다.

> [!NOTE]
> 프로젝트 이름을 선택합니다. 자습서를 진행할 때 **AzureRmPipeline**을 프로젝트 이름으로 바꿔야 합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * GitHub 리포지토리 준비
> * Azure DevOps 프로젝트 만들기
> * Azure 파이프라인 만들기
> * 파이프라인 배포 확인
> * 템플릿을 업데이트하고 다시 배포
> * 리소스 정리

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* 템플릿의 리포지토리를 만드는 데 사용할 **GitHub 계정**. 계정이 없는 경우 [무료로 만들 수 있습니다](https://github.com). GitHub 리포지토리 사용에 대한 자세한 내용은 [GitHub 리포지토리 빌드](/azure/devops/pipelines/repos/github)를 참조하세요.
* **Git를 설치**합니다. 이 자습서의 지침에서는 *Git Bash* 또는 *Git Shell*을 사용합니다. 지침은 [Git 설치]( https://www.atlassian.com/git/tutorials/install-git)를 참조하세요.
* **Azure DevOps 조직**. 조직이 없는 경우 무료로 만들 수 있습니다. [조직 또는 프로젝트 컬렉션 만들기]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)를 참조하세요.
* Resource Manager 도구 확장이 포함된 Visual Studio Code. [Visual Studio Code를 사용하여 Azure Resource Manager 템플릿 만들기](./resource-manager-tools-vs-code.md)를 참조하세요.

## <a name="prepare-a-github-repository"></a>GitHub 리포지토리 준비

GitHub는 Resource Manager 템플릿을 비롯한 프로젝트 소스 코드를 저장하는 데 사용됩니다. 지원되는 다른 리포지토리에 대한 내용은 [Azure DevOps에서 지원하는 리포지토리](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types)를 참조하세요.

### <a name="create-a-github-repository"></a>GitHub 리포지토리 만들기

GitHub 계정이 없는 경우 [필수 조건](#prerequisites)을 참조하세요.

1. [GitHub](https://github.com)에 로그인합니다.
2. 오른쪽 위 모서리에서 계정 이미지를 선택한 다음, **해당 리포지토리**를 선택합니다.

    ![Azure Resource Manager Azure DevOps Azure Pipelines GitHub 리포지토리 만들기](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. 녹색 **새로 만들기** 단추를 선택합니다.
1. **리포지토리 이름**에서 리포지토리 이름을 입력합니다.  예: **AzureRmPipeline-repo**. **AzureRmPipeline**을 프로젝트 이름으로 바꿉니다. 이 자습서를 **공개**로 진행할 것인지 아니면 **프라이빗**으로 진행할 것인지 선택할 수 있습니다. 그런 다음, **리포지토리 만들기**를 선택합니다.
1. URL을 적어 둡니다. 리포지토리 URL은 다음과 같은 형식입니다.

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

이 리포지토리를 *원격 리포지토리*라고 합니다. 같은 프로젝트를 작업하는 각 개발자는 자신의 *로컬 리포지토리*를 복제하고, 변경 내용을 원격 리포지토리에 병합할 수 있습니다.

### <a name="clone-the-remote-repository"></a>원격 리포지토리 복제

1. Git Shell 또는 Git Bash를 엽니다.  [필수 조건](#prerequisites)을 참조하세요.
1. 현재 폴더가 **github**인지 확인합니다.
1. 다음 명령 실행:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    **[YourAccountName]** 을 해당 GitHub 계정 이름으로 바꾸고, **[YourGitHubRepositoryName]** 을 이전 절차에서 만든 리포지토리 이름으로 바꿉니다.

    다음 스크린샷은 예제를 보여 줍니다.

    ![Azure Resource Manager Azure DevOps Azure Pipelines GitHub bash 만들기](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

**CreateAzureStorage** 폴더는 템플릿이 저장되는 폴더입니다. **pwd** 명령은 폴더 경로를 보여줍니다. 경로는 다음 절차에 따라 템플릿을 저장하는 위치입니다.

### <a name="download-a-quickstart-template"></a>빠른 시작 템플릿 다운로드

템플릿을 만드는 대신 [빠른 시작 템플릿]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)을 다운로드할 수 있습니다. 이 템플릿은 Azure Storage 계정을 만듭니다.

1. Visual Studio Code를 엽니다. [필수 조건](#prerequisites)을 참조하세요.
2. 다음 URL을 사용하여 템플릿을 엽니다.

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. 파일을 **CreateAzureStorage** 폴더에 **azuredeploy.json**으로 저장합니다. 폴더 이름과 파일 이름은 파이프라인에서 사용되던 그대로 사용됩니다.  이러한 이름을 변경할 경우 파이프라인에서 사용되는 이름을 업데이트해야 합니다.

### <a name="push-the-template-to-the-remote-repository"></a>원격 리포지토리에 템플릿 푸시

azuredeploy.json 파일이 로컬 리포지토리에 추가되었습니다. 다음으로, 템플릿을 원격 리포지토리에 업로드합니다.

1. *Git Shell* 또는 *Git Bash*가 열려 있지 않으면 지금 엽니다.
1. 디렉터리를 로컬 리포지토리의 CreateAzureStorage 폴더로 변경합니다.
1. **azuredeploy.json** 파일이 이 폴더에 있는지 확인합니다.
1. 다음 명령 실행:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    LF에 대한 경고가 발생할 수 있습니다. 경고를 무시해도 됩니다. **마스터**는 마스터 분기입니다.  일반적으로 각 업데이트에 대한 분기를 만듭니다. 이 자습서에서는 간단하게 마스터 분기를 직접 사용하겠습니다.
1. 브라우저에서 GitHub 리포지토리를 찾습니다.  URL은 **https://github.com/ [YourAccountName]/[YourGitHubRepository]** 입니다. **CreateAzureStorage** 폴더와 그 안에 있는 **Azuredeploy.json** 파일이 보일 것입니다.

지금까지 GitHub 리포지토리를 만들고, 리포지토리에 템플릿을 업로드했습니다.

## <a name="create-a-devops-project"></a>DevOps 프로젝트 만들기

다음 절차를 진행하려면 DevOps 조직이 필요합니다.  조직이 없는 경우 [필수 조건](#prerequisites)을 참조하세요.

1. [Azure DevOps](https://dev.azure.com)에 로그인합니다.
1. 왼쪽에서 DevOps 조직을 선택합니다.

    ![Azure Resource Manager Azure DevOps Azure Pipelines Azure DevOps 프로젝트 만들기](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. **프로젝트 만들기**를 선택합니다. 프로젝트가 하나도 없는 경우 [프로젝트 만들기] 페이지가 자동으로 열립니다.
1. 다음 값을 입력합니다.

    * **프로젝트 이름**: 프로젝트 이름을 입력합니다. 자습서를 시작할 때 선택한 프로젝트 이름을 사용해도 됩니다.
    * **버전 제어**: **Git**를 선택합니다. **버전 제어**를 보려면 **고급**을 확장해야 할 수도 있습니다.

    그 외의 속성은 기본값을 사용합니다.
1. **프로젝트 만들기**를 선택합니다.

Azure에 프로젝트를 배포하는 데 사용되는 서비스 연결을 만듭니다.

1. 왼쪽 메뉴의 맨 아래에서 **프로젝트 설정**을 선택합니다.
1. **파이프라인** 아래에서 **서비스 연결**을 선택합니다.
1. **새 서비스 연결**을 선택한 다음, **AzureResourceManager**를 선택합니다.
1. 다음 값을 입력합니다.

    * **연결 이름**: 연결 이름을 입력합니다. 예: **AzureRmPipeline-conn**. 이 이름을 적어 둡니다. 파이프라인을 만들 때 이 이름이 필요합니다.
    * **범위 수준**: **Subscription**을 선택합니다.
    * **구독**: 해당 구독을 선택합니다.
    * **리소스 그룹**: 비워 둠
    * **모든 파이프라인에서 이 연결을 사용하도록 허용합니다**. (선택됨)
1. **확인**을 선택합니다.

## <a name="create-a-pipeline"></a>파이프라인 만들기

지금까지 다음 작업을 완료했습니다.  GitHub 및 DevOps에 익숙해서 이전 섹션을 건너뛴 경우 다음 작업을 완료해야만 계속 진행할 수 있습니다.

- GitHub 리포지토리를 만들고, 이 리포지토리의 **CreateAzureStorage** 폴더에 [이 템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)을 저장합니다.
- DevOps 프로젝트를 만들고, Azure Resource Manager 서비스 연결을 만듭니다.

템플릿을 배포하는 단계를 사용하여 파이프라인을 만들려면 다음을 수행합니다.

1. 왼쪽 메뉴에서 **파이프라인**을 선택합니다.
1. **새 파이프라인**을 선택합니다.
1. **연결** 탭에서 **GitHub**를 선택합니다. GitHub 자격 증명을 입력하라는 메시지가 표시되면 입력하고 그 다음 지침을 따릅니다. 다음 화면이 표시되면 **리포지토리만 선택**을 선택하고, 리포지토리가 목록에 있는지 확인한 다음, **승인 및 설치**를 선택합니다.

    ![Azure Resource Manager Azure DevOps Azure Pipelines 리포지토리만 선택](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. **선택** 탭에서 리포지토리를 선택합니다.  기본 이름은 **[YourAccountName]/[YourGitHubRepositoryName]** 입니다.
1. **구성** 탭에서 **시작 파이프라인**을 선택합니다. 두 스크립트 단계가 있는 **azure-pipelines.yml** 파이프라인 파일이 표시됩니다.
1. **steps** 섹션을 다음 YAML로 바꿉니다.

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    다음과 비슷하게 보일 것입니다.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    다음과 같이 변경합니다.

    * **deloymentScope**: `Management Group`, `Subscription` 및 `Resource Group` 옵션에서 배포 범위를 선택합니다. 이 자습서에서는 **리소스 그룹**을 사용합니다. 범위에 대해 자세히 알아보려면 [배포 범위](./resource-group-template-deploy-rest.md#deployment-scope)를 참조하세요.
    * **ConnectedServiceName**: 이전에 만든 서비스 연결 이름을 지정합니다.
    * **SubscriptionName**:  대상 구독 ID를 지정합니다.
    * **작업**: **리소스 그룹 만들기 또는 업데이트** 작업은 2가지 작업을 수행합니다. 1. 새 리소스 그룹 이름이 제공되면 리소스 그룹을 만듭니다. 2. 지정된 템플릿을 배포합니다.
    * **resourceGroupName**: 새 리소스 그룹 이름을 지정합니다. 예: **AzureRmPipeline-rg**.
    * **위치**: 리소스 그룹의 위치를 지정합니다.
    * **templateLocation**: **연결된 아티팩트**를 지정하면 작업은 연결된 리포지토리에서 직접 템플릿 파일을 찾습니다.
    * **csmFile**은 템플릿 파일의 경로입니다. 템플릿에 정의된 모든 매개 변수에는 기본값이 있으므로 템플릿 매개 변수 파일을 지정할 필요가 없습니다.

    작업에 대한 자세한 내용은 [Azure Resource Group 배포 작업](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment) 및 [Azure Resource Manager 템플릿 배포 작업](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)을 참조하세요.
1. **저장 및 실행**을 선택합니다.
1. **저장 및 실행**을 다시 선택합니다. 연결된 리포지토리에 YAML 파일의 복사본이 저장됩니다. 리포지토리로 이동하면 YAML 파일을 볼 수 있습니다.
1. 파이프라인이 성공적으로 실행되는지 확인합니다.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>배포 확인

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 리소스 그룹을 엽니다. 이름은 파이프라인 YAML 파일에서 지정한 이름입니다.  스토리지 계정이 하나 만들어진 것이 보입니다.  스토리지 계정 이름은 **store**로 시작합니다.
1. 스토리지 계정 이름을 선택하여 엽니다.
1. **속성**을 선택합니다. **복제**는 **LRS(로컬 중복 스토리지)** 입니다.

    ![Azure Resource Manager Azure DevOps Azure Pipelines 포털 확인](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>업데이트 및 다시 배포

템플릿을 업데이트하고 변경 내용을 원격 리포지토리에 푸시하면 파이프라인이 자동으로 리소스(이 예에서는 스토리지 계정)를 업데이트합니다.

1. Visual Studio Code에서 로컬 리포지토리에 있는 **azuredeploy.json** 파일을 엽니다.
1. **storageAccountType**의 **defaultValue**를 **Standard_GRS**로 업데이트합니다. 다음 스크린샷을 참조하세요.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml 업데이트](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. 변경 내용을 저장합니다.
1. Git Bash/Shell에서 다음 명령을 실행하여 원격 리포지토리에 변경 내용을 푸시합니다.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    첫 번째 명령은 로컬 리포지토리를 원격 리포지토리와 동기화합니다. YAML 파일이 원격 리포지토리에 추가된 파이프라인을 기억하세요.

    원격 리포지토리의 마스터 분기가 업데이트되었으므로 파이프라인이 다시 실행됩니다.

변경 내용을 확인하려면 스토리지 계정의 복제 속성을 확인하면 됩니다.  [배포 확인](#verify-the-deployment)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

GitHub 리포지토리 및 Azure DevOps 프로젝트를 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Resource Manager 템플릿을 배포하는 Azure DevOps 파이프라인을 만듭니다. 여러 지역에 Azure 리소스를 배포하는 방법 및 안전한 배포 사례를 사용하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [안전한 배포 사례 사용](./deployment-manager-tutorial.md)
