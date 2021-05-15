---
title: Azure Automation 업데이트 관리에 대한 경고를 만드는 방법
description: 이 문서에서는 업데이트 평가 또는 배포 상태에 대해 알리도록 Azure 경고를 구성하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 03/15/2021
ms.topic: conceptual
ms.openlocfilehash: 224a7b5457a099fd763ac657349fc5497824ab76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601434"
---
# <a name="how-to-create-alerts-for-update-management"></a>업데이트 관리에 대한 경고를 만드는 방법

Azure의 경고는 Runbook 작업, 서비스 상태 문제 또는 Automation 계정과 관련된 기타 시나리오의 결과를 사전에 알려 줍니다. Azure Automation에는 미리 구성된 경고 규칙이 포함되어 있지 않지만 생성되는 데이터를 기반으로 사용자 고유의 경고 규칙을 만들 수 있습니다. 이 문서에서는 업데이트 관리에 포함된 메트릭을 사용하여 경고 규칙을 만드는 방법에 대한 참고 자료를 제공합니다.

## <a name="available-metrics"></a>사용 가능한 메트릭

Azure Automation은 수집되어 Azure Monitor로 전달되는 업데이트 관리와 관련된 두 가지의 고유한 플랫폼 메트릭을 만듭니다. 이러한 메트릭은 [메트릭 탐색기](../../azure-monitor/essentials/metrics-charts.md)를 사용하여 분석하고 [메트릭 경고 규칙](../../azure-monitor/alerts/alerts-metric.md)을 사용하여 경고하는 데 사용할 수 있습니다.

내보내진 두 메트릭은 다음과 같습니다.

* 총 업데이트 배포 머신 실행
* 총 업데이트 배포 실행

경고에 사용되는 경우 이 두 메트릭은 추가 정보를 전달하는 차원을 지원하여 특정 업데이트 배포 세부 정보에 대한 경고 범위를 지정합니다. 다음 표에서는 경고를 구성할 때 사용할 수 있는 메트릭 및 차원에 대한 세부 정보를 보여줍니다.

|신호 이름|차원|Description
|---|---|---|
|`Total Update Deployment Runs`|- 업데이트 배포 이름<br>- 상태 | 업데이트 배포의 전체 상태를 알립니다.|
|`Total Update Deployment Machine Runs`|- 업데이트 배포 이름</br>- 상태</br>- 대상 컴퓨터</br>- 업데이트 배포 실행 ID    |특정 머신을 대상으로 하는 업데이트 배포의 상태를 알립니다.|

## <a name="create-alert"></a>경고 만들기

업데이트 배포 상태를 알려 주도록 경고를 설정하려면 아래 단계를 따르세요. Azure 경고를 처음 사용하는 경우 [Azure 경고 개요](../../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

1. Automation 계정의 **모니터링** 아래에서 **경고** 를 선택한 다음, **새 경고 규칙** 을 선택합니다.

1. **경고 규칙 만들기** 페이지에 Automation 계정이 이미 리소스로 선택되어 있습니다. 변경하려면 **리소스 편집** 을 선택합니다.

1. 리소스 선택 페이지의 **리소스 종류별로 필터링** 드롭다운 목록에서 **Automation 계정** 을 선택합니다.

1. 사용하려는 Automation 계정을 선택한 다음, **완료** 를 선택합니다.

1. **조건 추가** 를 선택하여 요구 사항에 적합한 신호를 선택합니다.

1. 차원의 경우 목록에서 유효한 값을 선택합니다. 원하는 값이 목록에 없으면 해당 차원 옆의 **\+** 를 선택하고 사용자 지정 이름을 입력합니다. 그런 다음, 찾을 값을 선택합니다. 차원의 모든 값을 선택하려면 **선택\*** 단추를 선택합니다. 차원 값을 선택하지 않으면 업데이트 관리에서 해당 차원을 무시합니다.

    ![신호 논리 구성](./media/manage-updates-for-vm/signal-logic.png)

1. **경고 논리** 에서 **시간 집계** 및 **임계값** 필드에 값을 입력한 다음, **완료** 를 선택합니다.

1. 다음 페이지에서 경고의 이름과 설명을 입력합니다.

1. **심각도** 필드는 성공적인 실행의 경우 **정보(심각도 2)** 로 설정하고, 실패한 실행의 경우 **정보(심각도 1)** 로 설정합니다.

    ![경고 규칙 이름, 설명 및 심각도 필드가 강조 표시된 경고 세부 정보 정의 섹션을 보여주는 스크린 샷.](./media/manage-updates-for-vm/define-alert-details.png)

1. **예** 를 선택하여 경고 규칙을 사용하도록 설정합니다.

## <a name="configure-action-groups-for-your-alerts"></a>경고에 대한 작업 그룹 구성

경고가 구성되었으면 여러 경고에서 사용할 작업 그룹을 설정할 수 있습니다. 이 작업에는 이메일 알림, Runbook, webhook 등이 포함될 수 있습니다. 작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../../azure-monitor/alerts/action-groups.md)를 참조하세요.

1. 경고를 선택한 다음 **작업** 에서 **작업 그룹 추가** 를 선택합니다. 그러면 **이 경고 규칙에 연결할 작업 그룹 선택** 창이 표시됩니다.

   :::image type="content" source="./media/manage-updates-for-vm/select-an-action-group.png" alt-text="사용량 및 예상 비용.":::

1. 연결할 작업 그룹의 확인란을 선택하고 선택을 누릅니다.

## <a name="next-steps"></a>다음 단계

* Azure Monitor의 [경고](../../azure-monitor/alerts/alerts-overview.md)에 대해 자세히 알아봅니다.

* Log Analytics 작업 영역에서 데이터를 검색하고 분석하는 [로그 쿼리](../../azure-monitor/logs/log-query-overview.md)에 대해 알아봅니다.

* [Azure Monitor 로그를 사용하여 사용량 및 비용](../../azure-monitor/logs/manage-cost-storage.md) 관리는 데이터 보존 기간을 변경하여 비용을 제어하는 방법 및 데이터 사용량에 대한 분석 및 경고 방법을 설명합니다.