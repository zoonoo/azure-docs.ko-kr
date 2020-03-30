---
title: Azure 파일 공유 만들기
titleSuffix: Azure Files
description: Azure 포털, PowerShell 또는 Azure CLI를 사용하여 Azure 파일 공유를 만드는 방법
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596914"
---
# <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
Azure 파일 공유를 만들려면 사용 방법에 대한 세 가지 질문에 답해야 합니다.

- **Azure 파일 공유에 대한 성능 요구 사항은 무엇입니까?**  
    Azure Files는 하드 디스크 기반(HDD 기반) 하드웨어에서 호스팅되는 표준 파일 공유및 솔리드 스테이트 디스크 기반(SSD 기반) 하드웨어에서 호스팅되는 프리미엄 파일 공유를 제공합니다.

- **어떤 크기의 파일 공유가 필요합니까?**  
    표준 파일 공유는 최대 100TiB까지 사용할 수 있지만 이 기능은 기본적으로 활성화되지 않습니다. 5TiB보다 큰 파일 공유가 필요한 경우 저장소 계정에 대 용량의 파일 공유 기능을 사용하도록 설정해야 합니다. 프리미엄 파일 공유는 특별한 설정없이 최대 100 TiB까지 지속될 수 있지만 프리미엄 파일 공유는 표준 파일 공유처럼 지불하지 않고 프로비저닝됩니다. 즉, 필요한 것보다 훨씬 큰 파일 공유를 프로비저닝하면 총 저장소 비용이 증가합니다.

- **Azure 파일 공유에 대한 중복 요구 사항은 무엇입니까?**  
    표준 파일 공유는 로컬 중복(LRS), 영역 중복(ZRS), 지리적 중복(GRS) 또는 지리적 영역 중복(GZRS) 저장소를 제공하지만 큰 파일 공유 기능은 로컬 중복 및 영역 중복 파일 공유에서만 지원됩니다. 프리미엄 파일 공유는 어떤 형태의 지리적 중복도 지원하지 않습니다.

    프리미엄 파일 공유는 저장소 계정을 제공하는 대부분의 지역에서 로컬 중복성으로 사용할 수 있으며 더 작은 영역 하위 집합에서 영역 중복성을 제공합니다. 해당 지역에서 프리미엄 파일 공유를 현재 사용할 수 있는지 확인하려면 Azure의 지역 페이지에서 [사용할 수](https://azure.microsoft.com/global-infrastructure/services/?products=storage) 있는 제품을 참조하세요. ZRS를 지원하는 지역에 대한 자세한 내용은 [Azure 저장소 중복을](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)참조하십시오.

이러한 세 가지 선택 항목에 대한 자세한 내용은 [Azure 파일 배포 계획을](storage-files-planning.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항
- 이 문서에서는 Azure 구독을 이미 만들었다고 가정합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- Azure PowerShell을 사용하려는 경우 [최신 버전을 설치합니다.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Azure CLI를 사용하려는 경우 [최신 버전을 설치합니다.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-storage-account"></a>스토리지 계정 만들기
Azure 파일 공유는 *저장소의*공유 풀을 나타내는 최상위 개체인 저장소 계정에 배포됩니다. 이 저장소 풀을 사용하여 여러 파일 공유를 배포할 수 있습니다. 

Azure는 고객이 가질 수 있는 다양한 저장소 시나리오에 대해 여러 유형의 저장소 계정을 지원하지만 Azure Files에 대한 두 가지 주요 유형의 저장소 계정이 있습니다. 만들어야 하는 저장소 계정 유형은 표준 파일 공유또는 프리미엄 파일 공유를 만들지 여부에 따라 달라집니다. 

- **범용 버전 2(GPv2) 저장소 계정**: GPv2 저장소 계정을 사용하면 표준/하드 디스크 기반(HDD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. GPv2 저장소 계정은 Azure 파일 공유를 저장하는 것 외에도 Blob 컨테이너, 큐 또는 테이블과 같은 다른 저장소 리소스를 저장할 수 있습니다. 

- **FileStorage 저장소 계정**: FileStorage 저장소 계정을 사용하면 프리미엄/솔리드 스테이트 디스크 기반(SSD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. FileStorage 계정은 Azure 파일 공유를 저장하는 데만 사용할 수 있습니다. 다른 저장소 리소스(Blob 컨테이너, 큐, 테이블 등)는 FileStorage 계정에 배포할 수 없습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure 포털을 통해 저장소 계정을 만들려면 대시보드에서 **+ 리소스 만들기를** 선택합니다. 결과 Azure Marketplace 검색 창에서 **저장소 계정을** 검색하고 결과 검색 결과를 선택합니다. 이렇게 하면 저장소 계정에 대한 개요 페이지가 표시됩니다. 저장소 계정 만들기 마법사를 진행하려면 **만들기를** 선택합니다.

![브라우저에서 저장소 계정의 스크린샷 빠른 만들기 옵션](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>기본 섹션
저장소 계정을 만들기 위해 완료할 첫 번째 섹션에는 Basics라는 레이블이 **지정됩니다.** 여기에는 저장소 계정을 만드는 데 필요한 모든 필드가 포함됩니다. GPv2 저장소 계정을 만들려면 **성능** 라디오 단추가 *표준으로* 설정되어 있고 **계정 종류** 드롭다운 목록이 *StorageV2(범용 v2)로*선택되어 있는지 확인합니다.

![StorageV2가 선택된 표준 선택 및 계정 종류가 있는 성능 라디오 버튼의 스크린샷](media/storage-how-to-create-file-share/create-storage-account-1.png)

FileStorage 저장소 계정을 만들려면 **성능** 라디오 단추가 *프리미엄으로* 설정되어 있고 **계정 종류** 드롭다운 목록이 *FileStorage*로 선택되어 있는지 확인합니다.

![프리미엄이 선택된 성능 라디오 버튼의 스크린샷과 FileStorage를 선택한 계정 종류](media/storage-how-to-create-file-share/create-storage-account-2.png)

다른 기본 필드는 저장소 계정의 선택과 독립적입니다.
- **구독**: 배포할 저장소 계정에 대한 구독입니다. 
- **리소스 그룹:** 저장소 계정에 배포할 리소스 그룹입니다. 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있습니다. 리소스 그룹은 Azure 리소스를 그룹화하기 위한 논리적 컨테이너입니다. 스토리지 계정을 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있는 옵션이 있습니다.
- **저장소 계정 이름**: 만들 저장소 계정 리소스의 이름입니다. 이 이름은 전역적으로 고유해야 하지만 그렇지 않으면 원하는 이름을 지정할 수 있습니다. SMB를 통해 Azure 파일 공유를 탑재할 때 저장소 계정 이름이 서버 이름으로 사용됩니다.
- **위치**: 저장소 계정을 배포할 영역입니다. 리소스 그룹 또는 사용 가능한 다른 리전과 연결된 영역일 수 있습니다.
- **복제**: 이 레이블이 복제된 것이지만 이 필드는 실제로 **중복성을**의미합니다. 로컬 중복성(LRS), 영역 중복성(ZRS), 지리적 중복성(GRS) 및 지리적 영역 중복성 등 원하는 중복 수준이 다 수 있습니다. 이 드롭다운 목록에는 Azure 파일 공유에 적용되지 않는 읽기 액세스 지리적 중복성(RA-GRS) 및 읽기 액세스 지리적 영역 중복성(RA-GZRS)도 포함되어 있습니다. 이러한 파일이 선택한 저장소 계정에서 생성된 모든 파일 공유는 실제로 각각 지리적 중복 또는 지역 영역 중복이 됩니다. 지역 또는 선택한 저장소 계정 유형에 따라 일부 중복 옵션이 허용되지 않을 수 있습니다.
- **액세스 계층**: 이 필드는 Azure 파일에 적용되지 않으므로 라디오 단추 중 하나를 선택할 수 있습니다.

#### <a name="the-networking-blade"></a>네트워킹 블레이드
네트워킹 섹션에서 네트워킹 옵션을 구성할 수 있습니다. 이러한 설정은 저장소 계정을 만들기 위한 선택 사항이며 원하는 경우 나중에 구성할 수 있습니다. 이러한 옵션에 대한 자세한 내용은 [Azure Files 네트워킹 고려 사항을](storage-files-networking-overview.md)참조하십시오.

#### <a name="the-advanced-blade"></a>고급 블레이드
고급 섹션에는 Azure 파일 공유에 대한 몇 가지 중요한 설정이 포함되어 있습니다.

- **보안 전송 필요**: 이 필드는 저장소 계정에 통신하기 위해 저장소 계정에 암호화가 필요한지 여부를 나타냅니다. SMB 2.1 지원이 필요한 경우 이 기능을 사용하지 않도록 설정하는 것이 좋습니다. 서비스 끝점 및/또는 개인 엔드포인트가 있는 가상 네트워크에 대한 저장소 계정 액세스를 제한하는 암호화를 사용하지 않도록 설정하는 것이 좋습니다.
- **대용량 파일 공유**: 이 필드를 사용하면 최대 100TiB에 걸친 파일 공유에 대한 저장소 계정을 사용할 수 있습니다. 이 기능을 사용하면 저장소 계정이 로컬 중복 및 영역 중복 저장소 옵션으로만 제한됩니다. GPv2 저장소 계정이 대용량 파일 공유에 대해 활성화되면 대용량 파일 공유 기능을 비활성화할 수 없습니다. FileStorage 저장소 계정(프리미엄 파일 공유용 저장소 계정)에는 모든 프리미엄 파일 공유가 최대 100TiB까지 확장될 수 있기 때문에 이 옵션이 없습니다. 

![Azure 파일에 적용되는 중요한 고급 설정의 스크린샷](media/storage-how-to-create-file-share/create-storage-account-3.png)

고급 탭에서 사용할 수 있는 다른 설정(Blob 소프트 삭제, Azure Data Lake 저장소 세대 2의 계층 적 네임스페이스 및 Blob 저장소의 NFSv3)은 Azure 파일에 적용되지 않습니다.

#### <a name="tags"></a>태그들
태그는 여러 리소스 및 리소스 그룹에 동일한 태그를 적용하여 리소스를 분류하고 통합 청구를 볼 수 있는 이름/값 쌍입니다. 이는 선택 사항이며 저장소 계정 생성 후에 적용할 수 있습니다.

#### <a name="review--create"></a>검토 + 만들기
저장소 계정을 만드는 마지막 단계는 검토 + 만들기 탭에서 **만들기** 단추를 선택하는 **것입니다.** 저장소 계정에 필요한 필드가 모두 채워지지 않으면 이 단추를 사용할 수 없습니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
PowerShell을 사용하여 저장소 계정을 만들려면 `New-AzStorageAccount` cmdlet을 사용합니다. 이 cmdlet에는 많은 옵션이 있습니다. 필요한 옵션만 표시됩니다. 고급 옵션에 대한 자세한 내용은 [ `New-AzStorageAccount` cmdlet 설명서를](/powershell/module/az.storage/new-azstorageaccount)참조하십시오.

저장소 계정 및 후속 파일 공유의 생성을 단순화하기 위해 변수에 여러 매개 변수를 저장합니다. 변수 내용을 원하는 값으로 바꿀 수 있지만 저장소 계정 이름은 전역적으로 고유해야 합니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

표준 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용합니다. 매개 `-SkuName` 변수는 원하는 중복 유형과 관련이 있습니다. 지역 중복 또는 지역 영역 중복 저장소 계정을 원하는 경우 `-EnableLargeFileShare` 매개 변수도 제거해야 합니다.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

프리미엄 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용합니다. 매개 변수가 `-SkuName` 로컬 중복()의 `Premium` `LRS`원하는 중복 수준과 둘 다 포함하도록 변경되었습니다. 이 `-Kind` 매개 `FileStorage` 변수는 GPv2 저장소 계정 대신 FileStorage 저장소 계정에 프리미엄 파일 공유를 만들어야 하기 `StorageV2` 때문입니다.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI를 사용하여 저장소 계정을 만들려면 az 저장소 계정 create 명령을 사용합니다. 이 명령에는 많은 옵션이 있습니다. 필요한 옵션만 표시됩니다. 고급 옵션에 대한 자세한 내용은 [ `az storage account create` 명령 설명서를](/cli/azure/storage/account)참조하십시오.

저장소 계정 및 후속 파일 공유의 생성을 단순화하기 위해 변수에 여러 매개 변수를 저장합니다. 변수 내용을 원하는 값으로 바꿀 수 있지만 저장소 계정 이름은 전역적으로 고유해야 합니다.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

표준 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용합니다. 매개 `--sku` 변수는 원하는 중복 유형과 관련이 있습니다. 지역 중복 또는 지역 영역 중복 저장소 계정을 원하는 경우 `--enable-large-file-share` 매개 변수도 제거해야 합니다.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

프리미엄 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용합니다. 매개 변수가 `--sku` 로컬 중복()의 `Premium` `LRS`원하는 중복 수준과 둘 다 포함하도록 변경되었습니다. 이 `--kind` 매개 `FileStorage` 변수는 GPv2 저장소 계정 대신 FileStorage 저장소 계정에 프리미엄 파일 공유를 만들어야 하기 `StorageV2` 때문입니다.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>파일 공유 만들기
저장소 계정을 만든 후에는 파일 공유를 만들 수 있습니다. 이 프로세스는 프리미엄 파일 공유 또는 표준 파일 공유를 사용 하든 관계없이 대부분 동일합니다. 주요 차이점은 **할당량과 할당량이** 나타내는 값입니다.

표준 파일 공유의 경우 최종 사용자가 갈 수 없는 Azure 파일 공유의 상위 경계입니다. 표준 파일 공유에 대한 할당량의 주요 목적은 예산입니다. 할당량을 지정하지 않으면 표준 파일 공유가 최대 100TiB(또는 대용량 파일 공유 속성이 저장소 계정에 대해 설정되지 않은 경우 5TiB)까지 확장될 수 있습니다.

프리미엄 파일 공유의 경우 할당량이 **프로비저닝된 크기를**의미하도록 오버로드됩니다. 프로비저닝된 크기는 실제 사용량에 관계없이 청구되는 금액입니다. 프리미엄 파일 공유를 프로비전할 때 는 1) 공간 활용 관점에서 공유의 미래 성장과 2) 워크로드에 필요한 IOPS의 두 가지 요소를 고려해야 합니다. 프로비저닝된 모든 GiB는 추가 예약 및 버스트 IOPS를 받을 수 있습니다. 프리미엄 파일 공유를 계획하는 방법에 대한 자세한 내용은 [프리미엄 파일 공유 프로비저닝을](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)참조하십시오.

# <a name="portal"></a>[포털](#tab/azure-portal)
저장소 계정을 방금 만든 경우 배포 화면에서 **리소스로 이동을 선택하여**저장소 계정으로 이동할 수 있습니다. 이전에 저장소 계정을 만든 경우 저장소 계정이 포함된 리소스 그룹을 통해 저장소 계정으로 이동할 수 있습니다. 저장소 계정에서 **파일 공유라는** 타일을 선택합니다(저장소 계정의 목차를 통해 **파일 공유로** 이동할 수도 있음).

![파일의 스크린샷은 타일을 공유합니다.](media/storage-how-to-create-file-share/create-file-share-1.png)

파일 공유 목록에서 이 저장소 계정에서 이전에 만든 파일 공유가 표시됩니다. 파일 공유가 아직 만들어지지 않은 경우 빈 테이블입니다. **+ 파일 공유를** 선택하여 새 파일 공유를 만듭니다.

새 파일 공유 블레이드가 화면에 나타납니다. 새 파일 공유 블레이드의 필드를 완료하여 파일 공유를 만듭니다.

- **이름**: 만들 파일 공유의 이름입니다.
- **할당량**: 표준 파일 공유에 대한 파일 공유 할당량; 프리미엄 파일 공유에 대한 파일 공유의 프로비저닝 된 크기입니다.

새 공유 작성을 완료하려면 **만들기를** 선택합니다. 저장소 계정이 가상 네트워크에 있는 경우 클라이언트가 가상 네트워크에 있지 않으면 Azure 파일 공유를 성공적으로 만들 수 없습니다. Azure PowerShell `New-AzRmStorageShare` cmdlet을 사용하여 이 시점 제한에 대해서도 해결할 수 있습니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
cmdlet을 사용하여 Azure [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) 파일 공유를 만들 수 있습니다. 다음 PowerShell 명령은 Azure PowerShell 섹션을 `$resourceGroupName` `$storageAccountName` 통해 저장소 계정을 만드는 데 위에서 정의된 변수와 위에서 정의한 대로 설정했다고 가정합니다. 

> [!Important]  
> 프리미엄 파일 공유의 `-QuotaGiB` 경우 매개 변수는 파일 공유의 프로비저닝된 크기를 나타냅니다. 파일 공유의 프로비저닝된 크기는 사용량에 관계없이 청구되는 금액입니다. 표준 파일 공유는 프로비저닝된 크기가 아닌 사용량에 따라 청구됩니다.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> 파일 공유의 이름은 모두 소문자여야 합니다. 파일 공유 및 파일 의 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조를](https://msdn.microsoft.com/library/azure/dn167011.aspx)참조하십시오.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI를 사용하여 Azure 파일 공유를 만들려면 먼저 파일 공유 만들기 작업을 승인하려면 저장소 계정 키를 받아야 합니다. 이 작업은 다음과 [`az storage account keys list`](/cli/azure/storage/account/keys) 같은 명령으로 수행할 수 있습니다.

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

저장소 계정 키가 있으면 명령을 사용하여 Azure 파일 공유를 [`az storage share create`](/cli/azure/storage/share) 만들 수 있습니다. 

> [!Important]  
> 프리미엄 파일 공유의 `--quota` 경우 매개 변수는 파일 공유의 프로비저닝된 크기를 나타냅니다. 파일 공유의 프로비저닝된 크기는 사용량에 관계없이 청구되는 금액입니다. 표준 파일 공유는 프로비저닝된 크기가 아닌 사용량에 따라 청구됩니다.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

저장소 계정이 가상 네트워크에 포함되어 있고 이 명령을 호출하는 컴퓨터가 가상 네트워크의 일부가 아닌 경우 이 명령이 실패합니다. 위에서 설명한 대로 Azure PowerShell `New-AzRmStorageShare` cmdlet을 사용하거나 VPN 연결을 포함하여 가상 네트워크의 일부인 컴퓨터에서 Azure CLI를 실행하여 이 시점 제한을 해결할 수 있습니다.

---

> [!Note]  
> 파일 공유의 이름은 모두 소문자여야 합니다. 파일 공유 및 파일 의 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조를](https://msdn.microsoft.com/library/azure/dn167011.aspx)참조하십시오.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포에 대](storage-files-planning.md) 한 Azure 파일 또는 [계획의 배포에 대 한 계획.](storage-sync-files-planning.md) 
- [네트워킹 개요](storage-files-networking-overview.md).
- 연결하고 [윈도우,](storage-how-to-use-files-windows.md) [맥 OS,](storage-how-to-use-files-mac.md) [리눅스에](storage-how-to-use-files-linux.md)파일 공유를 탑재 .