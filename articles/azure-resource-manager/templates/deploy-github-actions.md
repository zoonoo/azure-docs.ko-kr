---
title: GitHub Actions를 사용하여 Resource Manager 템플릿 배포
description: GitHub Actions를 사용하여 Azure Resource Manager 템플릿을 배포하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: adb0b9d9a7da19c45904a5d222573e1880915b12
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841685"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>GitHub Actions를 사용하여 Azure Resource Manager 템플릿 배포

[Github 작업](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) 은 코드를 저장 하 고 끌어오기 요청 및 문제에 대해 공동으로 작업 하는 것과 동일한 장소에서 소프트웨어 개발 워크플로를 자동화 하는 github의 기능 모음입니다.

[Azure Resource Manager 템플릿 배포 작업](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) 을 사용 하 여 Azure에 리소스 관리자 템플릿 배포를 자동화할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.  
    - 리소스 관리자 템플릿 및 워크플로 파일을 저장할 GitHub 리포지토리입니다. 리포지토리를 만들려면 [새 리포지토리 만들기](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository)를 참조하세요.


## <a name="workflow-file-overview"></a>워크플로 파일 개요

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

이 파일에는 다음 두 가지 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체를 정의합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**배포** | 1. 리소스 관리자 템플릿을 배포 합니다. |

## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성


[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) 명령을 사용하여 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. 이 명령은 Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 실행합니다.

아직 없는 경우 리소스 그룹을 만듭니다. 

```azurecli-interactive
    az group create -n {MyResourceGroup}
```

자리 표시자를 `myApp` 응용 프로그램의 이름으로 바꿉니다. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

위의 예제에서 자리 표시자를 구독 ID 및 리소스 그룹 이름으로 바꿉니다. 출력은 아래와 같이 App Service 앱에 대 한 액세스를 제공 하는 역할 할당 자격 증명을 포함 하는 JSON 개체입니다. 나중에이 JSON 개체를 복사 합니다. ,, 및 값이 포함 된 섹션만 `clientId` 필요 `clientSecret` `subscriptionId` `tenantId` 합니다. 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 항상 최소한의 액세스 권한을 부여하는 것이 좋습니다. 이전 예제의 범위는 리소스 그룹으로 제한 됩니다.



## <a name="configure-the-github-secrets"></a>GitHub 비밀 구성

Azure 자격 증명, 리소스 그룹 및 구독에 대 한 암호를 만들어야 합니다. 

1. [GitHub](https://github.com/)에서 리포지토리를 찾습니다.

1. **설정 > 비밀 > 새 비밀** 을 차례로 선택합니다.

1. Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. 비밀 이름을 `AZURE_CREDENTIALS`로 지정합니다.

1. 이라는 다른 암호 `AZURE_RG` 를 만듭니다. 비밀의 값 필드에 리소스 그룹의 이름을 추가 합니다 (예: `myResourceGroup` ). 

1. 이라는 추가 암호를 만듭니다 `AZURE_SUBSCRIPTION` . 암호의 값 필드에 구독 ID를 추가 합니다 (예: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ). 

## <a name="add-resource-manager-template"></a>Resource Manager 템플릿 추가

GitHub 리포지토리에 리소스 관리자 템플릿을 추가 합니다. 이 템플릿은 저장소 계정을 만듭니다.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

파일을 리포지토리의 어디에나 배치할 수 있습니다. 다음 섹션의 workflow 샘플에서는 템플릿 파일의 이름이 **azuredeploy.js설정** 된 것으로 가정 하 고 리포지토리의 루트에 저장 됩니다.

## <a name="create-workflow"></a>워크플로 만들기

워크플로 파일은 리포지토리의 루트에 있는 **github/** workflow 폴더에 저장 해야 합니다. 워크플로 파일 확장명은 **.yml** 또는 **.yaml** 일 수 있습니다.

1. GitHub 리포지토리의 상단 메뉴에서 **작업** 을 선택합니다.
1. **새 워크플로** 를 선택합니다.
1. **워크플로 직접 설정** 을 선택합니다.
1. **main.yml** 이 아닌 다른 이름을 선호하는 경우 워크플로 파일의 이름을 바꿉니다. 예: **deployStorageAccount.yml**.
1. yml 파일의 내용을 다음으로 바꿉니다.

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@master

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
     
          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS 
        
          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > ARM 배포 작업에서 대신 JSON 형식 매개 변수 파일을 지정할 수 있습니다 (예: `.azuredeploy.parameters.json` ).  

    워크플로 파일의 첫 번째 섹션에는 다음이 포함 됩니다.

    - **name**: 워크플로의 이름입니다.
    - **on**: 워크플로를 트리거하는 GitHub 이벤트의 이름입니다. 마스터 분기에 지정된 두 파일 중 하나 이상을 수정하는 푸시 이벤트가 있는 경우 워크플로가 트리거됩니다. 두 파일은 워크플로 파일 및 템플릿 파일입니다.

1. **커밋 시작** 을 선택합니다.
1. **마스터 분기에 직접 커밋** 을 선택합니다.
1. **새 파일 커밋**(또는 **변경 내용 커밋**)을 선택합니다.

업데이트되는 워크플로 파일이나 템플릿 파일에 의해 트리거되도록 워크플로를 구성했기 때문에 변경 내용을 커밋하면 워크플로가 바로 시작됩니다.

## <a name="check-workflow-status"></a>워크플로 상태 확인

1. **작업** 탭을 선택 합니다. **Create deployStorageAccount** 가 표시 됩니다. 워크플로를 실행 하는 데 1-2 분이 소요 됩니다.
1. 워크플로를 선택하여 엽니다.
1. 메뉴에서 **ARM 배포 실행** 을 선택 하 여 배포를 확인 합니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹 및 리포지토리가 더 이상 필요 하지 않은 경우 리소스 그룹 및 GitHub 리포지토리를 삭제 하 여 배포 된 리소스를 정리 합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [첫 번째 ARM 템플릿 만들기](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
