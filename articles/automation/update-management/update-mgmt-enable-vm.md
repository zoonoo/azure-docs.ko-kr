---
title: Azure VM에서 Azure Automation 업데이트 관리 사용
description: 이 문서에서는 Azure VM에서 업데이트 관리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.date: 09/16/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: dead4649635bebd9f73f79937dea1d24565f273e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327805"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Azure VM에서 업데이트 관리 사용

이 문서에서는 Azure VM을 사용하여 [업데이트 관리](update-mgmt-overview.md) 기능을 다른 컴퓨터에서 사용하도록 설정하는 방법을 설명합니다. 대규모로 Azure VM을 사용하도록 설정하려면 업데이트 관리를 사용하여 기존 VM을 사용하도록 설정해야 합니다.

> [!NOTE]
> 업데이트 관리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](../how-to/region-mappings.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](../index.yml)
* [가상 머신](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-the-feature-for-deployment"></a>배포 기능 사용

1. [Azure Portal](https://portal.azure.com)에서 **가상 머신**을 선택하거나 홈페이지에서 **가상 머신**을 검색하여 선택합니다.

2. 업데이트 관리를 사용하도록 설정할 VM을 선택합니다. VM은 Automation 계정의 위치와 상관없이 모든 지역에 있을 수 있습니다. 이러한 항목을

3. VM 페이지의 **작업**아래에서 **게스트 + 호스트 업데이트**를 선택 합니다.

    ![왼쪽 창에서 게스트 + 호스트 업데이트를 선택 합니다.](media/update-mgmt-enable-vm/select-guest-and-os-updates.png)

4. VM이 작업 영역에 대해 사용하도록 설정되었는지 확인하려면 `Microsoft.OperationalInsights/workspaces/read` 권한이 있어야 합니다. 필요한 추가 권한에 대한 자세한 내용은 [컴퓨터를 사용하도록 설정하는 데 필요한 권한](../automation-role-based-access-control.md#feature-setup-permissions)을 참조하세요. 한 번에 여러 컴퓨터를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Automation 계정에서 업데이트 관리 사용](update-mgmt-enable-automation-account.md)을 참조하세요.

5. 업데이트 관리 사용 페이지에서 Log Analytics 작업 영역 및 Automation 계정을 선택 하 고 **사용** 을 클릭 하 여 업데이트 관리를 사용 하도록 설정 합니다. 업데이트 관리를 사용 하도록 설정한 후 VM에서 업데이트 평가를 보려면 약 15 분이 걸릴 수 있습니다.

    ![업데이트 관리 사용](media/update-mgmt-enable-vm/enable-update-management.png)

## <a name="next-steps"></a>다음 단계

* VM에 업데이트 관리를 사용하려면 [Azure VM의 업데이트 및 패치 관리](update-mgmt-manage-updates-for-vm.md)를 참조하세요.

* 일반적인 업데이트 관리 오류를 해결하려면 [업데이트 관리 문제 해결](../troubleshoot/update-management.md)을 참조하세요.
