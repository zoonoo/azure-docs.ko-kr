---
title: Azure Application Insights와 Azure Diagnostics 통합을 사용하여 Azure Cloud Services의 문제에 대한 경고 | Microsoft Docs
description: Azure Application Insights를 통해 Azure Cloud Services에서 시작 오류, 충돌 및 역할 재활용 루프와 같은 문제 모니터링
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.author: mbullwin
ms.openlocfilehash: c71ace039c8b8b7ec89c5a38ef203399f5af82aa
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54004716"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Azure Application Insights와 Azure Diagnostics 통합을 사용하여 Azure Cloud Services의 문제에 대한 경고

이 문서에서는 Azure Cloud Services에서 시작 오류, 충돌 및 역할 재활용 루프와 같은 문제를 모니터링하는 경고 규칙을 설정하는 방법을 설명합니다(웹 및 작업자 역할).

이 문서에서 설명하는 방법은 [Application Insights와 Azure Diagnostics 통합](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/) 및 최근에 릴리스된 [Application Insights에 대한 로그 경고](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) 기능을 기반으로 합니다.

## <a name="define-a-base-query"></a>기본 쿼리 정의

시작하기 위해 Application Insights에 추적 레코드로 캡처되는 Windows 이벤트 로그 이벤트를 Windows Azure 채널에서 검색하는 기본 쿼리를 정의하겠습니다.
이러한 레코드는 시작 오류, 런타임 오류 및 재활용 루프와 같은 Azure Cloud Services의 다양한 문제를 감지하는 데 사용할 수 있습니다.

> [!NOTE]
> 다음 기본 쿼리는 30분 동안 문제를 확인하고 원격 분석 레코드 수집에 10분의 대기 시간을 가정합니다. 이러한 기본값은 사용자가 원하는 대로 구성할 수 있습니다.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>특정 이벤트 ID에 대한 확인

Windows 이벤트 로그 이벤트를 검색한 후, 해당 이벤트 ID 및 메시지 속성을 검사하면 특정 문제가 감지될 수 있습니다(아래 예제 참조).
단순히 위에 나온 기본 쿼리를 아래 쿼리 중 하나와 결합하고 로그 경고 규칙을 정의할 때 결합된 쿼리를 사용한 것입니다.

> [!NOTE]
> 아래 예제에서는 분석된 특정 기간 동안 세 개 이상의 이벤트가 발견되면 문제가 감지됩니다. 이 기본값을 구성하여 경고 규칙의 중요도를 변경할 수 있습니다.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>경고 만들기

Application Insights 리소스 내 탐색 메뉴에서 **경고**로 이동한 다음, **새 경고 규칙**을 선택합니다.

![규칙 만들기 스크린샷](./media/proactive-cloud-services/001.png)

**규칙 만들기** 창의 **경고 조건 정의** 섹션에서 **기준 추가**를 클릭한 다음, **사용자 지정 로그 검색**을 선택합니다.

![경고에 대한 조건 기준 정의의 스크린샷](./media/proactive-cloud-services/002.png)

**쿼리 검색** 상자에서 이전 단계에서 준비한 결합된 쿼리를 붙여넣습니다.

그런 다음, **임계값** 상자로 이동하여 해당 값을 0으로 설정합니다. 필요에 따라 **기간** 및 주파수 **필드**를 수정할 수도 있습니다.
**Done**을 클릭합니다.

![신호 논리 구성 쿼리의 스크린샷](./media/proactive-cloud-services/003.png)

**경고 세부 정보 정의** 섹션에서 경고 규칙에 대한 **이름** 및 **설명**을 입력하고 **심각도**를 설정합니다.
또한 **규칙을 만들면 바로 사용** 단추가 **예**로 설정되어 있는지 확인합니다.

![경고 세부 정보 스크린샷](./media/proactive-cloud-services/004.png)

**작업 그룹 정의** 섹션에서 기존 **작업 그룹**을 선택하거나 새로 만들 수 있습니다.
작업 그룹에 다양한 종류의 여러 작업을 포함하도록 선택할 수도 있습니다.

![작업 그룹 스크린샷](./media/proactive-cloud-services/005.png)

작업 그룹을 정의하고 나면 변경 내용을 확인하고 **경고 규칙 만들기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

자동으로 감지하는 방법에 대해 자세히 알아봅니다.

[오류 잘못된 부분](../../application-insights/app-insights-proactive-failure-diagnostics.md)
[메모리 누수](../../application-insights/app-insights-proactive-potential-memory-leak.md)
[성능 이상](../../application-insights/app-insights-proactive-performance-diagnostics.md)

