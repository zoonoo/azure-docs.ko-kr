---
title: Azure에 VHD를 업로드하거나 여러 리전 - Azure CLI에서 디스크 복사
description: 직접 업로드를 통해 Azure CLI를 사용하여 Azure 관리 디스크에 VHD를 업로드하고 여러 리전에서 관리디스크를 복사하는 방법을 알아봅니다.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6813206aebe67e4e6d2afd0d9c78d03f0c20c952
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420965"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Azure에 VHD를 업로드하거나 관리 디스크를 다른 리전인 Azure CLI로 복사

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>사전 요구 사항

- [AzCopy v10의](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)최신 버전을 다운로드합니다.
- [Azure CLI를 설치합니다.](/cli/azure/install-azure-cli)
- 온-프레미스에서 VHD를 업로드하려는 경우: [Azure용으로 준비된](../windows/prepare-for-upload-vhd-image.md)고정 크기 VHD는 로컬에 저장됩니다.
- 또는 복사 작업을 수행하려는 경우 Azure의 관리 디스크입니다.

## <a name="getting-started"></a>시작

GUI를 통해 디스크를 업로드하려는 경우 Azure 저장소 탐색기를 사용하여 디스크를 업로드할 수 있습니다. 자세한 내용은 참조: [Azure 저장소 탐색기를 사용하여 Azure 관리 디스크를 관리합니다.](disks-use-storage-explorer-managed-disks.md)

VHD를 Azure에 업로드하려면 이 업로드 프로세스에 대해 구성된 빈 관리 디스크를 만들어야 합니다. 하나를 만들기 전에 이러한 디스크에 대해 알아야 할 몇 가지 추가 정보가 있습니다.

이러한 종류의 관리 디스크에는 두 가지 고유한 상태가 있습니다.

- ReadToUpload는 디스크가 업로드를 받을 준비가 되었지만 [SAS(보안 액세스 서명)가](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) 생성되지 않았습니다.
- ActiveUpload는 디스크가 업로드를 받을 준비가 되었고 SAS가 생성되었음을 의미합니다.

> [!NOTE]
> 이러한 상태 중 하나에 있는 동안 관리 되는 디스크는 [표준 HDD 가격으로](https://azure.microsoft.com/pricing/details/managed-disks/)청구 됩니다., 디스크의 실제 종류에 관계 없이. 예를 들어 P10은 S10으로 청구됩니다. 이는 VM에 `revoke-access` 디스크를 연결하기 위해 필요한 관리 디스크에서 호출될 때까지 해당됩니다.

## <a name="create-an-empty-managed-disk"></a>빈 관리 디스크 만들기

업로드를 위해 빈 표준 HDD를 만들려면 업로드하려는 VHD의 파일 크기가 바이트별로 필요합니다. 이를 얻으려면 또는 `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`을 사용할 수 있습니다. 이 값은 **--upload-size-bytes** 매개 변수를 지정할 때 사용됩니다.

**업로드용 --for-upload** 매개 변수와 cmdlet을 [만드는 디스크의](/cli/azure/disk#az-disk-create) **--upload-size-bytes** 매개 변수를 모두 지정하여 업로드를 위한 빈 표준 HDD를 만듭니다.

`<yourresourcegroupname>`을 `<yourdiskname>` `<yourregion>` 선택한 값으로 바꿉습니다. 매개 `--upload-size-bytes` 변수에는 `34359738880`의 예제 값이 포함되어 있습니다.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

프리미엄 SSD 또는 표준 SSD를 업로드하려면 **standard_lrs** **premium_LRS** 또는 **standardssd_lrs**로 바꿉습니다. 울트라 디스크는 현재 지원되지 않습니다.

업로드 프로세스에 대해 구성된 빈 관리 디스크를 만들었으니 VHD를 업로드할 수 있습니다. 디스크에 VHD를 업로드하려면 쓰기 가능한 SAS가 필요하므로 업로드 대상으로 참조할 수 있습니다.

빈 관리 디스크의 쓰기 가능한 SAS를 `<yourdiskname>`생성하려면 다음 명령을 사용하여 교체하고 `<yourresourcegroupname>`을 사용합니다.

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

샘플 반환 값:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>VHD 업로드

빈 관리 디스크에 대한 SAS가 있으므로 이를 사용하여 관리 디스크를 업로드 명령의 대상으로 설정할 수 있습니다.

AzCopy v10을 사용하여 생성한 SAS URI를 지정하여 로컬 VHD 파일을 관리 디스크에 업로드합니다.

이 업로드는 동일한 [표준 HDD와](disks-types.md#standard-hdd)동일한 처리량을 가짐을 가짐입니다. 예를 들어 S4와 같은 크기가 있는 경우 최대 60MiB/s의 처리량을 갖게 됩니다. 그러나 S70과 같은 크기가 있는 경우 최대 500MiB/s의 처리량을 갖게 됩니다.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

업로드가 완료되면 더 이상 디스크에 데이터를 쓸 필요가 없으므로 SAS를 해지합니다. SAS를 취소하면 관리 디스크의 상태가 변경되고 디스크를 VM에 연결할 수 있습니다.

`<yourresourcegroupname>`다음 `<yourdiskname>`명령을 사용하여 디스크를 사용할 수 있도록 합니다.

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>관리 디스크 복사

또한 직접 업로드하면 관리되는 디스크를 복사하는 프로세스도 간소화됩니다. 동일한 리전 내에서 복사하거나 다른 지역으로 교차 영역을 복사할 수 있습니다.

다음 스크립트는 이 작업을 수행하며, 프로세스는 기존 디스크로 작업하기 때문에 몇 가지 차이점이 있는 이전 단계와 유사합니다.

> [!IMPORTANT]
> Azure에서 관리되는 디스크의 바이트 단위로 디스크 크기를 제공할 때 512의 오프셋을 추가해야 합니다. 이는 Azure가 디스크 크기를 반환할 때 바닥글이 생략하기 때문입니다. 이렇게 하지 않으면 복사본이 실패합니다. 다음 스크립트는 이미 이 작업을 수행합니다.

`<sourceResourceGroupHere>`을 `<sourceDiskNameHere>`을 `<targetDiskNameHere>`대체하고 `<targetResourceGroupHere>` `<yourTargetLocationHere>` (위치 값의 예는 uswest2) 다음 스크립트를 실행하여 관리되는 디스크를 복사합니다.

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>다음 단계

관리 디스크에 VHD를 성공적으로 업로드했으므로 디스크를 [기존 VM에 데이터 디스크로](add-disk.md) 연결하거나 [디스크를 OS 디스크로 VM에 연결하여](upload-vhd.md#create-the-vm)새 VM을 만들 수 있습니다. 

