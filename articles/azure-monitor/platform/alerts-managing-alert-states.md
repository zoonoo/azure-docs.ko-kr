---
title: 경고 및 스마트 그룹 상태 관리
description: 경고 및 스마트 그룹 인스턴스의 상태 관리
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: b91c3d59a5a37457d67fdfbc646334fccb331df5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104804"
---
# <a name="manage-alert-and-smart-group-states"></a>경고 및 스마트 그룹 상태 관리

Azure Monitor의 경고에는 이제 [경고 상태 및 모니터 조건](./alerts-overview.md)이 있으며, 이와 유사하게 스마트 그룹에는 [스마트 그룹 상태](./alerts-smartgroups-overview.md?toc=%252fazure%252fazure-monitor%252ftoc.json)가 있습니다. 이제 상태 변경 사항은 각각의 경고 또는 스마트 그룹과 연관된 히스토리에 캡처됩니다. 이 문서에서는 경고 및 스마트 그룹 모두에 대한 상태 변경 프로세스를 단계별로 안내합니다.

## <a name="change-the-state-of-an-alert"></a>경고 상태 변경

1. 다음과 같은 여러 다른 방법으로 경고의 상태를 변경할 수 있습니다. 
    * [모든 경고] 페이지에서 상태를 변경할 경고 옆의 확인란을 클릭하고 [상태 변경]을 클릭합니다.   
    ![상태 변경이 선택 된 모든 경고 페이지가 스크린샷에 표시 됩니다.](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * 특정 경고 인스턴스의 [경고 세부사항] 페이지에서 [상태 변경]을 클릭할 수 있습니다.   
    ![경고 상태 변경이 선택 된 상태에서 경고 정보 페이지를 보여 주는 스크린샷](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * 특정 경고 인스턴스의 [경고 세부사항] 페이지에 있는 스마트 그룹 분할창에서 원하는 경고 옆에 있는 확인란을 클릭할 수 있습니다.    
    ![스크린샷 확인 표시가 있는 일부 인스턴스와 하트 비트 경고에 대 한 경고 세부 정보 페이지를 표시 합니다.](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * [스마트 그룹 세부사항] 페이지의 [멤버 경고] 목록에서 상태를 변경하려는 경고 옆에 있는 확인란을 클릭하고 [상태 변경]을 클릭하여 상태를 변경할 수 있습니다.   
    ![상태를 변경할 경고를 선택할 수 있는 스마트 그룹 세부 정보 페이지를 보여 주는 스크린샷](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. [상태 변경]을 클릭하여 팝업이 열리면 상태(New/Acknowledged/Closed)를 선택하고 필요한 경우 주석을 입력할 수 있습니다.   
![스크린샷 정보 변경 경고 대화 상자를 표시 합니다.](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. 완료되면 상태 변경이 각 경고의 히스토리에 기록됩니다. 이 내용은 각 [세부사항] 페이지를 열고 히스토리 섹션을 확인하여 볼 수 있습니다.    
![상태 변경 기록을 보여 주는 스크린샷](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>스마트 그룹의 상태를 변경합니다.
1. 다음과 같은 다른 방법으로 스마트 그룹의 상태를 변경할 수 있습니다.
    1. [스마트 그룹] 목록 페이지에서 상태를 변경하려는 스마트 그룹 옆에 있는 선택란을 클릭하고 [상태 변경]을 클릭할 수 있습니다.  
    ![스마트 그룹의 상태 변경 페이지를 보여 주는 스크린샷](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. [스마트 그룹 세부사항] 페이지에서 [상태 변경]을 클릭할 수 있습니다.        
    ![스마트 그룹 상태 변경이 선택 된 스마트 그룹 세부 정보 페이지를 보여 주는 스크린샷](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. [상태 변경]을 클릭하여 팝업이 열리면 상태(New/Acknowledged/Closed)를 선택하고 필요한 경우 주석을 입력할 수 있습니다. 
![스크린샷 스마트 그룹에 대 한 상태 변경 대화 상자를 표시 합니다.](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  스마트 그룹의 상태를 변경해도 개별 멤버 경고의 상태는 변경되지 않습니다.

1. 완료되면 상태 변경이 각 스마트 그룹의 히스토리에 기록됩니다. 이 내용은 각 [세부사항] 페이지를 열고 히스토리 섹션을 확인하여 볼 수 있습니다.     
![스크린샷 스마트 그룹에 대 한 변경 기록을 보여 줍니다.](./media/alerts-managing-alert-states/state-sg-history.jpg)