---
title: SSIS 패키지 실행 작업으로 SSIS 패키지 실행 - Azure | Microsoft Docs
description: 이 문서에서는 Azure Data Factory 파이프라인에서 SSIS 패키지 실행 작업을 사용하여 SSIS(SQL Server Integration Services) 패키지를 실행하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/19/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 7287dc2fccf461cf23c45202336e3d92bc5a40aa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66153205"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Azure Data Factory에서 SSIS 패키지 실행 작업을 사용하여 SSIS 패키지 실행
이 문서에서는 SSIS 패키지 실행 작업을 사용하여 ADF(Azure Data Factory) 파이프라인에서 SSIS 패키지를 실행하는 방법을 설명합니다. 

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS IR(Integration Runtime)이 없는 경우 [자습서: Azure에 SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md)의 단계별 지침에 따라 새로 만듭니다.

## <a name="run-a-package-in-the-azure-portal"></a>Azure Portal에서 패키지 실행
이 섹션에서는 ADF UI(사용자 인터페이스)/앱을 사용하여 SSIS 패키지를 실행하는 SSIS 패키지 실행 작업이 있는 ADF 파이프라인을 만듭니다.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS 패키지 실행 작업으로 파이프라인 만들기
이 단계에서는 ADF UI/앱을 사용하여 파이프라인을 만듭니다. 파이프라인에 SSIS 패키지 실행 작업을 추가하고 SSIS 패키지를 실행하도록 구성합니다. 

1. Azure Portal의 ADF 개요/홈페이지에서 **작성자 및 모니터** 타일을 클릭하여 별도의 탭에서 ADF UI/앱을 시작합니다. 

   ![데이터 팩터리 홈페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   **시작** 페이지에서 **파이프라인 만들기**를 클릭합니다. 

   ![시작 페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. **작업** 도구 상자에서 **일반**을 펼치고, **SSIS 패키지 실행** 작업을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 

   ![SSIS 패키지 실행 작업을 디자이너 화면으로 끌기](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. SSIS 패키지 실행 작업의 **일반** 탭에서 작업 이름과 설명을 입력합니다. 선택적 시간 제한 및 다시 시도 값을 설정합니다.

   ![일반 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. SSIS 패키지 실행 작업의 **설정** 탭에서 패키지가 배포된 SSISDB 데이터베이스와 연결된 Azure-SSIS IR을 선택합니다. 패키지에서 데이터 저장소에 액세스 하려면 Windows 인증을 사용 하는 경우 온-프레미스 SQL 서버/파일을 공유 하는 예를 들어 Azure Files 등을 확인 합니다 **Windows 인증** 확인란을 선택 하 고 패키지에 대 한 도메인/사용자 이름/암호를 입력 실행 합니다. 패키지를 실행하는 데 32비트 런타임이 필요한 경우 **32비트 런타임** 확인란을 선택합니다. **로깅 수준**에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정 로깅 이름을 대신 입력하려면 **사용자 지정** 확인란을 선택합니다. Azure-SSIS IR이 실행 중이고 **수동 입력** 확인란이 선택 취소된 경우 SSISDB에서 기존 폴더/프로젝트/패키지/환경을 찾아 선택할 수 있습니다. **새로 고침** 단추를 클릭하여 새로 추가한 폴더/프로젝트/패키지/환경을 찾아서 선택할 수 있도록 SSISDB에서 페치합니다. 

   ![설정 탭의 속성 설정 - 자동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   경우에 Azure SSIS IR이 아님 또는 **수동 항목** 확인란이 선택 되어, 다음 형식으로 직접 SSISDB에서 패키지 및 환경 경로 입력할 수 있습니다: `<folder name>/<project name>/<package name>.dtsx` 및 `<folder name>/<environment name>`.

   ![설정 탭의 속성 설정 - 수동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

5. SSIS 패키지 실행 작업의 **SSIS 매개 변수** 탭에서 Azure-SSIS IR이 실행 중이고 **설정** 탭의 **수동 입력** 확인란이 선택 취소된 경우 SSISDB에서 선택한 프로젝트/패키지의 기존 SSIS 매개 변수가 표시되므로 값을 할당할 수 있습니다. 그렇지 않으면 매개 변수를 하나씩 입력하여 수동으로 값을 할당할 수 있습니다. 패키지 실행에 성공하려면 매개 변수가 있고 올바르게 입력되었는지 확인합니다. 식, 함수, ADF 시스템 변수 및 ADF 파이프라인 매개 변수/변수를 사용 하 여 해당 값에 동적 콘텐츠를 추가할 수 있습니다. 또는에서 Key Vault (AKV (Azure) 해당 값으로 저장 된 비밀을 사용할 수 있습니다. 이렇게 하려면 클릭 합니다 **AZURE KEY VAULT** 는 관련 매개 변수 옆의 확인란을 선택/편집 기존 AKV 연결 된 서비스 또는 새 대시보드를 만든 및 매개 변수 값에 대 한 비밀 이름/버전을 선택 합니다.  때 AKV 연결 된 서비스를 만들기/편집 선택/편집 하면 기존 AKV 또는 새 대시보드를 만든 따르면 하세요 ADF 관리 되는 id 액세스 권한을 부여 하 여 AKV 하지 않았으면 지금 이미 있는 경우. 다음 형식으로 직접 암호를 입력할 수도 있습니다: `<AKV linked service name>/<secret name>/<secret version>`합니다.

   ![SSIS 매개 변수 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. 에 **연결 관리자** SSIS 패키지 실행 작업의 경우에 Azure SSIS IR이 실행 중일 때 탭 및 **수동 항목** 확인란을 **설정** 탭이 선택 취소 SSISDB에서 선택한 프로젝트/패키지에서 기존 연결 관리자는 해당 속성에 값을 할당할 수 표시 됩니다. 그렇지 않으면 입력할 수 있습니다 하나씩에 해당 속성에 값을 수동으로 할당 – 존재 하 고 성공 하 여 패키지 실행에 대 한 정확 하 게 입력을 확인 하세요. 식, 함수, ADF 시스템 변수 및 ADF 파이프라인 매개 변수/변수를 사용 하 여 해당 속성 값을 동적 콘텐츠를 추가할 수 있습니다. 또는에서 Key Vault (AKV (Azure) 해당 속성 값으로 저장 된 비밀을 사용할 수 있습니다. 이렇게 하려면 클릭 합니다 **AZURE KEY VAULT** 관련 속성 옆의 확인란을 선택/편집 기존 AKV 연결 된 서비스 또는 새 대시보드를 만든를 선택한 다음 속성 값에 대 한 비밀 이름/버전입니다.  때 AKV 연결 된 서비스를 만들기/편집 선택/편집 하면 기존 AKV 또는 새 대시보드를 만든 따르면 하세요 ADF 관리 되는 id 액세스 권한을 부여 하 여 AKV 하지 않았으면 지금 이미 있는 경우. 다음 형식으로 직접 암호를 입력할 수도 있습니다: `<AKV linked service name>/<secret name>/<secret version>`합니다.

   ![연결 관리자 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. SSIS 패키지 실행 작업의 **속성 재정의** 탭에서, SSISDB에서 선택한 패키지에 있는 기존 속성의 경로를 하나씩 입력하여 수동으로 값을 할당할 수 있습니다. 존재하는 속성을 올바르게 입력해야 패키지가 제대로 실행됩니다. 예를 들어 사용자 변수의 값을 재정의하려면 `\Package.Variables[User::YourVariableName].Value` 형식으로 해당 경로를 입력합니다. 식, 함수, ADF 시스템 변수 및 ADF 파이프라인 매개 변수/변수를 사용하여 값에 동적 콘텐츠를 추가할 수도 있습니다.

   ![속성 재정의 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

8. 파이프라인 구성에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사**를 클릭합니다. **파이프라인 유효성 검사 보고서**를 닫으려면 **>>** 를 클릭합니다.

9. **모두 게시** 단추를 클릭하여 ADF에 파이프라인을 게시합니다. 

### <a name="run-the-pipeline"></a>파이프라인 실행
이 단계에서는 파이프라인 실행을 트리거합니다. 

1. 파이프라인 실행을 트리거하려면 도구 모음에서 **트리거**를 클릭하고 **지금 트리거**를 클릭합니다. 

   ![지금 트리거](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. **파이프라인 실행** 창에서 **마침**을 선택합니다. 

### <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. 다른 정보(예: 실행 시작 시간)와 함께 파이프라인 실행 및 해당 상태를 확인합니다. 보기를 새로 고치려면 **새로 고침**을 클릭합니다.

   ![파이프라인 실행](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. **작업** 열에서 **작업 실행 보기** 링크를 클릭합니다. 파이프라인에는 하나의 작업(SSIS 패키지 실행 작업)만 있으므로 하나의 작업 실행만 파이프라인으로 표시됩니다.

   ![작업 실행](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Azure SQL 서버의 SSISDB 데이터베이스에 대해 다음 **쿼리**를 실행하여 패키지가 실행되었는지 확인할 수 있습니다. 

   ```sql
   select * from catalog.executions
   ```

   ![패키지 실행 확인](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. 파이프라인 작업 실행의 출력에서 SSISDB 실행 ID를 가져올 수 있고 더 포괄적인 실행 로그 및 SSMS에서 오류 메시지를 확인하는 ID를 사용할 수도 있습니다.

   ![실행 ID를 가져옵니다.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>트리거를 사용하여 파이프라인 예약

또한 파이프라인이 일정대로(시간별, 일별, 등) 실행되도록 파이프라인에 대해 예약된 트리거를 만들 수도 있습니다. 예를 들어 [데이터 팩터리 만들기 - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)를 참조하세요.

## <a name="run-a-package-with-powershell"></a>PowerShell을 사용하여 패키지 실행
이 섹션에서는 Azure PowerShell을 사용하여 SSIS 패키지를 실행하는 SSIS 패키지 실행 작업이 있는 ADF 파이프라인을 만듭니다. 

[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-az-ps)의 단계별 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.

### <a name="create-an-adf-with-azure-ssis-ir"></a>Azure-SSIS IR이 있는 ADF 만들기
이미 Azure-SSIS IR이 프로비전된 기존 ADF를 사용하거나 [자습서: PowerShell을 통해 Azure에 SSIS 패키지 배포](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)의 단계별 지침에 따라 Azure-SSIS IR이 있는 새 ADF를 만들 수 있습니다.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS 패키지 실행 작업으로 파이프라인 만들기 
이 단계에서는 SSIS 패키지 실행 작업으로 파이프라인을 만듭니다. 이 작업은 SSIS 패키지를 실행합니다. 

1. 다음 예제와 비슷한 내용이 포함된 **RunSSISPackagePipeline.json**이라는 JSON 파일을 **C:\ADF\RunSSISPackage** 폴더에 만듭니다.

   > [!IMPORTANT]
   > 파일을 저장하기 전에 개체 이름, 설명 및 경로, 속성 및 매개 변수 값, 암호 및 기타 변수 값을 바꾸세요. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "mySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "myAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MySecret"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "abc"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "userName": {
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

2. Azure PowerShell에서 `C:\ADF\RunSSISPackage` 폴더로 전환합니다.

3. 파이프라인을 만듭니다 **RunSSISPackagePipeline**을 실행 합니다 **집합 AzDataFactoryV2Pipeline** cmdlet.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   샘플 출력은 다음과 같습니다.

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>파이프라인 실행
사용 된 **Invoke AzDataFactoryV2Pipeline** 파이프라인을 실행 하려면 cmdlet. Cmdlet은 향후 모니터링을 위해 파이프라인 실행 ID를 캡처합니다.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>파이프라인 모니터링

다음 PowerShell 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다. PowerShell 창에서 다음 스크립트를 복사/붙여넣기하고 ENTER 키를 누릅니다. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Azure Portal을 사용하여 파이프라인을 모니터링 할 수도 있습니다. 단계별 지침은 [파이프라인 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.

### <a name="schedule-the-pipeline-with-a-trigger"></a>트리거를 사용하여 파이프라인 예약
이전 단계에서는 파이프라인을 주문형으로 실행했습니다. 일정 트리거를 만들어 파이프라인을 예약형으로 실행할 수도 있습니다(매시간, 매일 등).

1. **C:\ADF\RunSSISPackage** 폴더에 다음 내용이 포함된 **MyTrigger.json**이라는 JSON 파일을 만듭니다. 

   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
2. **Azure PowerShell**에서 **C:\ADF\RunSSISPackage** 폴더로 전환합니다.
3. 실행 합니다 **집합 AzDataFactoryV2Trigger** 트리거를 생성 하는 cmdlet입니다. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. 기본적으로 트리거는 중지된 상태입니다. 실행 하 여 트리거를 시작 합니다 **시작 AzDataFactoryV2Trigger** cmdlet. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. 트리거를 실행 하 여 시작됨인지 확인 합니다 **Get AzDataFactoryV2Trigger** cmdlet. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. 한 시간 후에 다음 명령을 실행합니다. 예를 들어 현재 시간이 오후 3:25(UTC)인 경우 오후 4시(UTC)에 명령을 실행합니다. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Azure SQL Server의 SSISDB 데이터베이스에 대해 다음 쿼리를 실행하여 패키지가 실행되었는지 확인할 수 있습니다. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>다음 단계
다음 블로그 게시물을 참조하십시오.
-   [ADF 파이프라인에서 SSIS 작업을 사용하여 ETL/ELT 워크플로 현대화 및 확장](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
