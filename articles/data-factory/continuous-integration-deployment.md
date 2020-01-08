---
title: Azure Data Factory에서 지속적인 통합 및 전달
description: 지속적인 통합 및 지속적인 업데이트를 사용하여 환경(개발, 테스트, 프로덕션) 간에 Data Factory 파이프라인을 이동하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 06c8784c235b157f5799bb727df9784dfaa2f376
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440523"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Azure Data Factory의 CI/CD(지속적인 통합 및 지속적인 업데이트)

## <a name="overview"></a>개요

지속적인 통합은 코드 베이스에 자동으로 이루어진 변경 내용을 각각 가능한 빨리 테스트하는 방법입니다. 연속 전달은 연속 통합 중에 발생 하는 테스트를 따르고 스테이징 또는 프로덕션 시스템에 변경 내용을 푸시합니다.

Azure Data Factory에서 지속적인 통합 & 배달은 Data Factory 파이프라인을 한 환경 (개발, 테스트, 프로덕션)에서 다른 환경으로 이동 하는 것을 의미 합니다. 지속적인 통합 & 배달 하려면 Azure Resource Manager 템플릿과 Data Factory UX 통합을 사용할 수 있습니다. Data Factory UX는 **ARM 템플릿** 드롭다운에서 리소스 관리자 템플릿을 생성할 수 있습니다. **ARM 템플릿 내보내기**를 선택하는 경우 포털에서는 Data Factory의 Resource Manager 템플릿과 모든 연결 문자열 및 기타 매개 변수를 포함하는 구성 파일을 생성합니다. 그런 다음 각 환경 (개발, 테스트, 프로덕션)에 대해 하나의 구성 파일을 만들어야 합니다. 주 Resource Manager 템플릿 파일은 모든 환경에서 동일하게 유지됩니다.

9분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>연속 통합 수명 주기

다음은 Azure Repos Git로 구성 된 Azure Data factory의 지속적인 통합 및 배달 수명 주기에 대 한 샘플 개요입니다. Git 리포지토리를 구성 하는 방법에 대 한 자세한 내용은 [Azure Data Factory에서 소스 제어](source-control.md)를 참조 하세요.

1.  모든 개발자가 파이프라인 및 데이터 집합과 같은 Data Factory 리소스를 작성할 수 있는 권한이 있는 Azure Repos Git를 사용 하 여 개발 데이터 팩터리가 만들어지고 구성 됩니다.

1.  개발자는 해당 기능 분기를 변경할 때 가장 최근의 변경 내용으로 파이프라인 실행을 디버그 합니다. 파이프라인 실행을 디버그 하는 방법에 대 한 자세한 내용은 [Azure Data Factory를 사용 하 여 반복 개발 및 디버깅](iterative-development-debugging.md)을 참조 하세요.

1.  개발자가 변경 내용에 만족 하면 해당 기능 분기에서 마스터 또는 공동 작업 분기로 끌어오기 요청을 만들어 해당 변경 내용을 피어로 검토할 수 있습니다.

1.  끌어오기 요청을 승인 하 고 변경 내용을 master 분기에 병합 한 후에는 개발 팩터리에 게시할 수 있습니다.

1.  팀에서 테스트 팩터리에 대 한 변경 내용을 배포 하 고 프로덕션 팩터리에 배포할 준비가 되 면 마스터 분기에서 리소스 관리자 템플릿을 내보냅니다.

1.  내보낸 리소스 관리자 템플릿이 테스트 팩터리 및 프로덕션 팩터리에 다른 매개 변수 파일을 사용 하 여 배포 됩니다.

## <a name="create-a-resource-manager-template-for-each-environment"></a>각 환경에 Resource Manager 템플릿 만들기

**Arm 템플릿** 드롭다운에서 **arm 템플릿 내보내기** 를 선택 하 여 개발 환경에서 데이터 팩터리에 대 한 리소스 관리자 템플릿을 내보냅니다.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

테스트 및 프로덕션 데이터 팩터리에서 **ARM 템플릿 가져오기**를 선택 합니다. 이 작업을 통해 Azure Portal로 이동합니다. 여기서 내보낸 템플릿을 가져올 수 있습니다. **편집기에서 사용자 고유의 템플릿 빌드** 를 선택 하 여 리소스 관리자 템플릿 편집기를 엽니다.

![사용자 지정 배포 사용자 고유의 템플릿 빌드](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

**파일 로드** 를 클릭 하 고 생성 된 리소스 관리자 템플릿을 선택 합니다.

![사용자 지정 배포 템플릿 편집](media/continuous-integration-deployment/custom-deployment-edit-template.png)

설정 창에서 연결 된 서비스 자격 증명과 같은 구성 값을 입력 합니다. 완료 되 면 **구매** 를 클릭 하 여 리소스 관리자 템플릿을 배포 합니다.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>연결 문자열

연결 문자열을 구성 하는 방법에 대 한 정보는 각 커넥터의 문서에서 찾을 수 있습니다. 예를 들어 Azure SQL Database의 경우 [Azure Data Factory를 사용하여 Azure SQL Database 간 데이터 복사](connector-azure-sql-database.md)를 참조하세요. 연결 문자열을 확인 하려면 Data Factory UX에서 리소스에 대 한 코드 보기를 열 수 있습니다. 코드 보기에서 연결 문자열의 암호 또는 계정 키 부분이 제거 됩니다. 코드 보기를 열려면 다음 스크린샷에서 강조 표시된 아이콘을 선택합니다.

![코드 보기를 열어 연결 문자열 확인](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Azure Pipelines 릴리스를 사용하여 지속적인 통합 자동화

다음은 데이터 팩터리를 여러 환경에 배포 하는 작업을 자동화 하는 Azure Pipelines 릴리스를 설정 하는 방법에 대 한 지침입니다.

![Azure Pipelines를 사용하는 지속적인 통합 다이어그램](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>요구 사항

-    [Azure Resource Manager 서비스 끝점](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)을 사용 하 여 Team Foundation Server 또는 Azure Repos에 연결 된 Azure 구독입니다.

-   Azure Repos Git 통합으로 구성 된 Data Factory입니다.

-   각 환경에 대 한 암호를 포함 하는 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 입니다.

### <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines 릴리스 설정

1.  [Azure DevOps 사용자 환경](https://dev.azure.com/)에서 Data Factory 구성 된 프로젝트를 엽니다.

1.  페이지의 왼쪽에서 **파이프라인** 을 클릭 하 고 **릴리스**를 선택 합니다.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  **새 파이프라인** 을 선택 하거나 기존 파이프라인, **새로 만들기**, **새 릴리스 파이프라인**을 차례로 선택 합니다.

1.  **빈 작업** 템플릿을 선택 합니다.

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  **스테이지 이름** 필드에 사용자 환경의 이름을 입력 합니다.

1.  **아티팩트 추가**를 선택 하 고 Data Factory 구성 된 것과 동일한 리포지토리를 선택 합니다. 최신 기본 버전을 사용하여 `adf_publish`를 기본 분기로 선택합니다.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager 배포 작업을 추가합니다.

    a.  스테이지 보기에서 **단계 작업 보기** 링크를 클릭 합니다.

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  새 태스크를 만듭니다. **Azure 리소스 그룹 배포**를 검색 하 고 **추가**를 클릭 합니다.

    다.  배포 작업에서 대상 Data Factory의 구독, 리소스 그룹 및 위치를 선택하고, 필요한 경우 자격 증명을 제공합니다.

    d.  작업 드롭다운에서 **리소스 그룹 만들기 또는 업데이트**를 선택 합니다.

    e.  **템플릿 매개 변수 재정의** 필드 **…** 를 선택합니다. [각 환경에 대 한 Resource Manager 템플릿 만들기](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment)의 **ARM 템플릿 가져오기** 단계를 통해 Azure Resource Manager 템플릿 만들기를 찾습니다. `adf_publish` 분기의 `<FactoryName>` 폴더에서 이 파일을 찾습니다.

    f.  **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수 필드에 있습니다.** 를 클릭 하 여 매개 변수 파일을 선택 합니다. 복사본을 만들었는지 또는 기본 파일 *ARMTemplateParametersForFactory.json*을 사용하는지에 따라 올바른 파일을 선택합니다.

    g.  **템플릿 매개 변수 재정의** 필드 옆에 있는 **…** 을 선택하고 대상 Data Factory에 대한 정보를 입력합니다. Key vault에서 가져온 자격 증명의 경우 큰따옴표 사이에 비밀 이름을 입력 합니다. 예를 들어 비밀의 이름이 `cred1`인 경우 해당 값에 대 한 `"$(cred1)"`을 입력 합니다.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. **증분** 배포 모드를 선택합니다.

    > [!WARNING]
    > 배포 모드 **완료** 를 선택 하는 경우 리소스 관리자 템플릿에 정의 되지 않은 대상 리소스 그룹의 모든 리소스를 포함 하 여 기존 리소스를 삭제할 수 있습니다.

1.  릴리스 파이프라인을 저장합니다.

1. 릴리스를 트리거하려면 **릴리스 만들기** 를 클릭 합니다.

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Azure Key Vault에서 비밀 가져오기

Azure Resource Manager 템플릿에서 암호를 전달 하는 경우 Azure Pipelines 릴리스와 Azure Key Vault를 사용 하는 것이 좋습니다.

비밀을 처리 하는 방법에는 두 가지가 있습니다.

1.  매개 변수 파일에 비밀을 추가합니다. 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](../azure-resource-manager/resource-manager-keyvault-parameter.md)을 참조하세요.

    -   게시 분기에 업로드된 매개 변수 파일의 복사본을 만들고 다음과 같은 형식의 키 자격 증명 모음에서 가져오려는 매개 변수의 값을 설정합니다.

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   이 메서드를 사용하는 경우 키 자격 증명 모음에서 자동으로 암호를 끌어옵니다.

    -   매개 변수 파일은 게시 분기에 포함되어야 합니다.

1.  이전 섹션에 설명된 Azure Resource Manager 배포 전에 [Azure Key Vault 작업](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)을 추가합니다.

    -   **작업** 탭을 선택하고, 새 작업을 만들고, **Azure Key Vault**를 검색하고 추가합니다.

    -   Key Vault 작업에서는 키 자격 증명 모음을 만든 구독을 선택하고, 필요한 경우 자격 증명을 제공한 다음, 키 자격 증명 모음을 선택합니다.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines 에이전트에 권한 부여

적절 한 권한이 없는 경우에는 액세스 거부 오류와 함께 Azure Key Vault 작업이 실패할 수 있습니다. 릴리스에 대한 로그를 다운로드하고, Azure Pipelines 에이전트에 권한을 부여하는 명령을 사용하여 `.ps1` 파일을 찾습니다. 직접 명령을 실행할 수 있습니다. 또는 파일에서 보안 주체 ID를 복사하고 Azure Portal에 액세스 정책을 수동으로 추가할 수 있습니다. **Get** 및 **List** 는 필요한 최소 권한입니다.

### <a name="update-active-triggers"></a>활성 트리거 업데이트

활성 트리거를 업데이트하려고 하면 배포에 실패할 수 있습니다. 활성 트리거를 업데이트하려면 수동으로 중단하고 배포 후에 시작해야 합니다. Azure Powershell 작업을 통해이 작업을 수행할 수 있습니다.

1.  릴리스의 작업 탭에서 **Azure Powershell** 작업을 추가 합니다.

1.  **Azure Resource Manager**를 연결 형식으로 선택하고 구독을 선택합니다.

1.  **인라인 스크립트**를 스크립트 형식으로 선택한 다음, 코드를 제공합니다. 다음 예제에서는 트리거를 중지합니다.

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

`Start-AzDataFactoryV2Trigger` 함수를 사용 하 여 비슷한 단계를 수행 하 여 배포 후에 트리거를 다시 시작할 수 있습니다.

> [!IMPORTANT]
> 지속적인 통합 및 지속적인 배포 시나리오에서는 서로 다른 환경 간의 Integration Runtime 유형이 동일해야 합니다. 예를 들어 개발 환경에 *자체 호스팅* IR(Integration Runtime)이 있는 경우 테스트 및 프로덕션과 같은 다른 환경에서도 동일한 IR이 *자체 호스팅* 유형이어야 합니다. 마찬가지로 여러 단계에서 통합 런타임을 공유하는 경우 개발, 테스트 및 프로덕션과 같은 모든 환경에서 통합 런타임을 *연결된 자체 호스팅*으로 구성해야 합니다.

#### <a name="sample-prepostdeployment-script"></a>샘플 사전/배포 후 스크립트

다음은 배포 전에 트리거를 중지 하 고 나중에 트리거를 다시 시작 하는 샘플 스크립트입니다. 스크립트에는 제거된 리소스를 삭제하는 코드도 포함됩니다. 최신 버전의 Azure PowerShell을 설치하려면 [PowerShellGet으로 Windows에 Azure PowerShell 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조하세요.

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Resource Manager 템플릿에서 사용자 지정 매개 변수 사용

GIT 모드의 경우 템플릿 및 하드 코드 된 속성에서 매개 변수화 된 속성을 설정 하도록 리소스 관리자 템플릿의 기본 속성을 재정의할 수 있습니다. 이러한 시나리오에서는 기본 매개 변수화 템플릿을 재정의할 수 있습니다.

* 자동화 된 CI/CD를 사용 하 고 리소스 관리자 배포 중에 일부 속성을 변경 하려고 하지만 속성은 기본적으로 매개 변수화 되지 않습니다.
* 팩터리는 기본 리소스 관리자 템플릿이 허용 되는 최대 매개 변수 (256)를 초과 하기 때문에 잘못 되었습니다.

이러한 조건에서 기본 매개 변수화 템플릿을 재정의 하려면 리포지토리의 루트 폴더에 이름이 *arm* 인 파일을 만듭니다. 파일 이름은 정확히 일치 해야 합니다. Data Factory는 현재 Azure Data Factory 포털에 있는 분기 (공동 작업 분기 뿐만 아니라)에서이 파일을 읽으려고 시도 합니다. UI에서 **내보내기 ARM 템플릿을** 사용 하 여 변경 내용을 테스트할 수 있는 private 분기에서 파일을 만들거나 편집할 수 있습니다. 그런 다음 공동 작업 분기에 파일을 병합할 수 있습니다. 파일이 없는 경우 기본 템플릿이 사용 됩니다.


### <a name="syntax-of-a-custom-parameters-file"></a>사용자 지정 매개 변수 파일의 구문

다음은 사용자 지정 매개 변수 파일을 작성할 때 사용할 수 있는 몇 가지 지침입니다. 이 파일은 트리거, 파이프라인, 연결 된 서비스, 데이터 집합, 통합 런타임 등의 각 엔터티 형식에 대 한 섹션으로 구성 됩니다.
* 관련 엔터티 형식 아래에 속성 경로를 입력 합니다.
* 속성 이름을 '\*' ' (으)로 설정 하는 경우 해당 속성의 모든 속성을 매개 변수화 하도록 지정 합니다 (재귀적이 아닌 첫 번째 수준 까지만). 이에 대 한 모든 예외를 제공할 수도 있습니다.
* 문자열로 속성의 값을 설정할 때 속성을 매개 변수화하려는 것을 나타냅니다.  `<action>:<name>:<stype>`형식을 사용 합니다.
   *  `<action>` 는 다음 문자 중 하나를 사용할 수 있습니다.
      * `=` 는 현재 값을 매개 변수의 기본값으로 유지 한다는 의미입니다.
      * `-` 는 매개 변수의 기본값을 유지 하지 않음을 의미 합니다.
      * `|` 는 연결 문자열이 나 키에 대 한 Azure Key Vault의 비밀에 대 한 특별 한 사례입니다.
   * `<name>` 는 매개 변수의 이름입니다. 비어 있는 경우 속성의 이름을 사용 합니다. 값이 `-` 문자로 시작 하는 경우에는 이름이 짧아집니다. 예를 들어 `AzureStorage1_properties_typeProperties_connectionString` `AzureStorage1_connectionString`으로 줄어듭니다.
   * `<stype>` 는 매개 변수의 형식입니다.  `<stype>` 비어 있는 경우 기본 유형은 `string`입니다. 지원 되는 값: `string`, `bool`, `number`, `object`및 `securestring`.
* 정의 파일에서 배열을 지정 하면 템플릿에서 일치 하는 속성이 배열 임을 알 수 있습니다. Data Factory 배열의 Integration Runtime 개체에 지정 된 정의를 사용 하 여 배열의 모든 개체를 반복 합니다. 두 번째 개체, 문자열은 각 반복에 대한 매개 변수의 이름으로 사용되는 속성의 이름이 됩니다.
* 리소스 인스턴스에 대 한 정의를 가질 수 없습니다. 모든 정의는 해당 형식의 모든 리소스에 적용 됩니다.
* 기본적으로 Key Vault 암호 및 보안 문자열 (예: 연결 문자열, 키, 토큰)과 같은 모든 보안 문자열에 매개 변수가 있습니다.
 
### <a name="sample-parameterization-template"></a>샘플 매개 변수화 템플릿

다음은 매개 변수화 템플릿이 어떻게 표시 될 수 있는지에 대 한 예입니다.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
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
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
다음은 위의 템플릿이 구성 된 방법에 대 한 설명입니다. 리소스 유형별로 분류 됩니다.

#### <a name="pipelines"></a>파이프라인
    
* Path 활동/typeProperties/waitTimeInSeconds의 모든 속성은 매개 변수화 됩니다. `waitTimeInSeconds` 라는 코드 수준 속성 (예: `Wait` 활동)이 있는 파이프라인의 모든 활동은 기본 이름을 가진 숫자로 매개 변수화 됩니다. 그러나 리소스 관리자 템플릿에는 기본값이 없습니다. 리소스 관리자 배포 중에 필수 입력이 됩니다.
* 마찬가지로 `headers` (예: `Web` 활동) 라는 속성은 형식 `object` (JObject)로 매개 변수화 됩니다. 이 값은 원본 팩터리의 값과 동일한 기본값을 가집니다.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* `typeProperties` 경로 아래에 있는 모든 속성은 해당 기본값을 사용 하 여 매개 변수화 됩니다. 예를 들어 **IntegrationRuntimes** 형식 속성에는 `computeProperties` 및 `ssisProperties`라는 두 가지 속성이 있습니다. 두 속성 유형 모두 해당 기본값과 유형 (개체)을 사용 하 여 생성 됩니다.

#### <a name="triggers"></a>트리거

* `typeProperties`에서는 두 개의 속성을 매개 변수화 합니다. 첫 번째 값은 `maxConcurrency`이며 기본값을 갖도록 지정 되 고`string`형식입니다. `<entityName>_properties_typeProperties_maxConcurrency`의 기본 매개 변수 이름이 있습니다.
* 또한 `recurrence` 속성은 매개 변수가 있습니다. 이 수준에서 해당 수준의 모든 속성은 기본값으로 매개 변수화 되도록 지정 되며 기본값 및 매개 변수 이름이 사용 됩니다. 단, `interval` 속성은 숫자 형식으로 매개 변수화 되 고 매개 변수 이름에는 `<entityName>_properties_typeProperties_recurrence_triggerSuffix`로 접미사로 사용 됩니다. 마찬가지로 `freq` 속성은 문자열이 며 문자열로 매개 변수화 됩니다. 그러나 `freq` 속성은 기본값이 없는 매개 변수화 됩니다. 이름이 단축 되 고 접미사가 붙습니다. `<entityName>_freq`)을 입력합니다.

#### <a name="linkedservices"></a>LinkedServices

* 연결 된 서비스는 고유 합니다. 연결 된 서비스 및 데이터 집합에는 다양 한 형식이 있으므로 유형별 사용자 지정을 제공할 수 있습니다. 이 예제에서는 `AzureDataLakeStore`형식의 모든 연결 된 서비스를 적용 하 고, 특정 템플릿이 적용 되며, 다른 모든 (\*를 통해) 다른 템플릿이 적용 됩니다.
* `connectionString` 속성은 `securestring` 값으로 매개 변수화 되 고 기본값은 없으며 `connectionString`접미사로 표시 되는 약식 매개 변수 이름입니다.
* 속성 `secretAccessKey`는 `AzureKeyVaultSecret` (예: `AmazonS3` 연결 된 서비스)로 발생 합니다. 자동으로 Azure Key Vault 암호로 매개 변수화 되 고 구성 된 Key Vault에서 인출 됩니다. 키 자격 증명 모음을 매개 변수화 할 수도 있습니다.

#### <a name="datasets"></a>데이터 세트

* 데이터 집합에 대해 유형별 사용자 지정을 사용할 수 있지만 명시적으로 \*수준 구성을 사용 하지 않고도 구성을 제공할 수 있습니다. 위의 예제에서 `typeProperties` 아래의 모든 데이터 집합 속성은 매개 변수가 있습니다.

### <a name="default-parameterization-template"></a>기본 매개 변수화 템플릿

다음은 현재 기본 매개 변수화 템플릿입니다. 하나 또는 몇 개의 매개 변수만 추가 해야 하는 경우 기존 매개 변수화 구조를 잃지 않으므로이를 직접 편집 하는 것이 유용할 수 있습니다.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

다음은 단일 값을 기본 매개 변수화 템플릿에 추가 하는 방법의 예입니다. Databricks 연결 된 서비스에 대 한 기존 Databricks 대화형 클러스터 ID를 매개 변수 파일에 추가 하려고 합니다. 아래 파일은 `Microsoft.DataFactory/factories/linkedServices`의 속성 필드에 포함 된 `existingClusterId`를 제외 하 고 위의 파일과 동일 합니다.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>연결된 Resource Manager 템플릿

데이터 팩터리에 대해 CI/CD (지속적인 통합 및 배포)를 설정한 경우 팩터리가 증가 함에 따라 Azure Resource Manager 템플릿 제한이 발생할 수 있습니다. 제한의 예는 리소스 관리자 템플릿의 최대 리소스 수입니다. 팩터리에 대 한 전체 리소스 관리자 템플릿 생성과 함께 많은 팩터리를 수용 하기 위해 이제 Data Factory 연결 된 리소스 관리자 템플릿을 생성 합니다. 이 기능을 사용 하면 전체 팩터리 페이로드가 여러 파일로 분할 되므로 제한에 실행 되지 않습니다.

Git를 구성한 경우에는 연결 된 템플릿이 생성 되 고 `adf_publish` 분기의 전체 리소스 관리자 템플릿과 함께 `linkedTemplates`라는 새 폴더 아래에 저장 됩니다.

![연결된 Resource Manager 템플릿 폴더](media/continuous-integration-deployment/linked-resource-manager-templates.png)

연결된 Resource Manager 템플릿은 일반적으로 마스터 템플릿과 마스터에 연결된 자식 템플릿 세트를 포함합니다. 부모 템플릿은 `ArmTemplate_master.json`이라고 하고 자식 템플릿은 `ArmTemplate_0.json`, `ArmTemplate_1.json` 등의 패턴으로 이름이 지정됩니다. 전체 리소스 관리자 템플릿 대신 연결 된 템플릿을 사용 하려면 `ArmTemplateForFactory.json` (전체 리소스 관리자 템플릿) 대신 `ArmTemplate_master.json`를 가리키도록 CI/CD 작업을 업데이트 합니다. 또한 Resource Manager에서는 연결된 템플릿을 스토리지 계정에 업로드해야 배포 시 Azure에서 액세스할 수 있습니다. 자세한 내용은 [VSTS를 사용하여 연결된 ARM 템플릿 배포](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)를 참조하세요.

배포 작업 전후에 CI/CD 파이프라인에 Data Factory 스크립트를 추가해야 합니다.

Git을 구성하지 않은 경우 연결된 템플릿은 **ARM 템플릿 내보내기** 제스처를 통해 액세스할 수 있습니다.

## <a name="hot-fix-production-branch"></a>핫 픽스 프로덕션 분기

공장을 프로덕션에 배포 하 고 즉시 해결 해야 하는 버그가 있음을 인식 하지만 현재 공동 작업 분기를 배포할 수 없는 경우에는 핫 픽스를 배포 해야 할 수 있습니다. 이 접근 방식은 빠른 수정 엔지니어링 또는 QFE 라고 합니다. 

1.  Azure DevOps에서 프로덕션에 배포 된 릴리스로 이동 하 여 배포 된 마지막 커밋을 찾습니다.

2.  커밋 메시지에서 공동 작업 분기의 커밋 ID를 가져옵니다.

3.  해당 커밋에서 새 핫 픽스 분기를 만듭니다.

4.  Azure Data Factory UX로 이동 하 여이 분기로 전환 합니다.

5.  Azure Data Factory UX를 사용 하 여 버그를 수정 합니다. 변경 내용을 테스트 합니다.

6.  수정이 확인 되 면 **ARM 템플릿 내보내기** 를 클릭 하 여 핫 픽스 리소스 관리자 템플릿을 가져옵니다.

7.  이 빌드를 adf_publish 분기로 수동으로 체크 인 합니다.

8.  Adf_publish 체크 인에 따라 자동으로 트리거되도록 릴리스 파이프라인이 구성 된 경우 새 릴리스가 자동으로 시작 됩니다. 그렇지 않으면 수동으로 릴리스를 큐에 대기 합니다.

9.  테스트 및 프로덕션 팩터리에 핫 픽스 릴리스를 배포 합니다. 이 릴리스에는 이전 프로덕션 페이로드 및 5 단계에서 만든 수정 사항이 포함 되어 있습니다.

10. 이후 릴리스가 동일한 버그로 실행 되지 않도록 핫 픽스에서 개발 분기에 변경 내용을 추가 합니다.

## <a name="best-practices-for-cicd"></a>CI/CD에 대한 모범 사례

데이터 팩터리를 통해 Git 통합을 사용할 때 개발에서 테스트, 프로덕션 순서로 변경 내용을 이동하는 CI/CD 파이프라인이 있는 경우 다음 모범 사례를 적용하는 것이 좋습니다.

-   **Git 통합**. Git 통합을 사용 하 여 개발 데이터 팩터리를 구성 해야 합니다. 테스트 및 프로덕션에 대 한 변경 사항은 CI/CD를 통해 배포 되며 Git 통합이 필요 하지 않습니다.

-   **Data Factory CI/CD 스크립트**. CI/CD의 리소스 관리자 배포 단계를 수행 하기 전에 트리거를 중지/시작 하 고 정리 하는 것과 같은 특정 작업이 필요 합니다. 배포 전후에 powershell 스크립트를 사용 하는 것이 좋습니다. 자세한 내용은 [활성 트리거 업데이트](#update-active-triggers)를 참조 하세요. 

-   **통합 런타임 및 공유**. 통합 런타임은 자주 변경 되지 않으며 CI/CD의 모든 단계에서 유사 합니다. 따라서 Data Factory에서는 CI/CD의 모든 단계에서 동일한 이름 및 동일한 유형의 통합 런타임을 사용해야 합니다. 모든 단계에서 통합 런타임을 공유 하려면 공유 통합 런타임을 포함 하는 경우에만 3 진 팩터리를 사용 하는 것이 좋습니다. 모든 환경에서이 공유 팩터리를 연결 된 통합 런타임 형식으로 사용할 수 있습니다.

-   **Key Vault**. Azure Key Vault 기반 연결 된 서비스를 사용 하는 경우 다른 환경에 대해 별도의 키 자격 증명 모음을 유지 하 여 더 많은 이점을 얻을 수 있습니다. 또한 각각에 대해 개별 권한 수준을 구성할 수도 있습니다. 예를 들어 팀 멤버에 게 프로덕션 암호에 대 한 사용 권한을 부여 하지 않으려고 할 수 있습니다. 이 접근 방식을 따를 경우 모든 단계에서 동일한 비밀 이름을 유지 하는 것이 좋습니다. 동일한 이름을 유지 하는 경우 리소스 관리자 템플릿 매개 변수 중 하나인 키 자격 증명 모음 이름만 변경 되므로 CI/CD 환경에서 리소스 관리자 템플릿을 변경할 필요가 없습니다.

## <a name="unsupported-features"></a>지원되지 않는 기능

- 기본적으로 ADF는 cherry-pick 선택 된 커밋 또는 리소스의 선택적 게시를 허용 _하지_ 않습니다. 게시에는 데이터 팩터리에 대 한 **모든** 변경 내용이 포함 됩니다.

    - 데이터 팩터리 엔터티는 서로 종속 됩니다. 예를 들어 트리거는 파이프라인에 의존 하 고 파이프라인은 데이터 집합 및 기타 파이프라인 등에 종속 됩니다. 리소스 하위 집합을 선택적으로 게시 하면 예기치 않은 동작 및 오류가 발생할 _수 있습니다_ .
    - 가끔 선택적 게시가 필요한 경우에는 핫 픽스를 고려할 수 있습니다. 자세한 내용은 [Hot Fix Production Branch](#hot-fix-production-branch) 항목을 참조 하세요.

-   비공개 분기에서 게시할 수 없습니다.

-   지금은 Bitbucket에서 프로젝트를 호스트할 수 없습니다.
