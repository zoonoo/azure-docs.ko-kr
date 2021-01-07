---
title: Azure DevTest Labs에서 Linux 가상 머신에 연결
description: 랩에서 Linux 가상 머신에 연결 하는 방법에 대해 알아봅니다 (Azure DevTest Labs).
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86531649"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>랩에서 Linux VM에 연결 (Azure DevTest Labs)
이 문서에서는 랩에서 Linux VM에 연결 하는 방법을 보여 줍니다. 

## <a name="connect-to-a-linux-vm"></a>Linux VM에 연결
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 창에서 **DevTest Labs**를 검색 하 고 선택 합니다. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="DevTest Labs를 검색 하 고 선택 합니다.":::    
1. 랩 목록에서 **랩을**선택 합니다.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="DevTest Labs를 검색 하 고 선택 합니다.":::            
1. 랩의 홈 페이지에 있는 **내 가상 컴퓨터** 목록에서 Linux VM을 선택 합니다. 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="DevTest Labs를 검색 하 고 선택 합니다.":::        
5. **개요** 페이지에서 VM의 FQDN (정규화 된 도메인 이름) 또는 IP 주소를 볼 수 있습니다. 다음 그림에 표시 된 것 처럼 포트를 볼 수도 있습니다.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="DevTest Labs를 검색 하 고 선택 합니다.":::    

    VM이 시작 된 경우에도 **연결** 단추가 회색으로 표시 됩니다. 이것은 의도적입니다.
6.  SSH를 사용 하 여 Linux VM에 연결 합니다. 다음 예제에서는 `mydtl07172452621450000.eastus.cloudapp.azure.com` 및 포트의 사용자 이름과 함께 FQDN을 사용 하 여 VM에 연결 합니다 `vmuser` `51637` . 사용자에 대 한 암호를 입력 하 여 VM에 연결 합니다. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    [Putty](https://www.putty.org/) 또는 다른 SSH 클라이언트와 같은 도구를 사용 하 여 VM에 연결할 수 있습니다. 

    SSH를 사용 하 여 연결한 후에는 데스크톱 환경 ([xfce](https://www.xfce.org)) 및 원격 데스크톱 ([xrdp](http://xrdp.org))을 설치 하 고 구성할 수 있습니다.  자세한 내용은 [Azure에서 LINUX VM에 연결 하려면 원격 데스크톱 설치 및 구성](../virtual-machines/linux/use-remote-desktop.md)을 참조 하세요. 

## <a name="next-steps"></a>다음 단계
[방법: Windows VM에 연결](connect-windows-virtual-machine.md)
