---
title: Azure VM에서 Azure Automation 변경 내용 추적 및 인벤토리 사용
description: 이 문서에서는 Azure VM에서 변경 내용 추적 및 인벤토리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9b14dcb9ce2f2426d8d1496541022602a114cb6e
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209974"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Azure VM에서 변경 내용 추적 및 인벤토리 사용

이 문서에서는 Azure VM을 사용 하 여 다른 컴퓨터에서 [변경 내용 추적 및 인벤토리](overview.md) 를 사용 하도록 설정 하는 방법을 설명 합니다. 대규모로 Azure VM을 사용하도록 설정하려면 변경 내용 추적 및 인벤토리를 사용하여 기존 VM을 사용하도록 설정해야 합니다.

> [!NOTE]
> 변경 내용 추적 및 인벤토리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](../how-to/region-mappings.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](../index.yml)
* [가상 머신](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="enable-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 사용

1. [Azure Portal](https://portal.azure.com)에서 **가상 머신**을 선택하거나 홈페이지에서 **가상 머신**을 검색하여 선택합니다.

2. 변경 내용 추적 및 인벤토리를 사용하도록 설정할 VM을 선택합니다. VM은 Automation 계정의 위치와 상관없이 모든 지역에 있을 수 있습니다.

3. VM 페이지에서 **구성 관리** 아래에 있는 **인벤토리** 또는 **변경 내용 추적**을 선택합니다.

4. VM이 작업 영역에 대해 사용하도록 설정되었는지 확인하려면 `Microsoft.OperationalInsights/workspaces/read` 권한이 있어야 합니다. 필요한 추가 사용 권한에 대한 자세한 내용은 [기능 설치 권한](../automation-role-based-access-control.md#feature-setup-permissions)을 참조하세요. 한 번에 여러 머신을 사용하도록 설정하는 방법에 대한 자세한 내용은 [자동화 계정에서 변경 내용 추적 및 인벤토리 사용](enable-from-automation-account.md)을 참조하세요.

5. Log Analytics 작업 영역 및 Automation 계정을 선택하고 **사용**을 클릭하여 VM에 대해 변경 내용 추적 및 인벤토리를 사용하도록 설정합니다. 설정을 완료하는 데 최대 15분이 걸립니다.

## <a name="next-steps"></a>다음 단계

* 기능을 사용 하는 방법에 대 한 자세한 내용은 [변경 내용 추적 관리](manage-change-tracking.md) 및 [인벤토리 관리](manage-inventory-vms.md)를 참조 하세요.

* 이 기능의 일반적인 문제를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](../troubleshoot/change-tracking.md)을 참조하세요.