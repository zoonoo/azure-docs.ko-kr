---
title: Azure DevOps를 사용 하 여 함수 코드 업데이트를 지속적으로 제공
description: Azure Functions를 대상으로 하는 Azure DevOps 파이프라인을 설정 하는 방법에 알아봅니다.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: 9806a982982971b1b3ac9c28454e17813b2ad2a5
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479878"
---
# <a name="continuous-delivery-using-azure-devops"></a>Azure DevOps를 사용한 연속 배달

사용 하 여 Azure 함수 앱에 함수를 자동으로 배포할 수 있습니다 [Azure 파이프라인](/azure/devops/pipelines/)합니다.
파이프라인을 정의 하려면 다음을 사용할 수 있습니다.

- YAML 파일: 빌드 단계 섹션과 릴리스 섹션 있을,이 파일은 파이프라인을 설명 합니다. YAML 파일을 앱과 같은 리포지토리 여야 합니다.

- 템플릿: 템플릿은 준비 빌드 또는 앱을 배포 하는 작업을 수행 합니다.

## <a name="yaml-based-pipeline"></a>YAML 기반 파이프라인

### <a name="build-your-app"></a>앱 빌드

Azure 파이프라인에서 앱을 빌드하는 앱의 프로그래밍 언어에 따라 달라 집니다.
각 언어에는 Azure에서 함수 앱 배포에 사용할 수 있는 배포 아티팩트를 만들어야 하는 특정 빌드 단계에 있습니다.

#### <a name="net"></a>.NET

.NET 앱을 작성 하기 위해 YAML 파일을 만들려면 다음 샘플을 사용할 수 있습니다.

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

JavaScript 앱을 작성 하기 위해 YAML 파일을 만들려면 다음 샘플을 사용할 수 있습니다.

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

다음 샘플을 사용 하 여 Python 앱을 빌드하려면 YAML 파일 만들기, Python Linux Azure Functions 에서만 지원 됩니다.

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

다음 샘플을 사용 하 여 PowerShell 앱을 패키지 하 여 YAML 파일을 만들 수 있습니다, 그리고 PowerShell은 Windows Azure Functions에만 지원 됩니다.

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

호스팅 OS에 따라 YAML 파일에 다음 YAML 샘플을 포함 해야 합니다.

#### <a name="windows-function-app"></a>Windows 함수 앱

Windows 함수 앱을 배포 하려면 다음 코드 조각 수 있습니다.

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

Linux 함수 앱을 배포 하려면 다음 코드 조각 수 있습니다.

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

Azure 파이프라인에서 앱을 빌드하는 앱의 프로그래밍 언어에 따라 달라 집니다. 각 언어에는 Azure에서 함수 앱 업데이트를 사용할 수 있는 배포 아티팩트를 만들어야 하는 특정 빌드 단계에 있습니다.
새 빌드 파이프라인을 만들 때 기본 제공 빌드 템플릿을 사용 하려면 **클래식 편집기를 사용 하 여** 디자이너 템플릿을 사용 하 여 파이프라인을 만들려면

![파이프라인의 azure 클래식 편집기](media/functions-how-to-azure-devops/classic-editor.png)

코드의 소스를 구성 했으면 Azure Functions 빌드 템플릿을 검색 하 고 앱 언어와 일치 하는 템플릿을 선택 합니다.

![Azure Functions 빌드 템플릿](media/functions-how-to-azure-devops/build-templates.png)

일부 경우에 빌드 아티팩트를 특정 폴더 구조가 있고 확인 해야 합니다 **보관 파일 경로에 루트 폴더 이름 앞에 추가** 옵션입니다.

![루트 폴더를 추가 합니다.](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript 앱

JavaScript 앱 Windows 네이티브 모듈에 대 한 종속성을 업데이트 해야 합니다.

- 에이전트 풀 버전을 **호스팅된 VS2017**

  ![OS 빌드 에이전트를 변경 합니다.](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>앱 배포

새 릴리스 파이프라인을 만들 때 Azure Functions 릴리스 템플릿을 검색 합니다.

![](media/functions-how-to-azure-devops/release-template.png)

배포 슬롯에 배포 릴리스 템플릿에서 지원 되지 않습니다.

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Azure CLI를 사용 하는 Azure 파이프라인 만들기

사용 하는 `az functionapp devops-pipeline create` [명령](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create), Azure 파이프라인을 빌드 및 릴리스 리포지토리의 모든 코드 변경 내용에 생성 됩니다. 명령은 빌드 및 릴리스 파이프라인을 정의 하는 새 YAML 파일을 생성 하 고 리포지토리에 커밋 됩니다. 배포 슬롯에 배포 된 Azure CLI 명령에 의해 지원 되지 않습니다.
이 명령에 대 한 필수 조건 코드의 위치에 따라 달라 집니다.

- 코드를 GitHub의 경우:

    - 있어야 **쓰기** 구독에 대 한 사용 권한.

    - Azure devops에서 프로젝트 관리자가.

    - 충분 한 권한이 있는 GitHub 개인 액세스 토큰을 만드는 권한이 있습니다. [GitHub PAT 권한 요구 사항입니다.](https://aka.ms/azure-devops-source-repos)

    - 자동 생성 된 YAML 파일을 커밋하려면 GitHub 리포지토리의 마스터 분기에 대 한 커밋 권한이 있습니다.

- 코드 리포지토리에서 Azure 경우:

    - 있어야 **쓰기** 구독에 대 한 사용 권한.

    - Azure devops에서 프로젝트 관리자가.

## <a name="next-steps"></a>다음 단계

+ [Azure 함수 개요](functions-overview.md)
+ [Azure DevOps 개요](/azure/devops/pipelines/)
