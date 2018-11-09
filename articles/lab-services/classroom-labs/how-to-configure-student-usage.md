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
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: a91d5826f52b2beb05f2d9a08f3646bd776b294e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142891"
---
# <a name="configure-usage-settings-and-policies"></a>사용 설정 및 정책 구성
이 문서에서는 랩의 사용자 수를 구성하고, 사용자를 랩에 등록하도록 하고, VM을 사용할 수 있는 시간을 제어하는 방법 등을 설명합니다. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>랩에 허용되는 사용자 수 지정

1. **사용 정책**을 선택합니다. 
2. **사용 정책**의 설정에서 랩을 사용할 수 있는 **사용자 수**를 입력합니다.
3. **저장**을 선택합니다. 

    ![사용 정책](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-users"></a>사용자에게 등록 링크 보내기

1. **대시보드** 보기로 전환합니다. 
2. **사용자 등록** 타일을 선택합니다.

    ![학생 등록 ​링크](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. **사용자 등록** 대화 상자에서 **복사** 단추를 선택합니다. 링크가 클립보드에 복사됩니다. 이메일 편집기에 붙여넣고 학생에게 이메일을 보냅니다. 학생들은 이 링크를 사용하여 랩에 등록하는 데 도움이 되는 페이지로 이동합니다. 

    ![학생 등록 ​링크](../media/tutorial-setup-classroom-lab/registration-link.png)
2. **사용자 등록** 대화 상자에서 **닫기**를 선택합니다. 

## <a name="view-users-registered-with-the-lab"></a>랩에 등록된 사용자 보기

왼쪽 메뉴에서 **사용자**를 선택하여 랩에 등록된 사용자 목록을 봅니다. 

![랩에 등록된 사용자 목록](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>사용자당 할당량 설정

1. 왼쪽 메뉴에서 **사용자 설정**을 선택합니다.
2. **사용자당 할당량** 타일을 선택합니다. 
3. **사용자가 VM을 사용할 수 있는 시간 제한**을 선택합니다. 
4. **각 사용자에게 시간을 얼마나 제공하시겠습니까?** 에 시간을 입력하고 **저장**을 선택합니다. 

    ![사용자당 시간 수](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. 이제 **사용자당 할당량** 타일에 시간 수가 표시됩니다. 

    ![사용자당 할당량](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="manage-user-vms"></a>사용자 VM 관리
학생이 사용자가 입력한 등록 링크를 사용하여 Azure Lab Services에 등록하면 **가상 머신** 탭에서 학생에게 할당된 VM이 표시됩니다. 

![학생에게 할당된 가상 머신](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

학생 VM에서 다음 작업을 수행할 수 있습니다. 

- VM이 실행 중인 경우 VM 중지 
- VM이 중지된 경우 VM 시작 
- VM에 연결합니다. 
- VM을 삭제합니다. 
- 사용자가 가상 머신을 사용한 시간을 봅니다. 


## <a name="next-steps"></a>다음 단계
Azure Lab Services를 사용하여 랩 설정 시작합니다.

- [클래스룸 랩 설정](how-to-manage-classroom-labs.md)
- [랩 설정](../tutorial-create-custom-lab.md)
