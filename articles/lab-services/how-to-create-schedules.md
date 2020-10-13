---
title: Azure Lab Services에서 Classroom(강의실) 랩의 일정 만들기 | Microsoft Docs
description: 랩의 VM이 지정된 시간에 시작 및 종료되도록 Azure Lab Services에서 Classroom(강의실) 랩의 일정을 만드는 방법을 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4eb4e2459069b61990f51f8d8de76522b736723e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326442"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Azure Lab Services에서 Classroom(강의실) 랩의 일정 만들기 및 관리 
일정을 사용하면 랩의 VM이 지정된 시간에 자동으로 시작 및 종료되도록 Classroom(강의실) 랩을 구성할 수 있습니다. 일회성 일정이나 되풀이 일정을 정의할 수 있습니다. 다음 절차에서는 Classroom(강의실) 랩의 일정을 만들고 관리하는 단계를 제공합니다. 

> [!IMPORTANT]
> VM의 예약된 실행 시간은 [사용자에게 할당된 할당량](how-to-configure-student-usage.md#set-quotas-for-users)에 계산되지 않습니다. 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간을 반영합니다. 

## <a name="set-a-schedule-for-the-lab"></a>랩에 대한 일정 설정
랩의 VM이 특정 시간에 자동으로 시작/중지되도록 랩에 예약된 이벤트를 만듭니다. 앞에서 지정한 사용자 할당량은 이 예약된 시간 이외의 시간으로 각 사용자에게 할당된 추가 시간입니다. 

> [!NOTE]
> 시작 하기 전에 일정은 랩 가상 컴퓨터에 영향을 주는 방법은 다음과 같습니다. 
>- 템플릿 가상 머신은 일정에 포함 되지 않습니다. 
>- 할당 된 가상 머신만 시작 됩니다. 즉, 학생 (최종 사용자)이 컴퓨터를 요구 하지 않는 경우 컴퓨터가 예약 된 시간에 시작 되지 않습니다. 
>- 모든 가상 컴퓨터 (사용자의 요구 여부에 상관 없음)는 랩 일정에 따라 중지 됩니다. 

1. **일정** 페이지로 전환한 다음, 도구 모음에서 **예약된 이벤트 추가**를 선택합니다. 

    ![일정 추가 단추가 선택 된 상태에서 Azure Lab Services "일정" 페이지를 보여 주는 스크린샷](./media/how-to-create-schedules/add-schedule-button.png)
2. **이벤트 유형**에 대해 **표준**이 선택되었는지 확인합니다. VM의 시작 시간만 지정하려면 **시작만**을 선택합니다. VM의 중지 시간만 지정하려면 **중지만**을 선택합니다. 
7. **반복** 섹션에서 현재 일정을 선택합니다. 

    ![일정 페이지의 일정 추가 단추](./media/how-to-create-schedules/select-current-schedule.png)
5. **반복** 대화 상자에서 다음 단계를 수행합니다.
    1. **반복** 필드에 대해 **매주**가 설정되었는지 확인합니다. 
    3. **시작 날짜**를 지정합니다.
    4. VM을 시작할 **시작 시간**을 지정합니다.
    5. VM을 종료할 **중지 시간**을 지정합니다. 
    6. 지정한 시작 및 중지 시간에 대한 **표준 시간대**를 지정합니다. 
    2. 일정을 적용하려는 요일을 선택합니다. 다음 예에서는 월요일-목요일이 선택되었습니다. 
    8. **저장**을 선택합니다. 

        ![반복 일정 설정](./media/how-to-create-schedules/set-repeat-schedule.png)

3. 이제 **예약된 이벤트 추가** 페이지의 **메모(선택 사항)** 에서 예약에 대한 설명 또는 메모를 입력합니다. 
4. **예약된 이벤트 추가** 페이지에서 **저장**을 선택합니다. 

    ![주별 일정](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>달력에서 일정 보기
다음 이미지와 같이 예약된 날짜 및 시간이 달력 보기에서 강조 표시된 것을 확인할 수 있습니다.

![달력 보기의 일정](./media/how-to-create-schedules/schedules-calendar.png)

오른쪽 위에 있는 **오늘** 단추를 선택하여 달력에서 현재 날짜로 전환합니다. **왼쪽 화살표**를 선택하면 이전 주로 전환되고, **오른쪽 화살표**를 선택하면 달력에서 다음 주로 전환됩니다. 

## <a name="edit-a-schedule"></a>일정 편집
일정에서 강조 표시 된 일정을 선택 하면 일정을 **편집** 하거나 **삭제** 하는 단추가 표시 됩니다. 

![일정 편집 페이지](./media/how-to-create-schedules/schedule-edit-button.png)

예약 된 **이벤트 편집** 페이지에서 일정을 업데이트 하 고 **저장**을 선택할 수 있습니다. 

## <a name="delete-a-schedule"></a>일정 삭제

1. 일정을 삭제 하려면 달력에서 강조 표시 된 일정을 선택 하 고 휴지통 아이콘 (삭제) 단추를 선택 합니다.

    ![도구 모음의 삭제 단추](./media/how-to-create-schedules/schedule-delete-button.png)
2. 예약 된 **이벤트 삭제** 대화 상자에서 **예** 를 선택 하 여 삭제를 확인 합니다. 



## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
