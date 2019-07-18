---
title: Azure 파이프라인 및 Resource Manager 템플릿 사용 하 여 CI/CD
description: Visual Studio에서 Azure 리소스 그룹 배포 프로젝트를 사용 하 여 Resource Manager 템플릿을 배포 하 여 Azure 파이프라인에서 연속 통합을 설정 하는 방법에 설명 합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191461"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Resource Manager 템플릿은 Azure 파이프라인과 통합

Visual Studio 템플릿 만들기 및 Azure 구독에 배포 하기에 대 한 Azure 리소스 그룹 프로젝트를 제공 합니다. 연속 통합 및 지속적인 배포 (CI/CD)에 대 한 Azure 파이프라인을 사용 하 여이 프로젝트를 통합할 수 있습니다.

Azure 파이프라인을 사용 하 여 템플릿을 배포 하는 방법은 두 가지 있습니다.

* **Azure PowerShell 스크립트를 실행 하는 작업 추가**합니다. 이 옵션을 사용 하면 Visual Studio 프로젝트 (Deploy-AzureResourceGroup.ps1)에 포함 된 동일한 스크립트를 사용 하 여 개발 수명 주기 동안 일관성을 제공 하는 이점이 있습니다. 스크립트 단계 아티팩트 프로젝트에서 Resource Manager에 액세스할 수 있는 저장소 계정에 있습니다. 아티팩트는 연결 된 템플릿, 스크립트 및 응용 프로그램 이진 파일을 같은 프로젝트에서 항목입니다. 그런 다음 스크립트는 템플릿을 배포 합니다.

* **복사 작업을 배포 하는 작업 추가**합니다. 이 옵션은 프로젝트 스크립트에 편리한 대안을 제공 합니다. 파이프라인의 두 가지 작업을 구성합니다. 하나 이상의 태스크 아티팩트 준비 및 다른 작업 템플릿을 배포 합니다.

이 문서에서는 두 가지 방식을 모두 설명합니다.

## <a name="prepare-your-project"></a>프로젝트 준비

이 문서는 파이프라인을 만들기 위한 Visual Studio 프로젝트 및 Azure DevOps 조직 준비가 가정 합니다. 다음 단계를 준비가 되었는지 확인 하는 방법을 보여 줍니다.

* Azure DevOps 조직을 해야합니다. 계정이 없는 경우 [무료로 만들](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)합니다. 팀에는 이미 Azure DevOps 조직에서 사용 하려는 Azure DevOps 프로젝트의 관리자 인 있는지 확인 합니다.

* 구성한 경우는 [서비스 연결](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) Azure 구독에 있습니다. 서비스 주체의 id에서 파이프라인의 작업을 실행합니다. 연결을 만드는 단계를 참조 하세요 [DevOps 프로젝트를 만들](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project)합니다.

* 생성 된 Visual Studio 프로젝트를 **Azure 리소스 그룹** starter 템플릿. 해당 유형의 프로젝트를 만드는 방법에 대 한 자세한 내용은 [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)합니다.

* Visual Studio 프로젝트가 [Azure DevOps 프로젝트를 연결할](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)합니다.

## <a name="create-pipeline"></a>파이프라인 만들기

1. 파이프라인에 이전에 추가 하지 않은 경우 새 파이프라인을 만드는 데 필요 합니다. Azure DevOps 조직에서 선택 **파이프라인** 하 고 **새 파이프라인**합니다.

   ![새 파이프라인 추가](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. 코드가 저장 되는 위치를 지정 합니다. 다음 이미지는 선택 표시 **Azure 리포지토리 Git**합니다.

   ![코드 소스를 선택 합니다.](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. 해당 원본에서 프로젝트에 대 한 코드가 있는 리포지토리를 선택 합니다.

   ![리포지토리 선택](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. 만들려는 파이프라인의 유형을 선택 합니다. 선택할 수 있습니다 **스타터 파이프라인**합니다.

   ![파이프라인 선택](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Azure PowerShell 작업 또는 복사 파일에는 추가 및 배포 작업 중 하나에 준비가 되었습니다.

## <a name="azure-powershell-task"></a>Azure PowerShell 작업

이 섹션에서는 프로젝트에서 PowerShell 스크립트를 실행 하는 단일 작업을 사용 하 여 연속 배포를 구성 하는 방법을 보여 줍니다. 다음 YAML 파일을 만듭니다는 [Azure PowerShell 작업](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

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

로 작업을 설정 하면 `AzurePowerShell@3`, 파이프라인 연결을 인증 하는 AzureRM 모듈에서 명령을 사용 합니다. 기본적으로 Visual Studio 프로젝트에서 PowerShell 스크립트는 AzureRM 모듈을 사용합니다. 사용 하 여 스크립트를 업데이트 한 경우는 [Az 모듈](/powershell/azure/new-azureps-module-az), 태스크로 `AzurePowerShell@4`합니다.

```yaml
steps:
- task: AzurePowerShell@4
```

에 대 한 `azureSubscription`, 사용자가 만든 서비스 연결의 이름을 제공 합니다.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

에 대 한 `scriptPath`, 스크립트에 파이프라인 파일의 상대 경로 제공 합니다. 경로를 보려면 리포지토리에서 확인할 수 있습니다.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

스테이지 아티팩트에 필요 하지 않으면, 이름 및 배포에 사용할 리소스 그룹의 위치를 전달 합니다. Visual Studio 스크립트 이미 존재 하지 않는 경우 리소스 그룹을 만듭니다.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

기존 저장소 계정에 대 한 스테이지 아티팩트를 해야 하는 경우 사용 합니다.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

이제, 작업을 만들고 파이프라인을 편집 하는 단계를 진행 해 보겠습니다 하는 방법을 이해 합니다.

1. 파이프라인을 열고 프로그램 YAML을 사용 하 여 콘텐츠를 바꿉니다.

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

   ![파이프라인 저장](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. 커밋, 메시지를 제공 하 고에 직접 커밋 **마스터**합니다.

1. 선택 하면 **저장할**, 빌드 파이프라인을 자동으로 실행 됩니다. 빌드 파이프라인에 대 한 요약으로 돌아가서 상태를 시청 하세요.

   ![결과 보기](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

작업에 대 한 자세한 내용을 보려면 현재 실행 중인 파이프라인을 선택할 수 있습니다. 완료 되 면 각 단계에 대 한 결과가 표시 됩니다.

## <a name="copy-and-deploy-tasks"></a>복사 및 배포 작업

이 섹션에서는 아티팩트를 스테이징 하 고 템플릿을 배포 하는 두 가지 작업을 사용 하 여 연속 배포를 구성 하는 방법을 보여 줍니다. 

다음 YAML은 [Azure 파일 복사 작업](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

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

사용자 환경에 대 한 수정 하려면이 작업의 여러 부분이 있습니다. `SourcePath` 파이프라인 파일을 기준으로 아티팩트 위치를 나타냅니다. 이 예제에서는 파일이 폴더에 있는 `AzureResourceGroup1` 프로젝트의 이름이 되었습니다.

```yaml
SourcePath: '<path-to-artifacts>'
```

에 대 한 `azureSubscription`, 사용자가 만든 서비스 연결의 이름을 제공 합니다.

```yaml
azureSubscription: '<your-connection-name>'
```

Storage 및 컨테이너 이름에 대 한 저장소 계정 및 아티팩트를 저장 하는 데 사용 하려는 컨테이너의 이름을 제공 합니다. 저장소 계정이 있어야 합니다.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

다음 YAML은 [Azure 리소스 그룹 배포 작업](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

사용자 환경에 대 한 수정 하려면이 작업의 여러 부분이 있습니다. 에 대 한 `azureSubscription`, 사용자가 만든 서비스 연결의 이름을 제공 합니다.

```yaml
azureSubscription: '<your-connection-name>'
```

에 대 한 `resourceGroupName` 및 `location`, 이름 및 배포 하려는 리소스 그룹의 위치를 제공 합니다. 작업이 존재 하지 않는 경우 리소스 그룹을 만듭니다.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

이라는 템플릿으로 배포 작업을 링크 `WebSite.json` 매개 변수 파일 이라는 WebSite.parameters.json 합니다. 템플릿 및 매개 변수 파일의 이름을 사용 합니다.

이제, 작업을 만들고 파이프라인을 편집 하는 단계를 진행 해 보겠습니다 하는 방법을 이해 합니다.

1. 파이프라인을 열고 프로그램 YAML을 사용 하 여 콘텐츠를 바꿉니다.

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
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. **저장**을 선택합니다.

1. 커밋, 메시지를 제공 하 고에 직접 커밋 **마스터**합니다.

1. 선택 하면 **저장할**, 빌드 파이프라인을 자동으로 실행 됩니다. 빌드 파이프라인에 대 한 요약으로 돌아가서 상태를 시청 하세요.

   ![결과 보기](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

작업에 대 한 자세한 내용을 보려면 현재 실행 중인 파이프라인을 선택할 수 있습니다. 완료 되 면 각 단계에 대 한 결과가 표시 됩니다.

## <a name="next-steps"></a>다음 단계

Resource Manager 템플릿을 사용 하 여 Azure 파이프라인을 사용 하 여 단계별 프로세스에 대 한 참조 [자습서: Azure 파이프라인을 사용 하 여 Azure Resource Manager 템플릿의 연속 통합](resource-manager-tutorial-use-azure-pipelines.md)합니다.
