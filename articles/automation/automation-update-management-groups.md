---
title: Azure Automation 업데이트 관리에서 동적 그룹 사용
description: 이 문서에서는 Azure Automation 업데이트 관리에서 동적 그룹을 사용 하는 방법을 설명 합니다.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617441"
---
# <a name="use-dynamic-groups-with-update-management"></a>업데이트 관리에서 동적 그룹 사용

업데이트 관리를 사용 하 여 업데이트 배포를 위한 Azure 또는 비 Azure Vm의 동적 그룹을 대상으로 지정할 수 있습니다. 쿼리에 의해 정의 되는 이러한 그룹은 배포 시 컴퓨터를 추가 하기 위해 편집 하지 않아도 되도록 배포 시 평가 됩니다.

## <a name="azure-machines"></a>Azure 컴퓨터

동적 그룹은 클래식 Vm에서 작동 하지 않습니다. 쿼리를 정의 하는 경우 다음 항목을 함께 사용 하 여 동적 그룹을 채울 수 있습니다.

* Subscription
* 리소스 그룹
* 위치
* 태그들

![그룹 선택](./media/automation-update-management/select-groups.png)

동적 그룹의 결과를 미리 보려면 **미리 보기**를 클릭 합니다. 미리 보기는 현재 시간에 그룹 멤버 자격을 보여 줍니다. 이 예에서는 그룹 `Role` **BackendServer**에 대 한 태그가 있는 컴퓨터를 검색 합니다. 추가 컴퓨터에이 태그가 추가 된 경우 해당 그룹에 대 한 향후 배포에 추가 됩니다.

![미리 보기 그룹](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>비 Azure 컴퓨터

비 Azure 컴퓨터의 경우 컴퓨터 그룹이 라고도 하는 저장 된 검색을 사용 하 여 동적 그룹을 만듭니다. 저장 된 검색을 만드는 방법을 알아보려면 [컴퓨터 그룹 만들기](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)를 참조 하세요. 그룹을 만든 후에는 저장 된 검색 목록에서 선택할 수 있습니다. 해당 시점에 저장 된 검색에서 컴퓨터를 미리 보려면 **미리 보기** 를 클릭 합니다.

![그룹 선택](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>다음 단계

동적 그룹을 만든 후 [업데이트 배포를 만들](automation-tutorial-update-management.md)수 있습니다.
