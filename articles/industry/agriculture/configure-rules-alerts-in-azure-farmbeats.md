---
title: 규칙 구성 및 경고 관리
description: FarmBeats에서 규칙을 구성 하 고 경고를 관리 하는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75482986"
---
# <a name="configure-rules-and-manage-alerts"></a>규칙 구성 및 경고 관리

Azure FarmBeats를 사용 하면 팜에 배포 된 센서 및 장치에서 흐르는 센서 데이터 외에 비즈니스 논리를 기반으로 하는 규칙을 만들 수 있습니다. 센서 값이 임계값을 초과 하는 경우 규칙이 시스템에서 경고를 트리거합니다. 임계값 이후에 생성 된 경고를 확인 하 고 분석 하 여 신속 하 게 문제를 해결 하 고 필요한 솔루션을 얻을 수 있습니다.

## <a name="create-rule"></a>규칙 만들기

1. 홈 페이지에서 **규칙**으로 이동 합니다.
2. **새 규칙**을 선택 합니다. 새 규칙 창이 표시 됩니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. **규칙 이름** 및 **규칙 설명을** 입력 한 다음 **팜 선택** 드롭다운 메뉴에서 팜을 선택 합니다.
4. 팜 이름을 입력 하 여 동일한 창에 표시 되는 팜 및 **조건** 섹션을 선택 합니다.  

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. **조건**에 **측정값**, **연산자** 및 **값**에 대 한 값을 입력 합니다.
6. **측정값** 드롭다운 메뉴에 측정값 이름을 입력 합니다.
7. **+ 조건 추가** 를 선택 하 여 규칙에 조건을 더 추가 합니다.
8. **심각도 수준을**선택 합니다.
9. **작업**에서 전자 메일 **사용** 토글 단추를 전환 하 여 전자 메일 경고를 사용 하도록 설정 합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. 전자 메일 경고를 보낼 전자 메일 **주소** 와 전자 메일 **제목** 및 **추가 메모**를 입력 합니다.  
11. **규칙 상태**에서 **사용** 토글 단추를 전환 하 여 규칙을 사용 하거나 사용 하지 않도록 설정 합니다.
    규칙의 영향을 받는 장치의 수를 볼 수 있습니다.
12. **적용** 을 선택 하 여 규칙을 만듭니다.

## <a name="view-rule"></a>규칙 보기

**팜** 페이지에는 사용 가능한 규칙 목록이 표시 됩니다. **규칙 이름을**선택 합니다. 창에는 선택한 규칙에 적용 되는 다음과 같은 세부 정보가 표시 됩니다.
 - 규칙 이름
 - 규칙이 연결 된 팜에 대 한 링크입니다.
 - 만든 날짜
 - 마지막 업데이트 날짜
 - 심각도 수준
 - 규칙 상태
 - 조건 목록  
 - 규칙의 영향을 받는 장치 수

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>규칙 편집

규칙을 편집 하려면 다음 단계를 수행 합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **규칙** 을 선택 합니다.
   규칙 창이 표시 됩니다.
2. 편집 하려는 규칙을 선택 합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. 작업 모음에서 **편집** 을 선택 하면 **규칙 편집** 창이 표시 됩니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. **규칙 이름**및 **규칙 설명을** 변경한 다음 **팜 선택** 드롭다운 메뉴에서 팜을 선택 합니다.
5. 팜 이름을 입력 하 여 동일한 창 **에 표시 되** 는 팜을 선택 합니다.  
6. **조건**에서 **측정값**, **연산자** 및 **값**을 편집 합니다.
7. **측정값** 드롭다운 메뉴에 측정값 이름을 입력 합니다.
8. **+ 조건 추가** 를 선택 하 여 규칙에 조건을 추가/편집 합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  **심각도 수준을**선택 합니다.  
10. **작업**에서 전자 메일 **사용** 토글 단추를 전환 하 여 전자 메일 경고를 사용 하도록 설정 합니다.
11. 전자 메일 **제목** 및 **추가 메모**와 함께 전자 메일 경고를 보낼 **전자 메일 주소** 를 편집 합니다.  
12. **규칙 상태**에서 **사용** 토글 단추를 전환 하 여 규칙을 사용 하거나 사용 하지 않도록 설정 합니다.
이 규칙의 영향을 받는 장치의 수를 볼 수 있습니다.
13. **적용** 을 선택 하 여 규칙을 편집 합니다.

## <a name="change-rule-status"></a>규칙 상태 변경

규칙의 상태를 변경 하려면 다음 단계를 수행 합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **규칙** 을 선택 합니다. 규칙 창이 표시 됩니다.
2. 상태를 변경 하려는 규칙을 선택 합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. 작업 모음에서 **상태 변경** 을 선택 합니다. **상태 변경** 창이 표시 됩니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. **상태 변경** 설정/해제 단추를 사용 하 여 규칙 상태를 변경 합니다.
   규칙의 영향을 받는 장치의 수를 볼 수 있습니다.
4. **적용** 을 선택 하 여 규칙 상태를 변경 합니다.

## <a name="delete-rule"></a>규칙 삭제

규칙을 삭제 하려면 다음 단계를 수행 합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **규칙** 을 선택 합니다. 규칙 창이 표시 됩니다.
2. 삭제 하려는 규칙을 선택 합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. 작업 모음에서 **삭제** 를 선택 합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. **규칙 삭제** 대화 상자가 표시 됩니다. **삭제**를 선택합니다.
