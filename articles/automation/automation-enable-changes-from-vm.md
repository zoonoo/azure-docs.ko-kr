---
title: Azure VM에서 Azure Automation 변경 내용 추적 및 인벤토리 사용
description: 이 문서에서는 Azure VM에서 변경 내용 추적 및 인벤토리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 5379f2c46bbeaba4ee8509603b7b739b75d08f04
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836790"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Azure VM에서 변경 내용 추적 및 인벤토리 사용

이 문서에서는 Azure VM을 사용하여 [변경 내용 추적 및 인벤토리](change-tracking.md) 기능을 다른 머신에서 사용하도록 설정하는 방법을 설명합니다. 대규모로 Azure VM을 사용하도록 설정하려면 변경 내용 추적 및 인벤토리를 사용하여 기존 VM을 사용하도록 설정해야 합니다. 

> [!NOTE]
> 변경 내용 추적 및 인벤토리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](how-to/region-mappings.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](automation-offering-get-started.md)
* [가상 머신](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="enable-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 사용

1. [Azure Portal](https://portal.azure.com)에서 **가상 머신**을 선택하거나 홈페이지에서 **가상 머신**을 검색하여 선택합니다.

2. 변경 내용 추적 및 인벤토리를 사용하도록 설정할 VM을 선택합니다. VM은 Automation 계정의 위치와 상관없이 모든 지역에 있을 수 있습니다.

3. VM 페이지에서 **구성 관리** 아래에 있는 **인벤토리** 또는 **변경 내용 추적**을 선택합니다.

4. VM이 작업 영역에 대해 사용하도록 설정되었는지 확인하려면 `Microsoft.OperationalInsights/workspaces/read` 권한이 있어야 합니다. 필요한 추가 사용 권한에 대한 자세한 내용은 [기능 설치 권한](automation-role-based-access-control.md#feature-setup-permissions)을 참조하세요. 한 번에 여러 머신을 사용하도록 설정하는 방법에 대한 자세한 내용은 [자동화 계정에서 변경 내용 추적 및 인벤토리 사용](automation-enable-changes-from-auto-acct.md)을 참조하세요.

5. Log Analytics 작업 영역 및 Automation 계정을 선택하고 **사용**을 클릭하여 VM에 대해 변경 내용 추적 및 인벤토리를 사용하도록 설정합니다. 설정을 완료하는 데 최대 15분이 걸립니다. 

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>범위 구성 확인

변경 내용 추적 및 인벤토리는 작업 영역 내의 범위 구성을 사용하고 컴퓨터를 대상으로 하여 이 기능을 사용할 수 있도록 합니다. 범위 구성은 기능 범위를 특정 컴퓨터로 제한하는 데 사용되는 하나 이상의 저장된 검색 그룹입니다. 자세한 내용은 [변경 내용 추적 및 인벤토리의 범위 구성 작업](automation-scope-configurations-change-tracking.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 이 기능으로 하는 작업에 대한 자세한 내용은 [변경 내용 추적 및 인벤토리 관리](change-tracking-file-contents.md)를 참조하세요.
* 범위 구성에 대한 자세한 내용은 [변경 내용 추적 및 인벤토리의 범위 구성 작업](automation-scope-configurations-change-tracking.md)을 참조하세요.
* 이 기능을 사용하여 사용자 환경에 설치된 소프트웨어를 파악하는 방법에 대한 자세한 내용은 [VM에 설치된 소프트웨어 검색](automation-tutorial-installed-software.md)을 참조하세요.
* 기능을 사용하도록 설정할 때 Automation 계정을 Log Analytics 작업 영역에 통합하지 않으려는 경우 [Automation 계정에서 작업 영역 연결 해제](automation-unlink-workspace-change-tracking.md)를 참조하세요.
* VM에 대한 변경 내용을 배포하는 작업이 완료되면 [변경 내용 추적 및 인벤토리에서 VM 제거](automation-remove-vms-from-change-tracking.md)에 설명된 대로 제거할 수 있습니다.
* 기능의 일반적인 문제를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](troubleshoot/change-tracking.md)을 참조하세요.
