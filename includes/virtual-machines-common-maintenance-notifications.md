---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/02/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: b3f71c4710bd9711a3209dd55f8e680f63627c1b
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50035330"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>포털에서 유지 관리를 위해 예약된 VM 보기

계획된 유지 관리 웨이브가 예약되면 예정된 유지 관리 웨이브의 영향을 받는 가상 머신 목록을 확인할 수 있습니다. 

Azure Portal을 사용하여 유지 관리하도록 예약된 VM을 찾아볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 탐색 모음에서 **Virtual Machines**를 클릭합니다.

3. Virtual Machines 창에서 **열** 단추를 클릭하여 사용 가능한 열 목록을 엽니다.

4. 다음 열을 선택하여 추가합니다.

   **유지 관리**: VM에 대한 유지 관리 상태를 표시합니다. 가능한 값은 다음과 같습니다.
      
      | 값 | 설명 |
      |-------|-------------|
      | 지금 시작 | VM이 유지 관리를 직접 시작할 수 있는 셀프 서비스 유지 관리 기간에 있습니다. VM에서 유지 관리를 시작하는 방법은 아래를 참조하세요. | 
      | 예약됨 | 유지 관리를 시작하기 위한 옵션 없이 VM의 유지 관리가 예약됩니다. 이 보기에서 유지 관리 - 예약 창을 선택하거나 VM을 클릭하여 유지 관리 기간을 알아볼 수 있습니다. | 
      | 이미 업데이트됨 | VM은 이미 업데이트되었으며 지금은 필요한 추가 작업이 없습니다. | 
      | 나중에 다시 시도 | 유지 관리를 시작했으나 실패했습니다. 나중에 셀프 서비스 유지 관리 옵션을 사용할 수 있습니다. | 
      | 지금 다시 시도 | 이전에 실패한 셀프 시작 유지 관리를 다시 시도할 수 있습니다. | 
      | - | 가상 머신이 계획된 유지 관리 웨이브에 속하지 않습니다. |
      

   **유지 관리 - 셀프 서비스 기간**: VM에서 유지 관리를 직접 시작할 수 있는 기간을 보여 줍니다.
   
   **유지 관리 - 예약된 기간**: Azure에서 유지 관리를 완료하기 위해 VM을 유지 관리할 기간을 보여 줍니다. 



## <a name="notification-and-alerts-in-the-portal"></a>포털에서 알림 및 경고

Azure에서는 구독 소유자 및 공동 소유자 그룹에 이메일을 보내 계획된 유지 관리를 위한 일정을 알립니다. Azure 활동 로그 경고를 만들어 이 통신에 받는 사람 및 채널을 더 추가할 수 있습니다. 자세한 내용은 [Azure 활동 로그로 구독 활동 모니터링](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모니터**를 선택합니다. 
3. **모니터 - 경고(클래식)** 창에서 **+ 활동 로그 경고 추가**를 클릭합니다.
5. **활동 로그 경고 추가** 페이지에 정보를 입력하고 **조건**에서 다음을 설정합니다.
   - **이벤트 범주**: 서비스 상태
   - **서비스**: Virtual Machine Scale Sets 및 Virtual Machines
   - **종류**: 계획된 유지 관리 
    
활동 로그 경고를 구성하는 방법에 대한 자세한 내용은 [활동 로그 경고 만들기](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md)를 참조하세요.
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>포털에서 VM에 대한 유지 관리 시작

VM 세부 정보를 확인하는 동안 유지 관리 관련 세부 정보를 자세히 볼 수 있습니다.  
VM 세부 정보 보기의 맨 위에서, VM이 계획된 유지 관리 웨이브에 포함되면 새 알림 리본이 추가됩니다. 또한 가능하면 유지 관리를 시작하는 새로운 옵션이 추가됩니다. 


유지 관리 알림을 클릭하면 유지 관리 페이지에 계획된 유지 관리에 대한 자세한 정보가 표시됩니다. 여기에서 VM에 대한 **유지 관리를 시작**할 수 있습니다.

유지 관리를 시작하면 가상 머신이 유지 관리되고 몇 분 이내 결과를 반영하여 유지 관리 상태가 업데이트됩니다.

셀프 서비스 기간을 놓쳤더라도 Azure에서 VM이 유지 관리될 기간을 계속 확인할 수 있습니다. 
