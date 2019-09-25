---
title: Azure CLI를 사용 하 여 Azure에 vhd 업로드
description: Azure CLI를 사용 하 여 Azure 관리 디스크에 vhd를 업로드 하는 방법을 알아봅니다.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: bd4d3b9b34f951896e838d5f6f50ca204d329568
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266602"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure에 vhd 업로드

이 문서에서는 로컬 컴퓨터에서 Azure 관리 디스크로 vhd를 업로드 하는 방법을 설명 합니다. 이전에는 저장소 계정에 데이터를 준비 하 고 저장소 계정을 관리 하는 추가 관련 프로세스를 수행 해야 했습니다. 이제 저장소 계정을 관리 하지 않아도 되며, vhd를 업로드 하기 위해 데이터를 준비 해야 합니다. 대신, 빈 관리 디스크를 만들고이 디스크에 직접 vhd를 업로드 합니다. 이는 온-프레미스 Vm을 Azure로 업로드 하는 작업을 간소화 하 고, 최대 32 TiB의 vhd를 대량 관리 디스크로 직접 업로드할 수 있게 해줍니다.

Azure에서 IaaS Vm에 대 한 백업 솔루션을 제공 하는 경우 직접 업로드를 사용 하 여 고객 백업을 관리 디스크로 복원 하는 것이 좋습니다. Azure 외부의 컴퓨터에서 VHD를 업로드 하는 경우의 속도는 로컬 대역폭에 따라 달라 집니다. Azure VM을 사용 하는 경우 대역폭은 표준 Hdd와 동일 합니다.

현재 직접 업로드는 표준 HDD, 표준 SSD 및 프리미엄 SSD 관리 디스크에 대해 지원 됩니다. 아직 ultra Ssd에 대해 지원 되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [AzCopy v10의 최신 버전](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)을 다운로드 합니다.
- [Azure CLI를 설치합니다](/cli/azure/install-azure-cli).
- 로컬로 저장 된 vhd 파일

## <a name="create-an-empty-managed-disk"></a>빈 관리 디스크 만들기

Azure에 vhd를 업로드 하려면이 업로드 프로세스에 대해 특별히 구성 된 빈 관리 디스크를 만들어야 합니다. 계정을 만들기 전에 이러한 디스크에 대해 알아야 하는 추가 정보가 있습니다.

이러한 종류의 관리 디스크에는 두 가지 고유한 상태가 있습니다.

- ReadToUpload는 디스크가 업로드를 받을 준비가 되었지만 SAS ( [보안 액세스 서명](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) )가 생성 되지 않았음을 의미 합니다.
- ActiveUpload-디스크가 업로드를 받을 준비가 되었으며 SAS가 생성 되었음을 의미 합니다.

이러한 상태 중 하나에서 관리 디스크는 실제 디스크 유형에 상관 없이 [표준 HDD 가격](https://azure.microsoft.com/pricing/details/managed-disks/)으로 청구 됩니다. 예를 들어 P10는 S10로 청구 됩니다. 이는 디스크를 VM `revoke-access` 에 연결 하는 데 필요한 관리 디스크에서가 호출 될 때까지 적용 됩니다.

[디스크 만들기](/cli/azure/disk#az-disk-create) cmdlet에서-- **upload** 매개 변수와 **--upload-bytes** 매개 변수를 모두 지정 하 여 업로드할 빈 표준 HDD를 만듭니다.

```azurecli-interactive
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

프리미엄 SSD 또는 표준 SSD를 업로드 하려는 경우 **standard_lrs** 를 **premium_LRS** 또는 **standardssd_lrs**로 바꿉니다. 울트라 SSD은 아직 지원 되지 않습니다.

이제 업로드 프로세스를 위해 구성 된 빈 관리 디스크를 만들었습니다. 디스크에 vhd를 업로드 하려면 업로드할 대상으로 참조할 수 있도록 쓰기 가능한 SAS가 필요 합니다.

비어 있는 관리 디스크의 쓰기 가능한 SAS를 생성 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

샘플 반환 값:

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Vhd 업로드

이제 빈 관리 디스크에 대 한 SAS가 있으므로이를 사용 하 여 업로드 명령의 대상으로 관리 디스크를 설정할 수 있습니다.

AzCopy v10를 사용 하 여 생성 한 SAS URI를 지정 하 여 로컬 VHD 파일을 관리 되는 디스크에 업로드 합니다.

이 업로드는 동일한 [표준 HDD](disks-types.md#standard-hdd)와 동일한 처리량을 갖습니다. 예를 들어 S4와 동일한 크기의 경우 최대 60 s p s/s의 처리량이 있습니다. 그러나 S70에 해당 하는 크기의 경우 최대 500 m b/s의 처리량이 있습니다.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

업로드 중에 sas가 만료 되어 아직를 호출 `revoke-access` 하지 않은 경우에는를 사용 하 여 `grant-access`업로드를 계속 하는 새 sas를 받을 수 있습니다.

업로드가 완료 되 고 더 이상 디스크에 더 이상 데이터를 쓸 필요가 없으면 SAS를 해지 합니다. SAS를 해지 하면 관리 디스크의 상태가 변경 되 고 해당 디스크를 VM에 연결할 수 있습니다.

```azurecli-interactive
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="next-steps"></a>다음 단계

이제 관리 디스크에 vhd를 성공적으로 업로드 했으므로 VM에 디스크를 연결 하 고 사용을 시작할 수 있습니다.

VM에 디스크를 연결 하는 방법에 대 한 자세한 내용은 주제에 대 한 문서를 참조 하세요. [LINUX VM에 디스크를 추가](add-disk.md)합니다.
