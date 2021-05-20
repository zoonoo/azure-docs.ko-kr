---
title: Azure Pipelines 및 템플릿을 사용한 CI/CD
description: Azure Resource Manager 템플릿을 사용하여 Azure Pipelines에서 연속 통합을 구성하는 방법을 설명합니다. PowerShell 스크립트를 사용하거나 파일을 스테이징 위치에 복사하여 배포하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 4a2f1f15de0abd802f3dce138b2cea33e52e3dfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561945"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>ARM 템플릿을 Azure Pipelines와 통합

CI/CD(연속 통합 및 지속적인 배포)를 위해 Azure Pipelines와 ARM 템플릿(Azure Resource Manager 템플릿)을 통합할 수 있습니다. 이 문서에서는 Azure Pipelines를 사용하여 템플릿을 배포하는 두 가지 고급 방법을 알아봅니다.

## <a name="select-your-option"></a>옵션 선택

이 문서를 진행하기 전에 파이프라인에서 ARM 템플릿 배포에 대한 다양한 옵션을 살펴보겠습니다.

* **ARM 템플릿 배포 작업을 사용합니다**. 이 옵션이 가장 쉬운 옵션입니다. 이 방법은 리포지토리에서 직접 템플릿을 배포하려는 경우에 작동합니다. 이 옵션은 이 문서에서 다루지 않지만 대신 [ARM 템플릿과 Azure Pipelines의 연속 통합](deployment-tutorial-pipeline.md) 자습서에서 설명합니다. [ARM 템플릿 배포 작업](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)을 사용하여 GitHub 리포지토리에서 템플릿을 배포하는 방법을 보여 줍니다.

* **Azure PowerShell 스크립트를 실행하는 작업을 추가합니다**. 이 옵션은 로컬 테스트를 실행할 때 사용한 것과 동일한 스크립트를 사용할 수 있기 때문에 개발 수명 주기 동안 일관성을 제공하는 이점이 있습니다. 스크립트는 템플릿을 배포하지만 매개 변수로 사용할 값을 가져오는 등의 다른 작업을 수행할 수도 있습니다. 이 옵션은 이 문서에 나와 있습니다. [Azure PowerShell 작업](#azure-powershell-task)을 참조하세요.

   Visual Studio는 PowerShell 스크립트를 포함하는 [Azure 리소스 그룹 프로젝트](create-visual-studio-deployment-project.md)를 제공합니다. 스크립트는 Resource Manager에서 액세스할 수 있는 스토리지 계정에 대한 프로젝트의 아티팩트를 스테이징합니다. 아티팩트는 연결된 템플릿, 스크립트, 애플리케이션 이진 파일 등 프로젝트의 항목입니다. 프로젝트에서 스크립트를 계속 사용하려면 이 문서에 표시된 PowerShell 스크립트 작업을 사용합니다.

* **작업을 추가하여 작업을 복사하고 배포합니다**. 이 옵션은 프로젝트 스크립트보다 편리한 대안을 제공합니다. 파이프라인에서 두 작업을 구성합니다. 한 작업은 아티팩트를 액세스할 수 있는 위치로 스테이징합니다. 다른 작업은 해당 위치에서 템플릿을 배포합니다. 이 옵션은 이 문서에 나와 있습니다. [작업 복사 및 배포](#copy-and-deploy-tasks)를 참조하세요.

## <a name="prepare-your-project"></a>프로젝트 준비

이 문서에서는 ARM 템플릿 및 Azure DevOps 조직이 파이프라인을 만들 준비가 되었다고 가정합니다. 다음 단계에서는 준비되었는지 확인하는 방법을 보여 줍니다.

* Azure DevOps 조직이 있습니다. 계정이 없는 경우 [체험 계정을 만듭니다](/azure/devops/pipelines/get-started/pipelines-sign-up). 팀에 이미 Azure DevOps 조직이 있는 경우 사용할 Azure DevOps 프로젝트의 관리자인지 확인합니다.

* Azure 구독에 대한 [서비스 연결](/azure/devops/pipelines/library/connect-to-azure)을 구성했습니다. 파이프라인의 작업은 서비스 주체의 ID로 실행됩니다. 연결을 만드는 단계는 [DevOps 프로젝트 만들기](deployment-tutorial-pipeline.md#create-a-devops-project)를 참조하세요.

* 프로젝트에 대한 인프라를 정의하는 [ARM 템플릿](quickstart-create-templates-use-visual-studio-code.md)이 있습니다.

## <a name="create-pipeline"></a>파이프라인 만들기

1. 이전에 파이프라인을 추가하지 않은 경우 새 파이프라인을 만들어야 합니다. Azure DevOps 조직에서 **파이프라인** 및 **새 파이프라인** 을 선택합니다.

   ![새 파이프라인 추가](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. 코드를 저장할 위치를 지정합니다. 다음 그림에서는 **Azure Repos Git** 선택을 보여 줍니다.

   ![코드 원본 선택](./media/add-template-to-azure-pipelines/select-source.png)

1. 해당 원본에서 프로젝트에 대한 코드를 포함하는 리포지토리를 선택합니다.

   ![리포지토리 선택](./media/add-template-to-azure-pipelines/select-repo.png)

1. 만들 파이프라인의 형식을 선택합니다. **시작 파이프라인** 을 선택할 수 있습니다.

   ![파이프라인 선택](./media/add-template-to-azure-pipelines/select-pipeline.png)

Azure PowerShell 작업 또는 복사 파일을 추가하고 작업을 배포할 준비가 되었습니다.

## <a name="azure-powershell-task"></a>Azure PowerShell 작업

이 섹션에서는 프로젝트에서 PowerShell 스크립트를 실행하는 단일 작업을 사용하여 지속적인 배포를 구성하는 방법을 보여 줍니다. 템플릿을 배포하는 PowerShell 스크립트가 필요한 경우 [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) 또는 [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1)을 참조하세요.

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

작업을 `AzurePowerShell@5`로 설정하면 파이프라인에서 [Az 모듈](/powershell/azure/new-azureps-module-az)을 사용합니다. 스크립트에서 AzureRM 모듈을 사용하는 경우 작업을 `AzurePowerShell@3`으로 설정합니다.

```yaml
steps:
- task: AzurePowerShell@3
```

`azureSubscription`에 대해 만든 서비스 연결의 이름을 제공합니다.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

`scriptPath`에 대해 파이프라인 파일에서 스크립트에 대한 상대 경로를 제공합니다. 리포지토리를 살펴보면 경로를 볼 수 있습니다.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

`ScriptArguments`에서 스크립트에 필요한 매개 변수를 제공합니다. 다음 예제에서는 스크립트에 대한 몇 가지 매개 변수를 보여 주지만 스크립트에 대한 매개 변수를 사용자 지정해야 합니다.

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

**저장** 을 선택하면 빌드 파이프라인이 자동으로 실행됩니다. 빌드 파이프라인의 요약으로 돌아가서 상태를 확인합니다.

![결과 보기](./media/add-template-to-azure-pipelines/view-results.png)

현재 실행 중인 파이프라인을 선택하여 작업에 대한 세부 정보를 볼 수 있습니다. 완료되면 각 단계에 대한 결과가 표시됩니다.

## <a name="copy-and-deploy-tasks"></a>작업 복사 및 배포

이 섹션에서는 두 가지 작업을 사용하여 지속적인 배포를 구성하는 방법을 보여 줍니다. 첫 번째 작업은 스토리지 계정에 대한 아티팩트를 스테이징하고 두 번째 작업은 템플릿을 배포합니다.

스토리지 계정에 파일을 복사하려면 서비스 연결에 대한 서비스 주체에 Storage Blob 데이터 기여자 또는 Storage Blob 데이터 소유자 역할을 할당해야 합니다. 자세한 내용은 [AzCopy 시작](../../storage/common/storage-use-azcopy-v10.md)을 참조하세요.

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

이 작업에는 환경에 맞게 수정하는 여러 부분이 있습니다. `SourcePath`는 파이프라인 파일을 기준으로 아티팩트의 위치를 나타냅니다.

```yaml
SourcePath: '<path-to-artifacts>'
```

`azureSubscription`에 대해 만든 서비스 연결의 이름을 제공합니다.

```yaml
azureSubscription: '<your-connection-name>'
```

스토리지 및 컨테이너 이름에는 아티팩트를 저장하는 데 사용하려는 스토리지 계정 및 컨테이너의 이름을 제공합니다. 스토리지 계정이 있어야 합니다.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

파일 복사 작업을 만든 후에는 준비된 템플릿을 배포하는 작업을 추가할 준비가 완료된 것입니다.

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

이 작업에는 더 자세히 검토할 몇 가지 부분이 있습니다.

- `deploymentScope`: `Management Group`, `Subscription` 및 `Resource Group` 옵션에서 배포 범위를 선택합니다. 범위에 대해 자세히 알아보려면 [배포 범위](deploy-rest.md#deployment-scope)를 참조하세요.

- `azureResourceManagerConnection`: 만든 서비스 연결의 이름을 제공합니다.

- `subscriptionId`: 대상 구독 ID를 제공합니다. 이 속성은 리소스 그룹 배포 범위 및 구독 배포 범위에만 적용됩니다.

- `resourceGroupName` 및 `location`: 배포하려는 리소스 그룹의 이름과 위치를 제공합니다. 작업은 리소스 그룹을 만듭니다(없는 경우).

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`: 준비된 템플릿에 대한 링크를 제공합니다. 값을 설정할 때 파일 복사 작업에서 반환된 변수를 사용합니다. 다음 예제에서는 mainTemplate.json이라는 템플릿에 연결합니다. 파일 복사 작업이 파일을 복사한 위치에 있기 때문에 **templates** 라는 폴더가 포함됩니다. 파이프라인에서 템플릿의 경로와 템플릿 이름을 제공합니다.

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

파이프라인은 다음과 같습니다.

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

**저장** 을 선택하면 빌드 파이프라인이 자동으로 실행됩니다. 빌드 파이프라인의 요약으로 돌아가서 상태를 확인합니다.

## <a name="next-steps"></a>다음 단계

* 파이프라인에서 가상 작업을 사용하려면 [파이프라인에서 가상을 사용하여 ARM 템플릿 테스트](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)를 참조하세요.
* GitHub Actions에서 ARM 템플릿을 사용하는 방법에 대한 자세한 내용은 [GitHub Actions를 사용하여 Azure Resource Manager 템플릿 배포](deploy-github-actions.md)를 참조하세요.
