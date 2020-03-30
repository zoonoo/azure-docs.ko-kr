---
title: Azure DevOps를 사용하여 기능 앱 코드를 지속적으로 업데이트
description: Azure 함수를 대상으로 하는 Azure DevOps 파이프라인을 설정하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.openlocfilehash: 5e2fc8fb06248e2cdad9067c56647da6d9626b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255764"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Azure DevOps를 사용하여 지속적인 배달

Azure 파이프라인을 사용하여 Azure Functions 앱에 [함수를](/azure/devops/pipelines/)자동으로 배포할 수 있습니다.

파이프라인을 정의하는 두 가지 옵션이 있습니다.

- **YAML 파일**: YAML 파일은 파이프라인을 설명합니다. 파일에 빌드 단계 섹션과 릴리스 섹션이 있을 수 있습니다. YAML 파일은 앱과 동일한 리포지토리에 있어야 합니다.
- **템플릿**: 템플릿은 앱을 빌드하거나 배포하는 기성 작업입니다.

## <a name="yaml-based-pipeline"></a>YAML 기반 파이프라인

YAML 기반 파이프라인을 만들려면 먼저 앱을 빌드한 다음 앱을 배포합니다.

### <a name="build-your-app"></a>앱 빌드

Azure 파이프라인에서 앱을 빌드하는 방법은 앱의 프로그래밍 언어에 따라 다릅니다. 각 언어에는 배포 아티팩트를 만드는 특정 빌드 단계가 있습니다. 배포 아티팩트는 Azure에서 함수 앱을 배포하는 데 사용됩니다.

# <a name="c"></a>[C\#](#tab/csharp)

다음 샘플을 사용하여 YAML 파일을 만들어 .NET 앱을 빌드할 수 있습니다.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

다음 샘플을 사용하여 YAML 파일을 만들어 JavaScript 앱을 빌드할 수 있습니다.

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

다음 샘플 중 하나를 사용하여 YAML 파일을 만들어 특정 파이썬 버전에 대한 앱을 빌드할 수 있습니다. 파이썬은 Linux에서 실행되는 함수 앱에대해서만 지원됩니다.

**버전 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**버전 3.6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

다음 샘플을 사용하여 YAML 파일을 만들어 PowerShell 앱을 패키징할 수 있습니다. PowerShell은 Windows Azure 기능에대해서만 지원됩니다.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>앱 배포

호스팅 OS에 따라 YAML 파일에 다음 YAML 샘플 중 하나를 포함해야 합니다.

#### <a name="windows-function-app"></a>윈도우 기능 앱

다음 코드 조각을 사용하여 Windows 함수 앱을 배포할 수 있습니다.

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>리눅스 기능 응용 프로그램

다음 스니펫을 사용하여 Linux 함수 앱을 배포할 수 있습니다.

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>템플릿 기반 파이프라인

Azure DevOps의 템플릿은 앱을 빌드하거나 배포하는 미리 정의된 작업 그룹입니다.

### <a name="build-your-app"></a>앱 빌드

Azure 파이프라인에서 앱을 빌드하는 방법은 앱의 프로그래밍 언어에 따라 다릅니다. 각 언어에는 배포 아티팩트를 만드는 특정 빌드 단계가 있습니다. 배포 아티팩트는 Azure에서 함수 앱을 업데이트하는 데 사용됩니다.

기본 제공 빌드 템플릿을 사용하려면 새 빌드 파이프라인을 만들 때 **클래식 편집기 사용을** 선택하여 디자이너 템플릿을 사용하여 파이프라인을 만듭니다.

![Azure 파이프라인 클래식 편집기 선택](media/functions-how-to-azure-devops/classic-editor.png)

코드의 소스를 구성한 후 Azure Functions 빌드 템플릿을 검색합니다. 앱 언어와 일치하는 템플릿을 선택합니다.

![Azure 함수 빌드 템플릿 선택](media/functions-how-to-azure-devops/build-templates.png)

경우에 따라 빌드 아티팩트에는 특정 폴더 구조가 있습니다. **경로 보관을 위해 Prepend 루트 폴더 이름을** 선택해야 할 수 있습니다.

![루트 폴더 이름을 준비하는 옵션](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>자바 스크립트 애플 리케이션

JavaScript 앱에 Windows 네이티브 모듈에 대한 종속성이 있는 경우 에이전트 풀 버전을 **호스팅 VS2017로**업데이트해야 합니다.

![에이전트 풀 버전 업데이트](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>앱 배포

새 릴리스 파이프라인을 만들 때 Azure Functions 릴리스 템플릿을 검색합니다.

![Azure 함수 릴리스 템플릿 검색](media/functions-how-to-azure-devops/release-template.png)

배포 슬롯에 배포하는 것은 릴리스 템플릿에서 지원되지 않습니다.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Azure CLI를 사용하여 빌드 파이프라인 만들기

Azure에서 빌드 파이프라인을 만들려면 `az functionapp devops-pipeline create` [명령을](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)사용합니다. 빌드 파이프라인은 리포지토리에서 변경된 코드 변경 내용을 빌드하고 릴리스하기 위해 만들어집니다. 명령은 빌드 및 릴리스 파이프라인을 정의하는 새 YAML 파일을 생성한 다음 리포지토리에 커밋합니다. 이 명령의 필수 구성 조건은 코드의 위치에 따라 다릅니다.

- 코드가 GitHub에 있는 경우:

    - 구독에 대한 **쓰기** 권한이 있어야 합니다.

    - Azure DevOps의 프로젝트 관리자여야 합니다.

    - 충분한 권한이 있는 PAT(GitHub 개인 액세스 토큰)를 만들 수 있는 권한이 있어야 합니다. 자세한 내용은 [GitHub PAT 권한 요구 사항을 참조하십시오.](https://aka.ms/azure-devops-source-repos)

    - 자동 생성된 YAML 파일을 커밋할 수 있도록 GitHub 리포지토리의 마스터 브랜치에 커밋할 수 있는 권한이 있어야 합니다.

- 코드가 Azure 리포지토리에 있는 경우:

    - 구독에 대한 **쓰기** 권한이 있어야 합니다.

    - Azure DevOps의 프로젝트 관리자여야 합니다.

## <a name="next-steps"></a>다음 단계

- Azure [함수 개요](functions-overview.md)검토 .
- Azure [DevOps 개요를 검토합니다.](/azure/devops/pipelines/)
