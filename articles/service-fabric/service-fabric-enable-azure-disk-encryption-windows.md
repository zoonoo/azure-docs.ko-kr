---
title: Azure Service Fabric Windows 클러스터에 대 한 디스크 암호화를 사용 하도록 설정 | Microsoft Docs
description: 이 문서에서는 Azure Resource Manager에서 Azure Key Vault를 사용 하 여 Azure Service Fabric 클러스터 노드에 대 한 디스크 암호화를 사용 하는 방법을 설명 합니다.
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
ms.openlocfilehash: c31fc43729bcb58c755959db0c8bc5185b8197f4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66471402"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Windows에서 Azure Service Fabric 클러스터 노드에 대 한 디스크 암호화를 사용 하도록 설정 
> [!div class="op_single_selector"]
> * [Windows용 디스크 암호화](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Linux용 디스크 암호화](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

이 자습서에서는 Windows에서 Service Fabric 클러스터 노드에서 디스크 암호화를 사용 하는 방법에 알아봅니다. 각 노드 형식 및 가상 머신 확장 집합에 대해 다음이 단계를 수행 해야 합니다. 노드를 암호화 하는 것에 대 한 가상 머신 확장 집합에서 Azure Disk Encryption 기능을 사용 하겠습니다.

이 가이드는 다음 항목을 다룹니다.

* Service Fabric 클러스터 가상 머신 확장에서 디스크 암호화를 사용 하도록 설정 하면 Windows에서 설정 하는 경우 알아야 할 주요 개념입니다.
* Service Fabric에서 디스크 암호화를 사용 하도록 설정 하기 전에 다음 단계는 Windows에서 노드 클러스터입니다.
* Windows의 Service Fabric 클러스터 노드에서 디스크 암호화를 사용 하도록 설정 하려면 다음 단계입니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

**Self-registration** 

가상 머신 확장 집합에 대 한 디스크 암호화 미리 보기에는 자체 등록 해야합니다. 다음 단계를 사용하세요. 

1. 먼저 다음 명령을 실행 합니다.
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 상태가 될 때까지 10 분 정도 기다립니다 *Registered*합니다. 다음 명령을 실행 하 여 상태를 확인할 수 있습니다. 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. 키 자격 증명 모음과 동일한 구독 및 지역 확장 집합으로 작성 한 다음 선택 합니다 **EnabledForDiskEncryption** 해당 PowerShell cmdlet을 사용 하 여 key vault에 대 한 정책에 액세스 합니다. 또한 다음 명령 사용 하 여 Azure portal에서 키 자격 증명 모음 UI를 사용 하 여 정책을 설정할 수 있습니다.
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 최신 버전을 설치 합니다 [Azure CLI](/cli/azure/install-azure-cli), 새 암호화 명령이 있습니다.
3. 최신 버전을 설치 합니다 [Azure PowerShell에서 Azure SDK](https://github.com/Azure/azure-powershell/releases) 릴리스 합니다. 다음은 가상 머신 확장 집합을 사용 하도록 설정 하려면 Azure Disk Encryption cmdlet ([설정할](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 암호화를 검색 ([가져오기](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 암호화 상태 및 제거 ([사용 하지 않도록 설정](/powershell/module/az.compute/disable-azvmssdiskencryption)) 눈금에는 암호화 인스턴스를 설정합니다.

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
* OS 암호화가 지원 및 Windows에서 가상 머신 확장의 데이터 볼륨을 설정 합니다. 사용 하지 않도록 Windows에서 설정 하는 가상 머신 확장에 대 한 데이터 볼륨 및 OS에 대 한 암호화도 지원 됩니다.
* 가상 머신 확장 집합에 대 한 가상 머신을 이미지로 다시 설치 및 업그레이드 작업은 현재 미리 보기에서 지원 되지 않습니다.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>새 클러스터를 만들고 디스크 암호화를 사용 하도록 설정

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

필요에 맞게 사용자 지정 템플릿을 작성 해야 하는 경우 좋습니다에서 사용할 수 있는 템플릿 중 하나를 사용 하 여 시작 하는 [Azure Service Fabric 클러스터 만들기 템플릿 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) 페이지입니다. 하 [클러스터 템플릿 사용자 지정] [ customize-your-cluster-template] 섹션에서 다음 지침을 참조 하세요.

사용자 지정 템플릿을 이미 있는 경우 모든 세 개의 인증서 관련 매개 변수 템플릿 및 매개 변수 파일을 다음과 같이 라고 하며 다음과 같은 값이 null 다시 확인 합니다.

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
클러스터에 응용 프로그램을 배포 하려면 단계 및 지침에 따릅니다 [제거 응용 프로그램 배포 및 PowerShell을 사용 하 여](service-fabric-deploy-remove-applications.md)입니다.


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Windows에서 설정 하는 가상 머신 확장에 대 한 디스크 암호화를 사용 하면 유효성 검사
다음 명령을 실행 하 여 확장 집합에 전체 가상 머신 확장 집합의 모든 인스턴스 상태를 가져옵니다.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


또한 가상 머신 확장 집합에 로그인 하 고 드라이브 암호화는 수 있습니다.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Service Fabric 클러스터에서 설정 하는 가상 머신 확장에 대 한 디스크 암호화를 사용 하지 않도록 설정 
다음 명령을 실행 하 여 설정 하는 가상 머신 확장에 대 한 디스크 암호화를 사용 하지 않도록 설정 합니다. 전체 가상 머신 확장 집합을 개별 인스턴스가 아니라 디스크 암호화를 사용 하지 않도록 설정 적용 되도록 note 합니다.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>다음 단계
이 시점에서 보안 클러스터가 구축 하 고 사용 하도록 설정 하 고 Service Fabric 클러스터 노드 및 가상 머신 확장 집합 용 디스크 암호화를 사용 하지 않도록 설정 하는 방법을 알고 해야 합니다. Linux에서 Service Fabric 클러스터 노드에서 비슷한 지침은 [Linux 용 디스크 암호화](service-fabric-enable-azure-disk-encryption-linux.md)합니다.

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
