---
title: GitHub 작업을 사용 하 여 리소스 관리자 템플릿 배포
description: GitHub 작업을 사용 하 여 Azure Resource Manager 템플릿을 배포 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 5fe147a9c42e83d5e644b0c08dfa67de88ec05c0
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82875195"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>GitHub 작업을 사용 하 여 Azure Resource Manager 템플릿 배포

[Github 작업](https://help.github.com/en/actions) 을 사용 하면 AZURE RESOURCE MANAGER (ARM) 템플릿이 저장 된 github 리포지토리에서 사용자 지정 소프트웨어 개발 수명 주기 워크플로를 직접 만들 수 있습니다. [워크플로](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) 는 yaml 파일에 의해 정의 됩니다. 워크플로에는 개별 작업을 수행 하는 일련의 단계가 포함 된 작업을 하나 이상 포함 합니다. 단계는 명령을 실행 하거나 작업을 사용할 수 있습니다. 사용자 고유의 작업을 만들거나 [GitHub 커뮤니티](https://github.com/marketplace?type=actions) 에서 공유 하는 작업을 사용 하 고 필요에 따라 사용자 지정할 수 있습니다. 이 문서에서는 [Azure CLI 작업](https://github.com/marketplace/actions/azure-cli-action) 을 사용 하 여 리소스 관리자 템플릿을 배포 하는 방법을 보여 줍니다.

Azure CLI 작업에는 두 가지 종속 작업이 있습니다.

- **[Checkout](https://github.com/marketplace/actions/checkout)**: 워크플로에서 지정 된 리소스 관리자 템플릿에 액세스할 수 있도록 리포지토리를 체크 아웃 합니다.
- **[Azure 로그인](https://github.com/marketplace/actions/azure-login)**: azure 자격 증명을 사용 하 여 로그인

리소스 관리자 템플릿을 배포 하는 기본 워크플로에는 세 가지 단계가 있습니다.

1. 템플릿 파일을 체크 아웃 합니다.
2. Azure에 로그인합니다.
3. 리소스 관리자 템플릿 배포

## <a name="prerequisites"></a>사전 요구 사항

리소스 관리자 템플릿 및 워크플로 파일을 저장 하려면 GitHub 리포지토리가 필요 합니다. 하나를 만들려면 [새 리포지토리 만들기](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository)를 참조 하세요.

## <a name="configure-deployment-credentials"></a>배포 자격 증명 구성

Azure 로그인 작업은 서비스 주체를 사용 하 여 Azure에 대해 인증 합니다. CI/CD 워크플로의 주 서버에는 일반적으로 Azure 리소스를 배포 하기 위한 기본 제공 참가자 권한이 필요 합니다.

다음 Azure CLI 스크립트는 Azure 리소스 그룹에 대 한 참가자 권한을 사용 하 여 Azure 서비스 주체를 생성 하는 방법을 보여 줍니다. 이 리소스 그룹은 워크플로가 리소스 관리자 템플릿에 정의 된 리소스를 배포 하는 위치입니다.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

스크립트에서 **$projectName** 및 **$location** 의 값을 사용자 지정 합니다. 리소스 그룹 이름은 **rg**가 추가된 프로젝트 이름입니다. 워크플로에서 리소스 그룹 이름을 지정 해야 합니다.

이 스크립트는 다음과 유사한 JSON 개체를 출력 합니다.

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

JSON 출력을 복사 하 여 GitHub 리포지토리 내에 GitHub 비밀로 저장 합니다. 아직 리포지토리가 없는 경우 [필수 구성 요소](#prerequisites) 를 참조 하세요.

1. GitHub 리포지토리에서 **설정** 탭을 선택 합니다.
1. 왼쪽 메뉴에서 **비밀** 을 선택 합니다.
1. 다음 값을 입력합니다.

    - **이름**: AZURE_CREDENTIALS
    - **값**: (JSON 출력 붙여넣기)
1. **비밀 추가**를 선택 합니다.

워크플로에서 비밀 이름을 지정 해야 합니다.

## <a name="add-resource-manager-template"></a>리소스 관리자 템플릿 추가

GitHub 리포지토리에 리소스 관리자 템플릿을 추가 합니다. 없는 경우 다음 템플릿을 사용할 수 있습니다. 템플릿은 저장소 계정을 만듭니다.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

파일을 리포지토리의 어디에 나 배치할 수 있습니다. 다음 섹션의 workflow 샘플에서는 템플릿 파일의 이름이 **azuredeploy**인 것으로 가정 하 고, 리포지토리 루트의 **템플릿** 이라는 폴더에 저장 됩니다.

## <a name="create-workflow"></a>워크플로 만들기

워크플로 파일은 리포지토리의 루트에 있는 **github/workflow** 폴더에 저장 해야 합니다. 워크플로 파일 확장은 **.yml** 또는 **.yaml**중 하나일 수 있습니다.

워크플로 파일을 만든 다음 파일을 리포지토리로 푸시/업로드 하거나 다음 절차를 사용할 수 있습니다.

1. GitHub 리포지토리의 상단 메뉴에서 **작업** 을 선택 합니다.
1. **새 워크플로**를 선택 합니다.
1. **직접 워크플로 설정**을 선택 합니다.
1. **주 .yml**이외의 다른 이름을 선호 하는 경우 워크플로 파일의 이름을 바꿉니다. 예: **Deploystorageaccount .yml**.
1. Iisnode.yml 파일의 내용을 다음으로 바꿉니다.

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

    - **이름**: 워크플로의 이름입니다.
    - **설정**: 워크플로를 트리거하는 GitHub 이벤트의 이름입니다. Master 분기에 푸시 이벤트가 있는 경우 워크플로는 지정 된 두 파일 중 하나 이상을 수정 합니다. 두 파일은 워크플로 파일 및 템플릿 파일입니다.

        > [!IMPORTANT]
        > 두 파일 및 해당 경로가 서로 일치 하는지 확인 합니다.
    - **작업**: 워크플로 실행은 하나 이상의 작업으로 구성 됩니다. **배포-저장소-계정 템플릿**이라는 작업은 하나 뿐입니다.  이 작업에는 세 가지 단계가 있습니다.

        - **소스 코드를 체크 아웃**합니다.
        - **Azure에 로그인**합니다.

            > [!IMPORTANT]
            > 비밀 이름이 리포지토리에 저장 한 것과 일치 하는지 확인 합니다. [배포 자격 증명 구성](#configure-deployment-credentials)을 참조 하세요.
        - **ARM 템플릿을 배포**합니다. **ResourceGroupName**의 값을 바꿉니다.  [배포 자격 증명 구성](#configure-deployment-credentials)에서 Azure CLI 스크립트를 사용한 경우 생성 된 리소스 그룹 이름은 **rg** 가 추가 된 프로젝트 이름입니다. 템플릿 **위치**의 값을 확인 합니다.

1. **커밋 시작**을 선택 합니다.
1. **마스터 분기에 직접 커밋을**선택 합니다.
1. **새 파일 커밋** (또는 **변경 내용 커밋**)을 선택 합니다.

워크플로 파일이 나 업데이트 되는 템플릿 파일에 의해 트리거될 수 있도록 워크플로를 구성 했기 때문에 워크플로는 변경 내용을 커밋한 후 바로 시작 됩니다.

## <a name="check-workflow-status"></a>워크플로 상태 확인

1. **작업** 탭을 선택 합니다. **Create deployStorageAccount. yml** 워크플로가 나열 됩니다. 워크플로를 실행 하는 데 1-2 분이 소요 됩니다.
1. 워크플로를 선택 하 여 엽니다.
1. 왼쪽 메뉴에서 **배포-저장소-계정 템플릿** (작업 이름)을 선택 합니다. 작업 이름은 워크플로에서 정의 됩니다.
1. **ARM 템플릿 배포** (단계 이름)를 선택 하 여 확장 합니다. REST API 응답을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

템플릿을 만드는 과정을 안내 하는 단계별 자습서는 [자습서: 첫 번째 ARM 템플릿 만들기 및 배포](template-tutorial-create-first-template.md)를 참조 하세요.
