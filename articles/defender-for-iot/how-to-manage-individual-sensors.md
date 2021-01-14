---
title: 개별 센서 관리
description: 활성화 파일 관리, 백업 수행 및 독립 실행형 센서 업데이트를 포함 하 여 개별 센서를 관리 하는 방법에 대해 알아봅니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/10/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b61bef89d53da280961b818425d11a6f81bbf5ea
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210242"
---
# <a name="manage-individual-sensors"></a>개별 센서 관리

이 문서에서는 개별 센서를 관리 하는 방법을 설명 합니다. 활성화 파일 관리, 백업 수행, 독립 실행형 센서 업데이트 등의 작업이 포함 됩니다.

또한 온-프레미스 관리 콘솔에서 여러 센서를 동시에 관리할 수 있는 특정 센서 관리 작업을 수행할 수 있습니다.

센서 온 보 딩 및 등록에 Azure Portal를 사용 합니다.

## <a name="manage-sensor-activation-files"></a>센서 활성화 파일 관리

센서는 Azure Portal의 IoT 용 Azure Defender와 등록 되었습니다. 각 센서는 로컬로 연결 된 센서 또는 클라우드 연결 센서로 등록 되었습니다.

배포 하는 각 센서에 고유한 정품 인증 파일이 업로드 됩니다. 새 파일을 사용 하는 시기 및 방법에 대 한 자세한 내용은 [새 활성화 파일 업로드](#upload-new-activation-files)를 참조 하세요. 파일을 업로드할 수 없으면 [활성화 파일 업로드 문제 해결](#troubleshoot-activation-file-upload)을 참조 하세요.

### <a name="about-activation-files-for-locally-connected-sensors"></a>로컬로 연결 된 센서의 정품 인증 파일 정보

로컬로 연결 된 센서는 Azure 구독과 연결 됩니다. 로컬로 연결 된 센서에 대 한 정품 인증 파일에 만료 날짜가 포함 되어 있습니다. 이 날짜로부터 한 달 전에 센서 콘솔 상단에 경고 메시지가 표시됩니다. 이 경고는 활성화 파일을 업데이트 한 후에도 유지 됩니다.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="시스템 설정의 스크린샷":::

활성화 파일이 만료 된 경우에도 IoT 기능에 대해 Defender를 계속 사용할 수 있습니다. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>클라우드 연결 센서에 대 한 활성화 파일 정보

클라우드 연결 된 센서는 IoT hub 용 Defender와 연결 됩니다. 이러한 센서는 활성화 파일의 기간에 따라 제한 되지 않습니다. 클라우드 연결 센서에 대 한 활성화 파일은 IoT hub 용 Defender에 대 한 연결을 보장 하는 데 사용 됩니다.

### <a name="upload-new-activation-files"></a>새 활성화 파일 업로드

다음과 같은 경우 등록 센서에 대 한 새 활성화 파일을 업로드 해야 할 수 있습니다.

- 정품 인증 파일은 로컬로 연결 된 센서에 만료 됩니다. 

- 다른 센서 관리 모드에서 작업 하려고 합니다. 

- 클라우드 연결 센서에 IoT hub에 대 한 새 Defender를 할당 하려고 합니다.

새 활성화 파일을 추가 하려면:

1. **센서 관리** 페이지로 이동 합니다.

2. 새 정품 인증 파일을 업로드 하려는 센서를 선택 합니다.

3. 삭제합니다.

4. 새 모드의 **온 보 딩** 페이지나 IoT hub 용 새 Defender에서 센서를 다시 등록 합니다.

5. 활성화 파일 **다운로드** 페이지에서 활성화 파일을 다운로드 합니다.

6. 파일을 저장합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="IoT hub 용 Defender에서 활성화 파일을 다운로드 합니다.":::

7. IoT 센서 콘솔용 Defender에 로그인 합니다.

8. 센서 콘솔에서 **시스템 설정** 다시  >  **활성화** 를 선택 합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="시스템 설정 화면에서 활성화를 선택 합니다.":::

9. **업로드** 를 선택 하 고 저장 한 파일을 선택 합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="저장 한 파일을 업로드 합니다.":::

10. **활성화** 를 선택합니다.

### <a name="troubleshoot-activation-file-upload"></a>활성화 파일 업로드 문제 해결

활성화 파일을 업로드할 수 없으면 오류 메시지가 표시 됩니다. 다음 이벤트가 발생 했을 수 있습니다.

- **로컬로 연결 된 센서**: 활성화 파일이 잘못 되었습니다. 파일이 유효 하지 않으면 IoT 포털 용 Defender로 이동 합니다. **센서 관리** 페이지에서 잘못 된 파일이 있는 센서를 선택 하 고 새 활성화 파일을 다운로드 합니다.

- **클라우드 연결 센서**: 센서를 인터넷에 연결할 수 없습니다. 센서의 네트워크 구성을 확인 하세요. 인터넷에 액세스 하기 위해 웹 프록시를 통해 센서를 연결 해야 하는 경우 **센서 네트워크 구성** 화면에서 프록시 서버가 올바르게 구성 되어 있는지 확인 합니다. \*Azure-devices.net:443가 방화벽 및/또는 프록시에서 허용 되는지 확인 합니다. 와일드 카드가 지원 되지 않거나 더 많은 제어를 원하는 경우 IoT hub에 대 한 특정 Defender에 대 한 FQDN을 방화벽 및/또는 프록시에서 열어야 합니다. 자세한 내용은 [참조 IoT Hub 끝점](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints)을 참조 하세요.  

- **클라우드 연결 센서의 경우**: 정품 인증 파일은 올바르지만 IoT 용 Defender는이 파일을 거부 했습니다. 이 문제를 해결할 수 없는 경우 Defender IoT 포털의 **센서 관리** 페이지에서 다른 정품 인증을 다운로드할 수 있습니다. 그래도 작동 하지 않으면 Microsoft 지원에 문의 하세요.

## <a name="manage-certificates"></a>인증서 관리

센서 설치 후에는 로컬 자체 서명 된 인증서가 생성 되어 센서 웹 응용 프로그램에 액세스 하는 데 사용 됩니다. 처음으로 센서에 로그인 할 때 관리자 사용자에 게 SSL/TLS 인증서를 제공 하 라는 메시지가 표시 됩니다.  처음 설정 하는 방법에 대 한 자세한 내용은 [센서 로그인 및 정품 인증](how-to-activate-and-set-up-your-sensor.md)을 참조 하세요.

이 문서에서는 인증서 업데이트, 인증서 CLI 명령 작업, 지원 되는 인증서 및 인증서 매개 변수에 대 한 정보를 제공 합니다.

### <a name="about-certificates"></a>인증서 정보

IoT 용 Azure Defender는 SSL/TLS 인증서를 사용 하 여 다음을 수행 합니다.

1. CA 서명 인증서를 업로드 하 여 조직에서 요청 하는 특정 인증서 및 암호화 요구 사항을 충족 합니다.

1. 관리 콘솔과 연결 된 센서 간의 유효성을 검사 하 고 관리 콘솔과 고가용성 관리 콘솔 간에 유효성을 검사 하도록 허용 합니다. 유효성 검사는 인증서 해지 목록 및 인증서 만료 날짜에 대해 평가 됩니다. **유효성 검사에 실패 하면 관리 콘솔과 센서 간의 통신이 중단 되 고 콘솔에 유효성 검사 오류가 표시 됩니다. 이 옵션은 설치 후 기본적으로 사용 하도록 설정 됩니다.**

 타사 전달 규칙 (예: SYSLOG, Splunk 또는 ServiceNow로 전송 되는 경고 정보) 또는 Active Directory 통신의 유효성을 검사 하지 않습니다.

### <a name="update-certificates"></a>인증서 업데이트

센서 관리자가 인증서를 업데이트할 수 있습니다.

인증서를 업데이트 하려면:  

1. **시스템 설정** 을 선택 합니다.
1. **SSL/TLS 인증서를 선택 합니다.**
1. 인증서를 삭제 하거나 편집 하 고 새 인증서를 추가 합니다.
    - 인증서 이름을 추가 합니다.
    - CRT 파일 및 키 파일을 업로드 하 고 암호를 입력 합니다.
    - 필요한 경우 PEM 파일을 업로드 합니다.

유효성 검사 설정을 변경 하려면:

1. **인증서 유효성 검사 사용** 설정/해제를 사용 하도록 설정 합니다.
1. **저장** 을 선택합니다.

이 옵션을 사용 하도록 설정 하 고 유효성 검사에 실패 하면 관리 콘솔과 센서 간의 통신이 중단 되 고 콘솔에 유효성 검사 오류가 표시 됩니다.

### <a name="certificate-support"></a>인증서 지원

지원 되는 인증서는 다음과 같습니다.

- 개인/엔터프라이즈 키 인프라 (사설 PKI) 
- 공개 키 인프라 (공용 PKI) 
- 어플라이언스에서 로컬로 생성 됩니다 (로컬에서 자체 서명 됨). **자체 서명 된 인증서를 사용 하지 않는 것이 좋습니다.** 이 연결은 *안전* 하지 않으며 테스트 환경에만 사용 해야 합니다. 인증서 소유자의 유효성을 검사할 수 없으며 시스템의 보안을 유지할 수 없습니다. 자체 서명 된 인증서는 프로덕션 네트워크에 사용해 서는 안 됩니다.  

지원 되는 매개 변수는 다음과 같습니다. 인증서 CRT

- 도메인 이름에 대 한 기본 인증서 파일
- 서명 알고리즘 = SHA256RSA
- 서명 해시 알고리즘 = SHA256
- 유효 기간 = 유효한 지난 날짜
- Valid To = 유효한 미래 날짜
- 공개 키 = RSA 2048bits (최소) 또는 4096bits
- CRL 배포 지점 = crl 파일의 URL
- Subject CN = URL은 와일드 카드 인증서 (예: example.contoso.com 또는 *contoso.com* ) 일 수 있습니다.*
- Subject (C) ountry = defined (예: US)
- 주체 (OU) Org Unit = defined (예: Contoso Labs)
- Subject (O) rganization = defined (예: Contoso Inc)

키 파일

- CSR을 만들 때 생성 되는 키 파일
- RSA 2048bits (최소) 또는 4096bits

인증서 체인

- CA에서 제공 하는 중간 인증서 파일 (있는 경우)
- 서버 인증서를 발급 한 CA 인증서가 먼저 파일에 있고 그 뒤에 루트까지 있는 인증서가 있어야 합니다. 
- 모음 특성을 포함할 수 있습니다.

암호

- 1 개 키 지원 됨
- 인증서를 가져올 때 설정

다른 매개 변수를 사용 하는 인증서는 작동 하지만 Microsoft에서 지원할 수 없습니다.

#### <a name="encryption-key-artifacts"></a>암호화 키 아티팩트

**. pem – 인증서 컨테이너 파일**

이름은 Privacy Enhanced Mail (PEM)에서 가져온 것으로, 보안 전자 메일에 대 한 기록 방법 이지만 사용 하는 컨테이너 형식은 그대로 사용 되며 x509 ASN 키의 base64 변환입니다.  

Rfc 1424 1421에 정의 됨: 공용 인증서 (예: Apache 설치 및 CA 인증서 파일/etc/ssl/certs)만 포함할 수 있는 컨테이너 형식 이거나 공개 키, 개인 키 및 루트 인증서를 포함 한 전체 인증서 체인을 포함할 수 있습니다.  

PKCS10 형식이 PEM으로 변환 될 수 있으므로 CSR을 인코딩할 수도 있습니다.

**. cert. .cer – 인증서 컨테이너 파일**

확장명이 다른 파일 (또는 거의 사용 되지 않는 파일 형식)입니다. 이 파일은 Windows 탐색기에서 인증서로 인식 됩니다. Windows 탐색기에서 pem 파일을 인식할 수 없습니다.

**. key – 개인 키 파일**

키 파일은 PEM 파일과 동일한 "형식" 이지만 확장명이 다릅니다.
##### <a name="use-cli-commands-to-deploy-certificates"></a>CLI 명령을 사용 하 여 인증서 배포

Cyberx- *xsense-인증서-가져오기* CLI 명령을 사용 하 여 인증서를 가져옵니다. 이 도구를 사용 하려면 winscp 또는 wget과 같은 도구를 사용 하 여 인증서 파일을 장치에 업로드 해야 합니다.

이 명령은 다음 입력 플래그를 지원 합니다.

-h 명령줄 도움말 구문을 표시 합니다.

--인증서 파일에 대 한 crt 경로 (CRT 확장)

--key *. key file, 키 길이는 최소 2048 비트 여야 합니다.

--인증서 체인 파일의 체인 경로 (옵션)

--인증서를 암호화 하는 데 사용 되는 암호를 전달 합니다 (선택 사항).

--passphrase-set Default = False, 사용 되지 않습니다. 이전 인증서와 함께 제공 된 이전 암호를 사용 하려면 TRUE로 설정 합니다 (선택 사항).

CLI 명령을 사용 하는 경우:

- 인증서 파일을 어플라이언스에서 읽을 수 있는지 확인 합니다.

- 인증서의 도메인 이름과 IP가 IT 부서에서 계획 한 구성과 일치 하는지 확인 합니다.


## <a name="connect-a-sensor-to-the-management-console"></a>관리 콘솔에 센서 연결

이 섹션에서는 센서와 온-프레미스 관리 콘솔 간의 연결을 확인 하는 방법을 설명 합니다. Gapped 네트워크에서 작업 중 이며 센서에서 자산 및 경고 정보를 관리 콘솔로 보내려는 경우이 작업을 수행 해야 합니다. 이 연결을 통해 관리 콘솔에서 센서에 시스템 설정을 푸시하고 센서에 대해 다른 관리 작업을 수행할 수도 있습니다.

연결 하려면:

1. 온-프레미스 관리 콘솔에 로그인 합니다.

2. **시스템 설정** 을 선택 합니다.

3. **센서 설정 – 연결 문자열** 섹션에서 자동으로 생성 된 연결 문자열을 복사 합니다.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="이 화면에서 연결 문자열을 복사 합니다."::: 

4. 센서 콘솔에 로그인 합니다.

5. 왼쪽 창에서 **시스템 설정** 을 선택 합니다.

6. **관리 콘솔 연결** 을 선택 합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="관리 콘솔 연결 대화 상자의 스크린샷":::

7. **연결 문자열 상자에** 연결 문자열을 붙여넣고 **연결** 을 선택 합니다.

8. 온-프레미스 관리 콘솔의 **사이트 관리** 창에서 센서를 영역에 할당 합니다.

## <a name="change-the-name-of-a-sensor"></a>센서 이름 변경

센서 콘솔의 이름을 변경할 수 있습니다. 새 이름이 콘솔 웹 브라우저, 다양 한 콘솔 창 및 로그 문제 해결에 표시 됩니다.

센서 이름을 변경 하는 프로세스는 로컬로 연결 된 센서 및 클라우드 연결 센서에 따라 다릅니다. 기본 이름은 **센서** 입니다.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>로컬로 연결 된 센서의 이름 변경

이름을 변경하는 방법은 다음과 같습니다.

1. 콘솔 왼쪽 창의 맨 아래에서 현재 센서 레이블을 선택 합니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="센서 레이블을 표시 하는 스크린샷":::

1. **센서 이름 편집** 대화 상자에서 이름을 입력 합니다.

1. **저장** 을 선택합니다. 새 이름이 적용 됩니다.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>클라우드 연결 센서의 이름 변경

센서가 클라우드 연결 센서로 등록 된 경우 센서 이름은 등록 하는 동안 할당 된 이름으로 정의 됩니다. 이 이름은 처음으로 로그인 할 때 업로드 한 활성화 파일에 포함 됩니다. 센서의 이름을 변경 하려면 새 활성화 파일을 업로드 해야 합니다.

이름을 변경하는 방법은 다음과 같습니다.

1. IoT 용 Azure Defender 포털에서 **센서 관리** 페이지로 이동 합니다.

1. 센서 **관리** 창에서 센서를 삭제 합니다.

1. 새 이름으로 등록 합니다.

1. 새 활성화 파일을 다운로드 합니다.

1. 센서에 로그인 하 고 새 활성화 파일을 업로드 합니다.

## <a name="update-the-sensor-network-configuration"></a>센서 네트워크 구성 업데이트

센서 네트워크 구성은 센서 설치 중에 정의 되었습니다. 구성 매개 변수를 변경할 수 있습니다. 프록시 구성을 설정할 수도 있습니다.

새 IP 주소를 만드는 경우 다시 로그인 해야 할 수 있습니다.

구성을 변경 하려면:

1. 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

2. **시스템 설정** 창에서 **네트워크** 를 선택 합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="네트워크 설정을 구성 합니다.":::

3. 다음과 같이 매개 변수를 설정 합니다.

    | 매개 변수 | 설명 |
    |--|--|
    | IP 주소 | 센서 IP 주소 |
    | 서브넷 마스크 | 마스크 주소 |
    | 기본 게이트웨이 | 기본 게이트웨이 주소 |
    | DNS | DNS 서버 주소 |
    | 호스트 이름 | 센서 호스트 이름 |
    | Proxy (프록시) | 프록시 호스트 및 포트 이름 |

4. **저장** 을 선택합니다.

## <a name="synchronize-time-zones-on-the-sensor"></a>센서의 표준 시간대를 동기화 합니다.

모든 사용자에 게 동일한 시간과 지역이 표시 되도록 센서의 시간 및 지역을 구성할 수 있습니다.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="시간과 지역을 구성 합니다.":::

| 매개 변수 | 설명 |
|--|--|
| 표준 시간대 | 다음에 대 한 표준 시간대 정의:<br />-경고<br />-추세 및 통계 위젯<br />-데이터 마이닝 보고서<br />   -위험 평가 보고서<br />-공격 벡터 |
| 날짜 형식 | 다음 서식 옵션 중 하나를 선택 합니다.<br />-dd/MM/yyyy HH: MM: ss<br />-MM/dd/yyyy HH: MM: ss<br />-yyyy/MM/dd HH: MM: ss |
| 날짜 및 시간 | 현재 날짜와 현지 시간을 선택한 형식으로 표시 합니다.<br />예를 들어 실제 위치가 아메리카와 뉴욕에 있지만 표준 시간대가 유럽 및 베를린로 설정 된 경우 시간은 베를린 현지 시간에 따라 표시 됩니다. |

센서 시간을 구성 하려면:

1. 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

2. **시스템 설정** 창에서 **시간 & 지역** 을 선택 합니다.

3. 매개 변수를 설정 하 고 **저장** 을 선택 합니다.

## <a name="set-up-backup-and-restore-files"></a>백업 설정 및 파일 복원

시스템 백업은 매일 오전 3:00에 자동으로 수행 됩니다. 데이터는 센서의 다른 디스크에 저장 됩니다. 기본 위치는 `/var/cyberx/backups`입니다.

내부 네트워크에이 파일을 자동으로 전송할 수 있습니다.

> [!NOTE]
> - 백업 및 복원 절차는 동일한 버전 에서만 수행할 수 있습니다.
> - 일부 아키텍처에서는 백업을 사용할 수 없습니다. 파일에서이 기능을 사용 하도록 설정할 수 있습니다 `/var/cyberx/properties/backup.properties` .

온-프레미스 관리 콘솔을 사용 하 여 센서를 제어할 때 센서의 백업 일정을 사용 하 여 이러한 백업을 수집 하 고 관리 콘솔 또는 외부 백업 서버에 저장할 수 있습니다.

**백업 대상:** 구성 및 데이터.

**백업 되지 않는 항목:** PCAP 파일 및 로그. PCAPs 및 로그를 수동으로 백업 하 고 복원할 수 있습니다.

센서 백업 파일은 자동으로 이름이로 지정 됩니다 `<sensor name>-backup-version-<version>-<date>.tar` . 예제는 `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`입니다.

백업을 구성 하려면:

- 관리 계정에 로그인 하 고을 입력 `$ sudo cyberx-xsense-system-backup` 합니다.

최신 백업 파일을 복원 하려면:

- 관리 계정에 로그인 하 고을 입력 `$ sudo cyberx-xsense-system-restore` 합니다.

백업을 외부 SMB 서버에 저장 하려면 다음을 수행 합니다.

1. 외부 SMB 서버에 공유 폴더를 만듭니다.

    SMB 서버에 액세스 하는 데 필요한 폴더 경로, 사용자 이름 및 암호를 가져옵니다.

2. 센서에서 백업용 디렉터리를 만듭니다.

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. 편집 `fstab`: 

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. SMB 서버에 대해 공유할 자격 증명을 편집 하 고 만듭니다.

    `sudo nano /etc/samba/user` 

5. 추가:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. 디렉터리를 탑재 합니다.

    `sudo mount -a`

7. IoT 센서에 대 한 Defender의 공유 폴더에 백업 디렉터리를 구성 합니다.  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>센서 복원

CLI를 사용 하 여 센서 콘솔에서 백업을 복원할 수 있습니다.

**콘솔에서 복원 하려면:**

- 센서의 **시스템 설정** 창에서 **이미지 복원** 을 선택 합니다.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="단추를 선택 하 여 이미지를 복원 합니다.":::

콘솔에 복원 오류가 표시 됩니다.

**CLI를 사용 하 여 복원 하려면:**

- 관리 계정에 로그인 하 고을 입력 `$ sudo cyberx-management-system-restore` 합니다.

## <a name="update-a-standalone-sensor-version"></a>독립 실행형 센서 버전 업데이트

다음 절차에서는 센서 콘솔을 사용 하 여 독립 실행형 센서를 업데이트 하는 방법을 설명 합니다. 업데이트 프로세스는 30 분 정도 걸립니다.

1. [Azure 포털](https://portal.azure.com/)로 이동합니다.

2. IoT 용 Defender로 이동 합니다.

3. **업데이트** 페이지로 이동 합니다.

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="IoT 용 Defender의 업데이트 페이지 스크린샷":::

4. **센서** 섹션에서 **다운로드** 를 선택 하 고 파일을 저장 합니다.

5. 센서 콘솔의 사이드바에서 **시스템 설정** 을 선택 합니다.

6. **버전 업그레이드** 창에서 **업그레이드** 를 선택 합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="업그레이드 창의 스크린샷":::

7. IoT 용 Defender **업데이트** 페이지에서 다운로드 한 파일을 선택 합니다.

8. 업데이트 프로세스가 시작 됩니다 .이 기간 동안에는 시스템을 두 번 다시 부팅 합니다. 업데이트 프로세스가 완료 되기 전에 첫 번째 다시 부팅 후에는 로그인 창이 열리고 시스템이 열립니다. 로그인 한 후 업그레이드 버전은 사이드바의 왼쪽 아래에 나타납니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="로그인 한 후 표시 되는 업그레이드 버전의 스크린샷":::

## <a name="forward-sensor-failure-alerts"></a>센서 오류 전달 경고 

다음에 대 한 세부 정보를 제공 하기 위해 타사에 경고를 전달할 수 있습니다.

- 연결 끊김 센서

- 원격 백업 실패

이 정보는 시스템 알림에 대 한 전달 규칙을 만들 때 전송 됩니다.

> [!NOTE]
> 관리자는 시스템 알림을 보낼 수 있습니다.

알림을 보내려면:

1. 온-프레미스 관리 콘솔에 로그인 합니다.
1. 측면 메뉴에서 **전달** 을 선택 합니다.
1. 전달 규칙을 만듭니다.
1. **보고서 시스템 알림** 을 선택 합니다.

전달 규칙에 대 한 자세한 내용은 [전달 경고 정보](how-to-forward-alert-information-to-partners.md)를 참조 하세요.

## <a name="adjust-system-properties"></a>시스템 속성 조정

시스템 속성은 센서의 다양 한 작업 및 설정을 제어 합니다. 이러한 항목을 편집 하거나 수정 하면 센서 콘솔의 작동이 손상 될 수 있습니다.

설정을 변경 하기 전에 [Microsoft 지원](https://support.microsoft.com/) 를 참조 하세요.

시스템 속성에 액세스 하려면:

1. 온-프레미스 관리 콘솔 또는 센서에 로그인 합니다.

2. **시스템 설정** 을 선택 합니다.

3. **일반** 섹션에서 **시스템 속성** 을 선택 합니다.

## <a name="see-also"></a>추가 정보

[위협 인텔리전스 연구 및 패키지](how-to-work-with-threat-intelligence-packages.md)

[관리 콘솔에서 센서 관리](how-to-manage-sensors-from-the-on-premises-management-console.md)
