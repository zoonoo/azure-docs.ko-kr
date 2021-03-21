---
title: Azure Lab Services 랩에서 사용 설정 구성
description: 랩에 대 한 학생 수를 구성 하 고, 랩에 등록 하 고, VM을 사용할 수 있는 시간 수를 제어 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 380a587eecb276c457b93ca3c3f3ac08b2239275
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791966"
---
# <a name="add-and-manage-lab-users"></a>랩 사용자 추가 및 관리

이 문서에서는 랩에 학생 사용자를 추가 하 고, 랩에 등록 하 고, VM (가상 머신)을 사용할 수 있는 추가 시간 수를 제어 하는 방법을 설명 합니다. 

사용자를 추가하면 기본적으로 **액세스 제한** 옵션이 설정되며, 사용자 목록에 없는 경우 등록 링크가 있어도 학생은 랩에 등록할 수 없습니다. 나열된 사용자만 보내는 등록 링크를 사용하여 랩에 등록할 수 있습니다. **액세스 제한** 은 해제할 수 있습니다. 이 경우 등록 링크가 있는 한 학생은 랩에 등록할 수 있습니다. 

이 문서에서는 랩에 사용자를 추가 하는 방법을 보여 줍니다.

## <a name="add-users-from-an-azure-ad-group"></a>Azure AD 그룹에서 사용자 추가

### <a name="overview"></a>개요

이제 사용자를 수동으로 추가 하거나 삭제할 필요가 없도록 랩 사용자 목록을 기존 Azure Active Directory (Azure AD) 그룹에 동기화 할 수 있습니다. 

조직 리소스 및 클라우드 기반 앱에 대한 액세스를 관리하기 위해 Azure AD 그룹을 조직의 Azure Active Directory 내에 만들 수 있습니다. 자세한 내용은 [Azure AD 그룹](../active-directory/fundamentals/active-directory-manage-groups.md)을 참조하세요. 조직에서 Microsoft Office 365 또는 Azure 서비스를 사용하는 경우 조직에는 이미 Azure Active Directory를 관리하는 관리자가 있습니다. 

### <a name="sync-users-with-azure-ad-group"></a>Azure AD 그룹과 사용자 동기화

> [!IMPORTANT]
> 사용자 목록이 비어 있어야 합니다. 수동으로 또는 CSV 파일 가져오기를 통해 추가한 랩 내에 기존 사용자가 있는 경우 랩을 기존 그룹에 동기화하는 옵션이 표시되지 않습니다. 

1. [Azure Lab Services 웹 사이트](https://labs.azure.com/)에 로그인합니다.
1. 작업 하려는 랩을 선택 합니다.
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

### <a name="automatic-management-of-virtual-machines-based-on-changes-to-the-azure-ad-group"></a>Azure AD 그룹에 대 한 변경 내용을 기반으로 가상 컴퓨터의 자동 관리 

랩을 Azure AD 그룹에 동기화 한 후에는 랩의 가상 컴퓨터 수가 자동으로 그룹의 사용자 수와 일치 하 게 됩니다. 더 이상 랩 용량을 수동으로 업데이트할 수 없습니다. 사용자가 Azure AD 그룹에 추가 되 면 랩에서 해당 사용자에 대 한 가상 컴퓨터를 자동으로 추가 합니다. Azure AD 그룹에서 사용자를 삭제 하면 랩에서 사용자의 가상 머신이 자동으로 삭제 됩니다. 

## <a name="add-users-manually-from-emails-or-csv-file"></a>이메일 또는 CSV 파일에서 수동으로 사용자 추가

이 섹션에서는 학생을 수동으로(이메일 주소 사용 또는 CSV 파일 업로드) 추가합니다. 

### <a name="add-users-by-email-address"></a>이메일 주소로 사용자 추가

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

### <a name="add-users-by-uploading-a-csv-file"></a>CSV 파일을 업로드하여 사용자 추가

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

## <a name="send-invitations-to-users"></a>사용자에게 초대 보내기

새 사용자에 게 등록 링크를 보내려면 다음 방법 중 하나를 사용 합니다. 

랩에 대 한 **액세스 제한** 옵션을 사용 하도록 설정한 경우 나열 된 사용자만 등록 링크를 사용 하 여 랩에 등록할 수 있습니다. 기본적으로 이 옵션은 사용하도록 설정됩니다. 

### <a name="invite-all-users"></a>모든 사용자 초대

이 방법은 나열 된 모든 학생에 게 등록 링크와 선택적 메시지를 포함 하는 전자 메일을 보내는 방법을 보여 줍니다.

1. **사용자** 창에서 **모두 초대** 를 선택 합니다. 

    !["모두 초대" 단추](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. **전자 메일을 통해 초대 보내기** 창에서 선택적 메시지를 입력 한 다음 **보내기** 를 선택 합니다. 

    이메일에는 등록 링크가 자동으로 포함됩니다. 등록 링크를 별도로 가져오고 저장 하려면 **사용자** 창의 맨 위에 있는 줄임표 (**...**)를 선택 하 고 **등록 링크** 를 선택 합니다. 

    !["전자 메일로 등록 링크 보내기" 창](./media/tutorial-setup-classroom-lab/send-email.png)

    **사용자** 목록의 **초대** 열은 추가 된 각 사용자의 초대 상태를 표시 합니다. 상태는 **전송 중** 으로 변경 된 다음 **에 \<date> 전송** 됩니다. 

### <a name="invite-selected-users"></a>선택한 사용자 초대

이 방법은 특정 학생이 초대 하 고 다른 사용자와 공유할 수 있는 등록 링크를 가져오는 방법을 보여 줍니다.

1. **사용자** 창의 목록에서 학생 또는 여러 학생을 선택 합니다. 

1. 선택한 학생에 대 한 행에서 **봉투** 아이콘을 선택 하거나 도구 모음에서 **초대** 를 선택 합니다. 

    ![선택한 사용자 초대](./media/how-to-configure-student-usage/invite-selected-users.png)

1. **전자 메일을 통해 초대 보내기** 창에서 선택적 **메시지** 를 입력 한 다음 **보내기** 를 선택 합니다. 

    ![선택한 사용자에 게 전자 메일 보내기](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    **사용자** 창에는이 작업의 상태가 테이블의 **초대** 열에 표시 됩니다. 초대 전자 메일은 학생이 랩에 등록 하는 데 사용할 수 있는 등록 링크를 포함 합니다.

## <a name="get-the-registration-link"></a>등록 링크 가져오기

이 섹션에서는 포털에서 등록 링크를 가져와 자신의 전자 메일 응용 프로그램을 사용 하 여 보낼 수 있습니다. 

1. **사용자** 창에서 **등록 링크** 를 선택 합니다.

    ![학생 등록 ​링크](./media/how-to-configure-student-usage/registration-link-button.png)

1. **사용자 등록** 창에서 **복사** 를 선택한 다음 **완료** 를 선택 합니다. 

    !["사용자 등록" 창](./media/how-to-configure-student-usage/registration-link.png)

    링크가 클립보드에 복사됩니다. 
    
1. 전자 메일 응용 프로그램에서 등록 링크를 붙여넣고 학생 들이 클래스에 등록할 수 있도록 전자 메일을 학생에 게 보냅니다. 

## <a name="view-registered-users"></a>등록된 사용자 보기

1. [Azure Lab Services](https://labs.azure.com) 웹 사이트로 이동 합니다. 
1. **로그인** 을 선택 하 고 자격 증명을 입력 합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다.
1. **내 실습** 페이지에서 사용 현황을 추적 하려는 랩을 선택 합니다. 
1. 왼쪽 창에서 **사용자** 를 선택 하거나 **사용자** 타일을 선택 합니다. 

    **사용자** 창에는 랩에 등록 된 학생의 목록이 표시 됩니다.  

    ![등록 된 사용자 목록](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>사용자에 대한 할당량 설정

다음을 수행 하 여 각 학생의 시간 할당량을 설정할 수 있습니다. 

1. **사용자** 창의 도구 모음에서 **사용자 당 할당량: \<number> 시간** 을 선택 합니다. 
1. **사용자 당 할당량** 창에서 각 학생에 게 예약 된 클래스 시간을 초과 하는 시간을 지정 하 고 **저장** 을 선택 합니다.

    !["사용자 당 할당량" 창](./media/how-to-configure-student-usage/quota-per-user.png)    

    이제 다음과 같이 변경 된 값이 도구 모음 및 사용자 목록에 있는 **사용자 당 할당량: \<number of hours>** 단추에 표시 됩니다.

    ![사용자 당 할당량 시간](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Vm의 예약 된 실행 시간은](how-to-create-schedules.md) 학생에 게 할당 된 할당량에 대해 계산 되지 않습니다. 할당량은 학생이 Vm에서 소비한 예약 된 시간을 벗어난 시간에 대 한 것입니다. 

## <a name="set-additional-quotas-for-specific-users"></a>특정 사용자에 대 한 추가 할당량 설정

이전 섹션의 모든 사용자에 대해 설정 된 공통 할당량을 초과 하 여 특정 학생에 대 한 할당량을 지정할 수 있습니다. 예를 들어 강사로 서 모든 학생의 할당량을 10 시간으로 설정 하 고 특정 학생에 대해 5 시간의 추가 할당량을 설정 하는 경우 해당 학생은 15 시간 (10 + 5)의 할당량을 얻습니다. 나중에 일반 할당량을 15로 변경 하는 경우 (예를 들어 15) 학생은 20 (15 + 5)의 할당량을 얻습니다. 이 전체 할당량은 예약 된 시간을 벗어납니다. 예약 된 시간 동안 학생이 랩 VM에서 소비한 시간은이 할당량에 대해 계산 되지 않습니다. 

추가 할당량을 설정 하려면 다음을 수행 합니다.

1. **사용자** 창의 목록에서 학생을 선택 하 고 도구 모음에서 **할당량 조정** 을 선택 합니다. 

    !["할당량 조정" 단추](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. **할당량 조정 대상 \<selected user or users email address>** 에 선택한 학생 또는 학생 들에 게 부여할 추가 랩 시간 수를 입력 한 다음 **적용** 을 선택 합니다. 

    !["할당량 조정 ..." 창인](./media/how-to-configure-student-usage/additional-quota.png)

    **사용** 열에는 선택한 학생의 업데이트 된 할당량이 표시 됩니다. 

    ![사용자에 대 한 새로운 사용](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>학생 계정

교실 랩에 학생을 추가 하려면 전자 메일 계정을 사용 합니다. 학생 들에 게는 다음과 같은 유형의 전자 메일 계정이 있을 수 있습니다.

- 대학 Azure Active Directory 인스턴스에서 제공 하는 학생 전자 메일 계정입니다.
- Microsoft 도메인 전자 메일 계정 (예: *outlook.com*, *hotmail.com*, *msn.com* 또는 *live.com*)
- Yahoo!에서 제공 하는 것과 같은 타사 전자 메일 계정 또는 Google. 그러나 이러한 유형의 계정은 Microsoft 계정 연결 해야 합니다.
- GitHub 계정. 이 계정은 Microsoft 계정 연결 해야 합니다.

### <a name="use-a-non-microsoft-email-account"></a>타사 전자 메일 계정 사용

학생 들은 Microsoft 이외의 전자 메일 계정을 사용 하 여 교실 랩에 등록 하 고 로그인 할 수 있습니다.  그러나 등록 하려면 먼저 타사 전자 메일 주소에 연결 된 Microsoft 계정를 만들어야 합니다.

많은 학생 들에 게 Microsoft 이외의 전자 메일 주소에 연결 된 Microsoft 계정 이미 있을 수 있습니다. 예를 들어 학생 들에 게는 다른 Microsoft 제품 또는 서비스 (예: Office, Skype, OneDrive, Windows)와 전자 메일 주소를 사용한 경우 이미 Microsoft 계정 있습니다.  

학생이 등록 링크를 사용 하 여 교실에 로그인 하는 경우 전자 메일 주소 및 암호를 입력 하 라는 메시지가 표시 됩니다. Microsoft 계정에 연결 되지 않은 Microsoft 계정를 사용 하 여 로그인 하려고 시도 하는 학생이 다음 오류 메시지를 받게 됩니다. 

![로그인 시 오류 메시지](./media/how-to-configure-student-usage/cant-find-account.png)

학생 들이 [Microsoft 계정에 등록](http://signup.live.com)하는 링크는 다음과 같습니다.  

> [!IMPORTANT]
> 학생 들이 교실 랩에 로그인 할 때 Microsoft 계정을 만들 수 있는 옵션이 제공 되지 않습니다. 따라서 http://signup.live.com Microsoft가 아닌 계정을 사용 하는 학생 들에 게 보내는이 등록 링크를 교실 랩 등록 전자 메일에 포함 하는 것이 좋습니다.

### <a name="use-a-github-account"></a>GitHub 계정 사용

학생은 기존 GitHub 계정을 사용 하 여 교실 랩에 등록 하 고 로그인 할 수도 있습니다. GitHub 계정에 연결 된 Microsoft 계정 이미 있는 경우 학생 들은 이전 섹션에 표시 된 대로 로그인 하 고 암호를 제공할 수 있습니다. 

아직 GitHub 계정을 Microsoft 계정에 연결 하지 않은 경우 다음을 수행할 수 있습니다.

1. 다음과 같이 **로그인 옵션** 링크를 선택 합니다.

    !["로그인 옵션" 링크](./media/how-to-configure-student-usage/signin-options.png)

1. **로그인 옵션** 창에서 **GitHub를 사용 하 여 로그인** 을 선택 합니다.

    !["GitHub를 사용 하 여 로그인" 링크](./media/how-to-configure-student-usage/signin-github.png)

    프롬프트에서 학생은 GitHub 계정에 연결 된 Microsoft 계정를 만듭니다. **다음** 을 선택 하면 연결이 자동으로 수행 됩니다. 그런 다음 즉시 로그인 하 여 교실 랩에 연결 됩니다.

## <a name="export-a-list-of-users-to-a-csv-file"></a>CSV 파일로 사용자 목록 내보내기

1. **사용자** 창으로 이동 합니다.
1. 도구 모음에서 줄임표 (**...**)를 선택한 다음, **CSV 내보내기** 를 선택 합니다. 

    !["CSV 내보내기" 단추](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

- 관리자: [랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- 랩 소유자: 랩 [만들기 및 관리](how-to-manage-classroom-labs.md) 및 [템플릿 설정 및 게시](how-to-create-manage-template.md)
- 랩 사용자: [액세스 랩](how-to-use-classroom-lab.md)