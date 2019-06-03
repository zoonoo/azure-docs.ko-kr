---
title: Azure Service Fabric Linux 클러스터에 대 한 디스크 암호화를 사용 하도록 설정 | Microsoft Docs
description: 이 문서에서는 Azure Resource Manager 및 Azure Key Vault를 사용 하 여 Linux에서 Azure Service Fabric 클러스터 노드에 대 한 디스크 암호화를 사용 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258772"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Linux에서 Azure Service Fabric 클러스터 노드에 대 한 디스크 암호화를 사용 하도록 설정 
> [!div class="op_single_selector"]
> * [Linux용 디스크 암호화](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Windows용 디스크 암호화](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

이 자습서에서는 Linux에서 Azure Service Fabric 클러스터 노드에서 디스크 암호화를 사용 하는 방법에 알아봅니다. 각 노드 형식 및 가상 머신 확장 집합에 대해 다음이 단계를 수행 해야 합니다. 노드를 암호화 하는 것에 대 한 가상 머신 확장 집합에서 Azure Disk Encryption 기능을 사용 하겠습니다.

이 가이드는 다음 항목을 다룹니다.

* Linux에서 Service Fabric 클러스터 가상 머신 확장에서 디스크 암호화를 사용 하도록 설정 하면 설정 되는 경우 알아야 할 주요 개념입니다.
* Service Fabric에서 디스크 암호화를 사용 하도록 설정 하기 전에 다음 단계는 Linux에서 노드 클러스터입니다.
* Linux에서 Service Fabric 클러스터 노드에서 디스크 암호화를 사용 하도록 설정 하려면 다음 단계입니다.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

 **Self-registration**

가상 머신 확장 집합에 대 한 디스크 암호화 미리 보기에는 자체 등록 해야합니다. 다음 단계를 사용하세요.

1. 다음 명령 실행: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 상태가 될 때까지 10 분 정도 기다립니다 *Registered*합니다. 다음 명령을 실행 하 여 상태를 확인할 수 있습니다.
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. 확장 집합과 동일한 구독 및 지역에서 키 자격 증명 모음을 만듭니다. 다음을 선택 합니다 **EnabledForDiskEncryption** 해당 PowerShell cmdlet을 사용 하 여 key vault에 대 한 정책에 액세스 합니다. 또한 다음 명령 사용 하 여 Azure portal에서 키 자격 증명 모음 UI를 사용 하 여 정책을 설정할 수 있습니다.
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 최신 버전을 설치 합니다 [Azure CLI](/cli/azure/install-azure-cli), 새 암호화 명령이 있습니다.

3. 최신 버전을 설치 합니다 [Azure PowerShell에서 Azure SDK](https://github.com/Azure/azure-powershell/releases) 릴리스 합니다. 다음은 가상 머신 확장 집합을 사용 하도록 설정 하려면 Azure Disk Encryption cmdlet ([설정할](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 암호화를 검색 ([가져오기](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 암호화 상태 및 제거 ([사용 하지 않도록 설정](/powershell/module/az.compute/disable-azvmssdiskencryption)) 눈금에는 암호화 인스턴스를 설정합니다.


| 명령 | Version |  원본  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 이상 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 이상 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>디스크 암호화에 지원되는 시나리오
* 가상 머신 확장 집합에 대 한 암호화는 관리 디스크를 사용 하 여 만든 확장 집합에 대해서만 지원 됩니다. 네이티브(또는 비관리) 디스크 확장 집합에는 지원되지 않습니다.
* Linux에서 가상 머신 확장 집합에서 OS 및 데이터 볼륨 암호화 및 암호화 사용 안 함 모두 지원 됩니다. 
* 가상 머신 확장 집합에 대 한 VM (가상 머신) 이미지로 다시 설치 및 업그레이드 작업은 현재 미리 보기에서 지원 되지 않습니다.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>새 클러스터를 만들고 디스크 암호화를 사용 하도록 설정

클러스터 만들기 및 Azure Resource Manager 템플릿 및 자체 서명 된 인증서를 사용 하 여 디스크 암호화를 사용 하도록 설정 하려면 다음 명령을 사용 합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인  

다음 명령을 사용 하 여 로그인 합니다.

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>이미 있는 사용자 지정 템플릿 사용 

사용자 지정 템플릿을 작성 해야 하는 경우 좋습니다에서 템플릿 중 하나를 사용 하는 합니다 [Azure Service Fabric 클러스터 만들기 템플릿 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) 페이지입니다. 

사용자 지정 템플릿을 이미 있는 경우 모든 세 개의 인증서 관련 매개 변수 템플릿 및 매개 변수 파일을 다음과 같이 라고 다시 확인 합니다. 또한 다음과 같이 값이 null을 확인 합니다.

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

Linux에서 가상 머신 확장 집합에 대 한 지원 되므로 전용 데이터 디스크 암호화 Resource Manager 템플릿을 사용 하 여 데이터 디스크를 추가 해야 합니다. 데이터 디스크 프로 비전에 대 한 서식 파일에 다음과 같이 업데이트 합니다.

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

해당 CLI 명령은 다음과 같습니다. 적절 한 값으로 선언 문의 값을 변경 합니다. CLI는 모든 다른 매개 변수는 위의 PowerShell 명령이 지 원하는 지원 합니다.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Linux 인스턴스에 데이터 디스크를 탑재 합니다.
가상 머신 확장 집합에서 암호화를 사용 하 여 계속 하기 전에 추가 데이터 디스크 올바로 마운트 되었는지 확인 합니다. Linux 클러스터 VM에 로그인 하 고 실행 합니다 **LSBLK** 명령입니다. 출력에서 해당 추가 데이터 디스크를 표시 해야 합니다 **탑재 지점을** 열입니다.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Linux에서 Service Fabric 클러스터에 응용 프로그램 배포
단계 및 지침에 따라 클러스터에 응용 프로그램을 배포 하려면 [빠른 시작: Service Fabric Linux 컨테이너 배포](service-fabric-quickstart-containers-linux.md)합니다.


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>이전에 만든 가상 머신 확장 집합 용 디스크 암호화를 사용 하도록 설정
가상 머신 확장에 대 한 디스크 암호화를 사용 하도록 설정 하려면 다음 명령을 실행 하 고 이전 단계를 통해 만든를 설정 합니다.
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Linux에서 설정 하는 가상 머신 확장에 대 한 디스크 암호화를 사용 하면 유효성 검사
확장 집합의 모든 인스턴스는 전체 가상 머신 확장 집합의 상태를 가져오려면 다음 명령을 실행 합니다.
Linux 클러스터 VM에 로그인 하 고 실행할 수 또한 합니다 **LSBLK** 명령입니다. 출력에서 추가 데이터 디스크를 표시 해야 합니다 **탑재 지점** 열 및 **형식** 열 읽어야 *Crypt*합니다.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Service Fabric 클러스터에서 설정 하는 가상 머신 확장에 대 한 디스크 암호화를 사용 하지 않도록 설정
다음 명령을 실행 하 여 설정 하는 가상 머신 확장에 대 한 디스크 암호화를 사용 하지 않도록 설정 합니다. 전체 가상 머신 확장 집합을 개별 인스턴스가 아니라 디스크 암호화를 사용 하지 않도록 설정 적용 되도록 note 합니다.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>다음 단계
이 시점에서 보안 클러스터가 구축 하 고 사용 하도록 설정 하 고 Service Fabric 클러스터 노드 및 가상 머신 확장 집합 용 디스크 암호화를 사용 하지 않도록 설정 하는 방법을 알고 해야 합니다. Linux에서 Service Fabric 클러스터 노드에서 비슷한 지침은 [디스크 암호화에 대 한 Windows](service-fabric-enable-azure-disk-encryption-windows.md)합니다. 
