---
title: 추세 및 통계 보고서 생성
description: IoT 추세 및 통계 위젯에 대해 Defender를 사용 하 여 네트워크 활동, 통계 및 추세에 대 한 통찰력을 확보 하세요.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 2/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 7cb3848f3711b6bf10c316ba4f5321286e2260fb
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "101706482"
---
# <a name="sensor-trends-and-statistics-reports"></a>센서 추세 및 통계 보고서

위젯 그래프와 원형 차트를 만들어 네트워크 추세와 통계에 대 한 통찰력을 얻을 수 있습니다. 위젯은 사용자 정의 대시보드 아래에서 그룹화 할 수 있습니다.

> [!NOTE]
> 관리자 및 보안 분석가는 추세와 통계 보고서를 만들 수 있습니다.

대시보드는 다음 유형의 정보를 그래픽으로 설명 하는 위젯로 구성 됩니다.

- 포트당 트래픽
- 포트 별 상위 트래픽
- 채널 대역폭
- 총 대역폭
- 활성 TCP 연결
- VLAN 별 상위 대역폭
- 디바이스
  - 새 디바이스
  - 사용 중인 장치
  - 공급 업체별 장치
  - OS 별 장치
  - VLAN 당 장치 수
  - 연결 되지 않은 장치
- 시간별 연결이 삭제 됩니다.
- 유형별 인시던트에 대 한 경고
- 데이터베이스 테이블 액세스
- 프로토콜 분석 위젯
- DELTAV
  - DeltaV roc 작업 배포
  - 이름으로 DeltaV roc 이벤트
  - 시간별 DeltaV 이벤트
- AMS
  - 서버 포트당 AMS 트래픽
  - 명령 별 AMS 트래픽
- 이더넷 및 IP 주소:
  - CIP 서비스의 이더넷 및 IP 주소 트래픽
  - CIP 클래스의 이더넷 및 IP 주소 트래픽
  - 명령에의 한 이더넷 및 IP 주소 트래픽
- OPC
  - OPC 상위 관리 작업
  - OPC 상위 i/o 작업
- Siemens S7:
  - Control 함수 별 S7 트래픽
  - Subfunction의 S7 트래픽
- VLAN
  - VLAN 당 장치 수
  - VLAN 별 상위 대역폭
- 60870-5-104
  - IEC-60870 트래픽 (ASDU 별)
- BACNET
  - BACnet 서비스
- DNP3
  - 함수 별 DNP3 트래픽
- SRTP:
  - 서비스 코드로 트래픽 SRTP
  - 일별 SRTP 오류
- SuiteLink:
  - SuiteLink top 쿼리 태그
  - SuiteLink numeric 태그 동작
- IEC-60870 트래픽 (ASDU 별)
- 함수 별 DNP3 트래픽
- 서비스별 MMS 트래픽
- 함수 별 modbus 트래픽
- OPC-서비스 별 UA 트래픽

> [!NOTE]
>  위젯의 시간은 센서 시간에 따라 설정 됩니다.

## <a name="create-reports"></a>보고서 만들기

대시보드 및 위젯을 보려면 다음을 수행 합니다.

측면 메뉴에서 **추세 & 통계** 를 선택 합니다.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="조사의 스크린샷입니다.":::

기본적으로 지난 7 일 동안 검색에 대 한 결과가 표시 됩니다. 필터 도구를 사용 하 여이 범위를 변경할 수 있습니다. 예를 들어, 무료 텍스트 검색입니다.

## <a name="create-a-dashboard"></a>대시보드 만들기

**대시보드** 드롭다운 메뉴를 선택 하 여 새 대시보드를 만듭니다. 대시보드에 많은 위젯을 만들고 추가할 수 있습니다.

다음 옵션을 사용 하 여 사용자 지정 대시보드를 만들 수 있습니다.

- 대시보드에 위젯 추가

- 대시보드에서 위젯 삭제

- 위젯의 필터 수정

- 위젯 크기 조정

- 위젯 위치 변경

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="위젯의 위치를 변경 합니다.":::

사용자 지정 대시보드를 만들려면 다음을 수행 합니다.

1. 왼쪽 패널에서 **추세 및 통계** 를 선택 합니다.

1. **대시보드 선택** 드롭다운 메뉴를 선택 하 고 **대시보드 만들기** 단추를 선택 합니다.

1. 새 대시보드에 대 한 의미 있는 이름을 입력 하 고 **만들기** 를 선택 합니다.

1. 페이지 왼쪽 위에 있는 **위젯 추가** 를 선택 합니다.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="위젯 저장소에서 위젯을 선택 합니다.":::

1. **보안** 및 **작업** 위젯을 창의 오른쪽 위에서 사용할 수 있습니다. 다양 한 범주 및 프로토콜에서 선택 합니다. 각 위젯에 축소 된 간략 한 설명이 표시 됩니다. 스크롤 막대를 사용 하 여 사용 가능한 위젯을 모두 표시 합니다.

1. **추가 하려면 클릭** 단추를 사용 하 여 위젯을 선택 합니다. 위젯은 대시보드에 즉시 표시 됩니다.

대시보드를 삭제 하려면:

1. 드롭다운 메뉴에서 대시보드 이름을 선택 합니다.

1. **삭제** 아이콘을 선택 하 고 **확인** 을 선택 합니다.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="대시보드를 삭제 하려면 삭제 아이콘을 선택 합니다.":::

대시보드 이름을 편집 하려면:

1. 드롭다운 메뉴에서 대시보드 이름을 선택 합니다.

1. **편집** 아이콘을 선택 합니다.
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="편집 아이콘을 선택 하 여 대시보드의 이름을 편집 합니다.":::

1. 대시보드의 새 이름을 입력 하 고 **저장** 을 선택 합니다.
 
기본 대시보드를 설정 하려면:

1. 드롭다운 메뉴에서 대시보드 이름을 선택 합니다.

1. **별표** 아이콘을 선택 하 여 기본 대시보드로 설정할 대시보드를 선택 합니다.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="별표 아이콘을 선택 하 여 기본 대시보드를 선택 합니다."::: 

위젯에 대 한 필터링 데이터를 수정 하려면:

1. **필터** 아이콘을 선택 합니다.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="필터 아이콘을 선택 하 여 위젯에 대 한 매개 변수를 설정 합니다.":::

1. 필수 매개 변수를 편집 합니다.

1. **확인** 을 선택합니다.

위젯을 삭제 하려면:

- 아이콘을 선택 :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: 합니다.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="위젯을 삭제 하려면 X를 선택 합니다.":::

## <a name="creating-widgets"></a>위젯 만들기 

위젯 저장소에서는 범주 및 프로토콜을 기준으로 위젯을 선택할 수 있습니다. 사용할 수 있는 **보안** 또는 **작업** 위젯을 선택 하 여 표시할 수 있습니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="위젯 저장소에서 위젯을 선택 합니다.":::

각 위젯에는 시스템 트래픽, 네트워크 통계, 프로토콜 통계, 장치 및 경고 정보에 대 한 특정 정보가 포함 되어 있습니다. 위젯에 대 한 데이터가 없으면 메시지가 표시 됩니다.

원형 차트에서 원형 차트의 섹션을 제거 하 여 나머지 조각의 상대적 중요도를 보다 명확 하 게 볼 수 있습니다. 화면 아래쪽의 범례에서 조각 이름을 선택 하 여이 작업을 수행 합니다.

다음 섹션에서는 몇 가지 위젯의 사용 사례에 대 한 예제를 제공 합니다.

### <a name="busy-devices-widget"></a>사용 중인 장치 위젯

이 위젯에는 가장 많은 장치 5 개가 나열 됩니다. **편집** 모드에서는 알려진 프로토콜을 기준으로 필터링 할 수 있습니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="사용 중인 장치 위젯의 뷰입니다.":::

### <a name="total-bandwidth-widget"></a>총 대역폭 위젯

이 위젯은 대역폭을 Mbps (초당 메가 비트) 단위로 추적 합니다. X 축에는 날짜가 표시 되 고 y 축에는 대역폭이 표시 됩니다. **편집** 모드에서는 클라이언트, 서버, 서버 포트 또는 서브넷에 따라 결과를 필터링 할 수 있습니다. 그래프 위에 커서를 놓으면 도구 설명이 나타납니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="총 대역폭 위젯의 뷰입니다.":::

### <a name="channels-bandwidth-widget"></a>채널 대역폭 위젯

이 위젯에는 상위 5 개 트래픽 채널이 표시 됩니다. 주소를 기준으로 필터링 하 고 표시 되는 결과의 수를 설정할 수 있습니다. 아래쪽 화살표를 선택 하 여 더 많은 채널을 표시 합니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="채널 대역폭 위젯의 뷰입니다.":::

### <a name="traffic-by-port-widget"></a>포트 위젯 별 트래픽

이 위젯은 각 포트가 다른 색으로 지정 된 원형 차트에 표시 되는 포트 별로 트래픽을 표시 합니다. 각 포트의 트래픽 양은 원형 차트의 해당 부분 크기에 비례 합니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="포트 위젯의 트래픽 뷰입니다.":::

### <a name="new-devices-widget"></a>새 장치 위젯

이 위젯에는 새 장치 가로 막대형 차트가 표시 됩니다 .이 차트는 특정 날짜에 검색 된 새 장치 수를 나타냅니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="새 장치 위젯의 뷰입니다.":::

### <a name="protocol-dissection-widgets"></a>프로토콜 분석 위젯

이 위젯에는 프로토콜별 트래픽, 함수 코드 및 서비스에 의해 표시 되는 것을 보여 주는 원형 차트가 표시 됩니다. 원형 차트 조각의 크기는 다른 조각에 상대적인 트래픽 양에 비례 합니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="프로토콜 분석 위젯의 뷰입니다.":::

### <a name="active-tcp-connections-widget"></a>활성 TCP 연결 위젯

이 위젯에는 시스템의 활성 TCP 연결 수를 보여 주는 차트가 표시 됩니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="활성 TCP 연결 위젯의 뷰입니다.":::

### <a name="incident-by-type-widget"></a>유형별 인시던트 위젯

이 위젯에는 유형별 인시던트 수를 표시 하는 원형 차트가 표시 됩니다. 미리 정의 된 기간 동안 각 엔진에 의해 생성 된 경고의 수입니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="유형 위젯의 인시던트 뷰입니다.":::

## <a name="devices-by-vendor-widget"></a>공급 업체 위젯의 장치

이 위젯에는 공급 업체별 장치 수를 보여 주는 원형 차트가 표시 됩니다. 특정 공급 업체에 대 한 장치 수는 다른 장치 공급 업체에 상대적인 디스크의 해당 장치 공급 업체 부분 크기에 비례 합니다.

## <a name="number-of-devices-per-vlan-widget"></a>VLAN 위젯 당 장치 수

이 위젯에는 VLAN 당 검색 된 장치 수를 보여 주는 원형 차트가 표시 됩니다. 원형 차트 조각의 크기는 다른 조각에 상대적인 검색 된 장치 수에 비례 합니다.

각 VLAN에는 수동으로 추가한 센서 또는 이름에 의해 할당 된 VLAN 태그가 표시 됩니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="장치 수 위젯의 뷰입니다.":::

### <a name="top-bandwidth-by-vlan-widget"></a>VLAN 위젯의 상위 대역폭

이 위젯은 VLAN의 대역폭 소비를 표시 합니다. 기본적으로 위젯에는 대역폭 사용량이 가장 높은 5 개의 Vlan이 표시 됩니다.

위젯에 표시 된 기간을 기준으로 데이터를 필터링 할 수 있습니다. 아래쪽 화살표를 선택 하 여 더 많은 결과를 표시 합니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="VLAN 위젯의 상위 대역폭 뷰입니다.":::

## <a name="system-report"></a>시스템 보고서

시스템 보고서를 다운로드 하려면: 

1. 측면 메뉴에서 **추세 & 통계** 를 선택 합니다.

1. 오른쪽 위 모서리에서 **시스템 보고서** 를 선택 합니다. 보고서가 자동으로 다운로드 됩니다.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="시스템 보고서 단추를 선택 하 여 시스템 보고서의 복사본을 다운로드 합니다.":::

시스템 보고서는 시스템의 모든 데이터를 포함 하는 PDF 파일입니다.

  - 디바이스

  - 경고

  - 네트워크 정책 정보

## <a name="devices-in-a-system-report"></a>시스템 보고서의 장치 

시스템 보고서에는 모든 장치 목록 및 해당 정보가 표시 됩니다. 예를 들어 형식, 이름 및 사용 되는 프로토콜입니다. 시스템 보고서에는 공급 업체 당 장치 목록도 표시 됩니다.

## <a name="alerts-in-system-report"></a>시스템 보고서의 경고 

시스템 보고서에는 날짜 및 심각도와 같은 정보가 포함 된 모든 경고의 목록이 표시 됩니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="시스템 보고서에 대 한 경고 보기입니다.":::

## <a name="network-information-in-system-report"></a>시스템 보고서의 네트워크 정보 

시스템 보고서에는 네트워크 기준에 대 한 세부 정보가 표시 됩니다. 예를 들어 DNP3 함수 코드와 연결당 포트를 엽니다.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="시스템 보고서의 DNP3 함수 뷰입니다.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="연결 당 열린 포트 보고서의 뷰입니다.":::

## <a name="see-also"></a>참조

[위험 평가 보고](how-to-create-risk-assessment-reports.md) 
 [센서 데이터 마이닝 쿼리](how-to-create-data-mining-queries.md) 
 [공격 벡터 보고](how-to-create-attack-vector-reports.md)
