---
title: Linux 클러스터에 대해 디스크 암호화 사용
description: 이 문서에서는 Azure Resource Manager 및 Azure Key Vault를 사용하여 Linux에서 Azure Service Fabric 클러스터 노드에 대해 디스크 암호화를 사용하도록 설정하는 방법에 대해 설명합니다.
ms.topic: article
ms.date: 03/22/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 826ca54718625fb236f64587b63080cf16aafae4
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110663570"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Linux에서 Azure Service Fabric 클러스터 노드에 대한 디스크 암호와 사용 
> [!div class="op_single_selector"]
> * [Linux용 디스크 암호화](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Windows용 디스크 암호화](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

이 자습서에서는 Linux의 Azure Service Fabric 클러스터 노드에서 디스크 암호화를 사용하도록 설정하는 방법에 대해 알아봅니다. 이러한 작업은 각 노드 유형 및 가상 머신 확장 집합에 대해 수행해야 합니다. 노드를 암호화하기 위해 가상 머신 확장 집합에서 Azure Disk Encryption 기능을 사용합니다.

이 가이드에서는 다음 항목을 다룹니다.

* Linux에서 Service Fabric 클러스터 가상 머신 확장 집합에 대해 디스크 암호화를 사용하도록 설정할 때 알아야 할 주요 개념.
* Linux의 Service Fabric 클러스터 노드에서 디스크 암호화를 사용하도록 설정하기 전에 따라야 하는 단계.
* Linux의 Service Fabric 클러스터 노드에서 디스크 암호화를 사용하도록 설정하기 위해 따라야 하는 단계.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

 **자체 등록**

가상 머신 확장 집합에 대한 디스크 암호화 미리 보기에는 자체 등록이 필요합니다. 다음 단계를 사용합니다.

1. 다음 명령 실행: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 상태가 등록됨이 될 때까지 10분 정도 기다립니다. 다음 명령을 실행하여 상태를 확인할 수 있습니다.
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. 확장 집합과 동일한 구독 및 지역에서 키 자격 증명 모음을 만듭니다. 그런 다음 PowerShell cmdlet을 사용하여 키 자격 증명 모음에 **EnabledForDiskEncryption** 액세스 정책을 선택합니다. 다음 명령을 통해 Azure Portal에서 Key Vault UI를 사용하여 정책을 설정할 수도 있습니다.
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 새 암호화 명령이 있는 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.

3. [Azure PowerShell의 Azure SDK](https://github.com/Azure/azure-powershell/releases) 릴리스 최신 버전을 설치합니다. 암호화를 사용하도록 설정하고([Set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)), 암호화 상태를 검색하고([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)), 확장 집합 인스턴스에서 암호화를 제거하는([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) 가상 머신 확장 집합 Azure Disk Encryption cmdlet은 다음과 같습니다.


| 명령 | 버전 |  원본  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 이상 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 이상 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>디스크 암호화에 지원되는 시나리오
* 가상 머신 확장 집합에 대한 암호화는 관리 디스크를 사용하여 만들어진 확장 집합에서만 지원됩니다. 네이티브(또는 비관리) 디스크 확장 집합에는 지원되지 않습니다.
* Linux에서 가상 머신 확장 집합의 OS 및 데이터 볼륨에는 암호화 및 암호화 사용 해제가 모두 지원됩니다. 
* 가상 머신 확장 집합에 대한 VM(가상 머신) 이미지로 다시 설치 및 업그레이드 작업은 현재 미리 보기에서 지원되지 않습니다.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>새 클러스터 만들기 및 디스크 암호화 사용

Azure Resource Manager 템플릿 및 자체 서명된 인증서를 사용하여 클러스터를 만들고 디스크 암호화를 사용하도록 설정하려면 다음 명령을 사용합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인  

다음 명령을 사용하여 로그인합니다.

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>이미 있는 사용자 지정 템플릿 사용 

사용자 지정 템플릿을 작성해야 하는 경우 [Azure Service Fabric 클러스터 생성 템플릿 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) 페이지의 템플릿 중 하나를 사용하는 것이 좋습니다. 

이미 사용자 지정 템플릿이 있는 경우 템플릿 및 매개 변수 파일에 있는 모든 세 개의 인증서 관련 매개 변수의 이름이 다음과 같은지 반드시 확인합니다. 또한 다음과 같이 값이 Null인지 확인합니다.

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

Linux의 가상 머신 확장 집합에는 데이터 디스크 암호화만 지원되므로 Resource Manager 템플릿을 사용하여 데이터 디스크를 추가해야 합니다. 다음과 같이 데이터 디스크 프로비저닝을 위한 템플릿을 업데이트합니다.

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

다음은 해당 CLI 명령입니다. 선언 문의 값을 적절한 값으로 변경합니다. CLI는 앞에 있는 PowerShell 명령이 지원하는 다른 모든 매개 변수를 지원합니다.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Linux 인스턴스에 데이터 디스크 탑재
가상 머신 확장 집합에서 암호화를 계속하기 전에 추가된 데이터 디스크가 올바르게 탑재되었는지 확인하세요. Linux 클러스터 VM에 로그인하고 **LSBLK** 명령을 실행합니다. 출력에는 **탑재 지점** 열에 추가된 데이터 디스크가 표시되어야 합니다.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Linux에서 Service Fabric 클러스터에 애플리케이션 배포
클러스터에 애플리케이션을 배포하려면 [빠른 시작: Service Fabric에 Linux 컨테이너 배포](service-fabric-quickstart-containers-linux.md)의 단계 및 지침을 따릅니다.


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>이전에 만든 가상 머신 확장 집합에 대해 디스크 암호화 사용
이전 단계에서 만든 가상 머신 확장 집합에 대해 디스크 암호화를 사용하도록 설정하려면 다음 명령을 실행합니다.
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Linux의 가상 머신 확장 집합에 대해 디스크 암호화가 사용하도록 설정되어 있는지 확인
전체 가상 머신 확장 집합의 상태 또는 확장 집합의 인스턴스를 가져오려면 다음 명령을 실행합니다.
또한 Linux 클러스터 VM에 로그인하고 **LSBLK** 명령을 실행할 수 있습니다. 출력에는 **탑재 지점** 열에 추가된 데이터 디스크가 표시되어야 하며 **유형** 열은 *Crypt* 를 읽어야 합니다.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Service Fabric 클러스터의 가상 머신 확장 집합에 대해 디스크 암호화를 사용하지 않도록 설정
다음 명령을 실행하여 가상 머신 확장 집합에 대한 디스크 암호화를 사용하지 않도록 설정합니다. 디스크 암호화를 사용하지 않도록 설정하면 인스턴스별로 적용되는 것이 아니라 전체 가상 머신 확장 집합에 적용됩니다.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>다음 단계
이제 안전한 클러스터가 있으며 Service Fabric 클러스터 노드 및 가상 머신 확장 집합에 대해 디스크 암호화를 사용하거나 사용하지 않도록 설정하는 방법을 알고 있습니다. Linux의 Service Fabric 클러스터 노드에 대한 유사한 지침은 [Windows용 디스크 암호화](service-fabric-enable-azure-disk-encryption-windows.md)를 참조하세요. 
