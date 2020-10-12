---
title: Azure DevTest Labs의 랩에서 VM 다시 배포 | Microsoft Docs
description: Azure DevTest Labs에서 가상 머신을 다시 배포(Azure 노드 간에 이동)하는 방법을 설명합니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a38b112165b893d877733b967c21bb62b20ca2f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530321"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩에서 VM 다시 배포
원격 데스크톱 연결을 통해 랩에서 VM (가상 머신)에 연결할 수 없는 경우 VM을 다시 배포 하 고 다시 연결 해 보세요. VM을 다시 배포하면 DevTest Labs가 VM을 실행 중인 노드에서 Azure 인프라 내의 새 노드로 이동합니다. 그런 다음 모든 구성 옵션 및 관련 리소스는 그대로 유지하고 VM을 시작합니다. 이 기능을 사용하면 랩의 Windows 기반 VM에 대한 애플리케이션 액세스 또는 원격 데스크톱 연결 관련 문제를 해결하는 시간을 줄일 수 있습니다. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>랩에서 VM을 다시 배포하는 단계 
Azure DevTest Labs의 랩에서 VM을 다시 배포하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
3. 랩 목록에서 다시 배포할 VM이 포함된 랩을 선택합니다.  
4. 왼쪽 패널에서 **내 Virtual Machine**을 선택합니다. 
5. VM 목록에서 원하는 VM을 선택합니다.
6. VM의 가상 머신 페이지에서 왼쪽 메뉴의 **작업** 아래에 있는 **다시 배포**를 선택합니다.

    ![화면 캡처는 재배포가 선택 된 가상 머신 페이지를 표시 합니다.](media/devtest-lab-redeploy-vm/redeploy.png)
7. 페이지의 정보를 확인하고 **다시 배포** 단추를 선택합니다. 9. **알림** 창에서 다시 배포 작업의 상태를 확인합니다.

    ![다시 배포 상태](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>다음 단계
Azure DevTest Labs에서 VM의 크기를 조정하는 방법에 대해 알아보려면 [VM 크기 조정](devtest-lab-resize-vm.md)을 참조하세요.


