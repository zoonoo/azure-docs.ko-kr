---
title: "Azure Virtual Machines의 변경 내용 추적 | Microsoft Docs"
description: "변경 내용 추적을 사용하여 가상 컴퓨터의 파일 및 레지스트리 변경 내용 추적"
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ba92390b684573818ec9d69fd8c7be6d064316c7
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="track-changes-in-your-azure-virtual-machines"></a>Azure Virtual Machines의 변경 내용 추적

변경 내용 추적을 사용하여 가상 컴퓨터의 파일 및 Windows 레지스트리 키 변경 내용을 추적할 수 있습니다. 에서도 확인할 수 있습니다. 구성 변경 내용을 식별하면 운영 문제를 쉽게 특정할 수 있습니다.

Azure Virtual Machines에서 직접 변경 내용 추적을 사용할 수 있습니다.

Azure Virtual Machines가 없는 경우 [Windows 퀵 스타트](../virtual-machines/windows/quick-create-portal.md) 또는 [Linux 퀵 스타트](../virtual-machines/linux/quick-create-portal.md)를 사용하여 만들 수 있습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure 포털에 로그인합니다.

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Azure Virtual Machines에 대해 변경 내용 추적 사용

1. 화면 왼쪽에서 **가상 컴퓨터**를 선택합니다.
1. 목록에서 가상 컴퓨터를 선택합니다.
1. 가상 컴퓨터 화면의 **작업** 섹션에서 **변경 내용 추적**을 클릭합니다. **업데이트 관리 사용** 화면이 열립니다.

   ![변경 내용 추적 온보드 VM](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)

이 가상 컴퓨터에 대해 변경 내용 추적을 사용하는지 판단하기 위한 유효성 검사가 수행됩니다. 변경 내용 추적을 사용하지 않는 경우 솔루션 설정 옵션을 제공하는 배너가 나타납니다.

   ![변경 내용 추적 온보드 구성 배너](./media/automation-vm-change-tracking/change-onboard-banner.png)

솔루션을 사용하도록 설정하려면 이 배너를 클릭합니다. 다음이 없는 경우 자동으로 추가됩니다.

* [Log Analytics](../log-analytics/log-analytics-overview.md) 작업 영역
* [Automation](../automation/automation-offering-get-started.md) 계정

변경 내용 추적의 데이터 로그를 저장할 Log Analytics 작업 공간과 변경 내용을 추적할 Automation 계정을 선택하고 **사용**을 클릭합니다.

상태 표시줄에 솔루션이 활성화했다는 메시지가 표시됩니다. 이 프로세스는 15분까지 걸릴 수 있습니다.

## <a name="configure-change-tracking"></a>변경 내용 추적 구성

변경 내용 추적을 사용하도록 설정한 후 **변경 내용 추적** 화면이 나타납니다. **설정 편집**을 클릭하여 추적할 파일 및 레지스트리 키를 선택합니다.

![변경 내용 추적 설정 편집](./media/automation-vm-change-tracking/change-edit-settings.png)

**작업 영역 구성** 화면이 열립니다. 적합한 탭에서 **추가**를 클릭하여 추적할 Windows 레지스트리 키, Windows 파일 또는 Linux 파일을 추가합니다.

## <a name="add-a-windows-registry-key"></a>Windows 레지스트리 키 추가

1. **작업 영역 구성** 화면의 **Windows 레지스트리** 탭에서 **추가**를 클릭합니다. **변경 내용 추적에 대한 Windows 레지스트리 추가** 화면이 열립니다.

   ![변경 내용 추적 레지스트리 추가](./media/automation-vm-change-tracking/change-add-registry.png)

1. **사용**에서 **True**를 선택합니다.
1. **항목 이름** 필드에 친근한 이름을 추가합니다.
1. **그룹** 필드에 그룹 이름(옵션)을 입력합니다.
1. **Windows 레지스트리 키** 필드에 추적할 레지스트리 키 이름을 추가합니다.
1. **Save**를 클릭합니다.

## <a name="add-a-windows-file"></a>Windows 파일 추가

1. **작업 영역 구성** 화면의**Windows 파일** 탭에서 **추가**를 클릭합니다. **변경 내용 추적에 대한 Windows 파일 추가** 화면이 열립니다.

   ![변경 내용 추적 Windows 파일 추가](./media/automation-vm-change-tracking/change-add-win-file.png)

1. **사용**에서 **True**를 선택합니다.
1. **항목 이름** 필드에 친근한 이름을 추가합니다.
1. **그룹** 필드에 그룹 이름(옵션)을 입력합니다.
1. **경로 입력** 필드에 추적할 파일의 전체 경로와 파일 이름을 추가합니다.
1. **Save**를 클릭합니다.

## <a name="add-a-linux-file"></a>Linux 파일 추가

1. **작업 영역 구성** 화면의 **Linux 파일** 탭에서 **추가**를 클릭합니다. **변경 내용 추적에 대한 Linux 파일 추가** 화면이 열립니다.

   ![변경 내용 추적 Linux 파일 추가](./media/automation-vm-change-tracking/change-add-linux-file.png)

1. **사용**에서 **True**를 선택합니다.
1. **항목 이름** 필드에 친근한 이름을 추가합니다.
1. **그룹** 필드에 그룹 이름(옵션)을 입력합니다.
1. **경로 입력** 필드에 추적할 파일의 전체 경로와 파일 이름을 추가합니다.
1. **경로 형식** 필드에서 **파일** 또는 **디렉터리**를 선택합니다.
1. **재귀**에서 **On**을 선택하여 지정된 경로 및 그 아래의 모든 파일과 경로에 대한 변경 내용을 추적합니다. 선택한 경로 또는 파일만 추적하려면 **Off**를 선택합니다.
1. **Sudo**에서 `sudo` 명령이 액세스해야 하는 파일을 추적하려면 **On**을 선택합니다. 그렇지 않은 경우 **Off**를 선택합니다.
1. **Save**를 클릭합니다.

## <a name="view-changes"></a>변경 내용 보기

시간 경과에 따라 **변경 내용 추적** 화면에서 가상 컴퓨터에 대한 여러 범주의 변경 내용을 확인할 수 있습니다.

   ![변경 내용 추적 추가 변경 내용 보기](./media/automation-vm-change-tracking/change-view-changes.png)

보려는 변경 내용의 시간 범위와 범주를 선택할 수 있습니다. 화면 위쪽에는 시간 경과에 따른 변경 내용의 그래픽 보기가 표시됩니다.
화면 아래쪽에는 최근 변경 내용 목록이 표시됩니다.

## <a name="view-change-tracking-log-data"></a>변경 내용 추적 로그 데이터 보기

변경 내용 추적에서 Log Analytics로 보내는 로그 데이터를 생성합니다. 쿼리를 실행하여 로그를 검색하려면 **변경 내용 추적** 화면 맨 위의 **Log Analytics**를 클릭합니다.

   ![변경 내용 추적 Log Analytics ](./media/automation-vm-change-tracking/change-log-analytics.png)

Log Analytics에서의 로그 파일 검색 실행에 대한 자세한 내용은 [Log Analytics](../log-analytics/log-analytics-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 변경 내용 추적에 대한 자세한 내용은 [변경 내용 추적](../log-analytics/log-analytics-change-tracking.md)을 참조하세요.
* 가상 컴퓨터의 업데이트 관리에 대한 자세한 내용은 [업데이트 관리](../operations-management-suite/oms-solution-update-management.md)를 참조하세요

