---
title: 온-프레미스 관리 콘솔의 경고 작업
description: 온-프레미스 관리 콘솔을 사용 하 여 네트워크에서 최근에 발생 한 위협에 대 한 엔터프라이즈 보기를 가져오고, 센서 사용자가이를 처리 하는 방법을 더 잘 이해할 수 있습니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 878b1b2d5ba13b68b5122e4b9cffc3c408e211e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100526326"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>온-프레미스 관리 콘솔의 경고 작업 

관리 콘솔의 **경고** 창에서 다음 작업을 수행할 수 있습니다.

- 경고 필터 작업

- 경고 카운터 작업

- 경고 정보 보기

- 경고 이벤트 관리

- 경고 제외 규칙 만들기

- 외부 시스템에서 경고 제외 규칙 트리거

- 경고 그룹을 사용 하 여 인시던트 워크플로 가속화

## <a name="view-alerts-in-the-on-premises-management-console"></a>온-프레미스 관리 콘솔에서 경고 보기

온-프레미스 관리 콘솔은 연결 된 모든 센서의 경고를 집계 합니다. 이를 통해 네트워크에서 최근에 발생 한 위협에 대 한 엔터프라이즈 보기를 제공 하 고 사용자가 센서를 처리 하는 방법을 보다 잘 이해할 수 있습니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="경고 창의 스크린샷":::

### <a name="work-with-alert-filters"></a>경고 필터 작업

**경고** 창에는 온-프레미스 관리 콘솔에 연결 된 센서에 의해 생성 된 경고가 표시 됩니다. 연결 된 센서에 대 한 모든 경고를 보거나 특정에서 전송 된 경고를 표시할 수 있습니다.

- 사이트

- 영역

- 디바이스

- 센서

모든 경고를 보려면 **필터 지우기** 를 선택 합니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="필터 지우기 단추를 선택 하 여 필터를 지웁니다.":::

### <a name="work-with-alert-counters"></a>경고 카운터 작업

경고 카운터는 심각도 및 승인 됨 상태별로 경고에 대 한 분석을 제공 합니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="경고 카운터는 보유 한 경고 수를 표시 합니다.":::

경고 카운터에 표시 되는 심각도 수준은 다음과 같습니다.

- **위험**: 즉시 처리 해야 하는 악의적인 공격을 나타냅니다.

- **Major**: 주소를 지정 하는 데 중요 한 보안 위협을 나타냅니다.

- **Minor**: 보안 위협을 포함할 수 있는 기본 동작과의 일부 편차를 나타냅니다.

- **경고**: 기본 동작과 보안 위협 없이 일부 편차를 나타냅니다.

심각도 수준은 미리 정의 되어 있습니다.

카운터를 조정 하 여 승인 되거나 승인 되지 않은 경고를 기반으로 숫자를 제공할 수 있습니다. 승인 되지 않은 경고가 IoT 센서에 대해 Defender에서 트리거 되었지만 센서의 운영자가 아직 검토 하지 않았습니다.

승인 된 **경고 표시** 옵션을 선택 하면 모든 승인 된 경고가 **경고** 창에 표시 됩니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="승인 됨 경고를 확인 합니다.":::

### <a name="view-alert-information"></a>경고 정보 보기

경고는 다음 정보를 제공 합니다.

- 경고 이벤트의 요약입니다.

- 경고 심각도입니다.

- 검색 한 센서의 경고에 대 한 링크입니다.

- 경고 UUID입니다. UUID는 센서에서 검색 된 경고 이벤트와 연결 된 경고 ID로 구성 되며, 하이픈으로 구분 된 다음 고유한 시스템 ID 번호로 구성 됩니다.

**온-프레미스 관리 콘솔 경고 UUID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="장치가 연결 되어 있지만 권한이 부여 되지 않았습니다.":::

**센서 경고 ID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="센서 경고 ID입니다.":::

Uuid를 사용 하면 온-프레미스 관리 콘솔에 표시 되는 각 경고가 검색 가능 하 고 고유한 숫자로 식별할 수 있습니다. 이는 여러 센서에서 생성 된 경고가 동일한 경고 ID를 생성할 수 있기 때문에 필요 합니다.

> [!NOTE]
> 기본적으로 전달 규칙이 정의 된 경우 (ArcSight, syslog 서버, QRadar, 센티널 및 NetWitness 모니터 서버)에는 Uuid가 다음과 같은 파트너 시스템에 표시 됩니다. 설정이 필요 하지 않습니다.

경고 정보를 보려면:

- 경고 목록에서 경고를 선택 합니다.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="경고 정보의 스크린샷":::

센서에서 경고를 보려면 다음을 수행 합니다.

- 경고에서 **센서 열기** 를 선택 합니다.

영역 맵에서 장치를 보려면 다음을 수행 합니다.

- 경고 장치 및 연결 된 모든 장치에 초점을 맞춘 장치 맵을 보려면 **장치 표시** 를 선택 합니다.

## <a name="manage-alert-events"></a>경고 이벤트 관리

온-프레미스 관리 콘솔에서 경고 이벤트를 관리 하는 데 사용할 수 있는 몇 가지 옵션이 있습니다.

- 경고 이벤트를 알아보거나 승인 합니다. 승인 **&** 승인을 선택 하 고 현재 승인 되지 않은 모든 경고 이벤트를 승인할 수 있습니다.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="자세히 알아보려면 & 승인을 선택 합니다.":::

- 경고 이벤트를 음소거 및 음소거 해제 합니다.

경고 이벤트를 학습, 승인 및 음소거 하는 방법에 대 한 자세한 내용은 센서 [관리 경고 이벤트](how-to-manage-the-alert-event.md) 문서를 참조 하세요.

## <a name="export-alert-information"></a>경고 정보 내보내기

경고 정보를 .csv 파일로 내보냅니다. 필터링 된 보기를 기반으로 검색 된 모든 경고 또는 내보내기 정보에 대 한 정보를 내보낼 수 있습니다. 다음 정보를 내보냅니다.

- 원본 주소
- 대상 주소
- 경고 제목
- 경고 심각도
- 경고 메시지
- 추가 정보
- 승인 상태
- PCAP 가용성

내보내기:

1. 측면 메뉴에서 경고를 선택 합니다.
1. 내보내기를 선택합니다.
1. 여러 장치를 포함 하는 각 경고에 대해 별도의 행으로 경고 정보를 내보내려면 확장 된 경고 내보내기를 선택 합니다. 확장 된 경고 내보내기를 선택 하는 경우 .csv 파일은 각 행에 고유한 항목으로 경고의 중복 행을 만듭니다. 이 옵션을 사용 하면 내보낸 경고 이벤트를 보다 쉽게 조사할 수 있습니다.  

## <a name="create-alert-exclusion-rules"></a>경고 제외 규칙 만들기

다음을 기준으로 경고 트리거를 무시 하도록 IoT에 지시 합니다.

- 표준 시간대 및 기간

- 장치 주소 (IP, MAC, 서브넷)

- 경고 이름

- 특정 센서

IoT for IoT에서 경고를 트리거하는 활동을 무시 하도록 하려는 경우 경고 제외 규칙을 만듭니다.

예를 들어 특정 센서에 의해 모니터링 되는 모든 장치에서 2 일 동안 유지 관리 절차를 수행 하는 경우 미리 정의 된 기간 동안이 센서가 검색 한 경고를 표시 하지 않도록 IoT에 대해 Defender에 지시 하는 제외 규칙을 정의할 수 있습니다.

### <a name="alert-exclusion-logic"></a>경고 제외 논리

경고 규칙 논리는 `AND` 기반입니다. 즉, 모든 규칙 조건이 충족 될 때만 경고가 트리거됩니다.

규칙 조건이 정의 되지 않은 경우 조건에 모든 옵션이 포함 됩니다. 예를 들어 규칙에 센서의 이름을 포함 하지 않으면 모든 센서에 적용 됩니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="제외 규칙 만들기 보기의 스크린샷":::

규칙 요약은 **제외 규칙** 창에 표시 됩니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="제외 규칙 요약 보기의 스크린샷":::

제외 규칙을 사용 하는 것 외에도 경고를 표시 하지 않고 경고를 표시 하지 않을 수 있습니다.

### <a name="create-exclusion-rules"></a>제외 규칙 만들기

제외 규칙을 만들려면:

1. 온-프레미스 관리 콘솔의 왼쪽 창에서 **경고 제외** 를 선택 합니다.  :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: 열리는 창의 오른쪽 위 모퉁이에 있는 추가 아이콘을 선택 하 여 새 제외 규칙을 정의 합니다. **제외 규칙 만들기** 대화 상자가 열립니다.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="여기에 정보를 입력 하 여 경고 제외를 만듭니다.":::

2. **이름** 필드에 규칙 이름을 입력 합니다. 이름에는 따옴표 ()를 사용할 수 없습니다 `"` .

3. 표준 시간대 **/기간** 섹션에서 특정 표준 시간대의 기간을 입력 합니다. 한 표준 시간대에서 특정 기간에 대 한 제외 규칙이 생성 될 때이 기능을 사용 하지만 다른 표준 시간대에서 동시에 구현 해야 합니다. 예를 들어 세 가지 표준 시간대에서 오전 8:00 시와 10:00 오전 사이에 제외 규칙을 적용 해야 할 수 있습니다. 이 경우에는 동일한 기간 및 관련 표준 시간대를 사용 하는 세 가지 별도의 제외 규칙을 만듭니다.

4. **ADD** 를 선택합니다. 제외 기간 동안 연결 된 센서에는 경고가 생성 되지 않습니다.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="기간별로 보기의 스크린샷":::

5. **장치 주소** 섹션에서 다음을 정의 합니다.

  - 제외 하려는 장치 IP 주소, MAC 주소 또는 서브넷 주소입니다.

  - 제외 된 장치, 원본 및 대상에 대 한 트래픽 방향입니다.

6. **ADD** 를 선택합니다.

7. **경고** 제목 섹션에서 경고 제목 입력을 시작 합니다. 드롭다운 목록에서 제외할 경고 제목 또는 제목을 선택 합니다.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="경고 제목 보기의 스크린샷":::

8. **ADD** 를 선택합니다.

9. 센서 **이름** 섹션에서 센서 이름 입력을 시작 합니다. 드롭다운 목록에서 제외 하려는 센서 또는 센서를 선택 합니다.

10. **ADD** 를 선택합니다.

11. **SAVE**(저장)를 선택합니다. 규칙 목록에 새 규칙이 표시 됩니다.

경고를 음소거 하거나 경고 제외 규칙을 만들어 경고를 표시 하지 않을 수 있습니다. 이 섹션에서는 두 기능에 대 한 잠재적 사용 사례에 대해 설명 합니다.

- **제외 규칙**. 다음 경우에 제외 규칙을 작성 합니다.

  - 데이터베이스에서 이벤트를 제외할 시간을 미리 알고 있습니다. 예를 들어 특정 센서에서 검색 된 시나리오는 관련이 없는 경고를 트리거합니다. 예를 들어 특정 사이트의 조직 PLCs에 대 한 유지 관리 작업을 수행 하 고이 사이트의 PLCs와 관련 된 경고를 표시 하지 않을 것입니다.

  - IoT 용 Defender에서 특정 시간 범위 (시스템 유지 관리 작업의 경우)에 대 한 이벤트를 무시 하도록 하려고 합니다.

  - 특정 서브넷의 이벤트를 무시 하려고 합니다.

  - 하나의 규칙을 사용 하 여 여러 센서에서 생성 된 경고 이벤트를 제어 하려고 합니다.

  - 경고 제외를 이벤트 로그의 이벤트로 추적 하지 않으려고 합니다.

- **음소거**. 경고 음소거:

  - 음소거 해야 하는 항목은 계획 되지 않습니다. 이벤트의 상관 관계를 미리 알 수 없습니다.

  - **경고 창에서** 경고를 표시 하지 않으려는 경우에도 이벤트 로그에서 경고를 추적 하려고 합니다.

  - 특정 채널에서 이벤트를 무시 하려고 합니다.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>외부 시스템에서 경고 제외 규칙 트리거

외부 시스템에서 경고 제외 규칙을 트리거합니다. 예를 들어, 엔터프라이즈 티켓 시스템 또는 네트워크 유지 관리 프로세스를 관리 하는 시스템에서 제외 규칙을 관리 합니다.

규칙을 적용할 센서, 엔진, 시작 시간 및 종료 시간을 정의 합니다. 자세한 내용은 [IoT 용 DEFENDER api 센서 및 관리 콘솔 api](references-work-with-defender-for-iot-apis.md)를 참조 하세요.

API를 사용 하 여 만든 규칙은 **제외 규칙** 창에 RO로 표시 됩니다.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="제외 규칙 편집 보기의 스크린샷":::

## <a name="see-also"></a>참고 항목

[센서에 대 한 경고 사용](how-to-work-with-alerts-on-your-sensor.md)
