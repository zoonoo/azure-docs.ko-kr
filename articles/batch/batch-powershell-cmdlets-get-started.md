---
title: Azure 배치 PowerShell 시작 | Microsoft Docs
description: Azure 배치 서비스를 관리하는 데 사용되는 Azure PowerShell cmdlet에 대해 간략히 알아보세요.
services: batch
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''

ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 07/28/2016
ms.author: danlep

---
# Azure 배치 PowerShell Cmdlet 시작
Azure 배치 PowerShell cmdlet을 사용하여 배치 API, Azure 포털, Azure CLI\(명령줄 인터페이스\)에서 실행한 많은 동일한 작업을 수행하고 스크립트를 작성할 수 있습니다. 배치 계정을 관리하고 풀, 작업, 태스크 등의 배치 리소스 작업에 사용할 수 있는 cmdlet에 대해 간략히 소개합니다. 이 문서는 Azure PowerShell 버전 1.6.0의 cmdlet를 기반으로 합니다.

배치 cmdlet의 전체 목록과 상세 cmdlet 구문은 [Azure 배치 cmdlet 참조](https://msdn.microsoft.com/library/azure/mt125957.aspx)에서 확인하세요.

## 필수 조건
* **Azure PowerShell** - Azure PowerShell 다운로드 및 설치 지침에 대해서는 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.
  
  * Azure 배치 cmdlet은 Azure Resource Manager 모듈에 탑재되므로 **Login-AzureRmAccount** cmdlet을 실행하여 구독에 연결해야 합니다.
  * 서비스 업데이트 및 향상을 최대한 활용하기 위해서 Azure PowerShell을 자주 업데이트하는 것이 좋습니다.
* **배치 공급자 네임스페이스\(일회성 작업\)에 등록** - 배치 계정을 사용하기 전에 배치 공급자 네임스페이스에 등록해야 합니다. 이 작업은 구독당 한 번만 수행하면 됩니다. 다음 cmdlet을 실행합니다.
  
        Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch

## 배치 계정 및 키 관리
### 배치 계정 만들기
**New-AzureBatchAccount**는 지정된 리소스 그룹에서 새로운 배치 계정을 만듭니다. 아직 리소스 그룹이 없는 경우는 [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) cmdlet을 실행하고 **위치** 매개 변수에 "미국 중부"와 같은 Azure 지역 중 하나를 지정하여 하나를 만듭니다. 예:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"


그런 다음, 리소스 그룹에 새 배치 계정을 만들어, \<*account\_name*\>의 계정 이름과 리소스 그룹의 위치와 이름을 지정합니다. 배치 계정을 만드는 데 다소 시간이 걸릴 수 있습니다. 예:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup

> [!NOTE]
> 배치 계정 이름은 리소스 그룹에 대해 Azure 지역에 고유해야 하며, 3자에서 24자 사이의 문자를 포함하고, 소문자와 숫자만 사용해야 합니다.
> 
> 

### 계정 액세스 키 가져오기
**Get-AzureRmBatchAccountKey**는 Azure 배치 계정과 연결된 액세스 키를 표시합니다. 예를 들어, 사용자가 만든 계정의 기본 및 보조 키를 가져오려면 다음을 실행합니다.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <accountname>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey


### 새 액세스 키 생성
**New-AzureRmBatchAccountKey**는 Azure 배치 계정에 대해 기본 또는 보조 계정 키를 새로 생성합니다. 예를 들어, 배치 계정의 새 기본 키를 생성하려면 다음과 같이 입력합니다.

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary


> [!NOTE]
> 새 보조 키를 생성하려면 **KeyType** 매개 변수에 "Secondary"를 지정합니다. 기본 및 보조 키를 개별적으로 다시 생성해야 합니다.
> 
> 

### 배치 계정 삭제
**Remove-AzureRmBatchAccount**는 배치 계정을 삭제합니다. 예:

    Remove-AzureRmBatchAccount -AccountName <account_name>

메시지가 나타나면 계정을 제거할 것인지 확인합니다. 계정을 제거하는 데는 시간이 걸릴 수 있습니다.

## BatchAccountContext 개체 만들기
배치 풀, 작업, 태스크, 기타 리소스를 만들고 관리할 때 배치 PowerShell cmdlet을 사용하여 인증하려면, 우선 계정 이름과 키를 저장할 BatchAccountContext 개체를 만듭니다.

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

BatchAccountContext 개체를 **BatchContext** 매개 변수를 사용하는 cmdlet에 전달합니다.

> [!NOTE]
> 기본적으로 계정의 기본 키는 인증에 사용되지만 BatchAccountContext 개체의 **KeyInUse** 속성을 변경하면 사용할 키를 명시적으로 선택할 수 있습니다.`$context.KeyInUse = "Secondary"`
> 
> 

## 배치 리소스 만들기 및 수정
**New-AzureBatchPool**, **New-AzureBatchJob** 및 **New-AzureBatchTask** 등의 cmdlet을 사용하여 배치 계정 아래 리소스를 만듭니다. 기존 리소스 속성을 업데이트하는 해당 **Get-** 및 **Set-** cmdlet과, 배치 계정에서 리소스를 제거하는 **Remove-** cmdlet이 있습니다.

이러한 많은 cmdlet을 사용하는 경우 BatchContext 개체를 전달 하는 것 외에도, 다음 예제와 같이 상세한 리소스 설정을 포함하는 개체를 만들거나 전달해야 합니다. 추가 예제를 보려면 각 cmdlet에 대한 자세한 도움말을 참조하세요.

### 배치 풀 만들기
배치 풀을 만들거나 업데이트할 때 계산 노드의 운영 체제에 대해 클라우드 서비스 구성 또는 가상 컴퓨터 구성을 선택합니다\([배치 기능 개요](batch-api-basics.md#pool) 참조\). 계산 노드의 이미지를 [Azure 게스트 OS 릴리스](../cloud-services/cloud-services-guestos-update-matrix.md#releases) 중 하나 또는 Azure 마켓플레이스에서 지원되는 Linux 또는 Windows VM 이미지 중 하나를 통해 만들지 여부를 선택합니다.

**New-AzureBatchPool**을 실행하는 경우, PSCloudServiceConfiguration 또는 PSVirtualMachineConfiguration 개체의 운영 체제 설정을 전달합니다. 예를 들어, 다음 cmdlet는 제품군 3\(Windows Server 2012\)의 최신 운영 체제 버전을 통해 이미지를 만든 클라우드 서비스 구성에서 소규모 계산 노드로 새 배치 풀을 만듭니다. 여기서 **CloudServiceConfiguration** 매개 변수는 *$configuration* 변수를 PSCloudServiceConfiguration 개체로 지정합니다. **BatchContext** 매개 변수는 이전에 정의한 *$context* 변수를 BatchAccountContext 개체로 지정합니다.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(3,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

새 풀의 계산 노드 대상 수는 자동 크기 조정 수식에 의해 결정됩니다. 이 경우 공식은 단순히 **$TargetDedicated=4**이며 풀의 계산 노드 수는 최대 4개입니다.

## 풀, 작업, 태스크 및 기타 상세 정보 쿼리
**Get-AzureBatchPool** , **Get-AzureBatchJob** 및 **Get-AzureBatchTask** 등의 cmdlet을 사용하여 배치 계정 아래에 만든 엔터티를 쿼리합니다.

### 데이터에 대한 쿼리
예제와 같이 **Get AzureBatchPools**을 사용하여 풀을 찾습니다. 이 작업은 기본적으로 사용자 계정 아래의 모든 풀을 쿼리합니다. 이때 *$context*에는 이미 BatchAccountContext 개체가 저장되어 있다고 가정합니다.

    Get-AzureBatchPool -BatchContext $context

### OData 필터 사용
**Filter** 매개 변수를 사용하여 OData 필터를 제공하면 사용자와 관계가 있는 개체만 찾을 수 있습니다. 예를 들어, "myPool"로 시작하는 ID를 갖는 모든 풀을 찾을 수 있습니다.

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context


이 방법은 로컬 파이프라인에서 "Where-Object"를 사용하는 것만큼 유연하지는 않습니다. 그러나 쿼리가 배치 서비스에 직접 전송되므로 서버에서 모든 필터링이 수행되어 인터넷 대역폭이 절약됩니다.

### ID 매개 변수 사용
OData 필터의 대안은 **ID** 매개 변수를 사용하는 것입니다. ID가 "myPool"인 특정 풀을 쿼리하려면

    Get-AzureBatchPool -Id "myPool" -BatchContext $context


**ID** 매개 변수는 전체 ID 검색만 지원하며 와일드카드 또는 OData 스타일 필터를 지원하지 않습니다.

### MaxCount 매개 변수 사용
기본적으로 각 cmdlet은 최대 1000개의 개체를 반환합니다. 이 제한에 도달하면 더 적은 수의 개체를 반환하도록 필터를 조정하거나 **MaxCount** 매개 변수를 사용하여 최대값을 명시적으로 설정합니다. 예:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

상한값을 제거하려면 **MaxCount**를 0 이하로 설정합니다.

### 파이프라인 사용
배치 cmdlet은 PowerShell 파이프라인을 활용하여 cmdlet 간에 데이터를 전송할 수 있습니다. 이 방식은 매개 변수를 지정하는 것과 동일한 효과를 갖지만 보다 쉽게 여러 엔터티를 나열할 수 있습니다. 예를 들어 다음은 사용자 계정의 모든 작업을 검색합니다.

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context


## 다음 단계
* 자세한 cmdlet 구문 및 예제는 [Azure 배치 cmdlet 참조](https://msdn.microsoft.com/library/azure/mt125957.aspx)를 참조하세요.
* 항목의 수 및 쿼리에 대해 배치에 반환되는 정보의 유형을 줄이는 데 대한 자세한 내용은 [효율적인 배치 서비스 쿼리](batch-efficient-list-queries.md)을 참조하세요.

<!---HONumber=AcomDC_0803_2016-->