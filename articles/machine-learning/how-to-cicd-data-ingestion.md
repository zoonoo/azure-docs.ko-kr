---
title: 데이터 수집 파이프라인에 대 한 DevOps
titleSuffix: Azure Machine Learning
description: 모델 학습을 위해 데이터를 준비 하는 데 사용 되는 데이터 수집 파이프라인 구현에 DevOps 사례를 적용 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80247183"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>데이터 수집 파이프라인에 대 한 DevOps

대부분의 시나리오에서 데이터 수집 솔루션은 모든 활동을 오케스트레이션 하는 스크립트, 서비스 호출 및 파이프라인에 대 한 컴퍼지션입니다. 이 문서에서는 일반적인 데이터 수집 파이프라인의 개발 수명 주기에 DevOps 사례를 적용 하는 방법에 대해 알아봅니다. 파이프라인은 Machine Learning 모델 학습을 위한 데이터를 준비 합니다.

## <a name="the-solution"></a>솔루션

다음 데이터 수집 워크플로를 고려 하십시오.

![데이터 수집 파이프라인](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

이 방법에서는 학습 데이터가 Azure blob 저장소에 저장 됩니다. Azure Data Factory 파이프라인은 입력 blob 컨테이너에서 데이터를 인출 하 고 변환한 후 출력 blob 컨테이너에 데이터를 저장 합니다. 이 컨테이너는 Azure Machine Learning 서비스에 대 한 [데이터 저장소](concept-data.md) 역할을 합니다. 데이터가 준비 되 면 Data Factory 파이프라인이 학습 Machine Learning 파이프라인을 호출 하 여 모델을 학습 합니다. 이 특정 예제에서 데이터 변환은 Azure Databricks 클러스터에서 실행 되는 Python 노트북에 의해 수행 됩니다. 

## <a name="what-we-are-building"></a>빌드 대상

모든 소프트웨어 솔루션과 마찬가지로이 팀에서 작업 하는 팀 (예: 데이터 엔지니어)이 있습니다. 

![cicd-데이터 수집](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Azure Data Factory, Azure Databricks, Azure Storage 계정과 같은 동일한 Azure 리소스를 공동 작업 하 고 공유 합니다. 이러한 리소스의 컬렉션은 개발 환경입니다. 데이터 엔지니어는 동일한 소스 코드 베이스에 기여 합니다. 연속 통합 프로세스는 코드를 조합 하 고 코드 품질 테스트, 단위 테스트를 사용 하 여 검사 하 고 테스트 된 코드 및 Azure Resource Manager 템플릿과 같은 아티팩트를 생성 합니다. 연속 배달 프로세스는 아티팩트를 다운스트림 환경에 배포 합니다. 이 문서에서는 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)를 사용 하 여 CI 및 CD 프로세스를 자동화 하는 방법을 보여 줍니다.

## <a name="source-control-management"></a>소스 제어 관리

팀 멤버는 Python 노트북 소스 코드와 Azure Data Factory 소스 코드를 공동 작업 하는 데 약간 다른 방식으로 작동 합니다. 그러나 두 경우 모두, 코드는 소스 제어 리포지토리 (예: Azure DevOps, GitHub, GitLab)에 저장 되 고 공동 작업은 일반적으로 일부 분기 모델 (예: [Gitlab](https://datasift.github.io/gitflow/IntroducingGitFlow.html))을 기반으로 합니다.

### <a name="python-notebook-source-code"></a>Python 노트북 소스 코드

데이터 엔지니어가 IDE (예: [Visual Studio Code](https://code.visualstudio.com))에서 로컬로 또는 Databricks 작업 영역에서 직접 Python 노트북 소스 코드를 사용 합니다. 후자는 개발 환경에서 코드를 디버깅 하는 기능을 제공 합니다. 어떤 경우 든 코드는 분기 정책을 따라 리포지토리에 병합 됩니다. Jupyter 노트북 형식이 아닌 파일에 `.py` 코드를 저장 하는 것이 좋습니다. `.ipynb` 코드 가독성을 향상 시키고 CI 프로세스에서 자동 코드 품질 검사를 사용 하도록 설정 합니다.

### <a name="azure-data-factory-source-code"></a>소스 코드 Azure Data Factory

Azure Data Factory 파이프라인의 소스 코드는 작업 영역에 의해 생성 된 json 파일의 컬렉션입니다. 일반적으로 데이터 엔지니어는 소스 코드 파일을 직접 사용 하는 대신 Azure Data Factory 작업 영역에서 비주얼 디자이너와 함께 작업 합니다. [Azure Data Factory 설명서](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration)에 설명 된 대로 소스 제어 리포지토리를 사용 하 여 작업 영역을 구성 합니다. 이 구성을 사용 하는 경우 데이터 엔지니어는 기본 분기 워크플로 다음에 소스 코드에 대해 공동 작업할 수 있습니다.    

## <a name="continuous-integration-ci"></a>CI (연속 통합)

연속 통합 프로세스의 궁극적인 목표는 소스 코드에서 공동 팀 작업을 수집 하 고 다운스트림 환경으로의 배포를 준비 하는 것입니다. 원본 코드 관리와 마찬가지로이 프로세스는 Python 노트북 및 Azure Data Factory 파이프라인에서 다릅니다. 

### <a name="python-notebook-ci"></a>Python 노트북 CI

Python 노트북의 CI 프로세스는 공동 작업 분기 (예: ***마스터*** 또는 ***개발***)에서 코드를 가져오고 다음 작업을 수행 합니다.
* 코드 lint
* 단위 테스트
* 코드를 아티팩트로 저장

다음 코드 조각에서는 Azure DevOps ***yaml*** 파이프라인에서 이러한 단계를 구현 하는 방법을 보여 줍니다.

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

파이프라인은 ***flake8*** 를 사용 하 여 Python 코드 lint를 수행 합니다. 소스 코드에 정의 된 단위 테스트를 실행 하 고 lint 및 테스트 결과를 게시 하 여 Azure 파이프라인 실행 화면에서 사용할 수 있도록 합니다.

![lint-단위 테스트](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Lint 및 단위 테스트가 성공 하면 파이프라인은 이후 배포 단계에서 사용할 아티팩트 리포지토리로 소스 코드를 복사 합니다.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Azure Data Factory 파이프라인에 대 한 CI 프로세스는 데이터 수집 파이프라인에 대 한 전체 CI/CD 스토리에서 병목 상태입니다. ***지속적인*** 통합은 없습니다. Azure Data Factory에 대 한 배포 가능 아티팩트는 Azure Resource Manager 템플릿의 컬렉션입니다. 이러한 템플릿을 생성 하는 유일한 방법은 Azure Data Factory 작업 영역에서 ***게시*** 단추를 클릭 하는 것입니다. 여기에는 자동화가 없습니다.
데이터 엔지니어가 기능 분기의 소스 코드를 ***마스터*** 또는 ***개발과***같은 공동 작업 분기로 병합 합니다. 그런 다음 권한이 부여 된 사용자는 ***게시*** 단추를 클릭 하 여 공동 작업 분기의 소스 코드에서 Azure Resource Manager 템플릿을 생성 합니다. 이 단추를 클릭 하면 작업 영역에서 파이프라인의 유효성을 검사 하 고 (lint 및 유닛 테스트를 기준으로 생각), 템플릿을 Azure Resource Manager 생성 하 여 (빌드 시점으로 생각), 생성 된 템플릿을 동일한 코드 리포지토리의 기술 분기 ***adf_publish*** (게시 아티팩트로 생각)에 저장 합니다. 이 분기는 Azure Data Factory 작업 영역에 의해 자동으로 만들어집니다. 이 프로세스는 [Azure Data Factory 설명서](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)에 자세히 설명 되어 있습니다.

생성 된 Azure Resource Manager 템플릿이 환경에 독립적이 지 않은지 확인 하는 것이 중요 합니다. 즉, 환경 마다 다를 수 있는 모든 값이 매개 변수가 있는 됩니다. Azure Data Factory는 대부분의 값을 매개 변수로 노출할 만큼 지능적입니다. 예를 들어 다음 템플릿에서는 Azure Machine Learning 작업 영역에 대 한 연결 속성이 매개 변수로 노출 됩니다.

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

그러나 기본적으로 Azure Data Factory 작업 영역에서 처리 하지 않는 사용자 지정 속성을 노출 하는 것이 좋습니다. 이 문서의 시나리오에서 Azure Data Factory 파이프라인은 데이터를 처리 하는 Python 노트북을 호출 합니다. 노트북은 입력 데이터 파일의 이름을 포함 하는 매개 변수를 허용 합니다.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

이 이름은 ***Dev***, ***QA***, ***UAT***및 ***PROD*** 환경에서 서로 다릅니다. 여러 작업을 포함 하는 복잡 한 파이프라인에서 여러 사용자 지정 속성을 사용할 수 있습니다. 모든 값을 한 곳에서 수집 하 고 파이프라인 ***변수로***정의 하는 것이 좋습니다.

![adf-변수](media/how-to-cicd-data-ingestion/adf-variables.png)

파이프라인 활동은 실제로 사용 하는 동안 파이프라인 변수를 참조할 수 있습니다.

![adf-노트북-매개 변수](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Azure Data Factory 작업 영역은 파이프라인 변수를 기본적으로 Azure Resource Manager 템플릿 매개 변수로 노출 ***하지 않습니다*** . 작업 영역에서는 Azure Resource Manager 템플릿 매개 변수로 노출 되어야 하는 파이프라인 속성을 지시 하는 [기본 매개 변수화 템플릿을](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) 사용 합니다. 파이프라인 변수를 목록에 추가 하려면 다음 코드 조각을 사용 하 여 [기본 매개 변수화 템플릿의](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) "DataFactory/factory/파이프라인" 섹션을 업데이트 하 고 결과 json 파일을 원본 폴더의 루트에 저장 합니다.

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

이렇게 하면 ***게시*** 단추를 클릭할 때 Azure Data Factory 작업 영역에서 매개 변수 목록에 변수를 강제로 추가 합니다.

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

Json 파일의 값은 파이프라인 정의에 구성 된 기본값입니다. Azure Resource Manager 템플릿이 배포 될 때 대상 환경 값으로 재정의 될 것으로 예상 됩니다.

## <a name="continuous-delivery-cd"></a>지속적인 업데이트 (CD)

연속 배달 프로세스는 아티팩트를 사용 하 여 첫 번째 대상 환경에 배포 합니다. 테스트를 실행 하 여 솔루션이 작동 하는지 확인할 수 있습니다. 성공 하면 다음 환경으로 계속 진행 됩니다. CD Azure 파이프라인은 환경을 나타내는 여러 단계로 구성 됩니다. 각 단계에는 다음 단계를 수행 하는 [배포](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) 및 [작업이](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) 포함 됩니다.
* Azure Databricks 작업 영역에 Python 노트북 배포
* Azure Data Factory 파이프라인 배포 
* 파이프라인 실행
* 데이터 수집 결과 확인

파이프라인 단계는 배포 프로세스가 환경 체인을 통해 진화 하는 방법에 대 한 추가 제어를 제공 하는 [승인](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) 및 [게이트](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) 를 사용 하 여 구성할 수 있습니다.

### <a name="deploy-a-python-notebook"></a>Python 노트북 배포

다음 코드 조각은 Python 노트북을 Databricks 클러스터에 복사 하는 Azure 파이프라인 [배포](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) 를 정의 합니다.

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

CI에 의해 생성 된 아티팩트는 자동으로 배포 에이전트로 복사 되 고 ***$ (Pipeline)*** 폴더에서 사용할 수 있습니다. 이 경우 배포 작업은 Python 노트북을 포함 하는 ***di-노트북*** 아티팩트를 참조 합니다. 이 [배포](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) 에서는 [Databricks Azure devops 확장](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) 을 사용 하 여 노트북 파일을 Databricks 작업 영역에 복사 합니다.
***Deploy_to_QA*** 단계에는 Azure devops 프로젝트에 정의 된 ***devops-vg*** 변수 그룹에 대 한 참조가 포함 되어 있습니다. 이 단계의 단계는이 변수 그룹의 변수를 참조 합니다 (예: $ (DATABRICKS_URL), $ (DATABRICKS_TOKEN)). 그 이유는 다음 단계 (예: ***Deploy_to_UAT***)는 자체 UAT 범위 변수 그룹에 정의 된 것과 동일한 변수 이름을 사용 하 여 작동 한다는 것입니다.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Azure Data Factory 파이프라인 배포

Azure Data Factory에 대 한 배포 가능 아티팩트는 Azure Resource Manager 템플릿입니다. 따라서 다음 코드 조각에 나와 있는 것 처럼 ***Azure 리소스 그룹 배포*** 작업을 통해 배포 될 예정입니다.

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
데이터 파일 이름 매개 변수 값은 QA 스테이지 변수 그룹에 정의 된 $ (DATA_FILE_NAME) 변수에서 제공 됩니다. 마찬가지로 ***ARMTemplateForFactory*** 에 정의 된 모든 매개 변수를 재정의할 수 있습니다. 이러한 값이 없으면 기본값이 사용 됩니다.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>파이프라인을 실행 하 고 데이터 수집 결과를 확인 합니다.

다음 단계는 배포 된 솔루션이 작동 하는지 확인 하는 것입니다. 다음 작업 정의는 [PowerShell 스크립트](https://github.com/microsoft/DataOps/tree/master/adf/utils) 를 사용 하 여 Azure Data Factory 파이프라인을 실행 하 고 Azure Databricks 클러스터에서 Python 노트북을 실행 합니다. 노트북은 데이터가 올바르게 수집 확인 하 고 $ (bin_FILE_NAME) 이름을 사용 하 여 결과 데이터 파일의 유효성을 검사 합니다.

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

작업의 최종 작업은 노트북 실행 결과를 확인 합니다. 오류를 반환 하는 경우 파이프라인 실행 상태를 failed로 설정 합니다.

## <a name="putting-pieces-together"></a>여러 항목 배치

이 문서의 결과는 다음 단계로 구성 된 CI/CD Azure 파이프라인입니다.
* CI
* QA에 배포
    * Databricks + ADF에 배포
    * 통합 테스트

여기에는 보유 하 고 있는 대상 환경의 수와 동일한 수의 ***배포*** 단계가 포함 되어 있습니다. 각 ***배포*** 단계에는 병렬로 실행 되는 두 개의 [배포](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) 와 환경에서 솔루션을 테스트 하기 위해 배포 후 실행 되는 [작업이](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) 포함 됩니다.

파이프라인의 샘플 구현은 다음 ***yaml*** 코드 조각에서 어셈블됩니다.

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
* [Azure Data Factory에서 지속적인 통합 및 전달](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [Azure Databricks에 대 한 DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
