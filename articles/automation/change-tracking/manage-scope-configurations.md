---
title: Azure Automation 변경 내용 추적 및 인벤토리 배포 범위 제한
description: 이 문서에서는 변경 내용 추적 및 인벤토리 배포의 범위를 제한하기 위해 범위 구성을 사용하는 방법을 설명합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 05/27/2021
ms.topic: conceptual
ms.openlocfilehash: 6812a0b0688efdb75d847a36d661ba87017a8b9d
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110653198"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>변경 내용 추적 및 인벤토리 배포 범위 제한

이 문서에서는 [변경 내용 추적 및 인벤토리](overview.md) 기능을 사용하여 VM에 변경 내용을 배포하는 경우 범위 구성을 사용하는 방법을 설명합니다. 자세한 내용은 [Azure Monitor의 모니터링 솔루션 대상 지정(미리 보기)](../../azure-monitor/insights/solution-targeting.md)을 참조하세요.

## <a name="about-scope-configurations"></a>범위 구성 정보

범위 구성은 변경 내용 추적 및 인벤토리의 범위를 특정 머신으로 제한하는 데 사용되는 하나 이상의 저장된 검색(쿼리) 그룹입니다. 범위 구성은 사용하도록 설정할 컴퓨터를 대상으로 지정하기 위해 Log Analytics 작업 영역 내에서 사용됩니다. 기능의 변경 내용에 머신을 추가하면 해당 머신이 작업 영역에서 저장된 검색에 추가됩니다.

기본적으로 변경 내용 추적 및 인벤토리는 컴퓨터를 사용하도록 설정한 방법에 따라 **ChangeTracking__MicrosoftDefaultComputerGroup** 이라는 컴퓨터 그룹을 만듭니다.

* Automation 계정에서 **+ Azure VM 추가** 를 선택했습니다.
* Automation 계정에서 **컴퓨터 관리** 를 선택한 다음, **사용 가능한 컴퓨터에서 사용** 또는 **선택한 컴퓨터에서 사용** 옵션을 선택합니다.

위의 방법 중 하나를 선택하면 이 컴퓨터 그룹이 **MicrosoftDefaultScopeConfig-ChangeTracking** 범위 구성에 추가됩니다. 하나 이상의 사용자 지정 컴퓨터 그룹을 이 범위에 추가하여 관리 요구 사항을 충족하고, 변경 내용 추적 및 인벤토리를 통해 특정 컴퓨터를 관리하도록 설정하는 방법을 제어할 수도 있습니다.

**ChangeTracking__MicrosoftDefaultComputerGroup** 에서 하나 이상의 컴퓨터를 제거하여 변경 내용 추적 및 인벤토리 관리를 중지하려면 [변경 내용 추적 및 인벤토리에서 VM 제거](remove-vms-from-change-tracking.md)를 참조하세요.

## <a name="set-the-scope-limit"></a>범위 제한 설정

변경 내용 추적 및 인벤토리 배포에 대한 범위를 제한하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 **Log Analytics 작업 영역** 으로 이동합니다. 목록에서 작업 영역을 선택합니다.

3. Log Analytics 작업 영역의 왼쪽 메뉴에서 **범위 구성(미리 보기)** 을 선택합니다.

4. **MicrosoftDefaultScopeConfig-ChangeTracking** 범위 구성의 오른쪽에 있는 줄임표를 선택하고 **편집** 을 선택합니다.

5. 편집 창에서 **컴퓨터 그룹 선택** 을 선택합니다. **컴퓨터 그룹** 창에 범위 구성에 추가된 저장된 검색이 표시됩니다. 업데이트 관리에서 사용하는 저장된 검색은 다음과 같습니다.

    |속성     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | ChangeTracking        | ChangeTracking__MicrosoftDefaultComputerGroup         |

6. 사용자 지정 그룹을 추가한 경우 목록에 표시됩니다. 선택을 취소하려면 항목 왼쪽에 있는 확인란을 선택 취소합니다. 범위에 사용자 지정 그룹을 추가하려면 해당 그룹을 선택한 다음, 변경이 완료되면 **선택** 을 클릭합니다.

7. **범위 구성 편집** 페이지에서 **확인** 을 클릭하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계

* 변경 내용 추적 및 인벤토리를 사용하려면 [변경 내용 추적 및 인벤토리 관리](manage-change-tracking.md)를 참조하세요.
* 일반적인 기능 문제를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](../troubleshoot/change-tracking.md)을 참조하세요.
