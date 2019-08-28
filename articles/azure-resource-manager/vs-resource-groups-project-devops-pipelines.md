---
title: Azure Pipelines 및 리소스 관리자 템플릿이 있는 CI/CD
description: Visual Studio에서 Azure 리소스 그룹 배포 프로젝트를 사용 하 여 리소스 관리자 템플릿을 배포 하는 Azure Pipelines에서 연속 통합을 설정 하는 방법을 설명 합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: ae896fa0820fbd25ed3f2d29c89fbcd56e7fd6f5
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982454"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Azure Pipelines와 리소스 관리자 템플릿 통합

Visual Studio는 템플릿을 만들고 Azure 구독에 배포 하기 위한 Azure 리소스 그룹 프로젝트를 제공 합니다. 연속 통합 및 CI/CD (지속적인 통합 및 지속적인 배포)를 위해이 프로젝트를 Azure Pipelines와 통합할 수 있습니다.

Azure Pipelines를 사용 하 여 템플릿을 배포 하는 방법에는 두 가지가 있습니다.

* **Azure PowerShell 스크립트를 실행 하는 작업을 추가**합니다. 이 옵션은 Visual Studio 프로젝트에 포함 된 것과 동일한 스크립트 (Deploy-azureresourcegroup.ps1)를 사용 하기 때문에 개발 수명 주기 동안 일관성을 제공 하는 이점이 있습니다. 스크립트는 리소스 관리자에서 액세스할 수 있는 저장소 계정에 대 한 프로젝트의 아티팩트를 준비 합니다. 아티팩트는 연결 된 템플릿, 스크립트, 응용 프로그램 이진 파일 등 프로젝트의 항목입니다. 그런 다음 스크립트에서 템플릿을 배포 합니다.

* 작업 **을 추가 하 여 작업을 복사 하 고 배포**합니다. 이 옵션은 프로젝트 스크립트에 대 한 편리한 대안을 제공 합니다. 파이프라인에서 두 작업을 구성 합니다. 한 작업은 아티팩트와 다른 작업에서 템플릿을 배포 합니다.

이 문서에서는 두 가지 방식을 모두 설명합니다.

## <a name="prepare-your-project"></a>프로젝트 준비

이 문서에서는 Visual Studio 프로젝트와 Azure DevOps 조직이 파이프라인을 만들 준비가 되었다고 가정 합니다. 다음 단계에서는 준비 되었는지 확인 하는 방법을 보여 줍니다.

* Azure DevOps 조직이 있습니다. 없는 경우 [무료로 만드세요](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). 팀에 이미 Azure DevOps 조직이 있는 경우 사용할 Azure DevOps 프로젝트의 관리자 인지 확인 합니다.

* Azure 구독에 대 한 [서비스 연결](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) 을 구성 했습니다. 파이프라인의 작업은 서비스 주체의 id로 실행 됩니다. 연결을 만드는 단계는 [DevOps 프로젝트 만들기](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project)를 참조 하세요.

* **Azure 리소스 그룹** 스타터 템플릿에서 만든 Visual Studio 프로젝트가 있습니다. 해당 형식의 프로젝트를 만드는 방법에 대 한 자세한 내용은 [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 참조 하세요.

* Visual Studio 프로젝트는 [Azure DevOps 프로젝트에 연결](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)되어 있습니다.

## <a name="create-pipeline"></a>파이프라인 만들기

1. 이전에 파이프라인을 추가 하지 않은 경우 새 파이프라인을 만들어야 합니다. Azure DevOps 조직에서 **파이프라인** 및 **새 파이프라인**을 선택 합니다.

   ![새 파이프라인 추가](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. 코드를 저장할 위치를 지정 합니다. 다음 그림에서는 **Azure Repos Git**를 선택 하는 방법을 보여 줍니다.

   ![코드 소스 선택](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. 해당 소스에서 프로젝트에 대 한 코드를 포함 하는 리포지토리를 선택 합니다.

   ![리포지토리 선택](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. 만들 파이프라인 형식을 선택 합니다. **시작 파이프라인**을 선택할 수 있습니다.

   ![파이프라인 선택](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Azure PowerShell 작업 또는 파일 복사를 추가 하 고 작업을 배포할 준비가 되었습니다.

## <a name="azure-powershell-task"></a>작업 Azure PowerShell

이 섹션에서는 프로젝트에서 PowerShell 스크립트를 실행 하는 단일 작업을 사용 하 여 연속 배포를 구성 하는 방법을 보여 줍니다. 다음 YAML 파일은 [Azure PowerShell 작업](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)을 만듭니다.

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

작업을로 `AzurePowerShell@3`설정 하면 파이프라인은 AzureRM 모듈의 명령을 사용 하 여 연결을 인증 합니다. 기본적으로 Visual Studio 프로젝트의 PowerShell 스크립트는 AzureRM 모듈을 사용 합니다. [Az module](/powershell/azure/new-azureps-module-az)을 사용 하도록 스크립트를 업데이트 한 경우 작업을로 `AzurePowerShell@4`설정 합니다.

```yaml
steps:
- task: AzurePowerShell@4
```

`azureSubscription`에서, 만든 서비스 연결의 이름을 제공 합니다.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

의 `scriptPath`경우 파이프라인 파일에서 스크립트에 대 한 상대 경로를 제공 합니다. 리포지토리를 살펴보면 경로를 볼 수 있습니다.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

아티팩트를 준비할 필요가 없는 경우 배포에 사용할 리소스 그룹의 이름과 위치를 전달 하면 됩니다. 아직 없는 경우 Visual Studio 스크립트는 리소스 그룹을 만듭니다.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

기존 저장소 계정에 대 한 아티팩트를 준비 해야 하는 경우 다음을 사용 합니다.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

이제 작업을 만드는 방법을 이해 하 고 파이프라인을 편집 하는 단계를 살펴보겠습니다.

1. 파이프라인을 열고 콘텐츠를 YAML로 바꿉니다.

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

1. 커밋에 대 한 메시지를 제공 하 고 **마스터**에 직접 커밋합니다.

1. **저장**을 선택 하면 빌드 파이프라인이 자동으로 실행 됩니다. 빌드 파이프라인의 요약으로 돌아가서 상태를 확인 합니다.

   ![결과 보기](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

현재 실행 중인 파이프라인을 선택 하 여 작업에 대 한 세부 정보를 볼 수 있습니다. 완료 되 면 각 단계에 대 한 결과가 표시 됩니다.

## <a name="copy-and-deploy-tasks"></a>작업 복사 및 배포

이 섹션에서는 두 작업을 사용 하 여 아티팩트를 준비 하 고 템플릿을 배포 하 여 연속 배포를 구성 하는 방법을 보여 줍니다. 

다음 YAML은 [Azure 파일 복사 작업](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)을 보여 줍니다.

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

이 작업에는 환경에 맞게 수정 하는 여러 부분이 있습니다. 는 `SourcePath` 파이프라인 파일을 기준으로 아티팩트의 위치를 나타냅니다. 이 예제에서 파일은 프로젝트의 이름인 라는 `AzureResourceGroup1` 폴더에 있습니다.

```yaml
SourcePath: '<path-to-artifacts>'
```

`azureSubscription`에서, 만든 서비스 연결의 이름을 제공 합니다.

```yaml
azureSubscription: '<your-connection-name>'
```

저장소 및 컨테이너 이름에는 아티팩트를 저장 하는 데 사용 하려는 저장소 계정 및 컨테이너의 이름을 제공 합니다. 저장소 계정이 있어야 합니다.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

다음 YAML은 [Azure 리소스 그룹 배포 작업](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops)을 보여 줍니다.

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

이 작업에는 환경에 맞게 수정 하는 여러 부분이 있습니다. `azureSubscription`에서, 만든 서비스 연결의 이름을 제공 합니다.

```yaml
azureSubscription: '<your-connection-name>'
```

`resourceGroupName` 및`location`의 경우 배포 하려는 리소스 그룹의 이름과 위치를 제공 합니다. 리소스가 없는 경우 태스크에서 리소스 그룹을 만듭니다.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

배포 작업은 이름이 인 `WebSite.json` 템플릿 및 매개 변수 파일 (. parameters. json)에 연결 됩니다. 템플릿 및 매개 변수 파일의 이름을 사용 합니다.

이제 작업을 만드는 방법을 이해 하 고 파이프라인을 편집 하는 단계를 살펴보겠습니다.

1. 파이프라인을 열고 콘텐츠를 YAML로 바꿉니다.

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

1. 커밋에 대 한 메시지를 제공 하 고 **마스터**에 직접 커밋합니다.

1. **저장**을 선택 하면 빌드 파이프라인이 자동으로 실행 됩니다. 빌드 파이프라인의 요약으로 돌아가서 상태를 확인 합니다.

   ![결과 보기](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

현재 실행 중인 파이프라인을 선택 하 여 작업에 대 한 세부 정보를 볼 수 있습니다. 완료 되 면 각 단계에 대 한 결과가 표시 됩니다.

## <a name="next-steps"></a>다음 단계

리소스 관리자 템플릿과 함께 Azure Pipelines를 사용 하는 단계별 프로세스를 보려면 [자습서: Azure Pipelines](resource-manager-tutorial-use-azure-pipelines.md)를 사용 하 여 Azure Resource Manager 템플릿 연속 통합.
