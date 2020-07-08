---
title: Azure Automation 업데이트 관리에서 동적 그룹 사용
description: 이 문서에서는 Azure Automation 업데이트 관리에서 동적 그룹을 사용하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: c440862f1379983d4644fe99f33207456f747d23
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830942"
---
# <a name="use-dynamic-groups-with-update-management"></a>업데이트 관리에서 동적 그룹 사용

업데이트 관리를 사용하면 Azure 또는 Azure가 아닌 VM의 동적 그룹을 업데이트 배포 대상으로 지정할 수 있습니다. 동적 그룹을 사용하면 머신을 업데이트하기 위해 배포를 편집할 필요가 없습니다.

> [!NOTE]
> 동적 그룹은 클래식 VM에서 작동하지 않습니다.

Azure Portal의 **업데이트 관리**에서 Azure 또는 Azure가 아닌 머신의 동적 그룹을 정의할 수 있습니다. [여러 Azure 가상 머신에 대한 업데이트 관리](manage-update-multi.md)를 참조하세요.

동적 그룹은 배포 시 Azure Automation이 평가하는 쿼리를 통해 정의됩니다. 동적 그룹 쿼리가 수많은 머신을 검색하는 경우에도 Azure Automation은 한 번에 최대 1000대의 머신을 처리할 수 있습니다. [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management)을 참조하세요. 

> [!NOTE]
> 업데이트할 머신의 수가 1000대를 초과할 것으로 예상되면 업데이트를 여러 일정으로 나누어 진행하는 것이 좋습니다. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Azure 머신에 대한 동적 그룹 정의

Azure 머신에 대한 동적 그룹 쿼리를 정의할 때 다음 항목을 사용하여 동적 그룹을 채울 수 있습니다.

* Subscription
* 리소스 그룹
* 위치
* 태그들

![그룹 선택](./media/automation-update-management/select-groups.png)

동적 그룹 쿼리의 결과를 미리 보려면 **미리 보기**를 클릭합니다. 미리 보기는 현재 시간의 그룹 멤버를 보여줍니다. 이 예제에서는 **BackendServer** 그룹에 대한 `Role` 태그가 있는 머신을 검색합니다. 이 태그가 추가된 머신이 더 있으면 해당 머신은 해당 그룹에 대한 향후 배포 시에 추가됩니다.

![미리 보기 그룹](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Azure가 아닌 머신에 대한 동적 그룹 정의

Azure가 아닌 머신에 대한 동적 그룹은 컴퓨터 그룹이라고도 하는 저장된 검색을 사용합니다. 저장된 검색을 만드는 방법에 대한 자세한 내용은 [컴퓨터 그룹 만들기](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)를 참조하세요. 저장된 검색을 만든 후에는 Azure Portal의 **업데이트 관리**에 있는 저장된 검색 목록에서 선택할 수 있습니다. 저장된 검색의 컴퓨터를 미리 보려면 **미리 보기**를 클릭합니다.

![그룹 선택](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>다음 단계

업데이트 관리를 사용하려면 [Azure VM의 업데이트 및 패치 관리](automation-tutorial-update-management.md)를 참조하세요.
