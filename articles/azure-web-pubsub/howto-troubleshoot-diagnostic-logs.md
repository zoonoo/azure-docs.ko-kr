---
title: Azure Web PubSub 서비스 진단 로그를 사용하여 문제를 해결하는 방법
description: 진단 로그를 가져오고 문제를 해결하는 방법 알아보기
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/22/2021
ms.openlocfilehash: 58017425eda0461a648b81472933aaeb911b24f1
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166993"
---
# <a name="how-to-troubleshoot-with-diagnostic-logs"></a>진단 로그를 사용하여 문제를 해결하는 방법

이 방법 가이드에서는 진단 로그를 가져오는 옵션과 이를 사용하여 문제를 해결하는 방법을 보여 줍니다.

## <a name="whats-the-diagnostic-logs"></a>진단 로그란?

진단 로그는 Azure Web PubSub 서비스 인스턴스에 대한 더 풍부한 연결 및 메시징 정보 보기를 제공합니다. 문제 식별, 연결 추적, 메시지 추적 및 분석에 사용할 수 있습니다.

로그에는 연결 로그와 메시징 로그라는 두 가지 유형이 있습니다.

### <a name="connectivity-logs"></a>연결 로그

연결 로그는 Azure Web PubSub 허브 연결에 대한 자세한 정보를 제공합니다. 예를 들면 기본 정보(사용자 ID, 연결 ID 등)와 이벤트 정보(연결, 연결 끊기 및 중단 이벤트 등)가 있습니다. 연결 로그가 연결 관련 문제를 해결하는 데 도움이 되는 이유입니다. 

### <a name="messaging-logs"></a>메시징 로그

메시징 로그는 Azure Web PubSub 서비스를 통해 수신 및 전송되는 Azure Web PubSub 허브 메시지에 대한 추적 정보를 제공합니다. 예를 들어 메시지의 추적 ID와 메시지 유형이 있습니다. 일반적으로 메시지는 서비스에 도착하거나 서비스에서 나갈 때 기록됩니다. 따라서 메시징 로그는 메시지 관련 문제를 해결하는 데 유용합니다. 

## <a name="capture-diagnostic-logs-with-azure-web-pubsub-service-live-trace-tool"></a>Azure Web PubSub 서비스 라이브 추적 도구를 사용하여 진단 로그 캡처 

Azure Web PubSub 서비스 라이브 추적 도구는 실시간으로 진단 로그를 수집하는 기능을 제공하며 고객의 개발 환경을 추적하는 데 도움이 됩니다. 라이브 추적 도구는 연결 로그와 메시징 로그를 모두 캡처할 수 있습니다.

> [!NOTE]
> 라이브 추적 도구에서 캡처한 실시간 진단 로그는 메시지(아웃바운드 트래픽)로 간주됩니다.

> [!NOTE]
> 무료 계층으로 생성된 Azure Web PubSub 서비스 인스턴스에는 일일 메시지(아웃바운드 트래픽) 제한이 있습니다.

### <a name="launch-the-live-trace-tool"></a>라이브 추적 도구 시작

1. Azure Portal로 이동합니다. 
1. Azure Web PubSub 서비스 인스턴스의 **진단 설정** 페이지에서 **라이브 추적 도구 열기** 를 선택합니다. 

    :::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-logs-with-live-trace-tool.png" alt-text="라이브 추적 도구를 시작합니다.":::

### <a name="capture-the-diagnostic-logs"></a>진단 로그 캡처

라이브 추적 도구는 문제 해결을 위해 진단 로그를 캡처하는 데 도움이 되는 몇 가지 기본 기능을 제공합니다.

* **캡처**: 라이브 추적 도구를 사용하여 Azure Web PubSub 인스턴스에서 실시간 진단 로그 캡처를 시작합니다.
* **지우기**: 캡처된 실시간 진단 로그를 지웁니다.
* **로그 필터**: 라이브 추적 도구를 사용하면 캡처된 실시간 진단 로그를 하나의 특정 키워드로 필터링할 수 있습니다. 일반적인 구분 기호(예: 공백, 쉼표, 세미콜론 등)는 키 단어의 일부로 처리됩니다. 
* **상태**: 상태는 라이브 추적 도구가 특정 인스턴스와 연결되어 있는지 또는 연결이 끊겼는지를 보여 줍니다.

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/live-trace-tool-capture.png" alt-text="라이브 추적 도구를 사용하여 진단 로그를 캡처합니다.":::

라이브 추적 도구에서 캡처한 실시간 진단 로그에는 문제 해결을 위한 자세한 정보가 포함되어 있습니다. 

| 이름 | Description |
| ------------ |  ------------------------ | 
| Time | 로그 이벤트 시간 |
| 로그 수준 | 로그 이벤트 수준(추적/디버그/정보/경고/오류) |
| 이벤트 이름 | 이벤트의 작업 이름 |
| 메시지 | 로그 이벤트의 세부 메시지 |
| 예외 | Azure Web PubSub 서비스의 런타임 예외 |
| 허브 | 사용자 정의 허브 이름 |
| 연결 ID | 연결의 ID |
| 사용자 ID | 사용자의 ID |
| IP | 클라이언트의 IP 주소 | 

Azure Web PubSub 서비스가 일반 공급된 후로, 라이브 추적 도구는 로그를 특정 형식으로 내보낸 다음, 문제 해결을 위해 다른 사람과 공유할 수 있는 기능도 지원합니다. 

## <a name="capture-diagnostic-logs-with-azure-monitor"></a>Azure Monitor를 사용하여 진단 로그 캡처

[Azure Monitor](https://azure.microsoft.com/services/monitor/), [Azure Storage](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs#send-to-azure-storage) 및 [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs#send-to-log-analytics-workspace)와 통합하여 진단 로그를 캡처하는 기능은 공개 미리 보기에서 지원되지 않습니다.

## <a name="troubleshoot-with-the-diagnostic-logs"></a>진단 로그 문제 해결

예기치 않게 연결이 늘어나거나 줄어드는 상황이 발생할 경우 진단 로그를 활용하여 문제를 해결할 수 있습니다. 일반적인 문제는 연결의 예기치 않은 수량 변경, 연결의 연결 제한 도달 및 권한 부여 오류에 대한 경우가 많습니다.

### <a name="unexpected-connection-number-changes"></a>예기치 않은 연결 수 변경

#### <a name="unexpected-connection-dropping"></a>예기치 않은 연결 끊김

연결이 끊어지면 진단 로그는 `operationName`에 `ConnectionAborted` 또는 `ConnectionEnded`를 사용하여 이 연결 끊김 이벤트를 기록합니다.

`ConnectionAborted`와 `ConnectionEnded`의 차이점은 `ConnectionEnded`가 클라이언트 또는 서버 쪽에서 트리거되는 예상한 연결 끊김이라는 것입니다. `ConnectionAborted`는 일반적으로 예기치 않은 연결 끊김 이벤트이며, `message`에 중단 이유가 제공됩니다.

중단 이유는 다음 표에 나열되어 있습니다.

| 이유 | Description |
| ------- | ------- |
| 연결 수가 제한에 도달 | 연결 수가 현재 가격 계층의 제한에 도달했습니다. 서비스 단위를 스케일 업하는 것이 좋습니다.
| 서비스 다시 로드, 다시 연결 | Azure Web PubSub 서비스를 다시 로드하는 중입니다. 고유한 다시 연결 메커니즘을 구현하거나 Azure Web PubSub 서비스에 수동으로 다시 연결해야 합니다. |
| 내부 서버의 일시적인 오류 | Azure Web PubSub 서비스에서 일시적인 오류가 발생하며, 자동으로 복구되어야 함

#### <a name="unexpected-connection-growing"></a>예기치 않은 연결 증가

예기치 않은 연결 증가 문제를 해결하려면 먼저 추가 연결을 필터링해야 합니다. 테스트 클라이언트 연결에 고유한 테스트 사용자 ID를 추가할 수 있습니다. 그런 다음, 진단 로그를 사용하여 이를 확인합니다. 두 개 이상의 클라이언트 연결이 동일한 테스트 사용자 ID 또는 IP를 사용하는 경우, 클라이언트 쪽에서 예상보다 많은 연결을 만들고 설정할 가능성이 높습니다. 클라이언트 쪽을 확인합니다.

### <a name="authorization-failure"></a>권한 부여 실패

클라이언트 요청에 대해 인증되지 않은 401이 반환되는 경우 진단 로그를 확인합니다. `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`가 발견될 경우, 액세스 토큰의 모든 대상 그룹이 잘못된 것입니다. 로그에 제안된 유효한 대상 그룹을 사용하세요.

### <a name="throttling"></a>제한

Azure Web PubSub 서비스에 대한 클라이언트 연결을 설정할 수 없는 경우 진단 로그를 확인합니다. 진단 로그에서 `Connection count reaches limit`가 발견될 경우 Azure Web PubSub 서비스에 너무 많은 연결을 설정해서 연결 수 제한에 도달한 것입니다. Azure Web PubSub 서비스 인스턴스를 스케일 업하는 것이 좋습니다. 진단 로그에서 `Message count reaches limit`가 발견될 경우 현재 무료 계층을 사용하고 있으며 메시지 할당량을 다 사용한 것입니다. 더 많은 메시지를 보내려면 Azure Web PubSub 서비스 인스턴스를 표준 계층으로 변경하여 추가 메시지를 전송하는 것이 좋습니다. 자세한 내용은 [Azure Web PubSub 서비스 가격 책정](https://azure.microsoft.com/pricing/details/web-pubsub/)을 참조하세요.

