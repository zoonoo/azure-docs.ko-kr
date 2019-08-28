---
title: Azure DevOps-Azure Functions를 사용 하 여 함수 코드 업데이트를 지속적으로 제공
description: Azure Functions를 대상으로 하는 Azure DevOps 파이프라인을 설정 하는 방법에 알아봅니다.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 0fdad0caa2deef0d7d55b30a85632f72f4ff0ecc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594471"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Azure DevOps를 사용 하 여 지속적인 업데이트

사용 하 여 Azure Functions 앱에 함수를 자동으로 배포할 수 있습니다 [Azure 파이프라인](/azure/devops/pipelines/)합니다.

파이프라인을 정의 하기 위한 두 가지 옵션이 있습니다.

- **YAML 파일**: YAML 파일을 파이프라인을 설명합니다. 빌드 단계 섹션과 릴리스 섹션 파일이 있을 수 있습니다. YAML 파일을 앱과 같은 리포지토리 여야 합니다.
- **템플릿**: 템플릿은 빌드하거나 앱을 배포 하는 바로 사용할 수 있는 작업입니다.

## <a name="yaml-based-pipeline"></a>YAML 기반 파이프라인

YAML 기반 파이프라인을 만들려면 먼저 앱을 빌드하고 앱을 배포 합니다.

### <a name="build-your-app"></a>앱 빌드

Azure 파이프라인에서 앱을 빌드하는 방법을 앱의 프로그래밍 언어에 따라 달라 집니다. 각 언어에는 배포 아티팩트를 만들어야 하는 특정 빌드 단계에 있습니다. 배포 아티팩트를 Azure에서 함수 앱 배포에 사용 됩니다.

#### <a name="net"></a>.NET

.NET 앱을 빌드하는 YAML 파일을 만들려면 다음 샘플을 사용할 수 있습니다.

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
    modifyOutputPath: true
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
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

JavaScript 앱을 빌드하는 YAML 파일을 만들려면 다음 샘플을 사용할 수 있습니다.

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
    name: 'drop'
```

#### <a name="python"></a>Python

다음 샘플 Python 앱을 빌드하는 YAML 파일 만들기에 사용할 수 있습니다. Python은 Linux Azure Functions에 대해서만 지원 됩니다.

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
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

PowerShell 앱을 패키지 하기 위한 YAML 파일을 만들려면 다음 샘플을 사용할 수 있습니다. PowerShell은 Windows Azure Functions에 대해서만 지원 됩니다.

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
    name: 'drop'
```

### <a name="deploy-your-app"></a>앱 배포

호스팅 OS에 따라 YAML 파일에 다음 YAML 샘플 중 하나를 포함 해야 합니다.

#### <a name="windows-function-app"></a>Windows 함수 앱

Windows 함수 앱을 배포 하려면 다음 코드 조각을 사용할 수 있습니다.

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

#### <a name="linux-function-app"></a>Linux 함수 앱

Linux 함수 앱을 배포 하려면 다음 코드 조각을 사용할 수 있습니다.

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

Azure DevOps의 서식 파일은 빌드하거나 앱을 배포 하는 작업의 미리 정의 된 그룹입니다.

### <a name="build-your-app"></a>앱 빌드

Azure 파이프라인에서 앱을 빌드하는 방법을 앱의 프로그래밍 언어에 따라 달라 집니다. 각 언어에는 배포 아티팩트를 만들어야 하는 특정 빌드 단계에 있습니다. 배포 아티팩트를 Azure에서 함수 앱을 업데이트 하는 데 사용 됩니다.

새 빌드 파이프라인을 만들 때 기본 제공 빌드 템플릿을 사용 하려면 **클래식 편집기를 사용 하 여** 디자이너 템플릿을 사용 하 여 파이프라인을 만드는 합니다.

![클래식 Azure 파이프라인 편집기를 선택 합니다.](media/functions-how-to-azure-devops/classic-editor.png)

코드의 소스를 구성 하면 Azure Functions에 대 한 검색 서식 파일을 빌드합니다. 앱 언어와 일치 하는 템플릿을 선택 합니다.

![Azure Functions 빌드 템플릿을 선택합니다](media/functions-how-to-azure-devops/build-templates.png)

일부 경우에 빌드 아티팩트에는 특정 폴더 구조가 있습니다. 선택 해야 합니다 **보관 파일 경로 앞에 추가 루트 폴더 이름을** 확인란 합니다.

![루트 폴더 이름 앞에 추가 하는 옵션](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript 앱

JavaScript 앱의 Windows 네이티브 모듈에서 종속성 에이전트 풀 버전을 업데이트 해야 합니다 **호스팅된 VS2017**합니다.

![에이전트 풀 버전 업데이트](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>앱 배포

새 릴리스 파이프라인을 만들 때 Azure Functions 릴리스 템플릿을 검색 합니다.

![Azure Functions 릴리스 템플릿 검색](media/functions-how-to-azure-devops/release-template.png)

배포 슬롯에 배포 릴리스 템플릿에서 지원 되지 않습니다.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 빌드 파이프라인을 만드는

Azure에서 빌드 파이프라인을 만들려면 사용 합니다 `az functionapp devops-pipeline create` [명령](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)입니다. 빌드 파이프라인 빌드 및 릴리스 리포지토리에 적용 된 모든 코드 변경에 만들어집니다. 이 명령은 새 YAML 파일을 빌드 및 릴리스 파이프라인을 정의 하 고 다음 리포지토리에 커밋합니다를 생성 합니다. 이 명령에 대 한 필수 구성 요소 코드의 위치에 따라 달라 집니다.

- 코드를 GitHub의 경우:

    - 있어야 **쓰기** 구독에 대 한 권한.

    - Azure DevOps 프로젝트 관리자 여야 합니다.

    - 충분 한 권한이 있는 GitHub 개인용 액세스 토큰 (PAT)을 만들 수 있는 권한이 있어야 합니다. 자세한 내용은 참조 하세요. [GitHub PAT 권한 요구 사항입니다.](https://aka.ms/azure-devops-source-repos)

    - 자동 생성 된 YAML 파일을 커밋할 수 있도록 GitHub 리포지토리의 마스터 분기에 커밋할 수 있는 권한이 있어야 합니다.

- 코드 리포지토리에서 Azure 경우:

    - 있어야 **쓰기** 구독에 대 한 권한.

    - Azure DevOps 프로젝트 관리자 여야 합니다.

## <a name="next-steps"></a>다음 단계

- 검토 합니다 [Azure Functions 개요](functions-overview.md)합니다.
- 검토 합니다 [Azure DevOps 개요](/azure/devops/pipelines/)합니다.
