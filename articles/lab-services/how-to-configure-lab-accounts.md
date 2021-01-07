---
title: Azure Lab Services에서 VM의 자동 종료 구성
description: 이 문서에서는 랩 계정에서 VM의 자동 종료를 구성하는 방법을 설명합니다.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: c0a147a81aaed88313a1b9aa4b0754d9a3badcb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650037"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>랩 계정에 대 한 Vm의 자동 종료 구성

여러 자동 종료 비용 제어 기능을 사용 하도록 설정 하 여 가상 컴퓨터를 적극적으로 사용 하지 않을 때 추가 비용을 사전에 방지할 수 있습니다. 다음 세 가지 자동 종료 및 연결 끊기 기능의 조합은 사용자가 실수로 가상 컴퓨터를 실행 하는 대부분의 경우를 포착 합니다.
 
- OS가 유휴 상태로 하다 고 판단 가상 머신에서 사용자를 자동으로 연결을 끊습니다.
- 사용자가 연결을 끊으면 자동으로 가상 컴퓨터를 종료 합니다.
- 시작 되었지만 사용자가 연결 하지 않는 가상 컴퓨터를 자동으로 종료 합니다.

[자동 종료 설정을 사용 하 여 비용 제어 최대화](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) 섹션에서 자동 종료 기능에 대 한 자세한 내용을 검토 합니다.

## <a name="enable-automatic-shutdown"></a>자동 종료 사용

1. [Azure Portal](https://portal.azure.com/) 에서 **랩 계정** 페이지로 이동 합니다.
1. 왼쪽 메뉴에서 **랩 설정** 을 선택 합니다.
1. 시나리오에 적합 한 자동 종료 설정을 선택 합니다.  

    > [!div class="mx-imgBorder"]
    > ![랩 계정에서 자동 종료 설정](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    설정은 랩 계정에 생성 된 모든 랩에 적용 됩니다. 랩 작성자(교육자)는 랩 수준에서 이 설정을 재정의할 수 있습니다. 랩 계정에서 이 설정을 변경하면 변경 후에 생성된 랩에만 영향을 줍니다.

    설정을 사용 하지 않으려면이 페이지에서 확인란의 선택을 취소 합니다. 

## <a name="next-steps"></a>다음 단계

랩 소유자가 랩 수준에서이 설정을 구성 하거나 재정의 하는 방법에 대 한 자세한 내용은 [랩에 대 한 vm의 자동 종료 구성](how-to-enable-shutdown-disconnect.md) 을 참조 하세요.
