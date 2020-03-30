---
title: 프리미엄 Azure 파일 공유 만들기
description: 이 문서에서는 프리미엄 Azure 파일 공유를 만드는 방법을 배웁니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7680a28b165dc252159cf95311439508d3c867e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529110"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>프리미엄 Azure 파일 공유를 만드는 방법
프리미엄 파일 공유는 SSD(솔리드 스테이트 디스크) 스토리지 미디어에 제공되며 호스팅 데이터베이스 및 고성능 컴퓨팅(HPC)을 비롯한 IO 집약적인 워크로드에 유용합니다. 프리미엄 파일 공유는 FileStorage 계정이라는 특수 목적 저장소 계정 종류에서 호스팅됩니다. 프리미엄 파일 공유는 고성능 및 엔터프라이즈 규모 애플리케이션을 위해 설계되어 일관된 낮은 대기 시간, 높은 IOPS 및 높은 처리량 공유를 제공합니다.

이 문서에서는 [Azure 포털,](https://portal.azure.com/)Azure PowerShell 및 Azure CLI를 사용하여 이 새 계정 유형을 만드는 방법을 보여 주며 이 문서에서는 이 새 계정 유형을 만드는 방법을 보여 주며, 이 문서에서는 이러한 새 계정 유형을 만드는 방법을 보여 주며, Azure PowerShell 및 Azure CLI를 사용하여 이 새 계정 유형을 만드는 방법을 보여 주며, 이 문서에서는 이러한 새

## <a name="prerequisites"></a>사전 요구 사항

프리미엄 Azure 파일 공유를 포함한 Azure 리소스에 액세스하려면 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Azure 포털을 사용하여 프리미엄 파일 공유 만들기

### <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure 포털에](https://portal.azure.com/)로그인합니다.

### <a name="create-a-filestorage-storage-account"></a>파일 저장소 저장소 계정 만들기

이제 저장소 계정을 만들 준비가 되었습니다.

모든 스토리지 계정은 Azure 리소스 그룹에 속해야 합니다. 리소스 그룹은 Azure 리소스를 그룹화하기 위한 논리적 컨테이너입니다. 스토리지 계정을 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있는 옵션이 있습니다. 이 문서에서는 새 리소스 그룹을 만드는 방법을 보여 주며 있습니다.

1. Azure 포털에서 왼쪽 메뉴에서 **저장소 계정을** 선택합니다.

    ![Azure 포털 메인 페이지 선택 저장소 계정](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 나타나는 **Storage 계정** 창에서 **추가**를 선택합니다.
1. 스토리지 계정을 만들 구독을 선택합니다.
1. **리소스 그룹** 필드 아래에서 **새로 만들기**를 선택합니다. 다음 이미지에 표시된 대로 새 리소스 그룹의 이름을 입력합니다.

1. 그런 다음, 스토리지 계정의 이름을 입력합니다. 선택하는 이름이 Azure에서 고유해야 합니다. 또한 이름의 길이가 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다.
1. 스토리지 계정의 위치를 선택하거나 기본 위치를 사용합니다.
1. **성능에** 대 한 **프리미엄을**선택 합니다.
1. **계정 종류를** 선택하고 **파일 저장소를**선택합니다.
1. **복제를** **LRS(로컬 중복 저장소)의**기본값으로 둡니다.

    ![프리미엄 파일 공유에 대한 저장소 계정을 만드는 방법](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. **검토 + 만들기**를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.
1. **만들기**를 선택합니다.

저장소 계정 리소스가 만들어지면 해당 리소스로 이동합니다.

### <a name="create-a-premium-file-share"></a>프리미엄 파일 공유 만들기

1. 저장소 계정의 왼쪽 메뉴에서 파일 **서비스** 섹션으로 스크롤한 다음 **파일을**선택합니다.
1. **프리미엄 파일 공유를** 만들려면 파일 공유를 선택합니다.
1. 파일 공유에 대한 이름과 원하는 할당량을 입력한 다음 **을 선택합니다.**

> [!NOTE]
> 프로비저닝된 공유 크기는 공유 할당량에 의해 지정되고 파일 공유는 프로비저닝된 크기에 따라 청구되며 자세한 내용은 [가격 책정 페이지를](https://azure.microsoft.com/pricing/details/storage/files/) 참조하십시오.

   ![프리미엄 파일 공유 만들기](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스를 정리하려면 리소스 그룹을 삭제하기만 하면 됩니다. 리소스 그룹을 삭제해도 리소스 그룹과 연결된 다른 리소스뿐만 아니라 연결된 저장소 계정도 삭제됩니다.

## <a name="create-a-premium-file-share-using-powershell"></a>PowerShell을 사용하여 프리미엄 파일 공유 만들기

### <a name="create-an-account-using-powershell"></a>PowerShell을 사용하여 계정 만들기

먼저 [PowerShellGet](/powershell/scripting/gallery/installing-psget) 모듈의 최신 버전을 설치합니다.

그런 다음 PowerShell 모듈을 업그레이드하고 Azure 구독에 로그인한 다음 리소스 그룹을 만든 다음 저장소 계정을 만듭니다.

### <a name="upgrade-your-powershell-module"></a>PowerShell 모듈 업그레이드

PowerShell에서 프리미엄 파일 공유와 상호 작용하려면 Az.Storage 모듈 버전 1.4.0 또는 최신 Az.Storage 모듈을 설치해야 합니다.

관리자 권한으로 PowerShell 세션을 열어 시작합니다.

Az.Storage 모듈 설치:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Azure 구독에 로그인합니다.

`Connect-AzAccount` 명령을 사용하고 화면의 지시에 따라 인증합니다.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

PowerShell에서 새 리소스 그룹을 만들려면 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용합니다.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>파일 스토리지 저장소 계정 만들기

PowerShell에서 파일 저장소 저장소 계정을 만들려면 [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) 명령을 사용합니다.

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>프리미엄 파일 공유 만들기

이제 FileStorage 계정이 있으므로 프리미엄 파일 공유를 만들 수 있습니다. [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) cmdlet을 사용하여 하나를 만듭니다.

> [!NOTE]
> 프로비저닝된 공유 크기는 공유 할당량에 의해 지정되고 파일 공유는 프로비저닝된 크기에 따라 청구되며 자세한 내용은 [가격 책정 페이지를](https://azure.microsoft.com/pricing/details/storage/files/) 참조하십시오.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>리소스 정리

새 스토리지 계정을 포함하여 리소스 그룹 및 관련 리소스를 제거하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용합니다. 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Azure CLI를 사용하여 프리미엄 파일 공유 만들기

Azure Cloud Shell을 시작하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다.

CLI의 로컬 설치에 로그인하려면 먼저 최신 버전이 있는지 확인한 다음 로그인 명령을 실행하십시오.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure CLI로 새 리소스 그룹을 만들려면 [az group create](/cli/azure/group) 명령을 사용합니다.

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>파일 스토리지 저장소 계정 만들기

Azure CLI에서 FileStorage 저장소 계정을 만들려면 [az 저장소 계정 만들기](/cli/azure/storage/account) 명령을 사용합니다.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>스토리지 계정 키 가져오기

저장소 계정 키는 저장소 계정의 리소스에 대한 액세스를 제어하며, 이 문서에서는 프리미엄 파일 공유를 만들기 위해 키를 사용합니다. 키는 스토리지 계정을 만들 때 자동으로 만들어집니다. [az storage account keys list](/cli/azure/storage/account/keys) 명령을 사용하여 스토리지 계정에 대한 스토리지 계정 키를 가져올 수 있습니다.

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>프리미엄 파일 공유 만들기

이제 파일 저장소 계정이 있으므로 프리미엄 파일 공유를 만들 수 있습니다. az [저장소 공유 만들기](/cli/azure/storage/share) 명령을 사용하여 하나를 만듭니다.

> [!NOTE]
> 프로비저닝된 공유 크기는 공유 할당량에 의해 지정되고 파일 공유는 프로비저닝된 크기에 따라 청구되며 자세한 내용은 [가격 책정 페이지를](https://azure.microsoft.com/pricing/details/storage/files/) 참조하십시오.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>리소스 정리

새 스토리지 계정을 포함하여 리소스 그룹과 관련 리소스를 제거하려면 [az group delete](/cli/azure/group) 명령을 사용합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 프리미엄 파일 공유를 만들었습니다. 이 계정에서 제공하는 성과에 대해 알아보려면 계획 가이드의 성능 계층 섹션을 계속 설명합니다.

> [!div class="nextstepaction"]
> [파일 공유 계층](storage-files-planning.md#storage-tiers)