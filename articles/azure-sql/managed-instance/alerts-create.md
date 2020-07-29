---
title: Managed Instance에 대 한 알림 및 알림 설정 (Azure Portal)
description: Azure Portal를 사용 하 여 SQL Managed Instance 경고를 만들 수 있습니다 .이 경고는 지정 된 조건이 충족 될 때 알림이나 자동화를 트리거할 수 있습니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: ae139dd65242be9456f3498c494e1a7c5a29402f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695702"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure SQL Managed Instance에 대 한 경고 만들기
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure Portal를 사용 하 여 Azure SQL Managed Instance 데이터베이스의 데이터베이스에 대 한 경고를 설정 하는 방법을 보여 줍니다. 경고는 전자 메일을 보내거나, 웹 후크를 호출 하 고, Azure Function을 실행 하 고, runbook을 실행 하거나, 외부 ITSM 호환 티켓 시스템을 호출 하거나, 예를 들어 인스턴스 저장소 크기 또는 CPU 사용량과 같은 일부 메트릭이 미리 정의 된 임계값에 도달할 때 휴대폰에서 호출 하거나 문자 메시지를 보낼 수 있습니다. 이 문서는 또한 경고 기간 설정에 대한 모범 사례를 제공합니다.


## <a name="overview"></a>개요

Azure 서비스 또는 Azure 서비스의 이벤트에 대한 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

* **메트릭 값** -지정 된 메트릭의 값이 어느 방향으로든 할당 하는 임계값을 초과할 경우 경고가 트리거됩니다. 즉 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 트리거됩니다.

트리거되면 다음을 수행하도록 경고를 구성할 수 있습니다.

* 서비스 관리자 및 공동 관리자에 게 전자 메일 알림 보내기
* 사용자가 지정한 추가 메일 주소로 메일을 보냅니다.
* 음성 프롬프트를 사용 하 여 전화 번호 호출
* 전화 번호로 문자 메시지 보내기
* 웹후크 호출
* Azure 함수 호출
* Azure runbook 호출
* 외부 티켓 ITSM 호환 시스템 호출

[Azure Portal, PowerShell 또는 Azure CLI](../../azure-monitor/platform/alerts-classic-portal.md) 또는 [Azure Monitor REST API](/rest/api/monitor/alertrules)를 사용 하 여 경고 규칙에 대 한 정보를 구성 하 고 가져올 수 있습니다. 

## <a name="alerting-metrics-available-for-managed-instance"></a>관리 되는 인스턴스에 사용할 수 있는 경고 메트릭

> [!IMPORTANT]
> 경고 메트릭은 관리 되는 인스턴스에만 사용할 수 있습니다. 관리 되는 인스턴스의 개별 데이터베이스에 대 한 경고 메트릭은 사용할 수 없습니다. 데이터베이스 진단 원격 분석은 [진단 로그](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)형식으로 제공 되는 기타 기능을 제공 합니다. 관리 되는 인스턴스의 [로그 경고 스크립트](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance) 를 사용 하 여 [SQL 분석](../../azure-monitor/insights/azure-sql.md) 제품 내에서 진단 로그에 대 한 경고를 설정할 수 있습니다.

경고 구성에 사용할 수 있는 관리 되는 인스턴스 메트릭은 다음과 같습니다.

| 메트릭 | 설명 | 측정 단위 (가능한 값) |
| :--------- | --------------------- | ----------- |
| 평균 CPU 비율 | 선택한 기간의 평균 CPU 사용률입니다. | 0-100 (%) |
| 읽은 IO 바이트 | 선택한 기간에서 읽은 IO 바이트 수입니다. | 바이트 |
| 기록된 IO 바이트 | 선택한 기간 동안 쓴 IO 바이트 수입니다. | 바이트 |
| IO 요청 수 | 선택한 기간의 IO 요청 수입니다. | 숫자 |
| 예약된 스토리지 공간 | 현재 최대입니다. 관리 되는 인스턴스에 대해 예약 된 저장소 공간입니다. 리소스 크기 조정 작업을 사용 하 여 변경 합니다. | MB (메가바이트) |
| 사용된 스토리지 공간 | 선택한 기간에 사용 되는 저장소 공간입니다. 데이터베이스 및 인스턴스의 저장소 사용에 대 한 변경 내용 | MB (메가바이트) |
| 가상 코어 수 | 관리 되는 인스턴스에 대해 프로 비전 된 vCores입니다. 리소스 크기 조정 작업을 사용 하 여 변경 합니다. | 4-80 (vCores) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure 포털에서 메트릭에 대한 경고 규칙 만들기

1. Azure [portal](https://portal.azure.com/)에서 모니터링 하려는 관리 되는 인스턴스를 찾아서 선택 합니다.

2. 모니터링 섹션에서 **메트릭** 메뉴 항목을 선택 합니다.

   ![모니터링](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. 드롭다운 메뉴에서 경고를 설정 하려는 메트릭 중 하나를 선택 합니다 (사용 중인 저장소 공간은 예제에 표시 됨).

4. 지정 된 기간 (Avg, Min 또는 Max)에서 집계 기간-평균, 최소값 또는 최대값에 도달 함을 선택 합니다. 

5. **새 경고 규칙** 선택

6. 경고 규칙 만들기 창에서 **조건 이름** 을 클릭 합니다 (사용 된 저장소 공간은 예제에 표시 됨).

   ![조건 정의](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. 신호 논리 구성 창에서 연산자, 집계 유형 및 임계값을 정의 합니다.

   * 연산자 유형 옵션이 보다 큼, 같음 및 보다 작음 (임계값)
   * 집계 유형 옵션은 min, max 또는 average (집계 세분성 기간)입니다.
   * 임계값은 연산자 및 집계 조건에 따라 평가 되는 경고 값입니다.
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   스크린샷에 표시 된 예제에서 1840876 MB 값은 1.8 TB의 임계값을 나타내는 데 사용 됩니다. 예제의 연산자가 보다 큼으로 설정 되 면 관리 되는 인스턴스의 저장소 공간 사용량이 1.8 TB를 초과 하는 경우 경고가 생성 됩니다. 저장소 공간 메트릭에 대 한 임계값은 MB 단위로 표시 되어야 합니다.

8. 평가 기간 집계 세분성을 분 단위로 설정 하 고 평가 빈도를 설정 합니다. 평가 빈도는 경고 시스템에서 임계값 조건이 충족 되었는지 주기적으로 확인 하는 시간을 나타냅니다.

9. 작업 그룹을 선택 합니다. 작업 그룹 창이 표시 됩니다 .이 창에서 기존를 선택 하거나 새 작업을 만들 수 있습니다. 이 작업은 경고를 트리거할 때 (예: 전자 메일 보내기, 휴대폰에서 호출, 웹 후크, Azure 함수 또는 runbook 실행)에 발생 하는 작업을 정의 합니다.

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * 새 작업 그룹을 만들려면 **+ 작업 그룹 만들기** 를 선택 합니다.

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * 작업 그룹 이름, 약식 이름, 작업 이름 및 작업 유형 선택을 입력 하 여 경고를 표시 하는 방법을 정의 합니다. 작업 유형은 전자 메일, 문자 메시지, 음성 통화를 통해 알릴지 아니면 webhook, Azure function, runbook이 실행 될 경우, 또는 ITSM 티켓이 호환 되는 시스템에서 생성 될 경우를 정의 합니다.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. 레코드에 대 한 경고 규칙 세부 정보를 입력 하 고 심각도 유형을 선택 합니다.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * **경고 규칙 만들기** 단추를 클릭 하 여 경고 규칙 만들기를 완료 합니다.

새 경고 규칙은 몇 분 내에 활성화 되 고 설정에 따라 트리거됩니다.

## <a name="verifying-alerts"></a>경고 확인

> [!NOTE]
> 잡음이 있는 경고를 표시 하지 않도록 하려면 [작업 규칙을 사용 하 여 경고 Supression](../../azure-monitor/platform/alerts-action-rules.md#suppression-of-alerts)를 참조 하세요.

경고 규칙을 설정 하는 동안 경고 트리거와 해당 빈도로 만족 하는지 확인 합니다. 사용 되는 저장소 공간에 대 한 경고를 설정 하는 데 사용 되는 저장소 공간에 대 한 경고를 설정 하는이 페이지의 예제에서는 경고 옵션이 전자 메일 인 경우 아래와 같은 전자 메일을 받을 수 있습니다.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

전자 메일은 경고 이름, 임계값의 세부 정보 및 경고를 트리거한 이유를 표시 하 여 경고를 확인 하 고 해결 하는 데 도움을 줍니다. **Azure Portal에서 확인** 단추를 사용 하 여 Azure Portal에서 전자 메일을 통해 수신 되는 경고를 볼 수 있습니다. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>기존 경고 규칙 보기, 일시 중단, 활성화, 수정 및 삭제

> [!NOTE]
> Azure Portal 대시보드의 경고 메뉴에서 기존 경고를 관리 해야 합니다. Managed Instance 리소스 블레이드에서 기존 경고를 수정할 수 없습니다.

기존 경고를 확인, 일시 중단, 활성화, 수정 및 삭제 하려면:

1. Azure Portal 검색을 사용 하 여 경고를 검색 합니다. 경고를 클릭 합니다.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   또는 구성 된 경우 Azure 탐색 모음에서 경고를 클릭할 수도 있습니다.

2. 경고 창에서 경고 규칙 관리를 선택 합니다.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   기존 경고 목록이 표시 됩니다. 관리할 개별 기존 경고 규칙을 선택 합니다. 기존 활성 규칙을 기본 설정으로 수정 하 고 조정할 수 있습니다. 활성 규칙을 삭제 하지 않고 일시 중지할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

* Azure Monitor 경고 시스템에 대 한 자세한 내용은의 [경고 개요](../../azure-monitor/platform/alerts-overview.md) 를 참조 하세요 Microsoft Azure
* 메트릭 경고에 대 한 자세한 내용은 [메트릭 경고 작동 방법 이해 Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md)
* 경고에서 웹 후크를 구성 하는 방법에 대 한 자세한 내용은 [클래식 메트릭 경고로 Webhook 호출](../../azure-monitor/platform/alerts-webhooks.md)
* PowerShell을 사용 하 여 경고를 구성 하 고 관리 하는 방법에 대 한 자세한 내용은 [작업 규칙](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)
* API를 사용 하 여 경고를 구성 하 고 관리 하는 방법에 대 한 자세한 내용은 [Azure Monitor REST API 참조](https://docs.microsoft.com/rest/api/monitor/) 
