---
title: 경고 보기
description: 다양 한 범주에 따라 경고를 보고, 검색 기능을 사용 하 여 관심 있는 경고를 쉽게 찾을 수 있습니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: bce83e11a0d4567d37e78388445c108385ca9a61
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509075"
---
# <a name="view-alerts"></a>경고 보기

이 문서에서는 센서에 의해 트리거되는 경고를 확인 하 고 경고 도구를 사용 하 여 관리 하는 방법을 설명 합니다.

보관 또는 고정 된 경고와 같은 다양 한 범주에 따라 경고를 볼 수 있습니다. 또는 IP 또는 MAC 주소를 기반으로 하는 경고와 같이 관심 있는 경고를 검색할 수 있습니다.  

센서 대시보드에서 경고를 볼 수도 있습니다.

경고를 보려면

- 측면 메뉴에서 **경고** 를 선택 합니다. **경고** 창에 센서가 감지한 경고가 표시 됩니다.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="경고 화면의 뷰입니다.":::

## <a name="view-alerts-by-category"></a>범주별 경고 보기

**경고** 주 보기에서 다양 한 범주에 따라 경고를 볼 수 있습니다. 경고를 선택 하 여 세부 정보를 검토 하 고 이벤트를 관리 합니다.

| 유형별 정렬 | 설명 |
|--|--|
| **중요 한 알림** | 경고가 중요도 별로 정렬 되었습니다. |
| **고정 된 경고** | 추가 조사를 위해 사용자에 게 고정 된 경고입니다. 고정 된 경고는 보관 되지 않으며 고정 된 폴더에 14 일 동안 저장 됩니다. |
| **최근 경고** | 시간별로 정렬 된 경고입니다. |
| **승인 됨 경고** | 승인 되 고 처리 되지 않았거나 unmuted 된 경고입니다. |
| **보관 된 경고** | 시스템이 자동으로 보관 됨을 경고 합니다. 관리자 사용자만 액세스할 수 있습니다. |

## <a name="search-for-alerts-of-interest"></a>관심 경고 검색

**경고** 주 보기는 관심 있는 경고를 찾는 데 도움이 되는 다양 한 검색 기능을 제공 합니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="경고 학습 스크린샷.":::

### <a name="text-search"></a>텍스트 검색 

텍스트, 숫자 또는 문자를 기준으로 경고를 검색 하려면 **무료 검색** 옵션을 사용 합니다.

검색 대상:

- **사용 가능한 검색** 필드에 필요한 텍스트를 입력 하 고 키보드에서 enter 키를 누릅니다.

검색을 지우려면:

- **사용 가능한 검색** 필드에서 텍스트를 삭제 하 고 키보드에서 enter 키를 누릅니다.

### <a name="device-group-or-device-ip-address-search"></a>장치 그룹 또는 장치 IP 주소 검색

특정 IP 주소 또는 장치 그룹을 참조 하는 경고를 검색 합니다. 장치 그룹은 장치 맵에 생성 됩니다.

고급 필터를 사용 하려면:

1. **경고** 주 보기에서 **고급 필터** 를 선택 합니다.

2. 장치 그룹 또는 장치를 선택 합니다.

3. **확인** 을 선택합니다.

4. 검색을 지우려면 **모두 지우기** 를 선택 합니다.

### <a name="security-versus-operational-alert-search"></a>보안 및 운영 경고 검색

운영 및 보안 경고 보기를 전환 합니다.

- **보안** 경고는 잠재적인 맬웨어 트래픽, 네트워크 변칙, 정책 위반 및 프로토콜 위반을 나타냅니다.

- **운영** 경고는 네트워크 비정상, 정책 위반 및 프로토콜 위반을 나타냅니다.

옵션을 선택 하지 않으면 모든 경고가 표시 됩니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="경고 화면에서 보안을 설정 합니다.":::

## <a name="alert-page-options"></a>경고 페이지 옵션

경고 메시지는 다음과 같은 작업을 제공 합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false":::경고를 승인 하려면 선택 합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false":::경고를 승인 하지 않도록 선택 합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false":::경고를 고정 하려면 선택 합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false":::경고의 고정을 해제 하려면 선택 합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false":::모든 경고를 승인 하려면 선택 합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false":::모든 경고를 알아보고 승인 하려면 선택 합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false":::경고 목록을 CSV 파일로 내보내고 내보내기 옵션을 선택 하려면 선택 합니다. 일반 내보내기-CSV 옵션에 대 한 **경고 내보내기** 를 선택 합니다. 또는 CSV 파일에서 경고에 대 한 추가 정보를 위해 별도의 행을 추가할 수 있도록 **확장 된 경고 내보내기** 를 선택 합니다.

## <a name="alert-pop-up-window-options"></a>경고 팝업 창 옵션

- 아이콘을 선택 :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: 하 여 경고 보고서를 PDF 파일로 다운로드 합니다.

- 아이콘을 선택 :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: 하 여 PCAP 파일을 다운로드 합니다. 사용 가능한 네트워크 프로토콜 분석기 인 Wireshark를 사용 하 여 파일을 볼 수 있습니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false":::관련 경고 패킷만 포함 하는 필터링 된 PCAP 파일을 다운로드 하 여 출력 파일 크기를 줄이고 더 집중 된 분석을 허용 하려면 선택 합니다. Wireshark를 사용 하 여 파일을 볼 수 있습니다.

- 아이콘을 선택 :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: 하 여 이벤트 타임 라인에 경고를 표시 합니다.

- 아이콘을 선택 :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: 하 여 경고를 고정 합니다.

- 아이콘을 선택 :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: 하 여 경고를 고정 해제 합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false":::트래픽을 승인 하려면 선택 합니다 (보안 분석가 및 관리자만 해당).

- 장치를 선택 하 여 장치 맵에 표시 합니다.

## <a name="next-steps"></a>다음 단계

[경고 이벤트 관리](how-to-manage-the-alert-event.md)

[경고 워크플로 가속화](how-to-accelerate-alert-incident-response.md)
