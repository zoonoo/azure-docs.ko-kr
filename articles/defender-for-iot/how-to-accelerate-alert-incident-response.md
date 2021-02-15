---
title: 경고 워크플로 가속화
description: 경고 및 인시던트 워크플로를 개선 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 0a445abe0f448c28742282aedd1a886fae0f5a43
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526292"
---
# <a name="accelerate-alert-workflows"></a>경고 워크플로 가속화

이 문서에서는 Azure Defender for IoT에서 경고 설명, 경고 그룹 및 사용자 지정 경고 규칙을 사용 하 여 경고 워크플로를 가속화 하는 방법을 설명 합니다.  이러한 도구는 다음과 같은 경우에 유용 합니다.

- 네트워크에서 검색 된 많은 양의 경고 이벤트를 분석 하 고 관리 합니다.

- 특정 네트워크 활동을 파악 하 고 처리 합니다.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>경고 주석을 사용 하 여 인시던트 워크플로 가속화

경고 이벤트를 조사 하는 동안 개인 및 팀 간의 통신을 개선 하려면 경고 주석을 사용 합니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="악의적인 작업을 보여 주는 스크린샷":::

경고 주석을 사용 하 여 다음을 개선 합니다.

- **워크플로 단계**: 경고 완화 단계를 제공 합니다.

- **워크플로 추가 작업**: 단계가 수행 되었음을 알립니다.

- **워크플로 지침**: 이벤트에 대 한 권장 사항, 정보 또는 경고를 제공 합니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="경고 설명을 표시 하는 스크린샷":::

사용 가능한 옵션 목록이 각 경고에 표시 됩니다. 사용자는 하나 또는 여러 개의 메시지를 선택할 수 있습니다.

경고 설명을 추가 하려면:

1. 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

2. **시스템 설정** 창에서 **경고 주석** 을 선택 합니다.

3. **설명 추가** 상자에 주석 텍스트를 입력 합니다. 최대 50 자까지 사용 합니다. 쉼표는 허용 되지 않습니다.

4. **추가** 를 선택합니다.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>경고 그룹을 사용 하 여 인시던트 워크플로 가속화

경고 그룹을 통해 SOC 팀은 SIEM 솔루션에서 경고를 보고 필터링 한 다음 엔터프라이즈 보안 정책 및 비즈니스 우선 순위에 따라 이러한 경고를 관리할 수 있습니다. 예를 들어 새 검색에 대 한 경고는 검색 그룹으로 구성 됩니다. 이 그룹에는 새 장치, 새 Vlan, 새 사용자 계정, 새 MAC 주소 등의 검색을 처리 하는 경고가 포함 됩니다.

경고 그룹은 다음과 같은 파트너 솔루션에 대 한 전달 규칙을 만들 때 적용 됩니다.

  - Syslog 서버

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="전달 규칙을 만드는 스크린샷":::

관련 경고 그룹이 파트너 출력 솔루션에 표시 됩니다. 

### <a name="requirements"></a>요구 사항

다음 접두사가 포함 된 지원 되는 파트너 솔루션에 경고 그룹이 표시 됩니다.

- QRadar, ArcSight, Syslog CEF, Syslog LEEF 용 **고양이**

- Syslog 텍스트 메시지에 대 한 **경고 그룹**

- Syslog 개체에 대 한 **alert_group**

이러한 필드는 파트너 솔루션에서 경고 그룹 이름을 표시 하도록 구성 해야 합니다. 경고 그룹과 연결 된 경고가 없으면 파트너 솔루션의 필드가 **NA** 를 표시 합니다.

### <a name="default-alert-groups"></a>기본 경고 그룹

자동으로 정의 되는 경고 그룹은 다음과 같습니다.
|  |  |  |
|--|--|--|
| 비정상적인 통신 동작 | 사용자 지정 경고 | 원격 액세스 |
| 비정상적인 HTTP 통신 동작 | 검색 | 다시 시작 및 중지 명령 |
| 인증 | 펌웨어 변경 | 검색 |
| 무단 통신 동작 | 잘못 된 명령 | 센서 트래픽 |
| 대역폭 이상 | 인터넷 액세스 | 맬웨어 했는지에 |
| 버퍼 오버플로 | 작업 실패 | 악의적인 활동의 했는지에 |
| 명령 실패 | 운영 문제 |  |
| 구성 변경 내용 | 프로그래밍 |  |

경고 그룹은 미리 정의 되어 있습니다. 경고 그룹과 연결 된 경고에 대 한 자세한 내용 및 사용자 지정 경고 그룹을 만드는 방법에 대 한 자세한 내용은 [Microsoft 지원](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099)를 참조 하세요.

## <a name="customize-alert-rules"></a>경고 규칙 사용자 지정

사용자 지정 경고 규칙을 사용 하 여 관심 있는 활동을 더욱 구체적으로 파악 합니다. 

다음을 기준으로 사용자 지정 경고 규칙을 추가할 수 있습니다.

- 범주 (예: 프로토콜, 포트 또는 파일)입니다.
- 원본 및 대상 주소
- 선택한 범주를 기반으로 하는 조건 (예: 프로토콜에 연결 된 함수, 파일 이름, 포트 또는 전송 번호)입니다.
- 날짜 및 시간 참조를 기반으로 하는 조건입니다. 예를 들어 특정 날 또는 특정 날에 대 한 검색이 수행 된 경우입니다.

센서가 규칙에 설명 된 활동을 감지 하면 경고가 전송 됩니다.
개별 센서가 검색 하는 정보입니다. 예를 들어 프로토콜에서 원본 IP, 대상 IP 또는 명령 (프로토콜 내)을 기반으로 경고를 트리거하도록 지시 하는 규칙을 정의 합니다. 센서가 규칙에 정의 된 트래픽을 감지 하면 경고나 이벤트가 생성 됩니다.

또한 경고 규칙 작업을 사용 하 여 Defender for IoT에 다음을 수행할 수 있습니다.

- 사용자가 경고에서 PCAP 파일에 액세스할 수 있도록 허용 합니다.
- 경고 심각도를 할당 합니다.
- 경고가 아닌 이벤트를 생성 합니다. 검색 된 정보가 이벤트 타임 라인에 표시 됩니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="사용자 정의 규칙을 보여 주는 스크린샷":::

경고 메시지는 사용자 정의 규칙이 경고를 트리거 했음을 나타냅니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="사용자 지정 된 경고를 보여 주는 스크린샷":::

사용자 지정 경고 규칙을 만들려면 다음을 수행 합니다.

1. 센서의 측면 메뉴에서 **사용자 지정 경고** 를 선택 합니다.
1. 더하기 기호 ()를 선택 **+** 하 여 규칙을 만듭니다.
1. 규칙 이름을 정의 합니다.
1. **범주 창에서** 범주 또는 프로토콜을 선택 합니다.
1. 특정 원본 및 대상 IP 또는 MAC 주소를 정의 하거나 임의의 주소를 선택 합니다.
1. 하나 또는 여러 규칙 조건을 정의 합니다. 다음 두 가지 범주의 조건을 만들 수 있습니다.
    - 선택한 범주와 연결 된 고유 값을 기반으로 하는 조건입니다. 추가를 선택 하 고 값을 정의 합니다.
    - 활동이 검색 된 시간을 기반으로 하는 조건입니다. 검색 섹션에서 경고를 보내기 위해 검색을 수행 해야 하는 기간 및 일을 선택 합니다. 작업 시간이 나 후에 언제 든 지 작업이 검색 되 면 경고를 보내도록 선택할 수 있습니다. 작업 시간 정의 옵션을 사용 하 여 조직에 대 한 IoT 작업 시간을 Defender에 지시할 수 있습니다.
1. 규칙 작업 정의: 
    - 규칙이 **경보** 또는 **이벤트** 를 트리거 하는지 여부를 표시 합니다.
    - 경고에 심각도 수준을 할당 합니다.
    - 경고에 PCAP 파일이 포함 되어 있는지 여부를 표시 합니다.
1. **저장** 을 선택합니다.

규칙은 **사용자 지정 된 경고 규칙** 목록에 추가 됩니다. 여기서 기본 규칙 매개 변수를 검토 하 고, 규칙이 마지막으로 트리거된 시간 등을 확인할 수 있습니다. 목록에서 규칙을 사용 하거나 사용 하지 않도록 설정할 수도 있습니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="사용자가 추가한 사용자 지정 규칙의 스크린샷":::

### <a name="see-also"></a>참조

[경고에 제공된 정보 보기](how-to-view-information-provided-in-alerts.md)

[경고 이벤트 관리](how-to-manage-the-alert-event.md)
