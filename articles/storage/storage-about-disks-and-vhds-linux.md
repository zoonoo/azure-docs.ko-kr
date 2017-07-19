---
title: "Microsoft Azure Linux VM에 대한 디스크 및 VHD 정보 | Microsoft Docs"
description: "Azure의 Linux 가상 컴퓨터용 디스크 및 VHD의 기본 사항에 대해 알아봅니다."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 7be8dd52-98f7-4187-9b78-55197915bc9b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 2da32c8f796ff872494e0ed729324511440c943d
ms.contentlocale: ko-kr
ms.lasthandoff: 06/16/2017


---
# <a name="about-disks-and-vhds-for-azure-linux-vms"></a>Azure Linux VM용 디스크 및 VHD 정보
다른 컴퓨터와 마찬가지로, Azure에서 가상 컴퓨터는 운영 체제, 응용 프로그램 및 데이터를 저장하는 장소로 디스크를 사용합니다. 모든 Azure 가상 컴퓨터는 적어도 2개의 디스크(Linux 운영 체제 디스크 및 임시 디스크)를 갖습니다. 운영 체제 디스크는 이미지에서 만들어지며, 운영 체제 디스크 및 이미지 모두는 실제로 Azure 저장소 계정에 저장된 가상 하드 디스크(VHD)입니다. 가상 컴퓨터에도 데이터 디스크가 있을 수 있으며 이러한 디스크도 VHD로 저장됩니다. 

이 문서에서는 디스크의 여러 가지 사용법에 대해 설명한 후 사용자가 만들고 사용할 수 있는 다양한 디스크 형식에 대해 설명합니다. 이 문서는 [Windows 가상 컴퓨터](storage-about-disks-and-vhds-windows.md)에도 적용됩니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>VM에서 사용되는 디스크

VM에서 디스크를 사용하는 방법에 대해 살펴보겠습니다.

## <a name="operating-system-disk"></a>운영 체제 디스크
모든 가상 컴퓨터는 하나의 연결된 운영 체제 디스크를 갖습니다. 이 디스크는 SATA 드라이브로 등록되며 기본적으로 /dev/sda라는 레이블이 붙습니다. 이 디스크의 최대 용량은 2048기가바이트(GB)입니다. 

## <a name="temporary-disk"></a>임시 디스크
각 VM에는 임시 디스크가 포함되어 있습니다. 이러한 임시 디스크는 응용 프로그램 및 프로세스에 대한 단기 저장소를 제공하며 페이지 또는 스왑 파일과 같은 데이터 저장에 사용됩니다. 임시 디스크의 데이터는 [유지 관리 이벤트](../virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-planned-vs-unplanned-maintenance) 또는 [VM을 다시 배포](../virtual-machines/linux/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)할 때 손실될 수 있습니다. VM의 표준 다시 부팅 동안 임시 드라이브의 데이터가 유지되어야 합니다.

Linux 가상 컴퓨터에서 디스크는 일반적으로 **/dev/sdb**이며, Azure Linux 에이전트에 의해 **/mnt**로 포맷되고 마운트됩니다. 임시 디스크의 크기는 가상 컴퓨터의 크기에 따라 달라집니다. 자세한 내용은 [Linux 가상 컴퓨터의 크기](../virtual-machines/linux/sizes.md)를 참조하세요.

Azure에서 임시 디스크를 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 가상 컴퓨터에서의 임시 드라이브 이해](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>데이터 디스크 
데이터 디스크는 응용 프로그램 데이터 또는 사용자가 보존해야 하는 기타 데이터를 저장하기 위해 가상 컴퓨터에 연결된 VHD입니다. 데이터 디스크는 SCSI 드라이브로 등록되며 사용자가 선택한 문자로 레이블이 지정됩니다. 각 데이터 디스크의 최대 용량은 4095GB입니다. 가상 컴퓨터의 크기에 따라 사용자가 해당 가상 컴퓨터에 연결할 수 있는 데이터의 디스크의 용량과 디스크를 호스트하기 위해 사용할 수 있는 저장소 유형이 결정됩니다.

> [!NOTE]
> 가상 컴퓨터 용량에 대한 자세한 내용은 [Linux 가상 컴퓨터에 대한 크기](../virtual-machines/linux/sizes.md)를 참조하세요.
> 

Azure는 사용자가 이미지에서 가상 컴퓨터를 만들 때 운영 체제 디스크를 만듭니다. 사용자가 데이터 디스크를 포함하는 이미지를 사용하는 경우, Azure는 가상 컴퓨터를 만들 때, 데이터 디스크도 함께 만듭니다. 그렇지 않은 경우, 가상 컴퓨터를 만든 후에 데이터 디스크를 추가하십시오.

사용자는 언제든지 가상 컴퓨터에 디스크를 **연결** 하여 해당 가상 컴퓨터에 데이터 디스크를 추가할 수 있습니다. 사용자는 자신의 저장소 계정 또는 Azure가 사용자 본인을 위해 만든 계정에 업로드하거나 복사한 VHD를 사용할 수 있습니다. 데이터 디스크를 추가하면 VHD 파일과 VM이 연결되며, 해당 VHD에서 "임대"를 설정하여 해당 VHD가 연결되어 있는 동안 저장소에서 파일이 삭제되지 않게 할 수 있습니다.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>문제 해결
[!INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>다음 단계
* [디스크를 연결](../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 하여 VM에 다른 저장소를 추가합니다.
* 중복성에 대해 [소프트웨어 RAID를 구성](../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)합니다.
* [Linux 가상 컴퓨터를 캡처](../virtual-machines/linux/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)하여 추가 VM을 신속하게 배포할 수 있습니다.


