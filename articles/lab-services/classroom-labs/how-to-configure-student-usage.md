---
title: Azure Lab Services의 클래스룸 랩에서 사용 설정 구성 | Microsoft Docs
description: 랩의 사용자 수를 구성하고, 사용자를 랩에 등록하도록 하고, VM을 사용할 수 있는 시간을 제어하는 방법 등을 알아봅니다.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 691907d1c221283f99ba59f0937cfbaf673f427a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324534"
---
# <a name="add-and-manage-lab-users"></a>랩 사용자 추가 및 관리
이 문서에서는 랩에 사용자를 추가하고, 사용자를 랩에 등록하고, VM을 사용할 수 있는 시간을 제어하는 방법 등을 설명합니다. 


## <a name="add-users-to-the-lab"></a>랩에 사용자 추가

1. 왼쪽 메뉴에서 **사용자**를 선택합니다. 기본적으로 **액세스 제한** 옵션이 사용되도록 설정됩니다. 이 설정이 켜져 있으면 사용자가 사용자 목록에 없으면 등록 링크가 있어도 랩에 등록할 수 없습니다. 목록의 사용자만 전송 받은 등록 링크를 사용하여 랩에 등록할 수 있습니다. 이 절차에서는 사용자를 목록에 추가합니다. **액세스 제한**을 끌 수도 있습니다. 이 경우 사용자는 등록 링크가 있기만 하면 랩에 등록할 수 있습니다. 
2. 도구 모음에서 **사용자 추가** 를 선택한 다음 **전자 메일 주소로 추가**를 선택 합니다. 

    ![사용자 추가 단추](../media/how-to-configure-student-usage/add-users-button.png)
1. **사용자 추가** 페이지에서 여러 줄에 걸쳐 또는 세미콜론으로 구분해서 단일 줄에 사용자의 이메일 주소를 입력합니다. 

    ![사용자 이메일 주소 추가](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. **저장**을 선택합니다. 목록에서 사용자의 이메일 주소 및 해당 상태(등록됨 또는 등록 해제됨)를 확인합니다. 

    ![사용자 목록](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>CSV 파일을 업로드하여 사용자 추가
사용자의 이메일 주소가 있는 CSV 파일을 업로드하여 사용자를 추가할 수도 있습니다.

1. 하나의 열에 사용자의 이메일 주소가 포함된 CSV 파일을 만듭니다.

    ![사용자가 포함 된 CSV 파일](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. 랩의 **사용자** 페이지에서 도구 모음에 있는 **사용자 추가** 를 선택한 다음 **CSV 업로드**를 선택 합니다.

    ![CSV 업로드 단추](../media/how-to-configure-student-usage/upload-csv-button.png)
3. 사용자 이메일 주소가 있는 CSV 파일을 선택합니다. CSV 파일을 선택한 후 **열기**를 선택하면 다음 **사용자 추가** 창이 표시됩니다. 이메일 주소 목록은 CSV 파일의 이메일 주소로 채워집니다. 

    ![CSV 파일의 이메일 주소로 채워진 사용자 추가 창](../media/how-to-configure-student-usage/add-users-window.png)
4. **사용자 추가** 창에서 **저장**을 선택합니다. 
5. 사용자 목록에 사용자가 표시되는지 확인합니다. 

    ![추가된 사용자 목록](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>사용자에 게 초대 보내기
학생에 게 등록 링크를 보내려면 다음 방법 중 하나를 사용 합니다. 첫 번째 방법은 등록 링크와 선택적 메시지를 사용 하 여 학생에 게 전자 메일을 보내는 방법을 보여 줍니다. 두 번째 방법에서는 원하는 방식으로 다른 사용자와 공유할 수 있는 등록 링크를 가져오는 방법을 보여 줍니다. 

랩에서 **액세스 제한**을 사용하도록 설정하면 사용자 목록의 사용자만 등록 링크를 사용하여 랩에 등록할 수 있습니다. 이 옵션은 기본적으로 사용 하도록 설정 되어 있습니다. 

### <a name="invite-all-users"></a>모든 사용자 초대

1. 랩의 **사용자** 페이지로 전환 합니다. 
2. 도구 모음에서 **모두 초대** 를 선택 합니다. 
3. 사용자에 게 **메시지** 를 입력 합니다. 선택적 단계입니다.
4. 그런 다음 **보내기**를 선택 합니다.

    ![모든 사용자 초대](../media/how-to-configure-student-usage/invite-all.png)

    **사용자** 목록의 **초대** 열에이 작업의 상태가 표시 됩니다. 초대 전자 메일에는 사용자가 랩에 등록 하는 데 사용할 수 있는 등록 링크가 포함 됩니다. 

### <a name="invite-selected-users"></a>선택한 사용자 초대

1. 목록에서 사용자 또는 여러 사용자를 선택 합니다. 
2. 그런 다음 선택한 행에 표시 되는 **봉투 (envelope** ) 아이콘을 선택 하거나 도구 모음에서 **초대** 를 선택 합니다. 

    ![선택한 사용자 초대](../media/how-to-configure-student-usage/invite-selected-users.png)
3. **전자 메일을 통해 초대 보내기** 창에서 선택적 **메시지**를 입력 한 다음 **보내기**를 선택 합니다. 

    ![선택한 사용자에 게 전자 메일 보내기](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    **사용자** 목록의 **초대** 열에이 작업의 상태가 표시 됩니다. 초대 전자 메일에는 사용자가 랩에 등록 하는 데 사용할 수 있는 등록 링크가 포함 됩니다.

1. 페이지에 아직 표시되지 않으면 **사용자** 보기로 전환합니다. 

## <a name="get-registration-link"></a>등록 링크 가져오기
포털에서 등록 링크를 가져와 자신의 전자 메일 클라이언트 응용 프로그램을 사용 하 여 보낼 수도 있습니다. 

1. 왼쪽 메뉴에서 **사용자**를 선택하여 **사용자** 보기로 전환합니다. 
2. ... **를 선택 합니다. (줄임표)** 도구 모음에서 **등록 링크**를 선택 합니다.

    ![학생 등록 링크](../media/how-to-configure-student-usage/registration-link-button.png)
1. **사용자 등록** 대화 상자에서 **복사** 단추를 선택 합니다. 링크가 클립보드에 복사됩니다. 이메일 편집기에 붙여넣고 학생에게 이메일을 보냅니다. 

    ![학생 등록 링크](../media/how-to-configure-student-usage/registration-link.png)
2. **사용자 등록** 대화 상자에서 **완료**를 선택 합니다. 
4. 학생 들이 클래스에 등록할 수 있도록 **등록 링크** 를 학생에 게 보냅니다. 

## <a name="view-users-registered-with-the-lab"></a>랩에 등록된 사용자 보기

왼쪽 메뉴에서 **사용자**를 선택하여 랩에 등록된 사용자 목록을 봅니다. 

![랩에 등록된 사용자 목록](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>사용자에 대한 할당량 설정
다음 단계를 사용하여 사용자당 할당량을 설정할 수 있습니다. 

1. 페이지가 아직 활성화되지 않은 경우 왼쪽 메뉴에서 **사용자**를 선택합니다. 
2. 도구 모음에서 **사용자 당 할당량 (&lt;number @ no__t-2 시간)** 을 선택 합니다. 
3. **사용자 당 할당량** 페이지에서 예약 된 클래스 시간 외에 각 사용자 (학생)에 게 제공할 시간 수를 지정한 다음 **저장**을 선택 합니다.

    ![사용자당 할당량](../media/how-to-configure-student-usage/quota-per-user.png)    
5. 이제 도구 모음에 변경 된 값이 표시 됩니다. **사용자 당 할당량: &lt;number number @ no__t-2**. 

    ![사용자 당 할당량-이후](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [VM의 예약된 실행 시간](how-to-create-schedules.md)은 사용자에게 할당된 할당량에 계산되지 않습니다. 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간을 반영합니다. 

## <a name="set-additional-quota-for-a-specific-user"></a>특정 사용자에 대 한 추가 할당량 설정
사용자에 대 한 별도의 할당량을 설정할 수 있습니다. 이렇게 하려면 다음 단계를 따르십시오.

1. **사용자 페이지의** 사용자 목록에서 사용자 (학생)를 선택 합니다.
2. 그런 다음 도구 모음에서 **할당량 조정** 을 선택 합니다. 

    ![할당량 조정 단추](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. 선택한 사용자에 대 한 **추가 시간** 을 입력 한 다음 **적용**을 선택 합니다. 

    ![사용자에 대 한 추가 할당량](../media/how-to-configure-student-usage/additional-quota.png)
4. **사용** 열에 사용자에 대 한 업데이트 된 사용이 표시 됩니다. 

    ![사용자에 대 한 새로운 사용](../media/how-to-configure-student-usage/new-usage-hours.png)


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
