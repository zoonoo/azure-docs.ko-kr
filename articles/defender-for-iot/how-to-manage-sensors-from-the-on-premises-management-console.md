---
title: 온-프레미스 관리 콘솔에서 센서 관리
description: 센서 버전 업데이트, 센서에 시스템 설정 푸시, 센서에서 엔진 사용 및 사용 안 함을 포함 하 여 관리 콘솔에서 센서를 관리 하는 방법에 대해 알아봅니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 73eb693ab9f06535fd523eb386969e2cce961eef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523960"
---
# <a name="manage-sensors-from-the-management-console"></a>관리 콘솔에서 센서 관리

이 문서에서는 다음을 포함 하 여 관리 콘솔에서 센서를 관리 하는 방법을 설명 합니다.

- 센서에 시스템 설정 푸시

- 센서에서 엔진 사용 및 사용 안 함

- 센서 버전 업데이트

## <a name="push-configurations"></a>푸시 구성

다양 한 시스템 설정을 정의 하 고 관리 콘솔에 연결 된 센서에 자동으로 적용할 수 있습니다. 이를 통해 시간을 절약 하 고 엔터프라이즈 센서를 통해 원활한 설정을 보장할 수 있습니다.

관리 콘솔에서 다음과 같은 센서 시스템 설정을 정의할 수 있습니다.

- 메일 서버

- SNMP MIB 모니터링

- Active Directory

- DNS 설정

- 서브넷

- 포트 별칭

시스템 설정을 적용 하려면:

1. 콘솔의 왼쪽 창에서 **시스템 설정** 을 선택 합니다.

2. **센서 구성** 창에서 옵션 중 하나를 선택 합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="센서에 대 한 시스템 설정 옵션입니다.":::

   다음 예에서는 엔터프라이즈 센서에 대 한 메일 서버 매개 변수를 정의 하는 방법을 설명 합니다.

3. **메일 서버** 를 선택 합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="시스템 설정 화면에서 메일 서버를 선택 합니다.":::

4. 왼쪽에서 센서를 선택 합니다.

5. 메일 서버 매개 변수를 설정 하 고 **복제** 를 선택 합니다. 센서 트리의 각 항목 옆에 확인란이 표시 됩니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="센서에 대해 확인란이 선택 되었는지 확인 합니다.":::

6. 센서 트리에서 구성을 적용 하려는 항목을 선택 합니다.

7. **저장** 을 선택합니다.

## <a name="update-versions"></a>업데이트 버전

온-프레미스 관리 콘솔에서 동시에 여러 센서를 업데이트할 수 있습니다.

여러 센서를 업데이트 하려면:

1. [Azure 포털](https://portal.azure.com/)로 이동합니다.

2. IoT 용 Azure Defender로 이동 합니다.

3. **업데이트** 페이지로 이동 합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="업데이트 대시보드 보기의 스크린샷":::

4. **센서** 섹션에서 **다운로드** 를 선택 하 고 파일을 저장 합니다.

5. 관리 콘솔에 로그인 하 고 **시스템 설정** 을 선택 합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="시스템 설정을 선택 하는 관리 메뉴의 스크린샷":::

6. **센서 엔진 구성** 섹션에서 업데이트 하려는 센서를 표시 한 다음 **자동 업데이트** 를 선택 합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="학습 모드와 자동 업데이트를 보여 주는 두 개의 센서":::

7. **변경 내용 저장** 을 선택합니다.

8. **센서 버전 업그레이드** 창에서을 선택 :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: 합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="파일을 표시 하는 센서 버전 업그레이드 화면입니다.":::

9. **파일 업로드** 대화 상자가 열립니다. **업데이트** 페이지에서 다운로드 한 파일을 업로드 합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="찾아보기 단추를 선택 하 여 파일을 업로드 합니다.":::

10. 업데이트 프로세스 중에 각 센서의 업데이트 상태가 **사이트 관리** 창에 표시 됩니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="업데이트의 진행률을 관찰 합니다.":::

## <a name="update-threat-intelligence-packages"></a>위협 인텔리전스 패키지 업데이트 

위협 인텔리전스에 대 한 데이터 패키지는 각각의 IoT 용 새 Defender 버전에 제공 되거나 릴리스 사이에 필요한 경우에 제공 됩니다. 패키지에는 서명 (맬웨어 서명 포함), CVEs 및 기타 보안 콘텐츠가 포함 됩니다. 

IoT 포털의 **업데이트** 페이지에서이 파일을 수동으로 업로드 하 고 센서로 자동으로 업데이트할 수 있습니다. 

위협 인텔리전스 데이터를 업데이트 하려면: 

1. IoT **업데이트** 용 Defender 페이지로 이동 합니다. 

2. 파일을 다운로드 하 고 저장 합니다.

3. 관리 콘솔에 로그인 합니다. 

4. 측면 메뉴에서 **시스템 설정** 을 선택 합니다. 

5. **센서 엔진 구성** 섹션에서 업데이트를 받을 센서를 선택 합니다.  

6. **위협 인텔리전스 데이터 선택** 섹션에서 더하기 기호 ()를 선택 **+** 합니다. 

7. IoT **업데이트** 에 대 한 Defender 페이지에서 다운로드 한 패키지를 업로드 합니다.

## <a name="understand-sensor-disconnection-events"></a>센서 연결 끊기 이벤트 이해

할당 된 온-프레미스 관리 콘솔에서 센서의 연결을 끊으면 **Site Manager** 창에 연결 끊기 정보가 표시 됩니다. 다음 센서 연결 끊기 정보를 사용할 수 있습니다.

- "온-프레미스 관리 콘솔은 센서에서 받은 데이터를 처리할 수 없습니다."

- "시간 드리프트가 검색 되었습니다. 온-프레미스 관리 콘솔을 센서에서 분리 했습니다. "

- "센서가 온-프레미스 관리 콘솔과 통신 하지 않습니다. 네트워크 연결 또는 인증서 유효성 검사를 확인 하십시오. "

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="영역 1 보기의 스크린샷":::

연결 되지 않은 센서에 대 한 정보를 사용 하 여 타사에 게 경고를 보낼 수 있습니다. 자세한 내용은 [센서 실패 경고 전달](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts)을 참조 하세요.

## <a name="enable-or-disable-sensors"></a>센서 사용 또는 사용 안 함

센서는 IoT 엔진에 대해 5 개의 Defender에 의해 보호 됩니다. 연결 된 센서에 대해 엔진을 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

| 엔진 | 설명 | 예제 시나리오 |
|--|--|--|
| 프로토콜 위반 엔진 | 패킷 구조 또는 필드 값이 프로토콜 사양을 따르지 않으면 프로토콜 위반이 발생 합니다. | "잘못 된 MODBUS 작업 (함수 코드 0)" 경고입니다. 이 경고는 기본 장치에서 함수 코드가 0 인 요청을 보조 장치로 보냈음을 나타냅니다. 이는 프로토콜 사양에 따라 허용 되지 않으며, 보조 장치에서 입력을 올바르게 처리 하지 못할 수 있습니다. |
| 정책 위반 엔진 | 정책 위반은 학습 또는 구성 된 정책에 정의 된 기준 동작의 편차를 사용 하 여 발생 합니다. | "권한이 없는 HTTP 사용자 에이전트" 경고입니다. 이 경고는 정책에 의해 학습 또는 승인 되지 않은 응용 프로그램이 장치에서 HTTP 클라이언트로 사용 됨을 나타냅니다. 이는 해당 장치의 새 웹 브라우저 또는 응용 프로그램 일 수 있습니다. |
| 맬웨어 엔진 | 맬웨어 엔진이 악의적인 네트워크 작업을 검색 합니다. | "했는지에 of 악성 작업 (Stuxnet)" 경고 이 경고는 센서가 산업용 제어 및 SCADA 네트워크를 목표로 하는 고급 영구 위협 인 Stuxnet 맬웨어에 관련 된 것으로 알려진 의심 스러운 네트워크 활동을 발견 했음을 나타냅니다. |
| 변칙 엔진 | 맬웨어 엔진이 네트워크 동작의 비정상을 감지 합니다. | "통신 채널의 주기적인 동작" 이 구성 요소는 네트워크 연결을 검사 하 고 산업용 네트워크에서 공통적으로 발생 하는 데이터 전송의 주기적 또는 순환 동작을 찾는 구성 요소입니다. |
| 운영 엔진 | 이 엔진은 작업 인시던트 또는 작동 하지 않는 엔터티를 검색 합니다. | `Device is Suspected to be Disconnected (Unresponsive)` 오류. 이 경고는 장치가 미리 정의 된 기간 동안 요청에 응답 하지 않을 때 트리거됩니다. 장치 종료, 연결 끊기 또는 오작동을 나타낼 수 있습니다.
|

연결 된 센서에 대 한 엔진을 사용 하거나 사용 하지 않도록 설정 하려면:

1. 콘솔의 왼쪽 창에서 **시스템 설정** 을 선택 합니다.

2. **센서 엔진 구성** 섹션에서 엔진에 대해 **사용** 또는 **사용 안 함** 을 선택 합니다.
         
3. **변경 내용 저장** 을 선택 합니다.

   엔터프라이즈 센서 중 하나에서 사용 가능한 엔진이 일치 하지 않으면 빨간색 느낌표가 표시 됩니다. 엔진이 센서에서 직접 사용 하지 않도록 설정 되었을 수 있습니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="사용 가능한 엔진이 일치 하지 않습니다."::: 

## <a name="define-sensor-backup-schedules"></a>센서 백업 일정 정의

온-프레미스 관리 콘솔에서 센서 백업을 예약 하 고 요청 시 센서 백업을 수행할 수 있습니다. 이렇게 하면 하드 드라이브 오류 및 데이터 손실을 방지할 수 있습니다.

- 백업 대상: 구성 및 데이터

- 백업 되지 않은 항목: PCAP 파일 및 로그. PCAPs 및 로그를 수동으로 백업 하 고 복원할 수 있습니다.

기본적으로 센서는 매일 오전 3:00 시에 자동으로 백업 됩니다. 센서의 백업 일정 기능을 사용 하면 이러한 백업을 수집 하 여 온-프레미스 관리 콘솔 또는 외부 백업 서버에 저장할 수 있습니다. 센서에서 온-프레미스 관리 콘솔로 파일 복사는 암호화 된 채널을 통해 수행 됩니다.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="센서 백업 화면의 뷰입니다.":::

기본 센서 백업 위치가 변경 되 면 콘솔에 해당 위치에 대 한 액세스 권한이 있는 경우 온-프레미스 관리 콘솔에서 센서의 새 위치 또는 외부 위치에서 파일을 자동으로 검색 합니다. 

센서가 온-프레미스 관리 콘솔에 등록 되어 있지 않으면 **센서 백업 일정** 대화 상자에서 관리 되는 센서가 없음을 나타냅니다.  

복원 프로세스는 파일이 저장 된 위치에 관계 없이 동일 합니다.

### <a name="backup-storage-for-sensors"></a>센서에 대 한 백업 저장소

백업 된 파일이 할당 된 최대 백업 공간을 초과 하지 않는 경우 온-프레미스 관리 콘솔을 사용 하 여 각 관리 센서에 대해 최대 9 개의 백업을 유지할 수 있습니다. 

사용 가능한 공간은 사용 중인 관리 콘솔 모델을 기반으로 계산 됩니다. 

- **프로덕션 모델**: 기본 저장소는 40 GB입니다. 한도는 100 GB입니다. 

- **중간 규모 모델**: 기본 저장소는 20gb입니다. 한도는 50 GB입니다. 

- **랩톱 모델**: 기본 저장소는 10gb입니다. 제한은 25gb입니다. 

- **씬 모델**: 기본 저장소는 2gb입니다. 한도는 4gb입니다. 

- **견고한 모델**: 기본 저장소는 10gb입니다. 제한은 25gb입니다. 

기본 할당은 **센서 백업 일정** 대화 상자에 표시 됩니다. 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="메일 서버 구성 편집 화면입니다.":::

외부 서버에 백업 하는 경우 저장소 제한은 없습니다. 그러나 **센서 백업 일정**  >  **사용자 지정 경로** 필드에서 상한 할당 한도를 정의 해야 합니다. 지원 되는 숫자 및 문자는 다음과 `/, a-z, A-Z, 0-9, and _` 같습니다. 

다음은 할당 저장소 제한을 초과 하는 방법에 대 한 정보입니다.

- 할당 된 저장소 공간을 초과 하는 경우 센서가 백업 되지 않습니다. 

- 둘 이상의 센서를 백업 하는 경우 관리 콘솔에서 관리 되는 센서에 대 한 센서 파일을 검색 하려고 합니다.  

- 한 센서의 검색에서 한도를 초과 하는 경우 관리 콘솔은 다음 센서에서 백업 정보를 검색 하려고 합니다. 

정의 된 백업 수를 초과 하면 가장 오래 된 백업 파일이 삭제 되어 새 백업 파일이 적용 됩니다.

센서 백업 파일은 자동으로 이름이로 지정 `<sensor name>-backup-version-<version>-<date>.tar` 됩니다. 예를 들어 `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`을 참조하십시오. 

센서를 백업 하려면:

1. **시스템 설정** 창에서 **센서 백업 예약** 을 선택 합니다. 온-프레미스 관리 콘솔에서 관리 하는 센서가 **센서 백업 일정** 대화 상자에 표시 됩니다.  

2. **백업 수집** 설정/해제를 사용 하도록 설정 합니다.  

3. 일정 간격, 날짜 및 표준 시간대를 선택 합니다. 시간 형식은 24 시간제를 기준으로 합니다. 예를 들어 **18:00** 로 6:00 PM을 입력 합니다. 

4. **백업 저장소 할당** 필드에 백업에 할당 하려는 저장소를 입력 합니다. 최대 공간을 초과 하는 경우 알림이 표시 됩니다.

5. **마지막 보존** 필드에서 보유 하려는 센서 당 백업 수를 지정 합니다. 제한을 초과 하면 가장 오래 된 백업이 삭제 됩니다.  

6. 백업 위치 선택:  

   - 온-프레미스 관리 콘솔로 백업 하려면 **사용자 지정 경로** 설정/해제를 사용 하지 않도록 설정 합니다. 기본 위치는 `/var/cyberx/sensor-backups`입니다.  

   - 외부 서버에 백업 하려면 **사용자 지정 경로** 설정/해제를 사용 하도록 설정 하 고 위치를 입력 합니다. 지원 되는 숫자 및 문자는 다음과 `/, a-z, A-Z, 0-9, and, _` 같습니다. 

7. **저장** 을 선택합니다. 

즉시 백업 하려면 다음을 수행 합니다. 

- **지금 백업** 을 선택 합니다. 온-프레미스 관리 콘솔에서 센서 백업 파일을 만들고 수집 합니다. 

### <a name="receiving-backup-notifications-for-sensors"></a>센서에 대 한 백업 알림 받기 

**센서 백업 일정** 대화 상자와 백업 로그에 백업 성공 및 실패에 대 한 정보가 자동으로 나열 됩니다.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="센서 및 해당 센서의 위치와 모든 관련 정보를 확인 합니다.":::

다음 이유로 인해 오류가 발생할 수 있습니다.    

- 백업 파일을 찾을 수 없습니다. 

- 파일이 있지만 검색할 수 없습니다.  

- 네트워크 연결에 오류가 있습니다. 

- 온-프레미스 관리 콘솔에 할당 된 공간이 부족 하 여 백업을 완료할 수 없습니다.  

오류가 발생 하면 전자 메일 알림, syslog 업데이트 및 시스템 알림을 보낼 수 있습니다. 이렇게 하려면 **시스템 알림에서** 전달 규칙을 만듭니다. 

### <a name="restoring-sensors"></a>센서 복원 

온-프레미스 관리 콘솔과 CLI를 사용 하 여 백업을 복원할 수 있습니다.  

콘솔에서 복원 하려면: 

- **센서 시스템** 설정 창에서 **이미지 복원** 을 선택 합니다.

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="이미지의 백업을 복원 합니다.":::

  그러면 콘솔에 복원 오류가 표시 됩니다.  

CLI를 사용 하 여 복원 하려면: 

- 관리 계정에 로그인 하 고을 입력 `$ sudo cyberx-xsense-system-restore` 합니다. 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>외부 SMB 서버에 센서 백업 저장

외부 드라이브에 센서 백업을 저장할 수 있도록 SMB 서버를 설정 하려면 다음을 수행 합니다. 

1. 외부 SMB 서버에 공유 폴더를 만듭니다. 

2. SMB 서버에 액세스 하는 데 필요한 폴더 경로, 사용자 이름 및 암호를 가져옵니다. 

3. IoT 용 Defender에서 백업용 디렉터리를 만듭니다. 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Fstab 편집:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. 공유할 자격 증명을 편집 하거나 만듭니다. SMB 서버에 대 한 자격 증명은 다음과 같습니다. 

   `sudo nano /etc/samba/user` 

6. 추가:  

   `username=<user name>` 

   `password=<password>` 

7. 디렉터리를 탑재 합니다. 

   `sudo mount -a` 

8. IoT 센서에 대 한 Defender의 공유 폴더에 백업 디렉터리를 구성 합니다.  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. `Backup.shared_location`를 `<backup_folder_name_on_cyberx_server>`로 설정합니다.

## <a name="see-also"></a>참고 항목

[개별 센서 관리](how-to-manage-individual-sensors.md)
