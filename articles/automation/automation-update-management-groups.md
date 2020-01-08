---
title: Azure 업데이트 관리에서 동적 그룹 사용
description: 이 문서에서는 Azure Automation 업데이트 관리에서 동적 그룹을 사용 하는 방법을 설명 합니다.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420390"
---
# <a name="use-dynamic-groups-with-update-management"></a>업데이트 관리에서 동적 그룹 사용

업데이트 관리은 업데이트 배포를 위해 Azure 또는 비 Azure Vm의 동적 그룹을 대상으로 할 수 있는 기능을 제공 합니다. 이러한 그룹은 배포 시 평가 되므로 컴퓨터를 추가 하기 위해 배포를 편집할 필요가 없습니다.

## <a name="azure-machines"></a>Azure 컴퓨터

이러한 그룹은 쿼리를 통해 정의되고, 업데이트 배포가 시작되면 해당 그룹의 멤버가 평가됩니다. 동적 그룹은 클래식 Vm에서 작동 하지 않습니다. 쿼리를 정의 하는 경우 다음 항목을 함께 사용 하 여 동적 그룹을 채울 수 있습니다.

* Subscription
* 리소스 그룹
* 위치
* 태그

![그룹 선택](./media/automation-update-management/select-groups.png)

동적 그룹의 결과를 미리 보려면 **미리 보기** 단추를 클릭합니다. 이 미리 보기는 당시의 그룹 멤버를 보여주며, 이 예제에서는 **Role** is equal to **BackendServer** 태그가 지정된 머신을 검색합니다. 더 많은 머신에 이 태그를 추가하면 해당 그룹에 대한 향후 배포 시에 추가됩니다.

![미리 보기 그룹](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>비 Azure 컴퓨터

비 Azure 컴퓨터의 경우 컴퓨터 그룹이 라고도 하는 저장 된 검색을 사용 하 여 동적 그룹을 만듭니다. 저장 된 검색을 만드는 방법을 알아보려면 [컴퓨터 그룹 만들기](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)를 참조 하세요. 그룹을 만든 후에는 저장 된 검색 목록에서 선택할 수 있습니다. 해당 시점에 저장 된 검색에서 컴퓨터를 미리 보려면 **미리 보기** 를 클릭 합니다.

![그룹 선택](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>다음 단계

동적 그룹을 만든 후 [업데이트 배포를 만들](automation-tutorial-update-management.md) 수 있습니다.
