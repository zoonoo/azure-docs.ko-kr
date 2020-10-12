---
title: Azure에서 Linux VHD 다운로드
description: Azure CLI 및 Azure Portal을 사용하여 Linux VHD를 다운로드합니다.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 897cae53e589f4058e5499c0e6e941d4f1d9bb2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87761065"
---
# <a name="download-a-linux-vhd-from-azure"></a>Azure에서 Linux VHD 다운로드

이 문서에서는 Azure Portal를 사용 하 여 Azure에서 Linux VHD (가상 하드 디스크) 파일을 다운로드 하는 방법에 대해 알아봅니다. 

## <a name="stop-the-vm"></a>VM을 중지합니다.

실행 중인 VM에 연결된 경우 Azure에서 VHD는 다운로드할 수 없습니다. VHD를 다운로드 하려면 VM을 중지 해야 합니다. 

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2.  왼쪽 메뉴에서 **Virtual Machines**를 선택 합니다.
3.  목록에서 VM을 선택합니다.
4.  VM에 대 한 페이지에서 **중지**를 선택 합니다.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="VM을 중지 하는 메뉴 단추를 표시 합니다.":::

## <a name="generate-sas-url"></a>SAS URL 생성

VHD 파일을 다운로드하려면 [SAS(공유 액세스 서명)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL을 생성해야 합니다. URL이 생성될 때 만료 시간이 URL에 할당됩니다.

1. VM에 대 한 페이지의 메뉴에서 **디스크**를 선택 합니다.
2. VM에 대 한 운영 체제 디스크를 선택 하 고 **디스크 내보내기**를 선택 합니다.
1. 필요한 경우 URL의 값을 업데이트 하 여 다운로드를 완료 하는 데 충분 한 시간을 제공 합니다 **(초)** . 기본값은 3600 초 (1 시간)입니다.
3. **URL 생성**을 선택 합니다.
 
      
## <a name="download-vhd"></a>VHD 다운로드

1.  생성 된 URL에서 **VHD 파일 다운로드**를 선택 합니다.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="VM을 중지 하는 메뉴 단추를 표시 합니다.":::

2.  다운로드를 시작 하려면 브라우저에서 **저장** 을 선택 해야 할 수도 있습니다. VHD 파일에 대한 기본 이름은 *abcd*입니다.

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용하여 사용자 지정 디스크에서 Linux VM 업로드 및 만들기](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 방법을 알아봅니다. 
- [Azure CLI를 사용하여 Azure 디스크를 관리합니다](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
