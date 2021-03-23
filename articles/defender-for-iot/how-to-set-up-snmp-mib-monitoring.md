---
title: SNMP MIB 모니터링 설정
description: SNMP를 사용 하 여 센서 상태 모니터링을 수행할 수 있습니다. 센서는 권한 있는 모니터링 서버에서 보낸 SNMP 쿼리에 응답 합니다.
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 1ba52236f65c6c5daba68c67677cdc6adfb699b4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781673"
---
# <a name="set-up-snmp-mib-monitoring"></a>SNMP MIB 모니터링 설정

SNMP (Simple Network Management Protocol)를 사용 하 여 센서 상태 모니터링을 수행할 수 있습니다. 센서는 권한 있는 모니터링 서버에서 보낸 SNMP 쿼리에 응답 합니다. SNMP 모니터는 센서 Oid를 주기적으로 폴링합니다 (초당 최대 50 회).

Snmp 지원 버전은 SNMP v2 또는 SNMP v3입니다. SNMP는 포트 161 (SNMP)를 사용 하 여 UDP를 전송 프로토콜로 사용 합니다.

SNMP 모니터링 구성을 시작 하기 전에 방화벽에서 UDP 161 포트를 열어야 합니다.

## <a name="oids"></a>Oid

| 관리 콘솔 및 센서 | OID | 서식 | Description |
|--|--|--|--|
| 어플라이언스 이름 | 1.3.6.1.2.1.1.5.0 | DISPLAYSTRING | 온-프레미스 관리 콘솔의 어플라이언스 이름 |
| Vendor | 1.3.6.1.2.1.1.4.0 | DISPLAYSTRING | Microsoft 지원 (support.microsoft.com) |
| 플랫폼 | 1.3.6.1.2.1.1.1.0 | DISPLAYSTRING | 센서 또는 온-프레미스 관리 콘솔 |
| 일련 번호 | 1.3.6.1.4.1.9.9.53313.1 | DISPLAYSTRING | 라이선스에서 사용 하는 문자열 |
| 소프트웨어 버전 | 1.3.6.1.4.1.9.9.53313.2 | DISPLAYSTRING | Xsense 전체 버전 문자열 및 관리 전체 버전 문자열 |
| CPU 사용량 | 1.3.6.1.4.1.9.9.53313.3.1 | 된 GAUGE32 | 0에서 100에 대 한 표시 |
| CPU 온도 | 1.3.6.1.4.1.9.9.53313.3.2 | DISPLAYSTRING | Linux 입력을 기준으로 0 ~ 100에 대 한 섭씨 표시 |
| 메모리 사용량 | 1.3.6.1.4.1.9.9.53313.3.3 | 된 GAUGE32 | 0에서 100에 대 한 표시 |
| 디스크 사용량 | 1.3.6.1.4.1.9.9.53313.3.4 | 된 GAUGE32 | 0에서 100에 대 한 표시 |
| 서비스 상태 | 1.3.6.1.4.1.9.9.53313.5 | DISPLAYSTRING | 네 가지 중요 한 구성 요소 중 하나가 다운 된 경우 온라인 또는 오프 라인 |
| 대역폭 | 2.4에 대 한 범위를 벗어났습니다. |  | Xsense의 각 모니터 인터페이스에서 받은 대역폭 |

   - 존재 하지 않는 키는 [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200)기반으로 NULL, HTTP 200를 사용 하 여 응답 합니다.
    
   - 모든 아키텍처와 실제 센서에서 하드웨어 관련 (Mib (CPU 사용량, CPU 온도, 메모리 사용량, 디스크 사용량)를 테스트 해야 합니다. 가상 컴퓨터의 CPU 온도는 적용 되지 않습니다.

패킷의 연결 데이터 및 원시 데이터를 포함 하 여 센서가 수신 하는 모든 SNMP 쿼리를 포함 하는 로그를 다운로드할 수 있습니다.

SNMP v2 상태 모니터링을 정의 하려면:

1. 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

2. **활성 검색** 창에서 **SNMP MIB 모니터링** 을 선택 :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: 합니다.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="SNMP 창을 편집 합니다.":::

3. 허용 된 **호스트** 섹션에서 **호스트 추가** 를 선택 하 고 시스템 상태 모니터링을 수행 하는 서버의 IP 주소를 입력 합니다.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="허용 된 호스트의 IP 주소를 입력 합니다.":::

4. **인증** 섹션의 **SNMP v2 커뮤니티 문자열** 상자에 문자열을 입력 합니다. SNMP 커뮤니티 문자열은 최대 32 문자를 포함할 수 있으며 영숫자 (대문자, 소문자 및 숫자)를 조합 하 여 포함할 수 있습니다. 공백은 허용되지 않습니다.

5. **저장** 을 선택합니다.

SNMP v3 상태 모니터링을 정의 하려면:

1. 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

2. **활성 검색** 창에서 **SNMP MIB 모니터링** 을 선택 :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: 합니다.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="SNMP 창을 편집 합니다.":::

3. 허용 된 **호스트** 섹션에서 **호스트 추가** 를 선택 하 고 시스템 상태 모니터링을 수행 하는 서버의 IP 주소를 입력 합니다.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="허용 된 호스트의 IP 주소를 입력 합니다.":::

4. **인증** 섹션에서 다음 매개 변수를 설정 합니다.

    | 매개 변수 | Description |
    |--|--|
    | **사용자 이름** | SNMP 사용자 이름은 최대 32 문자를 포함할 수 있으며 영숫자 (대문자, 소문자 및 숫자)를 조합 하 여 포함할 수 있습니다. 공백은 허용되지 않습니다. <br /> <br />SNMP v3 인증에 대 한 사용자 이름은 시스템 및 SNMP 서버에서 구성 해야 합니다. |
    | **암호** | 대/소문자를 구분 하는 인증 암호를 입력 합니다. 인증 암호는 8 ~ 12 자의 문자를 포함할 수 있으며 영숫자 (대문자, 소문자 및 숫자)를 조합 하 여 포함할 수 있습니다. <br /> <br/>SNMP v3 인증에 대 한 사용자 이름은 시스템 및 SNMP 서버에서 구성 해야 합니다. |
    | **인증 유형** | MD5 또는 SHA를 선택 합니다. |
    | **암호화** | DES 또는 AES를 선택 합니다. |
    | **비밀 키** | 키는 정확히 8 개의 문자를 포함 해야 하며 영숫자 (대문자, 소문자 및 숫자)를 조합 하 여 포함 해야 합니다. |

5. **저장** 을 선택합니다.

## <a name="see-also"></a>참고 항목

[문제 해결 로그 내보내기](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
