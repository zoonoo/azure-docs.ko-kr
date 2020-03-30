---
title: 데이터 수집 파이프라인에 대한 데브옵스
titleSuffix: Azure Machine Learning
description: 모델 학습을 위한 데이터를 준비하는 데 사용되는 데이터 수집 파이프라인 구현에 DevOps 사례를 적용하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247183"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>데이터 수집 파이프라인에 대한 데브옵스

대부분의 시나리오에서 데이터 수집 솔루션은 스크립트, 서비스 호출 및 모든 활동을 오케스트레이션하는 파이프라인의 구성입니다. 이 문서에서는 일반적인 데이터 수집 파이프라인의 개발 수명 주기에 DevOps 사례를 적용하는 방법을 배웁니다. 파이프라인은 기계 학습 모델 학습에 대한 데이터를 준비합니다.

## <a name="the-solution"></a>솔루션

다음 데이터 수집 워크플로를 고려하십시오.

![데이터 수집 파이프라인](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

이 방법에서 학습 데이터는 Azure Blob 저장소에 저장됩니다. Azure Data Factory 파이프라인은 입력 Blob 컨테이너에서 데이터를 가져오고 변환하여 데이터를 출력 Blob 컨테이너에 저장합니다. 이 컨테이너는 Azure 기계 학습 서비스에 대한 [데이터 저장소](concept-data.md) 역할을 합니다. 데이터를 준비하면 Data Factory 파이프라인은 모델을 학습하는 교육 기계 학습 파이프라인을 호출합니다. 이 특정 예제에서는 Azure Databricks 클러스터에서 실행되는 Python 노트북에서 데이터 변환을 수행합니다. 

## <a name="what-we-are-building"></a>우리가 구축하고 있는 것

다른 소프트웨어 솔루션과 마찬가지로, 팀(예: 데이터 엔지니어)이 작업하고 있습니다. 

![cicd 데이터 수집](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Azure 데이터 팩터리, Azure Databricks, Azure 저장소 계정 등과 같은 동일한 Azure 리소스를 공동 작업하고 공유합니다. 이러한 리소스의 컬렉션은 개발 환경입니다. 데이터 엔지니어는 동일한 소스 코드 베이스에 기여합니다. 연속 통합 프로세스는 코드를 어셈블하고 코드 품질 테스트로 확인하고 단위 테스트를 수행하며 테스트된 코드 및 Azure 리소스 관리자 템플릿과 같은 아티팩트를 생성합니다. 연속 배달 프로세스는 아티팩트를 다운스트림 환경에 배포합니다. 이 문서에서는 Azure 파이프라인을 사용하여 CI 및 CD [프로세스를 자동화하는](https://azure.microsoft.com/services/devops/pipelines/)방법을 보여 줍니다.

## <a name="source-control-management"></a>소스 제어 관리

팀 구성원은 Python 노트북 소스 코드및 Azure Data Factory 소스 코드에서 공동 작업을 하기 위해 약간 다른 방식으로 작업합니다. 그러나 두 경우 모두 코드는 소스 제어 리포지토리(예: Azure DevOps, GitHub, GitLab)에 저장되며 공동 작업은 일반적으로 일부 분기 모델(예: [GitFlow)을](https://datasift.github.io/gitflow/IntroducingGitFlow.html)기반으로 합니다.

### <a name="python-notebook-source-code"></a>파이썬 노트북 소스 코드

데이터 엔지니어는 IDE(예: Visual Studio Code)에서 로컬로 또는 Databricks 작업 공간에서 직접 Python 노트북 소스 [코드로](https://code.visualstudio.com)작업합니다. 후자는 개발 환경에서 코드를 디버깅할 수 있는 기능을 제공합니다. 어쨌든 코드는 분기 정책에 따라 리포지토리에 병합됩니다. Jupyter 노트북 형식이 아닌 `.py` 파일에 코드를 저장하는 것이 좋습니다. `.ipynb` CI 프로세스에서 코드 가독성을 향상시키고 자동 코드 품질 검사를 가능하게 합니다.

### <a name="azure-data-factory-source-code"></a>Azure 데이터 팩터리 소스 코드

Azure Data Factory 파이프라인의 소스 코드는 작업 영역에서 생성된 json 파일의 모음입니다. 일반적으로 데이터 엔지니어는 소스 코드 파일이 아닌 Azure Data Factory 작업 영역에서 시각적 디자이너와 함께 작업합니다. [Azure Data Factory 설명서에](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration)설명된 대로 원본 제어 리포지토리를 사용하여 작업 영역을 구성합니다. 이 구성을 통해 데이터 엔지니어는 선호하는 분기 워크플로에 따라 소스 코드에서 공동 작업을 수행할 수 있습니다.    

## <a name="continuous-integration-ci"></a>연속 적분(CI)

연속 통합 프로세스의 궁극적인 목표는 소스 코드에서 공동 팀 작업을 수집하고 다운스트림 환경에 배포할 준비를 하는 것입니다. 소스 코드 관리와 마찬가지로 이 프로세스는 Python 전자 필기장 및 Azure Data Factory 파이프라인에 대해 다릅니다. 

### <a name="python-notebook-ci"></a>파이썬 노트북 CI

Python 노트북의 CI 프로세스는 공동 작업 분기(예: ***마스터*** 또는 ***개발)에서***코드를 받고 다음 작업을 수행합니다.
* 코드 보풀
* Unit Testing
* 코드를 아티팩트로 저장

다음 코드 스니펫은 Azure DevOps ***yaml*** 파이프라인에서 이러한 단계의 구현을 보여 줍니다.

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

파이프라인은 ***flake8을*** 사용하여 파이썬 코드 린팅을 수행합니다. 소스 코드에 정의된 단위 테스트를 실행하고 Linting 및 테스트 결과를 게시하여 Azure 파이프라인 실행 화면에서 사용할 수 있도록 합니다.

![리팅 단위 테스트](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

린팅 및 단위 테스트가 성공하면 파이프라인은 소스 코드를 아티팩트 리포지토리에 복사하여 후속 배포 단계에서 사용할 것입니다.

### <a name="azure-data-factory-ci"></a>Azure 데이터 팩터리 CI

Azure 데이터 팩터리 파이프라인에 대한 CI 프로세스는 데이터 수집 파이프라인에 대한 전체 CI/CD 스토리의 병목 현상입니다. ***지속적인*** 통합이 없습니다. Azure 데이터 팩터리에 대한 배포 가능한 아티팩트는 Azure 리소스 관리자 템플릿의 컬렉션입니다. 이러한 템플릿을 생성하는 유일한 방법은 Azure Data Factory 작업 영역에서 ***게시*** 단추를 클릭하는 것입니다. 여기에 자동화가 없습니다.
데이터 엔지니어는 피처 분기의 소스 코드를 공동 작업 분기(예: ***마스터*** 또는 ***개발)로***병합합니다. 그런 다음 권한 부여가 있는 사용자가 ***게시*** 단추를 클릭하여 공동 작업 분기의 소스 코드에서 Azure Resource Manager 템플릿을 생성합니다. 단추를 클릭하면 작업 영역은 파이프라인의 유효성을 검사하고(보풀 및 단위 테스트로 간주), Azure Resource Manager 템플릿을 생성하고(빌드로 생각) 동일한 코드 리포지토리의 기술 분기 ***adf_publish*** 생성된 템플릿을 저장합니다(게시 아티팩트로 생각). 이 분기는 Azure 데이터 팩터리 작업 영역에 의해 자동으로 만들어집니다. 이 프로세스는 [Azure 데이터 팩터리 설명서의](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)세부 정보에 설명되어 있습니다.

생성된 Azure 리소스 관리자 템플릿이 환경에 구애받지 않는지 확인하는 것이 중요합니다. 즉, 환경 간에 다를 수 있는 모든 값이 매개 변수화됩니다. Azure 데이터 팩터리는 매개 변수와 같은 값의 대부분을 노출할 만큼 스마트합니다. 예를 들어 다음 템플릿에서 Azure 기계 학습 작업 영역에 대한 연결 속성은 매개 변수로 노출됩니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

그러나 기본적으로 Azure Data Factory 작업 영역에서 처리되지 않는 사용자 지정 속성을 노출할 수 있습니다. 이 문서의 시나리오에서는 Azure Data Factory 파이프라인데이터를 처리하는 Python 노트북을 호출합니다. 전자 필기장 입력 데이터 파일의 이름으로 매개 변수를 허용 합니다.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

이 이름은 ***Dev,*** ***QA,*** ***UAT***및 ***PROD*** 환경에 대해 다릅니다. 여러 활동이 있는 복잡한 파이프라인에는 여러 사용자 지정 속성이 있을 수 있습니다. 이러한 모든 값을 한 곳에서 수집하고 파이프라인 변수로 정의하는 것이 ***좋습니다.***

![adf 변수](media/how-to-cicd-data-ingestion/adf-variables.png)

파이프라인 활동은 파이프라인 변수를 실제로 사용하는 동안 파이프라인 변수를 참조할 수 있습니다.

![adf-노트북 매개 변수](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Azure Data Factory 작업 영역은 기본적으로 Azure 리소스 관리자 템플릿 매개 변수로 파이프라인 변수를 ***노출하지 않습니다.*** 작업 영역에서는 Azure Resource Manager 템플릿 매개 변수로 노출해야 하는 파이프라인 속성을 지시하는 [기본 매개 변수화](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) 템플릿을 사용합니다. 목록에 파이프라인 변수를 추가하려면 [기본 매개 변수화 템플릿의](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) "Microsoft.DataFactory/공장/파이프라인" 섹션을 다음 코드 조각으로 업데이트하고 결과 json 파일을 소스 폴더의 루트에 배치합니다.

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

이렇게 하면 게시 ***단추를*** 클릭할 때 Azure Data Factory 작업 영역이 매개 변수 목록에 변수를 추가하도록 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

json 파일의 값은 파이프라인 정의에 구성된 기본값입니다. Azure 리소스 관리자 템플릿을 배포할 때 대상 환경 값으로 재정의될 것으로 예상됩니다.

## <a name="continuous-delivery-cd"></a>연속 전달(CD)

연속 배달 프로세스는 아티팩트를 가져와 첫 번째 대상 환경에 배포합니다. 테스트를 실행하여 솔루션이 작동하는지 확인합니다. 성공하면 다음 환경으로 계속 됩니다. CD Azure 파이프라인은 환경을 나타내는 여러 단계로 구성됩니다. 각 단계에는 다음 단계를 수행하는 배포 및 [작업이](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) [포함됩니다.](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)
* Azure 데이터브릭 작업 영역에 파이썬 노트북 배포
* Azure 데이터 팩터리 파이프라인 배포 
* 파이프라인 실행
* 데이터 수집 결과 확인

파이프라인 단계는 환경 [체인을](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) 통해 배포 프로세스가 어떻게 발전하는지 에 대한 추가 제어를 제공하는 승인 및 [게이트로](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) 구성할 수 있습니다.

### <a name="deploy-a-python-notebook"></a>파이썬 노트북 배포

다음 코드 코드 조각은 Python 노트북을 Databricks 클러스터에 복사하는 Azure 파이프라인 [배포를](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) 정의합니다.

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

CI에서 생성한 아티팩트는 배포 에이전트에 자동으로 복사되며 ***$(Pipeline.Workspace)*** 폴더에서 사용할 수 있습니다. 이 경우 배포 작업은 Python ***노트북을*** 포함하는 디 노트북 아티팩트를 나타냅니다. 이 [배포는](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) [Databricks Azure DevOps 확장프로그램을](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) 사용하여 전자 필기장 파일을 Databricks 작업 영역에 복사합니다.
***Deploy_to_QA*** 단계에는 Azure DevOps 프로젝트에 정의된 ***devops-ds-qa-vg*** 변수 그룹에 대한 참조가 포함되어 있습니다. 이 단계의 단계는 이 변수 그룹의 변수(예: $(DATABRICKS_URL), $(DATABRICKS_TOKEN)를 참조합니다. 아이디어는 다음 단계 (예 : ***Deploy_to_UAT)가***자체 UAT 범위 변수 그룹에 정의 된 동일한 변수 이름으로 작동한다는 것입니다.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Azure 데이터 팩터리 파이프라인 배포

Azure 데이터 팩터리에 대한 배포 가능한 아티팩트는 Azure 리소스 관리자 템플릿입니다. 따라서 다음 코드 에서 보여 줄 수 있는 ***대로 Azure 리소스 그룹 배포*** 작업과 함께 배포 될 예정 입니다.

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
데이터 파일 이름 매개 변수의 값은 QA 단계 변수 그룹에 정의된 $(DATA_FILE_NAME) 변수에서 비롯됩니다. 마찬가지로 ***ARMTemplateForFactory.json에*** 정의된 모든 매개 변수를 재정의할 수 있습니다. 그렇지 않은 경우 기본값이 사용됩니다.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>파이프라인을 실행하고 데이터 수집 결과를 확인합니다.

다음 단계는 배포된 솔루션이 작동하는지 확인하는 것입니다. 다음 작업 정의는 [PowerShell 스크립트를](https://github.com/microsoft/DataOps/tree/master/adf/utils) 사용하여 Azure Data Factory 파이프라인을 실행하고 Azure Databricks 클러스터에서 Python 전자 필기장을 실행합니다. 전자 필기장에서 데이터가 올바르게 수집되었는지 확인하고 $(bin_FILE_NAME) 이름으로 결과 데이터 파일의 유효성을 검사합니다.

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

작업의 마지막 작업은 전자 필기장 실행의 결과를 확인합니다. 오류를 반환하는 경우 파이프라인 실행 상태를 실패로 설정합니다.

## <a name="putting-pieces-together"></a>조각을 함께 넣기

이 문서의 결과는 다음 단계로 구성된 CI/CD Azure 파이프라인입니다.
* CI
* QA에 배포
    * 데이터브릭에 배포 + ADF에 배포
    * 통합 테스트

여기에는 여러 ***가지 배포*** 단계가 있는 대상 환경 수와 동일합니다. 각 ***배포*** 단계에는 병렬로 실행되는 두 [개의 배포와](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) 배포 후 실행되는 [작업이](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) 포함되어 환경에서 솔루션을 테스트합니다.

파이프라인의 샘플 구현은 다음 ***yaml*** 스니펫으로 어셈블됩니다.

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>다음 단계

* [Azure Data Factory의 소스 제어](https://docs.microsoft.com/azure/data-factory/source-control)
* [Azure 데이터 팩터리에서 지속적인 통합 및 제공](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [Azure 데이터 브릭에 대한 데브옵스](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
