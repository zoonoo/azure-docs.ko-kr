---
title: Azure Pipelines를 사용하여 HPC 솔루션 빌드 및 배포
description: Azure Batch에서 실행되는 HPC 애플리케이션에 대해 빌드/릴리스 파이프라인을 배포하는 방법을 알아봅니다.
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 8ea126637679974035d62cc415c4fd07f597d53f
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111961862"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Azure Pipelines를 사용하여 HPC 솔루션 빌드 및 배포

Azure DevOps에서 제공하는 도구를 사용하면 HPC(고성능 컴퓨팅) 솔루션의 자동화된 빌드 및 테스트를 수행할 수 있습니다. [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)는 소프트웨어를 빌드, 배포, 테스트 및 모니터링하기 위한 다양한 최신 CI(지속적 통합) 및 CD(지속적 배포) 프로세스를 제공합니다. 이러한 프로세스는 소프트웨어 제공을 가속해 주며 지원 인프라와 운영에 신경 쓰지 않고 코드에 집중할 수 있도록 지원합니다.

이 문서에서는 Azure Batch에 배포된 HPC 솔루션에 대해 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)를 사용하여 CI/CD 프로세스를 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 수행하려면 [Azure DevOps 조직](/azure/devops/organizations/accounts/create-organization)이 필요합니다. 또한 [Azure DevOps에서 프로젝트를 생성](/azure/devops/organizations/projects/create-project)해야 합니다.

시작하기 전에 [원본 제어](/azure/devops/user-guide/source-control) 및 [Azure Resource Manager 템플릿 구문](../azure-resource-manager/templates/syntax.md)에 대한 기본적인 이해가 있으면 도움이 됩니다.

## <a name="create-an-azure-pipeline"></a>Azure Pipelines 만들기

이 예제에서는 Azure Batch 인프라를 배포하고 애플리케이션 패키지를 릴리스하기 위한 빌드 및 릴리스 파이프라인을 만듭니다. 코드가 로컬에서 개발된다고 가정했을 때 일반적인 배포 흐름은 다음과 같습니다.

![파이프라인의 배포 흐름을 보여 주는 다이어그램](media/batch-ci-cd/DeploymentFlow.png)

이 샘플에서는 여러 Azure Resource Manager 템플릿 및 기존 이진 파일을 사용합니다. 예제를 리포지토리에 복사하여 Azure DevOps로 푸시할 수 있습니다.

### <a name="understand-the-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 이해

이 예에서는 여러 Azure Resource Manager 템플릿을 사용하여 솔루션을 배포합니다. 세 가지 기능 템플릿(단위 또는 모듈과 유사)은 특정 기능을 구현하는 데 사용됩니다. 그런 다음, 엔드투엔드 솔루션 템플릿(deployment.json)을 사용하여 이러한 기본 기능 템플릿을 배포합니다. 이 [연결된 템플릿 구조](../azure-resource-manager/templates/deployment-tutorial-linked-template.md)를 사용하면 각 기능 템플릿을 개별적으로 테스트하고 여러 솔루션에서 재사용할 수 있습니다.

![Azure Resource Manager 템플릿을 사용하는 연결된 템플릿 구조를 보여 주는 다이어그램](media/batch-ci-cd/ARMTemplateHierarchy.png)

이 템플릿은 Batch 계정에 애플리케이션을 배포하는 데 필요한 Azure Storage 계정을 정의합니다. 자세한 내용은 [Microsoft.Storage 리소스 유형에 대한 Resource Manager 템플릿 참조 가이드](/azure/templates/microsoft.storage/allversions)를 참조하세요.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

다음 템플릿은 [Azure Batch 계정](accounts.md)을 정의합니다. Batch 계정은 [풀](nodes-and-pools.md#pools)에서 수많은 애플리케이션을 실행하는 플랫폼 역할을 합니다. 자세한 내용은 [Microsoft.Batch 리소스 유형에 대한 Resource Manager 템플릿 참조 가이드](/azure/templates/microsoft.batch/allversions)를 참조하세요.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

다음 템플릿은 Batch 계정에 Batch 풀을 만듭니다. 자세한 내용은 [Microsoft.Batch 리소스 유형에 대한 Resource Manager 템플릿 참조 가이드](/azure/templates/microsoft.batch/allversions)를 참조하세요.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

최종 템플릿은 세 가지 기본 기능 템플릿을 배포하는 오케스트레이터 역할을 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="understand-the-hpc-solution"></a>HPC 솔루션 이해

앞서 언급했듯이 이 샘플에서는 여러 Azure Resource Manager 템플릿 및 기존 이진 파일을 사용합니다. 예제를 리포지토리에 복사하여 Azure DevOps로 푸시할 수 있습니다.

이 솔루션에서 ffmpeg는 애플리케이션 패키지로 사용됩니다. 아직 없는 경우 [ffmpeg 패키지를 다운로드](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08)할 수 있습니다.

![리포지토리 구조의 스크린샷](media/batch-ci-cd/git-repository.jpg)

이 리포지토리는 네 개의 주요 섹션으로 구성됩니다.

- Azure Resource Manager 템플릿이 포함된 **arm-templates** 폴더
- Windows 64비트 버전의 [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08)이 포함된 **hpc-application** 폴더
- 빌드 파이프라인 프로세스를 정의하는 YAML 파일이 포함된 **pipelines** 폴더
- 선택 사항: [ffmpeg를 사용한 Azure Batch .NET 파일 처리](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) 샘플의 복사본인 **client-application** 폴더 이 문서에는 이 애플리케이션이 필요하지 않습니다.


> [!NOTE]
> 이는 코드 기반 구조의 한 가지 예입니다. 이 접근 방식은 애플리케이션, 인프라 및 파이프라인 코드가 동일한 리포지토리에 저장됨을 보여 주기 위해 사용됩니다.

소스 코드를 설정했으니 이제 첫 번째 빌드를 시작할 수 있습니다.

## <a name="continuous-integration"></a>연속 통합

Azure DevOps Services의 [Azure Pipelines](/azure/devops/pipelines/get-started/)를 사용하면 애플리케이션의 빌드, 테스트 및 배포 파이프라인을 구현하는 데 도움이 됩니다.

파이프라인의 이 단계에서는 일반적으로 코드를 검증하고 소프트웨어의 적절한 부분을 빌드하기 위해 테스트가 실행됩니다. 테스트의 개수와 유형, 그리고 실행해야 하는 추가 작업은 전체 빌드 및 릴리스 전략에 따라 달라집니다.

## <a name="prepare-the-hpc-application"></a>HPC 애플리케이션 준비

이 섹션에서는 **hpc-application** 폴더를 사용합니다. 이 폴더에는 Azure Batch 계정 내에서 실행될 소프트웨어(ffmpeg)가 포함되어 있습니다.

1. Azure DevOps 조직에서 Azure Pipelines의 빌드 섹션으로 이동합니다. **새 파이프라인** 을 만듭니다.

    ![새 파이프라인 화면의 스크린샷](media/batch-ci-cd/new-build-pipeline.jpg)

1. 빌드 파이프라인을 만드는 두 가지 옵션이 있습니다.

    a. [비주얼 디자이너 사용](/azure/devops/pipelines/get-started-designer). 이렇게 하려면 **새 파이프라인** 페이지에서 "비주얼 디자이너 사용"을 선택합니다.

    b. [YAML 빌드를 사용합니다](/azure/devops/pipelines/get-started-yaml). **새 파이프라인** 페이지에서 Azure Repos 또는 GitHub 옵션을 클릭하여 새 YAML 파이프라인을 만들 수 있습니다. 또는 아래 예제를 소스 제어에 저장하고 비주얼 디자이너를 선택하여 기존 YAML 파일을 참조한 다음 YAML 템플릿을 사용할 수도 있습니다.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. 빌드를 필요에 따라 구성한 후에는 **저장 및 큐** 를 선택합니다. **트리거** 섹션에서 연속 통합을 사용하도록 설정한 경우, 리포지토리에 빌드에 설정된 조건을 충족하는 새 커밋이 적용되면 빌드가 자동으로 트리거됩니다.

    ![기존 빌드 파이프라인의 스크린샷](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Azure Pipelines의 **빌드** 섹션으로 이동하여 Azure DevOps에서 진행되는 빌드의 진행 상황을 라이브 업데이트로 봅니다. 빌드 정의에서 해당 빌드를 선택합니다.

    ![Azure DevOps 빌드의 라이브 출력 스크린샷](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> 클라이언트 애플리케이션을 사용하여 HPC 솔루션을 실행하는 경우, 이 애플리케이션에 대한 별도의 빌드 정의를 만들어야 합니다. [Azure Pipelines](/azure/devops/pipelines/get-started/index) 설명서에서 방법 가이드를 확인할 수 있습니다.

## <a name="continuous-deployment"></a>연속 배포

Azure Pipelines는 애플리케이션과 기본 인프라를 배포하는 데도 사용됩니다. [릴리스 파이프라인](/azure/devops/pipelines/release)은 지속적인 배포를 가능하게 하고 릴리스 프로세스를 자동화합니다.

### <a name="deploy-your-application-and-underlying-infrastructure"></a>애플리케이션 및 기본 인프라 배포

인프라 배포를 진행하려면 몇 가지 단계가 수행됩니다. 이 솔루션은 [연결된 템플릿](../azure-resource-manager/templates/linked-templates.md)을 사용하기 때문에 이러한 템플릿은 공용 엔드포인트(HTTP 또는 HTTPS)에서 액세스할 수 있어야 합니다. 퍼블릭 엔드포인트는 GitHub의 리포지토리, Azure Blob Storage 계정 또는 다른 스토리지 위치일 수 있습니다. 업로드한 템플릿 아티팩트는 프라이빗 모드로 보관되나 SAS(공유 액세스 서명) 토큰으로 액세스되므로 안전하게 유지됩니다.

다음 예제에서는 Azure Storage Blob에서 템플릿을 사용하여 인프라를 배포하는 방법을 보여 줍니다.

1. **새 릴리스 정의** 를 만든 다음 빈 정의를 선택합니다. 새로 만든 환경에 파이프라인과 관련 있는 이름을 지정해야 합니다.

    ![초기 릴리스 파이프라인 스크린샷](media/batch-ci-cd/Release-0.jpg)

1. HPC 애플리케이션의 출력을 얻기 위해 빌드 파이프라인에 대해 종속성을 만듭니다.

    > [!NOTE]
    > 릴리스 정의 내에서 작업이 생성될 때 필요하므로 **원본 별칭** 을 기록해 둡니다.

    ![해당 빌드 파이프라인에서 HPCApplicationPackage에 대한 아티팩트 링크를 보여 주는 스크린샷](media/batch-ci-cd/Release-1.jpg)

1. 다른 아티팩트(Azure Repo)에 대한 링크를 만듭니다. 이 링크는 리포지토리에 저장된 Resource Manager 템플릿에 액세스하는 데 필요합니다. Resource Manager 템플릿에는 컴파일이 필요하지 않으므로 빌드 파이프라인을 통과하도록 푸시하지 않아도 됩니다.

    > [!NOTE]
    > 나중에 필요하므로 **원본 별칭** 을 다시 한 번 확인합니다.

    ![Azure Repos에 대한 아티팩트 링크를 보여 주는 스크린샷](media/batch-ci-cd/Release-2.jpg)

1. **변수** 섹션으로 이동합니다. 동일한 정보를 여러 작업에 다시 입력할 필요가 없도록 파이프라인에 여러 변수를 만들려고 합니다. 이 예제에서는 다음 변수를 사용합니다.

   - **applicationStorageAccountName**: HPC 애플리케이션 이진 파일을 보유하는 스토리지 계정의 이름
   - **batchAccountApplicationName**: Batch 계정의 애플리케이션 이름
   - **batchAccountName**: Batch 계정의 이름
   - **batchAccountPoolName**: 처리를 수행하는 VM의 풀 이름
   - **batchApplicationId**: Batch 애플리케이션의 고유 ID
   - **batchApplicationVersion**: 배치 애플리케이션(즉, ffmpeg 이진 파일)의 의미론적 버전
   - **location**: Azure 리소스를 배포할 위치
   - **resourceGroupName**: 만들려는 리소스 그룹의 이름과 리소스를 배포할 위치
   - **storageAccountName**: 연결된 Resource Manager 템플릿을 보유하는 스토리지 계정의 이름

   ![Azure Pipelines 릴리스에 대해 설정된 변수를 보여 주는 스크린샷](media/batch-ci-cd/Release-4.jpg)

1. 개발 환경의 작업으로 이동합니다. 아래 스냅샷에서는 여섯 개의 작업을 볼 수 있습니다. 해당 작업은 압축된 ffmpeg 파일을 다운로드하고, 중첩된 Resource Manager 템플릿을 저장할 스토리지 계정을 배포하고, Resource Manager 템플릿을 스토리지 계정으로 복사하고, Batch 계정 및 필요한 종속성을 배포하고, Azure Batch 계정에 애플리케이션을 만들고, 애플리케이션 패키지를 Azure Batch 계정으로 업로드합니다.

    ![HPC 애플리케이션을 Azure Batch로 릴리스하는 데 사용되는 작업을 보여 주는 스크린샷](media/batch-ci-cd/Release-3.jpg)

1. **파이프라인 아티팩트 다운로드(미리 보기)** 작업을 추가하고 다음 속성을 설정합니다.
    - **표시 이름**: ApplicationPackage를 에이전트로 다운로드
    - **다운로드할 아티팩트의 이름:** hpc-application
    - **다운로드 경로**: $(System.DefaultWorkingDirectory)

1. 스토리지 계정을 만들어 Azure Resource Manager 템플릿을 저장합니다. 솔루션의 기존 스토리지 계정을 사용할 수 있지만 이 자체 포함된 샘플 및 콘텐츠 격리를 지원하기 위해 전용 스토리지 계정을 만듭니다.

    **Azure 리소스 그룹 배포** 작업을 추가하고 다음 속성을 설정합니다.
    - **표시 이름**: Resource Manager 템플릿을 위한 스토리지 계정 배포
    - **Azure 구독**: 적절한 Azure 구독 선택
    - **작업**: 리소스 그룹을 만들기 또는 업데이트
    - **리소스 그룹**: $(resourceGroupName)
    - **위치**: $(location)
    - **템플릿**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    - **템플릿 매개 변수 재정의**: -accountName $(storageAccountName)

1. Azure Pipelines를 사용하여 원본 제어의 아티팩트를 스토리지 계정에 업로드합니다. 이 Azure Pipelines 작업의 일부로 스토리지 계정 컨테이너 URI 및 SAS 토큰을 Azure Pipelines 변수에 출력하여 이 에이전트 단계 전체에서 다시 사용할 수 있습니다.

    **Azure 파일 복사** 작업을 추가하고 다음 속성을 설정합니다.
    - **원본:** $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    - **Azure 연결 형식**: Azure 리소스 관리자
    - **Azure 구독**: 적절한 Azure 구독 선택
    - **대상 유형**: Azure Blob
    - **RM 스토리지 계정**: $(storageAccountName)
    - **컨테이너 이름**: templates
    - **스토리지 컨테이너 URI**: templateContainerUri
    - **스토리지 컨테이너 SAS 토큰**: templateContainerSasToken

1. 오케스트레이터 템플릿을 배포합니다. 이 템플릿에는 스토리지 계정 컨테이너 URI 및 SAS 토큰에 대한 매개 변수가 포함됩니다. Resource Manager 템플릿에 필요한 변수는 릴리스 정의의 변수 섹션에 있거나 다른 Azure Pipelines 작업에서 설정되었습니다(예: Azure Blob 복사 작업의 일부로).

    **Azure 리소스 그룹 배포** 작업을 추가하고 다음 속성을 설정합니다.
    - **표시 이름**: Azure Batch 배포
    - **Azure 구독**: 적절한 Azure 구독 선택
    - **작업**: 리소스 그룹을 만들기 또는 업데이트
    - **리소스 그룹**: $(resourceGroupName)
    - **위치**: $(location)
    - **템플릿**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    - **템플릿 매개 변수 재정의**: `-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   일반적인 방법은 Azure Key Vault 작업을 사용하는 것입니다. Azure 구독에 연결된 서비스 주체에 적절한 액세스 정책이 설정되어 있다면, 서비스 주체가 Azure Key Vault에서 비밀을 다운로드하여 파이프라인에서 이를 변수로 사용할 수 있습니다. 비밀의 이름은 해당 값으로 설정됩니다. 예를 들어, sshPassword의 비밀은 릴리스 정의에서 $(sshPassword)로 참조할 수 있습니다.

1. 다음 단계는 Azure CLI를 호출하는 것입니다. 첫 번째는 Azure Batch에서 애플리케이션을 만들고 관련 패키지를 업로드하는 데 사용됩니다.

    **Azure CLI** 작업을 추가하고 다음 속성을 설정합니다.
    - **표시 이름**: Azure Batch 계정에서 애플리케이션 만들기
    - **Azure 구독**: 적절한 Azure 구독 선택
    - **스크립트 위치**: 인라인 스크립트
    - **인라인 스크립트**: `az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. 두 번째 단계는 애플리케이션에 연결된 패키지를 업로드하는 데 사용됩니다(이 경우 ffmpeg 파일).

    **Azure CLI** 작업을 추가하고 다음 속성을 설정합니다.
    - **표시 이름**: Azure Batch 계정으로 패키지 업로드
    - **Azure 구독**: 적절한 Azure 구독 선택
    - **스크립트 위치**: 인라인 스크립트
    - **인라인 스크립트**: `az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > 애플리케이션 패키지의 버전 번호는 변수로 설정됩니다. 이렇게 하면 패키지의 이전 버전을 덮어쓸 수 있으며 Azure Batch에 푸시된 패키지의 버전 번호를 수동으로 제어할 수 있습니다.

1. **릴리스 > 새 릴리스 만들기** 를 선택하여 새 릴리스를 만듭니다. 트리거되면 새 릴리스에 대한 링크를 선택하여 상태를 확인합니다.

1. 사용자 환경 아래에서 **로그** 단추를 선택하여 에이전트의 라이브 출력을 봅니다.

    ![릴리스 상태를 보여 주는 스크린샷](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>환경 테스트

환경을 설정했으면 다음 테스트가 성공적으로 완료될 수 있는지 확인합니다.

PowerShell 명령 프롬프트에서 Azure CLI를 사용하여 새 Azure Batch 계정에 접속합니다.

- `az login`을 사용하여 Azure 계정에 로그인하고 지침에 따라 인증합니다.
- 이제 Batch 계정을 인증합니다. `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>사용 가능한 애플리케이션 나열

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>풀이 유효한지 확인

```azurecli
az batch pool list
```

이 명령의 출력에서 `currentDedicatedNodes`의 값을 살펴봅니다. 이 값은 다음 테스트에서 조정됩니다.

#### <a name="resize-the-pool"></a>풀 크기 조정

작업 및 태스크 테스트에 사용할 수 있는 컴퓨팅 노드가 준비되도록 풀의 크기를 조정합니다. 크기 조정이 완료되고 사용 가능한 노드가 생길 때까지 pool list 명령을 사용하여 현재 상태를 확인합니다.

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>다음 단계

간단한 애플리케이션을 통해 Batch 계정과 상호 작용하는 방법을 알아보려면 이 자습서를 참조하세요.

- [Python API를 사용하여 Azure Batch에서 병렬 워크로드 실행](tutorial-parallel-python.md)
- [.NET API를 사용하여 Azure Batch에서 병렬 워크로드 실행](tutorial-parallel-dotnet.md)