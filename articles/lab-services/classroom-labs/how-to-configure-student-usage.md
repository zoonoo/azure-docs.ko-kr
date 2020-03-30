---
title: Azure 랩 서비스의 강의실 랩에서 사용 설정 구성
description: 랩의 학생 수를 구성하고, 랩에 등록하고, VM을 사용할 수 있는 시간 등을 제어하는 방법을 알아봅니다.
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
ms.openlocfilehash: 2697bed77263bb5b8349898765851a9b87992279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159458"
---
# <a name="add-and-manage-lab-users"></a>랩 사용자 추가 및 관리

이 문서에서는 학생 사용자를 랩에 추가하고, 랩에 등록하고, 가상 컴퓨터(VM)를 사용할 수 있는 추가 시간 수를 제어하는 방법을 설명합니다. 

## <a name="add-users-to-a-lab"></a>랩에 사용자 추가

이 섹션에서는 수동으로 또는 CSV 파일을 업로드하여 학생을 랩에 추가합니다. 다음을 수행합니다.

1. 왼쪽 창에서 **사용자**를 선택합니다. 

    기본적으로 액세스 **제한** 옵션이 켜져 있으며, 사용자 목록에 있지 않으면 학생은 등록 링크가 있더라도 랩에 등록할 수 없습니다. 나열된 사용자만 보내는 등록 링크를 사용하여 랩에 등록할 수 있습니다. 이 절차에서는 사용자를 목록에 추가합니다. 또는 **액세스 제한을**해제하여 학생이 등록 링크가 있는 한 랩에 등록할 수 있습니다. 

1. **사용자** 창 상단에서 **사용자 추가를**선택한 다음 **전자 메일 주소로 추가를**선택합니다. 

    !["사용자 추가" 버튼](../media/how-to-configure-student-usage/add-users-button.png)

1. 사용자 **추가** 창에서 학생의 전자 메일 주소를 별도의 줄또는 세미콜론으로 구분된 한 줄에 입력합니다. 

    ![사용자의 이메일 주소 추가](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. **저장**을 선택합니다. 

    이 목록에는 랩에 등록되어 있는지 여부에 관계없이 현재 사용자의 전자 메일 주소와 상태가 표시됩니다. 

    ![사용자 목록](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > 학생이 랩에 등록되면 목록에 이름이 표시됩니다. 목록에 표시된 이름은 Azure Active Directory에서 학생의 이름과 성을 사용하여 생성됩니다. 

### <a name="add-users-by-uploading-a-csv-file"></a>CSV 파일을 업로드하여 사용자 추가

이메일 주소가 포함된 CSV 파일을 업로드하여 사용자를 추가할 수도 있습니다.

1. Microsoft Excel에서 학생의 전자 메일 주소를 한 열에 나열하는 CSV 파일을 만듭니다.

    ![CSV 파일의 사용자 목록](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. **사용자** 창 상단에서 **사용자 추가를**선택한 다음 **CSV 업로드를**선택합니다.

    !["CSV 업로드" 버튼](../media/how-to-configure-student-usage/upload-csv-button.png)

1. 학생의 이메일 주소가 포함된 CSV 파일을 선택한 다음 **열기를**선택합니다.

    **사용자 추가** 창에는 CSV 파일의 전자 메일 주소 목록이 표시됩니다. 

    ![CSV 파일의 전자 메일 주소가 있는 "사용자 추가" 창](../media/how-to-configure-student-usage/add-users-window.png)

1. **저장**을 선택합니다. 

1. **사용자** 창에서 추가된 학생 목록을 봅니다. 

    !["사용자" 창에 추가된 사용자 목록](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>사용자에게 초대 보내기

새 사용자에게 등록 링크를 보내려면 다음 방법 중 하나를 사용합니다. 

랩에 **대한 액세스 제한** 옵션을 사용하도록 설정한 경우 나열된 사용자만 등록 링크를 사용하여 랩에 등록할 수 있습니다. 기본적으로 이 옵션은 사용하도록 설정됩니다. 

### <a name="invite-all-users"></a>모든 사용자 초대

이 메서드는 등록 링크와 나열된 모든 학생에게 선택적 메시지를 사용하여 전자 메일을 보내는 방법을 보여 주며

1. **사용자** 창에서 모두 **초대를**선택합니다. 

    !["모두 초대" 버튼](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. 전자 **메일 창으로 초대 보내기창에서** 선택적 메시지를 입력한 다음 **에서 보내기를**선택합니다. 

    이메일에는 등록 링크가 자동으로 포함됩니다. 등록 링크를 별도로 저장하려면 **사용자** 창 상단의 타원 **(...**) 을 선택한 다음 **등록 링크를**선택합니다. 

    !["이메일로 등록 링크 보내기" 창](../media/tutorial-setup-classroom-lab/send-email.png)

    **사용자** 목록의 **초대** 열에는 추가된 각 사용자의 초대 상태가 표시됩니다. 상태가 **>보낸** 다음 **보낸 \<날짜로 **변경됩니다. 

### <a name="invite-selected-users"></a>선택한 사용자 초대

이 방법을 사용하면 특정 학생만 초대하고 다른 사용자와 공유할 수 있는 등록 링크를 얻는 방법을 보여 주어 있습니다.

1. **사용자** 창에서 목록에서 학생 또는 여러 학생을 선택합니다. 

1. 선택한 학생의 행에서 **봉투** 아이콘을 선택하거나 도구 모음에서 **초대를**선택합니다. 

    ![선택한 사용자 초대](../media/how-to-configure-student-usage/invite-selected-users.png)

1. 전자 **메일창에서** 선택적 **메시지를**입력한 다음 **에서 보내기를**선택합니다. 

    ![선택한 사용자에게 이메일 보내기](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    **사용자** 창에는 테이블의 **초대** 란에 이 작업의 상태가 표시됩니다. 초대 이메일에는 학생이 랩에 등록하는 데 사용할 수 있는 등록 링크가 포함됩니다.

## <a name="get-the-registration-link"></a>등록 링크 받기

이 섹션에서는 포털에서 등록 링크를 얻고 사용자 고유의 전자 메일 응용 프로그램을 사용하여 보낼 수 있습니다. 

1. **사용자** 창에서 등록 **링크를**선택합니다.

    ![학생 등록 ​링크](../media/how-to-configure-student-usage/registration-link-button.png)

1. 사용자 **등록** 창에서 **복사를**선택한 다음 **완료**를 선택합니다. 

    !["사용자 등록" 창](../media/how-to-configure-student-usage/registration-link.png)

    링크가 클립보드에 복사됩니다. 
    
1. 이메일 신청서에서 등록 링크를 붙여넣은 다음 학생이 수업에 등록할 수 있도록 학생에게 이메일을 보냅니다. 

## <a name="view-registered-users"></a>등록된 사용자 보기

1. Azure 랩 서비스 웹 사이트로 [이동합니다.](https://labs.azure.com) 
1. **로그인을**선택한 다음 자격 증명을 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다.
1. 내 랩 페이지에서 추적할 사용량을 선택한 **랩을 선택합니다.** 
1. 왼쪽 창에서 **사용자를**선택하거나 **사용자** 타일을 선택합니다. 

    **사용자** 창에는 랩에 등록한 학생 목록이 표시됩니다.  

    ![등록된 사용자 목록](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>사용자에 대한 할당량 설정

다음을 수행하여 각 학생에 대해 시간 할당량을 설정할 수 있습니다. 

1. **사용자** 창에서 **사용자당 \<할당량:** 도구 모음에서 시간> 수를 선택합니다. 
1. **사용자당 할당량** 창에서 예약된 수업 시간 외에 각 학생에게 줄 시간을 지정한 다음 **저장을**선택합니다.

    !["사용자당 할당량" 창](../media/how-to-configure-student-usage/quota-per-user.png)    

    변경된 값은 이제 **사용자당 할당량에 \<** 표시됩니다>.

    ![사용자당 할당량 시간](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [VM의 예약된 실행 시간은](how-to-create-schedules.md) 학생에게 할당된 할당량에 포함되지 않습니다. 할당량은 학생이 VM에 지출하는 예약된 시간 이외의 시간에 대한 것입니다. 

## <a name="set-additional-quotas-for-specific-users"></a>특정 사용자에 대한 추가 할당량 설정

이전 섹션의 모든 사용자에 대해 설정된 공통 할당량을 초과하여 특정 학생에 대한 할당량을 지정할 수 있습니다. 예를 들어 교수자로서 모든 학생의 할당량을 10시간으로 설정하고 특정 학생에 대해 5시간의 추가 할당량을 설정하면 해당 학생은 15시간(10+ 5시간)의 할당량을 받습니다. 나중에 공통 할당량을 15로 변경하면 학생은 20시간(15+ 5시간)의 할당량을 받습니다. 이 전체 할당량은 예약된 시간을 벗어났습니다. 학생이 예약된 시간 동안 랩 VM에서 보내는 시간은 이 할당량에 포함되지 않습니다. 

추가 할당량을 설정하려면 다음을 수행합니다.

1. **사용자** 창에서 목록에서 학생을 선택한 다음 도구 모음에서 **할당량 조정을** 선택합니다. 

    !["할당량 조정" 버튼](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. 선택한 **사용자 또는 \<사용자 이메일 주소>adjust 할당량에서 **선택한 학생 또는 학생에게 부여할 추가 랩 시간 수를 입력한 다음 **적용을**선택합니다. 

    !["할당량 조정..." 창](../media/how-to-configure-student-usage/additional-quota.png)

    **사용** 열에는 선택한 학생에 대한 업데이트된 할당량이 표시됩니다. 

    ![사용자에 대한 새로운 사용](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>학생 계정

학생을 교실 랩에 추가하려면 이메일 계정을 사용합니다. 학생은 다음과 같은 유형의 이메일 계정을 가질 수 있습니다.

- Office 365에 대 한 대학의 Azure Active Directory 인스턴스에서 제공 하는 학생 전자 메일 계정입니다. 
- *outlook.com,* *hotmail.com,* *msn.com*또는 *live.com*같은 Microsoft 도메인 전자 메일 계정입니다.
- 야후에서 제공하는 것과 같은 비 마이크로 소프트 이메일 계정! 또는 구글. 그러나 이러한 유형의 계정은 Microsoft 계정과 연결되어야 합니다.
- GitHub 계정. 이 계정은 Microsoft 계정과 연결되어 있어야 합니다.

### <a name="use-a-non-microsoft-email-account"></a>Microsoft 이외의 전자 메일 계정 사용
학생은 Microsoft 이외의 전자 메일 계정을 사용하여 강의실 랩에 등록하고 로그인할 수 있습니다.  그러나 등록을 수행하려면 먼저 Microsoft 이외의 전자 메일 주소에 연결된 Microsoft 계정을 만들어야 합니다.

많은 학생이 이미 Microsoft 이외의 전자 메일 주소에 연결된 Microsoft 계정을 가지고 있을 수 있습니다. 예를 들어, 학생은 Office, Skype, OneDrive 또는 Windows와 같은 다른 Microsoft 제품 또는 서비스와 함께 전자 메일 주소를 사용한 경우 이미 Microsoft 계정을 사용하고 있습니다.  

학생이 등록 링크를 사용하여 교실에 로그인하면 이메일 주소와 비밀번호가 표시됩니다. Microsoft 계정에 연결되지 않은 비 Microsoft 계정으로 로그인을 시도하는 학생에게는 다음과 같은 오류 메시지가 표시됩니다. 

![로그인 시 오류 메시지](../media/how-to-configure-student-usage/cant-find-account.png)

다음은 학생이 Microsoft 계정에 [등록할 수 있는 링크입니다.](http://signup.live.com)  

> [!IMPORTANT]
> 학생이 교실 랩에 로그인하면 Microsoft 계정을 만들 수 있는 옵션이 제공되지 않습니다. 따라서 Microsoft 이외의 계정을 사용하는 학생에게 보내는 http://signup.live.com강의실 랩 등록 전자 메일에 이 등록 링크를 포함하는 것이 좋습니다.

### <a name="use-a-github-account"></a>GitHub 계정 사용
학생들은 기존 GitHub 계정을 사용하여 교실 랩에 등록하고 로그인할 수도 있습니다. 이미 GitHub 계정에 연결된 Microsoft 계정이 있는 경우 학생은 이전 섹션과 같이 로그인하여 암호를 제공할 수 있습니다. 

GitHub 계정을 Microsoft 계정에 아직 연결하지 않은 경우 다음을 수행할 수 있습니다.

1. 다음과 같이 **로그인 옵션** 링크를 선택합니다.

    !["로그인 옵션" 링크](../media/how-to-configure-student-usage/signin-options.png)

1. 로그인 **옵션** 창에서 **GitHub를 사용 하 고 로그인을**선택 합니다.

    !["GitHub로 로그인" 링크](../media/how-to-configure-student-usage/signin-github.png)

    프롬프트에서 학생들은 GitHub 계정에 연결된 Microsoft 계정을 만듭니다. 연결은 **다음**을 선택하면 자동으로 수행됩니다. 그런 다음 즉시 로그인하고 교실 랩에 연결합니다.

## <a name="export-a-list-of-users-to-a-csv-file"></a>사용자 목록을 CSV 파일로 내보내기

1. **사용자** 창으로 이동합니다.
1. 도구 모음에서 타원(...)을 **...** 선택한 다음 **CSV 내보내기를**선택합니다. 

    !["CSV 내보내기" 버튼](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- 관리자: [랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- 랩 소유자: [랩 생성 및 관리](how-to-manage-classroom-labs.md) 및 템플릿 설정 및 [게시](how-to-create-manage-template.md)
- 랩 사용자의 경우: [강의실 랩에 액세스](how-to-use-classroom-lab.md)
