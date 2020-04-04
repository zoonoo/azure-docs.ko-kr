---
title: 관리되는 인스턴스에 대한 설정 경고 및 알림(Azure 포털)
description: Azure 포털을 사용하여 지정한 조건이 충족될 때 알림 또는 자동화를 트리거할 수 있는 SQL 관리형 인스턴스 경고를 만듭니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/02/2020
ms.openlocfilehash: a332627d149a36ba5d5beb2626023e58a221f0d6
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639199"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure SQL 관리 인스턴스에 대한 경고 만들기

## <a name="overview"></a>개요

이 문서에서는 Azure 포털을 사용하여 Azure SQL 관리 인스턴스 데이터베이스의 데이터베이스에 대한 경고를 설정하는 방법을 보여 주며 있습니다. 일부 메트릭(예: 인스턴스 저장소 크기 또는 CPU 사용량)이 임계값에 도달하면 경고를 보내거나 웹 후크를 호출할 수 있습니다. 이 문서는 또한 경고 기간 설정에 대한 모범 사례를 제공합니다.

Azure 서비스 또는 Azure 서비스의 이벤트에 대한 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

* **메트릭 값** - 지정된 메트릭 값이 어느 방향으로든 할당된 임계값을 초과할 때 경고가 트리거됩니다. 즉 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 트리거됩니다.
* **활동 로그 이벤트** - *경고는 모든* 이벤트에서 트리거되거나 특정 수의 이벤트가 발생하는 경우에만 트리거할 수 있습니다.

트리거되면 다음을 수행하도록 경고를 구성할 수 있습니다.

* 서비스 관리자 및 공동 관리자에게 메일 알림을 보냅니다.
* 사용자가 지정한 추가 메일 주소로 메일을 보냅니다.
* 웹후크 호출

다음을 통해 경고에 대한 정보를 구성하고 가져올 수 있습니다.

* [Azure portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [명령줄 인터페이스(CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure 포털에서 메트릭에 대한 경고 규칙 만들기

1. [포털](https://portal.azure.com/)에서 모니터링하려는 리소스를 찾아 선택합니다.
2. 모니터링 섹션에서 **경고를 선택합니다.** 텍스트와 아이콘은 리소스마다 약간씩 다를 수 있습니다.  

   ![모니터링](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. 새 **경고 규칙** 단추를 선택하여 **규칙 만들기** 페이지를 엽니다.
   ![규칙 만들기](media/sql-database-insights-alerts-portal/create-rule.png)

4. **조건** 섹션에서 **추가**를 클릭합니다.
   ![조건 정의](media/sql-database-insights-alerts-portal/create-rule.png)
5. 신호 **논리 구성** 페이지에서 신호를 선택합니다.
   ![신호 선택](media/sql-database-insights-alerts-portal/select-signal.png)
6. **CPU 백분율과**같은 신호를 선택하면 **신호 논리 구성** 페이지가 나타납니다.
   ![신호 논리 구성](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. 이 페이지에서 해당 임계값 유형, 연산자, 집계 유형, 임계값, 집계 세분성 및 평가 빈도를 구성합니다. 그런 다음 **완료 를 클릭합니다.**
8. 만들기 **규칙에서**기존 **작업 그룹을** 선택하거나 새 그룹을 만듭니다. 작업 그룹을 사용하면 경고 조건이 발생할 때 수행할 작업을 정의할 수 있습니다.
  ![작업 그룹 정의](media/sql-database-insights-alerts-portal/action-group.png)

9. 규칙 의 이름을 정의하고, 선택적 설명을 제공하고, 규칙에 대한 심각도 수준을 선택하고, 규칙 생성 시 규칙을 사용하도록 설정할지 여부를 선택한 다음 **규칙 경고 만들기를** 클릭하여 메트릭 규칙 경고를 만듭니다.

10분 이내에 경고가 활성화되고 앞서 설명한 대로 트리거됩니다.

## <a name="next-steps"></a>다음 단계

* [경고에서의 webhook 구성](../azure-monitor/platform/alerts-webhooks.md)에 대해 자세히 알아봅니다.
