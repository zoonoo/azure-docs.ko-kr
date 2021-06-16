---
title: Managed Instance 경고 및 알림 설정(Azure Portal)
description: Azure Portal을 사용하여 사용자가 지정한 조건에 부합하면 알림이나 자동 작업을 트리거할 수 있는 SQL Managed Instance 경고를 만듭니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma, wiassaf
ms.date: 05/04/2020
ms.openlocfilehash: 7b596b6e9702d0518bb1103088e718529ad7929e
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111593053"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure SQL Managed Instance에 대한 경고 만들기
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure Portal을 사용하여 Azure SQL Managed Instance Database에서 데이터베이스 경고를 설정하는 방법을 보여 줍니다. 경고는 이메일을 보내거나, 웹 후크를 호출하거나, Azure Function을 실행하거나, runbook을 실행하거나, 외부 ITSM 호환 티켓 시스템을 호출하거나, 일부 메트릭(예: 인스턴스 스토리지 크기 또는 CPU 사용량)이 미리 정의된 임계값에 도달할 때 휴대폰으로 연락하거나 문자 메시지를 보낼 수 있습니다. 이 문서는 또한 경고 기간 설정에 대한 모범 사례를 제공합니다.


## <a name="overview"></a>개요

Azure 서비스 또는 Azure 서비스의 이벤트에 대한 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

* **메트릭 값** - 이 경고는 특정 메트릭의 값이 어느 방향으로든 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 즉 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 트리거됩니다.

트리거되면 다음을 수행하도록 경고를 구성할 수 있습니다.

* 서비스 관리자 및 공동 관리자에게 메일 알림 보내기
* 사용자가 지정한 추가 메일 주소로 메일을 보냅니다.
* 음성 프롬프트를 사용하여 전화 걸기
* 문자 메시지 보내기
* 웹후크 호출
* Azure Function 호출
* Azure Runbook 호출
* 외부 티켓 ITSM 호환 시스템 호출

[Azure Portal, PowerShell 또는 Azure CLI](../../azure-monitor/alerts/alerts-classic-portal.md) 또는 [Azure Monitor REST API](/rest/api/monitor/alertrules)를 사용하여 경고 규칙에 대한 정보를 구성하고 가져올 수 있습니다. 

## <a name="alerting-metrics-available-for-managed-instance"></a>관리되는 인스턴스에 사용 가능한 경고 메트릭

> [!IMPORTANT]
> 경고 메트릭은 관리되는 인스턴스에만 사용할 수 있습니다. 관리되는 인스턴스에서 개별 데이터베이스의 경고 메트릭은 사용할 수 없습니다. 반면, 데이터베이스 진단 원격 분석은 [진단 로그](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export) 형식으로 제공됩니다. 진단 로그에 대한 경고는 관리되는 인스턴스용 [로그 경고 스크립트](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance)를 사용하여 [SQL Analytics](../../azure-monitor/insights/azure-sql.md) 제품 내에서 설정할 수 있습니다.

경고 구성에 사용할 수 있는 관리되는 인스턴스 메트릭은 다음과 같습니다.

| 메트릭 | 설명 | 측정 단위 / 가능한 값 |
| :--------- | --------------------- | ----------- |
| 평균 CPU 비율 | 선택한 기간의 평균 CPU 사용률입니다. | 0~100(%) |
| 읽은 IO 바이트 | 선택한 기간에 읽은 IO 바이트 수입니다. | 바이트 |
| 기록된 IO 바이트 | 선택한 기간에 쓴 IO 바이트 수입니다. | 바이트 |
| IO 요청 수 | 선택한 기간의 IO 요청 수입니다. | 숫자 |
| 예약된 스토리지 공간 | 관리되는 인스턴스에 예약된 현재 최대 스토리지 공간입니다. 리소스 크기 조정 작업을 사용하여 변경합니다. | MB(메가바이트) |
| 사용된 스토리지 공간 | 선택한 기간에 사용된 스토리지 공간입니다. 데이터베이스 및 인스턴스의 스토리지 사용량에 따라 변동합니다. | MB(메가바이트) |
| 가상 코어 수 | 관리되는 인스턴스용으로 프로비전된 vCore 수입니다. 리소스 크기 조정 작업을 사용하여 변경합니다. | 4~80(vCore) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure 포털에서 메트릭에 대한 경고 규칙 만들기

1. Azure [Portal](https://portal.azure.com/)에서 모니터링하려는 관리되는 인스턴스를 찾아서 선택합니다.

2. 모니터링 섹션에서 **메트릭** 메뉴 항목을 선택합니다.

   ![모니터링](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. 드롭다운 메뉴에서 경고를 설정하려는 메트릭 중 하나를 선택합니다(예제에서는 사용된 스토리지 공간이 표시됨).

4. 지정된 기간에 도달한 집계 기간 평균, 최솟값 또는 최댓값을 선택합니다(Avg, Min 또는 Max). 

5. **새 경고 규칙** 을 선택합니다.

6. 경고 규칙 만들기 창에서 **조건 이름** 을 클릭합니다(예제에서는 사용된 스토리지 공간이 표시됨).

   ![조건 정의](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. 신호 논리 구성 창에서 연산자, 집계 유형 및 임계값을 정의합니다.

   * 연산자 유형 옵션은 보다 큼, 같음 및 보다 작음(임계값 대비)입니다.
   * 집계 유형 옵션은 최솟값, 최댓값 또는 평균(집계 세분성 기간 내)입니다.
   * 임계값은 연산자 및 집계 조건에 따라 평가되는 경고값입니다.
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   스크린샷에 표시된 예제에서 1840876MB 값은 1.8TB의 임계값을 나타내는 데 사용됩니다. 예제에서 연산자가 보다 큼으로 설정되어 있으므로 관리되는 인스턴스의 스토리지 공간 사용량이 1.8TB를 초과하는 경우 경고가 생성됩니다. 스토리지 공간 메트릭에 대한 임계값은 MB 단위로 표시되어야 합니다.

8. 평가 기간 집계 세분성(분) 및 평가 빈도를 설정합니다. 평가 빈도는 경고 시스템에서 임계값 조건이 충족되었는지 주기적으로 확인하는 시간을 나타냅니다.

9. 작업 그룹을 선택합니다. 작업 그룹 창이 표시됩니다.이 창에서 기존 작업을 선택하거나 새 작업을 만들 수 있습니다. 이 작업은 경고를 트리거할 때(예: 이메일 보내기, 전화 걸기, 웹후크 실행, Azure 함수 실행 또는 Runbook 실행) 발생하는 동작을 정의합니다.

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * 새 작업 그룹을 만들려면 **+작업 그룹 만들기** 를 선택합니다.

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * 경고를 표시하는 방법을 정의합니다. 작업 그룹 이름, 약식 이름, 작업 이름을 입력하고 작업 유형을 선택합니다. 작업 유형은 이메일, 문자 메시지, 음성 통화를 통해 알림을 받을지, 웹후크, Azure 함수, Runbook이 실행될지, 호환 시스템에서 ITSM 티켓이 생성될지를 정의합니다.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. 레코드에 대한 경고 규칙 세부 정보를 입력하고 심각도 유형을 선택합니다.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * **경고 규칙 만들기** 단추를 클릭하여 경고 규칙 만들기를 완료합니다.

새 경고 규칙은 몇 분 내에 활성화되고 설정에 따라 트리거됩니다.

## <a name="verifying-alerts"></a>경고 확인

> [!NOTE]
> 노이즈가 있는 경고를 표시하지 않도록 하려면 [작업 규칙을 사용하여 경고 표시 안 함](../../azure-monitor/alerts/alerts-action-rules.md#suppression-of-alerts)을 참조하세요.

경고 규칙을 설정할 때 경고 트리거 및 해당 빈도가 적절한지 확인합니다. 사용된 스토리지 공간에 대한 경고를 설정하는 이 페이지에 표시된 예제의 경우 경고 옵션이 이메일이라면 아래 표시된 것과 같은 이메일을 받게 됩니다.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

이메일은 경고 이름, 임계값 세부 정보, 경고가 트리거된 이유를 표시하여 경고를 확인하고 문제를 해결하는 데 도움을 줍니다. **Azure Portal에서 확인** 단추를 사용하여 이메일을 통해 수신한 경고를 Azure Portal에서 볼 수 있습니다. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>기존 경고 규칙 보기, 일시 중지, 활성화, 수정 및 삭제

> [!NOTE]
> 기존 경고는 Azure Portal 대시보드의 경고 메뉴에서 관리해야 합니다. Managed Instance 리소스 블레이드에서는 기존 경고를 수정할 수 없습니다.

기존 경고를 확인, 일시 중지, 활성화, 수정 및 삭제하려면

1. Azure Portal 검색을 사용하여 경고를 검색합니다. 경고를 클릭합니다.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   또는, 구성한 경우 Azure 탐색 모음에서 경고를 클릭할 수도 있습니다.

2. 경고 창에서 경고 규칙 관리를 선택합니다.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   기존 경고의 목록이 표시됩니다. 개별 기존 경고 규칙을 선택하여 관리합니다. 기존 활성 규칙을 필요에 따라 수정하고 조정할 수 있습니다. 활성 규칙을 삭제하지 않고 일시 중지할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

* Azure Monitor 경고 시스템에 대해 알아봅니다. [Microsoft Azure의 경고 개요](../../azure-monitor/alerts/alerts-overview.md)를 참조하세요.
* 메트릭 경고에 대해 자세히 알아봅니다. [Azure Monitor에서 메트릭 경고가 작동하는 방식 이해](../../azure-monitor/alerts/alerts-metric-overview.md)를 참조하세요.
* 경고에서 웹후크를 구성하는 방법을 알아봅니다. [클래식 메트릭 경고를 사용하여 웹후크 호출](../../azure-monitor/alerts/alerts-webhooks.md)을 참조하세요
* PowerShell을 사용하여 경고를 구성 및 관리하는 방법을 알아봅니다. [작업 규칙](/powershell/module/az.monitor/add-azmetricalertrulev2)을 참조하세요.
* API를 사용하여 경고를 구성 및 관리하는 방법을 알아봅니다. [Azure Monitor REST API 참조](/rest/api/monitor/)를 참조하세요.