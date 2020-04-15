---
title: 이벤트 집계
description: IoT 이벤트 집계를 위한 Azure 보안 센터에 대해 자세히 알아보세요.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: f72ef8cc5161bd6f885249e7d39344a57fa2368e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311409"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>IoT 이벤트 집계를 위한 Azure 보안 센터

IoT 보안 에이전트용 Azure 보안 센터는 로컬 장치에서 데이터 및 시스템 이벤트를 수집하고 처리 및 분석을 위해 이 데이터를 Azure 클라우드로 보냅니다. 보안 에이전트는 새 프로세스 및 새 연결 이벤트를 비롯한 다양한 유형의 장치 이벤트를 수집합니다. 새 프로세스와 새 연결 이벤트는 1초 내에 장치에서 합법적으로 자주 발생할 수 있으며 강력하고 포괄적인 보안을 위해 중요하지만 보안 에이전트가 보내야 하는 메시지 수는 IoT Hub 할당량 및 비용 제한을 빠르게 도달하거나 초과할 수 있습니다. 그러나 이러한 이벤트에는 장치를 보호하는 데 중요한 매우 중요한 보안 정보가 포함되어 있습니다.

장치를 보호된 상태로 유지하면서 추가 할당량 및 비용을 줄이기 위해 IoT 에이전트용 Azure 보안 센터는 이러한 유형의 이벤트를 집계합니다.

이벤트 집계는 기본적으로 **켜져** 있으며 권장되지는 않지만 언제든지 수동으로 **해제할** 수 있습니다.

집계는 현재 다음과 같은 유형의 이벤트에 사용할 수 있습니다.

* 프로세스 만들기
* 연결 만들기
* 프로세스 종료(Windows만)

## <a name="how-does-event-aggregation-work"></a>이벤트 집계는 어떻게 작동합니까?

이벤트 **집계가 켜지면**IoT 에이전트용 Azure 보안 센터에서 간격 기간 또는 시간 기간에 대한 이벤트를 집계합니다.
간격 기간이 지나면 에이전트는 추가 분석을 위해 집계된 이벤트를 Azure 클라우드로 보냅니다.
집계된 이벤트는 Azure 클라우드로 전송될 때까지 메모리에 저장됩니다.

에이전트의 메모리 공간을 줄이기 위해 에이전트가 이미 메모리에 보관중인 이벤트와 동일한 이벤트를 수집할 때마다 에이전트는 이 특정 이벤트의 적중 횟수를 늘입니다. 집계 시간 창이 지나가면 에이전트는 발생한 각 특정 유형의 이벤트의 적중 횟수를 보냅니다. 이벤트 집계는 단순히 수집된 각 이벤트 유형의 적중 횟수의 집계입니다.

이벤트는 다음 조건이 충족되는 경우에만 동일한 것으로 간주됩니다.

* 프로세스 만들기 이벤트 - **명령줄,** **실행,** **사용자 이름**및 **userid가** 동일할 때
* 연결 이벤트 만들기 - **명령줄,** **userId**, **방향,** **로컬 주소,** **원격 주소**, **프로토콜 및 **대상 포트가** 동일할 때
* 프로세스 종료 이벤트 - **실행 및** **종료 상태가** 동일한 경우

### <a name="working-with-aggregated-events"></a>집계된 이벤트 작업

집계 하는 동안 집계 되지 않은 이벤트 속성은 삭제 되 고 0 값을 가진 로그 분석에 나타납니다.

* 프로세스 만들기 이벤트 - **processId**및 **parentProcessId가** 0으로 설정됩니다.
* 연결 만들기 이벤트 - **processId**및 **소스 포트가** 0으로 설정됨

## <a name="event-aggregation-based-alerts"></a>이벤트 집계 기반 경고

분석 후 IoT용 Azure 보안 센터는 의심스러운 집계 이벤트에 대한 보안 경고를 만듭니다. 집계된 이벤트에서 만든 경고는 집계된 각 이벤트에 대해 한 번만 표시됩니다.

각 이벤트에 대한 집계 시작 시간, 종료 시간 및 적중 횟수는 조사 중에 사용할 로그 애널리틱스 내의 이벤트 **세부정보** 필드에 기록됩니다.

집계된 각 이벤트는 수집된 경고의 24시간 기간을 나타냅니다. 각 이벤트의 왼쪽 상단에 있는 이벤트 옵션 메뉴를 사용하여 집계된 각 이벤트를 **해제할** 수 있습니다.

## <a name="event-aggregation-twin-configuration"></a>이벤트 집계 쌍 구성

**Azureiotsecurity** 모듈의 모듈 쌍 ID의 [에이전트 구성 개체](how-to-agent-configuration.md) 내에서 IoT 이벤트 집계에 대 한 Azure 보안 센터의 구성을 변경 합니다.

| 구성 이름 | 가능한 값 | 세부 정보 | 설명 |
|:-----------|:---------------|:--------|:--------|
| 집계EnabledProcessCreate | boolean | 프로세스 만들기 이벤트에 대한 이벤트 집계 사용/비활성화 |
| 집계간격프로세스 만들기 | ISO8601 타임스팬 스트링 | 프로세스 만들기 이벤트에 대한 집계 간격 |
| 집계활성화연결만들기 | boolean| 연결 만들기 이벤트에 대한 이벤트 집계 사용/비활성화 |
| 집계간격연결만들기 | ISO8601 타임스팬 스트링 | 연결을 위한 집계 간격으로 이벤트를 만듭니다. |
| 집계EnabledProcessTerminate | boolean | 프로세스 종료 이벤트에 대한 이벤트 집계 사용/비활성화 | Windows만 해당|
| 집계간격프로세스종료 | ISO8601 타임스팬 스트링 | 프로세스 종료 이벤트에 대한 집계 간격 | Windows만 해당|
|

## <a name="default-configurations-settings"></a>기본 구성 설정

| 구성 이름 | 기본값 |
|:-----------|:---------------|
| 집계EnabledProcessCreate | true |
| 집계간격프로세스 만들기 | "PT1H"|
| 집계활성화연결만들기 | true |
| 집계간격연결만들기 | "PT1H"|
| 집계EnabledProcessTerminate | true |
| 집계간격프로세스종료 | "PT1H"|
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 보안 에이전트 집계용 Azure 보안 센터 및 사용 가능한 이벤트 구성 옵션에 대해 배웠습니다.

IoT 배포를 위한 Azure 보안 센터를 계속 시작하려면 다음 문서를 사용하십시오.

- [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md) 이해
- [보안 에이전트](how-to-deploy-agent.md) 선택 및 배포
- IoT 서비스 전제 [조건에](service-prerequisites.md) 대한 Azure 보안 센터 검토
- [IoT 허브에서 IoT 서비스를 위한 Azure 보안 센터를 활성화하는](quickstart-onboard-iot-hub.md) 방법 알아보기
- [IoT FAQ용 Azure 보안 센터에서](resources-frequently-asked-questions.md) 서비스에 대해 자세히 알아보기
