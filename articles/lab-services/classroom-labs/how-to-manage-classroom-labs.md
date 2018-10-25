---
title: Azure Lab Services의 클래스룸 랩 관리 | Microsoft Docs
description: 클래스룸 랩을 만들고 구성하거나, 모든 클래스룸 랩을 보거나, 랩 사용자와 등록 링크를 공유하거나 또는 랩을 삭제하는 방법에 대해 알아봅니다.
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
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: eebdc98db5ecdf518d3b0b58e6757a2b7ecd5dd7
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043807"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Azure Lab Services에서 클래스룸 랩 관리 
이 아티클에서는 클래스룸 랩 생성 및 구성, 모든 클래스룸 랩 보기 또는 클래스룸 랩 삭제 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
랩 계정에서 클래스룸 랩을 설정하려면 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 랩 소유자는 [랩 작성자 역할에 사용자 추가](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) 문서의 단계에 따라 랩 작성자 역할에 다른 사용자를 추가할 수 있습니다.

## <a name="create-a-classroom-lab"></a>클래스룸 랩 만들기

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. 
2. **로그인**을 선택하고 자격 증명을 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
3. **새 랩** 창에서 다음 작업을 수행합니다. 
    1. 랩에 대한 **이름**을 지정합니다. 
    2. 랩에 허용되는 최대 **사용자 수**를 지정합니다. 
    6. **저장**을 선택합니다.

        ![클래스룸 랩 만들기](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **가상 머신 사양 선택** 페이지에서 다음 단계를 수행합니다.
    1. 랩에서 만드는 VM(가상 머신)의 **크기**를 선택합니다. 
    2. VM을 만들려는 **지역**을 선택합니다. 
    3. 랩에서 VM을 만드는 데 사용할 **VM 이미지**를 선택합니다. 
    4. **다음**을 선택합니다.

        ![VM 사양 지정](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. **자격 증명 설정** 페이지에서 랩의 모든 VM에 대한 기본 자격 증명을 지정합니다. 
    1. 랩의 모든 VM에 대한 **사용자 이름**을 지정합니다.
    2. 사용자에 대한 **암호**를 지정합니다. 

        > [!IMPORTANT]
        > 사용자 이름과 암호를 적어 둡니다. 다시 표시되지 않습니다.
    3. **만들기**를 선택합니다. 

        ![자격 증명 설정](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. **템플릿 구성** 페이지에 랩 만들기 프로세스의 상태가 표시됩니다. 랩에서 템플릿을 만드는 데는 최대 20분이 걸립니다. 

    ![템플릿 구성](../media/tutorial-setup-classroom-lab/configure-template.png)
7. 템플릿 구성이 완료되면 다음 페이지가 표시됩니다. 

    ![완료된 템플릿 페이지 구성](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. 다음 단계는 이 자습서의 선택적 단계입니다. 
    1. **시작**을 선택하여 템플릿 VM을 시작합니다.
    2. **연결**을 선택하여 템플릿 VM에 연결합니다. 
    3. 템플릿 VM에 소프트웨어를 설치하고 구성합니다. 
    4. VM을 **중지**합니다.  
    5. 템플릿에 대한 **설명**을 입력합니다.

        ![템플릿 구성 페이지의 다음 단추](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. 템플릿 페이지에서 **다음**을 선택합니다. 
10. **템플릿 게시** 페이지에서 다음 작업을 수행합니다. 
    1. 템플릿을 즉시 게시하려면 *게시한 후에는 템플릿을 수정할 수 없습니다. 이 프로세스는 한 번만 수행할 수 있으며 최대 1시간이 걸릴 수 있습니다.* 확인란을 선택하고, **게시**를 선택합니다.  

        > [!WARNING]
        > 게시한 후에는 게시를 취소할 수 없습니다. 
    2. 나중에 게시하려면 **나중을 위해 저장**을 선택합니다. 마법사가 완료되면 템플릿 VM을 게시할 수 있습니다. 마법사가 완료된 후에 구성과 게시를 수행하는 방법에 대한 자세한 내용은 [클래스룸 랩을 관리하는 방법](how-to-manage-classroom-labs.md) 문서의 [템플릿 게시](#publish-the-template) 섹션을 참조하세요.

        ![템플릿 게시](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 템플릿 **게시 진행률**이 표시됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 템플릿이 성공적으로 게시되면 다음 페이지가 표시됩니다. **완료**를 선택합니다.

    ![템플릿 게시 - 성공](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 랩의 **대시보드**가 표시됩니다. 
    
    ![클래스룸 랩 대시보드](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. **가상 머신** 페이지로 전환하고, **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="configure-usage-policy"></a>사용 정책 구성

1. **사용 정책**을 선택합니다. 
2. **사용 정책**의 설정에서 랩을 사용할 수 있는 **사용자 수**를 입력합니다.
3. **저장**을 선택합니다. 

    ![사용 정책](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>템플릿 설정
랩의 템플릿은 모든 사용자의 가상 머신이 만들어지는 기본 가상 머신 이미지입니다. 랩 사용자에게 제공하려는 구성과 정확히 일치하도록 템플릿 가상 머신을 설정합니다. 랩 사용자에게 표시되는 템플릿의 이름 및 설명을 제공할 수 있습니다. 랩 사용자가 템플릿 VM의 인스턴스를 사용할 수 있도록 템플릿을 게시합니다.  

### <a name="set-template-title-and-description"></a>템플릿 제목 및 설명 설정
1. **템플릿** 섹션에서 템플릿의 **편집**(연필 아이콘)을 선택합니다. 
2. **사용자 보기** 창에 템플릿의 **제목**을 입력합니다.
3. 템플릿에 대한 **설명**을 입력합니다.
4. **저장**을 선택합니다.

    ![클래스룸 랩 설명](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>템플릿 VM 설정
 템플릿 VM에 연결하고 필요한 소프트웨어를 설치한 후 학생에게 제공합니다. 

1. 템플릿 가상 머신이 준비될 때까지 기다립니다. 가상 머신이 준비되면 **시작** 단추가 활성화됩니다. VM을 시작하려면 **시작**을 선택합니다.

    ![템플릿 VM 시작](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. VM에 연결하려면 **연결**을 선택하고 지침을 따릅니다. 

    ![템플릿 VM에 연결](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. 학생들이 랩을 사용하는 데 필요한 소프트웨어를 설치합니다(예: Visual Studio, Azure Storage Explorer 등). 
2. 템플릿 VM의 연결을 종료합니다(원격 데스크톱 세션 종료). 
3. **중지**를 선택하여 템플릿 VM을 **중지**합니다. 

    ![템플릿 VM 중지](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


## <a name="publish-the-template"></a>템플릿 게시 
템플릿을 게시하면 Azure Lab Services가 템플릿을 사용하여 랩에 VM을 만듭니다. 이 과정에서 만들어지는 VM 수는 랩에 허용되는 최대 사용자 수와 같으며, 이 값은 랩의 사용 정책에서 설정할 수 있습니다. 모든 가상 머신은 템플릿과 구성이 동일합니다. 

1. **템플릿** 섹션에서 **게시**를 선택합니다. 

    ![템플릿 VM 게시](../media/tutorial-setup-classroom-lab/public-access.png)
1. **게시** 창에서 **게시됨** 옵션을 선택합니다. 
2. 이제 **게시** 단추를 선택합니다. 만들려는 VM 수, 즉 랩에 허용되는 사용자 수에 따라 이 프로세스를 완료하는 데 다소 시간이 걸릴 수 있습니다.
    
    > [!IMPORTANT]
    > 템플릿이 게시되면 게시를 취소할 수 없습니다. 
4. **가상 머신** 페이지로 전환하고, **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. 

    ![가상 머신](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. VM이 만들어질 때까지 기다립니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="send-registration-link-to-students"></a>학생에게 등록 링크 보내기

1. **대시보드** 보기로 전환합니다. 
2. **사용자 등록** 타일을 선택합니다.

    ![학생 등록 ​링크](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. **사용자 등록** 대화 상자에서 **복사** 단추를 선택합니다. 링크가 클립보드에 복사됩니다. 이메일 편집기에 붙여넣고 학생에게 이메일을 보냅니다. 

    ![학생 등록 ​링크](../media/tutorial-setup-classroom-lab/registration-link.png)
2. **사용자 등록** 대화 상자에서 **닫기**를 선택합니다. 

## <a name="view-all-classroom-labs"></a>모든 클래스룸 랩 보기
1. [Azure Lab Services Portal](https://labs.azure.com)로 이동합니다.
2. 선택한 랩 계정에 있는 모든 랩이 표시되는지 확인합니다. 

    ![모든 랩](../media/how-to-manage-classroom-labs/all-labs.png)
3. 상단의 드롭 다운 목록을 사용하여 다른 랩 계정을 선택합니다. 선택한 랩 계정에 랩이 표시됩니다. 

## <a name="delete-a-classroom-lab"></a>클래스룸 랩 삭제
1. 랩에 대한 타일에서 구석에 있는 3 개의 점(...)을 선택합니다. 

    ![랩 선택](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. **삭제**를 선택합니다. 

    ![삭제 단추](../media/how-to-manage-classroom-labs/delete-button.png)
3. **랩 삭제** 대화 상자에서 **삭제**를 선택합니다. 

    ![대화 상자 삭제](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 
## <a name="manage-student-vms"></a>학생 VM 관리
학생이 사용자가 입력한 등록 링크를 사용하여 Azure Lab Services에 등록하면 **가상 머신** 탭에서 학생에게 할당된 VM이 표시됩니다. 

![학생에게 할당된 가상 머신](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

학생 VM에서 다음 작업을 수행할 수 있습니다. 

- VM이 실행 중인 경우 VM 중지 
- VM이 중지된 경우 VM 시작 
- VM에 연결합니다. 
- VM을 삭제합니다. 

## <a name="next-steps"></a>다음 단계
Azure Lab Services를 사용하여 랩 설정 시작합니다.

- [클래스룸 랩 설정](how-to-manage-classroom-labs.md)
- [랩 설정](../tutorial-create-custom-lab.md)
