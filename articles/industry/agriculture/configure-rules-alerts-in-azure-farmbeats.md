---
title: 규칙 구성 및 경고 관리
description: FarmBeats에서 규칙을 구성하고 경고를 관리하는 방법을 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: b9110d522d6b7b07549d022be5c67f204224cd0d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108751412"
---
# <a name="configure-rules-and-manage-alerts"></a>규칙 구성 및 경고 관리

Azure FarmBeats를 사용하면 팜에 배포된 센서 및 디바이스에서 흐르는 센서 데이터 이외에도 비즈니스 논리를 기반으로 규칙을 만들 수 있습니다. 규칙은 센서 값이 임계값을 초과할 때마다 시스템에서 경고를 트리거합니다. 임계값을 초과한 후 생성된 경고를 확인하고 분석하면 문제에 대해 신속하게 조치를 취하고 필요한 솔루션을 얻을 수 있습니다.

## <a name="create-rule"></a>규칙 만들기

1. 홈페이지에서 **규칙** 으로 이동합니다.
2. **새 규칙** 을 선택합니다. 새 규칙 창이 표시됩니다.

    ![새 규칙 단추 및 새 규칙 섹션을 강조 표시한 스크린샷](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. **규칙 이름** 및 **규칙 설명** 을 입력한 다음, **팜 선택** 드롭다운 메뉴에서 팜을 선택합니다.
4. 팜 이름을 입력하여 팜을 선택하면 동일한 창에 **조건** 섹션이 나타납니다.  

    ![조건 섹션을 강조 표시하는 스크린샷](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. **조건** 에서 **측정값**, **연산자** 및 **값** 에 해당하는 값을 입력합니다.
6. **측정값** 드롭다운 메뉴에 측정값 이름을 입력합니다.
7. **+조건 추가** 를 선택하여 규칙에 조건을 더 추가합니다.
8. **심각도 수준** 을 선택합니다.
9. **작업** 에서 **이메일 사용** 토글 단추를 전환하여 이메일 경고를 사용하도록 설정합니다.

    ![이메일 사용 옵션을 보여주는 스크린샷](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. 이메일 경고를 보낼 **이메일 주소** 와 **이메일 제목** 및 **추가 메모** 를 입력합니다.  
11. **규칙 상태** 에서 **사용** 토글 단추를 전환하여 규칙을 사용하거나 사용하지 않도록 설정합니다.
    규칙의 영향을 받는 디바이스 수를 볼 수 있습니다.
12. **적용** 을 선택하여 규칙을 만듭니다.

## <a name="view-rule"></a>규칙 보기

**팜** 페이지에 사용 가능한 규칙 목록이 표시됩니다. **규칙 이름** 을 선택합니다. 창에는 선택한 규칙에 적용되는 다음과 같은 세부 정보가 표시됩니다.
 - 규칙 이름
 - 규칙이 연결된 팜에 대한 링크
 - 만든 날짜
 - 마지막 업데이트 날짜
 - 심각도 수준
 - 규칙 상태
 - 조건 목록  
 - 규칙의 영향을 받는 디바이스 수

    ![규칙 세부 정보를 보여주는 스크린샷](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>규칙 편집

규칙을 편집하려면 다음 단계를 수행하세요.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **규칙** 을 선택합니다.
   규칙 창이 표시됩니다.
2. 편집할 규칙을 선택합니다.

    ![선택한 규칙을 보여주는 스크린샷](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. 작업 표시줄에서 **편집** 을 선택하면 **규칙 편집** 창이 표시됩니다.

    ![규칙 편집 화면을 보여주는 스크린샷](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. **규칙 이름** 및 **규칙 설명** 을 변경한 다음, **팜 선택** 드롭다운 메뉴에서 팜을 선택합니다.
5. 팜 이름을 입력하여 팜을 선택하면 동일한 창에 **조건** 이 나타납니다.  
6. **조건** 에서 **측정값**, **연산자**, **값** 을 편집합니다.
7. **측정값** 드롭다운 메뉴에 측정값 이름을 입력합니다.
8. **+조건 추가** 를 선택하여 규칙에 조건을 추가/편집합니다.

    ![조건 추가 단추를 강조 표시하는 스크린샷](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  **심각도 수준** 을 선택합니다.  
10. **작업** 에서 **이메일 사용** 토글 단추를 전환하여 이메일 경고를 사용하도록 설정합니다.
11. 이메일 경고를 보낼 **이메일 주소** 와 **이메일 제목** 및 **추가 메모** 를 편집합니다.  
12. **규칙 상태** 에서 **사용** 토글 단추를 전환하여 규칙을 사용하거나 사용하지 않도록 설정합니다.
규칙의 영향을 받는 디바이스 수를 볼 수 있습니다.
13. **적용** 을 선택하여 규칙을 편집합니다.

## <a name="change-rule-status"></a>규칙 상태 변경

규칙의 상태를 변경하려면 다음 단계를 수행하세요.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **규칙** 을 선택합니다. 규칙 창이 표시됩니다.
2. 상태를 변경할 규칙을 선택합니다.

    ![상태 변경 단추를 보여주는 스크린샷](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. 작업 표시줄에서 **상태 변경** 을 선택합니다. **상태 변경** 창이 표시됩니다.

    ![상태 변경 화면을 보여주는 스크린샷](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. **상태 변경** 토글 단추를 사용하여 규칙 상태를 변경합니다.
   규칙의 영향을 받는 디바이스 수를 볼 수 있습니다.
4. **적용** 을 선택하여 규칙 상태를 변경합니다.

## <a name="delete-rule"></a>규칙 삭제

규칙을 삭제하려면 다음 단계를 수행하세요.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **규칙** 을 선택합니다. 규칙 창이 표시됩니다.
2. 삭제할 규칙을 선택합니다.

    ![삭제 단추를 강조 표시하는 스크린샷](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. 작업 표시줄에서 **삭제** 를 선택합니다.

    ![Farm Beats 프로젝트](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. **규칙 삭제** 대화 상자가 표시됩니다. **삭제** 를 선택합니다.
