---
title: Azure 자동화 업데이트 관리를 사용하여 동적 그룹 사용
description: 이 문서에서는 동적 그룹이 Azure 자동화 업데이트 관리에서 작동하는 방식에 대해 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617441"
---
# <a name="use-dynamic-groups-with-update-management"></a>업데이트 관리에서 동적 그룹 사용

업데이트 관리를 사용하면 업데이트 배포를 위해 Azure 또는 비 Azure VM의 동적 그룹을 대상으로 지정할 수 있습니다. 쿼리에 의해 정의된 이러한 그룹은 배포 시 평가되므로 컴퓨터를 추가하기 위해 배포를 편집할 필요가 없습니다.

## <a name="azure-machines"></a>Azure 컴퓨터

동적 그룹은 클래식 VM에서 작동하지 않습니다. 쿼리를 정의할 때 다음 항목을 함께 사용하여 동적 그룹을 채울 수 있습니다.

* Subscription
* 리소스 그룹
* 위치
* 태그들

![그룹 선택](./media/automation-update-management/select-groups.png)

동적 그룹의 결과를 미리 보려면 **미리 보기를**클릭합니다. 미리 보기에는 현재 그룹 구성원이 표시됩니다. 이 예제에서는 **그룹 BackendServer**에 `Role` 대한 태그가 있는 컴퓨터를 검색합니다. 이 태그가 추가된 컴퓨터가 더 많은 경우 해당 그룹에 대한 향후 배포에 추가됩니다.

![미리 보기 그룹](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Azure가 아닌 컴퓨터

Azure가 아닌 컴퓨터의 경우 컴퓨터 그룹이라고도 하는 저장된 검색을 사용하여 동적 그룹을 만듭니다. 저장된 검색을 만드는 방법을 알아보려면 [컴퓨터 그룹 만들기를](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)참조하십시오. 그룹이 만들어지면 저장된 검색 목록에서 그룹을 선택할 수 있습니다. **미리 보기를** 클릭하여 해당 시점에 저장된 검색에서 컴퓨터를 미리 봅을 클릭합니다.

![그룹 선택](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>다음 단계

동적 그룹을 만든 후 [업데이트 배포](automation-tutorial-update-management.md)를 만들 수 있습니다.
