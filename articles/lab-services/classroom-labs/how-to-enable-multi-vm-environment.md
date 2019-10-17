---
title: Azure Lab Services에서 다중 VM 환경 사용 Microsoft Docs
description: Azure Lab Services의 클래스 룸 랩에서 템플릿 가상 머신 내에서 여러 가상 머신을 사용 하 여 환경을 만드는 방법에 대해 알아봅니다.
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
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332372"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>교실 랩의 템플릿 VM 내에 여러 Vm이 있는 환경 만들기
현재 Azure Lab Services에서는 랩에 하나의 템플릿 가상 머신을 설정 하 고 각 사용자가 단일 복사본을 사용할 수 있도록 합니다. 그러나 방화벽이 나 서버를 설정 하는 방법에 대 한 IT 클래스를 교수 하는 경우에는 여러 가상 컴퓨터가 네트워크를 통해 서로 통신할 수 있는 환경에 각 학생을 제공 해야 할 수 있습니다.

중첩 된 가상화를 사용 하면 랩의 템플릿 가상 머신 내에서 다중 VM 환경을 만들 수 있습니다. 템플릿을 게시 하면 랩에 있는 각 사용자에 게 가상 머신 내에서 여러 Vm을 설정 하는 가상 머신이 제공 됩니다.

## <a name="what-is-nested-virtualization"></a>중첩 가상화 란?
중첩 된 가상화를 사용 하면 가상 머신 내에서 가상 머신을 만들 수 있습니다. 중첩 된 가상화는 Hyper-v를 통해 수행 되며 Windows Vm 에서만 사용할 수 있습니다.

중첩 된 가상화에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure에서 중첩 된 가상화](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Azure VM에서 중첩 된 가상화를 사용 하도록 설정 하는 방법](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Azure Lab Services에서 중첩 된 가상화 사용
중요 한 단계는 다음과 같습니다.

1. 랩에 대 한 **큰** 규모의 **Windows** 템플릿 컴퓨터를 만듭니다. 
2. 연결 하 고 [중첩 된 가상화를 사용 하도록 설정](../../virtual-machines/windows/nested-virtualization.md)합니다.


다음 절차에서는 자세한 단계를 제공 합니다. 

1. 랩 계정이 아직 없는 경우 만듭니다. 지침은 [자습서: Azure Lab Services 사용 하 여 랩 계정 설정](tutorial-setup-lab-account.md)을 참조 하세요.
1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. Internet Explorer 11은 아직 지원되지 않습니다. 
2. **로그인**을 선택하고 자격 증명을 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
3. **새 랩**을 선택 합니다. 
    
    ![클래스룸 랩 만들기](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. **새 랩** 창에서 다음 작업을 수행합니다. 
    1. 랩에 대한 **이름**을 지정합니다. 
    2. **가상 컴퓨터 크기**에 대해 **큰 (중첩 된 가상화)** 또는 **중간 (중첩 된 가상화)** 을 선택 합니다.
    6. 사용할 Windows **이미지** 를 선택 합니다. 중첩 된 가상화는 Windows 컴퓨터 에서만 사용할 수 있습니다. 
    4. 그다음에 **다음**을 선택합니다. 

        ![클래스룸 랩 만들기](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. **가상 컴퓨터 자격 증명** 페이지에서 랩의 모든 vm에 대 한 기본 자격 증명을 지정 합니다. 사용자의 **이름과** **암호** 를 지정 하 고 **다음**을 선택 합니다.  

        ![새 랩 창](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > 사용자 이름과 암호를 적어 둡니다. 다시 표시되지 않습니다.
    3. **랩 정책** 페이지에서 각 사용자에 게 할당 된 시간 (**각 사용자에 대 한 할당량**)을 랩의 예약 된 시간 외부에 입력 한 다음 **마침**을 선택 합니다. 

        ![각 사용자에 대 한 할당량](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. 템플릿 VM 만들기의 상태를 보여 주는 다음 화면이 표시 됩니다. 랩에서 템플릿을 만드는 데는 최대 20분이 걸립니다. 

    ![템플릿 VM 만들기 상태](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. **템플릿** 페이지의 도구 모음에서 **템플릿 사용자 지정** 을 선택 합니다. 

    ![템플릿 사용자 지정 단추](../media/how-to-create-manage-template/customize-template-button.png)
2. **템플릿 사용자 지정** 대화 상자에서 **계속**을 선택 합니다. 템플릿을 시작 하 고 변경한 후에는 더 이상 사용자에 게 마지막으로 게시 한 가상 머신과 동일한 설정이 적용 되지 않습니다. 다시 게시 하기 전 까지는 사용자의 기존 가상 컴퓨터에 템플릿 변경 내용이 반영 되지 않습니다.

    ![사용자 지정 대화 상자](../media/how-to-create-manage-template/customize-template-dialog.png)
1. 템플릿 VM에 연결 하 여 중첩 된 가상화를 구성 하려면 도구 모음에서 **템플릿에 연결** 단추를 선택 하 고 지침을 따릅니다. Windows 컴퓨터의 경우 RDP 파일을 다운로드 하는 옵션이 표시 됩니다. 

    ![템플릿 VM에 연결](../media/how-to-create-manage-template/connect-template-vm.png) 
9. 템플릿 가상 머신 내에서 중첩 된 가상화를 설정 하 고 여러 가상 머신을 사용 하 여 가상 네트워크를 구성 합니다. 자세한 단계별 지침은 [AZURE VM에서 중첩 된 가상화를 사용 하도록 설정 하는 방법](../../virtual-machines/windows/nested-virtualization.md)을 참조 하세요. 다음은 단계에 대 한 간략 한 요약입니다. 
    1. 템플릿 가상 머신에서 Hyper-v 기능을 사용 하도록 설정 합니다.
    2. 중첩 된 가상 컴퓨터에 대 한 인터넷 연결을 사용 하 여 내부 가상 네트워크 설정
    3. Hyper-v 관리자를 통해 가상 컴퓨터 만들기
    4. 가상 컴퓨터에 IP 주소 할당
10. **템플릿** 페이지의 도구 모음에서 **게시** 를 선택 합니다. 

    ![게시 템플릿 단추](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 게시한 후에는 게시를 취소할 수 없습니다. 
8. **게시 템플릿** 페이지에서 랩에서 만들 가상 머신의 수를 입력 한 다음 **게시**를 선택 합니다. 

    ![게시 템플릿-Vm 수](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 페이지에 템플릿 **게시 상태가** 표시 됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>다음 단계

이제 각 사용자는 내부에 다중 VM 환경을 포함 하는 단일 가상 머신을 가져옵니다. 랩에 사용자를 추가 하 고 등록 링크를 보내는 방법을 알아보려면 [랩에 사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)문서를 참조 하세요.