---
title: NFS 공유 만들기-Azure Files (미리 보기)
description: 네트워크 파일 시스템 프로토콜을 사용 하 여 탑재할 수 있는 Azure 파일 공유를 만드는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 2ff669d0ff3bde791de9bc7773e13d880762f898
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214425"
---
# <a name="how-to-create-an-nfs-share"></a>NFS 공유를 만드는 방법
Azure 파일 공유는 클라우드에 상주 하는 완전히 관리 되는 파일 공유입니다. 이 문서에서는 NFS 프로토콜을 사용 하는 파일 공유를 만드는 방법을 설명 합니다. 두 프로토콜에 대 한 자세한 내용은 [Azure 파일 공유 프로토콜](storage-files-compare-protocols.md)을 참조 하세요.

## <a name="limitations"></a>제한 사항
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>국가별 가용성
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>필수 구성 요소
- NFS 공유는 신뢰할 수 있는 네트워크 에서만 액세스할 수 있습니다. NFS 공유에 대 한 연결은 다음 원본 중 하나에서 시작 해야 합니다.
    - [개인 끝점을 만들거나](storage-files-networking-endpoints.md#create-a-private-endpoint) (권장) [공용 끝점에 대 한 액세스를 제한](storage-files-networking-endpoints.md#restrict-public-endpoint-access)합니다.
    - [Azure Files와 함께 사용 하기 위해 Linux에서 지점 및 사이트 간 (P2S) VPN을 구성](storage-files-configure-p2s-vpn-linux.md)합니다.
    - [Azure Files와 함께 사용 하기 위해 사이트 간 VPN을 구성](storage-files-configure-s2s-vpn.md)합니다.
    - [Express](../../expressroute/expressroute-introduction.md)경로를 구성 합니다.

- Azure CLI를 사용하려면 [최신 버전을 설치](/cli/azure/install-azure-cli)하세요.

## <a name="register-the-nfs-41-protocol"></a>NFS 4.1 프로토콜 등록
Azure PowerShell 모듈이 나 Azure CLI를 사용 하는 경우 다음 명령을 사용 하 여 기능을 등록 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure PowerShell 또는 Azure CLI를 사용 하 여 Azure Files에 NFS 4.1 기능을 등록 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

등록 승인은 최대 한 시간까지 걸릴 수 있습니다. 등록이 완료 되었는지 확인 하려면 다음 명령을 사용 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure PowerShell 또는 Azure CLI를 사용 하 여 Azure Files에 대 한 NFS 4.1 기능의 등록을 확인 합니다. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>FileStorage 저장소 계정 만들기
현재 NFS 4.1 공유는 프리미엄 파일 공유로만 사용할 수 있습니다. NFS 4.1 프로토콜 지원을 사용 하 여 프리미엄 파일 공유를 배포 하려면 먼저 FileStorage storage 계정을 만들어야 합니다. 저장소 계정은 azure에서 여러 Azure 파일 공유를 배포 하는 데 사용할 수 있는 저장소의 공유 풀을 나타내는 최상위 개체입니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
FileStorage 저장소 계정을 만들려면 Azure Portal로 이동 합니다.

1. Azure Portal의 왼쪽 메뉴에서 **저장소 계정** 을 선택 합니다.

    ![Azure Portal 기본 페이지 저장소 계정 선택](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. 나타나는 **Storage 계정** 창에서 **추가** 를 선택합니다.
3. 스토리지 계정을 만들 구독을 선택합니다.
4. 저장소 계정을 만들 리소스 그룹을 선택 합니다.

5. 그런 다음, 스토리지 계정의 이름을 입력합니다. 선택하는 이름이 Azure에서 고유해야 합니다. 또한 이름의 길이가 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다.
6. 스토리지 계정의 위치를 선택하거나 기본 위치를 사용합니다.
7. **성능을** 위해 **프리미엄** 을 선택 합니다.

    **계정 종류** 드롭다운에서 사용 가능한 옵션을 사용 하려면 **Premium** for **FileStorage** 를 선택 해야 합니다.

8. **계정 종류** 를 선택 하 고 **FileStorage** 를 선택 합니다.
9. **복제** 를 기본값인 **LRS (로컬 중복 저장소)** 로 설정 된 상태로 둡니다.

    ![프리미엄 파일 공유에 대 한 저장소 계정을 만드는 방법](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. **검토 + 만들기** 를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.
11. **만들기** 를 선택합니다.

저장소 계정 리소스를 만든 후으로 이동 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
FileStorage 저장소 계정을 만들려면 PowerShell 프롬프트를 열고 다음 명령을 실행 합니다 `<resource-group>` . 이때 및를 `<storage-account>` 사용자 환경에 적합 한 값으로 바꿉니다.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
FileStorage 저장소 계정을 만들려면 터미널을 열고 다음 명령을 실행 합니다 `<resource-group>` . 이때 및를 `<storage-account>` 사용자 환경에 적합 한 값으로 바꿉니다.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="create-an-nfs-share"></a>NFS 공유 만들기

# <a name="portal"></a>[포털](#tab/azure-portal)

FileStorage 계정을 만들고 네트워킹을 구성 했으므로 NFS 파일 공유를 만들 수 있습니다. 이 프로세스는 SMB 공유를 만들 때와 유사 하며, 공유를 만들 때 **smb** 대신 **NFS** 를 선택 합니다.

1. 스토리지 계정으로 이동하여 **파일 공유** 를 선택합니다.
1. **+ 파일 공유** 를 선택 하 여 새 파일 공유를 만듭니다.
1. 파일 공유 이름을로 프로 비전 된 용량을 선택 합니다.
1. **프로토콜** 의 경우 **NFS (미리 보기)** 를 선택 합니다.
1. **Root Squash** 는 선택 항목을 만듭니다.

    - Root squash (기본값)-remote 수퍼유저 (root)에 대 한 액세스는 UID (65534) 및 GID (65534)에 매핑됩니다.
    - Root squash-Remote 수퍼유저 (root)는 root로 액세스를 수신 합니다.
    - 모든 squash-모든 사용자 액세스는 UID (65534) 및 GID (65534)에 매핑됩니다.
    
1. **만들기** 를 선택합니다.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="파일 공유 만들기 블레이드의 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. .NET framework가 설치 되어 있는지 확인 합니다. [다운로드 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)를 참조 하세요.
 
1. 다음 명령을 사용 하 여 설치한 PowerShell 버전이 이상 인지 확인 `5.1` 합니다.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   PowerShell 버전을 업그레이드 하려면 [기존 Windows Powershell 업그레이드](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell) 를 참조 하세요.
    
1. PowershellGet 모듈의 최신 버전을 설치 합니다.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. PowerShell 콘솔을 닫았다가 다시 엽니다.

1. **Az. Storage** preview module version **2.5.2-preview** 를 설치 합니다.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   PowerShell 모듈을 설치 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps?view=azps-3.0.0&preserve-view=true) 를 참조 하세요.
   
1. Azure PowerShell 모듈을 사용 하 여 프리미엄 파일 공유를 만들려면 [AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) cmdlet을 사용 합니다.

    > [!NOTE]
    > 프리미엄 파일 공유는 프로 비전 된 모델을 사용 하 여 청구 됩니다. 공유의 프로 비전 된 크기는 아래에 지정 되어 `QuotaGiB` 있습니다. 자세한 내용은 [프로 비전 된 모델 이해](understanding-billing.md#provisioned-model) 및 [Azure Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/)를 참조 하세요.

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI를 사용 하 여 프리미엄 파일 공유를 만들려면 [az storage share create](/cli/azure/storage/share-rm) 명령을 사용 합니다.

> [!NOTE]
> 프리미엄 파일 공유는 프로 비전 된 모델을 사용 하 여 청구 됩니다. 공유의 프로 비전 된 크기는 아래에 지정 되어 `quota` 있습니다. 자세한 내용은 [프로 비전 된 모델 이해](understanding-billing.md#provisioned-model) 및 [Azure Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/)를 참조 하세요.

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>다음 단계
NFS 공유를 만들었으므로이를 사용 하려면 Linux 클라이언트에 탑재 해야 합니다. 자세한 내용은 [NFS 공유를 탑재 하는 방법](storage-files-how-to-mount-nfs-shares.md)을 참조 하세요.

문제가 발생 하는 경우 [AZURE NFS 파일 공유 문제 해결](storage-troubleshooting-files-nfs.md)을 참조 하세요.