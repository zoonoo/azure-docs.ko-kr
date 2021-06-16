---
title: Azure Portal에서 경고 및 알림 설정
description: Azure 포털을 사용하여 사용자가 지정한 조건에 부합하면 알림이나 자동 작업을 트리거할 수 있는 경고를 만듭니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: AlainDormehlMSFT
ms.author: aldorme
ms.reviewer: mathoma, wiassaf
ms.date: 05/04/2020
ms.openlocfilehash: afedce181d9d7c7e2feb476716a453c556344091
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110706517"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure SQL Database 및 Azure Synapse Analytics에 대한 경고 만들기
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>개요

이 문서에서는 Azure Portal을 사용하여 Azure SQL Database와 Azure Synapse Analytics에서 데이터베이스 경고를 설정하는 방법을 보여 줍니다. 메트릭(예: 데이터베이스 크기 또는 CPU 사용량)이 임계값에 도달하면 경고에서 이메일을 보내거나 webhook를 호출할 수 있습니다.

> [!NOTE]
> Azure SQL Managed Instance 관련 지침은 [Azure SQL Managed Instance에 대한 경고 만들기](../managed-instance/alerts-create.md)를 참조하세요.

Azure 서비스 또는 Azure 서비스의 이벤트에 대한 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

* **메트릭 값** - 이 경고는 특정 메트릭의 값이 어느 방향으로든 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 즉 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 트리거됩니다.
* **활동 로그 이벤트** - *모든* 이벤트에 대해 또는 특정 이벤트 수가 발생했을 때만 경고를 트리거할 수 있습니다

트리거되면 다음을 수행하도록 경고를 구성할 수 있습니다.

* 서비스 관리자 및 공동 관리자에게 메일 알림을 보냅니다.
* 사용자가 지정한 추가 메일 주소로 메일을 보냅니다.
* 웹후크 호출

다음을 통해 경고에 대한 정보를 구성하고 가져올 수 있습니다.

* [Azure Portal](../../azure-monitor/alerts/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/alerts/alerts-classic-portal.md)
* [명령줄 인터페이스(CLI)](../../azure-monitor/alerts/alerts-classic-portal.md)
* [Azure Monitor REST API](/rest/api/monitor/alertrules)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure 포털에서 메트릭에 대한 경고 규칙 만들기

1. [포털](https://portal.azure.com/)에서 모니터링하려는 리소스를 찾아 선택합니다.
2. 모니터링 섹션에서 **경고** 를 선택합니다. 텍스트와 아이콘은 리소스마다 약간씩 다를 수 있습니다.  

   ![모니터링](./media/alerts-insights-configure-portal/Alerts.png)
  
3. **새 경고 규칙** 단추를 선택하여 **규칙 만들기** 페이지를 엽니다.
  ![규칙 만들기](./media/alerts-insights-configure-portal/create-rule.png)

4. **조건** 섹션에서 **추가** 를 클릭합니다.
  ![조건 정의](./media/alerts-insights-configure-portal/create-rule.png)
5. **신호 논리 구성** 페이지에서 신호를 선택합니다.
  ![신호 선택](./media/alerts-insights-configure-portal/select-signal.png)
6. **CPU 백분율** 등의 신호를 선택하면 **신호 논리 구성** 페이지가 나타납니다.
  ![신호 논리 구성](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. 이 페이지에서 해당 임계값 유형, 연산자, 집계 유형, 임계값, 집계 세분성 및 평가 빈도를 구성합니다. **완료** 를 클릭합니다.
8. **규칙 만들기** 에서 기존 **작업 그룹** 을 선택하거나 새 그룹을 만듭니다. 작업 그룹을 사용하면 경고 조건이 발생할 때 수행할 작업을 정의할 수 있습니다.
  ![작업 그룹 정의](./media/alerts-insights-configure-portal/action-group.png)

9. 규칙의 이름을 정의하고, 선택적인 설명을 제공하고, 규칙의 심각도 수준을 선택하고, 규칙 생성 즉시 규칙을 사용할지 여부를 선택한 후 **규칙 경고 만들기** 를 클릭하여 메트릭 규칙 경고를 만듭니다.

10분 안에 앞서 설명한 대로 경고가 활성화 및 트리거됩니다.

## <a name="next-steps"></a>다음 단계

* [경고에서의 webhook 구성](../../azure-monitor/alerts/alerts-webhooks.md)에 대해 자세히 알아봅니다.