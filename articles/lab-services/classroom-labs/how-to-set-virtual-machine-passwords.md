---
title: Azure Lab Services에서 Vm에 대 한 암호를 설정 합니다. | Microsoft Docs
description: 설정 하 고 Azure Lab Services의 클래스 룸 랩의 virtual machines (Vm)에 대 한 암호를 재설정 하는 방법에 알아봅니다.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144092"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>또는 클래스 룸 랩의 가상 머신에 대 한 암호를 다시 설정
이 문서에서는 클래스 룸 랩에 Vm에 액세스 하기 위한 여러 가지 설정과 resettings 암호를 제공 합니다. 

## <a name="lab-owners-teachers"></a>랩 소유자 (교사)
랩 소유자 (교사) 수 설정/재설정 암호 Vm에 대 한 랩 (랩 만들기 마법사)를 작성 하거나 (대시보드)에서 랩을 만든 후에 합니다. 

### <a name="set-password-at-the-time-of-lab-creation"></a>랩 생성 시 암호를 설정 합니다.
랩 소유자 (교사)를 사용 하는 암호를 설정할 수 Vm에 대 한 랩에서에 **자격 증명 설정** 랩 만들기 마법사의 페이지입니다.

![자격 증명 설정](../media/tutorial-setup-classroom-lab/set-credentials.png)

활성화/비활성화 하 여는 **모든 가상 머신에 대해 동일한 암호를 사용 하 여** 옵션이이 페이지에서는 교사는 랩에서 모든 Vm에 대해 동일한 암호를 사용 하거나 해당 Vm에 대 한 암호를 설정 하는 학생을 허용 하도록 선택할 수 있습니다. 기본적으로 Ubuntu 제외한 모든 Windows 및 Linux 운영 체제 이미지에 대 한이 설정을 사용 합니다. 이 설정을 비활성화 되 면 학생 처음으로 VM에 연결 하려고 하면 암호를 설정 하려면 나타납니다. 

랩 소유자이 암호를 재설정할 수이 필요한 경우에 **구성 템플릿** 랩 만들기 마법사의 페이지입니다. 

![완료된 템플릿 페이지 구성](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

랩 소유자 대시보드에서 랩을 만든 후 암호를 다시 설정할 수도 있습니다. 

### <a name="reset-password-on-the-dashboard"></a>대시보드에서 암호 재설정

1. 랩 타일에서 오버플로 메뉴 (세로 점 세 개)를 선택 하 고 선택 **암호 재설정**합니다. 

    ![홈 페이지의 메뉴 암호 다시 설정](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. 에 **암호 설정** 대화 상자에서 암호를 입력 하 고 선택 **세트 암호**합니다.
    
    ![설정 암호 대화 상자](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>랩 사용자 (학생)
랩을 만드는 동시에 랩 소유자 설정 하거나 해제할 수는 **동일한 암호를 사용 하 여 모든 가상 머신에 대해**합니다. 이 옵션을 사용 하는 경우 학생에 게 암호를 재설정할 수 없습니다. 랩에서 모든 Vm은 교사에 의해 설정 된 동일한 암호를 해야 합니다. 

이 옵션을 사용할 경우 사용자가 처음으로 VM에 연결 하려고 할 때 암호를 설정 해야 합니다. 사용자 (학생) 선택 하는 경우는 **Connect** 랩 타일에서 단추를 **내 가상 머신** 페이지에서 사용자에 게 VM에 대 한 암호를 설정 하려면 다음 대화 상자가 표시: 

![학생에 대 한 암호 재설정](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

학생도 암호를 설정할 수 오버플로 메뉴를 클릭 하 여 (**세로 점 세 개**) 랩 타일을 선택 하 고 **암호 재설정**합니다. 

## <a name="next-steps"></a>다음 단계
옵션에 대해 알아보려면 다른 학생 사용 하면 (랩 소유자로) 수 구성, 다음 문서를 참조 하세요. [학생 사용 구성](how-to-configure-student-usage.md)합니다.
