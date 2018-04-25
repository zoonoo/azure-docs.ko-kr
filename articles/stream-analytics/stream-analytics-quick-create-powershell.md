---
title: Azure PowerShell을 사용하여 Stream Analytics 작업 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 Azure PowerShell 모듈을 사용하여 Azure Stream Analytics 작업을 배포하고 실행하는 방법을 자세히 설명합니다.
services: stream-analytics
keywords: Stream Analytics, 클라우드 작업, Azure PowerShell, 작업 입력, 작업 출력, 작업 변환
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 8a1036531ea0e7c1426224bc4d42c83e9049cabf
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Stream Analytics 작업 만들기

Azure PowerShell 모듈은 PowerShell cmdlet 또는 스크립트를 사용하여 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 빠른 시작에서는 Azure PowerShell 모듈을 사용하여 Azure Stream Analytics 작업을 배포하고 실행하는 방법을 자세히 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure 구독이 아직 없는 경우 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.  

* 이 빠른 시작에서는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. 로컬 컴퓨터에 설치된 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) 문서를 참조하세요. 이 문서의 시나리오는 Blob 저장소에서 데이터를 읽고 데이터를 변환한 다음, 동일한 Blob 저장소의 다른 컨테이너에 다시 쓰는 방법을 설명합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

`Connect-AzureRmAccount` 명령을 사용하여 Azure 구독에 로그인하고 팝업 브라우저에 Azure 자격 증명을 입력합니다. 구독이 여러 개인 경우, 로그인 한 후 아래 cmdlet을 실행하여 빠른 시작에 사용할 구독을 선택합니다. <your subscription>을 구독의 이름으로 바꿔야 합니다.  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# Select the Azure subscription you want to use to create the resource group.
Get-AzureRmSubscription `
  -SubscriptionName “<your subscription>” | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>입력 데이터 준비

Stream Analytics 작업을 정의하기 전에 작업에 대한 입력으로 구성된 데이터를 준비해야 합니다. 다음 단계를 실행하여 작업에 필요한 입력 데이터를 준비합니다. 

1. GitHub에서 [센서 샘플 데이터](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)를 다운로드합니다.  

2. [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount) cmdlet을 사용하여 LRS 복제로 표준 범용 저장소 계정을 만듭니다.  

3. 사용할 저장소 계정을 정의하는 저장소 계정 컨텍스트를 검색합니다. 저장소 계정으로 작업할 때 자격 증명을 반복적으로 제공하는 대신 컨텍스트를 참조합니다. 이 예제에서는 LRS(로컬 중복 저장소) 및 Blob 암호화(기본적으로 사용)를 사용하여 mystorageaccount라는 저장소 계정을 만듭니다.  

4. 다음으로 [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer)를 사용하여 컨테이너를 만들고 파일에 대한 공용 액세스를 허용하도록 'Blob'에 대한 권한을 설정하고 이전에 다운로드한 [센서 샘플 데이터](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)를 업로드합니다. 

이러한 단계는 다음과 같은 PowerShell 스크립트를 실행하여 수행할 수 있습니다.

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

[New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0) cmdlet을 사용하여 Stream Analytics 작업을 만듭니다. 이 cmdlet은 작업 이름, 리소스 그룹 이름 및 작업 정의를 매개 변수로 사용합니다. 작업 이름에는 작업을 식별할 수 있는 이름을 사용하고 Stream Analytics 작업 이름에는 영숫자, 하이픈 및 밑줄만 포함할 수 있고 길이는 3자에서 63자 사이여야 합니다. 작업 정의는 작업을 만드는 데 필요한 속성을 포함하는 JSON 파일입니다. 로컬 컴퓨터에서 "JobDefinition.json"이라는 파일을 만들고 다음과 같은 JSON 데이터를 추가합니다.

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

그런 다음, New-AzureRMStreamAnalyticsJob cmdlet을 실행하고 jobDefinitionFile 변수의 값을 작업 정의 JSON 파일을 저장한 경로로 바꿔야 합니다. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>작업에 대한 입력 구성

[New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0) cmdlet을 사용하여 작업에 대한 입력을 추가합니다. 이 cmdlet은 작업 이름, 작업 입력 이름, 리소스 그룹 이름 및 작업 입력 정의를 매개 변수로 사용합니다. 작업 입력 정의는 작업의 입력을 구성하는 데 필요한 속성을 포함하는 JSON 파일입니다. 이 예제에서는 Blob 저장소를 입력으로 생성합니다. 로컬 컴퓨터에서 "JobInputDefinition.json"이라는 파일을 만들고 다음과 같은 JSON 데이터를 추가한 다음, **accountKey**의 값을 $ storageAccountKey 값에 저장된 값인 저장소 계정의 액세스 키로 바꿔야 합니다. 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

그런 다음, New-AzureRMStreamAnalyticsInput cmdlet을 실행하고 jobDefinitionFile 변수의 값을 작업 입력 정의 JSON 파일을 저장한 경로로 바꿔야 합니다. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>작업에 대한 출력 구성

[New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0) cmdlet을 사용하여 작업에 대한 출력을 추가합니다. 이 cmdlet은 작업 이름, 작업 출력 이름, 리소스 그룹 이름 및 작업 출력 정의를 매개 변수로 사용합니다. 작업 출력 정의는 작업의 출력을 구성하는 데 필요한 속성을 포함하는 JSON 파일입니다. 이 예제에서는 Blob 저장소를 출력으로 생성합니다. 로컬 컴퓨터에서 "JobOutputDefinition.json"이라는 파일을 만들고 다음과 같은 JSON 데이터를 추가한 다음, **accountKey**의 값을 $ storageAccountKey 값에 저장된 값인 저장소 계정의 액세스 키로 바꿔야 합니다. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

그런 다음, New-AzureRMStreamAnalyticsOutput cmdlet을 실행하고 jobOutputDefinitionFile 변수의 값을 작업 출력 정의 JSON 파일을 저장한 경로로 바꿔야 합니다. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>변환 쿼리 정의

[New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0) cmdlet을 사용하여 작업에 변환을 추가합니다. 이 cmdlet은 작업 이름, 작업 변환 이름, 리소스 그룹 이름 및 작업 변환 정의를 매개 변수로 사용합니다. 로컬 컴퓨터에서 "JobTransformationDefinition.json"이라는 파일을 만들고 다음과 같은 JSON 데이터를 추가합니다. JSON 파일에는 변환 쿼리를 정의하는 쿼리 매개 변수가 포함됩니다.

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

그런 다음, New-AzureRMStreamAnalyticsTransformation cmdlet을 실행하고 jobTransformationDefinitionFile 변수의 값을 작업 변환 정의 JSON 파일을 저장한 경로로 바꿔야 합니다. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics 작업을 시작하고 출력을 확인합니다.

[Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) cmdlet을 사용하여 작업을 시작합니다. 이 cmdlet은 작업 이름, 리소스 그룹 이름, 출력 시작 모드 및 시작 시간을 매개 변수로 사용합니다. OUtpputStartMode는 JobStartTime, CustomTime 또는 LastOutputEventTime이라는 두 값을 받아들여 이러한 값들이 각각 무엇을 참조하는지 확인합니다. 자세한 내용은 PowerShell 설명서의 [매개 변수](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) 섹션을 참조하세요. 이 예제에서는 모드를 CustomTime으로 지정하고 OutputStartTime에 값을 제공할 수 있습니다. 

시간 값에는 Blob 저장소에 파일을 업로드하기 하루 전을 선택합니다. 파일이 업로드된 시간이 현재 시간보다 빠르기 때문입니다. 다음과 같은 cmdlet을 실행한 후 작업이 시작되면 "True"를 출력으로 반환합니다. “myoutputcontainer”라는 컨테이너가 변환된 데이터로 저장소 계정에 만들어집니다. 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, 스트리밍 작업 및 모든 관련 리소스가 더 이상 필요하지 않으면 삭제합니다. 작업을 삭제하면 작업에서 사용되는 스트리밍 단위에 대한 청구를 방지합니다. 작업을 나중에 사용하려는 경우 중지하고 나중에 필요할 때 다시 시작할 수 있습니다. 이 작업을 계속 사용하지 않으려면 다음과 같은 cmdlet을 실행하여 빠른 시작에서 만든 리소스를 모두 삭제합니다.

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 간단한 Stream Analytics 작업을 배포하여 다른 입력 원본을 구성하고 실시간 검색을 수행하는 방법을 알아보았습니다. 다음 문서로 이동하세요.

> [!div class="nextstepaction"]
> [Azure Stream Analytics를 사용하여 실시간 부정 행위 감지](stream-analytics-real-time-fraud-detection.md)
