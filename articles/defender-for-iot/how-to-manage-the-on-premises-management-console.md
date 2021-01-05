---
title: 온-프레미스 관리 콘솔 관리
description: 백업 및 복원과 같은 온-프레미스 관리 콘솔 옵션, 호스트 이름 정의 및 센서에 대 한 프록시 설정에 대해 알아봅니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 7bbac0d8593d47c3162a8ea43e928343a88f2de4
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861437"
---
# <a name="manage-the-on-premises-management-console"></a>온-프레미스 관리 콘솔 관리

이 문서에서는 백업 및 복원, 위원회 장치 활성화 파일 다운로드, 인증서 업데이트, 센서에 프록시 설정 등과 같은 온-프레미스 관리 콘솔 옵션에 대해 설명 합니다.

Azure Portal에서 온-프레미스 관리 콘솔을 등록 합니다.

## <a name="upload-an-activation-file"></a>활성화 파일 업로드

처음 로그인 하면 온-프레미스 관리 콘솔에 대 한 정품 인증 파일이 다운로드 됩니다. 이 파일에는 온 보 딩 프로세스 중에 정의 된 커밋된 집계 장치가 포함 되어 있습니다. 이 목록에는 여러 구독과 연결 된 센서가 포함 됩니다.

초기 활성화 후 모니터링 되는 장치 수는 온 보 딩 중에 정의 된 커밋된 장치 수를 초과할 수 있습니다. 예를 들어 관리 콘솔에 추가 센서를 연결 하는 경우이 이벤트가 발생할 수 있습니다. 모니터링 되는 장치 수와 커밋된 장치 수 간에 불일치가 있는 경우 관리 콘솔에 경고가 표시 됩니다. 이 이벤트가 발생 하는 경우 새 활성화 파일을 업로드 해야 합니다.

활성화 파일을 업로드 하려면:

1. IoT 용 Azure Defender **가격 책정** 페이지로 이동 합니다.
1. **관리 콘솔 탭의 활성화 파일 다운로드** 를 선택 합니다. 활성화 파일이 다운로드 됩니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="활성화 파일을 다운로드 합니다.":::

1. 관리 콘솔에서 **시스템 설정** 을 선택 합니다.
1. **활성화** 를 선택 합니다.
1. **파일** 선택을 선택 하 고 저장 한 파일을 선택 합니다.

## <a name="manage-certificates"></a>인증서 관리

온-프레미스 관리 콘솔을 설치한 후 로컬 자체 서명 된 인증서가 생성 되어 관리 콘솔의 웹 응용 프로그램에 액세스 하는 데 사용 됩니다. 관리자 사용자가 관리 콘솔에 처음으로 로그인 하면 SSL/TLS 인증서를 제공 하 라는 메시지가 표시 됩니다. 처음 설치 하는 방법에 대 한 자세한 내용은 [온-프레미스 관리 콘솔 활성화 및 설정](how-to-activate-and-set-up-your-on-premises-management-console.md)을 참조 하세요.

다음 섹션에서는 인증서 업데이트, 인증서 CLI 명령 작업, 지원 되는 인증서 및 인증서 매개 변수에 대 한 정보를 제공 합니다.

### <a name="about-certificates"></a>인증서 정보

IoT 용 Azure Defender는 SSL 및 TLS 인증서를 사용 하 여 다음을 수행 합니다.

- CA 서명 인증서를 업로드 하 여 조직에서 요청 하는 특정 인증서 및 암호화 요구 사항을 충족 합니다.

- 관리 콘솔과 연결 된 센서 간의 유효성을 검사 하 고 관리 콘솔과 고가용성 관리 콘솔 사이에서 유효성을 검사 합니다. 유효성 검사는 인증서 해지 목록 및 인증서 만료 날짜에 대해 평가 됩니다. *유효성 검사에 실패 하면 관리 콘솔과 센서 간의 통신이 중단 되 고 콘솔에 유효성 검사 오류가 나타납니다.* 이 옵션은 설치 후 기본적으로 사용 하도록 설정 됩니다.

타사 전달 규칙의 유효성은 검사 되지 않습니다. 예제는 SYSLOG, Splunk 또는 ServiceNow로 전송 되는 경고 정보입니다. Active Directory와 통신 합니다.

### <a name="update-certificates"></a>인증서 업데이트

온-프레미스 관리 콘솔의 관리자가 인증서를 업데이트할 수 있습니다.

인증서를 업데이트 하려면:  

1. **시스템 설정** 을 선택 합니다.
1. **SSL/TLS 인증서** 를 선택 합니다.
1. 인증서를 삭제 하거나 편집 하 고 새 인증서를 추가 합니다.
   
   - 인증서 이름을 추가 합니다.
   - CRT 파일 및 키 파일을 업로드 하 고 암호를 입력 합니다.
   - 필요한 경우 PEM 파일을 업로드 합니다.

유효성 검사 설정을 변경 하려면:

1. **인증서 유효성 검사 사용** 토글을 설정 하거나 해제 합니다.
1. **저장** 을 선택합니다.

이 옵션을 사용 하도록 설정 하 고 유효성 검사에 실패 하면 관리 콘솔과 센서 간의 통신이 중단 되 고 콘솔에 유효성 검사 오류가 나타납니다.

### <a name="certificate-support"></a>인증서 지원

지원 되는 인증서는 다음과 같습니다.

- 개인 및 엔터프라이즈 키 인프라 (사설 PKI) 
- 공개 키 인프라 (공용 PKI) 
- 어플라이언스에서 로컬로 생성 (로컬에서 자체 서명 됨) 

  > [!IMPORTANT]
  > 자체 서명 된 인증서를 사용 하지 않는 것이 좋습니다. 이 연결은 안전 하지 않으며 테스트 환경에만 사용 해야 합니다. 인증서 소유자의 유효성을 검사할 수 없으며 시스템의 보안을 유지할 수 없습니다. 자체 서명 된 인증서는 프로덕션 네트워크에 사용해 서는 안 됩니다.  

지원되는 매개 변수는 다음과 같습니다. 

**인증서 CRT**

- 도메인 이름에 대 한 기본 인증서 파일
- 서명 알고리즘 = SHA256RSA
- 서명 해시 알고리즘 = SHA256
- 유효 기간 = 유효한 지난 날짜
- Valid To = 유효한 미래 날짜
- 공개 키 = RSA 2048 비트 (최소) 또는 4096 비트
- CRL 배포 지점 = crl 파일의 URL
- Subject CN = URL은 와일드 카드 인증서 일 수 있습니다. 예: www.contoso.com 또는 \* . contoso.com
- Subject (C) ountry = defined (예: US)
- 주체 (OU) Org Unit = defined; 예: Contoso Labs
- Subject (O) rganization = defined; 예: Contoso Inc

**키 파일**

- CSR을 만들 때 생성 된 키 파일입니다.
- RSA 2048 비트 (최소) 또는 4096 비트

**인증서 체인**

- CA에서 제공 하는 중간 인증서 파일 (있는 경우)입니다.
- 서버 인증서를 발급 한 CA 인증서가 먼저 파일에 있고 그 뒤에 루트까지 있는 인증서가 있어야 합니다. 
- 체인에는 모음 특성을 포함할 수 있습니다.

**전달**

- 하나의 키가 지원 됩니다.
- 인증서를 가져올 때 설정 합니다.

다른 매개 변수를 사용 하는 인증서는 작동할 수 있지만 Microsoft는 지원 하지 않습니다.

#### <a name="encryption-key-artifacts"></a>암호화 키 아티팩트

**. pem: 인증서 컨테이너 파일**

이름은 보안 전자 메일에 대 한 기록 메서드인 PEM (Privacy Enhanced Mail)입니다. 컨테이너 형식은 x509 ASN 키의 Base64 변환입니다.  

이 파일은 Rfc 1421 1424에 정의 되어 있습니다. 공용 인증서 (예: Apache 설치, CA 인증서 파일 및 기타 SSL 인증서)만 포함 될 수 있는 컨테이너 형식입니다. 또는 공개 키, 개인 키 및 루트 인증서를 포함 하 여 전체 인증서 체인을 포함할 수 있습니다.  

PKCS10 형식을 PEM으로 변환할 수 있기 때문에 CSR을 인코딩할 수도 있습니다.

**. cert. .cer. crt: 인증서 컨테이너 파일**

이 파일은 확장명이 다른 파일 형식 파일입니다. Windows 파일 탐색기는 인증서로 인식 합니다. 파일 탐색기는 pem 파일을 인식 하지 못합니다.

**. 키: 개인 키 파일**

키 파일의 형식은 PEM 파일과 동일 하지만 확장명은 다릅니다. 

#### <a name="cli-commands"></a>CLI 명령

`cyberx-xsense-certificate-import`CLI 명령을 사용 하 여 인증서를 가져옵니다. 이 도구를 사용 하려면 winscp 또는 wget과 같은 도구를 사용 하 여 인증서 파일을 장치에 업로드 해야 합니다.

이 명령은 다음 입력 플래그를 지원 합니다.

- `-h`: 명령줄 도움말 구문을 표시 합니다.

- `--crt`: 인증서 파일 (.crt 확장명)의 경로입니다.

- `--key`:  \* . key 파일. 키 길이는 2048 비트 이상 이어야 합니다.

- `--chain`: 인증서 체인 파일의 경로입니다 (선택 사항).

- `--pass`: 인증서를 암호화 하는 데 사용 되는 암호입니다 (선택 사항).

- `--passphrase-set`: 기본값은 `False` 사용 되지 않습니다. `True`이전 인증서와 함께 제공 된 이전 암호를 사용 하려면로 설정 합니다 (옵션).

CLI 명령을 사용 하는 경우:

- 인증서 파일을 어플라이언스에서 읽을 수 있는지 확인 합니다.

- 인증서의 도메인 이름과 IP가 IT 부서가 계획 한 구성과 일치 하는지 확인 합니다.

## <a name="define-backup-and-restore-settings"></a>백업 및 복원 설정 정의

온-프레미스 관리 콘솔 시스템 백업은 매일 자동으로 수행 됩니다. 데이터가 다른 디스크에 저장 됩니다. 기본 위치는 `/var/cyberx/backups`입니다. 

내부 네트워크에이 파일을 자동으로 전송할 수 있습니다. 

> [!NOTE]
> 동일한 버전 에서만 백업 및 복원 절차를 수행할 수 있습니다. 

온-프레미스 관리 콘솔 컴퓨터를 백업 하려면 다음을 수행 합니다. 

- 관리 계정에 로그인 하 고을 입력 `sudo cyberx-management-backup -full` 합니다.

최신 백업 파일을 복원 하려면:

- 관리 계정에 로그인 하 고을 입력 `$ sudo cyberx-management-system-restore` 합니다.

백업을 외부 SMB 서버에 저장 하려면 다음을 수행 합니다.

1. 외부 SMB 서버에 공유 폴더를 만듭니다.  

   SMB 서버에 액세스 하는 데 필요한 폴더 경로, 사용자 이름 및 암호를 가져옵니다. 

2. IoT 용 Defender에서 백업용 디렉터리를 만듭니다.

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Fstab 편집:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. SMB 서버가 공유할 자격 증명을 편집 하거나 만듭니다. 

   - `sudo nano /etc/samba/user` 

5. 추가: 

   - `username=<user name>`

   - `password=<password>` 

6. 디렉터리를 탑재 합니다. 

   - `sudo mount -a` 

7. IoT 온-프레미스 관리 콘솔의 Defender에서 공유 폴더에 백업 디렉터리를 구성 합니다.  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>호스트 이름 편집

조직 DNS 서버에 구성 된 관리 콘솔의 호스트 이름을 편집 하려면 다음을 수행 합니다.

1. 관리 콘솔의 왼쪽 창에서 **시스템 설정** 을 선택 합니다.  

2. 콘솔의 네트워킹 섹션에서 **네트워크** 를 선택 합니다. 

3. 조직 DNS 서버에 구성 된 호스트 이름을 입력 합니다. 

4. **저장** 을 선택합니다.

## <a name="define-vlan-names"></a>VLAN 이름 정의

VLAN 이름은 센서와 관리 콘솔 간에 동기화 되지 않습니다. 구성 요소에 동일한 이름을 정의 해야 합니다.

네트워킹 영역에서 **vlan** 을 선택 하 고 검색 된 vlan id에 이름을 추가 합니다. 그런 다음 **저장** 을 선택합니다.

## <a name="define-a-proxy-to-sensors"></a>센서에 대 한 프록시 정의

사용자가 센서에 직접 로그인 하지 못하도록 하 여 시스템 보안을 강화 합니다. 대신, 사용자가 단일 방화벽 규칙을 사용 하 여 온-프레미스 관리 콘솔에서 센서에 액세스할 수 있도록 프록시 터널링을 사용 합니다. 이러한 향상 된 기능을 통해 센서를 초과 하는 네트워크 환경에 대 한 무단 액세스 가능성을 좁힐 수 있습니다.

센서에 직접 연결 되지 않는 환경에서 프록시를 사용 합니다.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="정의.":::

다음 절차에서는 온-프레미스 관리 콘솔에 센서를 연결 하 고 해당 콘솔에서 터널링을 사용 하도록 설정 합니다.

1. 관리 자격 증명을 사용 하 여 온-프레미스 관리 콘솔 어플라이언스 CLI에 로그인 합니다.

2. `sudo cyberx-management-tunnel-enable`을 입력 하 고 **Enter 키** 를 선택 합니다.

4. `--port 10000`을 입력 하 고 **Enter 키** 를 선택 합니다.

## <a name="adjust-system-properties"></a>시스템 속성 조정

시스템 속성은 관리 콘솔에서 다양 한 작업 및 설정을 제어 합니다. 이러한 항목을 편집 하거나 수정 하면 관리 콘솔의 작업이 손상 될 수 있습니다. 설정을 변경 하기 전에 [Microsoft 지원](https://support.microsoft.com) 를 참조 하세요.

시스템 속성에 액세스 하려면: 

1. 온-프레미스 관리 콘솔 또는 센서에 로그인 합니다.

2. **시스템 설정** 을 선택 합니다.

3. **일반** 섹션에서 **시스템 속성** 을 선택 합니다.

## <a name="change-the-name-of-the-on-premises-management-console"></a>온-프레미스 관리 콘솔의 이름 변경

온-프레미스 관리 콘솔의 이름을 변경할 수 있습니다. 새 이름이 콘솔 웹 브라우저, 다양 한 콘솔 창 및 로그 문제 해결에 나타납니다. 기본 이름은 **관리 콘솔** 입니다.

이름을 변경하는 방법은 다음과 같습니다.

1. 왼쪽 창의 맨 아래에서 현재 이름을 선택 합니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="온-프레미스 관리 콘솔 버전의 스크린샷":::

2. **관리 콘솔 구성 편집** 대화 상자에서 새 이름을 입력 합니다. 이름은 25 자 보다 길 수 없습니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="IoT 플랫폼 구성 용 Defender를 편집 하는 스크린샷":::

3. **저장** 을 선택합니다. 새 이름이 적용 됩니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="콘솔의 변경 된 이름을 보여 주는 스크린샷":::

## <a name="password-recovery"></a>암호 복구

온-프레미스 관리 콘솔에 대 한 암호 복구는 장치가 연결 된 구독과 연결 됩니다. 장치가 연결 된 구독을 모를 경우에는 암호를 복구할 수 없습니다.

암호를 다시 설정하려면:

1. 온-프레미스 관리 콘솔의 로그인 페이지로 이동 합니다.
1. **암호 복구** 를 선택 합니다.
1. 고유 식별자를 복사 합니다.
1. IoT **사이트 및 센서** 용 Defender 페이지로 이동 하 여 **암호 복구** 탭을 선택 합니다.
1. 고유 식별자를 입력 하 고 **복구** 를 선택 합니다. 활성화 파일이 다운로드 됩니다.
1. **암호 복구** 페이지로 이동 하 여 활성화 파일을 업로드 합니다.
1. **다음** 을 선택합니다.
 
   이제 사용자 이름과 새 시스템 생성 암호가 제공 됩니다.

> [!NOTE]
> 센서는 원래 연결 된 구독에 연결 됩니다. 연결 된 구독과 동일한 구독을 사용 해야만 암호를 복구할 수 있습니다.

## <a name="update-the-software-version"></a>소프트웨어 버전 업데이트

다음 절차에서는 온-프레미스 관리 콘솔 소프트웨어 버전을 업데이트 하는 방법을 설명 합니다. 업데이트 프로세스는 30 분 정도 걸립니다.

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

1. IoT 용 Defender로 이동 합니다.

1. **업데이트** 페이지로 이동 합니다.

1. 온-프레미스 관리 콘솔 섹션에서 버전을 선택 합니다.

1. **다운로드** 를 선택하고 파일을 저장합니다.

1. 온-프레미스 관리 콘솔에 로그인 하 고 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

1. **버전 업데이트** 창에서 **업데이트** 를 선택 합니다.

1. IoT 용 Defender **업데이트** 페이지에서 다운로드 한 파일을 선택 합니다.

## <a name="see-also"></a>참조

[관리 콘솔에서 센서 관리](how-to-manage-sensors-from-the-on-premises-management-console.md)

[개별 센서 관리](how-to-manage-individual-sensors.md)
