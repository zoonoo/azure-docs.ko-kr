---
title: 경고 정보 보기
description: 경고 창에서 경고를 선택 하 여 세부 정보를 검토 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/03/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 5877db6b3bc7366f28e679882a2c784e6828b1c1
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523586"
---
# <a name="view-information-in-alerts"></a>경고 정보 보기

**경고 창에서** 경고를 선택 하 여 경고 세부 정보를 검토 합니다. 세부 정보에는 다음 정보가 포함 됩니다.

- 경고 메타 데이터

- 트래픽, 장치 및 이벤트에 대 한 정보

- 장치 맵의 연결 된 장치에 대 한 링크

- 보안 분석가 및 관리자가 정의한 설명

- 이벤트 조사에 대 한 권장 사항

## <a name="alert-metadata"></a>경고 메타 데이터

경고 세부 정보에는 다음과 같은 경고 메타 데이터가 포함 됩니다.

  - 경고 ID

  - 경고를 트리거한 정책 엔진

  - 경고가 트리거된 날짜 및 시간

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="권한 없는 인터넷 연결이 검색 되었습니다.":::

## <a name="information-about-devices-traffic-and-the-event"></a>장치, 트래픽 및 이벤트에 대 한 정보

경고 메시지는 다음에 대 한 정보를 제공 합니다.

  - 검색 된 장치입니다.

  - 장치 간에 검색 된 트래픽 (예: 프로토콜 및 함수 코드)입니다.

  - 이벤트의 의미에 대 한 정보입니다.

경고 이벤트를 관리 하는 방법을 결정할 때이 정보를 사용할 수 있습니다.

## <a name="links-to-connected-devices-in-the-device-map"></a>장치 맵의 연결 된 장치에 대 한 링크

검색 된 장치에 연결 된 장치에 대 한 자세한 내용을 보려면 경고에서 장치 이미지를 선택 하 고 맵에서 연결 된 장치를 볼 수 있습니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="RCP 작업이 실패 했습니다.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="장치 스크린샷.":::

지도는 선택한 장치와 해당 장치에 연결 된 기타 장치에 필터를 적용할 수 있습니다. 지도에는 경고에서 검색 된 장치에 대 한 **빠른 속성** 대화 상자도 표시 됩니다.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>보안 분석가 및 관리자가 정의한 설명 

경고에는 미리 정의 된 주석 목록이 포함 될 수 있습니다. 예를 들어 의견은 수행할 완화 작업 또는 이벤트에 대해 연락할 개인의 이름에 대 한 지침이 될 수 있습니다.

경고 이벤트를 관리할 때 이벤트 상태 또는 경고를 조사 하기 위해 수행한 단계를 가장 잘 반영 하는 설명 또는 주석을 선택할 수 있습니다.

선택한 설명은 경고 메시지에 저장 됩니다. 주석을 사용 하면 경고 이벤트를 조사 하는 동안 개인과 팀 간의 통신이 향상 됩니다. 따라서 주석은 인시던트 응답 시간을 단축할 수 있습니다.

관리자 또는 보안 분석가가 주석을 작성 합니다. 선택한 설명은 전달 규칙에 정의 된 파트너 시스템에 전달 되지 않습니다.

경고의 정보를 검토 한 후에는 다양 한 법정 단계를 수행 하 여 경고 이벤트 관리를 안내할 수 있습니다. 예를 들면 다음과 같습니다.

- 최근 장치 활동 분석 (데이터 마이닝 보고서). 

- 동시에 발생 한 다른 이벤트 (이벤트 시간대)를 분석 합니다. 

- PCAP 파일 (종합적인 이벤트 트래픽)을 분석 합니다.

## <a name="pcap-files"></a>PCAP 파일

경우에 따라 경고 메시지에서 PCAP 파일에 액세스할 수 있습니다. 이는 연결 된 네트워크 트래픽에 대 한 자세한 정보를 원하는 경우에 유용할 수 있습니다.

PCAP 파일을 다운로드 하려면 :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="다운로드 아이콘을 선택 합니다."::: **경고 정보** 대화 상자의 오른쪽 위에서

## <a name="recommendations-for-investigating-an-event"></a>이벤트 조사에 대 한 권장 사항 

경고의 **권장 사항** 영역에는 이벤트를 보다 잘 이해할 수 있도록 하는 정보가 표시 됩니다. 경고 이벤트를 관리 하거나 장치 또는 네트워크에서 작업을 수행 하기 전에이 정보를 검토 합니다.

## <a name="next-steps"></a>다음 단계

[경고 워크플로 가속화](how-to-accelerate-alert-incident-response.md)

[경고 이벤트 관리](how-to-manage-the-alert-event.md)
