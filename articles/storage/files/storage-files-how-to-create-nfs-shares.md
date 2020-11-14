---
title: NFS 공유 만들기-Azure Files
description: 네트워크 파일 시스템 프로토콜을 사용 하 여 탑재할 수 있는 Azure 파일 공유를 만드는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 7680e251d8411ce154e1f7dfb8af1d66514dd579
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629464"
---
# <a name="how-to-create-an-nfs-share"></a>NFS 공유를 만드는 방법

Azure 파일 공유는 클라우드에 상주 하는 완전히 관리 되는 파일 공유입니다. 서버 메시지 블록 프로토콜 또는 NFS (네트워크 파일 시스템) 프로토콜을 사용 하 여 액세스할 수 있습니다. 이 문서에서는 NFS 프로토콜을 사용 하는 파일 공유를 만드는 방법을 설명 합니다. 두 프로토콜에 대 한 자세한 내용은 [Azure 파일 공유 프로토콜](storage-files-compare-protocols.md)을 참조 하세요.

## <a name="limitations"></a>제한 사항

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>필수 구성 요소

- [FileStorage 계정을](storage-how-to-create-premium-fileshare.md)만듭니다.

    > [!IMPORTANT]
    > NFS 공유는 신뢰할 수 있는 네트워크 에서만 액세스할 수 있습니다. NFS 공유에 대 한 연결은 다음 원본 중 하나에서 시작 해야 합니다.

    - [개인 끝점을 만들거나](storage-files-networking-endpoints.md#create-a-private-endpoint) (권장) [공용 끝점에 대 한 액세스를 제한](storage-files-networking-endpoints.md#restrict-public-endpoint-access)합니다.
    - [Azure Files와 함께 사용 하기 위해 Linux에서 지점 및 사이트 간 (P2S) VPN을 구성](storage-files-configure-p2s-vpn-linux.md)합니다.
    - [Azure Files와 함께 사용 하기 위해 사이트 간 VPN을 구성](storage-files-configure-s2s-vpn.md)합니다.
    - [Express](../../expressroute/expressroute-introduction.md)경로를 구성 합니다.
- Azure CLI를 사용하려면 [최신 버전을 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)하세요.

## <a name="register-the-nfs-41-protocol"></a>NFS 4.1 프로토콜 등록

Azure PowerShell 모듈이 나 Azure CLI를 사용 하는 경우 다음 명령을 사용 하 여 기능을 등록 합니다.

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az login
az feature register --name AllowNfsFileShares \
                    --namespace Microsoft.Storage \
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-that-the-feature-is-registered"></a>기능이 등록 되어 있는지 확인 합니다.

등록 승인은 최대 한 시간까지 걸릴 수 있습니다. 등록이 완료 되었는지 확인 하려면 다음 명령을 사용 합니다.

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

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
    
   PowerShell 버전을 업그레이드 하려면 [기존 Windows Powershell 업그레이드](/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) 를 참조 하세요.
    
1. PowershellGet 모듈의 최신 버전을 설치 합니다.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. PowerShell 콘솔을 닫았다가 다시 엽니다.

1. **Az. Storage** preview module version **2.5.2-preview** 를 설치 합니다.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   PowerShell 모듈을 설치 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps?view=azps-3.0.0) 를 참조 하세요.
   
1. Azure PowerShell 모듈을 사용 하 여 프리미엄 파일 공유를 만들려면 [AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) cmdlet을 사용 합니다.

> [!NOTE]
> 프로 비전 된 공유 크기는 공유 할당량으로 지정 되며 파일 공유는 프로 비전 된 크기에 따라 청구 됩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/)를 참조하세요.

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 프리미엄 파일 공유를 만들려면 [az storage share create](/cli/azure/storage/share-rm) 명령을 사용 합니다.

> [!NOTE]
> 프로 비전 된 공유 크기는 공유 할당량으로 지정 되며 파일 공유는 프로 비전 된 크기에 따라 청구 됩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/)를 참조하세요.

```azurecli-interactive
az storage share-rm create \
    --storage-account $STORAGEACCT \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>다음 단계

NFS 공유를 만들었으므로이를 사용 하려면 Linux 클라이언트에 탑재 해야 합니다. 자세한 내용은 [NFS 공유를 탑재 하는 방법](storage-files-how-to-mount-nfs-shares.md)을 참조 하세요.

문제가 발생 하는 경우 [AZURE NFS 파일 공유 문제 해결](storage-troubleshooting-files-nfs.md)을 참조 하세요.