<properties
	pageTitle="Azure 배치 PowerShell Cmdlet 시작"
	description="Azure 배치 서비스를 관리하는 데 사용되는 Azure PowerShell cmdlet을 소개합니다."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="yidingz"/>

<tags
	ms.service="batch"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="big-compute"
	ms.date="04/15/2015"
	ms.author="danlep"/>

# Azure 배치 PowerShell Cmdlet 시작
이 문서에서는 배치 계정을 관리하고 배치 작업 항목, 작업 및 태스크에 대 한 정보를 얻는 데 사용할 수 있는 Azure PowerShell cmdlet에 대해 간략히 소개합니다.

자세한 cmdlet 구문을 보려면 ```get-help <Cmdlet_name>```을 입력하세요.


## 필수 조건

* **배치 미리 보기** -서비스에서 작업하려면 아직 등록하지 않은 경우 [배치 미리 보기](https://account.windowsazure.com/PreviewFeatures)에 등록합니다.
* **Azure PowerShell** - 필수 조건과 다운로드 및 설치 지침에 대해서는 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요. 배치 cmdlet은 버전 0.8.10 이후 버전에서 도입되었습니다.

## 배치 cmdlet 사용

Azure PowerShell을 시작하고 [Azure 구독에 연결](../powershell-install-configure.md#Connect)하려면 표준 절차를 사용합니다. 또한 다음을 수행할 수도 있습니다.

* **Azure 구독 선택** - 훨씬 많은 구독이 있는 경우 배치 미리 보기 기능을 추가했던 구독을 선택합니다.

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **AzureResourceManage 모드로 전환** - Azure 리소스 관리자 모듈에서 배치 cmdlet을 전달합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요. 이 모듈을 사용하려면 [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx) cmdlet을 실행합니다.

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## 배치 계정 및 키 관리

Azure PowerShell cmdlet을 사용하여 배치 계정 및 키를 만들고 관리할 수 있습니다.

### 배치 계정 만들기

**New-AzureBatchAccount**를 사용하여 지정된 리소스 그룹에서 새로운 배치 계정을 만듭니다. 리소스 그룹이 아직 없는 경우는 [New-AzureResourceGroup](https://msdn.microsoft.com/library/dn654594.aspx) cmdlet을 실행하여 하나 만듭니다. 이때 **위치** 매개 변수에 Azure 지역 중 하나를 지정합니다. ([Get-AzureLocation](https://msdn.microsoft.com/library/dn654582.aspx)을 실행하면 다른 Azure 리소스에 대해 사용 가능한 지역을 찾을 수 있습니다.) 예:

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

그런 다음, 리소스 그룹에 새 배치 계정을 만듭니다. <*account_name*>의 계정 이름과 배치 서비스를 사용할 수 있는 위치도 지정합니다. 계정을 만드는 데는 몇 분 정도 걸릴 수 있습니다. 예:

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]배치 계정 이름은 Azure에서 고유해야 하며, 3자에서 24자 사이의 문자를 포함하고 소문자와 숫자만 사용해야 합니다.

### 계정 액세스 키 가져오기
**Get-AzureBatchAccountKeys**는 Azure 배치 계정과 연결된 액세스 키를 표시합니다. 예를 들어, 사용자가 만든 계정의 기본 및 보조 키를 가져오려면 다음을 실행합니다.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### 새 액세스 키 생성
**New-AzureBatchAccountKey**는 Azure 배치 계정에 대해 기본 또는 보조 계정 키를 새로 생성합니다. 예를 들어, 배치 계정의 새 기본 키를 생성하려면 다음과 같이 입력합니다.

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]새 보조 키를 생성하려면 **KeyType** 매개 변수에 "Secondary"를 지정합니다. 기본 및 보조 키를 개별적으로 다시 생성해야 합니다.

### 배치 계정 삭제
**Remove-AzureBatchAccount**는 배치 계정을 삭제합니다. 예:

```
Remove-AzureBatchAccount -AccountName <account_name>
```

메시지가 나타나면 계정을 제거할 것인지 확인합니다. 계정을 제거하는 데는 시간이 걸릴 수 있습니다.

## 작업 항목, 작업 및 태스크에 대한 쿼리

**Get-AzureBatchWorkItem**, **Get-AzureBatchJob**, **Get-AzureBatchTask**, **Get-AzureBatchPool** 등의 cmdlet을 사용하여 배치 계정 아래에 만든 엔터티를 쿼리합니다.

이러한 cmdlet을 사용하려면 먼저 계정 이름과 키를 저장할 AzureBatchContext 개체를 만들어야 합니다.

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

**BatchContext** 매개 변수를 사용하여 배치 서비스와 상호 작용하는 cmdlet에 이 컨텍스트를 전달합니다.

> [AZURE.NOTE]기본적으로 계정의 기본 키는 인증에 사용되지만 BatchAccountContext 개체의 **KeyInUse** 속성을 변경하면 사용할 키를 명시적으로 선택할 수 있습니다.```$context.KeyInUse = "Secondary"```


### 데이터에 대한 쿼리

예제와 같이 **Get-AzureBatchWorkItem**을 사용하여 작업 항목을 찾습니다. 이 작업은 기본적으로 사용자 계정 아래의 모든 작업 항목을 쿼리합니다. 이때 *$context*에는 이미 BatchAccountContext 개체가 저장되어 있다고 가정합니다.

```
Get-AzureBatchWorkItem -BatchContext $context
```

다음과 같이, 풀 등의 다른 엔터티에서도 동일한 작업을 수행할 수 있습니다.

```
Get-AzureBatchPool -BatchContext $context
```
### OData 필터 사용

**Filter** 매개 변수를 사용하여 OData 필터를 제공하면 사용자와 관계가 있는 개체만 찾을 수 있습니다. 예를 들어, "myWork"로 시작하는 이름을 가진 모든 작업 항목을 찾을 수 있습니다.

```
$filter = "startswith(name,'myWork') and state eq 'active'" 
Get-AzureBatchWorkItem -Filter $filter -BatchContext $context
``` 

이 방법은 로컬 파이프라인에서 "Where-Object"를 사용하는 것만큼 유연하지는 않습니다. 그러나 쿼리가 배치 서비스에 직접 전송되므로 서버에서 모든 필터링이 수행되어 인터넷 대역폭이 절약됩니다.

### Name 매개 변수 사용

OData 필터의 대안으로 **Name** 매개 변수를 사용합니다. "myWorkItem"이라는 특정 작업 항목을 쿼리하려면 다음과 같이 수행합니다.

``` 
Get-AzureBatchWorkItem -Name "myWorkItem" -BatchContext $context 

```
 **Name** 매개 변수는 전체 이름 검색만 지원하며 와일드 카드 또는 OData 스타일 필터는 지원하지 않습니다.

### 파이프라인 사용

배치 cmdlet은 PowerShell 파이프라인을 활용하여 cmdlet 간에 데이터를 전송할 수 있습니다. 이 방식은 매개 변수를 지정하는 것과 동일한 효과를 갖지만 보다 쉽게 여러 엔터티를 나열할 수 있습니다. 예를 들어, 다음과 같이 사용자 계정 아래의 모든 작업을 찾을 수 있습니다.

```
Get-AzureBatchWorkItem -BatchContext $context | Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context 
```

### MaxCount 매개 변수 사용

기본적으로 각 cmdlet은 최대 1000개의 개체를 반환합니다. 이 제한에 도달하면 더 적은 수의 개체를 반환하도록 필터를 조정하거나 **MaxCount** 매개 변수를 사용하여 최대값을 명시적으로 설정합니다. 예:

```
Get-AzureBatchWorkItem -MaxCount 2500 -BatchContext $context 

```

상한값을 제거하려면 **MaxCount**를 0 이하로 설정합니다.

## 관련된 항목
* [배치 기술 개요](batch-technical-overview.md)
* [Azure PowerShell 다운로드](http://go.microsoft.com/p/?linkid=9811175)
* [Azure cmdlet 참조](https://msdn.microsoft.com/library/jj554330.aspx)

<!---HONumber=GIT-SubDir-->