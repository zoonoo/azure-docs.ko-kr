---
title: Azure Automation의 Azure VM 변경 내용 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure VM의 변경 내용 문제를 해결하는 방법을 설명합니다.
services: automation
ms.subservice: change-inventory-management
keywords: 변경 내용, 추적, 변경 내용 추적, 인벤토리, 자동화
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 63fe36a51b69648039c3a16be4267fc0c72e8a58
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185809"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>Azure VM의 변경 내용 문제 해결

이 자습서에서는 Azure 가상 머신의 변경 내용 문제를 해결하는 방법에 대해 알아봅니다. 변경 내용 추적과 인벤토리를 사용하면 컴퓨터의 소프트웨어, 파일, Linux 디먼, Windows Services 및 Windows 레지스트리 키에 대한 변경 내용을 추적할 수 있습니다.
이러한 구성 변경 내용을 식별하면 환경 전체에서 작동 문제를 정확히 파악할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM에 변경 내용 추적 및 인벤토리 사용
> * 중지된 서비스에 대한 변경 로그 검색
> * 변경 내용 추적 구성
> * 활동 로그 연결 사용
> * 이벤트 트리거
> * 변경 내용 보기
> * 경고 구성

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* Azure 구독 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 감시자, 작업 Runbook 및 Watcher 태스크를 보관할 [Automation 계정](./index.yml)
* 기능에 사용할 수 있는 [가상 머신](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="enable-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 사용

이 자습서를 진행하려면 먼저 변경 내용 추적 및 인벤토리를 사용하도록 설정해야 합니다. 이전에 이 기능을 사용하도록 설정한 경우에는 이 단계가 필요하지 않습니다.

>[!NOTE]
>필드가 회색으로 표시되면 해당 VM에 다른 자동화 기능을 사용하도록 설정하고 동일한 작업 영역과 Automation 계정을 사용해야 합니다.

1. **가상 머신**을 선택하고 목록에서 VM을 선택합니다.
2. 왼쪽 메뉴에서 **작업** 아래의 **인벤토리**를 선택합니다. 인벤토리 페이지가 열립니다.

    ![변경 사용](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. [Log Analytics](../azure-monitor/log-query/log-query-overview.md) 작업 영역을 선택합니다. 이 작업 영역은 변경 내용 추적 및 인벤토리 같은 기능에 의해 생성된 데이터를 수집합니다. 이 작업 영역은 여러 원본의 데이터를 검토 및 분석하는 단일 위치를 제공합니다.

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. 업그레이드할 Automation 계정을 선택합니다.

5. 배포 위치를 구성합니다.

5. **사용**을 클릭하여 VM용 기능을 배포합니다. 

설정 중에 VM은 Windows용 Log Analytics 에이전트와 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)를 사용하여 프로비저닝됩니다. 변경 내용 추적 및 인벤토리가 사용하도록 설정되는 데 최대 15분이 소요될 수 있습니다. 이 시간 동안에는 브라우저 창을 닫으면 안됩니다.

기능을 사용하도록 설정한 후 설치된 소프트웨어에 대한 정보 및 VM에 대한 변경 내용이 Azure Monitor 로그로 이동합니다.
이 데이터를 분석에 사용할 수 있게 되는 데 30분에서 6시간까지 걸릴 수 있습니다.

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>Azure Monitor 로그에서 변경 내용 추적 및 인벤토리 사용

변경 내용 추적 및 인벤토리에서 Azure Monitor 로그로 전송되는 로그 데이터를 생성합니다. 쿼리를 실행하여 로그를 검색하려면 변경 내용 추적 페이지 상단에 있는 **Log Analytics**를 선택합니다. 변경 내용 추적 데이터는 `ConfigurationChange` 형식 아래에 저장됩니다.

다음 샘플 Log Analytics 쿼리는 중지된 모든 Windows 서비스를 반환합니다.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Azure Monitor 로그에서 로그 파일을 실행하고 검색하는 방법에 대한 자세한 내용은 [Azure Monitor 로그](../azure-monitor/log-query/log-query-overview.md)를 참조하세요.

## <a name="configure-change-tracking"></a>변경 내용 추적 구성

변경 내용 추적의 경우 VM의 변경 내용 추적 페이지 상단에 있는 **설정 편집**을 사용하여 수집하고 추적할 파일 및 레지스트리 키를 선택합니다. 작업 영역 구성 페이지에서 추적할 Windows 레지스트리 키, Windows 파일 또는 Linux 파일을 추가할 수 있습니다.

> [!NOTE]
> 변경 내용 추적 및 인벤토리는 모두 동일한 컬렉션 설정을 사용하며, 이 설정은 작업 영역 수준에서 구성됩니다.

### <a name="add-a-windows-registry-key"></a>Windows 레지스트리 키 추가

1. **Windows 레지스트리** 탭에서 **추가**를 선택합니다. 

1. 변경 내용 추적에 대해 Windows 레지스트리 추가 페이지에서 추적할 키에 대한 정보를 입력하고 **저장**을 클릭합니다.

    |속성  |Description  |
    |---------|---------|
    |사용     | 설정 적용 여부 결정        |
    |Item Name     | 추적할 파일의 이름        |
    |그룹     | 파일을 논리적으로 그룹화하는 그룹 이름        |
    |Windows 레지스트리 키   | 파일을 확인할 경로입니다(예: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>Windows 파일 추가

1. **Windows 파일** 탭에서 **추가**를 선택합니다. 

1. 변경 내용 추적에 대해 Windows 파일 추가 페이지에서 추적할 파일이나 디렉터리의 정보를 입력하고 **저장**을 클릭합니다.

    |속성  |Description  |
    |---------|---------|
    |사용     | 설정 적용 여부 결정        |
    |Item Name     | 추적할 파일의 이름        |
    |그룹     | 파일을 논리적으로 그룹화하는 그룹 이름        |
    |경로 입력     | 파일을 확인할 경로입니다(예: "c:\temp\\\*.txt").<br>"%winDir%\System32\\\*.*"와 같은 환경 변수도 사용할 수 있습니다.         |
    |재귀     | 추적할 항목을 찾을 때 재귀가 사용되는지 결정합니다.        |
    |모든 설정에 대한 파일 콘텐츠 업로드| 추적된 변경 내용에 대해 파일 콘텐츠 업로드를 설정 또는 해제합니다. 사용 가능한 옵션은 **True** 또는 **False**입니다.|

### <a name="add-a-linux-file"></a>Linux 파일 추가

1. **Linux 파일** 탭에서 **추가**를 선택합니다. 

1. 변경 내용 추적에 대해 Linux 파일 추가 페이지에서 추적할 파일이나 디렉터리의 정보를 입력하고 **저장**을 클릭합니다.

    |속성  |Description  |
    |---------|---------|
    |사용     | 설정 적용 여부 결정        |
    |Item Name     | 추적할 파일의 이름        |
    |그룹     | 파일을 논리적으로 그룹화하는 그룹 이름        |
    |경로 입력     | 파일을 확인할 경로. 예: "/etc/*.conf"       |
    |경로 유형     | 추적할 항목 유형입니다. 가능한 값은 파일 및 디렉터리입니다.        |
    |재귀     | 추적할 항목을 찾을 때 재귀가 사용되는지 결정합니다.        |
    |sudo 사용     | 항목을 확인할 때 sudo가 사용되는지 여부를 결정합니다.         |
    |링크     | 디렉터리를 트래버스할 때 기호화된 링크에서 처리하는 방법을 결정합니다.<br> **Ignore** - 심볼 링크를 무시하고 참조된 파일/디렉터리를 포함하지 않음<br>**Follow** - 재귀 중에 심볼 링크를 따르고 참조된 파일/디렉터리도 포함<br>**Manage** - 심볼 링크를 따르고 반환된 콘텐츠의 처리를 변경하도록 허용      |
    |모든 설정에 대한 파일 콘텐츠 업로드| 추적된 변경 내용에 대해 파일 콘텐츠 업로드를 설정 또는 해제합니다. 사용 가능한 옵션은 True 또는 False입니다.|

   > [!NOTE]
   > **링크** 속성의 **관리** 값은 권장되지 않습니다. 파일 콘텐츠 검색은 지원되지 않습니다.

## <a name="enable-activity-log-connection"></a>활동 로그 연결 사용

1. VM의 변경 내용 추적 페이지에서 **활동 로그 연결 관리**를 선택합니다. 

2. Azure 활동 로그 페이지에서 **연결**을 클릭하여 변경 내용 추적 및 인벤토리를 VM의 Azure 활동 로그에 연결합니다.

3. VM에 대한 개요 페이지로 이동하고 **중지**를 선택하여 VM을 중지합니다. 

4. 메시지가 표시되면 **예**를 선택하여 VM을 중지합니다. 

5. VM 할당이 취소되면 **시작**을 선택하여 다시 시작합니다. VM을 중지하고 시작하면 활동 로그에 이벤트가 기록됩니다. 

## <a name="view-changes"></a>변경 내용 보기

1. 변경 내용 추적 페이지로 돌아가서 페이지 맨 아래에 있는 **이벤트** 탭을 선택합니다. 

2. 잠시 후에 변경 내용 추적 이벤트가 차트와 테이블에 표시됩니다. 차트에는 시간이 지남에 따라 발생한 변경 내용이 표시됩니다. 위쪽의 꺾은선 그래프에는 Azure 활동 로그 이벤트가 표시됩니다. 막대 그래프의 각 행은 다른 추적 가능한 변경 유형을 나타냅니다. 이러한 유형은 Linux 디먼, 파일, Windows 레지스트리 키, 소프트웨어 및 Windows 서비스입니다. 변경 탭에는 표시된 변경 내용에 대한 세부 정보가 표시되며 가장 최근의 변경 내용이 먼저 표시됩니다.

    ![포털의 이벤트 보기](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. 서비스 및 소프트웨어 변경을 포함한 여러 변경 내용이 시스템에 적용되었음을 알 수 있습니다. 페이지 위쪽의 필터를 사용하여 결과를 **변경 유형** 또는 시간 범위별로 필터링할 수 있습니다.

    ![VM에 대한 변경 내용 목록](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. **WindowsServices** 변경 내용을 선택합니다. 이 항목을 선택하면 변경 내용 및 변경 전후의 값에 대한 세부 정보가 표시된 변경 세부 정보 페이지가 열립니다. 이 경우 소프트웨어 보호 서비스가 중지되었습니다.

    ![포털에서 변경 세부 정보 보기](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>경고 구성

Azure Portal에서 변경 내용을 보는 기능도 유용하지만, 중지된 서비스와 같은 변경이 발생할 경우 경고를 받을 수 있으므로 더 유용합니다. 중지된 서비스에 대한 경고를 추가해 보겠습니다. 

1. Azure Portal에서 **모니터**로 이동합니다. 

2. **공유 서비스**에서 **경고**를 선택하고 **+ 새로운 경고 규칙**을 클릭합니다.

3. **선택**을 클릭하여 리소스를 선택합니다. 

4. 리소스 선택 페이지의 **리소스 종류별로 필터링** 드롭다운 메뉴에서 **Log Analytics**를 선택합니다. 

5. Log Analytics 작업 영역을 선택한 다음, **완료**를 클릭합니다.

    ![리소스 선택](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. **조건 추가**를 클릭합니다.

7. 신호 논리 구성 페이지의 테이블에서 **로그 검색 사용자 지정**을 선택합니다. 

8. 검색 쿼리 텍스트 상자에 다음 쿼리를 입력합니다.

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    이 조회에서는 지정된 시간 프레임에서 W3SVC 서비스가 중지된 컴퓨터를 리턴합니다.

9. **경고 논리** 아래에서 **임계값**으로 **0**을 입력합니다. 작업을 완료하면 **완료**를 클릭합니다.

    ![신호 논리 구성](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. **작업 그룹** 아래에서 **새로 만들기**를 선택합니다. 작업 그룹은 여러 경고에서 사용할 수 있는 작업의 그룹입니다. 이 작업에는 이메일 알림, Runbook, 웹후크 등이 포함되며 이에 국한되지 않습니다. 작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md)를 참조하세요.

11. **경고 세부 정보** 아래에서 경고에 대한 이름과 설명을 입력합니다. 

12. **심각도**를 **정보(심각도 2)** , **경고(심각도 1)** 또는 **위험(심각도 0)** 으로 설정합니다.

13. **작업 그룹 이름** 상자에 경고의 이름 및 약식 이름을 입력합니다. 약식 이름은 이 그룹을 사용하여 알림을 보내는 경우 전체 작업 그룹 이름 대신 사용됩니다.

14. **작업**에서 **이메일 관리자**과 같은 작업의 이름을 입력합니다. 

15. **작업 유형**에서 **이메일/SMS/푸시/음성**을 선택합니다. 

16. **세부 정보**에서 **세부 정보 편집**을 선택합니다.

    ![작업 그룹 추가](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

17. 이메일/SMS/푸시/음성 창에서 이름을 입력하고 **이메일** 확인란을 선택한 후 올바른 이메일 주소를 입력합니다. 완료되면 창에서 **확인**을 클릭한 다음, 작업 그룹 추가 페이지에서 **확인**을 클릭합니다.

18. 경고 이메일의 제목을 사용자 지정하려면 **작업 사용자 지정**을 선택합니다. 

19. **규칙 만들기**에서 **이메일 제목**을 선택한 다음, **경고 규칙 만들기**를 선택합니다. 경고에서는 언제 업데이트 배포가 성공하고 해당 부분의 업데이트 배포 실행에 어떤 컴퓨터가 사용되었는지 알려줍니다. 다음 이미지는 W3SVC 서비스가 중지되면 받는 예제 메일입니다.

    ![이메일](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * VM에 변경 내용 추적 및 인벤토리 사용
> * 중지된 서비스에 대한 변경 로그 검색
> * 변경 내용 추적 구성
> * 활동 로그 연결 사용
> * 이벤트 트리거
> * 변경 내용 보기
> * 경고 구성

자세히 알아보려면 변경 내용 추적 및 인벤토리 기능에 대한 개요로 이동하세요.

> [!div class="nextstepaction"]
> [변경 내용 추적 및 인벤토리 개요](./change-tracking.md)
