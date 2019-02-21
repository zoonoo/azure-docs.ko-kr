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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 834674eb63af75088434db0f614b11c7a36e7adf
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964817"
---
# <a name="configure-usage-settings-and-policies"></a>사용 설정 및 정책 구성
이 문서에서는 랩에 사용자를 추가하고, 사용자를 랩에 등록하고, VM을 사용할 수 있는 시간을 제어하는 방법 등을 설명합니다. 


## <a name="add-users-to-the-lab"></a>랩에 사용자 추가
**액세스 제한**이 사용되도록 설정되면 목록에 사용자(이메일 주소)를 추가합니다.

1. 왼쪽 메뉴에서 **사용자**를 선택합니다.
2. 도구 모음에서 **사용자 추가**를 선택합니다. 

    ![사용자 추가 단추](../media/how-to-configure-student-usage/add-users-button.png)
1. **사용자 추가** 페이지에서 여러 줄에 걸쳐 또는 세미콜론으로 구분해서 단일 줄에 사용자의 이메일 주소를 입력합니다. 

    ![사용자 이메일 주소 추가](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. **저장**을 선택합니다. 목록에서 사용자의 이메일 주소 및 해당 상태(등록됨 또는 등록 해제됨)를 확인합니다. 

    ![사용자 목록](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="send-registration-link-to-students"></a>학생에게 등록 링크 보내기
다음 절차는 사용자에게 등록 링크를 보내는 단계를 포함합니다. 랩에서 **액세스 제한**을 사용하도록 설정하면 사용자 목록의 사용자만 등록 링크를 사용하여 랩에 등록할 수 있습니다. 

1. 왼쪽 메뉴에서 **사용자**를 선택하여 **사용자** 보기로 전환합니다. 
2. **등록 링크 가져오기** 타일을 선택합니다.

    ![학생 등록 ​링크](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. **사용자 등록** 대화 상자에서 **복사** 단추를 선택합니다. 링크가 클립보드에 복사됩니다. 이메일 편집기에 붙여넣고 학생에게 이메일을 보냅니다. 

    ![학생 등록 ​링크](../media/tutorial-setup-classroom-lab/registration-link.png)
2. **사용자 등록** 대화 상자에서 **닫기**를 선택합니다. 
4. 학생이 강의에 등록할 수 있도록 등록 링크를 학생들과 공유합니다. **제한 옵션** 설정을 사용하도록 설정했으며 목록에 사용자 목록이 있으면 다음 작업을 수행합니다.
    1. 목록에서 사용자의 **이메일 주소**를 선택합니다. 
    2. 기본 이메일 프로그램 창에 **받는 사람** 주소가 표시됩니다. 
    3. 이전에 복사한 **등록 URL**을 붙여 넣습니다. 
    4. **이메일**을 보냅니다. 

## <a name="view-users-registered-with-the-lab"></a>랩에 등록된 사용자 보기

왼쪽 메뉴에서 **사용자**를 선택하여 랩에 등록된 사용자 목록을 봅니다. 

![랩에 등록된 사용자 목록](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>사용자당 할당량 설정
다음 단계를 사용하여 사용자당 할당량을 설정할 수 있습니다. 

1. 왼쪽 메뉴에서 **사용자**를 선택합니다.
2. 도구 모음에서 **사용자당 할당량: 제한 없음**을 선택합니다. 
3. **사용자당 할당량** 페이지에서 다음 옵션 중 하나를 선택합니다. 
    1. **없음**. 사용자는 예약된 시간 동안 또는 랩 소유자가 가상 머신을 사용하도록 설정한 경우에만 가상 머신을 사용할 수 있습니다.
    2. **제한 없음(기본값)**. 사용자가 시간 제한 없이 가상 머신을 사용할 수 있습니다.
    3. **사용자당 시간 수 지정**. 사용자는 예약된 시간 외에도 설정된 시간(아래에 지정) 동안 가상 머신을 사용할 수 있습니다. 이 옵션을 선택하는 경우 텍스트 상자에 **시간**을 입력합니다. 

        ![사용자당 시간 수](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. **저장**을 선택합니다. 
5. 이제 도구 모음에서 변경된 값을 확인할 수 있습니다. **사용자당 할당량: &lt;시간 수&gt;** 

    ![사용자당 할당량](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> [VM의 예약된 실행 시간](how-to-create-schedules.md)은 사용자에게 할당된 할당량에 계산되지 않습니다. 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간입니다. 

### <a name="add-users-by-uploading-a-csv-file"></a>CSV 파일을 업로드하여 사용자 추가
사용자의 이메일 주소가 있는 CSV 파일을 업로드하여 사용자를 추가할 수도 있습니다.

1. 하나의 열에 사용자의 이메일 주소가 포함된 CSV 파일을 만듭니다.

    ![사용자당 할당량](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. 랩의 **사용자** 페이지에서 도구 모음의 **CSV 업로드**를 선택합니다.

    ![CSV 업로드 단추](../media/how-to-configure-student-usage/upload-csv-button.png)
3. 사용자 이메일 주소가 있는 CSV 파일을 선택합니다. CSV 파일을 선택한 후 **열기**를 선택하면 다음 **사용자 추가** 창이 표시됩니다. 이메일 주소 목록은 CSV 파일의 이메일 주소로 채워집니다. 

    ![CSV 파일의 이메일 주소로 채워진 사용자 추가 창](../media/how-to-configure-student-usage/add-users-window.png)
4. **사용자 추가** 창에서 **저장**을 선택합니다. 
5. 사용자 목록에 사용자가 표시되는지 확인합니다. 

    ![추가된 사용자 목록](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>사용자 VM 관리
학생이 사용자가 입력한 등록 링크를 사용하여 Azure Lab Services에 등록하면 **가상 머신** 탭에서 학생에게 할당된 VM이 표시됩니다. 

![학생에게 할당된 가상 머신](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

학생 VM에서 다음 작업을 수행할 수 있습니다. 

- VM이 실행 중인 경우 VM 중지 
- VM이 중지된 경우 VM 시작 
- VM에 연결합니다. 
- VM을 삭제합니다. 
- 사용자가 가상 머신을 사용한 시간을 봅니다. 

## <a name="update-number-of-virtual-machines-in-lab"></a>랩에서 가상 머신 수 업데이트
랩에서 가상 머신의 수를 업데이트하려면 **가상 머신** 페이지에서 다음 단계를 수행합니다.

1. 왼쪽 메뉴에서 **가상 머신**을 선택합니다. 
2. 도구 모음에서 **랩 용량: &lt;number&gt;개 머신**을 선택합니다. 
3. 가상 머신의 **수**를 입력합니다.
4. **저장**을 선택합니다.

    ![랩의 가상 머신](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
