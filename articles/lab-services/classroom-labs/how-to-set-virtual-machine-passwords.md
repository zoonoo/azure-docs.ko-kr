---
title: Azure Lab Services에서 Vm에 대 한 암호 설정 Microsoft Docs
description: Azure Lab Services의 교실 랩에서 가상 머신 (Vm)에 대 한 암호를 설정 하 고 재설정 하는 방법을 알아봅니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: a4cb2abec429a790f493f95d3d16b2ff7b3eb445
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645023"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-instructor"></a>교실 랩의 가상 컴퓨터에 대 한 암호 설정 또는 다시 설정 (강사)
랩 소유자 (교사)는 랩 (랩 만들기 마법사)을 만들 때 또는 랩 (대시보드)을 만든 후에 Vm에 대 한 암호를 설정/다시 설정할 수 있습니다. 

## <a name="set-password-at-the-time-of-lab-creation"></a>랩 생성 시 암호 설정
랩 소유자 (교사)는 랩 만들기 마법사의 **자격 증명 설정** 페이지에서 랩의 vm에 대 한 암호를 설정할 수 있습니다.

![자격 증명 설정](../media/tutorial-setup-classroom-lab/set-credentials.png)

이 페이지에서 **모든 가상 컴퓨터에 대해 동일한 암호 사용** 옵션을 사용 하거나 사용 하지 않도록 설정 하면 교사는 랩의 모든 vm에 대해 동일한 암호를 사용 하도록 선택 하거나 학습자가 해당 vm에 대 한 암호를 설정할 수 있습니다. 기본적으로이 설정은 Ubuntu를 제외한 모든 Windows 및 Linux 운영 체제 이미지에 대해 사용 하도록 설정 됩니다. 이 설정을 사용 하지 않도록 설정 하면 학생 들이 처음으로 VM에 연결 하려고 할 때 암호를 설정 하 라는 메시지가 표시 됩니다. 

랩 소유자는 랩 만들기 마법사의 **템플릿 구성** 페이지에서 필요한 경우이 암호를 다시 설정할 수 있습니다. 

![완료된 템플릿 페이지 구성](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

랩 소유자는 랩이 생성 된 후 대시보드에서 암호를 다시 설정할 수도 있습니다. 

## <a name="reset-password-on-the-dashboard"></a>대시보드의 암호 다시 설정

1. 랩 타일에서 오버플로 메뉴 (세로 3 점)를 선택 하 고 **암호 재설정**을 선택 합니다. 

    ![홈 페이지에서 암호 다시 설정 메뉴](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. **암호 설정** 대화 상자에서 암호를 입력 하 고 **암호 설정**을 선택 합니다.
    
    ![암호 설정 대화 상자](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>다음 단계
랩 소유자가 구성할 수 있는 다른 학생 사용 옵션에 대해 알아보려면 다음 문서를 참조 하세요. [학생 사용을 구성](how-to-configure-student-usage.md)합니다.

학생이 자신의 Vm에 대 한 암호를 다시 설정 하는 방법에 대 한 자세한 내용은 [교실 랩에서 가상 컴퓨터의 암호 설정 또는 다시 설정 (학생용)](how-to-set-virtual-machine-passwords-student.md)을 참조 하세요.