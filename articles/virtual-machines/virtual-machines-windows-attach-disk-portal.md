---
title: "Windows VM에 데이터 디스크 연결 | Microsoft Docs"
description: "리소스 관리자 배포 모델을 사용하여 Azure 포털의 Windows VM에 신규 및 기존 데이터 디스크를 연결하는 방법."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/02/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: e7ec8715087abaf5c96a6327d4bcde0ad3fd547a


---
# <a name="how-to-attach-a-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Azure 포털에서 Windows VM에 데이터 디스크를 연결하는 방법
이 문서에서는 Azure 포털을 통해 신규 및 기존 디스크를 Windows 가상 컴퓨터에 연결하는 방법을 보여 줍니다. 또한 [Azure 포털에서 Linux VM에 데이터 디스크를 연결](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)할 수도 있습니다. 이 작업을 수행 하기 전에 다음 팁을 검토하세요.

* 가상 컴퓨터의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
* 새 디스크의 경우 Azure가 디스크를 연결할 때 생성하므로 먼저 생성하지 않아도 됩니다.
* 기존 디스크의 경우 Azure 저장소 계정에서 .vhd 파일을 사용할 수 있어야 합니다. 다른 가상 컴퓨터에 연결되지 않은 경우 이미 있는 .vhd 파일을 사용하거나 고유의 .vhd 파일을 저장소 계정에 업로드할 수 있습니다.

또한 [Powershell을 사용하여 데이터 디스크를 연결](virtual-machines-windows-ps-manage.md#add-a-data-disk-to-a-virtual-machine)할 수 있습니다.

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="a-idinitializeinwsahow-to-initialize-a-new-data-disk-in-windows-server"></a><a id="initializeinWS"></a>방법: Windows Server에서 새 데이터 디스크 초기화
1. 가상 컴퓨터에 연결합니다. 지침은 [Windows를 실행하는 Azure 가상 컴퓨터에 연결하고 로그온하는 방법](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.
2. 가상 컴퓨터에 로그온한 후 **Server Manager**를 엽니다. 왼쪽 창에서 **파일 및 저장소 서비스**를 선택합니다.
   
    ![서버 관리자 열기](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. 메뉴를 확장하고 **디스크**를 선택합니다.
4. **디스크** 섹션은 디스크를 나열합니다. 대부분의 경우에서 디스크 0, 디스크 1 및 디스크 2가 됩니다. 디스크 0은 운영 체제 디스크이고, 디스크 1은 임시 리소스 디스크이며, 디스크 2는 방금 가상 컴퓨터에 연결한 데이터 디스크입니다. 새 데이터 디스크의 파티션은 **알 수 없음**으로 나열됩니다. 디스크를 마우스 오른쪽 단추로 클릭한 다음 **초기화**를 선택합니다.
5. 디스크가 초기화 될 때 모든 데이터를 삭제된다고 알려 줍니다. **예** 를 클릭하여 경고를 확인하고 디스크를 초기화합니다. 완료되면 파티션이 **GPT**로 나열됩니다. 디스크를 다시 마우스 오른쪽 단추로 클릭하고 **새 볼륨**을 선택합니다.
6. 기본값을 사용하여 마법사를 완료합니다. 마법사를 완료하면 새 볼륨이 **볼륨** 섹션에 나열됩니다. 이제 디스크가 온라인 상태이며 데이터를 저장할 준비가 완료되었습니다.

    ![볼륨 초기화됨](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [!NOTE]
> VM의 크기로 연결할 수 있는 디스크 개수가 결정됩니다. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
> 
> 

## <a name="next-steps"></a>다음 단계
응용 프로그램이 데이터를 저장하는 데 D: 드라이브를 사용해야 하면 [Windows 임시 디스크의 드라이브 문자를 변경](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)할 수 있습니다.




<!--HONumber=Nov16_HO3-->


