---
title: Azure DevTest Labs에서 Windows 가상 머신에 연결
description: 랩에서 Windows 가상 머신에 연결 하는 방법 알아보기 (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86540723"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>랩에서 Windows VM에 연결 (Azure DevTest Labs)
이 문서에서는 랩에서 Windows VM에 연결 하는 방법을 보여 줍니다. 

## <a name="connect-to-a-windows-vm"></a>Windows VM에 연결
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 창에서 **DevTest Labs**를 검색 하 고 선택 합니다. 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="DevTest Labs를 검색 하 고 선택 합니다.":::    
1. 랩 목록에서 **랩을**선택 합니다.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="DevTest Labs를 검색 하 고 선택 합니다.":::            
1. 랩의 홈 페이지에 있는 **내 가상 컴퓨터** 목록에서 Windows VM을 선택 합니다. 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="DevTest Labs를 검색 하 고 선택 합니다.":::                
1. VM에 대 한 **가상 머신** 페이지의 도구 모음에서 **연결** 을 선택 합니다. VM이 중지 된 경우 먼저 **시작** 을 선택 하 여 vm을 시작 합니다.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="DevTest Labs를 검색 하 고 선택 합니다.":::                    
1. Edge 브라우저를 사용 하는 경우 **다운로드 한 RDP 파일** 에 대 한 링크가 브라우저 아래쪽에 표시 됩니다. 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="DevTest Labs를 검색 하 고 선택 합니다.":::                        
1. RDP 파일을 열고 VM을 만들 때 입력 한 VM 자격 증명을 입력 합니다. 이제 Windows VM에 연결 해야 합니다. 

## <a name="next-steps"></a>다음 단계
[방법: Linux VM에 연결](connect-linux-virtual-machine.md)
