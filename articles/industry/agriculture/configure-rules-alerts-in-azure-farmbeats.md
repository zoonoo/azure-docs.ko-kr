---
title: 규칙 구성 및 경고 관리
description: FarmBeats에서 규칙을 구성하고 경고를 관리하는 방법을 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482986"
---
# <a name="configure-rules-and-manage-alerts"></a>규칙 구성 및 경고 관리

Azure FarmBeats를 사용하면 팜에 배포된 센서 및 장치에서 흐르는 센서 데이터 외에도 비즈니스 논리를 기반으로 규칙을 만들 수 있습니다. 규칙은 센서 값이 임계값을 초과할 때마다 시스템에서 경고를 트리거합니다. 임계값 이후에 생성된 경고를 보고 분석하면 모든 문제에 대해 신속하게 조치를 조치를 취하지 않고 필요한 솔루션을 얻을 수 있습니다.

## <a name="create-rule"></a>규칙 만들기

1. 홈 페이지에서 **규칙**으로 이동합니다.
2. **새 규칙을**선택합니다. 새 규칙 창이 표시됩니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. 규칙 **이름** 및 **규칙 설명을** 입력한 다음 팜 선택 드롭다운 메뉴에서 **팜을** 선택합니다.
4. 팜 이름을 입력하여 팜을 선택하고 **조건** 섹션이 동일한 창에 나타납니다.  

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. **조건에서** **측정값,** **연산자** 및 **값에**대한 값을 입력합니다.
6. **측정값** 드롭다운 메뉴에 측정값 이름을 입력합니다.
7. 규칙에 조건을 더 추가하려면 **조건 추가를 선택합니다.**
8. **심각도 수준을**선택합니다.
9. **작업**에서, 이메일 알림을 활성화하려면 **전자 메일 사용** 토글 버튼을 켭타.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. 이메일 제목 및 **추가 메모와**함께 전자 메일 알림을 보낼 **이메일** **주소를** 입력합니다.  
11. 규칙 **상태에서** **사용 토글** 단추를 켜서 규칙을 사용하거나 사용하지 않도록 설정합니다.
    규칙의 영향을 받는 장치 수를 볼 수 있습니다.
12. 규칙을 만들려면 **적용을** 선택합니다.

## <a name="view-rule"></a>규칙 보기

**팜** 페이지에사용 가능한 규칙 목록이 표시됩니다. 규칙 **이름을**선택합니다. 창에는 선택한 규칙에 적용할 수 있는 다음 세부 정보가 표시됩니다.
 - 규칙 이름
 - 규칙이 연결된 팜에 대한 링크
 - 만든 날짜
 - 마지막 업데이트 날짜
 - 심각도 수준
 - 규칙 상태
 - 조건 목록  
 - 규칙의 영향을 받는 장치 수

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>규칙 편집

규칙을 편집하려면 다음 단계를 따르십시오.

1. 홈 페이지에서 왼쪽 탐색 메뉴에서 **규칙을** 선택합니다.
   규칙 창이 표시됩니다.
2. 편집할 규칙을 선택합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. 작업 표시줄에서 **편집을** 선택하면 **규칙 편집** 창이 표시됩니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. 규칙 **이름**및 **규칙 설명을** 변경한 다음 팜 선택 드롭다운 메뉴에서 **팜을** 선택합니다.
5. 팜 이름을 입력하여 팜을 선택하고 **조건이** 동일한 창에 나타납니다.  
6. **조건에서** **측정값,** **연산자** 및 **값을**편집합니다.
7. **측정값** 드롭다운 메뉴에 측정값 이름을 입력합니다.
8. **+조건 추가를** 선택하여 조건에 조건을 추가/편집합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  **심각도 수준을 선택합니다.**  
10. **작업**에서, 이메일 알림을 활성화하려면 **전자 메일 사용** 토글 버튼을 켭타.
11. 전자 메일 제목 및 **추가 메모와**함께 전자 메일 경고를 보낼 **전자** 메일 **주소를** 편집합니다.  
12. 규칙 **상태에서** **사용 토글** 단추를 켜서 규칙을 사용하거나 사용하지 않도록 설정합니다.
이 규칙의 영향을 받는 장치 수를 볼 수 있습니다.
13. 규칙을 편집하려면 **적용을** 선택합니다.

## <a name="change-rule-status"></a>규칙 상태 변경

규칙의 상태를 변경하려면 다음 단계를 따르십시오.

1. 홈 페이지에서 왼쪽 탐색 메뉴에서 **규칙을** 선택합니다. 규칙 창이 표시됩니다.
2. 상태를 변경할 규칙을 선택합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. 작업 표시줄에서 **상태 변경을** 선택합니다. **상태 변경** 창이 표시됩니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. **상태 변경** 토글 단추를 사용하여 규칙 상태를 변경합니다.
   규칙의 영향을 받는 장치 수를 볼 수 있습니다.
4. 규칙 상태를 변경하려면 **적용을** 선택합니다.

## <a name="delete-rule"></a>규칙 삭제

규칙을 삭제하려면 다음 단계를 따르십시오.

1. 홈 페이지에서 왼쪽 탐색 메뉴에서 **규칙을** 선택합니다. 규칙 창이 표시됩니다.
2. 삭제할 규칙을 선택합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. 작업 표시줄에서 **삭제를** 선택합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. **규칙 삭제** 대화 상자가 표시됩니다. **삭제를 선택합니다.**
