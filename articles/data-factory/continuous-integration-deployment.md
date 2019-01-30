---
title: Azure Data Factory의 지속적인 통합 및 지속적인 업데이트 | Microsoft Docs
description: 지속적인 통합 및 지속적인 업데이트를 사용하여 환경(개발, 테스트, 프로덕션) 간에 Data Factory 파이프라인을 이동하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: douglasl
ms.openlocfilehash: 80b594eb8d926465f37771e2e6911f9ab3e63f1f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54423819"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Azure Data Factory의 CI/CD(지속적인 통합 및 지속적인 업데이트)

지속적인 통합은 코드 베이스에 자동으로 이루어진 변경 내용을 각각 가능한 빨리 테스트하는 방법입니다. 지속적인 업데이트는 지속적인 통합 중에 발생하는 테스트를 수행하고, 변경 내용을 준비 또는 프로덕션 시스템에 푸시합니다.

Azure Data Factory의 경우 지속적인 통합 및 지속적인 업데이트는 환경(개발, 테스트, 프로덕션) 간에 Data Factory 파이프라인을 이동하는 것입니다. 지속적인 통합 및 지속적인 업데이트를 수행하기 위해 Azure Resource Manager 템플릿과 Data Factory UI의 통합을 사용할 수 있습니다. **ARM 템플릿** 옵션을 선택하는 경우 Data Factory UI가 Resource Manager 템플릿을 생성할 수 있습니다. **ARM 템플릿 내보내기**를 선택하는 경우 포털에서는 Data Factory의 Resource Manager 템플릿과 모든 연결 문자열 및 기타 매개 변수를 포함하는 구성 파일을 생성합니다. 그런 다음, 각 환경(개발, 테스트, 프로덕션)에 하나의 구성 파일을 만들어야 합니다. 주 Resource Manager 템플릿 파일은 모든 환경에서 동일하게 유지됩니다.

9분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

## <a name="create-a-resource-manager-template-for-each-environment"></a>각 환경에 Resource Manager 템플릿 만들기
**ARM 템플릿 내보내기**를 선택하여 개발 환경에서 Data Factory에 Resource Manager 템플릿을 내보냅니다.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

그런 다음, 테스트 Data Factory 및 프로덕션 Data Factory로 이동하고, **ARM 템플릿 가져오기**를 선택합니다.

![](media/continuous-integration-deployment/continuous-integration-image2.png)

이 작업을 통해 Azure Portal로 이동합니다. 여기서 내보낸 템플릿을 가져올 수 있습니다. **편집기에서 고유한 템플릿 빌드** 및 **파일 로드**를 차례로 선택하고, 생성된 Resource Manager 템플릿을 선택합니다. 설정을 입력하면, 프로덕션 환경에서 데이터 팩터리 및 전체 파이프라인을 가져옵니다.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

**파일 로드**를 선택하여 내보낸 Resource Manager 템플릿을 선택하고 모든 구성 값(예: 연결된 서비스)을 제공합니다.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**연결 문자열** 개별 커넥터에 대한 문서에서 연결 문자열을 만드는 데 필요한 정보를 찾을 수 있습니다. 예를 들어 Azure SQL Database의 경우 [Azure Data Factory를 사용하여 Azure SQL Database 간 데이터 복사](connector-azure-sql-database.md)를 참조하세요. 올바른 연결 문자열을 확인하려면(예: 연결된 서비스의 경우) Data Factory UI에서 리소스에 대한 코드 보기를 열 수도 있습니다. 그러나 코드 보기에서 연결 문자열의 암호 또는 계정 키 부분은 제거됩니다. 코드 보기를 열려면 다음 스크린샷에서 강조 표시된 아이콘을 선택합니다.

![코드 보기를 열어 연결 문자열 확인](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>연속 통합 수명 주기
Data Factory UI에서 Azure Repos GIT 통합을 사용하도록 설정한 후에 사용할 수 있는 지속적인 통합 및 지속적인 업데이트의 전체 수명 주기는 다음과 같습니다.

1.  모든 개발자가 파이프라인, 데이터 세트 등과 같은 Data Factory 리소스를 작성할 수 있는 Azure Repos를 사용하여 개발 데이터 팩터리를 설정합니다.

1.  그런 다음, 개발자는 파이프라인 등의 리소스를 수정할 수 있습니다. 수정하는 경우 **디버그**를 선택하여 가장 최근 변경 내용을 사용하여 파이프라인을 실행하는 방법을 확인할 수 있습니다.

1.  개발자가 해당 변경 내용을 충족하면 해당 분기에서 마스터 분기(또는 공동 작업 분기)로 끌어오기 요청을 만들어서 피어에서 해당 변경 내용을 검토할 수 있습니다.

1.  변경 내용이 마스터 분기에 있다면 **게시**를 선택하여 개발 팩터리에 게시할 수 있습니다.

1.  팀에서 테스트 팩터리 및 프로덕션 팩터리로 변경 내용의 수준을 올릴 준비가 되면 마스터 분기 또는 다른 분기(해당 마스터 분기가 라이브 개발 Data Factory를 백업하는 경우)에서 Resource Manager 템플릿을 내보낼 수 있습니다.

1.  내보낸 Resource Manager 템플릿은 테스트 팩터리 및 프로덕션 팩터리에 다른 매개 변수 파일을 사용하여 배포될 수 있습니다.

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Azure Pipelines 릴리스를 사용하여 지속적인 통합 자동화

여러 환경으로의 데이터 팩터리 배포를 자동화할 수 있도록 Azure Pipelines 릴리스를 설정하는 단계는 다음과 같습니다.

![Azure Pipelines를 사용하는 지속적인 통합 다이어그램](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>요구 사항

-    [*Azure Resource Manager 서비스 엔드포인트*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)를 사용하여 Team Foundation Server 또는 Azure Repos에 연결된 Azure 구독

-   Azure Repos Git 통합이 구성된 Data Factory

-   비밀을 포함한  [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 

### <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines 릴리스 설정

1.  Data Factory를 사용하여 구성된 것과 동일한 프로젝트에서 Azure Repos 페이지로 이동합니다.

1.  위쪽 메뉴에서 **Azure Pipelines** &gt; **릴리스** &gt; **릴리스 정의 만들기**를 차례로 클릭합니다.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  **빈 프로세스** 템플릿을 선택합니다.

1.  환경의 이름을 입력합니다.

1.  Git 아티팩트를 추가하고 Data Factory를 사용하여 구성된 동일한 리포지토리를 선택합니다. 최신 기본 버전을 사용하여 `adf_publish`를 기본 분기로 선택합니다.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager 배포 작업을 추가합니다.

    a.  새 작업을 만들고, **Azure 리소스 그룹 배포**를 검색하고, 추가합니다.

    b.  배포 작업에서 대상 Data Factory의 구독, 리소스 그룹 및 위치를 선택하고, 필요한 경우 자격 증명을 제공합니다.

    다.  **리소스 그룹 만들기 또는 업데이트** 작업을 선택합니다.

    d.  **템플릿 매개 변수 재정의** 필드 **…** 를 선택합니다. 포털에서 게시 작업으로 만들어진 Resource Manager 템플릿(*ARMTemplateForFactory.json*)을 찾아봅니다. `adf_publish` 분기의 `<FactoryName>` 폴더에서 이 파일을 찾습니다.

    e.  매개 변수 파일에 동일한 작업을 수행합니다. 복사본을 만들었는지 또는 기본 파일 *ARMTemplateParametersForFactory.json*을 사용하는지에 따라 올바른 파일을 선택합니다.

    f.  **템플릿 매개 변수 재정의** 필드 옆에 있는 **…** 을 선택하고 대상 Data Factory에 대한 정보를 입력합니다. 키 자격 증명 모음에서 제공하는 자격 증명의 경우 암호에 다음과 같은 형식의 동일한 이름을 사용합니다. 암호 이름이 `cred1`이라고 가정하면 `"$(cred1)"`(따옴표 포함)를 입력합니다.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. **증분** 배포 모드를 선택합니다.

    > [!WARNING]
    > **전체** 배포 모드를 선택하면 Resource Manager 템플릿에 정의되지 않은 대상 리소스 그룹의 모든 리소스를 포함하여 기존 리소스가 삭제될 수 있습니다.

1.  릴리스 파이프라인을 저장합니다.

1.  이 릴리스 파이프라인에서 새 릴리스를 만듭니다.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>선택 사항 - Azure Key Vault에서 비밀을 가져옵니다.

Azure Resource Manager 템플릿에 전달할 비밀이 있는 경우 Azure Pipelines 릴리스에서 Azure Key Vault를 사용하는 것이 좋습니다.

암호를 처리하는 두 가지 방법이 있습니다.

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

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines 에이전트에 권한 부여
Azure Key Vault 작업에서 액세스 거부 오류가 표시되며 통합 런타임이 실패할 수 있습니다. 릴리스에 대한 로그를 다운로드하고, Azure Pipelines 에이전트에 권한을 부여하는 명령을 사용하여 `.ps1` 파일을 찾습니다. 직접 명령을 실행할 수 있습니다. 또는 파일에서 보안 주체 ID를 복사하고 Azure Portal에 액세스 정책을 수동으로 추가할 수 있습니다. (*가져오기* 및 *나열*은 필요한 최소 권한입니다.)

### <a name="update-active-triggers"></a>활성 트리거 업데이트
활성 트리거를 업데이트하려고 하면 배포에 실패할 수 있습니다. 활성 트리거를 업데이트하려면 수동으로 중단하고 배포 후에 시작해야 합니다. 다음 예제와 같이 이를 위해 Azure PowerShell 작업을 추가할 수 있습니다.

1.  릴리스의 [작업] 탭에서 **Azure PowerShell**을 검색하여 추가합니다.

1.  **Azure Resource Manager**를 연결 형식으로 선택하고 구독을 선택합니다.

1.  **인라인 스크립트**를 스크립트 형식으로 선택한 다음, 코드를 제공합니다. 다음 예제에서는 트리거를 중지합니다.

    ```powershell
    $triggersADF = Get-AzureRmDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

비슷한 단계를 수행하고 유사한 코드(`Start-AzureRmDataFactoryV2Trigger` 함수 포함)를 사용하여 배포 후에 트리거를 다시 시작할 수 있습니다.

> [!IMPORTANT]
> 지속적인 통합 및 지속적인 배포 시나리오에서는 서로 다른 환경 간의 Integration Runtime 유형이 동일해야 합니다. 예를 들어 개발 환경에 *자체 호스팅* IR(Integration Runtime)이 있는 경우 테스트 및 프로덕션과 같은 다른 환경에서도 동일한 IR이 *자체 호스팅* 유형이어야 합니다. 마찬가지로 여러 단계에서 통합 런타임을 공유하는 경우 개발, 테스트 및 프로덕션과 같은 모든 환경에서 통합 런타임을 *연결된 자체 호스팅*으로 구성해야 합니다.

## <a name="sample-deployment-template"></a>샘플 배포 템플릿

Azure Pipelines에서 가져올 수 있는 샘플 배포 템플릿은 다음과 같습니다.

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>트리거를 중지 및 다시 시작하고 정리하는 샘플 스크립트

배포하기 전에 트리거를 중지하고 나중에 트리거를 다시 시작하는 샘플 스크립트는 다음과 같습니다. 스크립트에는 제거된 리소스를 삭제하는 코드도 포함됩니다. 최신 버전의 Azure PowerShell을 설치하려면 [PowerShellGet으로 Windows에 Azure PowerShell 설치](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.9.0)를 참조하세요.

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
$triggersADF = Get-AzureRmDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
        Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzureRmDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzureRmDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzureRmDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzureRmDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzureRmDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzureRmDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzureRmDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzureRmResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzureRmResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Resource Manager 템플릿에서 사용자 지정 매개 변수 사용

Resource Manager 템플릿에 대한 사용자 지정 매개 변수를 정의할 수 있습니다. 리포지토리의 루트 폴더에 `arm-template-parameters-definition.json`이라는 파일이 있으면 됩니다. 파일 이름은 여기에 표시된 이름과 정확히 일치해야 합니다. Data Factory는 공동 작업 분기만이 아니라 현재 작업 중인 모든 분기에서 파일을 읽으려고 합니다. 파일이 없는 경우 Data Factory에서 기본 매개 변수와 값을 사용합니다.

### <a name="syntax-of-a-custom-parameters-file"></a>사용자 지정 매개 변수 파일의 구문

사용자 지정 매개 변수 파일을 작성할 때 사용할 몇 가지 지침은 다음과 같습니다. 이 구문의 예제를 보려면 다음 섹션, [샘플 사용자 지정 매개 변수 파일](#sample)을 참조하세요.

1. 정의 파일에서 배열을 지정할 때 템플릿의 일치하는 속성이 배열임을 나타냅니다. Data Factory는 배열의 통합 런타임 개체에 지정된 정의를 사용하여 배열에 있는 모든 개체를 반복합니다. 두 번째 개체, 문자열은 각 반복에 대한 매개 변수의 이름으로 사용되는 속성의 이름이 됩니다.

    ```json
    ...
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
            }
        }
    },
    ...
    ```

2. 속성 이름을 `*`로 설정하면 템플릿에서 명시적으로 정의된 속성을 제외하고 해당 수준의 모든 속성을 사용하도록 하려는 것을 나타냅니다.

3. 문자열로 속성의 값을 설정할 때 속성을 매개 변수화하려는 것을 나타냅니다. `<action>:<name>:<stype>` 양식을 사용합니다.
    1.  `<action>`은 다음 문자 중 하나일 수 있습니다. 
        1.  `=`는 매개 변수에 대한 기본값으로 현재 값을 유지하는 것을 의미합니다.
        2.  `-`는 매개 변수에 대한 기본값을 유지하지 않는 것을 의미합니다.
        3.  `|`는 연결 문자열에 대한 Azure Key Vault에서 비밀에 대한 특수 사례입니다.
    2.  `<name>`은 매개 변수의 이름입니다. `<name`>이 비어 있는 경우 매개 변수의 이름을 사용합니다. 
    3.  `<stype>`은 매개 변수의 형식입니다. `<stype>`이 비어 있는 경우 기본 형식은 문자열입니다.
4.  매개 변수 이름의 시작 부분에 `-` 문자를 입력하는 경우 전체 Resource Manager 매개 변수 이름은 `<objectName>_<propertyName>`으로 단축됩니다.
예를 들어 `AzureStorage1_properties_typeProperties_connectionString`은 `AzureStorage1_connectionString`으로 단축됩니다.


### <a name="sample"></a> 샘플 사용자 지정 매개 변수 파일

다음 예제에서는 샘플 매개 변수 파일을 보여 줍니다. 이 샘플을 참조로 사용하여 사용자 지정 매개 변수 파일을 만듭니다. 제공한 파일의 JSON 형식이 올바르지 않으면 Data Factory에서 브라우저 콘솔에 오류 메시지를 출력하고, Data Factory UI에 표시된 기본 매개 변수와 값으로 돌아갑니다.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {},
    "Microsoft.DataFactory/factories/integrationRuntimes": {
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
                    }
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
                    "connectionString": "|:-connectionString:secureString"
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
        }
    }
}
```

## <a name="linked-resource-manager-templates"></a>연결된 Resource Manager 템플릿

Data Factory에 대해 지속적인 통합 및 배포(CI/CD)를 설정한 경우 팩터리가 커질수록 Resource Manager 템플릿에서 최대 리소스 수 또는 최대 페이로드와 같은 Resource Manager 템플릿 제한 문제가 발생합니다. 이와 같은 시나리오의 경우, Data Factory는 팩터리에 대한 전체 Resource Manager 템플릿 생성과 함께 연결된 Resource Manager 템플릿도 생성합니다. 따라서 팩터리 전체 페이로드가 여러 파일로 분류되어 위에서 말한 제한 문제가 발생하지 않습니다.

Git을 구성한 경우 연결된 템플릿이 생성되고 `adf_publish` 분기에서 `linkedTemplates`라는 새 폴더 아래에 전체 Resource Manager 템플릿과 함께 저장됩니다.

![연결된 Resource Manager 템플릿 폴더](media/continuous-integration-deployment/linked-resource-manager-templates.png)

연결된 Resource Manager 템플릿은 일반적으로 마스터 템플릿과 마스터에 연결된 자식 템플릿 세트를 포함합니다. 부모 템플릿은 `ArmTemplate_master.json`이라고 하고 자식 템플릿은 `ArmTemplate_0.json`, `ArmTemplate_1.json` 등의 패턴으로 이름이 지정됩니다. 전체 Resource Manager 템플릿을 사용하는 것에서 연결된 템플릿을 사용하는 것으로 전환하려면 CI/CD 작업이 `ArmTemplateForFactory.json`(즉, 전체 Resource Manager 템플릿) 대신 `ArmTemplate_master.json`을 가리키도록 업데이트합니다. 또한 Resource Manager에서는 연결된 템플릿을 스토리지 계정에 업로드해야 배포 시 Azure에서 액세스할 수 있습니다. 자세한 내용은 [VSTS를 사용하여 연결된 ARM 템플릿 배포](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)를 참조하세요.

배포 작업 전후에 CI/CD 파이프라인에 Data Factory 스크립트를 추가해야 합니다.

Git을 구성하지 않은 경우 연결된 템플릿은 **ARM 템플릿 내보내기** 제스처를 통해 액세스할 수 있습니다.

## <a name="best-practices-for-cicd"></a>CI/CD에 대한 모범 사례

데이터 팩터리를 통해 Git 통합을 사용할 때 개발에서 테스트, 프로덕션 순서로 변경 내용을 이동하는 CI/CD 파이프라인이 있는 경우 다음 모범 사례를 적용하는 것이 좋습니다.

-   **Git 통합**. Git 통합을 사용해 개발 데이터 팩터리만 구성하면 됩니다. 테스트 및 프로덕션에 대한 변경 내용은 CI/CD를 통해 배포되므로 Git 통합이 필요 없습니다.

-   **Data Factory CI/CD 스크립트**. CI/CD에서의 Resource Manager 배포 단계 전에 트리거 중지 및 다른 종류의 팩터리 정리와 같은 작업을 처리해야 합니다. 이러한 모든 작업을 처리해 주는 [이 스크립트](#sample-script-to-stop-and-restart-triggers-and-clean-up)를 사용하는 것이 좋습니다. 배포 전과 후에 한 번씩 적절한 플래그를 사용하여 스크립트를 실행합니다.

-   **통합 런타임 및 공유**. 통합 런타임은 데이터 팩터리의 인프라 구성 요소 중 하나로, 자주 변경이 되지 않으며 CI/CD의 모든 단계에서 유사합니다. 따라서 Data Factory에서는 CI/CD의 모든 단계에서 동일한 이름 및 동일한 유형의 통합 런타임을 사용해야 합니다. 모든 단계에서 통합 런타임을 공유하려는 경우(예: 셀프 호스팅 통합 런타임), 한 가지 공유 방법은 공유 통합 런타임을 포함하기 위해 3개로 구성된 팩터리에 셀프 호스팅 IR을 호스팅하는 것입니다. 그런 다음 연결된 IR 유형으로 개발/테스트/프로덕션에서 통합 런타임을 사용할 수 있습니다.

-   **Key Vault**. 권장 Azure Key Vault 기반 연결된 서비스를 사용하는 경우 잠재적으로 개발/테스트/프로덕션에 대한 개별 키 자격 증명 모음을 유지함으로써 해당 서비스를 한층 더 효과적으로 활용할 수 있습니다. 또한 각각에 대해 개별 권한 수준을 구성할 수도 있습니다. 팀원이 프로덕션 비밀에 대한 권한을 갖지 않도록 할 수도 있습니다. 또한 모든 단계에서 동일한 비밀 이름을 유지하는 것이 좋습니다. 동일한 이름을 유지할 경우 Resource Manager 템플릿 매개 변수 중 하나인 키 자격 증명 모음 이름만 변경하면 되므로 CI/CD에서 Resource Manager 템플릿을 변경할 필요가 없습니다.

## <a name="unsupported-features"></a>지원되지 않는 기능

-   데이터 팩터리 엔터티는 서로 종속되어 있으므로 개별 리소스를 게시할 수 없습니다. 예를 들어, 트리거는 파이프라인에 종속되고, 파이프라인은 데이터 세트 및 다른 파이프라인 등에 종속됩니다. 변경 종속성을 추적하는 것은 어렵습니다. 수동으로 게시할 리소스를 선택할 수 있으면 전체 변경 내용 집합의 하위 집합만 선택할 수 있으며, 이는 게시 후 예기치 않은 동작으로 이어질 수 있습니다.

-   개인 분기에서 게시할 수 없습니다.

-   Bitbucket에서 프로젝트를 호스팅할 수 없습니다.
