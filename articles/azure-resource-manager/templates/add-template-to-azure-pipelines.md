---
title: Azure 파이프라인 및 템플릿이 있는 CI/CD
description: Visual Studio의 Azure 리소스 그룹 배포 프로젝트를 사용하여 리소스 관리자 템플릿을 배포하여 Azure 파이프라인에서 지속적인 통합을 설정하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 7617bf47595fce7baa533b0f7cc94a1803ddd349
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153457"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>ARM 템플릿을 Azure 파이프라인과 통합

Visual Studio는 ARM(Azure 리소스 관리자) 템플릿을 만들고 Azure 구독에 배포하기 위한 Azure 리소스 그룹 프로젝트를 제공합니다. 이 프로젝트를 Azure 파이프라인과 통합하여 지속적인 통합 및 지속적인 배포(CI/CD)할 수 있습니다.

Azure 파이프라인을 사용하여 템플릿을 배포하는 방법에는 두 가지가 있습니다.

* **Azure PowerShell 스크립트를 실행하는 작업을 추가합니다.** 이 옵션은 Visual Studio 프로젝트(Deploy-AzureResourceGroup.ps1)에 포함된 스크립트와 동일한 스크립트를 사용하기 때문에 개발 수명 주기 전반에 걸쳐 일관성을 제공한다는 장점이 있습니다. 스크립트는 프로젝트에서 리소스 관리자가 액세스할 수 있는 저장소 계정으로 아티팩트를 단계별로 작성합니다. 아티팩트는 연결된 템플릿, 스크립트 및 응용 프로그램 이별과 같은 프로젝트의 항목입니다. 그런 다음 스크립트가 템플릿을 배포합니다.

* **작업을 복사하고 배포하는 작업을 추가합니다.** 이 옵션은 프로젝트 스크립트에 대한 편리한 대안을 제공합니다. 파이프라인에서 두 개의 작업을 구성합니다. 한 작업은 아티팩트를 단계화하고 다른 작업은 템플릿을 배포합니다.

이 문서에서는 두 가지 방식을 모두 설명합니다.

## <a name="prepare-your-project"></a>프로젝트 준비

이 문서에서는 Visual Studio 프로젝트와 Azure DevOps 조직이 파이프라인을 만들 준비가 되었다고 가정합니다. 다음 단계는 준비가 되었는지 확인하는 방법을 보여 주며,

* Azure DevOps 조직이 있습니다. 없는 경우 무료로 만들 [수 있습니다.](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) 팀에 이미 Azure DevOps 조직이 있는 경우 사용하려는 Azure DevOps 프로젝트의 관리자인지 확인합니다.

* Azure 구독에 대한 [서비스 연결을](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) 구성했습니다. 파이프라인의 작업은 서비스 주체의 ID로 실행됩니다. 연결을 만드는 단계는 [DevOps 프로젝트 만들기를](template-tutorial-use-azure-pipelines.md#create-a-devops-project)참조하십시오.

* **Azure 리소스 그룹** 시작 템플릿에서 만든 Visual Studio 프로젝트가 있습니다. 이러한 유형의 프로젝트를 만드는 방법은 [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포를](create-visual-studio-deployment-project.md)참조하세요.

* Visual Studio 프로젝트가 [Azure DevOps 프로젝트에 연결되어](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)있습니다.

## <a name="create-pipeline"></a>파이프라인 만들기

1. 이전에 파이프라인을 추가하지 않은 경우 새 파이프라인을 만들어야 합니다. Azure DevOps 조직에서 파이프라인 및 **새 파이프라인을** **선택합니다.**

   ![새 파이프라인 추가](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. 코드가 저장되는 위치를 지정합니다. 다음 이미지는 **Azure Repos Git**을 선택하는 것을 보여 주며 있습니다.

   ![코드 소스 선택](./media/add-template-to-azure-pipelines/select-source.png)

1. 해당 원본에서 프로젝트에 대한 코드가 있는 리포지토리를 선택합니다.

   ![리포지토리 선택](./media/add-template-to-azure-pipelines/select-repo.png)

1. 만들 파이프라인 유형을 선택합니다. **스타터 파이프라인을**선택할 수 있습니다.

   ![파이프라인 선택](./media/add-template-to-azure-pipelines/select-pipeline.png)

Azure PowerShell 태스크 또는 복사 파일을 추가하고 작업을 배포할 준비가 되었습니다.

## <a name="azure-powershell-task"></a>Azure PowerShell 작업

이 섹션에서는 프로젝트에서 PowerShell 스크립트를 실행하는 단일 작업을 사용하여 연속 배포를 구성하는 방법을 보여 줍니다. 다음 YAML 파일은 [Azure PowerShell 작업을](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)만듭니다.

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus'
    azurePowerShellVersion: LatestVersion
```

작업을 `AzurePowerShell@3`로 설정하면 파이프라인은 AzureRM 모듈의 명령을 사용하여 연결을 인증합니다. 기본적으로 Visual Studio 프로젝트의 PowerShell 스크립트는 AzureRM 모듈을 사용합니다. [Az 모듈을](/powershell/azure/new-azureps-module-az)사용하도록 스크립트를 업데이트한 경우 작업을 `AzurePowerShell@4`로 설정합니다.

```yaml
steps:
- task: AzurePowerShell@4
```

의 `azureSubscription`경우 만든 서비스 연결의 이름을 제공합니다.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

의 `scriptPath`경우 파이프라인 파일에서 스크립트로의 상대 경로를 제공합니다. 리포지토리에서 경로를 볼 수 있습니다.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

아티팩트를 스테이지할 필요가 없는 경우 배포에 사용할 리소스 그룹의 이름과 위치를 전달하기만 하면 됩니다. Visual Studio 스크립트가 아직 존재하지 않는 경우 리소스 그룹을 만듭니다.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

아티팩트를 기존 저장소 계정으로 단계화해야 하는 경우 다음을 사용합니다.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

이제 작업을 만드는 방법을 이해하므로 파이프라인을 편집하는 단계를 살펴보겠습니다.

1. 파이프라인을 열고 내용을 YAML로 바꿉꿉을 바꿉꿉입니다.

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. **저장**을 선택합니다.

   ![파이프라인 저장](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. 커밋에 대한 메시지를 제공하고 **마스터에**직접 커밋합니다.

1. **저장을**선택하면 빌드 파이프라인이 자동으로 실행됩니다. 빌드 파이프라인에 대한 요약으로 돌아가 상태를 확인합니다.

   ![결과 보기](./media/add-template-to-azure-pipelines/view-results.png)

현재 실행 중인 파이프라인을 선택하여 작업에 대한 세부 정보를 볼 수 있습니다. 작업이 완료되면 각 단계에 대한 결과가 표시됩니다.

## <a name="copy-and-deploy-tasks"></a>작업 복사 및 배포

이 섹션에서는 두 작업을 사용하여 아티팩트를 단계화하고 템플릿을 배포하여 연속 배포를 구성하는 방법을 보여 주십니다.

다음 YAML은 [Azure 파일 복사 작업을](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 YAML을 보여 주며,

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

사용자 환경에 맞게 수정해야 하는 이 작업의 여러 부분이 있습니다. 는 `SourcePath` 파이프라인 파일을 기준으로 한 아티팩트의 위치를 나타냅니다. 이 예제에서는 파일이 프로젝트 이름인 폴더에 `AzureResourceGroup1` 있습니다.

```yaml
SourcePath: '<path-to-artifacts>'
```

의 `azureSubscription`경우 만든 서비스 연결의 이름을 제공합니다.

```yaml
azureSubscription: '<your-connection-name>'
```

저장소 및 컨테이너 이름의 경우 아티팩트를 저장하는 데 사용할 저장소 계정 및 컨테이너의 이름을 제공합니다. 저장소 계정이 있어야 합니다.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

다음 YAML은 [Azure 리소스 관리자 템플릿 배포 작업을](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 YAML을 보여 주며 다음과 같은 YAML을 보여 주며

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

사용자 환경에 맞게 수정해야 하는 이 작업의 여러 부분이 있습니다.

- `deploymentScope`: 옵션에서 `Management Group` `Subscription` `Resource Group`배포 범위를 선택합니다. 이 연습에서는 **리소스 그룹을** 사용합니다. 범위에 대해 자세히 알아보려면 [배포 범위](deploy-rest.md#deployment-scope)를 참조하세요.

- `ConnectedServiceName`: 만든 서비스 연결의 이름을 제공합니다.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: 대상 구독 ID를 제공합니다. 이 속성은 리소스 그룹 배포 범위 및 구독 배포 범위에만 적용됩니다.

- `resourceGroupName`및 `location`: 배포할 리소스 그룹의 이름과 위치를 제공합니다. 작업이 존재하지 않는 경우 리소스 그룹을 만듭니다.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

배포 작업은 명명된 `WebSite.json` 템플릿과 WebSite.parameters.json이라는 매개 변수 파일에 연결됩니다. 템플릿 및 매개 변수 파일의 이름을 사용합니다.

이제 작업을 만드는 방법을 이해하므로 파이프라인을 편집하는 단계를 살펴보겠습니다.

1. 파이프라인을 열고 내용을 YAML로 바꿉꿉을 바꿉꿉입니다.

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
    - task: AzureResourceGroupDeployment@2
      displayName: 'Deploy template'
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. **저장**을 선택합니다.

1. 커밋에 대한 메시지를 제공하고 **마스터에**직접 커밋합니다.

1. **저장을**선택하면 빌드 파이프라인이 자동으로 실행됩니다. 빌드 파이프라인에 대한 요약으로 돌아가 상태를 확인합니다.

   ![결과 보기](./media/add-template-to-azure-pipelines/view-results.png)

현재 실행 중인 파이프라인을 선택하여 작업에 대한 세부 정보를 볼 수 있습니다. 작업이 완료되면 각 단계에 대한 결과가 표시됩니다.

## <a name="next-steps"></a>다음 단계

ARM 템플릿을 사용하여 Azure 파이프라인을 사용하는 단계별 프로세스는 [자습서: ARM 템플릿과 Azure 파이프라인의 지속적인 통합을](template-tutorial-use-azure-pipelines.md)참조하십시오.
