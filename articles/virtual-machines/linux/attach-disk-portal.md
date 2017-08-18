---
title: "Linux VM에 데이터 디스크 연결 | Microsoft Docs"
description: "리소스 관리자 배포 모델을 사용하여 Azure 포털의 Linux VM에 신규 및 기존 데이터 디스크를 연결하는 방법."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: fb3b4ea612777d0ba801342dacf4b2ece46afed6
ms.contentlocale: ko-kr
ms.lasthandoff: 08/11/2017

---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Azure 포털에서 Linux VM에 데이터 디스크를 연결하는 방법
이 문서에서는 Azure 포털을 통해 신규 및 기존 디스크를 Linux 가상 컴퓨터에 연결하는 방법을 보여줍니다. 또한 [Azure Portal에서 Windows VM에 데이터 디스크를 연결](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수도 있습니다. Azure Managed Disks 또는 관리되지 않는 디스크를 사용하도록 선택할 수 있습니다. 관리되는 디스크는 Azure 플랫폼을 통해 처리되며 디스크를 저장할 위치나 준비가 필요하지 않습니다. 관리되지 않는 디스크는 저장소 계정이 필요하며 [적용되는 할당량 및 제한](../../azure-subscription-service-limits.md#storage-limits)이 있습니다. Azure Managed Disks에 대한 자세한 내용은 [Azure Managed Disks 개요](../../storage/storage-managed-disks-overview.md)를 참조하세요.

VM에 디스크를 연결하기 전에 다음 팁을 검토합니다.

* 가상 컴퓨터의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 컴퓨터의 크기](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* 프리미엄 저장소를 사용하려면 DS 시리즈 또는 GS 시리즈 가상 컴퓨터가 필요합니다. 이러한 가상 컴퓨터와 함께 프리미엄 및 표준 디스크를 모두 사용할 수 있습니다. 프리미엄 저장소는 특정 지역에서만 사용할 수 있습니다. 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* 가상 컴퓨터에 연결된 디스크는 실제로 Azure에 저장된 .vhd 파일입니다. 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD 정보](../../storage/storage-about-disks-and-vhds-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.


## <a name="find-the-virtual-machine"></a>가상 컴퓨터 찾기
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 허브 메뉴에서 **가상 컴퓨터**를 클릭합니다.
3. 목록에서 가상 컴퓨터를 선택합니다.
4. 가상 컴퓨터 블레이드의 **Essentials**에서 **디스크**를 클릭합니다.
   
    ![디스크 설정 열기](./media/attach-disk-portal/find-disk-settings.png)

[관리되는 디스크](#use-azure-managed-disks) 또는 [관리되지 않는 디스크](#use-unmanaged-disks) 중 하나를 연결하려면 다음 지침에 따라 계속합니다.

## <a name="use-azure-managed-disks"></a>Azure Managed Disks 사용

### <a name="attach-a-new-disk"></a>새 디스크 연결

1. **디스크** 블레이드에서 **+ 데이터 디스크 추가**를 클릭합니다.
2. **이름**에 대한 드롭다운 메뉴를 클릭하고 **디스크 만들기**를 선택합니다.

    ![Azure Managed Disks 만들기](./media/attach-disk-portal/create-new-md.png)

3. 관리되는 디스크에 대한 이름을 입력합니다. 기본 설정을 검토하고 필요에 따라 업데이트한 다음 **만들기**를 클릭합니다.
   
   ![디스크 설정 검토](./media/attach-disk-portal/create-new-md-settings.png)

4. **저장**을 클릭하여 관리되는 디스크를 만들고 VM 구성을 업데이트합니다.

   ![새 Azure Managed Disk 저장](./media/attach-disk-portal/confirm-create-new-md.png)

5. Azure가 디스크를 만들고 가상 컴퓨터에 연결하면 가상 컴퓨터의 디스크 설정의 **데이터 디스크**아래에 새 디스크가 나열됩니다. 관리되는 디스크는 최상위 수준 리소스이므로 디스크는 리소스 그룹의 루트에 나타납니다.

   ![리소스 그룹의 Azure Managed Disk](./media/attach-disk-portal/view-md-resource-group.png)

### <a name="attach-an-existing-disk"></a>기존 디스크 연결
1. **디스크** 블레이드에서 **+ 데이터 디스크 추가**를 클릭합니다.
2. **이름**에 대한 드롭다운 메뉴를 클릭하여 Azure 구독에 액세스할 수 있는 기존 관리되는 디스크의 목록을 봅니다. 연결할 관리되는 디스크를 선택합니다.

   ![기존 Azure Managed Disk 연결](./media/attach-disk-portal/select-existing-md.png)

3. **저장**을 클릭하여 기존 관리되는 디스크를 연결하고 VM 구성을 업데이트합니다.
   
   ![Azure Managed Disk 업데이트 저장](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Azure가 디스크를 가상 컴퓨터에 연결한 후 가상 컴퓨터의 디스크 설정의 **데이터 디스크**아래에 해당 디스크가 나열됩니다.

## <a name="use-unmanaged-disks"></a>관리되지 않는 디스크 사용

### <a name="attach-a-new-disk"></a>새 디스크 연결

1. **디스크** 블레이드에서 **+ 데이터 디스크 추가**를 클릭합니다.
2. 기본 설정을 검토하고 필요에 따라 업데이트한 다음 **확인**을 클릭합니다.
   
   ![디스크 설정 검토](./media/attach-disk-portal/attach-new.png)
3. Azure가 디스크를 만들고 가상 컴퓨터에 연결하면 가상 컴퓨터의 디스크 설정의 **데이터 디스크**아래에 새 디스크가 나열됩니다.

### <a name="attach-an-existing-disk"></a>기존 디스크 연결
1. **디스크** 블레이드에서 **+ 데이터 디스크 추가**를 클릭합니다.
2. **기존 디스크 연결**에서 **VHD 파일**을 클릭합니다.
   
   ![기존 디스크 연결](./media/attach-disk-portal/attach-existing.png)
3. **저장소 계정**에서 계정 및 .vhd 파일을 보관하는 컨테이너를 선택합니다.
   
   ![VHD 위치 찾기](./media/attach-disk-portal/find-storage-container.png)
4. .vhd 파일을 업로드합니다.
5. **기존 디스크 연결**에서 방금 선택한 파일이 **VHD 파일** 아래에 나열됩니다. **확인**을 클릭합니다.
6. Azure가 디스크를 가상 컴퓨터에 연결한 후 가상 컴퓨터의 디스크 설정의 **데이터 디스크**아래에 해당 디스크가 나열됩니다.


## <a name="next-steps"></a>다음 단계
디스크를 추가한 후 해당 디스크를 사용할 수 있도록 준비해야 합니다. 자세한 내용은 [방법: Linux에서 새 데이터 디스크 초기화](add-disk.md)를 참조하세요.

