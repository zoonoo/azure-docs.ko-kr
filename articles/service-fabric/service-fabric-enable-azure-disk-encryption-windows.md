---
title: Windows 클러스터에 대해 디스크 암호화 사용
description: 이 문서에서는 Azure Resource Manager의 Azure Key Vault를 사용 하 여 Azure Service Fabric 클러스터 노드에 대해 디스크 암호화를 사용 하도록 설정 하는 방법을 설명 합니다.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: 24405c3c34bfd7b88932101c42a8d0fc96c90fa1
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614454"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Windows에서 Azure Service Fabric 클러스터 노드에 대해 디스크 암호화 사용 
> [!div class="op_single_selector"]
> * [Windows용 디스크 암호화](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Linux용 디스크 암호화](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

이 자습서에서는 Windows의 Service Fabric 클러스터 노드에서 디스크 암호화를 사용 하도록 설정 하는 방법에 대해 알아봅니다. 각 노드 유형 및 가상 머신 확장 집합에 대해 이러한 단계를 수행 해야 합니다. 노드를 암호화 하는 데는 가상 머신 확장 집합에 대 한 Azure Disk Encryption 기능을 사용 합니다.

이 가이드에서는 다음 항목을 다룹니다.

* Windows에서 Service Fabric 클러스터 가상 머신 확장 집합에 대 한 디스크 암호화를 사용 하도록 설정할 때 알아야 할 주요 개념입니다.
* Windows의 Service Fabric 클러스터 노드에서 디스크 암호화를 사용 하도록 설정 하기 전에 따라야 하는 단계입니다.
* Windows의 Service Fabric 클러스터 노드에서 디스크 암호화를 사용 하도록 설정 하는 단계를 수행 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

**자체 등록** 

가상 머신 확장 집합에 대 한 디스크 암호화 미리 보기에는 자체 등록이 필요 합니다. 다음 단계를 사용합니다. 

1. 먼저, 다음 명령을 실행 합니다.
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 상태가 *등록*됨이 될 때까지 10 분 정도 기다립니다. 다음 명령을 실행 하 여 상태를 확인할 수 있습니다. 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. 확장 집합과 동일한 구독 및 지역에 키 자격 증명 모음을 만든 다음 해당 PowerShell cmdlet을 사용 하 여 주요 자격 증명 모음에서 **EnabledForDiskEncryption** 액세스 정책을 선택 합니다. 다음 명령을 사용 하 여 Azure Portal에서 Key Vault UI를 사용 하 여 정책을 설정할 수도 있습니다.
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 새 암호화 명령이 있는 [Azure CLI](/cli/azure/install-azure-cli)의 최신 버전을 설치 합니다.
3. Azure PowerShell 릴리스에서 최신 버전의 [AZURE SDK](https://github.com/Azure/azure-powershell/releases) 를 설치 합니다. 다음은 확장 집합 인스턴스에서 암호화를 사용 하도록 설정 하 고 ([설정](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 암호화 상태를 검색 ([get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 하 고 암호화를 제거 ([해제](/powershell/module/az.compute/disable-azvmssdiskencryption)) 하는 가상 머신 확장 집합 Azure Disk Encryption cmdlet입니다.

| 명령 | 버전 |  원본  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 이상 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 이상 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 이상 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>디스크 암호화에 지원되는 시나리오
* 가상 머신 확장 집합에 대 한 암호화는 관리 디스크를 사용 하 여 만든 확장 집합에 대해서만 지원 됩니다. 네이티브(또는 비관리) 디스크 확장 집합에는 지원되지 않습니다.
* 암호화는 Windows의 가상 머신 확장 집합의 OS 및 데이터 볼륨에 대해 지원 됩니다. 암호화 사용 안 함은 Windows의 가상 머신 확장 집합에 대 한 OS 및 데이터 볼륨에 대해서도 지원 됩니다.
* 가상 머신 확장 집합에 대 한 가상 머신 이미지로 다시 설치 및 업그레이드 작업은 현재 미리 보기에서 지원 되지 않습니다.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>새 클러스터 만들기 및 디스크 암호화 사용

Azure Resource Manager 템플릿 및 자체 서명된 인증서를 사용하여 클러스터를 만들고 디스크 암호화를 사용하도록 설정하려면 다음 명령을 사용합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인 
다음 명령을 사용 하 여 로그인 합니다.
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>이미 있는 사용자 지정 템플릿 사용 

사용자의 요구 사항에 맞게 사용자 지정 템플릿을 작성 해야 하는 경우 [Azure Service Fabric 클러스터 생성 템플릿 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) 페이지에서 사용할 수 있는 템플릿 중 하나를 사용 하 여 시작 하는 것이 좋습니다. [클러스터 템플릿 섹션을 사용자 지정][customize-your-cluster-template] 하려면 다음 지침을 참조 하세요.

사용자 지정 템플릿이 이미 있는 경우 템플릿 및 매개 변수 파일에 있는 세 개의 인증서 관련 매개 변수 이름은 다음과 같이 지정 되 고 값은 null이 되도록 두 번 확인 합니다.

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Windows에서 Service Fabric 클러스터에 응용 프로그램 배포
클러스터에 응용 프로그램을 배포 하려면 [PowerShell을 사용 하 여 응용 프로그램 배포 및 제거](service-fabric-deploy-remove-applications.md)의 단계 및 지침을 따릅니다.


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>이전에 만든 가상 머신 확장 집합에 대해 디스크 암호화 사용

이전 단계를 통해 만든 가상 머신 확장 집합에 대해 디스크 암호화를 사용 하도록 설정 하려면 다음 명령을 실행 합니다.
 
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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Windows에서 가상 머신 확장 집합에 대해 디스크 암호화를 사용 하도록 설정 했는지 확인
다음 명령을 실행 하 여 전체 가상 머신 확장 집합 또는 확장 집합의 모든 인스턴스에 대 한 상태를 가져옵니다.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


또한 가상 머신 확장 집합에 로그인 하 고 드라이브가 암호화 되어 있는지 확인할 수 있습니다.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Service Fabric 클러스터의 가상 머신 확장 집합에 대해 디스크 암호화 사용 안 함 
다음 명령을 실행 하 여 가상 머신 확장 집합에 대 한 디스크 암호화를 사용 하지 않도록 설정 합니다. 디스크 암호화를 사용 하지 않도록 설정 하는 것은 개별 인스턴스가 아니라 전체 가상 머신 확장 집합에 적용 됩니다.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>다음 단계
이 시점에서 보안 클러스터를 사용 하 고 Service Fabric 클러스터 노드 및 가상 머신 확장 집합에 대 한 디스크 암호화를 사용 하거나 사용 하지 않도록 설정 하는 방법을 알고 있어야 합니다. Linux의 클러스터 노드 Service Fabric에 대 한 유사한 지침은 [linux 용 디스크 암호화](service-fabric-enable-azure-disk-encryption-linux.md)를 참조 하세요.

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
