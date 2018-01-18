---
title: "Azure 가상 머신의 변경 내용 문제 해결 | Microsoft Docs"
description: "변경 내용 추적을 사용하여 Azure 가상 머신의 변경 내용 문제를 해결합니다."
services: automation
keywords: "변경 내용, 추적, 자동화"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/14/2017
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 0aefa175d676bd7e98841d3a1e9ff5a8c90b7deb
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2018
---
# <a name="troubleshoot-changes-in-your-environment"></a>환경 변경 문제 해결

이 자습서에서는 Azure 가상 머신의 변경 내용 문제를 해결하는 방법에 대해 알아봅니다. 변경 내용 추적을 사용하면 컴퓨터의 소프트웨어, 파일, Linux 디먼, Windows 서비스 및 Windows 레지스트리 키에 대한 변경 내용을 추적할 수 있습니다.
이러한 구성 변경 내용을 식별하면 환경 전체에서 작동 문제를 정확히 파악할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 변경 내용 추적 및 인벤토리를 위한 VM 등록
> * 중지된 서비스에 대한 변경 로그 검색
> * 변경 내용 추적 구성
> * 활동 로그 연결 사용
> * 이벤트 트리거
> * 변경 내용 보기

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

* Azure 구독. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 감시자, 작업 Runbook 및 감시자 태스크를 보관할 [Automation 계정](automation-offering-get-started.md)
* 등록할 [가상 머신](../virtual-machines/windows/quick-create-portal.md)

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal( http://portal.azure.com )에 로그인합니다.

## <a name="enable-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 사용

먼저 이 자습서에서 사용할 VM에 대한 변경 내용 추적 및 인벤토리를 사용하도록 설정해야 합니다. 이전에 VM에 대해 다른 자동화 솔루션을 사용하도록 설정한 경우에는 이 단계가 필요하지 않습니다.

1. 왼쪽 메뉴에서 **가상 머신**을 선택하고 목록에서 VM을 선택합니다.
1. 왼쪽 메뉴의 **작업** 섹션 아래에서 **인벤토리**를 클릭합니다. **변경 내용 추적 및 인벤토리 사용** 페이지가 열립니다.

이 VM에 대해 변경 내용 추적 및 인벤토리를 사용하도록 설정되어 있는지 확인하기 위해 유효성 검사가 수행됩니다.
유효성 검사 중에는 Log Analytics 작업 영역 및 연결된 Automation 계정이 확인되고 솔루션이 작업 영역에 있는지 여부가 확인됩니다.

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 작업 영역은 기능 및 서비스(예: 인벤토리)에서 생성된 데이터를 수집하는 데 사용됩니다.
이 작업 영역은 여러 원본의 데이터를 검토 및 분석하는 단일 위치를 제공합니다.

또한 유효성 검사 프로세스는 VM이 MMA(Microsoft Monitoring Agent) 및 하이브리드 작업자로 프로비전되는지 여부도 확인합니다.
이 에이전트는 VM과 통신하고 설치된 소프트웨어에 대한 정보를 얻는 데 사용됩니다.
또한 유효성 검사 프로세스는 VM이 MMA 및 Automation 하이브리드 Runbook 작업자를 통해 프로비전되는지 확인합니다.

이러한 필수 구성 요소가 충족되지 않으면 솔루션을 사용하도록 설정하는 옵션을 제공하는 배너가 표시됩니다.

![변경 내용 추적 및 인벤토리 등록 구성 배너](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

솔루션을 사용하도록 설정하려면 배너를 클릭합니다.
유효성 검사 후에 다음 필수 구성 요소 중 하나라도 누락된 것으로 확인되면 자동으로 추가됩니다.

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 작업 영역
* [Automation](./automation-offering-get-started.md)
* [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md)가 VM에서 사용되도록 설정됩니다.

**변경 내용 추적 및 인벤토리** 화면이 열립니다. 사용할 위치, Log Analytics 작업 영역 및 Automation 계정을 구성하고 **사용**을 클릭합니다. 필드가 회색으로 표시되면 해당 VM에 대해 다른 자동화 솔루션을 사용하도록 설정하고 동일한 작업 영역과 Automation 계정을 사용해야 함을 의미합니다.

![변경 내용 추적 솔루션 사용 창](./media/automation-tutorial-troubleshoot-changes/installed-software-enable.png)

솔루션을 사용하도록 설정하는 데 최대 15분이 걸릴 수 있습니다. 이 시간 동안에는 브라우저 창을 닫으면 안됩니다.
솔루션을 사용하도록 설정되면 설치된 소프트웨어에 대한 정보 및 VM에 대한 변경 내용이 Log Analytics로 이동합니다.
이 데이터를 분석에 사용할 수 있게 되는 데 30분에서 6시간까지 걸릴 수 있습니다.


## <a name="using-change-tracking-in-log-analytics"></a>Log Analytics에서 변경 내용 추적 사용

변경 내용 추적에서 Log Analytics로 보내는 로그 데이터를 생성합니다. 쿼리를 실행하여 로그를 검색하려면 **변경 내용 추적** 창 맨 위의 **Log Analytics**를 선택합니다.
변경 내용 추적 데이터는 **ConfigurationChange** 형식 아래에 저장됩니다. 다음 샘플 Log Analytics 쿼리는 중지된 모든 Windows 서비스를 반환합니다.

```
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Log Analytics에서 로그 파일을 실행하고 검색하는 방법에 대한 자세한 내용은 [Azure Log Analytics](https://docs.loganalytics.io/index)를 참조하세요.

## <a name="configure-change-tracking"></a>변경 내용 추적 구성

변경 내용 추적을 통해 VM의 구성 변경 내용을 추적할 수 있습니다. 다음 단계에서는 레지스트리 키 및 파일의 추적을 구성하는 방법을 보여 줍니다.
 
수집하고 추적할 파일 및 레지스트리 키를 선택하려면 **변경 내용 추적** 페이지 위쪽의 **설정 편집**을 선택합니다.

> [!NOTE]
> 인벤토리 및 변경 내용 추적은 동일한 컬렉션 설정을 사용하며, 이 설정은 작업 영역 수준에서 구성됩니다.

**작업 영역 구성** 창에서 다음 3개 섹션에서 설명한 대로 추적할 Windows 레지스트리 키, Windows 파일 또는 Linux 파일을 추가합니다.

### <a name="add-a-windows-registry-key"></a>Windows 레지스트리 키 추가

1. **Windows 레지스트리** 탭에서 **추가**를 선택합니다.
    **변경 내용 추적에 대한 Windows 레지스트리 추가** 창이 열립니다.

   ![변경 내용 추적 - 레지스트리 추가](./media/automation-vm-change-tracking/change-add-registry.png)

2. **사용**에서 **True**를 선택합니다.
3. **항목 이름** 상자에 친근한 이름을 입력합니다.
4. (선택 사항) **그룹** 상자에 그룹 이름을 입력합니다.
5. **Windows 레지스트리 키** 상자에 추적할 레지스트리 키 이름을 입력합니다.
6. **저장**을 선택합니다.

### <a name="add-a-windows-file"></a>Windows 파일 추가

1. **Windows 파일** 탭에서 **추가**를 선택합니다. **변경 내용 추적에 대한 Windows 파일 추가** 창이 열립니다.

   ![변경 내용 추적 - Windows 파일 추가](./media/automation-vm-change-tracking/change-add-win-file.png)

2. **사용**에서 **True**를 선택합니다.
3. **항목 이름** 상자에 친근한 이름을 입력합니다.
4. (선택 사항) **그룹** 상자에 그룹 이름을 입력합니다.
5. **경로 입력** 창에 추적할 파일의 전체 경로와 파일 이름을 입력합니다.
6. **저장**을 선택합니다.

### <a name="add-a-linux-file"></a>Linux 파일 추가

1. **Linux 파일** 탭에서 **추가**를 선택합니다. **변경 내용 추적에 대한 Linux 파일 추가** 창이 열립니다.

   ![변경 내용 추적 - Linux 파일 추가](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. **사용**에서 **True**를 선택합니다.
3. **항목 이름** 상자에 친근한 이름을 입력합니다.
4. (선택 사항) **그룹** 상자에 그룹 이름을 입력합니다.
5. **경로 입력** 창에 추적할 파일의 전체 경로와 파일 이름을 입력합니다.
6. **경로 형식** 상자에서 **파일** 또는 **디렉터리**를 선택합니다.
7. **재귀**에서 지정된 경로 및 그 아래의 모든 파일과 경로에 대한 변경 내용을 추적하려면 **On**을 선택합니다. 선택한 경로 또는 파일만 추적하려면 **Off**를 선택합니다.
8. **Sudo 사용**에서 `sudo` 명령이 액세스해야 하는 파일을 추적하려면 **On**을 선택합니다. 그렇지 않은 경우 **Off**를 선택합니다.
9. **저장**을 선택합니다.

## <a name="enable-activity-log-connection"></a>활동 로그 연결 사용

VM에 대한 **변경 내용 추적** 페이지에서 **활동 로그 연결 관리**를 선택합니다. 이 작업은 **Azure 활동 로그** 페이지를 엽니다. **연결**을 선택하여 변경 내용 추적을 VM에 대한 Azure 활동 로그에 연결합니다.

이 설정을 사용하도록 설정하면 VM에 대한 **개요** 페이지로 이동하여 VM을 중지하기 위해 **중지**를 선택합니다. 메시지가 표시되면 **예**를 선택하여 VM을 중지합니다. 할당이 취소되면 **시작**을 선택하여 VM을 다시 시작합니다.

VM을 중지하고 시작하면 활동 로그에 이벤트가 기록됩니다. **변경 내용 추적** 페이지로 다시 이동합니다. 페이지 아래쪽의 **이벤트** 탭을 선택합니다. 잠시 후에 이벤트가 차트와 테이블에 표시됩니다. 이전 단계와 마찬가지로 각 이벤트를 선택하여 해당 이벤트에 대한 자세한 정보를 볼 수 있습니다.

![포털에서 변경 세부 정보 보기](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>변경 내용 보기

변경 내용 추적 및 인벤토리 솔루션을 사용하도록 설정되면 **변경 내용 추적** 페이지에서 결과를 볼 수 있습니다.

VM 내에서 **작업** 아래에 있는 **변경 내용 추적**을 선택합니다.

![VM에 대한 변경 내용 목록을 보여 주는 스크린샷](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

차트에는 시간이 지남에 따라 발생한 변경 내용이 표시됩니다.
활동 로그 연결을 추가하면 위쪽의 선 그래프에 Azure 활동 로그 이벤트가 표시됩니다.
막대 그래프의 각 행은 서로 다른 추적 가능한 변경 유형을 나타냅니다.
이러한 유형은 Linux 디먼, 파일, Windows 레지스트리 키, 소프트웨어 및 Windows 서비스입니다.
변경 탭에는 시각화에 표시된 변경 내용에 대한 세부 정보가 변경이 발생한 시간의 내림차순(가장 최근이 첫 번째)으로 표시됩니다.
**이벤트** 탭에서 테이블에는 가장 최근에 연결된 활동 로그 이벤트 및 해당 세부 정보가 첫 번째 항목으로 표시됩니다.

서비스 및 소프트웨어 변경을 포함하여 여러 변경 내용이 시스템에 있음을 결과에서 확인할 수 있습니다. 페이지 위쪽의 필터를 사용하여 결과를 **변경 유형** 또는 시간 범위별로 필터링할 수 있습니다.

**WindowsServices** 변경을 선택하면 **변경 세부 정보** 창이 열립니다. 변경 세부 정보 창에는 변경 내용 및 변경 전후의 값에 대한 세부 정보가 표시됩니다. 이 경우 소프트웨어 보호 서비스가 중지되었습니다.

![포털에서 변경 세부 정보 보기](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 변경 내용 추적 및 인벤토리를 위한 VM 등록
> * 중지된 서비스에 대한 변경 로그 검색
> * 변경 내용 추적 구성
> * 활동 로그 연결 사용
> * 이벤트 트리거
> * 변경 내용 보기

자세히 알아보려면 변경 내용 추적 및 인벤토리 솔루션에 대한 개요로 계속 진행하세요.

> [!div class="nextstepaction"]
> [변경 관리 및 인벤토리 솔루션](../log-analytics/log-analytics-change-tracking.md?toc=%2fazure%2fautomation%2ftoc.json)
