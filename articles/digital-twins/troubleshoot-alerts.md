---
title: 경고 설정
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 메트릭에 대해 경고를 사용하도록 설정하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 461d7a82854ce62ee99eef1227c13c7a8f0371e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100594870"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Azure Digital Twins 문제 해결: 경고

Azure Digital Twins는 리소스의 상태에 대한 정보를 제공하는 서비스 인스턴스에 대한 [메트릭](troubleshoot-metrics.md)을 수집합니다. 이러한 메트릭을 사용하여 Azure Digital Twins 서비스의 전반적인 상태와 연결된 리소스를 평가할 수 있습니다.

**경고** 는 메트릭 데이터에서 중요한 조건이 발견되면 사전에 알림을 발송합니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 경고에 대한 자세한 내용은 [*Microsoft Azure의 경고 개요*](../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

## <a name="turn-on-alerts"></a>경고 설정

Azure Digital Twins 인스턴스에 대해 경고를 사용하도록 설정하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Digital Twins 인스턴스로 이동합니다. 포털 검색 창에 이름을 입력하여 찾을 수 있습니다. 

2. 메뉴에서 **경고** 를 선택하고 **+ 새 경고 규칙** 을 선택합니다.

3. 다음에 나오는 *경고 규칙 만들기* 페이지에서 프롬프트에 따라 조건, 트리거할 작업 및 경고 세부 정보를 정의할 수 있습니다.     
    * **범위** 세부 정보는 인스턴스에 대한 세부 정보로 자동으로 채워집니다.
    * 경고 트리거 및 응답을 사용자 지정하도록 **조건** 및 **작업 그룹** 세부 정보를 정의합니다.
    * **경고 규칙 세부 정보** 섹션에서 규칙의 이름과 설명(선택 사항)을 입력합니다. 경고가 생성되는 즉시 활성 상태가 되도록 하려면 _경고 규칙을 만들면 바로 사용_ 확인란을 선택하면 됩니다.
        - 여기에서 _리소스 그룹_ 및 _심각도_ 수준을 선택할 수도 있습니다.

4. _경고 규칙 만들기_ 단추를 선택하여 경고 규칙을 만듭니다.

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="범위, 조건, 작업 그룹 및 경고 규칙 세부 정보에 대한 섹션이 있는 경고 규칙 만들기 페이지를 보여주는 스크린샷" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

이러한 필드를 작성하는 방법에 대한 단계별 연습은 [*Microsoft Azure의 경고 개요*](../azure-monitor/alerts/alerts-overview.md)를 참조하세요. 다음은 Azure Digital Twins의 단계에 대한 몇 가지 예입니다.

### <a name="select-conditions"></a>조건 선택

다음은 Azure Digital Twins에 사용할 수 있는 경고 신호의 유형을 보여주는 *조건 선택* 프로세스에서 발췌한 것입니다. 이 페이지에서 신호의 유형을 필터링하고 목록에서 원하는 신호를 선택할 수 있습니다.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="메트릭 또는 활동 로그를 선택하는 신호 유형 상자와 아래에 선택할 수 있는 메트릭 목록이 표시된 첫 번째 신호 논리 구성 페이지를 보여주는 스크린샷":::

신호를 선택한 후에는 경고의 논리를 구성하라는 메시지가 표시됩니다. 차원을 필터링하고, 경고에 대한 임계값을 설정하고, 조건에 대한 확인 빈도를 설정할 수 있습니다. 다음은 평균 라우팅 실패율 메트릭이 5%를 초과하는 경우에 대한 경고를 설정하는 예입니다.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="메트릭 기록이 표시되고 Event Grid 작업과 같이 차원에서 필터링할 영역이 있으며 '평균이 5보다 큼'과 같은 경고 논리를 정의하는 섹션이 있는 두 번째 신호 논리 구성 페이지를 보여주는 스크린샷":::

### <a name="verify-success"></a>성공 확인

경고를 설정하면 인스턴스에 대한 *경고* 페이지에 다시 표시됩니다.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="하나의 경고가 구성된 경고 페이지와 추가할 단추를 보여주는 스크린샷" lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>다음 단계

* Azure Monitor 경고에 대한 자세한 내용은 [*Microsoft Azure의 경고 개요*](../azure-monitor/alerts/alerts-overview.md)를 참조하세요.
* Azure Digital Twins 메트릭에 대한 정보는 [*문제 해결: Azure Monitor로 메트릭 보기*](troubleshoot-metrics.md)를 참조하세요.
* 메트릭에 대해 진단 로깅을 사용하도록 설정하는 방법은 [*문제 해결: 진단 설정*](troubleshoot-diagnostics.md)을 참조하세요.
