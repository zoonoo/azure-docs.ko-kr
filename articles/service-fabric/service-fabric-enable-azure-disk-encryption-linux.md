---
title: Azure Service Fabric Linux 클러스터에 대 한 디스크 암호화를 사용 하도록 설정 | Microsoft Docs
description: 이 문서에서는 Azure Resource Manager와 Azure Key Vault를 사용하여 Azure에서 Service Fabric 클러스터 확장 집합에 대해 디스크 암호화를 사용하도록 설정하는 방법에 대해 설명합니다.
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
ms.openlocfilehash: f580bf02b222f01a3d5aad1254f208791ea22b38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161780"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Service Fabric Linux 클러스터 노드에 대한 디스크 암호화 사용 
> [!div class="op_single_selector"]
> * [Linux용 디스크 암호화](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Windows용 디스크 암호화](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

아래 단계에 따라 Service Fabric Linux 클러스터 노드에서 디스크 암호화를 사용하도록 설정합니다. 이러한 작업은 각 노드 유형/가상 머신 확장 집합에 대해 수행해야 합니다. 노드를 암호화하기 위해 가상 머신 확장 집합에서 Azure Disk Encryption 기능이 활용됩니다.

이 가이드에서는 다음 절차를 다룹니다.

* Service Fabric Linux 클러스터 가상 머신 확장 집합에서 디스크 암호화를 사용하도록 설정하기 위해 알고 있어야 하는 주요 개념
* Service Fabric Linux 클러스터 가상 머신 확장 집합에서 디스크 암호화를 사용하도록 설정하기 전에 수행해야 할 필수 조건 단계
* Service Fabric Linux 클러스터 가상 머신 확장 집합에서 디스크 암호화를 사용하도록 설정하기 위해 수행해야 할 단계



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
* **Azure Key Vault** - 가상 머신 확장 집합과 동일한 구독 및 지역에 KeyVault를 만들고 PS cmdlet을 사용하여 KeyVault에서 'EnabledForDiskEncryption' 액세스 정책을 설정합니다. Azure Portal에서 KeyVault UI를 사용하여 정책을 설정할 수도 있습니다. 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* 최신 버전 설치 [Azure CLI](/cli/azure/install-azure-cli) , 새 암호화 명령이 있습니다.
* [Azure PowerShell 릴리스에서 최신 버전의 Azure SDK](https://github.com/Azure/azure-powershell/releases)를 설치합니다. 다음은 가상 머신 확장 집합을 ADE를 사용 하도록 설정 하는 cmdlet ([설정](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 암호화를 검색 ([가져오기](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 암호화 상태 및 제거 ([사용 하지 않도록 설정](/powershell/module/az.compute/disable-azvmssdiskencryption)) 확장에서 암호화 인스턴스를 설정 합니다. 

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
* Linux 가상 머신 확장 집합용 데이터 볼륨에 대한 가상 머신 확장 집합 암호화가 지원됩니다. Linux용 OS 디스크 암호화는 현재 미리 보기에서 지원되지 않습니다.
* 가상 머신 확장 집합 VM 이미지로 다시 설치 하 고 현재 미리 보기로 업그레이드 작업이 지원 되지 않습니다.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>새 Linux 클러스터 만들기 및 디스크 암호화 사용

클러스터를 만들고 Azure Resource Manager 템플릿 및 자체 서명 된 인증서를 사용 하 여 디스크 암호화를 사용 하도록 설정 하려면 다음 명령을 사용 합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인  

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>이미 있는 사용자 지정 템플릿 사용 

필요에 맞게 사용자 지정 템플릿을 작성해야 하는 경우 Linux 클러스터용 [Azure Service Fabric 템플릿 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)에서 사용할 수 있는 템플릿 중 하나로 시작하는 것이 좋습니다. 

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

Linux 가상 머신 확장 집합의 경우 데이터 디스크 암호화만 지원되므로 Azure Resource Manager 템플릿을 사용하여 데이터 디스크를 추가해야 합니다. 아래와 같이 데이터 디스크 프로비전에 대한 템플릿을 업데이트합니다.

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
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

동일한 작업을 수행하는 해당 CLI 명령은 다음과 같습니다. 선언 문의 값을 적절한 값으로 변경합니다. CLI는 위의 PowerShell 명령이 지원하는 다른 모든 매개 변수를 지원합니다.

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

#### <a name="linux-data-disk-mounting"></a>Linux 데이터 디스크 탑재
Linux 가상 머신 확장 집합에서 암호화를 계속 진행하기 전에 추가된 데이터 디스크가 올바르게 탑재되었는지 확인해야 합니다. Linux 클러스터 VM에 로그인 하 고 LSBLK 명령을 실행 합니다. 출력에는 탑재 지점 열에 추가된 데이터 디스크가 표시되어야 합니다.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Linux Service Fabric 클러스터에 애플리케이션 배포
[클러스터에 애플리케이션을 배포](service-fabric-quickstart-containers-linux.md)하기 위한 단계와 지침을 따릅니다.


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>위에서 만든 Linux Service Fabric 클러스터 가상 머신 확장 집합에 대해 디스크 암호화를 사용하도록 설정
 
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

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Linux 가상 머신 확장 집합에 대해 디스크 암호화를 사용하도록 설정되어 있는지 확인합니다.
전체 가상 머신 확장 집합의 상태 또는 확장 집합의 모든 인스턴스 VM 가져오기 아래 명령을 참조하세요.
또한 사용자는 Linux 클러스터 VM에 로그인 하 고 LSBLK 명령을 실행할 수 있습니다. 출력에는 탑재 지점 열에 추가된 데이터 디스크가 표시되고, 추가된 데이터 디스크에 대한 [유형] 열이 Crypt로 표시되어야 합니다.

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

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>다음 단계
이 시점에서는 Linux Service Fabric 클러스터 가상 머신 확장 집합에 대해 디스크 암호화를 사용하거나 사용하지 않도록 설정하는 방법이 포함된 보안 클러스터가 있습니다. 다음으로, [Windows용 디스크 암호화](service-fabric-enable-azure-disk-encryption-windows.md)가 있습니다. 
