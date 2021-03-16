---
title: Synapse 작업 영역에 대 한 지속적인 통합 및 배달
description: 연속 통합 및 배달을 사용 하 여 작업 영역의 변경 내용을 한 환경 (개발, 테스트, 프로덕션)에서 다른 환경으로 배포 하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: de3738573bb9bb6f045a45d290c74ba9e6902a5e
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561960"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Azure Synapse 작업 영역에 대 한 지속적인 통합 및 전달

## <a name="overview"></a>개요

CI (연속 통합)는 팀 멤버가 변경 내용을 버전 제어에 커밋할 때마다 코드의 빌드 및 테스트를 자동화 하는 프로세스입니다. CD (연속 배포)는 여러 테스트 또는 스테이징 환경에서 프로덕션 환경으로 빌드, 테스트, 구성 및 배포 하는 프로세스입니다.

Azure Synapse 작업 영역의 경우 CI/CD (지속적인 통합 및 배달)는 모든 엔터티를 한 환경 (개발, 테스트, 프로덕션)에서 다른 환경으로 이동 합니다. 작업 영역을 다른 작업 영역으로 승격 하려면 두 부분으로 구성 됩니다. 작업 영역 리소스 (풀 및 작업 영역)를 만들거나 업데이트 하려면 [Azure Resource Manager 템플릿을](../../azure-resource-manager/templates/overview.md) 사용 합니다. Azure DevOps의 Synapse CI/CD 도구를 사용 하 여 아티팩트 (SQL 스크립트, 노트북, Spark 작업 정의, 파이프라인, 데이터 집합, 데이터 흐름 등)를 마이그레이션합니다. 

이 문서에서는 Azure 릴리스 파이프라인을 사용 하 여 여러 환경에 Synapse 작업 영역의 배포를 자동화 하는 방법을 간략하게 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

-   개발에 사용 되는 작업 영역이 스튜디오에서 Git 리포지토리로 구성 되었습니다. [Synapse Studio의 소스 제어](source-control.md)를 참조 하세요.
-   Azure DevOps 프로젝트가 릴리스 파이프라인을 실행할 준비가 되었습니다.

## <a name="set-up-a-release-pipelines"></a>릴리스 파이프라인 설정

1.  [Azure DevOps](https://dev.azure.com/)에서 릴리스에 대해 만든 프로젝트를 엽니다.

1.  페이지의 왼쪽에서 **파이프라인** 을 선택한 다음 **릴리스** 를 선택합니다.

    ![파이프라인, 릴리스 선택](media/create-release-1.png)

1.  **새 파이프라인** 을 선택하거나, 기존 파이프라인이 있는 경우에는 **새로 만들기** 를 선택한 다음 **새 릴리스 파이프라인** 을 선택합니다.

1.  **빈 작업** 템플릿을 선택합니다.

    ![빈 작업 선택](media/create-release-select-empty.png)

1.  **단계 이름** 상자에 사용자 환경의 이름을 입력합니다.

1.  **아티팩트 추가** 를 선택한 다음 Development Synapse Studio를 사용 하 여 구성 된 git 리포지토리를 선택 합니다. 풀 및 작업 영역의 ARM 템플릿을 관리 하는 데 사용한 git 리포지토리를 선택 합니다. GitHub를 원본으로 사용 하는 경우 GitHub 계정 및 풀 리포지토리에 대 한 서비스 연결을 만들어야 합니다. [서비스 연결](/azure/devops/pipelines/library/service-endpoints) 에 대 한 자세한 내용은 

    ![게시 분기 추가](media/release-creation-github.png)

1.  리소스 업데이트를 위한 ARM 템플릿 분기를 선택 합니다. **기본 버전** 의 경우 **기본 분기에서 최신 버전** 을 선택합니다.

    ![ARM 템플릿 추가](media/release-creation-arm-branch.png)

1.  **기본 분기** 에 대한 리포지토리의 [게시 분기](source-control.md#configure-publishing-settings)를 선택합니다. 기본적으로 이 게시 분기는 `workspace_publish`입니다. **기본 버전** 의 경우 **기본 분기에서 최신 버전** 을 선택합니다.

    ![아티팩트 추가](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>ARM 리소스 만들기 및 업데이트에 대 한 스테이지 작업 설정 

작업 영역 및 풀을 포함 하 여 리소스를 만들거나 업데이트 하는 Azure Resource Manager 배포 작업을 추가 합니다.

1. 단계 보기에서 **단계 작업 보기** 를 선택합니다.

    ![단계 보기](media/release-creation-stage-view.png)

1. 새 작업을 만듭니다. **ARM 템플릿 배포** 를 검색 한 다음 **추가** 를 선택 합니다.

1. 배포 작업에서 대상 작업 영역에 대 한 구독, 리소스 그룹 및 위치를 선택 합니다. 필요한 경우 자격 증명을 제공합니다.

1. **작업** 목록에서 **리소스 그룹 만들기 또는 업데이트** 를 선택합니다.

1. **템플릿** 상자 옆에 있는 줄임표 단추( **...** )를 선택합니다. 대상 작업 영역의 Azure Resource Manager 템플릿 찾아보기

1. **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수** 상자 옆에서 매개 변수 파일을 선택합니다.

1. **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수 재정의** 상자 옆에 대상 작업 영역에 대 한 원하는 매개 변수 값을 입력 합니다. 

1. **배포 모드** 에 대해 **증분** 을 선택합니다.
    
    ![작업 영역 및 풀 배포](media/pools-resource-deploy.png)

1. 필드 권한 부여 및 업데이트 작업 영역 역할 할당에 대 한 **Azure PowerShell** 를 추가 합니다. 릴리스 파이프라인을 사용 하 여 Synapse 작업 영역을 만드는 경우 파이프라인의 서비스 주체가 기본 작업 영역 관리자로 추가 됩니다. PowerShell을 실행 하 여 다른 계정에 작업 영역에 대 한 액세스 권한을 부여할 수 있습니다. 
    
    ![권한 부여](media/release-creation-grant-permission.png)

 > [!WARNING]
> 전체 배포 모드에서는 리소스 그룹에 있지만 새 리소스 관리자 템플릿에 지정 되지 않은 리소스는 **삭제** 됩니다. 자세한 내용은 [Azure Resource Manager 배포 모드](../../azure-resource-manager/templates/deployment-modes.md) 를 참조 하세요.

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>아티팩트 배포에 대 한 단계 작업 설정 

[Synapse workspace deployment](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) extension을 사용 하 여 데이터 집합, SQL 스크립트, 노트북, spark 작업 정의, 데이터 흐름, 파이프라인, 연결 된 서비스, 자격 증명 및 IR (Integration Runtime)와 같은 Synapse 작업 영역에 다른 항목을 배포 합니다.  

1. **Azure DevOps marketplace** 에서 확장을 검색 하 고 가져옵니다 (https://marketplace.visualstudio.com/azuredevops) 

     ![확장 가져오기](media/get-extension-from-market.png)

1. 확장을 설치할 조직을 선택 합니다. 

     ![확장 설치](media/install-extension.png)

1. Azure DevOps 파이프라인의 서비스 주체에 게 구독 권한이 부여 되 고 대상 작업 영역에 대 한 작업 영역 관리자로 할당 되었는지 확인 합니다. 

1. 새 작업을 만듭니다. **Synapse 작업 영역 배포** 를 검색 한 다음 **추가** 를 선택 합니다.

     ![확장 추가](media/add-extension-task.png)

1.  작업 **에서 ...를 선택 합니다** . **템플릿 상자 옆** 의 템플릿 파일을 선택 합니다.

1. **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수** 상자 옆에서 매개 변수 파일을 선택합니다.

1. 연결, 리소스 그룹 및 대상 작업 영역의 이름을 선택 합니다. 

1. **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수 재정의** 상자 옆에 대상 작업 영역에 대 한 원하는 매개 변수 값을 입력 합니다. 

    ![Synapse 작업 영역 배포](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> CI/CD 시나리오에서는 서로 다른 환경에서의 IR(통합 런타임) 형식이 동일해야 합니다. 예를 들어 개발 환경에 자체 호스팅 IR이 있는 경우 테스트 및 프로덕션과 같은 다른 환경에서 동일한 IR이 자체 호스팅 유형이어야 합니다. 마찬가지로 여러 단계에서 통합 런타임을 공유하는 경우 개발, 테스트 및 프로덕션과 같은 모든 환경에서 통합 런타임을 연결된 자체 호스팅으로 구성해야 합니다.

## <a name="create-release-for-deployment"></a>배포용 릴리스 만들기 

모든 변경 내용을 저장 한 후 **릴리스 만들기** 를 선택 하 여 수동으로 릴리스를 만들 수 있습니다. 릴리스 만들기를 자동화하려면 [Azure DevOps 릴리스 트리거](/azure/devops/pipelines/release/triggers)를 참조하세요.

   ![릴리스 만들기 선택](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>작업 영역 템플릿의 사용자 지정 매개 변수 사용 

자동화 된 CI/CD를 사용 하 고 배포 중에 일부 속성을 변경 하려고 하지만 속성은 기본적으로 매개 변수화 되지 않습니다. 이 경우 기본 매개 변수 템플릿을 재정의할 수 있습니다.

기본 매개 변수 템플릿을 재정의 하려면 git 공동 작업 분기의 루트 폴더에 **template-parameters-definition.js에** 있는 파일 인 사용자 지정 매개 변수 템플릿을 만들어야 합니다. 정확한 파일 이름을 사용해야 합니다. 공동 작업 분기에서 게시 하는 경우 Synapse 작업 영역에서이 파일을 읽고 해당 구성을 사용 하 여 매개 변수를 생성 합니다. 파일이 없는 경우 기본 매개 변수 템플릿이 사용 됩니다.

### <a name="custom-parameter-syntax"></a>사용자 지정 매개 변수 구문

다음은 사용자 지정 매개 변수 파일을 만들기 위한 몇 가지 지침입니다.

* 관련 엔터티 형식 아래에 속성 경로를 입력합니다.
* 속성 이름을로 설정 `*` 하면 그 아래의 모든 속성을 매개 변수화 합니다. 즉, 재귀적이 아닌 첫 번째 수준 까지만 매개 변수화 됩니다. 이 구성에 대한 예외를 제공할 수도 있습니다.
* 문자열로 속성의 값을 설정하면 속성을 매개 변수화하려고 함을 나타냅니다. `<action>:<name>:<stype>` 형식을 사용합니다.
   *  `<action>` 다음 문자 중 하나를 사용할 수 있습니다.
      * `=` 은 현재 값을 매개 변수의 기본값으로 유지 합니다.
      * `-` 는 매개 변수의 기본값을 유지 하지 않음을 의미 합니다.
      * `|` 는 연결 문자열이 나 키에 대 한 Azure Key Vault의 비밀에 대 한 특별 한 사례입니다.
   * `<name>`은 매개 변수의 이름입니다. 비어 있는 경우 속성의 이름을 사용합니다. 값이 `-` 문자로 시작하는 경우에는 이름이 짧아집니다. 예를 들어 `AzureStorage1_properties_typeProperties_connectionString`에서 `AzureStorage1_connectionString`으로 줄어듭니다.
   * `<stype>` 매개 변수의 형식입니다. `<stype>`가 비어 있으면 기본 형식은 `string` 입니다. 지원 되는 값은 `string` , `securestring` , `int` ,, `bool` `object` `secureobject` 및 `array` 입니다.
* 파일에 배열을 지정 하면 템플릿의 일치 하는 속성이 배열 임을 나타냅니다. Synapse는 지정 된 정의를 사용 하 여 배열의 모든 개체를 반복 합니다. 두 번째 개체, 문자열은 각 반복에 대한 매개 변수의 이름으로 사용되는 속성의 이름이 됩니다.
* 정의는 리소스 인스턴스와 관련될 수 없습니다. 모든 정의는 해당 형식의 모든 리소스에 적용됩니다.
* 기본적으로 Key Vault 비밀과 같은 모든 보안 문자열과 연결 문자열, 키, 토큰 등의 보안 문자열은 매개 변수화됩니다.

### <a name="parameter-template-definition-samples"></a>매개 변수 템플릿 정의 샘플 

매개 변수 템플릿 정의의 예는 다음과 같습니다.

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
다음은 리소스 종류별로 분할된 이전 템플릿의 구성 방법에 대한 설명입니다.

#### <a name="notebooks"></a>Notebooks 

* 경로의 모든 속성 `properties/bigDataPool/referenceName` 은 해당 기본값을 사용 하 여 매개 변수화 됩니다. 각 노트북 파일에 대해 연결 된 Spark 풀을 매개 변수화 할 수 있습니다. 

#### <a name="sql-scripts"></a>SQL 스크립트 

* 경로의 속성 (poolName 및 databaseName) `properties/content/currentConnection` 은 템플릿에 기본값이 없는 문자열로 매개 변수화 됩니다. 

#### <a name="pipelines"></a>파이프라인

* `activities/typeProperties/waitTimeInSeconds` 경로에 있는 모든 속성은 매개 변수화됩니다. `waitTimeInSeconds`라는 코드 수준 속성(예: `Wait` 작업)이 있는 파이프라인의 모든 작업은 기본 이름을 가진 숫자로 매개 변수화됩니다. 그러나 Resource Manager 템플릿에는 기본값이 없습니다. Resource Manager 배포 중에 입력해야 할 필수 항목입니다.
* 마찬가지로 `headers` (예: 활동에서) 라는 속성 `Web` 은 유형 (Object)으로 매개 변수화 됩니다 `object` . 이 값은 원본 팩터리의 값과 동일한 기본값을 갖습니다.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* `typeProperties` 경로 아래에 있는 모든 속성은 해당 기본값을 사용하여 매개 변수화됩니다. 예를 들어 `IntegrationRuntimes` 형식 속성에는 `computeProperties` 및 `ssisProperties`의 두 가지 속성이 있습니다. 두 속성 유형 모두 해당 기본값과 유형(Object)으로 만들어집니다.

#### <a name="triggers"></a>트리거

* `typeProperties`에서 두 개의 속성은 매개 변수화됩니다. 첫 번째 값은 기본값을 갖도록 지정된 `maxConcurrency`이며 `string` 형식입니다. 이 값은 기본 매개 변수 이름 `<entityName>_properties_typeProperties_maxConcurrency`를 갖습니다.
* `recurrence` 속성도 매개 변수화됩니다. 이 속성 아래 수준의 모든 속성은 문자열로 매개 변수화되어 기본값 및 매개 변수 이름으로 지정됩니다. 단, `int` 형식으로 매개 변수화되는 `interval` 속성은 예외입니다. 매개 변수 이름에는 `<entityName>_properties_typeProperties_recurrence_triggerSuffix`가 접미사로 붙습니다. 마찬가지로 `freq` 속성은 문자열이며 문자열로 매개 변수화됩니다. 그러나 `freq` 속성은 기본값 없이 매개 변수화됩니다. 이름이 단축되고 접미사가 붙습니다. `<entityName>_freq`)을 입력합니다.

#### <a name="linkedservices"></a>LinkedServices

* 연결된 서비스는 고유합니다. 연결된 서비스 및 데이터 세트에는 다양한 형식이 있어 형식별로 사용자 지정할 수 있습니다. 이 예에서는 `AzureDataLakeStore` 형식의 연결된 모든 서비스에 대해 특정 템플릿이 적용됩니다. `*`을 통한 다른 모든 서비스에 대해서는 다른 템플릿이 적용됩니다.
* `connectionString` 속성은 `securestring` 값으로 매개 변수화됩니다. 기본값이 없습니다. `connectionString`이 접미사로 사용되는 축약된 매개 변수 이름이 포함됩니다.
* 속성 `secretAccessKey`는 `AzureKeyVaultSecret`(예: Amazon S3에 연결된 서비스)가 됩니다. 자동으로 Azure Key Vault 비밀로 매개 변수화되고 구성된 키 자격 증명 모음에서 페치됩니다. 키 자격 증명 모음을 자체적으로 매개 변수화할 수도 있습니다.

#### <a name="datasets"></a>데이터 세트

* 데이터 세트에 대해 형식별 사용자 지정을 사용할 수 있지만, 명시적으로 \* 수준으로 구성하지 않고도 구성할 수 있습니다. 이전 예에서 `typeProperties` 아래의 모든 데이터 세트 속성은 매개 변수화됩니다.


## <a name="best-practices-for-cicd"></a>CI/CD에 대한 모범 사례

Synapse 작업 영역에서 Git 통합을 사용 하 고 변경 내용을 개발에서 테스트로 이동 하는 CI/CD 파이프라인을 사용 하는 경우 다음 모범 사례를 따르는 것이 좋습니다.

-   **Git 통합**. Git 통합을 사용 하 여 development Synapse 작업 영역을 구성 합니다. 테스트 및 프로덕션 작업 영역에 대 한 변경 내용은 CI/CD를 통해 배포 되며 Git 통합이 필요 하지 않습니다.
-   **아티팩트 마이그레이션 전에 풀을 준비** 합니다. 개발 작업 영역의 풀에 연결 된 SQL 스크립트 또는 노트북이 있는 경우 다른 환경에서 동일한 풀 이름이 필요 합니다. 
-   **IaC (Infrastructure As Code)**. 설명 모델에서 인프라 (네트워크, 가상 컴퓨터, 부하 분산 장치 및 연결 토폴로지)를 관리 하는 것은 DevOps 팀에서 소스 코드에 대해 사용 하는 것과 동일한 버전 관리를 사용 합니다. 
-   **기타**. [ADF 아티팩트의 모범 사례를](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd) 참조 하세요.

## <a name="troubleshooting-artifacts-deployment"></a>아티팩트 배포 문제 해결 

### <a name="use-the-synapse-workspace-deployment-task"></a>Synapse workspace 배포 작업 사용

Synapse에는 ARM 리소스가 아닌 여러 아티팩트가 있습니다. 이는 Azure Data Factory와 다릅니다. ARM 템플릿 배포 작업이 Synapse 아티팩트를 배포 하는 데 제대로 작동 하지 않습니다.
 
### <a name="unexpected-token-error-in-release"></a>릴리스에 예기치 않은 토큰 오류가 있습니다.

매개 변수 파일에 이스케이프 되지 않은 매개 변수 값이 있는 경우 릴리스 파이프라인이 파일 구문 분석에 실패 하 고 "예기치 않은 토큰" 오류가 생성 됩니다. 매개 변수를 재정의 하거나 Azure KeyVault를 사용 하 여 매개 변수 값을 검색 하는 것이 좋습니다. 또한 이중 이스케이프 문자를 해결 방법으로 사용할 수 있습니다.
