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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: c01106bf7288e777e601c06558b1408960f4e49b
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539436"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>가상 머신 풀 설정 및 관리 
이 문서에서는 다음 작업을 수행 하는 방법을 보여 줍니다.

- 랩에서 Vm (가상 머신)의 수를 늘립니다.
- 모든 Vm 또는 선택한 Vm 시작 
- Vm 다시 설정

## <a name="update-the-lab-capacity"></a>랩 용량 업데이트
랩 용량 (랩에서 가상 머신 수)을 늘리거나 줄이려면 다음 단계를 수행 합니다.

1. **가상 컴퓨터 풀** 페이지에서 **Lab capacity: &lt;number&gt; machines**를 선택 합니다.
2. 랩에서 원하는 새 **vm 수** 를 입력 합니다. 이 수는 랩에 등록 된 사용자 수보다 크거나 같아야 합니다. 
3. 그런 다음 **저장**을 선택합니다. 

    ![모두 시작 단추](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. 용량을 늘리면 VM이 생성 되는 VM을 확인할 수 있습니다. 새 VM이 목록에 표시 되지 않으면 페이지를 새로 고칩니다. 

    ![VM을 만드는 중임](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>VM 시작

### <a name="start-ot-stop-all-vms"></a>모든 Vm 중지 시작
1. **가상 컴퓨터 풀** 페이지로 전환 합니다. 
2. 도구 모음에서 **모두 시작** 을 선택 합니다. 

    ![모두 시작 단추](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. 모든 Vm이 시작 되 면 도구 모음에서 **모두 중지** 단추를 선택 하 여 모든 vm을 중지할 수 있습니다. 

    ![모두 중지 단추](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>선택한 Vm 시작
선택 된 Vm을 시작 하는 방법에는 두 가지가 있습니다 (하나 이상). 첫 번째 방법은 목록에서 VM을 선택 하 고 도구 모음에서 **시작** 을 선택 하는 것입니다. 

두 번째 방법은 목록에서 하나 이상의 Vm을 선택 하 고 **상태** 열에서 단추를 설정/해제 하는 것입니다. 

![선택한 Vm 시작](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

마찬가지로 **상태** 열의 단추를 전환 하거나 도구 모음에서 **중지** 를 선택 하 여 하나 이상의 vm을 중지할 수 있습니다. 

## <a name="reset-vms"></a>Vm 다시 설정
하나 이상의 Vm을 다시 설정 하려면 목록에서 Vm을 선택한 다음 도구 모음에서 **다시 설정** 을 선택 합니다. 

![선택한 Vm 다시 설정](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

**가상 컴퓨터 다시 설정** 대화 상자에서 **다시 설정**을 선택 합니다. 

![VM 다시 설정 대화 상자](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Vm에 대 한 암호 설정
랩 소유자 (교사)는 랩 (랩 만들기 마법사)을 만들 때 또는 **템플릿** 페이지에서 랩을 만든 후에 vm에 대 한 암호를 설정/다시 설정할 수 있습니다. 

### <a name="set-password-at-the-time-of-lab-creation"></a>랩 생성 시 암호 설정
랩 소유자 (교사)는 랩 만들기 마법사의 **가상 컴퓨터 자격 증명** 페이지에서 랩의 vm에 대 한 암호를 설정할 수 있습니다.

![새 랩 창](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

이 페이지에서 **모든 가상 컴퓨터에 대해 동일한 암호 사용** 옵션을 사용 하거나 사용 하지 않도록 설정 하면 교사는 랩의 모든 vm에 대해 동일한 암호를 사용 하도록 선택 하거나 학습자가 해당 vm에 대 한 암호를 설정할 수 있습니다. 기본적으로이 설정은 Ubuntu를 제외한 모든 Windows 및 Linux 운영 체제 이미지에 대해 사용 하도록 설정 됩니다. 이 설정을 사용 하지 않도록 설정 하면 학생 들이 처음으로 VM에 연결 하려고 할 때 암호를 설정 하 라는 메시지가 표시 됩니다. 

### <a name="reset-password-later"></a>나중에 암호 다시 설정

1. 랩의 **템플릿** 페이지에 있는 도구 모음에서 **암호 재설정** 을 선택 합니다. 
1. **암호 재설정** 대화 상자에서 암호를 입력 하 고 **암호 재설정**을 선택 합니다.
    
    ![암호 설정 대화 상자](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>학생 Vm에 연결
다음 조건이 충족 되 면 랩 작성자 (강사/교수)에서 학생 VM에 연결할 수 있습니다. 

- 랩을 만들 때 **모든 가상 머신에 동일한 암호 사용** 옵션을 선택 했습니다.
- VM이 실행 되 고 있습니다. 

 학생 VM에 연결 하려면 목록에서 VM을 마우스로 가리키고 컴퓨터 단추를 선택 합니다.  

![학생 VM에 연결 단추](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> 교수에서 VM을 시작 하 고 연결할 때 학생 할당량은 영향을 받지 않습니다. 

## <a name="next-steps"></a>다음 단계
랩 소유자가 구성할 수 있는 다른 학생 사용 옵션에 대해 알아보려면 [학생 사용 구성](how-to-configure-student-usage.md)문서를 참조 하세요.

학생이 자신의 Vm에 대 한 암호를 다시 설정 하는 방법에 대 한 자세한 내용은 [교실 랩에서 가상 컴퓨터의 암호 설정 또는 다시 설정 (학생용)](how-to-set-virtual-machine-passwords-student.md)을 참조 하세요.