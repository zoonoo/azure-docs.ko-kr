---
title: Data Factory를 사용하여 주문형 Hadoop 클러스터 만들기 - Azure HDInsight | Microsoft Docs
description: Azure Data Factory를 사용하여 HDInsight에서 주문형 Hadoop 클러스터를 만드는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: 6344b9a50f182a2b9ab05562c29099c9d6976f0b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Azure Data Factory를 사용하여 HDInsight에서 주문형 Hadoop 클러스터 만들기
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Azure Data Factory](../data-factory/introduction.md) 는 데이터의 이동과 변환을 조율하고 자동화하는 클라우드 기반의 데이터 통합 서비스입니다. 입력 데이터 조각을 처리하고 처리가 완료되면 클러스터를 삭제하기 위해 HDInsight Hadoop 클러스터를 적시에 만들 수 있습니다. 주문형 HDInsight Hadoop 클러스터를 사용할 때의 이점은 다음과 같습니다.

- HDInsight Hadoop 클러스터에서 실행 중인 작업(및 간략히 구성 가능한 유휴 시간)에만 시간을 할애합니다. HDInsight 클러스터에 대한 결제는 사용 여부에 관계없이 분당으로 비례 배분됩니다. Data Factory에서 주문형 HDInsight 연결된 서비스를 사용할 때 클러스터는 주문 시 생성됩니다. 그리고 작업이 완료되면 클러스터가 자동으로 삭제됩니다. 따라서 작업 실행 시간 및 잠깐의 유휴 시간(TTL 설정)에 대해서만 지불합니다.
- Data Factory 파이프라인을 사용하여 워크플로를 만들 수 있습니다. 예를 들어 온-프레미스 SQL Server에서 Azure Blob Storage로 데이터를 복사하는 파이프라인을 포함하고 주문형 HDInsight Hadoop 클러스터에서 Hive 스크립트 및 Pig 스크립트를 실행하여 데이터를 처리할 수 있습니다. 그런 다음 결과 데이터를 사용할 BI 응용 프로그램에 대한 Azure SQL Data Warehouse에 복사합니다.
- 주기적(매시간, 매일, 매주, 매월 등)으로 워크플로 실행을 예약할 수 있습니다.

Azure Data Factory에서 데이터 팩터리에는 하나 이상의 데이터 파이프라인이 포함될 수 있습니다. 데이터 파이프라인은 하나 이상의 활동을 포함합니다. 두 가지 유형의 활동이 있으며 [데이터 이동 활동](../data-factory/copy-activity-overview.md) 및 [데이터 변환 활동](../data-factory/transform-data.md)입니다. 데이터 이동 활동(현재는, 복사 작업만)을 사용하여 원본 데이터 저장소에서 대상 데이터 저장소로 데이터를 이동합니다. 데이터 변환 활동을 사용하여 데이터를 변환/처리합니다. HDInsight Hive 작업은 Data Factory에서 지원하는 데이터 변환 활동 중 하나입니다. 이 자습서에서는 Hive 변환 작업을 사용합니다.

고유한 HDInsight Hadoop 클러스터 또는 주문형 HDInsight Hadoop 클러스터를 사용하도록 hive 작업을 구성할 수 있습니다. 이 자습서에서 데이터 팩터리 파이프라인의 Hive 작업은 주문형 HDInsight 클러스터를 사용하도록 구성됩니다. 따라서 데이터 조각을 처리하기 위해 작업을 실행하면 과정은 다음과 같습니다.

1. 조각을 처리하기 위해 HDInsight Hadoop 클러스터가 자동으로 적시에 생성됩니다.  
2. 입력 데이터는 클러스터에서 HiveQL 스크립트를 실행하여 처리됩니다.
3. 처리가 완료된 후 HDInsight Hadoop 클러스터가 삭제되고 클러스터는 구성된 시간(timeToLive 설정) 동안 유휴 상태입니다 이 timeToLive 유휴 시간에 처리를 위해 다음 데이터 조각이 제공되면 조각을 처리하는 데 동일한 클러스터가 사용됩니다.  

이 자습서에서 hive 작업과 관련된 HiveQL 스크립트는 다음 작업을 수행합니다.

1. Azure Blob Storage에 저장된 원시 웹 로그 데이터를 참조하는 외부 테이블을 만듭니다.
2. 원시 데이터를 연도별, 월별로 분할합니다.
3. 분할된 데이터를 Azure Blob Storage에 저장합니다.

이 자습서에서 hive 작업과 관련된 HiveQL 스크립트는 Azure Blob Storage에 저장된 원시 웹 로그 데이터를 참조하는 외부 테이블을 만듭니다. 다음은 입력 파일의 각 월에 대한 샘플 행입니다.

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

HiveQL 스크립트는 원시 데이터를 연도별, 월별로 분할합니다. 이전 입력을 기반으로 세 개의 출력 폴더를 만듭니다. 각 폴더에는 각 월의 항목과 함께 파일이 포함되어 있습니다.

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

Hive 작업 외에도 데이터 팩터리의 데이터 변환 활동 목록은 [Azure Data Factory를 사용한 분석 및 변환](../data-factory/transform-data.md)을 참조하세요.

> [!NOTE]
> 현재, Azure Data Factory에서 HDInsight 클러스터 버전 3.2 만들기만 할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 문서의 지침을 시작하기 전에 다음 항목이 있어야 합니다.

* [Azure 구독](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>저장소 계정 준비
이 시나리오에서는 최대 3개의 저장소 계정을 사용할 수 있습니다.

- HDInsight 클러스터에 대한 기본 저장소 계정
- 입력 데이터에 대한 저장소 계정
- 출력 데이터에 대한 저장소 계정

자습서를 간단하게 만들기 위해 1개의 저장소 계정을 3가지 용도로 사용합니다. 이 섹션에 있는 Azure PowerShell 샘플 스크립트는 다음 작업을 수행합니다.

1. Azure에 로그인합니다.
2. Azure 리소스 그룹 만들기
3. Azure Storage 계정 만들기
4. 저장소 계정에 Blob 컨테이너 만들기
5. Blob 컨테이너에 다음 두 파일 복사

   * 입력 데이터 파일: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * HiveQL 스크립트: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     두 파일은 공용 Blob 컨테이너에 저장되어 있습니다.


**저장소를 준비하고 Azure PowerShell을 사용하여 파일을 복사하려면**
> [!IMPORTANT]
> 스크립트로 생성될 Azure 리소스 그룹 및 Azure Storage 계정 이름을 지정합니다.
> 스크립트에 출력된 **리소스 그룹 이름**, **저장소 계정 이름** 및 **저장소 계정 키**를 적어 둡니다. 다음 섹션에 필요합니다.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

PowerShell 스크립트에 대해 도움이 필요한 경우 [Azure Storage에서 Azure PowerShell 사용](../storage/common/storage-powershell-guide-full.md)을 참조하세요. 대신 Azure CLI를 사용하려는 경우 Azure CLI 스크립트에 대한 [부록](#appendix) 섹션을 참조하세요.

**저장소 계정 및 그 내용을 검사하려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **리소스 그룹** 을 클릭합니다.
3. PowerShell 스크립트에서 만든 리소스 그룹 이름을 두 번 클릭합니다. 나열된 리소스 그룹이 너무 많은 경우 필터를 사용합니다.
4. **리소스** 타일에서 리소스 그룹을 다른 프로젝트와 공유하지 않는 한 하나의 리소스가 나열됩니다. 그 리소스는 이전에 지정한 이름의 저장소 계정입니다. 저장소 계정 이름을 클릭합니다.
5. **Blob** 타일을 클릭합니다.
6. **adfgetstarted** 컨테이너를 클릭합니다. **inputdata** 및 **script**라는 두 폴더가 표시됩니다.
7. 폴더를 열고 폴더에서 파일을 확인합니다. inputdata에는 입력 데이터가 있는 input.log 파일이 포함되며 스크립트 폴더에는 HiveQL 스크립트 파일이 포함됩니다.

## <a name="create-a-data-factory-using-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 데이터 팩터리 만들기
저장소 계정, 입력 데이터 및 HiveQL 스크립트가 준비되었으면 Azure Data Factory를 만들 준비가 된 것입니다. 데이터 팩터리는 여러 가지 방법으로 만들 수 있습니다. 이 자습서에서는 Azure Portal을 사용하여 Azure Resource Manager 템플릿을 배포하여 데이터 팩터리를 만듭니다. 또한 [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 및 [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template)을 사용하여 Resource Manager 템플릿을 배포할 수도 있습니다. 기타 데이터 팩터리 만들기 방법은 [자습서: 첫 번째 데이터 팩터리 빌드](../data-factory/quickstart-create-data-factory-dot-net.md)를 참조하세요.

1. Azure에 로그인하여 Azure Portal에서 Azure Resource Manager 템플릿을 열려면 다음 이미지를 클릭합니다. 템플릿은 https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json에 위치합니다. 템플릿에 정의된 엔터티에 대한 자세한 내용은 [템플릿의 Data Factory 엔터티](#data-factory-entities-in-the-template) 섹션을 참조하세요. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. **리소스 그룹** 설정에 대해 **Use existing**(기존 항목 사용) 옵션을 선택하고 이전 단계에서 만든 리소스 그룹의 이름을 선택합니다(PowerShell 스크립트 사용).
3. 데이터 팩터리의 이름(**Data Factory Name**)을 입력합니다. 이 이름은 전역적으로 고유해야 합니다.
4. 이전 단계에서 기록해 둔 **저장소 계정 이름** 및 **저장소 계정 키**를 입력합니다.
5. **사용 약관**을 읽은 다음 위에 명시된 **사용 약관에 동의함**을 선택합니다.
6. **대시보드에 고정** 옵션을 선택합니다.
6. **구매/만들기**를 클릭합니다. 대시보드에 **템플릿 배포 중**이라는 타일이 표시됩니다. 리소스 그룹에 대한 **리소스 그룹** 블레이드가 열릴 때까지 기다립니다. 리소스 그룹 이름이 제목으로 표시된 타일을 클릭하여 리소스 그룹 블레이드를 열 수도 있습니다.
6. 리소스 그룹 블레이드가 아직 열리지 않은 경우 타일을 클릭하여 리소스 그룹을 엽니다. 이제 저장소 계정 리소스 외에 하나 이상의 데이터 팩터리 리소스가 나열됩니다.
7. 데이터 팩터리의 이름을 클릭합니다(**Data Factory Name** 매개 변수에 대해 지정한 값).
8. Data Factory 블레이드에서 **다이어그램** 타일을 클릭합니다. 다이어그램은 입력 데이터 집합 및 출력 데이터 집합이 있는 하나의 작업을 보여 줍니다.

    ![Azure Data Factory HDInsight 주문형 hive 작업 파이프라인 다이어그램](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    이름은 Resource Manager 템플릿에 정의됩니다.
9. **AzureBlobOutput**을 두 번 클릭합니다.
10. **최근 업데이트된 조각**에 하나의 조각이 표시됩니다. 상태가 **진행 중**이면 **준비**로 변경될 때까지 기다립니다. HDInsight 클러스터를 만들려면 보통 약 **20분** 정도 걸립니다.

### <a name="check-the-data-factory-output"></a>데이터 팩터리 출력 확인

1. 마지막 세션에서와 동일한 절차를 사용하여 Adfgetstarted 컨테이너의 컨테이너들을 확인합니다. **adfgetsarted**외에도 두 개의 새 컨테이너가 있습니다.

   * `adf<yourdatafactoryname>-linkedservicename-datetimestamp` 패턴을 따르는 이름을 가진 컨테이너. 이 컨테이너는 HDInsight 클러스터의 기본 컨테이너입니다.
   * adfjobs: 이 컨테이너는 ADF 작업 로그에 대한 컨테이너입니다.

     데이터 팩터리 출력은 Resource Manager 템플릿에 구성된 대로 **afgetstarted**에 저장됩니다.
2. **adfgetstarted**를 클릭합니다.
3. **partitioneddata**를 두 번 클릭합니다. 모든 웹 로그가 2014년에 지정되었으므로 **year=2014** 폴더가 표시됩니다.

    ![Azure Data Factory HDInsight 주문형 hive 작업 파이프라인 출력](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    목록을 드릴다운하면 1월, 2월 및 3월에 대한 3개 폴더가 표시됩니다. 각 월에 대한 로그가 있습니다.

    ![Azure Data Factory HDInsight 주문형 hive 작업 파이프라인 출력](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>템플릿의 데이터 팩터리 엔터티
데이터 팩터리를 위한 최상위 Resource Manager 템플릿은 다음과 같습니다.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>데이터 팩터리 정의
다음 예제에서처럼 Resource Manager 템플릿에서 데이터 팩터리를 정의합니다.  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
dataFactoryName은 템플릿을 배포할 때 지정하는 데이터 팩터리의 이름입니다. 데이터 팩터리는 현재 미국 동부, 미국 서부 및 북유럽 하위 지역에서만 지원됩니다.

### <a name="defining-entities-within-the-data-factory"></a>데이터 팩터리 내에서 엔터티 정의
다음 데이터 팩터리 엔터티는 JSON 템플릿에 정의됩니다.

* [Azure Storage 연결된 서비스](#azure-storage-linked-service)
* [HDInsight 주문형 연결된 서비스](#hdinsight-on-demand-linked-service)
* [Azure Blob 입력 데이터 집합](#azure-blob-input-dataset)
* [Azure Blob 출력 데이터 집합:](#azure-blob-output-dataset)
* [복사 작업을 포함하는 데이터 파이프라인](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스
Azure Storage 연결된 서비스는 Azure Storage 계정을 데이터 팩터리에 연결합니다. 이 자습서에서는 기본 HDInsight 저장소 계정, 입력 데이터 저장소 및 출력 데이터 저장소로 동일한 저장소 계정이 사용됩니다. 따라서 Azure Storage 연결된 서비스 하나만 정의합니다. 연결된 서비스 정의에서 Azure Storage 계정의 이름 및 키를 지정합니다. Azure Storage 연결된 서비스를 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure Storage 연결된 서비스](../data-factory/connector-azure-blob-storage.md)를 참조하세요.

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
**connectionString**은 storageAccountName 및 storageAccountKey 매개 변수를 사용합니다. 템플릿을 배포하는 동안 다음 매개 변수 값을 지정합니다.  

#### <a name="hdinsight-on-demand-linked-service"></a>HDInsight 주문형 연결된 서비스
주문형 HDInsight 연결된 서비스 정의에서 Data Factory 서비스에 사용된 구성 매개 변수에 대한 값을 지정하여 런타임에 HDInsight Hadoop 클러스터를 만듭니다. HDInsight 주문형 연결된 서비스를 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Compute 연결된 서비스](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) 문서를 참조하세요.  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
다음 사항에 유의하세요.

* Data Factory는 사용자 대신 **Linux 기반** HDInsight 클러스터를 만듭니다.
* HDInsight Hadoop 클러스터는 저장소 계정과 동일한 지역에 생성됩니다.
* *timeToLive* 설정을 확인합니다. 데이터 팩터리는 클러스터가 30분 동안 유휴 상태가 지속되면 클러스터를 자동으로 삭제합니다.
* HDInsight 클러스터는 JSON(**linkedServiceName**)에서 지정한 Blob Storage에 **기본 컨테이너**를 만듭니다. HDInsight는 클러스터가 삭제될 때 이 컨테이너를 삭제하지 않습니다. 이 동작은 의도된 것입니다. 주문형 HDInsight 연결된 서비스에서는 기존 라이브 클러스터(**timeToLive**)가 없는 한 슬라이스를 처리해야 할 때마다 HDInsight 클러스터가 만들어지며 처리가 완료되면 삭제됩니다.

자세한 내용은 [주문형 HDInsight 연결된 서비스](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) 를 참조하세요.

> [!IMPORTANT]
> 많은 조각이 처리될수록 Azure Blob 저장소에 컨테이너가 많아집니다. 작업의 문제 해결을 위해 이 항목들이 필요하지 않다면 저장소 비용을 줄이기 위해 삭제할 수 있습니다. 이 컨테이너의 이름은 "adf**yourdatafactoryname**-**linkedservicename**-datetimestamp" 패턴을 따릅니다. [Microsoft 저장소 탐색기](http://storageexplorer.com/) 같은 도구를 사용하여 Azure Blob 저장소에서 컨테이너를 삭제합니다.

#### <a name="azure-blob-input-dataset"></a>Azure Blob 입력 데이터 집합
입력 데이터 집합 정의에서 입력 데이터를 포함하는 Blob 컨테이너, 폴더 및 파일의 이름을 지정합니다. Azure Blob 데이터 집합을 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure Blob 데이터 집합 속성](../data-factory/connector-azure-blob-storage.md)을 참조하세요.

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}

```

JSON 정의에서 다음과 같은 특정 설정을 확인합니다.

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Azure Blob 출력 데이터 집합
출력 데이터 집합 정의에서 출력 데이터를 포함하는 Blob 컨테이너 및 폴더의 이름을 지정합니다. Azure Blob 데이터 집합을 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure Blob 데이터 집합 속성](../data-factory/connector-azure-blob-storage.md)을 참조하세요.  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

folderPath는 출력 데이터를 포함하는 폴더에 대한 경로를 지정합니다.

```json
"folderPath": "adfgetstarted/partitioneddata",
```

[데이터 집합 가용성](../data-factory/concepts-datasets-linked-services.md) 설정은 다음과 같습니다.

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

Azure Data Factory에서 출력 데이터 집합 가용성이 파이프라인을 유도합니다. 이 예에서, 월의 마지막 날(EndOfInterval)에 조각이 매달 생성됩니다. 

#### <a name="data-pipeline"></a>데이터 파이프라인
주문형 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하여 데이터를 변환하는 파이프라인을 정의합니다. 이 예에서 파이프라인을 정의하는 데 사용된 JSON 요소에 대한 자세한 설명은 [파이프라인 JSON](../data-factory/concepts-pipelines-activities.md)을 참조하세요.

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

파이프라인에는 HDInsightHive 작업 한 가지가 포함됩니다. 시작 및 종료 날짜 모두 2016년 1월에 있으므로 한 달에 대한 데이터(조각)만 처리됩니다. 작업에 대한 *start* 및 *end*가 모두 과거 날짜이므로 Data Factory는 해당 월에 대한 데이터를 즉시 처리합니다. end가 미래 날짜이면 데이터 팩터리는 시간이 되면 다른 조각을 만듭니다. 자세한 내용은 [데이터 팩터리 예약 및 실행](../data-factory/v1/data-factory-scheduling-and-execution.md)을 참조하세요.

## <a name="clean-up-the-tutorial"></a>자습서 정리

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>주문형 HDInsight 클러스터에 생성된 Blob 컨테이너 삭제
주문형 HDInsight 연결된 서비스에서는 기존 라이브 클러스터(timeToLive)가 없는 한 조각을 처리해야 할 때마다 HDInsight 클러스터가 만들어지며 처리가 완료되면 클러스터가 삭제됩니다. 각 클러스터에 대해 Azure Data Factory는 클러스터에 대한 기본 저장소 계정으로 사용되는 Azure Blob Storage에 Blob 컨테이너를 만듭니다. HDInsight 클러스터가 삭제되어도 기본 Blob 저장소 컨테이너와 연결된 저장소 계정은 삭제되지 않습니다. 이 동작은 의도된 것입니다. 많은 조각이 처리될수록 Azure Blob Storage에 컨테이너가 많아집니다. 작업의 문제 해결을 위해 이 항목들이 필요하지 않다면 저장소 비용을 줄이기 위해 삭제할 수 있습니다. 이러한 컨테이너의 이름은 `adfyourdatafactoryname-linkedservicename-datetimestamp` 패턴을 따릅니다.

**adfjobs** 및 **adfyourdatafactoryname-linkedservicename-datetimestamp** 폴더를 삭제합니다. adfjobs 컨테이너는 Azure Data Factory의 작업 로그를 포함합니다.

### <a name="delete-the-resource-group"></a>리소스 그룹 삭제
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)는 솔루션을 그룹으로 배포, 관리 및 모니터링하는 데 사용됩니다.  리소스 그룹을 삭제하면 그룹 내의 모든 구성 요소가 삭제됩니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **리소스 그룹** 을 클릭합니다.
3. PowerShell 스크립트에서 만든 리소스 그룹 이름을 클릭합니다. 나열된 리소스 그룹이 너무 많은 경우 필터를 사용합니다. 새 블레이드에서 리소스 그룹을 엽니다.
4. **리소스** 타일에서 리소스 그룹을 다른 프로젝트와 공유하지 않는 한 기본 저장소 계정과 데이터 팩터리가 나열됩니다.
5. 블레이드 맨 위의 **삭제**를 클릭합니다. 이렇게 하면 저장소 계정 및 저장소 계정에 저장된 데이터도 삭제됩니다.
6. 삭제를 확인하려면 리소스 그룹 이름을 입력하고 **삭제**를 클릭합니다.

리소스 그룹을 삭제할 때 저장소 계정을 삭제하지 않으려는 경우 기본 저장소 계정에서 비즈니스 데이터를 구분하여 다음 아키텍처를 고려할 수 있습니다. 이 경우 비즈니스 데이터가 있는 저장소 계정에 대해 하나의 리소스 그룹을 포함하고 HDInsight 연결된 서비스용 기본 저장소 계정 및 데이터 팩터리에 대해 다른 리소스 그룹을 포함합니다. 두 번째 리소스 그룹을 삭제할 경우 비즈니스 데이터 저장소 계정에 영향을 주지 않습니다. 이렇게 하려면 다음을 수행합니다.

* 다음을 Resource Manager 템플릿에서 Microsoft.DataFactory/datafactories 리소스와 함께 최상위 수준 리소스 그룹에 추가합니다. 저장소 계정을 만듭니다.

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* 새 저장소 계정에 새 연결된 서비스 지점을 추가합니다.

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* 추가 dependsOn 및 additionalLinkedServiceNames로 HDInsight 주문형 연결된 서비스를 구성합니다.

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Data Factory를 사용하여 주문형 HDInsight 클러스터를 만들고 Hive 작업을 처리하는 방법을 알아보았습니다. 자세히 알아보려면 다음을 참조하세요.

* [Hadoop 자습서: HDInsight에서 Linux 기반 Hadoop 사용 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight 설명서](https://azure.microsoft.com/documentation/services/hdinsight/)
* [데이터 팩터리 설명서](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>부록

### <a name="azure-cli-script"></a>Azure CLI 스크립트
Azure PowerShell을 사용하는 대신 Azure CLI를 사용하여 자습서를 수행할 수 있습니다. Azure CLI를 사용하려면 다음 지침에 따라 먼저 Azure CLI를 설치합니다.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>Azure CLI를 사용하여 저장소 준비 및 파일 복사

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

컨테이너 이름은 *adfgetstarted*입니다. 그대로 유지합니다. 그렇지 않으면 Resource Manager 템플릿을 업데이트해야 합니다. 이 CLI 스크립트에 대해 도움이 필요한 경우 [Azure Storage에서 Azure CLI 사용](../storage/common/storage-azure-cli.md)을 참조하세요.
