---
title: Azure Automation의 Azure VM에 대한 업데이트 및 패치 관리
description: 이 문서에서는 업데이트 관리를 사용하여 Azure VM에 대한 업데이트 및 패치를 관리하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 79cffa7aedd0fc04dd4a747ef28bc67cacf37905
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204891"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Azure VM에 대한 업데이트 및 패치 관리

이 문서에서는 Azure Automation [업데이트 관리](automation-update-management.md) 기능을 사용하여 Azure Windows VM에 대한 업데이트 및 패치를 관리하는 방법을 설명합니다. 가격 책정 정보에 대해서는 [업데이트 관리를 위한 Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.

> [!NOTE]
> 업데이트 관리는 자사 업데이트 배포와 패치 미리 다운로드를 지원합니다. 이 지원을 사용하려면 패치할 시스템을 변경해야 합니다. 시스템에 이러한 설정을 구성하는 방법을 알아보려면 [Azure Automation 업데이트 관리에 대한 Windows 업데이트 설정 구성](automation-configure-windows-update.md)을 참조하세요.

이 문서의 절차를 수행하기 전에 다음 기법 중 하나를 사용하여 VM에 업데이트 관리를 사용하도록 설정했는지 확인하세요.

* [Automation 계정에서 업데이트 관리 사용](automation-onboard-solutions-from-automation-account.md)
* [Azure Portal을 탐색하여 업데이트 관리 사용](automation-onboard-solutions-from-browse.md)
* [Runbook에서 업데이트 관리 사용](automation-onboard-solutions.md)
* [Azure VM에서 업데이트 관리 사용](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>배포 범위 제한

업데이트 관리는 작업 영역 내의 범위 구성을 사용하여 업데이트를 수신할 대상 컴퓨터를 지정합니다. 자세한 내용은 [업데이트 관리 배포 범위 제한](automation-scope-configurations-update-management.md)을 참조하세요.

## <a name="view-update-assessment"></a>업데이트 평가 보기

업데이트 평가를 보려면 다음을 수행합니다.

1. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리**를 선택합니다. 

2. 사용자 환경에 대한 업데이트가 업데이트 관리 페이지에 나열됩니다. 업데이트가 누락된 것으로 식별된 경우 **누락 업데이트** 탭에 누락된 업데이트 목록이 표시됩니다.

3. **정보 링크**에서 업데이트 링크를 선택하여 업데이트에 대한 중요한 정보를 제공하는 지원 문서를 엽니다.

    ![업데이트 상태 보기](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. 업데이트의 다른 위치를 클릭하면 로그 검색 창이 열립니다. 로그 검색에 대한 쿼리는 해당 특정 업데이트에 대해 미리 정의되어 있습니다. 이 쿼리를 수정하거나 자체 쿼리를 만들어 자세한 정보를 볼 수 있습니다.

    ![업데이트 상태 보기](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>경고 구성

업데이트 배포 상태를 알려 주도록 경고를 설정하려면 아래 단계를 따르세요.

1. Automation 계정의 **모니터링** 아래에서 **경고**로 이동한 다음, **새 경고 규칙**을 클릭합니다.

2. 경고 규칙 만들기 페이지에 Automation 계정이 이미 리소스로 선택되어 있습니다. 변경하려면 **리소스 편집**을 클릭합니다. 

3. 리소스 선택 페이지의 **리소스 종류별로 필터링** 드롭다운 메뉴에서 **Automation 계정**을 선택합니다. 

4. 사용하려는 Automation 계정을 선택한 다음, **완료**를 클릭합니다.

5. **조건 추가**를 클릭하여 업데이트 배포에 적절한 신호를 선택합니다. 다음 표는 사용 가능한 두 가지 신호의 세부 정보를 보여줍니다.

    |신호 이름|차원|Description
    |---|---|---|
    |`Total Update Deployment Runs`|- 업데이트 배포 이름<br>- 상태    |업데이트 배포의 전체 상태를 알립니다.|
    |`Total Update Deployment Machine Runs`|- 업데이트 배포 이름</br>- 상태</br>- 대상 컴퓨터</br>- 업데이트 배포 실행 ID    |특정 머신을 대상으로 하는 업데이트 배포의 상태를 알립니다.|

6. 차원의 경우 목록에서 유효한 값을 선택합니다. 원하는 값이 목록에 없으면 해당 차원 옆의 **\+** 를 클릭하고 사용자 지정 이름을 입력합니다. 그런 다음, 찾을 값을 선택합니다. 차원의 모든 값을 선택하려면 **선택\*** 단추를 클릭합니다. 차원 값을 선택하지 않으면 업데이트 관리에서 해당 차원을 무시합니다.

    ![신호 논리 구성](./media/automation-tutorial-update-management/signal-logic.png)

7. **경고 논리**에서 **시간 집계** 및 **임계값** 필드에 값을 입력한 다음, **완료**를 클릭합니다.

8. 다음 창에서 경고의 이름과 설명을 입력합니다.

9. **심각도** 필드는 성공적인 실행의 경우 **정보(심각도 2)** 로 설정하고, 실패한 실행의 경우 **정보(심각도 1)** 로 설정합니다.

    ![신호 논리 구성](./media/automation-tutorial-update-management/define-alert-details.png)

10. 경고 규칙을 사용하도록 설정하려는 방법에 따라 **예** 또는 **아니요**를 클릭합니다.

11. 이 규칙에 대한 경고를 표시하지 않으려면 **경고 표시 안 함**을 선택합니다.

## <a name="configure-action-groups-for-your-alerts"></a>경고에 대한 작업 그룹 구성

경고가 구성되었으면 여러 경고에서 사용할 작업 그룹을 설정할 수 있습니다. 이 작업에는 이메일 알림, Runbook, webhook 등이 포함될 수 있습니다. 작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md)를 참조하세요.

1. 경고를 선택한 다음, **작업 그룹**에서 **새로 만들기**를 선택합니다. 

2. 작업 그룹의 전체 이름과 약식 이름을 입력합니다. 업데이트 관리는 지정된 그룹을 사용하여 알림을 보낼 때 약식 이름을 사용합니다.

3. **작업**에서 작업을 지정하는 이름(예: **이메일 알림**)을 입력합니다. 

4. **작업 유형**에서 적절한 유형(예: **이메일/SMS/푸시/음성**)을 선택합니다. 

5. **세부 정보 편집**을 클릭합니다.

6. 작업 유형에 대한 창을 채웁니다. 예를 들어 **이메일/SMS/푸시/음성**을 사용하는 경우 작업 이름을 입력하고 **메일** 확인란을 선택한 후 유효한 이메일 주소를 입력한 다음, **확인**을 클릭합니다.

    ![이메일 작업 그룹 구성](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. 작업 그룹 추가 창에서 **확인**을 클릭합니다.

8. 경고 이메일의 경우 이메일 제목을 사용자 지정할 수 있습니다. **규칙 만들기**에서 **작업 사용자 지정**을 선택한 다음, **이메일 제목**을 선택합니다. 

9. 작업이 완료되면 **경고 규칙 만들기**를 클릭합니다. 

## <a name="schedule-an-update-deployment"></a>업데이트 배포 예약

업데이트 배포를 예약하면 대상 머신에서 업데이트 배포를 처리하는 **MicrosoftOMSComputers** Runbook에 연결된 [일정](shared-resources/schedules.md) 리소스가 만들어집니다. 릴리스 일정 및 서비스 기간 이후로 배포를 예약하여 업데이트를 설치해야 합니다. 배포에 포함할 업데이트 형식을 선택할 수 있습니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

>[!NOTE]
>배포를 만든 후 Azure Portal에서 또는 PowerShell을 사용하여 일정 리소스를 삭제하면 예약된 업데이트 배포가 중단되고 포털에서 예약 리소스를 다시 구성하려고 할 때 오류가 표시됩니다. 해당 배포 일정을 삭제해야만 일정 리소스를 삭제할 수 있습니다.  

새로운 업데이트 배포를 예약하려면 다음을 수행합니다.

1. Automation 계정에서 **업데이트 관리**로 이동한 다음, **업데이트 관리**에서 **업데이트 배포 예약**을 선택합니다.

2. **새 업데이트 배포**에서 **이름** 필드를 사용하여 배포의 고유한 이름을 입력합니다.

3. 업데이트 배포의 대상 운영 체제를 선택합니다.

4. **업데이트할 그룹(미리 보기)** 영역에서 구독, 리소스 그룹, 위치 및 태그가 결합된 쿼리를 정의하여 배포에 포함할 Azure VM의 동적 그룹을 빌드합니다. 자세히 알아보려면 [업데이트 관리에서 동적 그룹 사용](automation-update-management-groups.md)을 참조하세요.

5. **업데이트할 머신** 영역에서 저장된 검색, 가져온 그룹을 선택하거나 드롭다운 메뉴에서 **머신**을 선택하고 개별 머신을 선택합니다. 이 옵션을 사용하여 각 머신의 Log Analytics 에이전트 준비 상태를 확인할 수 있습니다. Azure Monitor 로그에서 컴퓨터 그룹을 만드는 다른 방법에 대해 알아보려면 [Azure Monitor 로그의 컴퓨터 그룹](../azure-monitor/platform/computer-groups.md)을 참조하세요.

6. **업데이트 분류** 영역을 사용하여 제품의 [업데이트 분류](automation-view-update-assessments.md#work-with-update-classifications)를 지정합니다. 업데이트 배포에 포함할 업데이트를 제외하고 각 제품에 대해 지원되는 모든 업데이트 분류를 선택 취소합니다.

7. **업데이트 포함/제외** 영역을 사용하여 배포할 특정 업데이트를 선택합니다. 포함/제외 페이지에는 포함하거나 제외할 업데이트가 KB 문서 ID 번호로 표시됩니다. 
    
   > [!IMPORTANT]
   > 제외 항목은 포함 항목을 재정의한다는 사실을 기억하세요. 예를 들어 `*`의 제외 규칙을 정의하면 업데이트 관리가 모든 패치 또는 패키지를 설치 대상에서 제외합니다. 제외된 패치는 여전히 머신에서 누락된 것으로 표시됩니다. Linux 머신의 경우 제외된 종속 패키지가 들어 있는 패키지를 포함할 경우 업데이트 관리에서 주 패키지를 설치하지 않습니다.

   > [!NOTE]
   > 업데이트 배포에 포함하기 위해 대체된 업데이트를 지정할 수 없습니다.

8. **일정 설정**을 선택합니다. 기본 시작 시간은 현재 시간으로부터 30분 후입니다. 앞으로 10분 이후부터 언제든지 시작 시간을 설정할 수 있습니다.

9. **되풀이** 필드를 사용하여 배포가 한 번 발생하는지 또는 되풀이 일정을 사용하는지 지정한 후 **확인**을 클릭합니다.

10. **사전 스크립트 + 사후 스크립트(미리 보기)** 영역에서 배포 전후에 실행할 스크립트를 선택합니다. 자세히 알아보려면 [사전 스크립트 및 사후 스크립트 관리](pre-post-scripts.md)를 참조하세요.
    
11. **유지 관리 기간(분)** 필드를 사용하여 업데이트 설치에 허용되는 시간을 지정합니다. 유지 관리 기간을 지정할 때는 다음 사항을 고려하세요.

    * 유지 관리 기간은 설치하려는 업데이트의 수를 제어합니다.
    * 유지 관리 기간 종료일이 임박해도 업데이트 관리는 새 업데이트 설치를 중지하지 않습니다.
    * 유지 관리 기간이 초과되어도 업데이트 관리는 진행 중인 업데이트를 종료하지 않습니다.
    * 서비스 팩 업데이트를 설치하는 데 오랜 시간이 소요되어 Windows에서 유지 관리 기간이 초과되는 경우가 많습니다.

    > [!NOTE]
    > Ubuntu에서 유지 관리 기간 외에 업데이트가 적용되지 않도록 하려면 자동 업데이트를 사용하지 않도록 `Unattended-Upgrade` 패키지를 다시 구성합니다. 패키지 구성 방법에 대한 자세한 내용은 [Ubuntu Server 가이드의 자동 업데이트 항목](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)을 참조하세요.

12. **다시 부팅 옵션** 필드를 사용하여 배포 중에 다시 부팅을 처리하는 방법을 지정할 수 있습니다. 다음 옵션을 사용할 수 있습니다. 
    * 필요한 경우 다시 부팅(기본값)
    * 항상 다시 부팅
    * 다시 부팅 안 함
    * 다시 부팅만 - 업데이트 설치 안 함

    > [!NOTE]
    > [다시 시작을 관리하는 데 사용되는 레지스트리 키](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)에 나열된 레지스트리 키는 **재부팅 옵션**이 **재부팅하지 않음**으로 설정되면 재부팅 이벤트가 발생할 수 있습니다.

13. 배포 일정 구성을 마쳤으면 **만들기**를 클릭합니다.

    ![업데이트 일정 설정 창](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. 상태 대시보드로 돌아갑니다. **예약된 업데이트 배포**를 선택하여 만든 배포 일정을 표시합니다.

## <a name="schedule-an-update-deployment-programmatically"></a>프로그래밍 방식으로 업데이트 배포 예약

REST API를 사용하여 업데이트 배포를 만드는 방법은 [소프트웨어 업데이트 구성 - 만들기](/rest/api/automation/softwareupdateconfigurations/create)를 참조하세요. 

샘플 Runbook을 사용하여 주간 업데이트 배포를 만들 수 있습니다. 이 Runbook에 대한 자세한 내용은 [리소스 그룹에 있는 하나 이상의 VM에 대한 주간 업데이트 배포 만들기](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)를 참조하세요.

## <a name="check-deployment-status"></a>배포 상태 확인

예약된 배포가 시작되면 **업데이트 관리**의 **업데이트 배포** 탭에서 상태를 볼 수 있습니다. 배포가 현재 실행 중이면 상태가 **진행 중**입니다. 배포가 성공적으로 완료되면 상태가 **성공**으로 변경됩니다. 배포에서 하나 이상의 업데이트에 오류가 발생하는 경우 상태는 **부분적으로 실패**입니다.

## <a name="view-results-of-a-completed-update-deployment"></a>완료된 업데이트 배포의 결과 보기

배포가 완료되면 이를 선택하여 대시보드를 볼 수 있습니다.

![특정 배포에 대한 업데이트 배포 상태 대시보드](./media/automation-tutorial-update-management/manageupdates-view-results.png)

**업데이트 결과**에서 요약은 대상 VM의 총 업데이트 수 및 배포 결과를 제공합니다. 오른쪽 표에는 각 업데이트에 대한 자세한 분석 및 설치 결과가 표시됩니다.

사용 가능한 값은

* **시도 안 함**: 정의된 유지 관리 기간에 따라 사용할 수 있는 시간이 충분하지 않기 때문에 업데이트가 설치되지 않았습니다.
* **선택 안 함** - 배포할 업데이트를 선택하지 않았습니다.
* **성공** - 업데이트했습니다.
* **실패** - 업데이트하지 못했습니다.

배포를 통해 생성된 모든 로그 항목을 보려면 **모든 로그**를 선택합니다.

**출력** 타일을 선택하여 대상 VM의 업데이트 배포를 관리하는 Runbook의 작업 스트림을 확인합니다.

배포의 모든 오류에 대한 자세한 정보를 보려면 **오류**를 선택합니다.

## <a name="view-the-deployment-alert"></a>배포 경고 보기

업데이트 배포가 완료되면 배포 설치 중에 지정한 경고가 표시됩니다. 예를 들어 패치를 확인하는 이메일은 다음과 같습니다.

![이메일 작업 그룹 구성](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>다음 단계

* 범위 구성에 대한 자세한 내용은 [업데이트 관리 배포 범위 제한](automation-scope-configurations-update-management.md)을 참조하세요.
* Log Analytics 작업 영역에 저장된 로그를 검색해야 하는 경우 [Azure Monitor 로그에서 로그 검색](../log-analytics/log-analytics-log-searches.md)을 참조하세요.
* 배포가 완료되면 [업데이트 관리의 Automation 계정에서 작업 영역 연결 해제](automation-unlink-workspace-update-management.md)를 참조하세요.
* 업데이트 관리에서 VM을 삭제하려면 [업데이트 관리에서 VM 제거](automation-remove-vms-from-update-management.md)를 참조하세요.
* 일반적인 업데이트 관리 오류를 해결하려면 [업데이트 관리 문제 해결](troubleshoot/update-management.md)을 참조하세요.
* Windows 업데이트 에이전트 문제를 해결하려면 [Windows 업데이트 에이전트 문제 해결](troubleshoot/update-agent-issues.md)을 참조하세요.
* Linux 업데이트 에이전트 관련 문제를 해결하려면 [Linux 업데이트 에이전트 문제 해결](troubleshoot/update-agent-issues-linux.md)을 참조하세요.