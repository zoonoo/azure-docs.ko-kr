---
title: 센서 및 온-프레미스 관리 콘솔 문제 해결
description: 센서 및 온-프레미스 관리 콘솔 문제를 해결 하 여 발생 하는 모든 문제를 제거 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: a57db4f88de4a3b32b4fb315fb331500f955d501
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840695"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>센서 및 온-프레미스 관리 콘솔 문제 해결

이 문서에서는 센서 및 온-프레미스 관리 콘솔의 기본 문제 해결 도구에 대해 설명 합니다. 여기에 설명 된 항목 외에도 다음과 같은 방법으로 시스템의 상태를 확인할 수 있습니다.

**경고**: 트래픽을 모니터링 하는 센서 인터페이스가 다운 된 경우 경고가 생성 됩니다. 

**Snmp**: 센서 상태는 snmp를 통해 모니터링 됩니다. IoT 용 Azure Defender는 권한 있는 모니터링 서버에서 보낸 SNMP 쿼리에 응답 합니다. 

**시스템 알림**: 관리 콘솔에서 센서를 제어할 때 실패 한 센서 백업 및 연결 끊김 센서에 대 한 경고를 전달할 수 있습니다.

## <a name="sensor-troubleshooting-tools"></a>센서 문제 해결 도구

### <a name="investigate-password-failure-at-initial-sign-in"></a>초기 로그인 시 암호 오류 조사

미리 구성 된 화살표 센서에 처음으로 로그인 하는 경우 다음 암호 복구를 수행 해야 합니다.

1. IoT 용 Defender 로그인 화면에서 **암호 복구** 옵션을 선택 합니다. 

   **암호 복구** 화면이 열립니다. 여기서 사용자 및 구독을 선택 하 라는 메시지가 표시 되 고 사용자에 게 고유한 식별자가 제공 됩니다.

1. IoT **사이트 및 센서** 용 Defender 페이지로 이동 하 여 **암호 복구** 탭을 선택 합니다.

1. **암호 복구** 화면에서 받은 고유 식별자를 입력 하 고 **복구** 를 선택 합니다. `password_recovery.zip`파일이 다운로드 됩니다.

   > [!NOTE]
   > 활성화 파일을 변경 하지 마세요. 서명 된 파일이 며 변조 하는 경우 작동 하지 않습니다.

1. **암호 복구** 화면에서 파일을 업로드 `password_recovery.zip` 하 고 **다음** 을 선택 합니다.

그런 다음 관리 콘솔에 대 한 시스템 생성 암호를 수신 합니다. 

### <a name="investigate-a-lack-of-traffic"></a>트래픽 부족 조사

센서는 구성 된 포트 중 하나에 트래픽이 없음을 인식 하면 콘솔 맨 위에 표시기가 나타납니다. 이 표시기는 모든 사용자에 게 표시 됩니다.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="트래픽이 검색 되지 않은 경고의 스크린샷":::
 
이 메시지가 표시 되 면 트래픽이 없는 위치를 조사할 수 있습니다. 범위 케이블이 연결 되어 있고 범위 아키텍처가 변경 되지 않았는지 확인 합니다.  

지원 및 문제 해결 정보는 [Microsoft 지원](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)에 문의 하세요.

### <a name="check-system-performance"></a>시스템 성능을 확인합니다. 

새 센서가 배포 되거나 예를 들어 센서가 느리게 작동 하거나 경고를 표시 하지 않는 경우 시스템 성능을 확인할 수 있습니다.

시스템 성능을 확인 하려면:

1. 대시보드에서를 확인 `PPS > 0` 합니다.

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="샘플 대시보드의 스크린샷"::: 

2. 측면 메뉴에서 **장치** 를 선택 합니다.

3. **장치** 창에서 장치를 검색 하는지 확인 합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="장치가 검색 되는지 확인 합니다.":::

4. 측면 메뉴에서 **데이터 마이닝** 을 선택 합니다.

5. **데이터 마이닝** 창에서 **모두** 를 선택 하 고 보고서를 생성 합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="데이터 마이닝을 사용 하 여 새 보고서를 생성 합니다.":::

6. 보고서에 데이터가 포함 되어 있는지 확인 합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="보고서에 데이터가 포함 되어 있는지 확인 합니다.":::

7. 측면 메뉴에서 **추세 & 통계** 를 선택 합니다.

8. **추세 & 통계** 창에서 **위젯 추가** 를 선택 합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="위젯을 선택 하 여 추가 합니다.":::

9. 위젯을 추가 하 고 데이터를 표시 하는지 확인 합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="위젯이 데이터를 표시 하는지 확인 합니다.":::

10. 측면 메뉴에서 **경고** 를 선택 합니다. **경고** 창이 표시 됩니다.

11. 경고가 생성 되었는지 확인 합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="경고가 생성 되었는지 확인 합니다.":::


### <a name="investigate-a-lack-of-expected-alerts"></a>예상 되는 경고의 부족 조사

**경고** 창에 필요한 경고가 표시 되지 않으면 다음을 확인 합니다.

- 동일한 경고가 **경고** 창에 다른 보안 인스턴스에 대 한 반응으로 이미 나타나는지 확인 합니다. 그렇다면이 경고가 아직 처리 되지 않은 경우에는 센서 콘솔에 새 경고가 표시 되지 않습니다.

- 관리 콘솔에서 **경고 제외** 규칙을 사용 하 여이 경고를 제외 하지 않았는지 확인 합니다. 

### <a name="investigate-widgets-that-show-no-data"></a>데이터를 표시 하지 않는 위젯 조사

**추세 & 통계** 창의 위젯은 데이터를 표시 하지 않으면 다음을 수행 합니다.

- [시스템 성능을 확인](#check-system-performance)합니다.

- 시간 및 지역 설정이 제대로 구성 되어 있고 이후 시간으로 설정 되어 있지 않은지 확인 합니다. 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>브로드캐스팅 장치만 표시 하는 장치 맵 조사

지도에 표시 된 장치가 서로 연결 되어 있지 않으면 범위 포트 구성에 문제가 있을 수 있습니다. 즉, 브로드캐스트 장치만 표시 되 고 유니캐스트 트래픽도 표시 되지 않을 수 있습니다.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="브로드캐스팅 장치를 봅니다.":::

이 경우 브로드캐스트 트래픽만 볼 수 있는지 유효성을 검사 해야 합니다. 그런 다음, 유니캐스트 트래픽을 볼 수 있도록 네트워크 엔지니어가 범위 포트 구성을 수정 하도록 요청 합니다.

브로드캐스트 트래픽만 확인 하려면 다음을 수행 합니다.

- **데이터 마이닝** 화면에서 **All** 옵션을 사용 하 여 보고서를 만듭니다. 그런 다음 브로드캐스트 및 멀티 캐스트 트래픽만 보고서에 표시 되는지 확인 합니다.

또는

- 스위치에서 직접 PCAP를 기록 하거나 Wireshark를 사용 하 여 랩톱을 연결 합니다.

### <a name="connect-the-sensor-to-ntp"></a>NTP에 센서 연결

독립 실행형 센서와 관련 센서를 사용 하 여 NTP에 연결 하는 관리 콘솔을 구성할 수 있습니다.

독립 실행형 센서를 NTP에 연결 하려면:

- [지원 팀에 도움을 요청 하세요](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

관리 콘솔에서 제어 하는 센서를 NTP에 연결 하려면 다음을 수행 합니다.

- NTP에 대 한 연결은 관리 콘솔에서 구성 됩니다. 관리 콘솔에서 제어 하는 모든 센서가 NTP 연결을 자동으로 가져옵니다.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>지도에 장치가 표시 되지 않거나 인터넷 관련 경고가 여러 개 있는 경우 조사

경우에 따라 ICS 장치는 외부 IP 주소로 구성 됩니다. 이러한 ICS 장치는 맵에 표시 되지 않습니다. 장치 대신 인터넷 클라우드가 지도에 표시 됩니다. 이러한 장치의 IP 주소는 클라우드 이미지에 포함 됩니다.

동일한 문제를 해결 하는 또 다른 방법은 인터넷 관련 경고가 여러 개 표시 되는 경우입니다.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="인터넷 관련 경고가 여러 개 있습니다.":::

구성을 수정 하려면:

1. 장치 맵의 클라우드 아이콘을 마우스 오른쪽 단추로 클릭 하 고 **IP 주소 내보내기** 를 선택 합니다. 비공개 인 공용 범위를 복사 하 여 서브넷 목록에 추가 합니다. 자세한 내용은 [서브넷 구성](how-to-control-what-traffic-is-monitored.md#configure-subnets)을 참조 하세요.

2. 인터넷 연결에 대 한 새 데이터 마이닝 보고서를 생성 합니다.

3. 데이터 마이닝 보고서에서 :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: 관리자 모드를 입력 하 고 ICS 장치의 IP 주소를 삭제 하려면 선택 합니다.

### <a name="tweak-the-sensors-quality-of-service"></a>센서의 서비스 품질 조정

네트워크 리소스를 저장 하기 위해 센서가 일상적인 절차에 사용 하는 인터페이스 대역폭을 제한할 수 있습니다.

인터페이스 대역폭을 제한 하려면 `cyberx-xsense-limit-interface` sudo 권한으로 실행 해야 하는 CLI 도구를 사용 합니다. 이 도구는 다음과 같은 인수를 가져옵니다.

  - `* -i`: 인터페이스 (예: eth0).

  - `* -l`: limit (예: 30 kbit/1mbit). Kbps, mbps, kbit, mbit 또는 bps와 같은 대역폭 단위를 사용할 수 있습니다.

  - `* -c`: clear (인터페이스 대역폭 제한을 지우려면).

서비스 품질을 조정 하려면 다음을 수행 합니다.

1. IoT 사용자에 대 한 Defender로 센서 CLI에 로그인 하 고을 입력 `sudo cyberx-xsense-limit-interface-I eth0 -l value` 합니다.

   `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > 실제 어플라이언스의 경우 em1 인터페이스를 사용 합니다.

2. 인터페이스 제한 사항을 취소 하려면을 입력 `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` 합니다.

## <a name="on-premises-management-console-troubleshooting-tools"></a>온-프레미스 관리 콘솔 문제 해결 도구

### <a name="investigate-a-lack-of-expected-alerts"></a>예상 되는 경고의 부족 조사

**경고 창에** 예상 되는 경고가 표시 되지 않으면 다음을 확인 합니다.

- 동일한 경고가 **경고** 창에 다른 보안 인스턴스에 대 한 반응으로 이미 나타나는지 확인 합니다. 그렇다면이 경고가 아직 처리 되지 않은 경우에는 새 경고가 표시 되지 않습니다.

- 온-프레미스 관리 콘솔에서 **경고 제외** 규칙을 사용 하 여이 경고를 제외 하지 않았는지 확인 합니다.  

### <a name="tweak-the-quality-of-service"></a>서비스 품질 조정

네트워크 리소스를 저장 하기 위해 어플라이언스와 온-프레미스 관리 콘솔 간에 하나의 동기화 작업에서 외부 시스템 (예: 전자 메일 또는 SIEM)으로 전송 되는 경고 수를 제한할 수 있습니다.

기본값은 50입니다. 즉, 어플라이언스와 온-프레미스 관리 콘솔 간에 하나의 통신 세션에서 외부 시스템에 대 한 경고는 50 개이 하 여야 합니다. 

경고 수를 제한 하려면 `notifications.max_number_to_report` 에서 사용할 수 있는 속성을 사용 `/var/cyberx/properties/management.properties` 합니다. 이 속성을 변경한 후에는 다시 시작할 필요가 없습니다.

서비스 품질을 조정 하려면 다음을 수행 합니다.

1. IoT 사용자에 대 한 Defender로 로그인 합니다. 

2. 기본값을 확인 합니다.

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   다음 기본값이 표시 됩니다.

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

3. 기본 설정을 편집 합니다.

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

4. 다음 줄의 설정을 편집 합니다.

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

5. 변경 내용을 저장합니다. 다시 시작하지 않아도 됩니다.

## <a name="export-information-for-troubleshooting"></a>문제 해결을 위한 정보 내보내기

네트워크를 모니터링 하 고 분석 하기 위한 도구 외에 추가 조사를 위해 지원 팀에 정보를 보낼 수 있습니다. 로그를 내보낼 때 센서는 내보낸 로그에 대해 고유한 OTP (일회용 암호)를 별도의 텍스트 파일에 자동으로 생성 합니다. 

로그를 내보내려면:

1. 왼쪽 창에서 **시스템 설정** 을 선택 합니다.

2. **로그 내보내기** 를 선택합니다.

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="시스템 지원으로 로그를 내보냅니다.":::

3. **파일 이름** 상자에 로그 내보내기에 사용할 파일 이름을 입력 합니다. 기본값은 현재 날짜입니다.

4. 내보낼 데이터를 정의 하려면 데이터 범주를 선택 합니다.  

    | 범주 내보내기 | Description |
    |--|--|
    | **운영 체제 로그** | 운영 체제 상태에 대 한 정보를 가져오려면이 옵션을 선택 합니다. |
    | **설치/업그레이드 로그** | 설치 및 업그레이드 구성 매개 변수를 조사 하려면이 옵션을 선택 합니다. |
    | **시스템 온전성 출력** | 시스템 성능을 확인 하려면이 옵션을 선택 합니다. |
    | **Dissection 로그** | 프로토콜 dissection에 대 한 고급 검사를 허용 하려면이 옵션을 선택 합니다. |
    | **OS 커널 덤프** | 커널 메모리 덤프를 내보내려면이 옵션을 선택 합니다. 커널 메모리 덤프에는이 커널에서 발생 한 문제 발생 시 커널이 사용 하 고 있는 모든 메모리가 포함 됩니다. 덤프 파일의 크기가 전체 메모리 덤프 보다 작습니다. 일반적으로 덤프 파일은 시스템의 실제 메모리 크기의 1/3입니다. |
    | **로그 전달** | 전달 규칙을 조사 하려면이 옵션을 선택 합니다. |
    | **SNMP 로그** | SNMP 상태 검사 정보를 수신 하려면이 옵션을 선택 합니다. |
    | **핵심 응용 프로그램 로그** | 핵심 응용 프로그램 구성 및 작업에 대 한 데이터를 내보내려면이 옵션을 선택 합니다. |
    | **CM 로그와 통신** | 관리 콘솔과의 연결 중단이 지속적으로 발생 하는 경우이 옵션을 선택 합니다. |
    | **웹 응용 프로그램 로그** | 응용 프로그램의 웹 인터페이스에서 보낸 모든 요청에 대 한 정보를 가져오려면이 옵션을 선택 합니다. |
    | **시스템 백업** | 시스템의 정확한 상태를 조사 하기 위해 모든 시스템 데이터의 백업을 내보내려면이 옵션을 선택 합니다. |
    | **Dissection 통계** | 프로토콜 통계의 고급 검사를 허용 하려면이 옵션을 선택 합니다. |
    | **데이터베이스 로그** | 시스템 데이터베이스에서 로그를 내보내려면이 옵션을 선택 합니다. 시스템 로그를 조사 하면 시스템 문제를 파악 하는 데 도움이 됩니다. |
    | **Configuration** | 모든 구성 가능한 매개 변수에 대 한 정보를 내보내 모든 구성 요소가 올바르게 구성 되었는지 확인 하려면이 옵션을 선택 합니다. |

5. 모든 옵션을 선택 하려면 **범주** 선택 옆에 있는 **모두 선택** 을 선택 합니다.

6. **로그 내보내기** 를 선택합니다.

내보낸 로그가 **보관 된 로그** 목록에 추가 됩니다. 지원 팀에 OTP를 별도의 메시지 및 내보낸 로그에서 중간으로 보냅니다. 지원 팀에서는 로그를 암호화 하는 데 사용 되는 고유 OTP를 사용 하 여 내보낸 로그를 추출할 수 있습니다.

보관 된 로그 목록에는 최대 5 개의 항목이 포함 될 수 있습니다. 목록의 항목 수가 해당 숫자를 벗어나면 가장 이른 항목이 삭제 됩니다.

## <a name="see-also"></a>참조

- [경고 보기](how-to-view-alerts.md)

- [SNMP MIB 모니터링 설정](how-to-set-up-snmp-mib-monitoring.md)

- [센서 연결 끊기 이벤트 이해](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
