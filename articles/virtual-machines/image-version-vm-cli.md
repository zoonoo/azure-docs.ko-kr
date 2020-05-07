---
title: VM에서 이미지 만들기
description: Azure의 VM에서 공유 이미지 갤러리에 이미지를 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f53a6b63c744b0e3e41f7ad22270cd842da57674
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796578"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure의 VM에서 이미지 버전 만들기

동일한 vm을 여러 개 만드는 데 사용 하려는 기존 VM이 있는 경우 해당 VM을 사용 하 여 Azure CLI를 사용 하 여 공유 이미지 갤러리에 이미지를 만들 수 있습니다. [Azure PowerShell](image-version-vm-powershell.md)를 사용 하 여 VM에서 이미지를 만들 수도 있습니다.

**이미지 버전** 은 공유 이미지 갤러리를 사용할 때 VM을 만드는 데 사용 됩니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. 이미지 버전을 사용 하 여 VM을 만드는 경우 이미지 버전이 새 VM에 대 한 디스크를 만드는 데 사용 됩니다. 이미지 버전은 여러 번 사용할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 기존 공유 이미지 갤러리가 있어야 합니다. 

또한 Azure의 기존 VM이 갤러리와 동일한 지역에 있어야 합니다. 

VM에 연결 된 데이터 디스크가 있는 경우 데이터 디스크 크기는 1TB를 넘을 수 없습니다.

이 문서를 진행할 때 필요한 경우 리소스 이름을 바꿉니다.

## <a name="get-information-about-the-vm"></a>VM 관련 정보 가져오기

[Az vm list](/cli/azure/vm#az-vm-list)를 사용 하 여 사용할 수 있는 vm 목록을 볼 수 있습니다. 

```azurecli-interactive
az vm list --output table
```

VM 이름 및 VM이 있는 리소스 그룹을 알고 있으면 [az vm get instance-view](/cli/azure/vm#az-vm-get-instance-view)를 사용 하 여 VM의 ID를 가져옵니다. 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>이미지 정의 만들기

이미지 정의 이미지에 대 한 논리적 그룹화를 만듭니다. 이러한 파일은 내부에서 생성 된 이미지 버전에 대 한 정보를 관리 하는 데 사용 됩니다. 

이미지 정의 이름은 대문자, 숫자, 점, 대시 및 마침표로 구성 될 수 있습니다. 

이미지 정의가 올바른 형식 인지 확인 합니다. VM을 일반화 한 경우 (Windows 용 Sysprep 사용 또는 Linux 용 waagent-프로 비전 해제)를 사용 하 여 `--os-state generalized`일반화 된 이미지 정의를 만들어야 합니다. 기존 사용자 계정을 제거 하지 않고 VM을 사용 하려는 경우를 사용 하 여 `--os-state specialized`특수 이미지 정의를 만듭니다.

이미지 정의에 대해 지정할 수 있는 값에 대 한 자세한 내용은 [이미지 정의](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)를 참조 하세요.

[Az sig image definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)를 사용 하 여 갤러리에서 이미지 정의를 만듭니다.

이 예제에서 이미지 정의 이름은 *Myimagedefinition*이며 [특수](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) 한 Linux OS 이미지를 위한 것입니다. Windows OS를 사용 하 여 이미지에 대 한 정의를 `--os-type Windows`만들려면를 사용 합니다. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>이미지 버전 만들기

[Az image gallery 만들기-이미지-버전](/cli/azure/sig/image-version#az-sig-image-version-create)을 사용 하 여 VM에서 이미지 버전을 만듭니다.  

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*. *MinorVersion*. *패치*.

이 예제에서 이미지의 버전은 *1.0.0* 이며 *, 지역 중복* 저장소를 사용 하 여 *미국 동부 2* 지역에서 *미국 중부 지역* 에 1 개의 복제본과 1 개의 복제본을 만듭니다. 복제 지역은 원본 VM이 있는 지역을 포함 해야 합니다.

이 예제의 값 `--managed-image` 을 이전 단계의 VM ID로 바꿉니다.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 동일한 관리 되는 이미지를 사용 하 여 다른 이미지 버전을 만들려면 먼저 이미지 버전이 빌드되고 복제 될 때까지 기다려야 합니다.
>
> 이미지 버전을 만들 때를 추가 `--storage-account-type  premium_lrs` `--storage-account-type  standard_zrs` 하 여 추가 또는 [영역 중복 저장소](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 로 Premiun 저장소에 이미지를 저장할 수도 있습니다.
>

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용 하 여 [일반화 된 이미지](vm-generalized-image-version-cli.md) 에서 VM을 만듭니다.
