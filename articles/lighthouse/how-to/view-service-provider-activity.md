---
title: 서비스 공급자 작업 보기
description: 고객은 기록된 활동을 보고 Azure 위임된 리소스 관리를 통해 서비스 공급자가 수행하는 작업을 볼 수 있습니다.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649639"
---
# <a name="view-service-provider-activity"></a>서비스 공급자 작업 보기

Azure 위임된 리소스 관리에 대한 구독을 위임한 고객은 [Azure Activity 로그](../../azure-monitor/platform/platform-logs-overview.md) 데이터를 보고 수행된 모든 작업을 볼 수 있습니다. 이를 통해 고객은 Azure 위임된 리소스 관리를 통해 서비스 공급자가 수행하는 작업에 대한 완전한 가시성과 고객의 Azure Active Directory(Azure AD) 테넌트 내의 사용자가 수행한 작업을 제공합니다.

> [!TIP]
> 또한 관리 테넌트에 대한 범위 위임을 감사하기 위해 Azure Policy 기본 제공 정책 정의를 제공합니다. 자세한 내용은 [사용자 환경의 감사 위임을](view-manage-service-providers.md#audit-delegations-in-your-environment)참조하십시오.

## <a name="view-activity-log-data"></a>활동 로그 데이터 보기

Azure [포털의](../../azure-monitor/platform/activity-log-view.md) **모니터** 메뉴에서 활동 로그를 볼 수 있습니다. 결과를 특정 구독으로 제한하려면 필터를 사용하여 특정 구독을 선택합니다. 또한 [프로그램적으로 활동 로그 이벤트를 보고 검색할](../../azure-monitor/platform/activity-log-view.md) 수도 있습니다.

> [!NOTE]
> 서비스 공급자의 테넌트의 사용자는 Azure 위임된 리소스 관리에 대한 구독이 온보딩된 경우 [Reader](../../role-based-access-control/built-in-roles.md#reader) 역할(또는 Reader 액세스를 포함하는 다른 기본 제공 역할)이 부여된 경우 고객 테넌트에서 위임된 구독에 대한 활동 로그 결과를 볼 수 있습니다.

활동 로그에서 작업 이름과 해당 상태는 수행된 날짜 및 시간과 함께 표시됩니다. 열로 **시작된 이벤트는** Azure 위임리소스 관리를 통해 동작하는 서비스 공급자의 테넌트의 사용자인지 아니면 고객의 자체 테넌트의 사용자인지 여부에 관계없이 작업을 수행한 사용자를 보여 주었습니다. 테넌트 나 해당 구독에 대해 할당 된 역할이 아니라 사용자 이름이 표시됩니다.

기록된 활동은 지난 90일 동안 Azure 포털에서 사용할 수 있습니다. 이 데이터를 90일 이상 저장하는 방법에 대해 알아보려면 [Log Analytics 작업 영역에서 Azure 활동 로그 수집 및 분석](../../azure-monitor/platform/activity-log-collect.md)을 참조하세요.

> [!NOTE]
> 서비스 공급자의 사용자는 활동 로그에 나타나지만 이러한 사용자와 해당 역할 할당은 **IAM(액세스 제어)** 또는 API를 통해 역할 할당 정보를 검색할 때 표시되지 않습니다.

## <a name="set-alerts-for-critical-operations"></a>중요한 작업에 대한 경고 설정

서비스 공급자(또는 사용자)가 수행하는 중요한 작업을 인식하려면 [활동 로그 경고를](../../azure-monitor/platform/activity-log-alerts.md)만드는 것이 좋습니다. 예를 들어 구독에 대한 모든 관리 작업을 추적하거나 특정 리소스 그룹의 가상 컴퓨터가 삭제될 때 알림을 받을 수 있습니다. 경고를 만들 때 고객의 테넌트뿐만 아니라 관리되는 테넌트의 사용자가 수행하는 작업이 포함됩니다.

자세한 내용은 [활동 로그 경고 만들기 및 관리](../../azure-monitor/platform/alerts-activity-log.md)를 참조하십시오.

## <a name="create-log-queries"></a>로그 쿼리 만들기

쿼리를 만들어 기록된 활동을 분석하거나 특정 항목에 집중할 수 있습니다. 예를 들어 감사를 수행하려면 구독에서 수행된 모든 관리 수준 작업을 보고해야 할 수 있습니다. 이러한 작업만 필터링하고 사용자, 날짜 또는 다른 값별로 결과를 정렬하는 쿼리를 만들 수 있습니다.

자세한 내용은 [Azure Monitor 의 로그 쿼리 개요를](../../azure-monitor/log-query/log-query-overview.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [Azure 모니터에](../../azure-monitor/index.yml)대해 자세히 알아보기.
- Azure 포털에서 [서비스 공급자 의 제안을 보고 관리하는](view-manage-service-providers.md) 방법을 알아봅니다.