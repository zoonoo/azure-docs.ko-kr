---
title: Azure Automation 업데이트 관리에서 동적 그룹 사용
description: 이 문서에서는 Azure Automation 업데이트 관리에서 동적 그룹을 사용 하는 방법을 설명 합니다.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: bf4c156d2bf9c205bd7545a96b5314dd43b2d02c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690767"
---
# <a name="use-dynamic-groups-with-update-management"></a>업데이트 관리에서 동적 그룹 사용

업데이트 관리를 사용 하 여 업데이트 배포를 위한 Azure 또는 비 Azure Vm의 동적 그룹을 대상으로 지정할 수 있습니다. 동적 그룹을 사용 하면 컴퓨터를 업데이트 하기 위해 배포를 편집 하지 않아도 됩니다.

> [!NOTE]
> 동적 그룹은 클래식 Vm에서 작동 하지 않습니다.

Azure Portal의 **업데이트 관리** 에서 azure 또는 비 azure 컴퓨터에 대 한 동적 그룹을 정의할 수 있습니다. [여러 Azure 가상 머신에 대 한 업데이트 관리를](manage-update-multi.md)참조 하세요.

동적 그룹은 배포 시 Azure Automation 평가 하는 쿼리에 의해 정의 됩니다. 동적 그룹 쿼리가 많은 수의 컴퓨터를 검색 Azure Automation 하는 경우에도 한 번에 최대 1000 개의 컴퓨터만 처리할 수 있습니다. [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management)을 참조하세요. 

> [!NOTE]
> 1000 대 이상의 컴퓨터를 업데이트 해야 하는 경우 여러 업데이트 일정 간에 업데이트를 분할 하는 것이 좋습니다. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Azure 컴퓨터에 대 한 동적 그룹 정의

Azure 컴퓨터에 대 한 동적 그룹 쿼리를 정의 하는 경우 다음 항목을 사용 하 여 동적 그룹을 채울 수 있습니다.

* Subscription
* 리소스 그룹
* 위치
* 태그들

![그룹 선택](./media/automation-update-management/select-groups.png)

동적 그룹 쿼리 결과를 미리 보려면 **미리 보기**를 클릭 합니다. 미리 보기는 현재 시간에 그룹 멤버 자격을 보여 줍니다. 이 예에서는 그룹 `Role` **BackendServer**에 대 한 태그가 있는 컴퓨터를 검색 합니다. 추가 컴퓨터에이 태그가 추가 된 경우 해당 그룹에 대 한 향후 배포에 추가 됩니다.

![미리 보기 그룹](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>비 Azure 컴퓨터에 대 한 동적 그룹 정의

비 Azure 컴퓨터에 대 한 동적 그룹은 컴퓨터 그룹이 라고도 하는 저장 된 검색을 사용 합니다. 저장 된 검색을 만드는 방법을 알아보려면 [컴퓨터 그룹 만들기](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)를 참조 하세요. 저장 된 검색을 만든 후 Azure Portal **업데이트 관리** 의 저장 된 검색 목록에서 선택할 수 있습니다. [ **미리 보기** ]를 클릭 하 여 저장 된 검색에서 컴퓨터를 미리 봅니다.

![그룹 선택](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>다음 단계

동적 그룹을 만든 후 [업데이트 배포를 만들](automation-tutorial-update-management.md)수 있습니다.
