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
ms.date: 04/24/2019
ms.author: spelluru
ms.openlocfilehash: bdcc4349f84a35b312ecb3ad6205273b62c2e989
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722723"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>자습서: 클래스룸 랩 설정 
이 자습서에서는 클래스룸에서 학생이 사용할 가상 머신이 포함된 클래스룸 랩을 설정합니다.  

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 클래스룸 랩 만들기
> * 랩에 사용자 추가
> * 학생에게 등록 링크 보내기

## <a name="prerequisites"></a>필수 조건
랩 계정에서 클래스룸 랩을 설정하려면 랩 계정에서 소유자, 랩 작성자 또는 기여자 역할 중 하나의 멤버여야 합니다. 랩 계정을 만드는 데 사용된 계정은 소유자 역할에 자동으로 추가됩니다.

랩 소유자는 **랩 작성자** 역할에 다른 사용자를 추가할 수 있습니다. 예를 들어 랩 소유자가 랩 작성자 역할에 교수님을 추가합니다. 그런 다음, 교수님이 강의용 VM을 사용하여 랩을 만듭니다. 학생들이 교수님에게 받은 등록 링크를 사용하여 랩에 등록합니다. 등록된 학생은 랩의 VM을 사용하여 강의 학습 및 숙제를 할 수 있습니다. 랩 작성자 역할에 사용자를 추가하는 자세한 단계는 [랩 작성자 역할에 사용자 추가](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)를 참조하세요.


## <a name="create-a-classroom-lab"></a>클래스룸 랩 만들기

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. Internet Explorer 11은 아직 지원되지 않습니다. 
2. **로그인**을 선택하고 자격 증명을 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
3. **새 랩** 창에서 다음 작업을 수행합니다. 
    1. 랩에 대한 **이름**을 지정합니다. 
    2. 랩에서 **가상 머신의 최대 수**를 지정합니다. 랩을 만든 후 또는 기존 랩에서 VM 수를 늘리거나 줄일 수 있습니다. 자세한 내용은 [랩의 VM 수 업데이트](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)를 참조하세요.
    6. **저장**을 선택합니다.

        ![클래스룸 랩 만들기](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **가상 머신 사양 선택** 페이지에서 다음 단계를 수행합니다.
    1. 랩에서 만드는 VM(가상 머신)의 **크기**를 선택합니다. 현재, **소형**, **중형**, **대형** 및 **GPU** 크기가 허용됩니다.
    3. 랩에서 VM을 만드는 데 사용할 **VM 이미지**를 선택합니다. Linux 이미지를 선택하면, 이에 대한 원격 데스크톱 연결을 사용하도록 설정하는 옵션이 표시됩니다. 자세한 내용은 [Linux에 대한 원격 데스크톱 연결을 사용하도록 설정](how-to-enable-remote-desktop-linux.md)을 참조하세요.
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
8. **템플릿 구성** 페이지에서 다음 단계를 수행합니다. 이 자습서에서는 이러한 단계가 **선택 사항**입니다.
    1. **연결**을 선택하여 템플릿 VM에 연결합니다. Linux 템플릿 VM인 경우, SSH 또는 RDP(RDP가 활성화된 경우) 중 무엇을 사용하여 연결할지 선택합니다.
    2. 템플릿 VM에 소프트웨어를 설치하고 구성합니다.     
    3. 템플릿에 대한 **설명**을 입력합니다.
9. 템플릿 페이지에서 **다음**을 선택합니다. 
10. **템플릿 게시** 페이지에서 다음 작업을 수행합니다. 
    1. 템플릿을 즉시 게시하려면 **게시**를 선택합니다.  

        > [!WARNING]
        > 게시한 후에는 게시를 취소할 수 없습니다. 
    2. 나중에 게시하려면 **나중을 위해 저장**을 선택합니다. 마법사를 마치면 템플릿 VM을 게시할 수 있습니다. 마법사를 마친 후에 템플릿을 구성하고 게시하는 방법에 대한 자세한 내용은 [클래스룸 랩을 관리하는 방법](how-to-manage-classroom-labs.md) 문서의 [템플릿 게시](how-to-create-manage-template.md#publish-the-template-vm) 섹션을 참조하세요.

        ![템플릿 게시](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 템플릿 **게시 진행률**이 표시됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 템플릿이 성공적으로 게시되면 다음 페이지가 표시됩니다. **완료**를 선택합니다.

    ![템플릿 게시 - 성공](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 랩의 **대시보드**가 표시됩니다. 
    
    ![클래스룸 랩 대시보드](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. 왼쪽 메뉴에서 가상 머신을 선택하거나 가상 머신 타일을 선택하여 **가상 머신** 페이지로 전환합니다. **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>랩에 사용자 추가

1. 왼쪽 메뉴에서 **사용자**를 선택합니다. 기본적으로 **액세스 제한** 옵션이 사용되도록 설정됩니다. 이 설정이 켜져 있으면 사용자가 사용자 목록에 없으면 등록 링크가 있어도 랩에 등록할 수 없습니다. 목록의 사용자만 전송 받은 등록 링크를 사용하여 랩에 등록할 수 있습니다. 이 절차에서는 사용자를 목록에 추가합니다. **액세스 제한**을 끌 수도 있습니다. 이 경우 사용자는 등록 링크가 있기만 하면 랩에 등록할 수 있습니다. 
2. 도구 모음에서 **사용자 추가**를 선택합니다. 

    ![사용자 추가 단추](../media/how-to-configure-student-usage/add-users-button.png)
1. **사용자 추가** 페이지에서 여러 줄에 걸쳐 또는 세미콜론으로 구분해서 단일 줄에 사용자의 이메일 주소를 입력합니다. 

    ![사용자 이메일 주소 추가](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. **저장**을 선택합니다. 목록에서 사용자의 이메일 주소 및 해당 상태(등록됨 또는 등록 해제됨)를 확인합니다. 

    ![사용자 목록](../media/how-to-configure-student-usage/users-list-new.png)


## <a name="send-an-email-with-the-registration-link"></a>등록 링크가 포함된 이메일 보내기

1. 페이지에 아직 표시되지 않으면 **사용자** 보기로 전환합니다. 
2. 목록에서 특정 또는 모든 사용자를 선택합니다. 특정 사용자를 선택하려면 목록 첫 번째 열의 확인란을 선택하세요. 모든 사용자를 선택하려면 첫 번째 열의 제목 앞에 있는 확인란(**이름**)을 선택하거나 목록에 있는 모든 사용자에 대한 확인란을 모두 선택하세요. 목록에서 **초대 상태**의 상태를 볼 수 있습니다.  다음 이미지는 모든 학생의 초대 상태가 **초대를 보내지 않음**으로 설정되어 있습니다. 

    ![학생 선택](../media/tutorial-setup-classroom-lab/select-students.png)
1. 행 중 하나에서 **이메일 아이콘(봉투)** 를 선택하거나 도구 모음에서 **초대 보내기**를 선택합니다. 목록의 학생 이름을 마우스로 가리켜서 이메일 아이콘을 표시할 수도 있습니다. 

    ![이메일로 등록 링크 보내기](../media/tutorial-setup-classroom-lab/send-email.png)
4. **이메일로 등록 링크 보내기** 페이지에서 다음 단계를 수행하세요. 
    1. 학생들에게 보내려는 **선택적 메시지**를 입력합니다. 이메일에는 등록 링크가 자동으로 포함됩니다. 
    2. **이메일로 등록 링크 보내기** 페이지에서 **보내기**를 선택합니다. 초대의 상태가 **초대를 보내는 중**에서 **초대 보냄**으로 차례로 변하는 것을 볼 수 있습니다. 
        
        ![초대 전송됨](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 클래스룸 랩을 만들고 랩을 구성했습니다. 학생이 등록 링크를 사용하여 랩에서 VM에 액세스하는 방법을 알아보려면 다음 자습서로 이동하십시오.

> [!div class="nextstepaction"]
> [클래스룸 랩의 VM에 연결](tutorial-connect-virtual-machine-classroom-lab.md)

