---
title: Azure Lab Services를 사용하여 클래스룸 랩 설정 | Microsoft Docs
description: 이 자습서에서는 Azure Lab Services를 사용하여 클래스의 학생이 사용하는 가상 머신이 포함된 클래스룸 랩을 설정합니다.
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 8093a1fd270cdba8bdccaf48737bf6737bdd394d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99821526"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>자습서: 클래스룸 랩 설정 
이 자습서에서는 클래스룸에서 학생이 사용할 가상 머신이 포함된 클래스룸 랩을 설정합니다.  

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 클래스룸 랩 만들기
> * 랩에 사용자 추가
> * 랩 일정 설정
> * 학생에게 초대 이메일 보내기

## <a name="prerequisites"></a>사전 요구 사항
이 자습서에서는 클래스의 가상 머신을 사용하여 랩을 설정합니다. 랩 계정에서 클래스룸 랩을 설정하려면 랩 계정에서 소유자, 랩 작성자 또는 기여자 역할 중 하나의 멤버여야 합니다. 랩 계정을 만드는 데 사용된 계정은 소유자 역할에 자동으로 추가됩니다. 따라서 랩 계정을 만드는 데 사용한 사용자 계정을 사용하여 클래스룸 랩을 만들 수 있습니다. 

Azure Lab Services를 사용하는 일반적인 워크플로는 다음과 같습니다.

1. 랩 계정 작성자는 **랩 작성자** 역할에 다른 사용자를 추가합니다. 예를 들어 랩 계정 작성자/관리자는 **랩 작성자** 역할에 교육자를 추가하여 해당 클래스에 대한 랩을 만들 수 있습니다. 
2. 그런 다음, 교육자는 클래스에 대한 VM을 사용하여 랩을 만들고 클래스에서 학생에게 등록 링크를 보냅니다. 
3. 학생은 교육자로부터 받은 등록 링크를 사용하여 랩에 등록합니다. 등록된 학생은 랩의 VM을 사용하여 강의 학습 및 숙제를 할 수 있습니다. 

## <a name="create-a-classroom-lab"></a>클래스룸 랩 만들기
이 단계에서는 Azure에서 클래스에 대한 랩을 만듭니다. 

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. Internet Explorer 11은 아직 지원되지 않습니다. 
2. **로그인** 을 선택하고 자격 증명을 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
3. **새 랩** 을 선택합니다. 
    
    !["새 랩" 단추가 선택된 "Azure Lab Services"를 보여주는 스크린샷.](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. **새 랩** 창에서 다음 작업을 수행합니다. 
    1. 랩의 **이름** 을 지정하고, **다음** 을 선택합니다.  

        ![클래스룸 랩 만들기](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. **가상 머신 자격 증명** 페이지에서 랩의 모든 VM에 대한 기본 자격 증명을 지정합니다. 사용자의 **이름** 및 **암호** 를 지정하고, **다음** 을 선택합니다.  

        ![새 랩 창](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > 사용자 이름과 암호를 적어 둡니다. 다시 표시되지 않습니다.
    3. **랩 정책** 페이지에서 **마침** 를 선택합니다. 

        ![각 사용자에 대한 할당량](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. 템플릿 VM 만들기 상태를 보여 주는 다음 화면이 표시됩니다. 이 작업에는 최대 20분이 걸립니다. 

    ![템플릿 VM 만들기 상태](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. **템플릿** 페이지에서 다음 단계를 수행합니다. 이 자습서에서는 이러한 단계가 **선택 사항** 입니다.

    1. **연결** 을 선택하여 템플릿 VM에 연결합니다. Linux 템플릿 VM인 경우, SSH 또는 RDP(RDP가 활성화된 경우) 중 무엇을 사용하여 연결할지 선택합니다.
    3. 템플릿 VM의 클래스에 필요한 소프트웨어를 설치하고 구성합니다. 
    4. 템플릿 VM을 **중지** 합니다.  

    > [!NOTE]
    > 템플릿 VM은 실행될 때 **비용** 이 발생하므로 템플릿 VM을 실행할 필요가 없는 경우 종료해야 합니다. 

## <a name="publish-the-template-vm"></a>템플릿 VM 게시
이 단계에서는 템플릿 VM을 게시합니다. 템플릿 VM이 게시되면 Azure Lab Services에서 템플릿을 사용하여 VM을 랩에 만듭니다. 모든 가상 머신은 템플릿과 구성이 동일합니다.

1. **템플릿** 페이지의 도구 모음에서 **게시** 를 선택합니다. 

    ![템플릿 게시 단추](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 게시한 후에는 게시를 취소할 수 없습니다. 
2. **템플릿 게시** 페이지에서 랩에서 만들려는 가상 머신의 수를 입력한 다음, **게시** 를 선택합니다. 

    ![템플릿 게시 - VM 수](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. 템플릿 **게시 상태** 가 페이지에 표시됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 게시가 완료될 때까지 기다린 다음, 왼쪽 메뉴에서 **가상 머신** 을 선택하거나 **가상 머신** 타일을 선택하여 **가상 머신 풀** 페이지로 전환합니다. **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > 교육자가 학생 VM을 켜는 경우 학생에 대한 할당량에 영향을 주지 않습니다. 사용자에 대한 할당량은 사용자가 예약된 수업 시간 이외의 시간에 사용할 수 있는 랩 시간 수를 지정합니다. 할당량에 대한 자세한 내용은 [사용자에 대한 할당량 설정](how-to-configure-student-usage.md?#set-quotas-for-users)을 참조하세요.

## <a name="set-a-schedule-for-the-lab"></a>랩에 대한 일정 설정
랩의 VM이 특정 시간에 자동으로 시작/중지되도록 랩에 예약된 이벤트를 만듭니다. 앞에서 지정한 사용자 할당량(기본값: 10시간)은 이 예약된 시간 이외의 시간으로 각 사용자에게 할당된 추가 시간입니다. 

1. **일정** 페이지로 전환한 다음, 도구 모음에서 **예약된 이벤트 추가** 를 선택합니다. 

    !["일정" 페이지의 "예약된 이벤트 추가" 단추를 보여주는 스크린샷.](./media/how-to-create-schedules/add-schedule-button.png)
2. **예약된 이벤트 추가** 페이지에서 다음 단계를 수행합니다.
    1. **이벤트 유형** 에 대해 **표준** 이 선택되었는지 확인합니다.  
    2. 클래스의 **시작 날짜** 를 선택합니다. 
    4. VM을 시작할 **시작 시간** 을 선택합니다.
    5. VM을 종료할 **중지 시간** 을 선택합니다. 
    6. 지정한 시작 및 중지 시간에 대한 **표준 시간대** 를 선택합니다. 
3. 동일한 **예약된 이벤트 추가** 페이지의 **반복** 섹션에서 현재 일정을 선택합니다.  

    ![일정 페이지의 일정 추가 단추](./media/how-to-create-schedules/select-current-schedule.png)
5. **반복** 대화 상자에서 다음 단계를 수행합니다.
    1. **반복** 필드에 대해 **매주** 가 설정되었는지 확인합니다. 
    2. 일정을 적용하려는 요일을 선택합니다. 다음 예제에서는 월요일-금요일이 선택되었습니다. 
    3. 일정의 **종료 날짜** 를 선택합니다.
    8. **저장** 을 선택합니다. 

        ![반복 일정 설정](./media/how-to-create-schedules/set-repeat-schedule.png)

3. 이제 **예약된 이벤트 추가** 페이지의 **메모(선택 사항)** 에서 예약에 대한 설명 또는 메모를 입력합니다. 
4. **예약된 이벤트 추가** 페이지에서 **저장** 을 선택합니다. 

    ![주별 일정](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. 달력의 시작 날짜로 이동하여 일정이 설정되어 있는지 확인합니다.
    
    ![달력의 일정](./media/how-to-create-schedules/schedule-calendar.png)

    클래스의 일정을 만들고 관리하는 방법에 대한 자세한 내용은 [랩 일정 만들기 및 관리](how-to-create-schedules.md)를 참조하세요.


## <a name="add-users-to-the-lab"></a>랩에 사용자 추가

사용자를 추가하면 기본적으로 **액세스 제한** 옵션이 설정되며, 사용자 목록에 없는 경우 등록 링크가 있어도 학생은 랩에 등록할 수 없습니다. 나열된 사용자만 보내는 등록 링크를 사용하여 랩에 등록할 수 있습니다. **액세스 제한** 은 해제할 수 있습니다. 이 경우 등록 링크가 있는 한 학생은 랩에 등록할 수 있습니다. 

### <a name="add-users-from-an-azure-ad-group"></a>Azure AD 그룹에서 사용자 추가

사용자를 수동으로 추가하거나 삭제할 필요가 없도록 랩 사용자 목록을 기존 Azure AD(Azure Active Directory) 그룹에 동기화할 수 있습니다. 

조직 리소스 및 클라우드 기반 앱에 대한 액세스를 관리하기 위해 Azure AD 그룹을 조직의 Azure Active Directory 내에 만들 수 있습니다. 자세한 내용은 [Azure AD 그룹](../active-directory/fundamentals/active-directory-manage-groups.md)을 참조하세요. 조직에서 Microsoft Office 365 또는 Azure 서비스를 사용하는 경우 조직에는 이미 Azure Active Directory를 관리하는 관리자가 있습니다. 

> [!IMPORTANT]
> 사용자 목록이 비어 있어야 합니다. 수동으로 또는 CSV 파일 가져오기를 통해 추가한 랩 내에 기존 사용자가 있는 경우 랩을 기존 그룹에 동기화하는 옵션이 표시되지 않습니다. 

1. 왼쪽 창에서 **사용자** 를 선택합니다. 
1. **그룹에서 동기화** 를 클릭합니다. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="Azure AD 그룹에서 동기화하여 사용자 추가":::
    
1. 랩을 동기화할 기존 Azure AD 그룹을 선택하라는 메시지가 표시됩니다. 
    
    Azure AD 그룹이 목록에 표시되지 않으면 다음과 같은 이유 때문일 수 있습니다.

    -   Azure Active Directory의 게스트 사용자인 경우(일반적으로 Azure AD를 소유한 조직 외부에 있는 경우) Azure AD 내에서 그룹을 검색할 수 없습니다. 이 경우 Azure AD 그룹을 랩에 추가할 수 없습니다. 
    -   Teams를 통해 만든 Azure AD 그룹은 이 목록에 표시되지 않습니다. Teams 내에서 랩을 직접 만들고 관리하는 Azure Lab Services 앱을 추가할 수 있습니다. [Teams 내에서 랩의 사용자 목록을 관리](how-to-manage-user-lists-within-teams.md)하는 방법에 대한 자세한 내용을 참조하세요. 
1. 랩을 동기화할 Azure AD 그룹이 선택되면 **추가** 를 클릭합니다.
1. 랩이 동기화되면 Azure AD 그룹 내의 모든 사용자를 사용자로 랩에 끌어오고 업데이트된 사용자 목록을 표시합니다. 이 Azure AD 그룹의 사용자만 랩에 액세스할 수 있습니다. 사용자 목록은 Azure AD 그룹의 최신 멤버 자격과 일치하도록 24시간마다 새로 고쳐집니다. 또한 [사용자] 탭에서 [동기화] 단추를 클릭하여 Azure AD 그룹의 최신 변경 내용에 수동으로 동기화할 수 있습니다.
1. **모두 초대** 단추를 클릭하여 사용자를 랩에 초대합니다. 그러면 모든 사용자에게 랩에 대한 등록 링크가 포함된 이메일이 보내집니다. 

### <a name="add-users-manually-from-emails-or-csv-file"></a>이메일 또는 CSV 파일에서 수동으로 사용자 추가

이 섹션에서는 학생을 수동으로(이메일 주소 사용 또는 CSV 파일 업로드) 추가합니다. 

#### <a name="add-users-by-email-address"></a>이메일 주소로 사용자 추가

1. 왼쪽 창에서 **사용자** 를 선택합니다. 
1. **수동으로 사용자 추가** 를 클릭합니다. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="수동으로 사용자 추가":::
1. **이메일 주소로 추가**(기본값)를 선택하고, 학생의 이메일 주소를 별도의 줄 또는 세미콜론으로 구분된 단일 줄에 입력합니다. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="사용자의 이메일 주소 추가":::
1. **저장** 을 선택합니다. 

    랩에 등록되었는지 여부에 관계없이 현재 사용자의 이메일 주소 및 상태가 목록에 표시됩니다. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="사용자 목록":::

    > [!NOTE]
    > 학생이 랩에 등록되면 해당 이름이 목록에 표시됩니다. 목록에 표시되는 이름은 Azure Active Directory에 있는 학생의 이름과 성을 사용하여 구성됩니다. 

#### <a name="add-users-by-uploading-a-csv-file"></a>CSV 파일을 업로드하여 사용자 추가

이메일 주소가 포함된 CSV 파일을 업로드하여 사용자를 추가할 수도 있습니다. 

CSV 텍스트 파일은 CSV(쉼표로 구분) 테이블 형식 데이터(숫자 및 텍스트)를 저장하는 데 사용됩니다. CSV 파일은 정보를 열 필드(예: 스프레드시트)에 저장하는 대신 쉼표로 구분된 정보를 저장합니다. CSV 파일의 각 줄에는 동일한 수의 쉼표로 구분된 "필드"가 있습니다. Excel을 사용하여 CSV 파일을 쉽게 만들고 편집할 수 있습니다.

1. Microsoft Excel에서 학생의 이메일 주소를 한 열에 나열하는 CSV 파일을 만듭니다.

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="CSV 파일의 사용자 목록":::
1. **사용자** 창의 위쪽에서 **사용자 추가** 를 선택한 다음, **CSV 업로드** 를 선택합니다.
1. 학생의 이메일 주소가 포함된 CSV 파일을 선택한 다음, **열기** 를 선택합니다.

    **사용자 추가** 창에 CSV 파일의 이메일 주소 목록이 표시됩니다. 
1. **저장** 을 선택합니다. 
1. **사용자** 창에서 추가된 학생 목록을 봅니다. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="사용자 창에 추가된 사용자 목록"::: 

## <a name="send-invitation-emails-to-users"></a>사용자에게 초대 이메일 보내기

1. 아직 해당 페이지에 있지 않으면 **사용자** 보기로 전환하고, 도구 모음에서 **모두 초대** 를 선택합니다. 

    ![학생 선택](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. **이메일로 초대 보내기** 페이지에서 선택적 메시지를 입력한 다음, **보내기** 를 선택합니다. 이메일에는 등록 링크가 자동으로 포함됩니다. 도구 모음에서 **...(줄임표)** , **등록 링크** 를 차례로 선택하여 이 등록 링크를 가져올 수 있습니다. 

    ![이메일로 등록 링크 보내기](./media/tutorial-setup-classroom-lab/send-email.png)
4. **사용자** 목록에 **초대** 상태가 표시됩니다. 상태가 **보내는 중** 으로 변경된 다음, **&lt;날짜&gt;에 보냄** 으로 변경됩니다. 

학생을 클래스에 추가하고 이들의 랩 사용을 관리하는 방법에 대한 자세한 내용은 [학생의 사용을 구성하는 방법](how-to-configure-student-usage.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure에서 클래스에 대한 랩을 만들었습니다. 학생이 등록 링크를 사용하여 랩에서 VM에 액세스하는 방법을 알아보려면 다음 자습서로 이동하십시오.

> [!div class="nextstepaction"]
> [클래스룸 랩의 VM에 연결](tutorial-connect-virtual-machine-classroom-lab.md)