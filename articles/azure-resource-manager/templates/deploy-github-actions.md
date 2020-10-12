---
title: GitHub Actions를 사용하여 Resource Manager 템플릿 배포
description: GitHub Actions를 사용하여 Azure Resource Manager 템플릿을 배포하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/02/2020
ms.openlocfilehash: 313354499901bc69ec6e00f0ba7c385065cae615
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85854743"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>GitHub Actions를 사용하여 Azure Resource Manager 템플릿 배포

[GitHub Actions](https://help.github.com/en/actions)를 사용하면 ARM(Azure Resource Manager) 템플릿이 저장되는 GitHub 리포지토리에서 사용자 지정 소프트웨어 개발 수명 주기 워크플로를 직접 만들 수 있습니다. [워크플로](https://help.github.com/actions/reference/workflow-syntax-for-github-actions)는 YAML 파일로 정의됩니다. 워크플로에는 개별 작업을 수행하는 일련의 단계가 포함된 작업이 하나 이상 포함됩니다. 단계는 명령을 실행하거나 작업을 사용할 수 있습니다. 사용자 고유의 작업을 만들거나 [GitHub 커뮤니티](https://github.com/marketplace?type=actions)에서 공유한 작업을 사용하고 필요에 따라 사용자 지정할 수 있습니다. 이 문서에서는 [Azure CLI 작업](https://github.com/marketplace/actions/azure-cli-action)을 사용하여 Resource Manager 템플릿을 배포하는 방법을 보여 줍니다.

Azure CLI 작업에는 두 가지 종속 작업이 있습니다.

- **[체크 아웃](https://github.com/marketplace/actions/checkout)** : 워크플로가 지정된 Resource Manager 템플릿에 액세스할 수 있도록 리포지토리를 체크 아웃합니다.
- **[Azure 로그인](https://github.com/marketplace/actions/azure-login)** : Azure 자격 증명으로 로그인

Resource Manager 템플릿을 배포하는 기본 워크플로에는 세 가지 단계가 있습니다.

1. 템플릿 파일을 체크 아웃합니다.
2. Azure에 로그인합니다.
3. Resource Manager 템플릿 배포

## <a name="prerequisites"></a>사전 요구 사항

Resource Manager 템플릿 및 워크플로 파일을 저장하려면 GitHub 리포지토리가 필요합니다. 리포지토리를 만들려면 [새 리포지토리 만들기](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository)를 참조하세요.

## <a name="configure-deployment-credentials"></a>배포 자격 증명 구성

Azure 로그인 작업은 서비스 주체를 사용하여 Azure에 인증합니다. CI/CD 워크플로의 보안 주체에는 일반적으로 Azure 리소스를 배포하는 데 필요한 기본 제공 기여자 권한이 필요합니다.

다음 Azure CLI 스크립트는 Azure 리소스 그룹에 대한 기여자 권한을 사용하여 Azure 서비스 주체를 생성하는 방법을 보여 줍니다. 워크플로는 Resource Manager 템플릿에 정의된 리소스를 이 리소스 그룹에 배포합니다.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

스크립트에서 **$projectName** 및 **$location** 값을 사용자 지정합니다. 리소스 그룹 이름은 **rg**가 추가된 프로젝트 이름입니다. 워크플로에 리소스 그룹 이름을 지정해야 합니다.

이 스크립트는 다음과 유사한 JSON 개체를 출력합니다.

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

JSON 출력을 복사하여 GitHub 리포지토리 내에 GitHub 비밀로 저장합니다. 아직 리포지토리가 없는 경우 [필수 구성 요소](#prerequisites)를 참조하세요.

1. GitHub 리포지토리에서 **설정** 탭을 선택합니다.
1. 왼쪽 메뉴에서 **비밀**을 선택합니다.
1. 다음 값을 입력합니다.

    - **Name**: AZURE_CREDENTIALS
    - **값**: (JSON 출력 붙여넣기)
1. **비밀 추가**를 선택합니다.

워크플로에 비밀 이름을 지정해야 합니다.

## <a name="add-resource-manager-template"></a>Resource Manager 템플릿 추가

GitHub 리포지토리에 Resource Manager 템플릿을 추가합니다. 없는 경우 다음 템플릿을 사용할 수 있습니다. 이 템플릿은 스토리지 계정을 만듭니다.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

파일을 리포지토리의 어디에나 배치할 수 있습니다. 다음 섹션의 워크플로 샘플에서는 템플릿 파일의 이름이 **azuredeploy.json**이고, 리포지토리 루트의 **templates**라는 폴더에 저장된다고 가정합니다.

## <a name="create-workflow"></a>워크플로 만들기

워크플로 파일은 리포지토리의 루트에 있는 **github/** workflow 폴더에 저장 해야 합니다. 워크플로 파일 확장명은 **.yml** 또는 **.yaml**일 수 있습니다.

워크플로 파일을 만든 다음, 파일을 리포지토리로 푸시/업로드하거나 다음 절차를 수행할 수 있습니다.

1. GitHub 리포지토리의 상단 메뉴에서 **작업**을 선택합니다.
1. **새 워크플로**를 선택합니다.
1. **워크플로 직접 설정**을 선택합니다.
1. **main.yml**이 아닌 다른 이름을 선호하는 경우 워크플로 파일의 이름을 바꿉니다. 예: **deployStorageAccount.yml**.
1. yml 파일의 내용을 다음으로 바꿉니다.

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    워크플로 파일에는 다음과 같은 세 개의 섹션이 있습니다.

    - **name**: 워크플로의 이름입니다.
    - **on**: 워크플로를 트리거하는 GitHub 이벤트의 이름입니다. 마스터 분기에 지정된 두 파일 중 하나 이상을 수정하는 푸시 이벤트가 있는 경우 워크플로가 트리거됩니다. 두 파일은 워크플로 파일 및 템플릿 파일입니다.

        > [!IMPORTANT]
        > 두 파일과 해당 경로가 일치하는지 확인합니다.
    - **jobs**: 워크플로 실행은 하나 이상의 작업으로 구성됩니다. **deploy-storage-account-template**이라는 하나의 작업만 있습니다.  이 작업은 3단계로 구성됩니다.

        - **소스 코드 체크 아웃**
        - **Azure에 로그인**

            > [!IMPORTANT]
            > 비밀 이름이 리포지토리에 저장된 것과 일치하는지 확인합니다. [배포 자격 증명 구성](#configure-deployment-credentials)을 참조하세요.
        - **ARM 템플릿 배포**. **resourceGroupName**의 값을 바꿉니다.  [배포 자격 증명 구성](#configure-deployment-credentials)에서 Azure CLI 스크립트를 사용한 경우 생성된 리소스 그룹의 이름이 **rg**가 추가된 프로젝트 이름으로 지정됩니다. **templateLocation**의 값을 확인합니다.

1. **커밋 시작**을 선택합니다.
1. **마스터 분기에 직접 커밋**을 선택합니다.
1. **새 파일 커밋**(또는 **변경 내용 커밋**)을 선택합니다.

업데이트되는 워크플로 파일이나 템플릿 파일에 의해 트리거되도록 워크플로를 구성했기 때문에 변경 내용을 커밋하면 워크플로가 바로 시작됩니다.

## <a name="check-workflow-status"></a>워크플로 상태 확인

1. **작업** 탭을 선택합니다. **deployStorageAccount.yml 만들기** 워크플로가 나열된 것을 볼 수 있습니다. 워크플로를 실행하는 데 1-2분이 소요됩니다.
1. 워크플로를 선택하여 엽니다.
1. 왼쪽 메뉴에서 **deploy-storage-account-template**(작업 이름)을 선택합니다. 작업 이름은 워크플로에 정의됩니다.
1. **ARM 템플릿 배포**(단계 이름)를 선택하여 확장합니다. REST API 응답을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

템플릿 만들기 프로세스를 안내하는 단계별 자습서는 [자습서: 첫 번째 ARM 템플릿 만들기 및 배포](template-tutorial-create-first-template.md)를 참조하세요.
