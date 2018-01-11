---
title: "Azure Virtual Machines의 변경 내용 추적 | Microsoft Docs"
description: "변경 내용 추적을 사용하여 가상 머신의 파일 및 레지스트리 변경 내용을 추적합니다."
services: automation
documentationcenter: automation
author: georgewallace
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
ms.author: gwallace
ms.custom: 
ms.openlocfilehash: 3a661fada2e768c2206183c125593d019b557c1d
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2017
---
# <a name="track-changes-in-your-azure-virtual-machines"></a>Azure Virtual Machines의 변경 내용 추적

변경 내용 추적을 사용하여 가상 머신의 파일 및 Windows 레지스트리 키 변경 내용을 추적할 수 있습니다. 구성 변경 내용을 식별하면 운영 문제를 쉽게 특정할 수 있습니다.

Azure Virtual Machines에서 직접 변경 내용 추적을 사용할 수 있습니다.

Azure 가상 머신이 없는 경우 [Windows 퀵 스타트](../virtual-machines/windows/quick-create-portal.md) 또는 [Linux 퀵 스타트](../virtual-machines/linux/quick-create-portal.md) 문서의 지침을 따라 만들 수 있습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure 포털에 로그인합니다.
[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Azure Virtual Machines에 대해 변경 내용 추적 사용

1. Azure Portal의 왼쪽 창에서 **가상 머신**를 선택합니다.
2. 목록에서 가상 머신을 선택합니다.
3. 가상 머신 창의 **작업** 아래에서 **변경 내용 추적**을 선택합니다. 

   ![변경 내용 추적 온보드 VM](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)  
    **업데이트 관리 사용** 창이 열립니다.

    이 가상 머신에 대해 변경 내용 추적을 사용하는지 판단하기 위한 유효성 검사가 수행됩니다. 변경 내용 추적을 사용하지 않는 경우 솔루션 설정 옵션을 제공하는 배너가 표시됩니다.

   ![변경 내용 추적 온보드 구성 배너](./media/automation-vm-change-tracking/change-onboard-banner.png)

4. 솔루션을 사용하도록 설정하려면 배너를 선택합니다. 다음 항목이 없는 경우 자동으로 추가됩니다.

   * [Log Analytics](../log-analytics/log-analytics-overview.md) 작업 영역
   * [Automation](../automation/automation-offering-get-started.md) 계정

5. 변경 내용 추적의 데이터 로그를 저장할 Log Analytics 작업 공간을 선택하고, 변경 내용을 추적할 Automation 계정을 선택한 다음 **사용**을 선택합니다.  
    상태 표시줄에 솔루션이 활성화했다는 메시지가 표시됩니다. 이 프로세스는 15분까지 걸릴 수 있습니다.

## <a name="configure-change-tracking"></a>변경 내용 추적 구성

변경 내용 추적을 사용하도록 설정한 후 **변경 내용 추적** 창이 나타납니다. 

추적할 파일 및 레지스트리 키를 선택하려면 **설정 편집**을 선택합니다.

   ![변경 내용 추적 설정 편집](./media/automation-vm-change-tracking/change-edit-settings.png)

   **작업 영역 구성** 창이 열립니다. 

**작업 영역 구성** 창에서 다음 3개의 섹션에 설명된 대로 추적될 Windows 레지스트리 키, Windows 파일 또는 Linux 파일을 추가합니다.

### <a name="add-a-windows-registry-key"></a>Windows 레지스트리 키 추가

1. **Windows 레지스트리** 탭에서 **추가**를 선택합니다.  
    **변경 내용 추적에 대한 Windows 레지스트리 추가** 창이 열립니다.

   ![변경 내용 추적 레지스트리 추가](./media/automation-vm-change-tracking/change-add-registry.png)

2. **사용**에서 **True**를 선택합니다.
3. **항목 이름** 상자에 친근한 이름을 입력합니다.
4. (선택 사항) **그룹** 상자에 그룹 이름을 입력합니다.
5. **Windows 레지스트리 키** 상자에 추적할 레지스트리 키 이름을 입력합니다.
6. **저장**을 선택합니다.

### <a name="add-a-windows-file"></a>Windows 파일 추가

1. **Windows 파일** 탭에서 **추가**를 선택합니다.  
    **변경 내용 추적에 대한 Windows 파일 추가** 창이 열립니다.

   ![변경 내용 추적 Windows 파일 추가](./media/automation-vm-change-tracking/change-add-win-file.png)

2. **사용**에서 **True**를 선택합니다.
3. **항목 이름** 상자에 친근한 이름을 입력합니다.
4. (선택 사항) **그룹** 상자에 그룹 이름을 입력합니다.
5. **경로 입력** 창에 추적할 파일의 전체 경로와 파일 이름을 입력합니다.
6. **저장**을 선택합니다.

### <a name="add-a-linux-file"></a>Linux 파일 추가

1. **Linux 파일** 탭에서 **추가**를 선택합니다.  
    **변경 내용 추적에 대한 Linux 파일 추가** 창이 열립니다.

   ![변경 내용 추적 Linux 파일 추가](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. **사용**에서 **True**를 선택합니다.
3. **항목 이름** 상자에 친근한 이름을 입력합니다.
4. (선택 사항) **그룹** 상자에 그룹 이름을 입력합니다.
5. **경로 입력** 창에 추적할 파일의 전체 경로와 파일 이름을 입력합니다.
6. **경로 형식** 상자에서 **파일** 또는 **디렉터리**를 선택합니다.
7. **재귀**에서 지정된 경로 및 그 아래의 모든 파일과 경로에 대한 변경 내용을 추적하려면 **On**을 선택합니다. 선택한 경로 또는 파일만 추적하려면 **Off**를 선택합니다.
8. **Sudo 사용**에서 `sudo` 명령이 액세스해야 하는 파일을 추적하려면 **On**을 선택합니다. 그렇지 않은 경우 **Off**를 선택합니다.
9. **저장**을 선택합니다.

## <a name="view-changes"></a>변경 내용 보기

시간 경과에 따라 **변경 내용 추적** 창에서 가상 머신에 대한 여러 범주의 변경 내용을 볼 수 있습니다.

   ![변경 내용 추적 추가 변경 내용 보기](./media/automation-vm-change-tracking/change-view-changes.png)

보려는 변경 내용의 시간 범위와 범주를 선택할 수 있습니다. 창 맨 위에서 시간이 지남에 따라 변경 내용의 그래픽 표현을 볼 수 있습니다. 창 아래쪽에서 최근 변경 내용 목록을 볼 수 있습니다.

## <a name="view-change-tracking-log-data"></a>변경 내용 추적 로그 데이터 보기

변경 내용 추적에서 Log Analytics로 보내는 로그 데이터를 생성합니다. 쿼리를 실행하여 로그를 검색하려면 **변경 내용 추적** 창 맨 위의 **Log Analytics**를 선택합니다.

   ![변경 내용 추적 Log Analytics ](./media/automation-vm-change-tracking/change-log-analytics.png)

Log Analytics에서의 로그 파일 실행 및 검색에 대한 자세한 내용은 [Log Analytics](../log-analytics/log-analytics-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 변경 내용 추적에 대한 자세한 내용은 [변경 내용 추적 솔루션으로 사용자 환경에서 소프트웨어 변경 추적](../log-analytics/log-analytics-change-tracking.md)을 참조하세요.
* 가상 머신의 업데이트 관리에 대한 자세한 내용은 [OMS의 업데이트 관리 솔루션](../operations-management-suite/oms-solution-update-management.md)을 참조하세요.
