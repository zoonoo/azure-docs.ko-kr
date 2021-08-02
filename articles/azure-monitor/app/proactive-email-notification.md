---
title: 스마트 검색 알림 변경 - Azure Application Insights
description: 스마트 검색에서 기본 알림 받는 사람으로 변경합니다. 스마트 검색을 사용하면 Azure Application Insights에서 추적 원격 분석의 비정상적인 패턴을 검색하여 애플리케이션 추적을 모니터링할 수 있습니다.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 0937683eaacefc9fe4bbee21802a0f1657918efd
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536698"
---
# <a name="smart-detection-e-mail-notification-change"></a>스마트 검색 메일 알림 변경

>[!NOTE]
>Application Insight 리소스를 경고 기반 스마트 검색(미리 보기)으로 마이그레이션할 수 있습니다. 마이그레이션은 다양한 스마트 검색 모듈에 대한 경고 규칙을 만듭니다. 경고 규칙이 만들어지면 다른 Azure Monitor 경고 규칙과 마찬가지로 이러한 규칙을 관리하고 구성할 수 있습니다. 또한 이러한 규칙에 대한 작업 그룹을 구성할 수 있으므로 여러 가지 방법으로 작업을 수행하거나 새 검색에 대한 알림을 트리거할 수 있습니다.
>
> 마이그레이션 프로세스 및 마이그레이션 후 스마트 검색 동작에 대한 자세한 내용은 [스마트 검색 경고 마이그레이션](../alerts/alerts-smart-detections-migration.md)을 참조하세요.

고객 피드백에 따라, 2019년 4월 1일에 스마트 검색에서 메일 알림을 받는 기본 역할이 변경됩니다.

## <a name="what-is-changing"></a>변경되는 내용

현재 스마트 검색 메일 알림은 기본적으로 ‘Subscription Owner’, ‘Subscription Contributor’ 및 ‘Subscription Reader’ 역할에 전송됩니다. 모니터링에 적극적으로 참여하지 않는 사용자가 이러한 역할에 포함되는 경우가 많으며, 이로 인해 많은 사용자가 불필요하게 알림을 수신하게 됩니다. 이 환경을 개선하기 위해 메일 알림이 기본적으로 [Monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) 및 [Monitoring Contributor](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 역할로만 이동하도록 변경됩니다.

## <a name="scope-of-this-change"></a>이 변경의 범위

이 변경은 다음을 제외한 모든 스마트 검색 규칙에 영향을 줍니다.

* 미리 보기로 표시된 스마트 검색 규칙. 이러한 스마트 검색 규칙은 현재 메일 알림을 지원하지 않습니다.

* 오류 이상 규칙.

## <a name="how-to-prepare-for-this-change"></a>이 변경을 준비하는 방법

스마트 검색의 메일 알림이 관련 사용자에게 전송되도록 하려면 해당 사용자를 구독의 [Monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) 및 [Monitoring Contributor](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 역할에 할당해야 합니다.

Azure Portal을 통해 Monitoring Reader 또는 Monitoring Contributor 역할에 사용자를 할당하려면 [역할 할당 추가](../../role-based-access-control/role-assignments-portal.md) 문서에 설명된 단계를 수행합니다. 사용자가 할당되는 역할로 ‘Monitoring Reader’ 또는 ‘Monitoring Contributor’를 선택해야 합니다.

> [!NOTE]
> 규칙 설정의 ‘추가 메일 수신자’ 옵션을 사용하여 구성된 스마트 검색 알림의 특정 수신자는 이 변경의 영향을 받지 않습니다. 해당 수신자는 메일 알림을 계속 수신합니다.

이 변경에 대한 질문이나 우려 사항이 있으면 [Microsoft에 문의](mailto:smart-alert-feedback@microsoft.com)하시기 바랍니다.

## <a name="next-steps"></a>다음 단계

스마트 검색에 대해 자세히 알아봅니다.

- [오류 잘못된 부분](./proactive-failure-diagnostics.md)
- [메모리 누수](./proactive-potential-memory-leak.md)
- [성능 이상](./proactive-performance-diagnostics.md)

