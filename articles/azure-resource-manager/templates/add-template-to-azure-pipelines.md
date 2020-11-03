---
title: Azure Pipelines 및 템플릿이 있는 CI/CD
description: Azure Resource Manager 템플릿을 사용 하 여 Azure Pipelines에서 연속 통합을 구성 하는 방법을 설명 합니다. PowerShell 스크립트를 사용 하거나 파일을 스테이징 위치에 복사 하 여 배포 하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 86ad2839375b73bf9595cf3369960e614ec03e67
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233817"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>ARM 템플릿을 Azure Pipelines와 통합

연속 통합 및 CI/CD (지속적인 통합 및 지속적인 배포)를 위해 Azure Pipelines와 Azure Resource Manager 템플릿 (ARM 템플릿)을 통합할 수 있습니다. [Arm Azure Pipelines 템플릿의 연속 통합](deployment-tutorial-pipeline.md) 자습서에서는 [arm 템플릿 배포 작업](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) 을 사용 하 여 GitHub 리포지토리에서 템플릿을 배포 하는 방법을 보여 줍니다. 이 방법은 리포지토리에서 직접 템플릿을 배포 하려는 경우에 작동 합니다.

이 문서에서는 Azure Pipelines를 사용 하 여 템플릿을 배포 하는 두 가지 방법을 알아봅니다. 이 문서는 다음 방법을 안내합니다.

* **Azure PowerShell 스크립트를 실행 하는 작업을 추가** 합니다. 이 옵션은 로컬 테스트를 실행할 때 사용한 것과 동일한 스크립트를 사용할 수 있기 때문에 개발 수명 주기 동안 일관성을 제공 하는 이점이 있습니다. 스크립트는 템플릿을 배포 하지만 매개 변수로 사용할 값을 가져오는 등의 다른 작업을 수행할 수도 있습니다.

   Visual Studio는 PowerShell 스크립트를 포함 하는 [Azure 리소스 그룹 프로젝트](create-visual-studio-deployment-project.md) 를 제공 합니다. 스크립트는 리소스 관리자에서 액세스할 수 있는 저장소 계정에 대 한 프로젝트의 아티팩트를 준비 합니다. 아티팩트는 연결 된 템플릿, 스크립트, 응용 프로그램 이진 파일 등 프로젝트의 항목입니다. 프로젝트에서 스크립트를 계속 사용 하려면이 문서에 표시 된 PowerShell 스크립트 태스크를 사용 합니다.

* 작업 **을 추가 하 여 작업을 복사 하 고 배포** 합니다. 이 옵션은 프로젝트 스크립트에 대 한 편리한 대안을 제공 합니다. 파이프라인에서 두 작업을 구성 합니다. 한 작업은 아티팩트를 액세스할 수 있는 위치로 단계적으로 준비 합니다. 다른 작업은 해당 위치에서 템플릿을 배포 합니다.

## <a name="prepare-your-project"></a>프로젝트 준비

이 문서에서는 ARM 템플릿 및 Azure DevOps 조직이 파이프라인을 만들 준비가 되었다고 가정 합니다. 다음 단계에서는 준비 되었는지 확인 하는 방법을 보여 줍니다.

* Azure DevOps 조직이 있습니다. 계정이 없는 경우 [체험 계정을 만듭니다](/azure/devops/pipelines/get-started/pipelines-sign-up). 팀에 이미 Azure DevOps 조직이 있는 경우 사용할 Azure DevOps 프로젝트의 관리자 인지 확인 합니다.

* Azure 구독에 대 한 [서비스 연결](/azure/devops/pipelines/library/connect-to-azure) 을 구성 했습니다. 파이프라인의 작업은 서비스 주체의 id로 실행 됩니다. 연결을 만드는 단계는 [DevOps 프로젝트 만들기](deployment-tutorial-pipeline.md#create-a-devops-project)를 참조 하세요.

* 프로젝트에 대 한 인프라를 정의 하는 [ARM 템플릿이](quickstart-create-templates-use-visual-studio-code.md) 있습니다.

## <a name="create-pipeline"></a>파이프라인 만들기

1. 이전에 파이프라인을 추가 하지 않은 경우 새 파이프라인을 만들어야 합니다. Azure DevOps 조직에서 **파이프라인** 및 **새 파이프라인** 을 선택 합니다.

   ![새 파이프라인 추가](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. 코드를 저장할 위치를 지정 합니다. 다음 그림에서는 **Azure Repos Git** 를 선택 하는 방법을 보여 줍니다.

   ![코드 소스 선택](./media/add-template-to-azure-pipelines/select-source.png)

1. 해당 소스에서 프로젝트에 대 한 코드를 포함 하는 리포지토리를 선택 합니다.

   ![리포지토리 선택](./media/add-template-to-azure-pipelines/select-repo.png)

1. 만들 파이프라인 형식을 선택 합니다. **시작 파이프라인** 을 선택할 수 있습니다.

   ![파이프라인 선택](./media/add-template-to-azure-pipelines/select-pipeline.png)

Azure PowerShell 작업 또는 파일 복사를 추가 하 고 작업을 배포할 준비가 되었습니다.

## <a name="azure-powershell-task"></a>Azure PowerShell 작업

이 섹션에서는 프로젝트에서 PowerShell 스크립트를 실행 하는 단일 작업을 사용 하 여 연속 배포를 구성 하는 방법을 보여 줍니다. 템플릿을 배포 하는 PowerShell 스크립트가 필요한 경우 [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) 또는 [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1)을 참조 하세요.

다음 YAML 파일은 [Azure PowerShell 작업](/azure/devops/pipelines/tasks/deploy/azure-powershell)을 만듭니다.

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-AzTemplate.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

작업을로 설정 하면 `AzurePowerShell@5` 파이프라인에서 [Az module](/powershell/azure/new-azureps-module-az)을 사용 합니다. 스크립트에서 AzureRM 모듈을 사용 하는 경우 작업을로 설정 `AzurePowerShell@3` 합니다.

```yaml
steps:
- task: AzurePowerShell@3
```

`azureSubscription`에서, 만든 서비스 연결의 이름을 제공 합니다.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

의 `scriptPath` 경우 파이프라인 파일에서 스크립트에 대 한 상대 경로를 제공 합니다. 리포지토리를 살펴보면 경로를 볼 수 있습니다.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

에서 `ScriptArguments` 스크립트에 필요한 매개 변수를 제공 합니다. 다음 예에서는 스크립트에 대 한 몇 가지 매개 변수를 보여 주지만 스크립트에 대 한 매개 변수를 사용자 지정 해야 합니다.

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

**저장** 을 선택 하면 빌드 파이프라인이 자동으로 실행 됩니다. 빌드 파이프라인의 요약으로 돌아가서 상태를 확인 합니다.

![결과 보기](./media/add-template-to-azure-pipelines/view-results.png)

현재 실행 중인 파이프라인을 선택 하 여 작업에 대 한 세부 정보를 볼 수 있습니다. 완료 되 면 각 단계에 대 한 결과가 표시 됩니다.

## <a name="copy-and-deploy-tasks"></a>작업 복사 및 배포

이 섹션에서는 두 가지 작업을 사용 하 여 연속 배포를 구성 하는 방법을 보여 줍니다. 첫 번째 작업은 저장소 계정에 대 한 아티팩트를 준비 하 고 두 번째 작업은 템플릿을 배포 합니다.

저장소 계정에 파일을 복사 하려면 서비스 연결에 대 한 서비스 주체에 저장소 Blob 데이터 참가자 또는 저장소 Blob 데이터 소유자 역할을 할당 해야 합니다. 자세한 내용은 [AzCopy 시작](../../storage/common/storage-use-azcopy-v10.md)을 참조 하세요.

다음 YAML은 [Azure 파일 복사 작업](/azure/devops/pipelines/tasks/deploy/azure-file-copy)을 보여 줍니다.

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

이 작업에는 환경에 맞게 수정 하는 여러 부분이 있습니다. 는 `SourcePath` 파이프라인 파일을 기준으로 아티팩트의 위치를 나타냅니다.

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

파일 복사 작업을 만든 후에는 준비 된 템플릿을 배포 하는 작업을 추가할 준비가 완료 된 것입니다.

다음 YAML은 [Azure Resource Manager 템플릿 배포 작업](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)을 보여 줍니다.

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

이 작업의 여러 부분에서 더 자세히 검토 합니다.

- `deploymentScope`: `Management Group` , 및 옵션에서 배포 범위를 선택 합니다. `Subscription` `Resource Group` 범위에 대해 자세히 알아보려면 [배포 범위](deploy-rest.md#deployment-scope)를 참조하세요.

- `azureResourceManagerConnection`: 만든 서비스 연결의 이름을 제공 합니다.

- `subscriptionId`: 대상 구독 ID를 제공 합니다. 이 속성은 리소스 그룹 배포 범위 및 구독 배포 범위에만 적용 됩니다.

- `resourceGroupName` 및 `location` : 배포 하려는 리소스 그룹의 이름과 위치를 제공 합니다. 리소스가 없는 경우 태스크에서 리소스 그룹을 만듭니다.

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`: 준비 된 템플릿에 대 한 링크를 제공 합니다. 값을 설정할 때 파일 복사 작업에서 반환 된 변수를 사용 합니다. 다음 예에서는 mainTemplate.js의 템플릿에 연결 합니다. 파일 복사 작업이 파일을 복사 하는 위치에 **템플릿** 폴더가 포함 됩니다. 파이프라인에서 템플릿의 경로와 템플릿 이름을 제공 합니다.

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

파이프라인의 모양은 다음과 같습니다.

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

**저장** 을 선택 하면 빌드 파이프라인이 자동으로 실행 됩니다. 빌드 파이프라인의 요약으로 돌아가서 상태를 확인 합니다.

## <a name="next-steps"></a>다음 단계

GitHub 작업에서 ARM 템플릿을 사용 하는 방법에 대 한 자세한 내용은 [Github 작업을 사용 하 여 Azure Resource Manager 템플릿 배포](deploy-github-actions.md)를 참조 하세요.
