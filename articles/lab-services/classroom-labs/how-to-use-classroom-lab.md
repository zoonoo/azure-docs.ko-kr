---
title: Azure Lab Services의 클래스룸 랩에 액세스하는 방법 | Microsoft Docs
description: 이 자습서에서는 교수가 설정한 클래스룸 랩의 가상 머신에 액세스합니다.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: db1e60ccd7e05cb8b6923d6b2ec008cdfb76eaa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501923"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Azure Lab Services의 클래스룸 랩에 액세스하는 방법
이 문서에서는 클래스룸 랩에 등록하고, 액세스할 수 있는 모든 랩을 살펴보고, 랩에서 VM을 시작/중지하고, VM에 연결하는 방법을 설명합니다. 

## <a name="register-to-the-lab"></a>랩에 등록

1. 교수/강사로부터 받은 **등록 URL**로 이동합니다. 등록을 완료한 후에는 등록 URL을 사용할 필요가 없습니다. 대신 URL: [https://labs.azure.com](https://labs.azure.com)을 사용합니다. Internet Explorer 11은 아직 지원되지 않습니다. 
1. 학교 계정을 사용하여 서비스에 로그인하여 등록을 완료합니다. 

    > [!NOTE]
    > Azure Lab Services를 사용하려면 Microsoft 계정이 필요합니다. Yahoo 또는 Google 계정과 같은 비 Microsoft 계정을 사용하여 포털에 로그인 하려는 경우 지침에 따라 Microsoft 계정에 연결할 Microsoft 계정을 만듭니다. 그런 다음, 단계에 따라 등록 프로세스를 완료합니다. 
1. 등록 후에는 액세스할 수 있는 랩의 가상 머신이 보이는지 확인합니다. 
1. 가상 머신이 준비될 때까지 기다립니다. VM 타일에서 다음 필드를 확인합니다.
    1. 타일의 맨 위에 **랩 이름**이 표시됩니다.
    1. 오른쪽에는 VM의 **OS(운영 체제)** 를 나타내는 아이콘이 표시됩니다. 이 예제에서는 Windows OS입니다. 
    1. 타일 아래쪽에는 VM을 시작/중지하고 VM에 연결하기 위한 아이콘/단추가 표시됩니다. 
    1. 단추 오른쪽에는 VM 상태가 표시됩니다. VM 상태가 **중지됨**인지 확인합니다.

        ![중지됨 상태의 VM](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>VM 시작 또는 중지
1. 다음 이미지와 같이 첫 번째 단추를 선택하여 VM을 **시작**합니다. 이 프로세스는 다소 시간이 걸립니다.  

    ![VM 시작](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. VM 상태가 **실행 중**으로 설정되어 있는지 확인합니다. 

    ![실행 중 상태의 VM](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    첫 번째 단추의 아이콘이 **중지** 작업을 나타내도록 변경되었는지 확인합니다. 이 단추를 선택하여 VM을 중지할 수 있습니다. 

## <a name="connect-to-the-vm"></a>VM에 연결

1. 다음 이미지와 같이 두 번째 단추를 선택하여 랩의 VM에 **연결**합니다. 

    ![VM에 연결](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 다음 단계 중 하나를 수행합니다. 
    1. **Windows** 가상 머신의 경우 **RDP** 파일을 하드 디스크에 저장합니다. RDP 파일을 열어 가상 머신에 연결합니다. 강사/교수로부터 받은 **사용자 이름** 및 **암호**를 사용하여 머신에 로그인합니다. 
    3. **Linux** 가상 머신의 경우, **SSH** 또는 **RDP**(사용하도록 설정된 경우)를 사용하여 연결할 수 있습니다. 자세한 내용은 [Linux 머신에 대해 원격 데스크톱 연결을 사용하도록 설정](how-to-enable-remote-desktop-linux.md)을 참조하세요. 
    1. **Mac**을 사용하여 랩 VM에 연결하는 경우 다음 섹션의 지침을 따르세요. 

## <a name="progress-bar"></a>진행률 표시줄 
타일의 진행률 표시줄에는 할당된 [할당량 시간](how-to-configure-student-usage.md#set-quotas-for-users) 대비 사용한 시간이 표시됩니다. 이 시간은 랩의 예약된 시간 외에 사용자에게 할당된 추가 시간입니다. 진행률 표시줄 및 진행률 표시줄 아래의 텍스트 색은 다음 시나리오에 따라 달라집니다.

- 클래스가 진행 중이면(클래스의 일정 내에서) 할당량 시간이 사용되지 않는다는 것을 나타내기 위해 진행률 표시줄이 회색으로 표시됩니다. 

    ![회색으로 표시된 진행률 표시줄](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- 할당량이 할당되지 않은 경우(0시간) 진행률 표시줄 대신 **Available during classes only(클래스 중에만 사용 가능)** 텍스트가 표시됩니다. 
    
    ![할당량이 설정되지 않은 상태](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- **할당량이 부족**하면 진행률 표시줄이 **빨간색**으로 표시됩니다. 

    ![빨간색 진행률 표시줄](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- 랩의 예약 시간을 벗어났거나 할당량 시간 중 일부가 사용된 경우 진행률 표시줄이 **파란색**으로 표시됩니다. 

    ![파란색 진행률 표시줄](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>모든 클래스룸 랩 보기
랩에 등록한 후 다음 단계를 수행하여 모든 클래스 룸 랩을 볼 수 있습니다. 

1. 로 [https://labs.azure.com](https://labs.azure.com)이동합니다. Internet Explorer 11은 아직 지원되지 않습니다. 
2. 랩에 등록하는 데 사용한 사용자 계정을 사용하여 서비스에 로그인합니다. 
3. 액세스 권한이 있는 모든 랩이 표시되는지 확인합니다. 

    ![모든 랩 보기](../media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
 