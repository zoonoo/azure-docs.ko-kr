---
title: "Microsoft Azure Windows VM에 대한 디스크 및 VHD 정보 | Microsoft Docs"
description: "Azure의 Windows 가상 컴퓨터용 디스크 및 VHD의 기본 사항에 대해 알아봅니다."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 0142c64d-5e8c-4d62-aa6f-06d6261f485a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 3b9d6eb9bcc4afe0e68920bbd5da7c259ceb0c67
ms.lasthandoff: 04/06/2017


---
# <a name="about-disks-and-vhds-for-azure-windows-vms"></a>Azure Windows VM용 디스크 및 VHD 정보
다른 컴퓨터와 마찬가지로, Azure에서 가상 컴퓨터는 운영 체제, 응용 프로그램 및 데이터를 저장하는 장소로 디스크를 사용합니다. 모든 Azure 가상 컴퓨터는 Windows 운영 체제 디스크와 임시 디스크라는 적어도 2개의 디스크를 갖습니다. 운영 체제 디스크는 이미지에서 만들어지며, 운영 체제 디스크 및 이미지 모두는 Azure 저장소 계정에 저장된 VHD(가상 하드 디스크)입니다. 가상 컴퓨터에도 데이터 디스크가 있을 수 있으며 이러한 디스크도 VHD로 저장됩니다. 

이 문서에서는 디스크의 여러 가지 사용법에 대해 설명한 후 사용자가 만들고 사용할 수 있는 다양한 디스크 형식에 대해 설명합니다. 이 문서는 [Linux 가상 컴퓨터](storage-about-disks-and-vhds-linux.md)에도 적용됩니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>VM에서 사용되는 디스크

VM에서 디스크를 사용하는 방법에 대해 살펴보겠습니다.

### <a name="operating-system-disk"></a>운영 체제 디스크
모든 가상 컴퓨터는 하나의 연결된 운영 체제 디스크를 갖습니다. 이 디스크는 SATA 드라이브로 등록되며 기본적으로 C 드라이브로 레이블이 지정됩니다. 이 디스크의 최대 용량은 1023기가바이트(GB)입니다. 

### <a name="temporary-disk"></a>임시 디스크
각 VM에는 임시 디스크가 포함되어 있습니다. 이러한 임시 디스크는 응용 프로그램 및 프로세스에 대한 단기 저장소를 제공하며 페이지 또는 스왑 파일과 같은 데이터 저장에 사용됩니다. 임시 디스크의 데이터는 [유지 관리 이벤트](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-planned-vs-unplanned-maintenance) 또는 [VM을 다시 배포](../virtual-machines/windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 때 손실될 수 있습니다. VM의 표준 다시 부팅 동안 임시 드라이브의 데이터가 유지되어야 합니다.

이 디스크는 일시적으로 D: 드라이브로 레이블이 지정되며 pagefile.sys를 저장하는 데 사용됩니다. 이 디스크를 다른 드라이브 문자로 다시 매핑하려면 [Windows 임시 디스크의 드라이브 문자 변경](../virtual-machines/windows/change-drive-letter.md)을 참조하세요. 임시 디스크의 크기는 가상 컴퓨터의 크기에 따라 달라집니다. 자세한 내용은 [Windows 가상 컴퓨터 크기](../virtual-machines/windows/sizes.md)를 참조하세요.

Azure에서 임시 디스크를 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 가상 컴퓨터에서의 임시 드라이브 이해](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)


### <a name="data-disk"></a>데이터 디스크 
데이터 디스크는 응용 프로그램 데이터 또는 사용자가 보존해야 하는 기타 데이터를 저장하기 위해 가상 컴퓨터에 연결된 VHD입니다. 데이터 디스크는 SCSI 드라이브로 등록되며 사용자가 선택한 문자로 레이블이 지정됩니다. 각 데이터 디스크의 최대 용량은 1023GB입니다. 가상 컴퓨터의 크기에 따라 사용자가 해당 가상 컴퓨터에 연결할 수 있는 데이터의 디스크의 용량과 디스크를 호스트하기 위해 사용할 수 있는 저장소 유형이 결정됩니다.

> [!NOTE]
> 가상 컴퓨터 용량에 대한 자세한 내용은 [Windows 가상 컴퓨터에 대한 크기](../virtual-machines/windows/sizes.md)를 참조하세요.
> 

Azure는 사용자가 이미지에서 가상 컴퓨터를 만들 때 운영 체제 디스크를 만듭니다. 사용자가 데이터 디스크를 포함하는 이미지를 사용하는 경우, Azure는 가상 컴퓨터를 만들 때, 데이터 디스크도 함께 만듭니다. 그렇지 않은 경우, 가상 컴퓨터를 만든 후에 데이터 디스크를 추가하십시오.

사용자는 언제든지 가상 컴퓨터에 디스크를 **연결** 하여 해당 가상 컴퓨터에 데이터 디스크를 추가할 수 있습니다. 사용자는 자신의 저장소 계정 또는 Azure가 사용자 본인을 위해 만든 계정에 업로드하거나 복사한 VHD를 사용할 수 있습니다. 데이터 디스크를 추가하면 VHD 파일과 VM이 연결되며, 해당 VHD에서 "임대"를 설정하여 해당 VHD가 연결되어 있는 동안 저장소에서 파일이 삭제되지 않게 할 수 있습니다.


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>권장: 관리되지 않은 표준 디스크와 TRIM 사용 

관리되지 않는 표준 디스크(HDD)를 사용하는 경우 TRIM을 사용하도록 설정해야 합니다. TRIM은 디스크에서 사용되지 않는 블록을 삭제하므로 실제로 사용 중인 저장소에 대해 청구됩니다. 큰 파일을 만들고 삭제하는 경우 비용을 절감할 수 있습니다. 

TRIM 설정을 확인하도록 이 명령을 실행할 수 있습니다. Windows VM에서 명령 프롬프트를 열어 다음을 입력합니다.

```
fsutil behavior query DisableDeleteNotify
```

명령이 0을 반환하는 경우 TRIM이 올바르게 활성화됩니다. 1을 반환하는 경우, 다음 명령을 실행하여 TRIM을 사용하도록 설정합니다.

```
fsutil behavior set DisableDeleteNotify 0
```

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>다음 단계
* [디스크를 연결](../virtual-machines/windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 하여 VM에 다른 저장소를 추가합니다.
* [Windows VM 이미지를 Azure에 업로드](../virtual-machines/windows/upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 합니다.
* [Windows 임시 디스크의 드라이브 문자 변경](../virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) 합니다.


