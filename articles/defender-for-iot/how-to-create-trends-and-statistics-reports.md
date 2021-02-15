---
title: 추세 및 통계 보고서 생성
description: IoT 추세 및 통계 위젯에 대해 Defender를 사용 하 여 네트워크 활동, 통계 및 추세에 대 한 통찰력을 확보 하세요.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: c28e2f1c24d39ceb915be9f4f6f222d70de9ee73
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522226"
---
# <a name="sensor-trends-and-statistics-reports"></a>센서 추세 및 통계 보고서

## <a name="about-sensor-trends-and-statistics-reports"></a>센서 추세 및 통계 보고서 정보

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

## <a name="next-steps"></a>다음 단계

[위험 평가 보고](how-to-create-risk-assessment-reports.md) 
 [센서 데이터 마이닝 쿼리](how-to-create-data-mining-queries.md) 
 [공격 벡터 보고](how-to-create-attack-vector-reports.md)