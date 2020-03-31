---
title: Azure 랩 서비스에서 VM에 대한 암호 설정 | 마이크로 소프트 문서
description: Azure Lab 서비스의 강의실 랩에서 가상 컴퓨터(VM)에 대한 암호를 설정하고 재설정하는 방법을 알아봅니다.
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
ms.openlocfilehash: 0ff464936025a20cb6925adc7ef6eb44c2fe1f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933817"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>가상 머신 풀 설정 및 관리 
이 문서에서는 다음 작업을 수행하는 방법을 보여 주며 다음과 같은 작업을 수행합니다.

- 랩의 VM(가상 컴퓨터 수 증가)
- 모든 VM 또는 선택한 VM 시작 
- VM 재설정

## <a name="update-the-lab-capacity"></a>랩 용량 업데이트
랩 용량(랩의 가상 컴퓨터 수)을 늘리거나 줄이려면 다음 단계를 수행합니다.

1. 가상 **컴퓨터 풀** 페이지에서 **랩 &lt;용량:&gt; 숫자 컴퓨터**입니다.
2. 랩에서 원하는 새 **VM 수를** 입력합니다. 이 숫자는 랩에 등록된 사용자 수보다 크거나 같아야 합니다. 
3. 그런 다음 **저장을**선택합니다. 

    ![모든 버튼 시작](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. 용량을 늘리면 VM 또는 VM이 생성되는 것을 볼 수 있습니다. 목록에 새 VM이 표시되지 않으면 페이지를 새로 고칩니다. 

    ![생성 중인 VM](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>VM 시작

### <a name="start-ot-stop-all-vms"></a>모든 VM을 중지하기 시작
1. **가상 컴퓨터 풀** 페이지로 전환합니다. 
2. 도구 모음에서 **모두 시작을** 선택합니다. 

    ![모든 버튼 시작](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. 모든 VM이 시작되면 도구 모음의 **모든 중지** 단추를 선택하여 모든 VM을 중지할 수 있습니다. 

    ![모든 버튼 중지](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>선택한 VM 시작
선택한 VM을 시작하는 방법에는 두 가지가 있습니다(하나 이상). 첫 번째 방법은 목록에서 VM 또는 VM을 선택한 다음 도구 모음에서 **시작을** 선택하는 것입니다. 

두 번째 방법은 목록에서 하나 이상의 VM을 선택하고 **상태** 열의 단추를 전환하는 것입니다. 

![선택한 VM 시작](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

마찬가지로 **상태** 열의 단추를 전환하거나 도구 모음에서 **중지를** 선택하여 하나 이상의 VM을 중지할 수 있습니다. 

> [!NOTE]
> 교육자가 학생 VM을 켜면 학생의 할당량은 영향을 받지 않습니다. 사용자에 대한 할당량은 예약된 수업 시간 이외에 사용자가 사용할 수 있는 랩 시간 수를 지정합니다. 할당량에 대한 자세한 내용은 [사용자에 대한 할당량 설정을](how-to-configure-student-usage.md?#set-quotas-for-users)참조하십시오.

## <a name="reset-vms"></a>VM 재설정
하나 이상의 VM을 재설정하려면 목록에서 VM을 선택한 다음 도구 모음에서 **재설정을** 선택합니다. 

![선택한 VM 재설정](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

가상 **컴퓨터 재설정** 대화 상자에서 **재설정을**선택합니다. 

![VM 대화 상자 재설정](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>VM에 대한 암호 설정
랩 소유자(교사)는 랩(랩 만들기 마법사)을 만들 거나 **템플릿** 페이지에서 랩을 만든 후 VM에 대한 암호를 설정/재설정할 수 있습니다. 

### <a name="set-password-at-the-time-of-lab-creation"></a>랩 생성 시 암호 설정
랩 소유자(교사)는 랩 만들기 마법사의 가상 컴퓨터 자격 증명 페이지에서 랩의 VM에 대한 암호를 설정할 수 **있습니다.**

![새 랩 창](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

이 페이지의 모든 가상 컴퓨터 옵션에 **대해 동일한 암호 사용** 옵션을 사용/비활성화하면 교사는 랩의 모든 VM에 대해 동일한 암호를 사용하거나 학생이 VM에 대한 암호를 설정할 수 있도록 허용할 수 있습니다. 기본적으로 이 설정은 우분투를 제외한 모든 Windows 및 Linux 운영 체제 이미지에 대해 활성화되어 있습니다. 이 설정을 사용하지 않도록 설정하면 VM에 처음으로 연결하려고 할 때 암호를 설정하라는 메시지가 표시됩니다. 

### <a name="reset-password-later"></a>나중에 암호 재설정

1. 랩의 **템플릿** 페이지에서 도구 모음에서 **암호 재설정을** 선택합니다. 
1. 암호 **재설정** 대화 상자에서 암호를 입력하고 **암호 재설정을**선택합니다.
    
    ![암호 대화 상자 설정](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>학생 VM에 연결
다음 조건이 충족되면 랩 작성자(강사/교수)가 학생 VM에 연결할 수 있습니다. 

- 랩을 만들 때 **모든 가상 시스템에 대해 동일한 암호 사용** 옵션이 선택되었습니다.
- VM이 실행 중입니다. 

 학생 VM에 연결하려면 목록의 VM에서 마우스를 마우스로 가리키고 컴퓨터 단추를 선택합니다.  

![학생 VM 버튼에 연결](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> 교수가 VM을 시작하고 VM에 연결하면 학생 할당량은 영향을 받지 않습니다. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>가상 컴퓨터 목록을 CSV 파일로 내보내기

1. **가상 컴퓨터 풀** 탭으로 전환합니다.
2. 도구 **...** 모음에서 ...(타원)을 선택한 다음 **CSV 내보내기를**선택합니다. 

    ![가상 시스템의 내보내기 목록](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>다음 단계
랩 소유자로서 구성할 수 있는 다른 학생 사용 옵션에 대해 알아보려면 다음 문서를 [참조하십시오.](how-to-configure-student-usage.md)

학생이 VM에 대한 암호를 재설정하는 방법에 대해 알아보려면 [교실 랩(학생)의 가상 컴퓨터에 대한 암호 설정 또는 재설정을](how-to-set-virtual-machine-passwords-student.md)참조하십시오.