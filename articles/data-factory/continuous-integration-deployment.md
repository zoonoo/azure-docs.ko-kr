---
title: Azure 데이터 팩터리에서 지속적인 통합 및 제공
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
ms.date: 02/12/2020
ms.openlocfilehash: 6aad01808ad155b745b614d8de6009386f0d2914
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687955"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure 데이터 팩터리에서 지속적인 통합 및 제공

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>개요

지속적인 통합은 코드베이스에 대한 각 변경 사항을 가능한 한 빨리 자동으로 테스트하는 방법입니다.연속 전달은 연속 통합 중에 발생하는 테스트를 따르며 스테이징 또는 프로덕션 시스템에 변경 내용을 푸시합니다.

Azure 데이터 팩터리에서 지속적인 통합 및 제공(CI/CD)은 데이터 팩터리 파이프라인을 한 환경(개발, 테스트, 프로덕션)에서 다른 환경으로 이동하는 것을 의미합니다. Azure 리소스 관리자 템플릿과 데이터 팩터리 UX 통합을 사용하여 CI/CD를 수행할 수 있습니다.

데이터 팩터리 UX에서 **ARM 템플릿** 드롭다운 메뉴에서 리소스 관리자 템플릿을 생성할 수 있습니다. **ARM 템플릿 내보내기를**선택하면 포털은 데이터 팩터리에 대한 리소스 관리자 템플릿과 모든 연결 문자열 및 기타 매개 변수를 포함하는 구성 파일을 생성합니다. 그런 다음 각 환경(개발, 테스트, 프로덕션)에 대해 하나의 구성 파일을 만듭니다. 주 Resource Manager 템플릿 파일은 모든 환경에서 동일하게 유지됩니다.

이 기능과 데모에 대한 9분 간의 소개를 보려면 다음 비디오를 시청하십시오.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD 수명 주기

다음은 Azure Repos Git으로 구성된 Azure 데이터 팩터리의 CI/CD 수명 주기에 대한 샘플 개요입니다. Git 리포지토리를 구성하는 방법에 대한 자세한 내용은 [Azure 데이터 팩터리의 소스 제어를](source-control.md)참조하십시오.

1.  개발 데이터 팩터리는 Azure Repos Git으로 만들어지고 구성됩니다. 모든 개발자는 파이프라인 및 데이터 집합과 같은 Data Factory 리소스를 작성할 수 있는 권한이 있어야 합니다.

1.  개발자가 기능 분기를 변경하면 가장 최근의 변경 내용으로 파이프라인 실행을 디버깅합니다. 파이프라인 실행을 디버깅하는 방법에 대한 자세한 내용은 [Azure Data Factory를 사용하여 반복 개발 및 디버깅을](iterative-development-debugging.md)참조하세요.

1.  개발자가 변경 사항에 만족하면 기능 분기에서 마스터 또는 공동 작업 분기로 끌어오기 요청을 만들어 동료가 변경 내용을 검토합니다.

1.  끌어오기 요청이 승인되고 변경 내용이 마스터 분기에 병합되면 변경 내용을 개발 팩터리에 게시할 수 있습니다.

1.  팀이 테스트 팩터리에 변경 내용을 배포한 다음 프로덕션 팩터에 배포할 준비가 되면 팀은 마스터 분기에서 Resource Manager 템플릿을 내보전합니다.

1.  내보낸 리소스 관리자 템플릿은 테스트 팩터리 및 프로덕션 팩터리에 다른 매개 변수 파일과 함께 배포됩니다.

## <a name="create-a-resource-manager-template-for-each-environment"></a>각 환경에 Resource Manager 템플릿 만들기

1. ARM **템플릿** 목록에서 **ARM 템플릿 내보내기를** 선택하여 개발 환경에서 데이터 팩터리의 리소스 관리자 템플릿을 내보냅니다.

   ![리소스 관리자 템플릿 내보내기](media/continuous-integration-deployment/continuous-integration-image1.png)

1. 테스트 및 프로덕션 데이터 팩터리에서 **ARM 템플릿 가져오기를**선택합니다. 이 작업을 통해 Azure Portal로 이동합니다. 여기서 내보낸 템플릿을 가져올 수 있습니다. **편집기에서 사용자 고유의 템플릿 빌드를** 선택하여 리소스 관리자 템플릿 편집기를 엽니다.

   ![나만의 템플릿 만들기](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. **파일 로드를**선택한 다음 생성된 리소스 관리자 템플릿을 선택합니다. 1단계에서 내보낸 .zip 파일에 있는 **arm_template.json** 파일입니다.

   ![템플릿 편집](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. 설정 섹션에서 연결된 서비스 자격 증명과 같은 구성 값을 입력합니다. 작업이 완료되면 **구매를** 선택하여 리소스 관리자 템플릿을 배포합니다.

   ![설정 섹션](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>연결 문자열

연결 문자열을 구성하는 방법에 대한 자세한 내용은 커넥터의 문서를 참조하십시오. 예를 들어 Azure SQL Database의 경우 [Azure Data Factory를 사용하여 Azure SQL Database 간 데이터 복사](connector-azure-sql-database.md)를 참조하세요. 연결 문자열을 확인하려면 Data Factory UX에서 리소스에 대한 코드 보기를 열 수 있습니다. 코드 보기에서 연결 문자열의 암호 또는 계정 키 부분이 제거됩니다. 코드 보기를 열려면 여기에 강조 표시된 아이콘을 선택합니다.

![코드 보기를 열어 연결 문자열을 확인합니다.](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Azure 파이프라인 릴리스를 사용하여 지속적인 통합 자동화

다음은 여러 환경에 데이터 팩터리 배포를 자동화하는 Azure 파이프라인 릴리스를 설정하기 위한 가이드입니다.

![Azure Pipelines를 사용하는 지속적인 통합 다이어그램](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>요구 사항

-   Azure [리소스 관리자 서비스 끝점을](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)사용하는 Visual Studio Team 파운데이션 서버 또는 Azure 리포지토리에 연결된 Azure 구독입니다.

-   Azure Repos Git 통합으로 구성된 데이터 팩터리입니다.

-   각 환경에 대한 비밀이 포함된 [Azure 키 자격 증명 모음입니다.](https://azure.microsoft.com/services/key-vault/)

### <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines 릴리스 설정

1.  [Azure DevOps에서](https://dev.azure.com/)데이터 팩터리로 구성된 프로젝트를 엽니다.

1.  페이지 왼쪽에서 **파이프라인 을**선택한 다음 **릴리스를**선택합니다.

    ![파이프라인, 릴리스 선택](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  **새 파이프라인을**선택하거나 기존 파이프라인이 있는 경우 **새** 파이프라인및 새 **릴리스 파이프라인을**선택합니다.

1.  빈 **작업** 템플릿을 선택합니다.

    ![빈 작업 선택](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  스테이지 **이름** 상자에 환경의 이름을 입력합니다.

1.  **아티팩트 추가를**선택한 다음 데이터 팩터리로 구성된 리포지토리를 선택합니다. **기본 분기에**대해 **adf_publish** 선택합니다. 기본 **버전의**경우 **기본 분기에서 최신 을**선택합니다.

    ![아티팩트 추가](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager 배포 작업을 추가합니다.

    a.  스테이지 보기에서 **스테이지 작업 보기를**선택합니다.

    ![스테이지 뷰](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  새 태스크를 만듭니다. Azure **리소스 그룹 배포를**검색한 다음 **에 추가를**선택합니다.

    다.  배포 작업에서 대상 데이터 팩터리의 구독, 리소스 그룹 및 위치를 선택합니다. 필요한 경우 자격 증명을 제공합니다.

    d.  **작업** 목록에서 리소스 **그룹 만들기 또는 업데이트 를**선택합니다.

    e.  **템플릿** 상자 옆에 있는 타원**단추(...**) 를 선택합니다. 이 문서의 각 환경 섹션에 대한 리소스 관리자 만들기 템플릿에서 **ARM 가져오기 템플릿을** 사용하여 만든 Azure 리소스 [관리자 템플릿을](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) 찾아봅습니다. adf_publish 분기의 <FactoryName> 폴더에서 이 파일을 찾습니다.

    f.  선택 **...** **템플릿 매개 변수** 상자 옆에 있는 매개 변수 파일을 선택합니다. 선택한 파일은 복사본을 만들었는지 기본 파일인 ARMTemplateParametersForFactory.json을 사용중인지에 따라 달라집니다.

    g.  선택 **...** 재정의 템플릿 매개 변수 상자 옆에 있는 대상 데이터 팩터리에 대한 정보를 **입력합니다.** Azure Key Vault에서 온 자격 증명의 경우 이중 따옴표 사이에 비밀의 이름을 입력합니다. 예를 들어 비밀의 이름이 cred1인 경우 이 값에 **대해 "$(cred1)"를** 입력합니다.

    h. **배포 모드에**대해 **증분을** 선택합니다.

    > [!WARNING]
    > **배포에**대한 **완료** 모드를 선택하면 리소스 관리자 템플릿에 정의되지 않은 대상 리소스 그룹의 모든 리소스를 포함하여 기존 리소스가 삭제될 수 있습니다.

    ![데이터 팩터리 프로드 배포](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  릴리스 파이프라인을 저장합니다.

1. 릴리스를 트리거하려면 **릴리스 만들기를**선택합니다.

   ![릴리스 만들기 선택](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> CI/CD 시나리오에서는 서로 다른 환경의 통합 런타임(IR) 형식이 동일해야 합니다. 예를 들어 개발 환경에서 자체 호스팅된 IR이 있는 경우 동일한 IR도 테스트 및 프로덕션과 같은 다른 환경에서 자체 호스팅되는 형식이어야 합니다. 마찬가지로 여러 단계에서 통합 런타임을 공유하는 경우 개발, 테스트 및 프로덕션과 같은 모든 환경에서 연결된 자체 호스팅으로 통합 런타임을 구성해야 합니다.

### <a name="get-secrets-from-azure-key-vault"></a>Azure 키 볼트에서 비밀 받기

Azure 리소스 관리자 템플릿에 전달할 암호가 있는 경우 Azure 파이프라인 릴리스와 함께 Azure 키 자격 증명 모음을 사용하는 것이 좋습니다.

비밀을 처리하는 방법에는 두 가지가 있습니다.

1.  매개 변수 파일에 비밀을 추가합니다. 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)을 참조하세요.

    게시 분기에 업로드된 매개 변수 파일의 복사본을 만듭니다. 다음 형식을 사용하여 Key Vault에서 얻을 매개 변수의 값을 설정합니다.

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

    이 메서드를 사용하는 경우 키 자격 증명 모음에서 자동으로 암호를 끌어옵니다.

    매개 변수 파일은 게시 분기에 포함되어야 합니다.

1. 이전 섹션에서 설명한 Azure 리소스 관리자 배포 작업 앞에 Azure [키 자격 증명 모음](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) 작업을 추가합니다.

    1.  **작업** 탭에서 새 작업을 만듭니다. Azure **키 자격 증명 모음을** 검색하고 추가합니다.

    1.  키 자격 증명 모음 작업에서 키 자격 증명 모음을 만든 구독을 선택합니다. 필요한 경우 자격 증명을 제공한 다음 키 자격 증명을 선택합니다.

    ![키 볼트 작업 추가](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines 에이전트에 권한 부여

올바른 사용 권한이 설정되지 않은 경우 Azure 키 자격 증명 모음 작업이 액세스 거부 오류로 실패할 수 있습니다. 릴리스에 대한 로그를 다운로드하고 Azure 파이프라인 에이전트에 대한 권한을 부여하는 명령이 포함된 .ps1 파일을 찾습니다. 명령을 직접 실행할 수 있습니다. 또는 파일에서 보안 주체 ID를 복사하고 Azure Portal에서 액세스 정책을 수동으로 추가할 수 있습니다. `Get``List` 필요한 최소 사용 권한입니다.

### <a name="update-active-triggers"></a>활성 트리거 업데이트

활성 트리거를 업데이트하려고 하면 배포에 실패할 수 있습니다. 활성 트리거를 업데이트하려면 수동으로 중지한 다음 배포 후 다시 시작해야 합니다. Azure PowerShell 작업을 사용하여 이 작업을 수행할 수 있습니다.

1.  릴리스의 **작업** 탭에서 Azure **PowerShell** 작업을 추가합니다. 작업 버전 4.*를 선택합니다. 

1.  팩터리 구독을 선택합니다.

1.  **스크립트 파일 경로를** 스크립트 유형으로 선택합니다. 이렇게 하려면 저장소에 PowerShell 스크립트를 저장해야 합니다. 다음 PowerShell 스크립트를 사용하여 트리거를 중지할 수 있습니다.

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

배포 후 트리거를 다시 `Start-AzDataFactoryV2Trigger` 시작하려면 함수와 함께 유사한 단계를 완료할 수 있습니다.

### <a name="sample-pre--and-post-deployment-script"></a>배포 전및 후 스크립트 샘플

다음 샘플 스크립트를 사용하여 배포 전에 트리거를 중지하고 나중에 다시 시작할 수 있습니다. 스크립트에는 제거된 리소스를 삭제하는 코드도 포함됩니다. Azure DevOps git 리포지토리에 스크립트를 저장하고 버전 4.*를 사용하여 Azure PowerShell 작업을 통해 참조합니다.

배포 전 스크립트를 실행할 때 **스크립트 인수** 필드에서 다음 매개 변수의 변형을 지정해야 합니다.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


배포 후 스크립트를 실행할 때 **스크립트 인수** 필드에서 다음 매개 변수의 변형을 지정해야 합니다.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell 작업](media/continuous-integration-deployment/continuous-integration-image11.png)

다음은 배포 전및 사후 배포에 사용할 수 있는 스크립트입니다. 삭제된 리소스 및 리소스 참조를 설명합니다.

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
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

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Resource Manager 템플릿에서 사용자 지정 매개 변수 사용

GIT 모드에 있는 경우 Resource Manager 템플릿의 기본 속성을 재정의하여 템플릿에서 매개 변수화된 속성과 하드 코딩된 속성을 설정할 수 있습니다. 다음 시나리오에서 기본 매개 변수화 템플릿을 재정의할 수 있습니다.

* 자동화된 CI/CD를 사용하고 리소스 관리자 배포 중에 일부 속성을 변경하려고 하지만 속성은 기본적으로 매개 변수화되지 않습니다.
* 팩터리의 규모가 너무 커서 기본 Resource Manager 템플릿이 허용되는 최대 매개 변수(256)보다 많기 때문에 유효하지 않습니다.

이러한 조건에서 기본 매개 변수화 템플릿을 재정의하려면 데이터 팩터리 git 통합의 루트 폴더로 지정된 폴더에 **arm-template-parameters-definition.json이라는** 파일을 만듭니다. 정확한 파일 이름을 사용해야 합니다. Data Factory는 공동 작업 분기뿐만 아니라 Azure Data Factory 포털에 현재 있는 분기에서 이 파일을 읽습니다. 개인 분기에서 파일을 만들거나 편집할 수 있으며, 여기서 UI에서 **ARM 템플릿 내보내기를** 선택하여 변경 내용을 테스트할 수 있습니다. 그런 다음 파일을 공동 작업 분기에 병합할 수 있습니다. 파일이 없는 경우 기본 템플릿이 사용됩니다.

> [!NOTE]
> 사용자 지정 매개 변수화 템플릿은 ARM 템플릿 매개 변수 제한(256)을 변경하지 않습니다. 매개 변수화된 속성 수를 선택하고 줄일 수 있습니다.

### <a name="syntax-of-a-custom-parameters-file"></a>사용자 지정 매개 변수 파일의 구문

다음은 사용자 지정 매개 변수 파일을 만들 때 따라야 할 몇 가지 지침입니다. 파일은 트리거, 파이프라인, 연결된 서비스, 데이터 집합, 통합 런타임 등 각 엔터티 유형에 대한 섹션으로 구성됩니다.
* 관련 엔터티 유형 아래에 속성 경로를 입력합니다.
* 속성 이름을 설정하여 `*` 속성 아래에 있는 모든 속성을 매개변수화하려는 경우를 나타냅니다(재귀가 아닌 첫 번째 수준까지만). 이 구성에 예외를 제공할 수도 있습니다.
* 속성 값을 문자열로 설정하면 속성을 매개변수화할 수 있음을 나타냅니다. 형식을 `<action>:<name>:<stype>`사용합니다.
   *  `<action>` 다음 문자 중 하나가 될 수 있습니다.
      * `=` 즉, 현재 값을 매개 변수의 기본값으로 유지합니다.
      * `-` 즉, 매개 변수의 기본값을 유지하지 않습니다.
      * `|` 는 연결 문자열 또는 키에 대 한 Azure 키 볼트에서 암호에 대 한 특별 한 경우입니다.
   * `<name>` 은 매개 변수의 이름입니다. 비어 있으면 속성의 이름을 지정합니다. 값이 `-` 문자로 시작하면 이름이 짧아집니다. 예를 `AzureStorage1_properties_typeProperties_connectionString` `AzureStorage1_connectionString`들어.
   * `<stype>` 은 매개 변수의 유형입니다. 비어 `<stype>` 있는 경우 기본 `string`형식은 입니다. 지원되는 `string`값: `object`, 에 대한 값: `securestring`및 `bool` `number`.
* 정의 파일에 배열을 지정하면 템플릿의 일치하는 속성이 배열임을 나타냅니다. Data Factory는 배열의 통합 런타임 개체에 지정된 정의를 사용하여 배열의 모든 개체를 통해 이어를 지정합니다. 두 번째 개체, 문자열은 각 반복에 대한 매개 변수의 이름으로 사용되는 속성의 이름이 됩니다.
* 정의는 리소스 인스턴스에만 다를 수 없습니다. 모든 정의는 해당 유형의 모든 리소스에 적용됩니다.
* 기본적으로 Key Vault 암호와 같은 모든 보안 문자열과 연결 문자열, 키 및 토큰과 같은 보안 문자열은 매개 변수화됩니다.
 
### <a name="sample-parameterization-template"></a>샘플 매개 변수화 템플릿

매개 변수화 템플릿의 모양은 다음과 같습니다.

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
다음은 리소스 유형별로 세분화된 이전 템플릿을 구성하는 방법에 대한 설명입니다.

#### <a name="pipelines"></a>파이프라인
    
* 경로의 `activities/typeProperties/waitTimeInSeconds` 모든 속성은 매개 변수화됩니다. 코드 수준 `waitTimeInSeconds` 속성(예: 활동)이 있는 파이프라인의 `Wait` 모든 활동은 기본 이름으로 매개 변수화됩니다. 그러나 리소스 관리자 템플릿에는 기본값이 없습니다. 리소스 관리자 배포 중에 필수 입력이 됩니다.
* 마찬가지로 활동에서 `headers` `Web` 라는 속성은 JObject(JObject)와 `object` 매개 변수화됩니다. 기본값은 소스 팩터리의 값과 동일합니다.

#### <a name="integrationruntimes"></a>통합런타임

* 경로 `typeProperties` 아래의 모든 속성은 해당 기본값과 매개변수화됩니다. 예를 들어 형식 속성 `IntegrationRuntimes` 아래에는 `computeProperties` 두 `ssisProperties`개의 속성이 있습니다. 두 속성 형식은 각각의 기본값과 형식(개체)으로 만들어집니다.

#### <a name="triggers"></a>트리거

* 아래에서 `typeProperties`두 속성은 매개 변수화됩니다. 첫 번째는 `maxConcurrency`기본값을 갖도록 지정되고 형식입니다.`string` 기본 매개 변수 `<entityName>_properties_typeProperties_maxConcurrency`이름이 있습니다.
* 속성도 `recurrence` 매개 변수화됩니다. 그 아래에는 해당 수준의 모든 속성이 기본값과 매개 변수 이름으로 매개 변수화되도록 지정됩니다. 예외는 형식으로 `interval` `number`매개 변수화 된 속성입니다. 매개 변수 이름은 에 `<entityName>_properties_typeProperties_recurrence_triggerSuffix`접미사가 있습니다. 마찬가지로 속성은 `freq` 문자열이며 문자열로 매개 변수화됩니다. 그러나 `freq` 속성은 기본값 없이 매개 변수화됩니다. 이름이 짧아지고 접미사가 붙습니다. `<entityName>_freq`)을 입력합니다.

#### <a name="linkedservices"></a>링크된 서비스

* 연결된 서비스는 고유합니다. 연결된 서비스 및 데이터 집합에는 다양한 형식이 있으므로 유형별 사용자 지정을 제공할 수 있습니다. 이 예제에서는 형식의 `AzureDataLakeStore`연결된 모든 서비스에 대해 특정 템플릿이 적용됩니다. 다른 모든 (via)에 `*`대해 다른 템플릿이 적용됩니다.
* 속성은 `connectionString` `securestring` 값으로 매개 변수화됩니다. 기본값이 없습니다. 에 접미사가 있는 단축된 매개 변수 `connectionString`이름이 있습니다.
* 속성은 `secretAccessKey` `AzureKeyVaultSecret` (예를 들어, 아마존 S3 연결 된 서비스에서) 발생 합니다. Azure 키 자격 증명 모음 보안 검색대로 자동으로 매개 변수화되고 구성된 키 자격 증명 모음에서 가져옵니다. 키 볼트 자체를 매개 변수화할 수도 있습니다.

#### <a name="datasets"></a>데이터 세트

* 데이터 집합에 대해 형식별 사용자 지정을 사용할 수 있지만 \*명시적으로 -level 구성없이 구성을 제공할 수 있습니다. 앞의 예제에서는 아래의 `typeProperties` 모든 데이터 집합 속성이 매개 변수화됩니다.

### <a name="default-parameterization-template"></a>기본 매개 변수화 템플릿

다음은 현재 기본 매개 변수화 템플릿입니다. 몇 가지 매개 변수만 추가해야 하는 경우 기존 매개 변수화 구조를 잃지 않으므로 이 템플릿을 직접 편집하는 것이 좋습니다.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
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
                    "poolName": "=",
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

다음 예제에서는 기본 매개 변수화 템플릿에 단일 값을 추가하는 방법을 보여 주며 있습니다. Databricks 연결된 서비스에 대한 기존 Azure Databricks 대화형 클러스터 ID만 매개 변수 파일에 추가하려고 합니다. 이 파일은 의 속성 필드 `existingClusterId` 아래에 추가된 경우를 제외하고 `Microsoft.DataFactory/factories/linkedServices`이전 파일과 동일합니다.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
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
            "poolName": "=",
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

데이터 팩터리에 대해 CI/CD를 설정한 경우 팩터리가 커질수록 Azure 리소스 관리자 템플릿 제한을 초과할 수 있습니다. 예를 들어 리소스 관리자 템플릿의 최대 리소스 수입니다. 팩터리의 전체 리소스 관리자 템플릿을 생성하는 동안 대규모 팩터리를 수용하기 위해 Data Factory는 연결된 리소스 관리자 템플릿을 생성합니다. 이 기능을 사용하면 전체 팩터리 페이로드가 여러 파일로 세분화되므로 제한에 제약을 받지 않습니다.

Git을 구성한 경우 링크된 템플릿은 링크된 템플릿이라는 새 폴더의 adf_publish 분기의 전체 리소스 관리자 템플릿과 함께 생성되고 저장됩니다.

![연결된 Resource Manager 템플릿 폴더](media/continuous-integration-deployment/linked-resource-manager-templates.png)

연결된 리소스 관리자 템플릿은 일반적으로 마스터 템플릿과 마스터에 연결된 하위 템플릿 집합으로 구성됩니다. 부모 템플릿을 ArmTemplate_master.json이라고 하며 하위 템플릿은 패턴 ArmTemplate_0.json, ArmTemplate_1.json 등의 이름으로 지정됩니다. 

전체 리소스 관리자 템플릿 대신 연결된 템플릿을 사용하려면 CI/CD 작업을 업데이트하여 ArmTemplateForFactory.json(전체 리소스 관리자 템플릿) 대신 ArmTemplate_master.json을 가리킵니다. 또한 리소스 관리자는 Azure가 배포 하는 동안 액세스할 수 있도록 저장소 계정에 연결 된 템플릿을 업로드 해야 합니다. 자세한 내용은 [VSTS를 사용 하](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)여 링크 된 리소스 관리자 템플릿 배포를 참조 합니다.

배포 작업 전후에 CI/CD 파이프라인에 Data Factory 스크립트를 추가해야 합니다.

Git이 구성되지 않은 경우 ARM 템플릿 목록에서 ARM **내보내기 템플릿을** 통해 연결된 **템플릿에** 액세스할 수 있습니다.

## <a name="hotfix-production-branch"></a>핫픽스 생산 분기

팩터리를 프로덕션에 배포하고 즉시 수정해야 하는 버그가 있지만 현재 공동 작업 분기를 배포할 수 없는 경우 핫픽스를 배포해야 할 수 있습니다. 이 방법을 빠른 수정 엔지니어링 또는 QFE라고 합니다.

1.    Azure DevOps에서 프로덕션에 배포된 릴리스로 이동합니다. 배포된 마지막 커밋을 찾습니다.

2.    커밋 메시지에서 공동 작업 분기의 커밋 ID를 가져옵니다.

3.    해당 커밋에서 새 핫픽스 분기를 만듭니다.

4.    Azure 데이터 팩터리 UX로 이동하여 핫픽스 분기로 전환합니다.

5.    Azure 데이터 팩터리 UX를 사용하여 버그를 수정합니다. 변경 내용을 테스트합니다.

6.    수정 프로그램이 확인되면 **ARM 내보내기 템플릿을** 선택하여 핫픽스 리소스 관리자 템플릿을 가져옵니다.

7.    이 빌드를 adf_publish 분기에 수동으로 확인합니다.

8.    adf_publish 체크 인에 따라 자동으로 트리거하도록 릴리스 파이프라인을 구성한 경우 새 릴리스가 자동으로 시작됩니다. 그렇지 않으면 릴리스를 수동으로 큐에 대기합니다.

9.    핫픽스 릴리스를 테스트 및 프로덕션 팩터에 배포합니다. 이 릴리스에는 이전 프로덕션 페이로드와 5단계에서 수정한 수정 사항이 포함되어 있습니다.

10.    이후 릴리스에 동일한 버그가 포함되지 않도록 핫픽스의 변경 내용을 개발 분기에 추가합니다.

## <a name="best-practices-for-cicd"></a>CI/CD에 대한 모범 사례

데이터 팩터리와 Git 통합을 사용하고 있고 변경 내용을 개발에서 테스트로 이동한 다음 프로덕션으로 이동하는 CI/CD 파이프라인이 있는 경우 다음 모범 사례를 사용하는 것이 좋습니다.

-   **Git 통합**. Git 통합을 사용하여 개발 데이터 팩터리만 구성해야 합니다. 테스트 및 프로덕션에 대한 변경 사항은 CI/CD를 통해 배포되며 Git 통합이 필요하지 않습니다.

-   **Data Factory CI/CD 스크립트**. CI/CD의 리소스 관리자 배포 단계 전에 트리거 중지 및 다시 시작 및 정리 수행과 같은 특정 작업을 완료해야 합니다. 배포 전후에 PowerShell 스크립트를 사용하는 것이 좋습니다. 자세한 내용은 [활성 트리거 업데이트](#update-active-triggers)를 참조하십시오.

-   **통합 런타임 및 공유**. 통합 런타임은 자주 변경되지 않으며 CI/CD의 모든 단계에서 유사합니다. 따라서 Data Factory는 CI/CD의 모든 단계에서 동일한 이름과 유형의 통합 런타임을 갖출 것으로 예상합니다. 모든 단계에서 통합 런타임을 공유하려면 삼차 팩터리를 사용하여 공유 통합 런타임을 포함하는 것이 좋습니다. 모든 환경에서 이 공유 팩터리를 연결된 통합 런타임 유형으로 사용할 수 있습니다.

-   **키 볼트**. Azure Key Vault를 기반으로 연결된 서비스를 사용하는 경우 다른 환경에 대해 별도의 키 자격 증명 모음을 유지하여 이 서비스를 더 활용할 수 있습니다. 각 키 자격 증명 모음에 대해 별도의 권한 수준을 구성할 수도 있습니다. 예를 들어 팀 구성원이 프로덕션 암호에 대한 권한을 갖지 않도록 할 수 있습니다. 이 방법을 따르는 경우 모든 단계에서 동일한 비밀 이름을 유지하는 것이 좋습니다. 동일한 이름을 유지하는 경우 CI/CD 환경에서 리소스 관리자 템플릿을 변경할 필요가 없습니다.

## <a name="unsupported-features"></a>지원되지 않는 기능

- 데이터 팩터리는 의도적으로 커밋의 체리 피킹 또는 리소스의 선택적 게시를 허용하지 않습니다. 게시에는 데이터 팩터리에서 변경한 모든 내용이 포함됩니다.

    - 데이터 팩터리 엔터티는 서로 종속됩니다. 예를 들어 트리거는 파이프라인에 따라 다르며 파이프라인은 데이터 집합 및 기타 파이프라인에 따라 달라집니다. 리소스 하위 집합을 선택적으로 게시하면 예기치 않은 동작과 오류가 발생할 수 있습니다.
    - 드물게 선택적 게시가 필요한 경우 핫픽스를 사용하는 것이 좋습니다. 자세한 내용은 [핫픽스 프로덕션 분기를](#hotfix-production-branch)참조하십시오.

-   프라이빗 분기에서 게시할 수 없습니다.

-   현재 Bitbucket에서 프로젝트를 호스팅할 수 없습니다.
