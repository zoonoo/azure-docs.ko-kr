---
title: Azure DevTest Labs의 활동 로그 | Microsoft Docs
description: 이 문서에서는 Azure DevTest Labs에 대 한 활동 로그를 확인 하는 단계를 제공 합니다.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 24b5eb25f1c8c9044ad6e77545381a43a11714f9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097856"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩을 위한 활동 로그 보기 
랩을 하나 이상 만든 후 랩을 액세스, 수정 및 관리 하는 방법과 시기를 모니터링 하는 것이 좋습니다. Azure DevTest Labs는 Azure Monitor 특히 **활동 로그**를 사용 하 여 이러한 작업에 대해 실습 정보를 제공 합니다. 

이 문서에서는 Azure DevTest Labs에서 랩에 대 한 활동 로그를 보는 방법을 설명 합니다.

## <a name="view-activity-log-for-a-lab"></a>랩에 대 한 활동 로그 보기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, **DEVOPS** 섹션에서 **DevTest Labs**를 선택합니다. **DEVOPS** 섹션에서 **DevTest Labs** 옆에 있는 *(별표)를 선택하는 경우입니다. 이 작업을 수행하면 다음에 쉽게 액세스할 수 있도록 **DevTest Labs**가 왼쪽 탐색 메뉴에 추가됩니다. 그러면 왼쪽 탐색 메뉴에서 **DevTest Labs**를 선택할 수 있습니다.

    ![모든 서비스 - DevTest Labs 선택](./media/devtest-lab-create-lab/all-services-select.png)
1. 랩 목록에서 랩을 선택합니다.
1. 랩의 홈 페이지에 있는 왼쪽 메뉴에서 **구성 및 정책** 을 선택 합니다. 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="왼쪽 메뉴에서 구성 및 정책을 선택 합니다.":::
1. **구성 및 정책** 페이지의 **관리**아래에 있는 왼쪽 메뉴에서 **활동 로그** 를 선택 합니다. 랩에서 수행 된 작업에 대 한 항목이 표시 됩니다. 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="활동 로그":::    
1. 이벤트를 선택 하 여 세부 정보를 확인 합니다. **요약** 페이지에 작업 이름, 타임 스탬프, 작업을 수행한 사용자 등의 정보가 표시 됩니다. 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="VM 이벤트 중지-요약":::        
1. 자세한 내용을 보려면 **JSON** 탭으로 전환 합니다. 다음 예제에서는 vm의 이름과 VM에서 수행 된 작업 (중지 됨)을 볼 수 있습니다.

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="VM 이벤트 중지-JSON":::           
1. 변경 기록 **(미리 보기)** 탭으로 전환 하 여 변경 기록을 확인 합니다. 다음 예제에서는 VM에서 수행 된 변경 내용을 확인 합니다. 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="VM 이벤트 중지-변경 기록":::             
1. 변경 내용에 대 한 자세한 내용을 보려면 변경 내용 목록에서 변경 내용을 선택 합니다. 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="VM 이벤트 중지-세부 정보 변경":::             

활동 로그에 대 한 자세한 내용은 [Azure 활동 로그](../azure-monitor/platform/activity-log.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 활동 로그에 대 한 **경고** 를 설정 하는 방법에 대 한 자세한 내용은 [경고 만들기](create-alerts.md)를 참조 하세요.
- 활동 로그에 대해 자세히 알아보려면 [Azure 활동 로그](../azure-monitor/platform/activity-log.md)를 참조 하세요.

