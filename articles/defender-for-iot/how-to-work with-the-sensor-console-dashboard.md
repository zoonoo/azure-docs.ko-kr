---
title: 센서 콘솔 대시보드 작업
description: 대시보드를 사용하여 네트워크의 보안 상태를 신속하게 볼 수 있습니다. 관련 디바이스에 대한 정보와 함께 타임라인의 전체 시스템에 대한 위협의 개략적인 수준의 개요를 제공합니다.
ms.date: 11/03/2020
ms.topic: article
ms.openlocfilehash: e9bc650e9c306d12ef63994852816c96e1bdf40a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781537"
---
# <a name="the-dashboard"></a>대시보드

대시보드를 사용하여 네트워크의 보안 상태를 신속하게 볼 수 있습니다. 대시보드는 타임라인에 전체 시스템에 대한 위협의 개략적인 수준의 개요를 다음과 같은 관련 디바이스에 대한 정보와 함께 제공합니다.

- 서로 다른 심각도 수준의 경고:

- 위험

- 주요함

- 부

- 경고

- 페이지의 가운데에 있는 두 개의 표시기는 PPS(초당 패킷 수) 및 UA(승인되지 않은 경고)를 표시합니다. **PPS** 는 초당 시스템에서 승인하는 패킷 수입니다. **UA** 는 아직 승인되지 않은 경고의 수입니다.

- 설명이 포함된 승인되지 않은 경고 목록입니다.

- 경고 설명이 있는 타임라인입니다.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="대시보드":::

## <a name="viewing-the-latest-alerts"></a>최신 경고 보기

페이지의 가운데에 있는 UA(승인되지 않은 경고) 계기는 이러한 경고의 수를 나타냅니다. 경고 목록을 보려면 대시보드 페이지의 아래쪽에서 **추가 경고** 를 선택하거나 측면 메뉴에서 **경고** 를 선택합니다.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="승인되지 않은 경고":::

## <a name="status-boxes"></a>상태 상자

각 상태 상자는 이 섹션에 설명되어 있습니다.

| 상태 상자 및 계기 | Description |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="중요한 알림"::: | **중요한 알림** - 페이지의 위쪽 가운데에 있는 상자는 중요한 알림의 수를 나타냅니다. 이 상자를 선택하면 타임라인 및 계기 아래의 목록(있는 경우)에 대한 경고 설명이 표시됩니다.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="주요 경고"::: | **주요 경고** -페이지의 오른쪽 위에 있는 상자에는 주요 경고 수가 표시됩니다. 이 상자를 선택하면 타임라인 및 계기 아래의 목록(있는 경우)에 대한 경고 설명이 표시됩니다.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="사소한 경고"::: | **사소한 경고** - 페이지 왼쪽 아래에 있는 상자에는 사소한 경고 수가 표시 됩니다. 이 상자를 선택하면 타임라인 및 계기 아래의 목록(있는 경우)에 대한 경고 설명이 표시됩니다.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="경고 알림"::: | **경고 알림** - 페이지의 아래쪽 가운데에 있는 상자는 경고 알림 수를 나타냅니다. 이 상자를 선택하면 타임라인 및 계기 아래의 목록(있는 경우)에 대한 경고 설명이 표시됩니다.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="All Alerts"::: | **모든 경고** - 페이지의 오른쪽 아래에 있는 상자에는 중요한 경고, 주요 경고, 사소한 경고 및 경고 알림의 수 합계가 표시됩니다. 이 상자를 선택하면 타임라인 및 계기 아래의 목록(있는 경우)에 대한 경고 설명이 표시됩니다. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="초당 패킷"::: | **PPS(초당 패킷)** - PPS 메트릭은 네트워크 성능에 대한 지표입니다. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="UA(승인되지 않은 이벤트)"::: | **승인되지 않은 이벤트** - 이 메트릭은 승인되지 않은 이벤트의 수를 나타냅니다.

## <a name="using-the-timeline"></a>타임라인 사용

경고는 날짜 및 시간 정보를 포함하는 세로 타임라인을 따라 표시됩니다.

그래픽으로 표시되는 타임라인:

- 중요한 알림

- 주요 경고

- 사소한 경고

- 경고 알림

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="타임라인 그래프":::

## <a name="viewing-alerts"></a>경고 보기

경고 상자 아래쪽에 있는 아래쪽 화살표 **V** 를 선택하여 경고 항목 및 디바이스 정보를 표시합니다.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="경고 항목 및 디바이스 정보":::

- 디바이스를 선택하여 실제 모드 맵을 표시합니다. 대상 디바이스가 강조됩니다.

- 경고 상자에서 아무 곳이나 클릭하여 경고에 대한 추가 세부 정보를 표시합니다. 팝업은 아래와 유사하게 표시됩니다

- :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel":::을 선택하여 경고에 대한 CSV 파일을 내보냅니다.

- 관리자 및 보안 분석가만 해당 - :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="모두 승인":::을 선택하여 관련된 모든 경고를 **승인** 합니다.

- :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF":::를 선택해 경고 보고서를 PDF 파일로 다운로드합니다.

- :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="고정":::을 선택하여 경고를 고정하거나 고정 해제합니다. 고정을 선택하면 경고가 **고정된 경고** 창에 추가되며 창은 **경고** 화면 위에 표시됩니다.

- 네트워크 프로토콜 분석이 있는 관련 PCAP 파일을 다운로드하여 경고를 조사하려면 :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="다운로드":::를 선택합니다.

- :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="클라우드":::를 선택하면 경고 관련 패킷만 포함된 필터링된 PCAP 파일을 다운로드하여 출력 파일 크기를 줄이고 보다 집중적으로 분석할 수 있습니다. [Wireshark](https://www.wireshark.org/)를 사용하여 볼 수 있습니다.

- :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="탐색":::을 선택하여 요청된 경고가 발생한 때의 이벤트 타임라인으로 이동합니다. 이렇게 하면 특정 경고를 발생시킬 수 있는 다른 이벤트를 평가할 수 있습니다.

- 관리자 및 보안 분석가만 해당 - 경고의 상태를 승인되지 않음에서 승인됨으로 변경합니다. 검색된 작업을 승인하는 방법 알아보기를 선택합니다.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="권한 없는 인터넷 연결 탐지됨":::

## <a name="see-also"></a>참고 항목

[센서의 경고 사용](how-to-work-with-alerts-on-your-sensor.md)
