---
title: 전자 메일 Azure Backup 보고서
description: 전자 메일을 통해 정기적으로 보고서를 받을 수 있는 자동화 된 작업 만들기
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 8c18d4c7a3c7a9ba343296961fa9a44614366405
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510510"
---
# <a name="email-azure-backup-reports"></a>전자 메일 Azure Backup 보고서

백업 보고서에서 사용할 수 있는 **전자 메일 보고서** 기능을 사용 하면 자동화 된 작업을 만들어 전자 메일을 통해 정기적으로 보고서를 받을 수 있습니다. 이 기능은 사용자가 제공 하는 입력에 따라 LA (선택한 Log Analytics) 작업 영역에서 데이터를 쿼리 하는 논리 앱을 Azure 환경에 배포 하는 방식으로 작동 합니다. [논리 앱 및 가격 책정에 대해 자세히 알아보세요](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>시작하기

백업 보고서를 통해 전자 메일 작업을 구성 하려면 다음 단계를 수행 합니다.

1.  **백업 센터**  >  **백업 보고서** 로 이동 하 여 **전자 메일 보고서** 탭을 클릭 합니다.
2.  다음 정보를 지정 하 여 작업을 만듭니다.
    * **작업 세부 정보** -만들 논리 앱의 이름, 구독, 리소스 그룹 및이를 만들어야 하는 위치입니다. 논리 앱은 여러 구독, 리소스 그룹 및 위치 (보고서 필터 섹션에서 선택 됨)에서 데이터를 쿼리할 수 있지만 단일 구독, 리소스 그룹 및 위치의 컨텍스트에서 생성 됩니다.
    * **내보낼 데이터** -내보낼 탭입니다. **모든** 탭 옵션을 선택 하 여 탭 당 단일 작업 앱을 만들거나 단일 작업을 사용 하 여 모든 탭을 메일로 보낼 수 있습니다.
    * **전자 메일 옵션**: 전자 메일 빈도, 받는 사람 전자 메일 ID 및 전자 메일 제목입니다.

    ![전자 메일 탭](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  **제출** 및 **확인** 을 클릭 하면 논리 앱이 생성 됩니다. 간편한 검색을 위해 **UsedByBackupReports: true** 태그를 사용 하 여 논리 앱과 연결 된 API 연결을 만듭니다. 아래 섹션에 설명 된 대로 논리 앱을 성공적으로 실행 하려면 일회성 권한 부여 단계를 수행 해야 합니다.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Azure Monitor 로그 및 Office 365에 대 한 연결 권한 부여

논리 앱은 LA 작업 영역을 쿼리 하는 데 [azuremonitorlogs](https://docs.microsoft.com/connectors/azuremonitorlogs/) 커넥터를 사용 하 고 전자 메일을 보내는 데 [Office365 Outlook](https://docs.microsoft.com/connectors/office365connector/) 커넥터를 사용 합니다. 이러한 두 커넥터에 대해 일회성 권한 부여를 수행 해야 합니다. 
 
권한 부여를 수행 하려면 다음 단계를 수행 합니다.

1.  Azure Portal **Logic Apps** 으로 이동 합니다.
2.  만든 논리 앱의 이름을 검색 하 여 리소스로 이동 합니다.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  **API 연결** 메뉴 항목을 클릭 합니다.

    ![API 연결](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  형식으로 두 개의 연결이 표시 됩니다 `<location>-azuremonitorlogs` `<location>-office365` . 즉, _eastus-azuremonitorlogs_ 와 _eoffice365_ 가 있습니다.
5.  이러한 각 연결로 이동 하 고 **API 연결 편집** 메뉴 항목을 선택 합니다. 표시 되는 화면에서 권한 부여 **를 선택 하** 고 권한 부여가 완료 되 면 연결을 저장 합니다.

    ![연결 권한 부여](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  권한 부여 후에 논리 앱이 작동 하는지 테스트 하려면 논리 앱으로 다시 이동 하 고, **개요** 를 열고, 위쪽 창에서 **트리거 실행** 을 선택 하 여 전자 메일이 성공적으로 생성 되는지 여부를 테스트할 수 있습니다.

## <a name="contents-of-the-email"></a>전자 메일의 내용

* 포털에 표시 된 모든 차트와 그래프는 전자 메일에서 인라인 콘텐츠로 사용할 수 있습니다.
* 포털에 표시 된 표는 전자 메일에서 * .csv 첨부 파일로 사용할 수 있습니다.
* 전자 메일에 표시 되는 데이터는 전자 메일 작업을 만들 때 보고서에서 사용자가 선택한 모든 보고서 수준 필터를 사용 합니다.
* **백업 인스턴스 이름**, **정책 이름** 등의 탭 수준 필터는 적용 되지 않습니다. 이에 대 한 유일한 예외는 **매일**, **매주**, **매월** 및 **매년** RP 보존에 대 한 필터가 적용 되는 **최적화** 탭의 **보존 최적화** 표입니다.
* 시간 범위와 집계 유형 (차트의 경우)은 보고서의 사용자 시간 범위 선택에 따라 결정 됩니다. 예를 들어 시간 범위 선택이 최근 60 일 (주간 집계 유형으로 변환) 되 고 전자 메일 빈도가 매일 이면 수신자는 지난 60 일 동안 발생 한 데이터를 포함 하는 차트와 매일 전자 메일을 받게 되며 주간에 데이터를 집계 합니다.

## <a name="troubleshooting-issues"></a>문제 해결

논리 앱을 성공적으로 배포한 후에도 예상 대로 전자 메일을 받지 못하는 경우 아래 단계에 따라 구성 문제를 해결할 수 있습니다.

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>시나리오 1: 성공 전자 메일과 오류 전자 메일 모두 수신 하지 않음

* 이 문제는 Outlook API 커넥터에 권한이 부여 되지 않은 경우에 발생할 수 있습니다. 연결에 권한을 부여 하려면 위에 제공 된 권한 부여 단계를 따르세요.

* 논리 앱을 만드는 동안 잘못 된 전자 메일 받는 사람을 지정한 경우에도이 문제가 발생할 수 있습니다. 전자 메일 받는 사람을 올바르게 지정 했는지 확인 하려면 Azure Portal에서 논리 앱으로 이동 하 여 논리 앱 디자이너를 열고 전자 메일 단계를 선택 하 여 올바른 전자 메일 Id를 사용 하 고 있는지 확인 합니다.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>시나리오 2: 논리 앱이 완료 될 때까지 실행 하지 못했음을 알리는 오류 전자 메일 받기

이 문제를 해결하려면 다음을 수행합니다.
1.  Azure Portal에서 논리 앱으로 이동 합니다.
2.  **개요** 화면 아래쪽에 **실행 기록** 섹션이 표시 됩니다. 최근 실행에서를 열고 실패 한 워크플로의 단계를 볼 수 있습니다. 몇 가지 가능한 원인은 다음과 같습니다.
    * **Azure Monitor Logs 커넥터에 권한이 부여** 되지 않았습니다 .이 문제를 해결 하려면 위에 제공 된 권한 부여 단계를 따르세요.
    * **La 쿼리의 오류**: 사용자 고유의 쿼리로 논리 앱을 사용자 지정한 경우에는 la 쿼리에서 오류가 발생 하 여 논리 앱이 실패할 수 있습니다. 관련 단계를 선택 하 고 쿼리를 잘못 실행 하는 오류를 볼 수 있습니다.

문제가 지속 되 면 Microsoft 지원에 문의 하세요.

## <a name="next-steps"></a>다음 단계
[백업 보고서에 대 한 자세한 정보](https://docs.microsoft.com/azure/backup/configure-reports)
