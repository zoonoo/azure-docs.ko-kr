---
title: Azure 파이프라인을 사용하여 hPC 솔루션 배포 & 빌드 - Azure Batch
description: Azure Batch에서 실행되는 HPC 응용 프로그램에 대한 빌드/릴리스 파이프라인을 배포하는 방법에 대해 알아봅니다.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 50cb711dfd16c2a8718d13ba9255ace1e7e3e26d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533133"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Azure 파이프라인을 사용하여 HPC 솔루션 빌드 및 배포

Azure DevOps 서비스는 사용자 지정 응용 프로그램을 빌드할 때 개발 팀에서 사용하는 다양한 도구를 제공합니다. Azure DevOps에서 제공하는 도구는 고성능 컴퓨팅 솔루션의 자동화된 빌드 및 테스트로 변환될 수 있습니다. 이 문서에서는 Azure Batch에 배포된 고성능 컴퓨팅 솔루션에 Azure 파이프라인을 사용하여 CI(연속 통합) 및 CD(연속 배포)를 설정하는 방법을 보여 줍니다.

Azure 파이프라인은 소프트웨어 빌드, 배포, 테스트 및 모니터링을 위한 다양한 최신 CI/CD 프로세스를 제공합니다. 이러한 프로세스는 소프트웨어 제공을 가속화하여 인프라 및 운영을 지원하는 대신 코드에 집중할 수 있도록 합니다.

## <a name="create-an-azure-pipeline"></a>Azure 파이프라인 만들기

이 예제에서는 Azure Batch 인프라를 배포하고 응용 프로그램 패키지를 릴리스하는 빌드 및 릴리스 파이프라인을 만듭니다. 코드가 로컬로 개발된 경우 는 일반적인 배포 흐름입니다.

![파이프라인의 배포 흐름을 보여주는 다이어그램](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>설치 프로그램

이 문서의 단계를 수행하려면 Azure DevOps 조직과 팀 프로젝트가 필요합니다.

* [Azure DevOps 조직 만들기](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Azure DevOps에서 프로젝트 만들기](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>사용자 환경에 대한 소스 제어

소스 제어를 통해 팀은 코드베이스에 대한 변경 내용을 추적하고 이전 버전의 코드를 검사할 수 있습니다.

일반적으로 소스 제어는 소프트웨어 코드와 함께 하는 것으로 생각됩니다. 기본 인프라는 어떻습니까? 이렇게 하면 Azure 리소스 관리자 템플릿 또는 기타 오픈 소스 대안을 사용하여 기본 인프라를 선언적으로 정의하는 코드로 인프라가 됩니다.

이 샘플은 JSON 문서(JSON 문서) 및 기존 바이너리의 수에 크게 의존합니다. 이러한 예제를 리포지토리에 복사하여 Azure DevOps로 푸시할 수 있습니다.

이 샘플에 사용되는 코드베이스 구조는 다음과 유사합니다.

* 여러 Azure 리소스 관리자 **템플릿을** 포함하는 팔 템플릿 폴더입니다. 템플릿은 이 문서에서 설명합니다.
* ffmpeg 샘플이 있는 [Azure Batch .NET 파일 처리의](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) 복사본인 **클라이언트 응용 프로그램** 폴더입니다. 이 문서에는 필요하지 않습니다.
* [ffmpeg 3.4의](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)윈도우 64 비트 버전인 **Hpc 응용 프로그램** 폴더 .
* **파이프라인 폴더입니다.** 여기에는 빌드 프로세스를 요약한 YAML 파일이 포함되어 있습니다. 이 문서에서 설명 합니다.

이 섹션에서는 버전 제어 및 리소스 관리자 템플릿 디자인에 익숙하다고 가정합니다. 이러한 개념에 익숙하지 않은 경우 자세한 내용은 다음 페이지를 참조하세요.

* [소스 제어란 무엇입니까?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Azure 리소스 관리자 템플릿의 구조 및 구문 이해](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

이 예제는 여러 리소스 관리자 템플릿을 활용하여 솔루션을 배포합니다. 이를 위해 특정 기능을 구현하는 여러 기능 템플릿(단위 또는 모듈과 유사)을 사용합니다. 또한 이러한 기본 기능을 함께 가져오는 일용종 솔루션 템플릿을 사용합니다. 이 접근 방식에는 다음과 같은 몇 가지 이점이 있습니다.

* 기본 기능 템플릿은 개별적으로 단위 테스트할 수 있습니다.
* 기본 기능 템플릿은 조직 내부의 표준으로 정의할 수 있으며 여러 솔루션에서 다시 사용할 수 있습니다.

이 예제에서는 세 개의 템플릿을 배포하는 종단 간 솔루션 템플릿(deployment.json)이 있습니다. 기본 템플릿은 솔루션의 특정 측면을 배포하는 기능 템플릿입니다.

![Azure 리소스 관리자 템플릿을 사용하여 연결된 템플릿 구조의 예](media/batch-ci-cd/ARMTemplateHierarchy.png)

살펴볼 첫 번째 템플릿은 Azure 저장소 계정입니다. 이 솔루션에는 응용 프로그램을 일괄 처리 계정에 배포하려면 저장소 계정이 필요합니다. 저장소 계정에 대한 리소스 관리자 템플릿을 빌드할 때 [Microsoft.Storage 리소스 유형에](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) 대한 리소스 관리자 템플릿 참조 가이드를 알고 있는 것이 좋습니다.

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

다음으로 Azure 일괄 처리 계정 템플릿을 살펴보겠습니다. Azure Batch 계정은 풀(컴퓨터 그룹화)에서 수많은 응용 프로그램을 실행하는 플랫폼 역할을 합니다. 배치 계정에 대한 리소스 관리자 템플릿을 빌드할 때 [Microsoft.Batch 리소스 유형에 대한](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) 리소스 관리자 템플릿 참조 가이드를 알고 있는 것이 좋습니다.

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

다음 템플릿은 Azure 일괄 처리 풀(응용 프로그램을 처리하는 백 엔드 컴퓨터)을 만드는 예제를 보여 줍니다. 일괄 처리 계정 풀에 대한 리소스 관리자 템플릿을 빌드할 때 [Microsoft.Batch 리소스 유형에](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) 대한 리소스 관리자 템플릿 참조 가이드를 알고 있는 것이 좋습니다.

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

마지막으로 오케스트레이터와 유사한 역할을 하는 템플릿이 있습니다. 이 템플릿은 기능 템플릿을 배포하는 작업을 담당합니다.

또한 별도의 문서에서 [연결된 Azure 리소스 관리자 템플릿을 만드는](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) 것에 대해 자세히 알아볼 수 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
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

#### <a name="the-hpc-solution"></a>HPC 솔루션

인프라와 소프트웨어는 코드로 정의하고 동일한 리포지토리에 배치할 수 있습니다.

이 솔루션의 경우 ffmpeg가 응용 프로그램 패키지로 사용됩니다. ffmpeg 패키지는 [여기에서](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)다운로드 할 수 있습니다.

![Git 리포지토리 구조 예제](media/batch-ci-cd/git-repository.jpg)

이 리포지토리에는 네 가지 주요 섹션이 있습니다.

* 인프라를 코드로 저장하는 **팔 템플릿** 폴더
* ffmpeg에 대한 바이너리를 포함하는 **hpc 응용 프로그램** 폴더
* 빌드 **pipelines** 파이프라인에 대한 정의가 포함된 파이프라인 폴더입니다.
* **선택 사항**: .NET 응용 프로그램에 대한 코드를 저장하는 **클라이언트 응용 프로그램** 폴더입니다. 샘플에서는 이 것을 사용하지 않지만 자체 프로젝트에서 클라이언트 응용 프로그램을 통해 HPC 배치 응용 프로그램의 실행을 실행할 수 있습니다.

> [!NOTE]
> 이것은 코드베이스에 대한 구조의 한 예일 뿐입니다. 이 방법은 응용 프로그램, 인프라 및 파이프라인 코드가 동일한 리포지토리에 저장된다는 것을 입증하기 위한 용도로 사용됩니다.

이제 소스 코드가 설정되었으므로 첫 번째 빌드를 시작할 수 있습니다.

## <a name="continuous-integration"></a>연속 통합

Azure DevOps 서비스 내에서 [Azure 파이프라인은](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)응용 프로그램에 대한 빌드, 테스트 및 배포 파이프라인을 구현하는 데 도움이 됩니다.

파이프라인의 이 단계에서는 일반적으로 코드의 유효성을 검사하고 소프트웨어의 적절한 부분을 빌드하기 위해 테스트를 실행합니다. 테스트의 수와 유형 및 실행하는 추가 작업은 광범위한 빌드 및 릴리스 전략에 따라 달라집니다.

## <a name="preparing-the-hpc-application"></a>HPC 응용 프로그램 준비

이 예제에서는 **hpc 응용 프로그램** 폴더에 중점을 둡니다. **hpc-응용 프로그램** 폴더는 Azure Batch 계정 내에서 실행되는 ffmpeg 소프트웨어입니다.

1. Azure DevOps 조직에서 Azure 파이프라인의 빌드 섹션으로 이동합니다. 새 **파이프라인을**만듭니다.

    ![새 빌드 파이프라인 만들기](media/batch-ci-cd/new-build-pipeline.jpg)

1. 빌드 파이프라인을 만드는 두 가지 옵션이 있습니다.

    a. [시각적 디자이너를 사용 하 여](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). 이를 사용하려면 **새 파이프라인** 페이지에서 "시각적 디자이너 사용"을 클릭합니다.

    b. [YAML 빌드 를 사용 하 여](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). 새 파이프라인 페이지에서 Azure Repos 또는 GitHub 옵션을 클릭하여 새 YAML 파이프라인을 만들 수 있습니다. 또는 아래 예제를 소스 컨트롤에 저장하고 Visual Designer를 클릭한 다음 YAML 템플릿을 사용하여 기존 YAML 파일을 참조할 수 있습니다.

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

1. 빌드가 필요에 따라 구성되면 **& 큐 저장을**선택합니다. 트리거 섹션에서 연속 **통합을** 사용하도록 설정한 경우 리포지토리에 대한 새 커밋이 만들어빌드에 설정된 조건을 충족하면 빌드가 자동으로 트리거됩니다.

    ![기존 빌드 파이프라인의 예](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Azure 파이프라인의 **빌드** 섹션으로 이동하여 Azure DevOps에서 빌드 진행 률에 대한 실시간 업데이트를 봅니다. 빌드 정의에서 적절한 빌드를 선택합니다.

    ![빌드에서 라이브 출력 보기](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> 클라이언트 응용 프로그램을 사용하여 HPC 일괄 처리 응용 프로그램을 실행하는 경우 해당 응용 프로그램에 대해 별도의 빌드 정의를 만들어야 합니다. Azure 파이프라인 설명서에서 여러 가지 방법 가이드를 찾을 수 [있습니다.](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops)

## <a name="continuous-deployment"></a>연속 배포

Azure 파이프라인은 응용 프로그램 및 기본 인프라를 배포하는 데도 사용됩니다. [릴리스 파이프라인은](https://docs.microsoft.com/azure/devops/pipelines/release) 지속적인 배포를 가능하게 하고 릴리스 프로세스를 자동화하는 구성 요소입니다.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>애플리케이션 및 기본 인프라 배포

인프라 배포에는 여러 단계가 있습니다. [링크된 템플릿을](../azure-resource-manager/templates/linked-templates.md)사용했기 때문에 공용 끝점(HTTP 또는 HTTPS)에서 해당 템플릿에 액세스할 수 있어야 합니다. GitHub의 리포지토리 또는 Azure Blob 저장소 계정 또는 다른 저장소 위치일 수 있습니다. 업로드된 템플릿 아티팩트는 개인 모드에서 유지되지만 일종의 SAS(공유 액세스 서명) 토큰을 사용하여 액세스할 수 있기 때문에 보안을 유지할 수 있습니다. 다음 예제에서는 Azure Storage Blob의 템플릿을 사용하여 인프라를 배포하는 방법을 보여 줍니다.

1. 새 **릴리스 정의를**만들고 빈 정의를 선택합니다. 그런 다음 새로 만든 환경의 이름을 파이프라인과 관련된 환경으로 바꿉니다.

    ![초기 릴리스 파이프라인](media/batch-ci-cd/Release-0.jpg)

1. 빌드 파이프라인에 대한 종속성을 만들어 HPC 응용 프로그램에 대한 출력을 가져옵니다.

    > [!NOTE]
    > 릴리스 정의 내에서 작업을 만들 때 필요할 수 있기 때문에 **소스 별칭을**다시 한 번 기록합니다.

    ![적절한 빌드 파이프라인에서 HPCApplicationPackage에 대한 아티팩트 링크를 만듭니다.](media/batch-ci-cd/Release-1.jpg)

1. 이번에는 Azure Repo에 대한 다른 아티팩트에 대한 링크를 만듭니다. 리포지토리에 저장된 리소스 관리자 템플릿에 액세스하려면 이 권한이 필요합니다. 리소스 관리자 템플릿은 컴파일이 필요하지 므로 빌드 파이프라인을 통해 푸시할 필요가 없습니다.

    > [!NOTE]
    > 릴리스 정의 내에서 작업을 만들 때 필요할 수 있기 때문에 **소스 별칭을**다시 한 번 기록합니다.

    ![Azure 리포지토리에 대한 아티팩트 링크 만들기](media/batch-ci-cd/Release-2.jpg)

1. 변수 섹션으로 **이동합니다.** 파이프라인에 여러 변수를 만드는 것이 좋습니다. 다음은 이 예제에서 사용되는 변수와 배포에 미치는 영향입니다.

    * **applicationStorage계정 이름**: HPC 응용 프로그램 바이너리를 보관할 저장소 계정이름
    * **batchAccountApplicationName**: Azure 일괄 처리 계정에서 응용 프로그램의 이름
    * **batchAccountName**: Azure 일괄 처리 계정의 이름
    * **batchAccountPoolName**: 처리를 수행하는 VM 풀의 이름
    * **배치 응용 프로그램 ID**: Azure 일괄 처리 응용 프로그램에 대한 고유 ID
    * **배치응용 프로그램 버전**: 배치 응용 프로그램의 시맨틱 버전(즉, ffmpeg 바이너리)
    * **위치**: Azure 리소스를 배포할 위치
    * **resourceGroupName**: 만들 리소스 그룹의 이름 및 리소스가 배포될 위치
    * **storageAccountName**: 연결된 리소스 관리자 템플릿을 보유하는 저장소 계정의 이름

    ![Azure 파이프라인 릴리스에 대해 설정된 변수의 예](media/batch-ci-cd/Release-4.jpg)

1. 개발자 환경의 작업으로 이동합니다. 아래 스냅샷에서 6개의 작업을 볼 수 있습니다. 이러한 작업은 지퍼ffmpeg 파일을 다운로드하고, 중첩된 Resource Manager 템플릿을 호스트하기 위해 저장소 계정을 배포하고, 해당 리소스 관리자 템플릿을 저장소 계정에 복사하고, 배치 계정 및 필요한 종속성을 배포하고, 응용 프로그램을 만듭니다. Azure 일괄 처리 계정을 만들고 응용 프로그램 패키지를 Azure 일괄 처리 계정에 업로드합니다.

    ![HPC 응용 프로그램을 Azure 일괄 처리로 릴리스하는 데 사용되는 작업의 예](media/batch-ci-cd/Release-3.jpg)

1. 파이프라인 **아티팩트 다운로드(미리 보기)** 작업을 추가하고 다음 속성을 설정합니다.
    * **표시 이름:** 에이전트에 응용 프로그램 패키지 다운로드
    * **다운로드 할 아티팩트의 이름 :** hpc-응용 프로그램
    * **다운로드 경로**: $(System.DefaultWorkingDirectory)

1. 저장소 계정을 만들어 아티팩트를 저장합니다. 솔루션의 기존 저장소 계정을 사용할 수 있지만 자체 포함된 샘플 및 콘텐츠 격리를 위해 아티팩트(특히 리소스 관리자 템플릿)에 대한 전용 저장소 계정을 만들고 있습니다.

    Azure **리소스 그룹 배포** 작업을 추가하고 다음 속성을 설정합니다.
    * **표시 이름:** 리소스 관리자 템플릿을 위한 저장소 계정 배포
    * **Azure 구독:** 적절한 Azure 구독 선택
    * **작업**: 리소스 그룹 생성 또는 업데이트
    * **리소스 그룹**: $(리소스그룹이름)
    * **위치**: $(위치)
    * **템플릿**: $(System.ArtifactsDirectory)/**{YourAzureRepo 아티팩트소스알리아스}**/팔 템플릿/스토리지계정.json
    * **템플릿 매개 변수 재정의**: -accountName $(storageAccountName)

1. 소스 컨트롤의 아티팩트를 저장소 계정에 업로드합니다. 이를 수행하는 Azure 파이프라인 작업이 있습니다. 이 작업의 일부로 저장소 계정 컨테이너 URL 및 SAS 토큰Azure 파이프라인의 변수에 출력할 수 있습니다. 즉, 이 에이전트 단계 전체에서 재사용할 수 있습니다.

    Azure **파일 복사** 작업을 추가하고 다음 속성을 설정합니다.
    * **출처:** $(System.ArtifactsDirectory)/**{YourAzureRepo 아티팩트소스알리아스}**/팔 템플릿/
    * **Azure 연결 유형**: Azure 리소스 관리자
    * **Azure 구독:** 적절한 Azure 구독 선택
    * **대상 유형**: Azure Blob
    * **RM 저장소 계정**: $(저장소계정 이름)
    * **컨테이너 이름**: 템플릿
    * **스토리지 컨테이너 URI**: 템플릿컨테이너Uri
    * **스토리지 컨테이너 SAS 토큰**: 템플릿컨테이너Sas토큰

1. 오케스트레이터 템플릿을 배포합니다. 이전에서 오케스트레이터 템플릿을 기억하면 SAS 토큰 외에도 저장소 계정 컨테이너 URL에 대한 매개 변수가 있음을 알 수 있습니다. Resource Manager 템플릿에 필요한 변수는 릴리스 정의의 변수 섹션에 보관되거나 다른 Azure 파이프라인 작업(예: Azure Blob Copy 작업의 일부)에서 설정되었습니다.

    Azure **리소스 그룹 배포** 작업을 추가하고 다음 속성을 설정합니다.
    * **표시 이름:** Azure 일괄 처리 배포
    * **Azure 구독:** 적절한 Azure 구독 선택
    * **작업**: 리소스 그룹 생성 또는 업데이트
    * **리소스 그룹**: $(리소스그룹이름)
    * **위치**: $(위치)
    * **템플릿**: $(System.ArtifactsDirectory)/**{YourAzureRepo 아티팩트소스알리아스}**/팔 템플릿/deployment.json
    * **템플릿 매개 변수 재정의:**```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

일반적인 방법은 Azure 키 자격 증명 모음 작업을 사용하는 것입니다. 서비스 주체(Azure 구독에 대한 연결)에 적절한 액세스 정책이 설정된 경우 Azure Key Vault에서 비밀을 다운로드하고 파이프라인의 변수로 사용할 수 있습니다. 보안 정보의 이름은 연결된 값으로 설정됩니다. 예를 들어, sshPassword의 비밀은 릴리스 정의에서 $(sshPassword)로 참조될 수 있습니다.

1. 다음 단계는 Azure CLI를 호출합니다. 첫 번째는 Azure Batch에서 응용 프로그램을 만드는 데 사용됩니다. 및 관련 패키지를 업로드할 수 있습니다.

    Azure **CLI** 작업을 추가하고 다음 속성을 설정합니다.
    * **표시 이름:** Azure 일괄 처리 계정에서 응용 프로그램 만들기
    * **Azure 구독:** 적절한 Azure 구독 선택
    * **스크립트 위치**: 인라인 스크립트
    * **인라인 스크립트**:```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. 두 번째 단계는 연결된 패키지를 응용 프로그램에 업로드하는 데 사용됩니다. 우리의 경우, ffmpeg 파일.

    Azure **CLI** 작업을 추가하고 다음 속성을 설정합니다.
    * **표시 이름:** Azure 일괄 처리 계정에 패키지 업로드
    * **Azure 구독:** 적절한 Azure 구독 선택
    * **스크립트 위치**: 인라인 스크립트
    * **인라인 스크립트**:```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > 응용 프로그램 패키지의 버전 번호가 변수로 설정됩니다. 이전 버전의 패키지를 덮어쓰는 것이 적합하며 Azure Batch로 푸시된 패키지의 버전 번호를 수동으로 제어하려는 경우 편리합니다.

1. 새 릴리스 만들기를 선택하여 **새 릴리스를**> 만듭니다. 트리거된 후 새 릴리스에 대한 링크를 선택하여 상태를 확인합니다.

1. 환경 아래에 있는 **로그 단추를** 선택하여 에이전트의 라이브 출력을 볼 수 있습니다.

    ![릴리스 상태 보기](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>환경 테스트

환경이 설정되면 다음 테스트를 성공적으로 완료할 수 있는지 확인합니다.

PowerShell 명령 프롬프트에서 Azure CLI를 사용하여 새 Azure 일괄 처리 계정에 연결합니다.

* Azure 계정에 `az login` 로그인하고 지침에 따라 인증합니다.
* 이제 Batch 계정을 인증합니다.`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>사용 가능한 응용 프로그램 나열

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>풀이 유효한지 확인

```azurecli
az batch pool list
```

이 명령의 `currentDedicatedNodes` 출력값에 유의하십시오. 이 값은 다음 테스트에서 조정됩니다.

#### <a name="resize-the-pool"></a>풀 크기 조정

작업 및 작업 테스트에 사용할 수 있는 계산 노드가 있도록 풀 크기를 조정하고 풀 목록 명령을 확인하여 크기 조정이 완료되고 사용 가능한 노드가 있을 때까지 현재 상태를 확인합니다.

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>다음 단계

이 문서 외에도 .NET 및 Python을 사용하여 ffmpeg를 사용하는 두 가지 자습서가 있습니다. 간단한 응용 프로그램을 통해 Batch 계정과 상호 작용하는 방법에 대한 자세한 내용은 다음 자습서를 참조하십시오.

* [Python API를 사용하여 Azure Batch에서 병렬 워크로드 실행](tutorial-parallel-python.md)
* [.NET API를 사용하여 Azure Batch에서 병렬 워크로드 실행](tutorial-parallel-dotnet.md)
