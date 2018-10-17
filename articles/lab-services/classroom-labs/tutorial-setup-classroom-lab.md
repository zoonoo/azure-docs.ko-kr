---
title: Azure Lab Services를 사용하여 클래스룸 랩 설정 | Microsoft Docs
description: 이 자습서에서는 클래스룸에서 사용할 랩을 설정합니다.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/05/2018
ms.author: spelluru
ms.openlocfilehash: 6696d6e7e53e98dfab2a65c7c66825936020f33c
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856641"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>자습서: 클래스룸 랩 설정 
이 자습서에서는 클래스룸에서 학생이 사용할 가상 머신이 포함된 클래스룸 랩을 설정합니다.  

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 클래스룸 랩 만들기
> * 클래스룸 랩 구성
> * 학생에게 등록 링크 보내기

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
    2. 나중에 게시하려면 **나중을 위해 저장**을 선택합니다. 마법사가 완료되면 템플릿 VM을 게시할 수 있습니다. 마법사가 완료된 후에 템플릿을 구성하고 게시하는 방법에 대한 자세한 내용은 [클래스룸 랩을 관리하는 방법](how-to-manage-classroom-labs.md) 문서의 [템플릿 게시](how-to-manage-classroom-labs.md#publish-the-template) 섹션을 참조하세요.

        ![템플릿 게시](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 템플릿 **게시 진행률**이 표시됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 템플릿이 성공적으로 게시되면 다음 페이지가 표시됩니다. **완료**를 선택합니다.

    ![템플릿 게시 - 성공](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 랩의 **대시보드**가 표시됩니다. 
    
    ![클래스룸 랩 대시보드](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. **가상 머신** 페이지로 전환하고, **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>학생에게 등록 링크 보내기

1. 왼쪽 메뉴에서 **대시보드**를 선택하여 **대시보드** 보기로 전환합니다. 
2. **사용자 등록** 타일을 선택합니다.

    ![학생 등록 ​링크](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. **사용자 등록** 대화 상자에서 **복사** 단추를 선택합니다. 링크가 클립보드에 복사됩니다. 이메일 편집기에 붙여넣고 학생에게 이메일을 보냅니다. 

    ![학생 등록 ​링크](../media/tutorial-setup-classroom-lab/registration-link.png)
2. **사용자 등록** 대화 상자에서 **닫기**를 선택합니다. 
3. 학생이 강의에 등록할 수 있도록 등록 링크를 학생들과 공유합니다. 


## <a name="next-steps"></a>다음 단계
이 자습서에서는 클래스룸 랩을 만들고 랩을 구성했습니다. 학생이 등록 링크를 사용하여 랩에서 VM에 액세스하는 방법을 알아보려면 다음 자습서로 이동하십시오.

> [!div class="nextstepaction"]
> [클래스룸 랩의 VM에 연결](tutorial-connect-virtual-machine-classroom-lab.md)

