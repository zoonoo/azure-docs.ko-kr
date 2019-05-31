---
title: Azure Service Fabric Windows 클러스터에 대 한 디스크 암호화를 사용 하도록 설정 | Microsoft Docs
description: 이 문서에서는 Azure Resource Manager와 Azure Key Vault를 사용하여 Azure에서 Service Fabric 클러스터 노드에 대해 디스크 암호화를 사용하도록 설정하는 방법에 대해 설명합니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: 2e9c41409c1f528947e3bef281e9a3c34da39e9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66119151"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Service Fabric Windows 클러스터 노드에 대한 디스크 암호화 사용 
> [!div class="op_single_selector"]
> * [Windows용 디스크 암호화](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Linux용 디스크 암호화](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

아래 단계에 따라 Service Fabric Windows 클러스터 노드에서 디스크 암호화를 사용하도록 설정합니다. 이러한 작업은 각 노드 유형/가상 머신 확장 집합에 대해 수행해야 합니다. 노드를 암호화하기 위해 가상 머신 확장 집합에서 Azure Disk Encryption 기능이 활용됩니다.

이 가이드에서는 다음 절차를 다룹니다.

* Service Fabric Windows 클러스터 가상 머신 확장 집합에서 디스크 암호화를 사용하도록 설정하기 위해 알고 있어야 하는 주요 개념
* Service Fabric Windows 클러스터 가상 머신 확장 집합에서 디스크 암호화를 사용하도록 설정하기 전에 수행해야 할 필수 조건 단계
* Service Fabric Windows 클러스터 가상 머신 확장 집합에서 디스크 암호화를 사용하도록 설정하기 위해 수행해야 할 단계

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건
* **자체 등록** - 가상 머신 확장 집합 디스크 암호화 미리 보기를 사용하려면 자체 등록이 필요합니다.
* 다음 단계를 실행하여 구독을 자체 등록할 수 있습니다. 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* 상태가 '등록됨'으로 될 때까지 10분 정도 기다립니다. 다음 명령을 실행하여 상태를 확인할 수 있습니다. 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault** - 확장 집합과 동일한 구독 및 지역에 KeyVault를 만들고, 해당 PS cmdlet을 사용하여 KeyVault에서 'EnabledForDiskEncryption' 액세스 정책을 설정합니다. Azure Portal에서 KeyVault UI를 사용하여 정책을 설정할 수도 있습니다. 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* 최신 버전 설치 [Azure CLI](/cli/azure/install-azure-cli) , 새 암호화 명령이 있습니다.
* [Azure PowerShell 릴리스에서 최신 버전의 Azure SDK](https://github.com/Azure/azure-powershell/releases)를 설치합니다. 암호화를 사용하도록 설정하고([Set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)), 암호화 상태를 검색하고([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)), 확장 집합 인스턴스에서 암호화를 제거하는([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) 가상 머신 확장 집합 ADE cmdlet은 다음과 같습니다.

| 명령 | Version |  원본  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 이상 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 이상 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>디스크 암호화에 지원되는 시나리오
* 가상 머신 확장 집합 암호화가 관리 디스크를 사용하여 만들어진 확장 집합에 지원되며, 네이티브(또는 비관리) 디스크 확장 집합에는 지원되지 않습니다.
* 가상 머신 확장 집합 암호화는 Windows 가상 머신 확장 집합에 대 한 OS 및 데이터 볼륨에 대 한 지원 됩니다. Windows 확장 집합의 OS 및 데이터 볼륨에 대해 암호화 사용 안 함이 지원됩니다.
* 가상 머신 확장 집합 VM 이미지로 다시 설치 하 고 현재 미리 보기로 업그레이드 작업이 지원 되지 않습니다.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>새 클러스터 만들기 및 디스크 암호화 사용

클러스터를 만들고 Azure Resource Manager 템플릿 및 자체 서명 된 인증서를 사용 하 여 디스크 암호화를 사용 하도록 설정 하려면 다음 명령을 사용 합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인 

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>이미 있는 사용자 지정 템플릿 사용 

필요에 맞게 사용자 지정 템플릿을 작성해야 하는 경우 [Azure Service Fabric 템플릿 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)에서 사용할 수 있는 템플릿 중 하나로 시작하는 것이 좋습니다. 아래 [클러스터 템플릿 사용자 지정][customize-your-cluster-template] 섹션의 설명 및 지침에 따릅니다.

사용자 지정 템플릿이 이미 있는 경우 템플릿 및 매개 변수 파일에 있는 모든 세 개의 인증서 관련 매개 변수 이름이 다음과 같이 지정되고 값이 다음과 같이 null인지 두 번 확인합니다.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```azurecli

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Windows Service Fabric 클러스터에 애플리케이션 배포
[클러스터에 애플리케이션을 배포](service-fabric-deploy-remove-applications.md)하기 위한 단계와 지침을 따릅니다.


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>위에서 만든 Service Fabric 클러스터 가상 머신 확장 집합에 대해 디스크 암호화를 사용하도록 설정
 
```powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Windows 가상 머신 확장 집합에 대해 디스크 암호화를 사용하도록 설정되어 있는지 확인합니다.
전체 가상 머신 확장 집합의 상태 또는 확장 집합의 모든 인스턴스 가져오기 아래 명령을 참조하세요.
사용자 수 확장 집합의 VM에 로그인 하 고 드라이브를 암호화 해야 하는 또한

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Service Fabric 클러스터 가상 머신 확장 집합에 대해 디스크 암호화를 사용하지 않도록 설정 
[디스크 암호화 사용 안 함]은 인스턴스별로 적용되는 것이 아니라 전체 가상 머신 확장 집합에 적용됩니다. 

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>다음 단계
이 시점에서는 Service Fabric 클러스터 가상 머신 확장 집합에 대해 디스크 암호화를 사용하거나 사용하지 않도록 설정하는 방법이 포함된 보안 클러스터가 있습니다. 다음으로, [Linux용 디스크 암호화](service-fabric-enable-azure-disk-encryption-linux.md)가 있습니다. 

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
