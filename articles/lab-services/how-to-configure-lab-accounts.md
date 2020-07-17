---
title: Azure Lab Services에서 VM의 자동 종료 구성
description: 이 문서에서는 랩 계정에서 VM의 자동 종료를 구성하는 방법을 설명합니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 783e3b310b3ad06f637453f0e1b11f6a78beec3a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445817"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>랩 계정의 연결 해제 설정에서 VM의 자동 종료 구성
원격 데스크톱 연결이 끊긴 후 Windows 랩 VM(템플릿 또는 학생)의 자동 종료를 사용하거나 사용하지 않도록 설정할 수 있습니다. Lab Services가 자동으로 종료되기 전에 사용자가 다시 연결될 때까지 대기해야 하는 기간도 지정할 수 있습니다.

## <a name="enable-automatic-shutdown"></a>자동 종료 사용

1. **랩 계정** 페이지의 왼쪽 메뉴에서 **랩 설정**을 선택합니다.
2. **사용자가 연결을 끊을 때 가상 머신 자동 종료** 옵션을 선택합니다.
3. VM을 자동으로 종료하기 전에 Lab Services에서 사용자가 다시 연결될 때까지 대기해야 하는 기간을 지정합니다.

    ![랩 계정에서 자동 종료 설정](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    이 설정은 랩 계정에 생성된 모든 랩에 적용됩니다. 랩 작성자(교육자)는 랩 수준에서 이 설정을 재정의할 수 있습니다. 랩 계정에서 이 설정을 변경하면 변경 후에 생성된 랩에만 영향을 줍니다.

    이 설정을 사용하지 않으려면 이 페이지에서 **사용자가 연결을 끊을 때 가상 머신 자동 종료** 옵션의 확인란을 선택 취소합니다. 

## <a name="next-steps"></a>다음 단계
랩 소유자가 랩 수준에서 이 설정을 구성하거나 재정의하는 방법에 대해 알아보려면 [이 문서](how-to-enable-shutdown-disconnect.md)를 참조하세요.
