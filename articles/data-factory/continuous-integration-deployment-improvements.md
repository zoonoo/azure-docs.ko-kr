---
title: 지속적인 통합 및 배달에 대 한 자동화 된 게시
description: 연속 통합 및 배달을 자동으로 게시 하는 방법에 대해 알아봅니다.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: b5becd8ddaf74ab2acd059054a095ce9d21c178f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366843"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>지속적인 통합 및 배달에 대 한 자동화 된 게시

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>개요

연속 통합은 코드 베이스에 대 한 각 변경 내용을 자동으로 테스트 하는 방법입니다. 가능한 연속 업데이트는 연속 통합 중에 발생 하는 테스트를 따르고 스테이징 또는 프로덕션 시스템에 변경 내용을 푸시합니다.

Azure Data Factory에서 CI/CD(지속적인 통합 및 지속적인 업데이트)는 환경(개발, 테스트, 프로덕션) 간에 Data Factory 파이프라인을 이동하는 것입니다. Azure Data Factory는 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)을 활용하여 다양한 ADF 엔터티(파이프라인, 데이터 세트, 데이터 흐름 등)의 구성을 저장합니다. Data Factory를 다른 환경으로 승격시키는 두 가지 제안된 방법이 있습니다.

- [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)와 Data Factory를 통합 하는 자동화 된 배포
- Azure Resource Manager와 Data Factory UX의 통합을 사용하여 Resource Manager 템플릿을 수동으로 업로드합니다.

자세한 내용은 [Azure Data Factory에서 지속적인 통합 및 배달](continuous-integration-deployment.md)을 참조 하세요.

이 문서에서는 CI/CD에 대 한 지속적인 배포 개선 사항 및 자동화 된 게시 기능에 중점을 둡니다.

## <a name="continuous-deployment-improvements"></a>지속적인 배포 기능 향상

"자동화 된 게시" 기능을 사용 하면 ADF UX에서 모든 *ARM (Azure Resource Manager* *유효성 검사* 및 내보내기) 템플릿 기능을 사용 하 고 공개적으로 사용할 수 있는 npm 패키지를 통해 논리를 사용할 수 있습니다 [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . 이렇게 하면 ADF UI로 이동 하 여 단추를 클릭 하는 대신 프로그래밍 방식으로 이러한 작업을 트리거할 수 있습니다. 그러면 CI/CD 파이프라인이 truer 연속 통합 환경을 제공 합니다.

### <a name="current-cicd-flow"></a>현재 CI/CD 흐름

1. 각 사용자는 개인 분기를 변경 합니다.
2. Master로 푸시는 사용할 수 없습니다. 사용자는 마스터에 대해 PR을 만들어 변경 해야 합니다.
3. 사용자는 ADF UI를 로드 하 고 게시를 클릭 하 여 Data Factory에 변경 내용을 배포 하 고 게시 분기에 ARM 템플릿을 생성 해야 합니다.
4. DevOps 릴리스 파이프라인은 새 릴리스를 만들고 새 변경 내용이 게시 분기로 푸시 될 때마다 ARM 템플릿을 배포 하도록 구성 됩니다.

![현재 CI/CD 흐름](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>수동 단계

현재 CI/CD 흐름에서는 UX를 사용 하 여 ARM 템플릿을 만들 수 있습니다. 따라서 사용자는 ADF UI로 이동한 다음 수동으로 게시를 클릭 하 여 ARM 템플릿 생성을 시작 하 고 게시 분기 (해킹)에 삭제 해야 합니다.

### <a name="the-new-cicd-flow"></a>새 CI/CD 흐름

1. 각 사용자는 개인 분기를 변경 합니다.
2. Master로 푸시는 사용할 수 없습니다. 사용자는 마스터에 대해 PR을 만들어 변경 해야 합니다.
3. **Azure DevOps 파이프라인 빌드는 master에 대 한 새 커밋이 만들어질 때마다 트리거되고, 유효성 검사에 성공 하면 리소스의 유효성을 검사 하 고 ARM 템플릿을 아티팩트로 생성 합니다.**
4. DevOps 릴리스 파이프라인은 새 릴리스를 만들고 새 빌드를 사용할 수 있을 때마다 ARM 템플릿을 배포 하도록 구성 됩니다. 

![새 CI/CD 흐름](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>변경 내용

- 이제 DevOps 빌드 파이프라인을 사용 하는 빌드 프로세스가 있습니다.
- 빌드 파이프라인은 모든 리소스의 유효성을 검사 하 고 ARM 템플릿 (단일 및 연결 된 템플릿)을 생성 하는 ADFUtilities NPM package를 사용 합니다.
- 빌드 파이프라인은 adf 리소스의 유효성을 검사 하 고 ADF UI 대신 ARM 템플릿 (게시 단추)을 생성 합니다.
- 이제 DevOps 릴리스 정의에서 Git 아티팩트 대신이 새 빌드 파이프라인을 사용 합니다.

> [!NOTE]
> 기존 메커니즘 (adf_publish 분기)을 계속 사용 하거나 새 흐름을 사용할 수 있습니다. 둘 다 지원 됩니다. 

## <a name="package-overview"></a>패키지 개요

패키지에서 현재 사용할 수 있는 명령에는 다음 두 가지가 있습니다.
- ARM 템플릿 내보내기
- 유효성 검사

### <a name="export-arm-template"></a>ARM 템플릿 내보내기

Npm 실행 시작 내보내기 <rootFolder> <factoryId> [outputfolder]를 실행 하 여 지정 된 폴더의 리소스를 사용 하 여 ARM 템플릿을 내보냅니다. 이 명령은 ARM 템플릿을 생성 하기 전에 유효성 검사를 실행 합니다. 예제는 아래와 같습니다.

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- RootFolder는 Data Factory 리소스가 있는 위치를 나타내는 필수 필드입니다.
- FactoryId는 "/subscriptions/ <subId> /Sggg//providers/Microsoft.DataFactory/factories/" 형식으로 데이터 팩터리 리소스 ID를 나타내는 필수 필드입니다 <rgName> <dfName> .
- OutputFolder는 생성 된 ARM 템플릿을 저장할 상대 경로를 지정 하는 선택적 매개 변수입니다.
 
> [!NOTE]
> 생성 된 ARM 템플릿은 팩터리의 버전에 게시 되지 않습니다 `Live` . 배포는 CI/CD 파이프라인을 사용 하 여 수행 해야 합니다. 
 

### <a name="validate"></a>유효성 검사

Npm run start validate <rootFolder> <factoryId> 를 실행 하 여 지정 된 폴더의 모든 리소스에 대 한 유효성을 검사 합니다. 예제는 아래와 같습니다.
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- RootFolder는 Data Factory 리소스가 있는 위치를 나타내는 필수 필드입니다.
- FactoryId는 "/subscriptions/ <subId> /Sggg//providers/Microsoft.DataFactory/factories/" 형식으로 데이터 팩터리 리소스 ID를 나타내는 필수 필드입니다 <rgName> <dfName> .


## <a name="create-an-azure-pipeline"></a>Azure 파이프라인 만들기

Npm 패키지는 다양 한 방식으로 사용 될 수 있지만, 주요 이점 중 하나는 [Azure 파이프라인](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0)을 통해 사용 됩니다. 공동 작업 분기에 대 한 각 병합에서는 먼저 모든 코드의 유효성을 검사 한 다음 릴리스 파이프라인에서 사용할 수 있는 [빌드 아티팩트](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) 로 ARM 템플릿을 내보내는 파이프라인을 트리거할 수 있습니다. **현재 CI/CD 프로세스와 어떻게 다른 지는 기존 분기 대신이 아티팩트에서 릴리스 파이프라인을 가리키도록 한다는 것입니다 `adf_publish` .**

시작 하려면 아래 단계를 수행 하세요.

1.  Azure DevOps 프로젝트를 열고 "파이프라인"으로 이동 합니다. "새 파이프라인"을 선택 합니다.

    ![새 파이프라인](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  파이프라인 YAML 스크립트를 저장 하려는 리포지토리를 선택 합니다. ADF 리소스의 동일한 리포지토리 내에 있는 *빌드* 폴더에 저장 하는 것이 좋습니다. 아래 예제와 같이 패키지 이름을 포함 하는 리포지토리에 파일 **에package.js** 있는지 확인 합니다.

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.2"
        }
    } 
    ```
    
3.  *시작 파이프라인* 을 선택 합니다. 아래 예제와 같이 YAML 파일을 업로드 하거나 병합 한 경우 해당 파일을 직접 가리키고 편집할 수도 있습니다. 

    ![시작 파이프라인](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  YAML 코드에를 입력 합니다. YAML 파일을 가져와 시작 지점으로 사용 하는 것이 좋습니다.
5.  을 저장 하 고 실행 합니다. YAML을 사용 하는 경우 "main" 분기가 업데이트 될 때마다 트리거됩니다.

## <a name="next-steps"></a>다음 단계

Data Factory에서 지속적인 통합 및 배달에 대 한 자세한 내용을 알아보세요. 

- [Azure Data Factory에서 지속적인 통합 및 전달](continuous-integration-deployment.md)
