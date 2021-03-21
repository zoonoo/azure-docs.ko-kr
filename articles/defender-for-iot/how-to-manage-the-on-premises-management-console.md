---
title: 온-프레미스 관리 콘솔 관리
description: 백업 및 복원과 같은 온-프레미스 관리 콘솔 옵션, 호스트 이름 정의 및 센서에 대 한 프록시 설정에 대해 알아봅니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: f3c9f8f78f17153c3d2eb7b014cf616253b3c0c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618256"
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

- 관리 콘솔과 연결 된 센서 간의 유효성을 검사 하 고 관리 콘솔과 고가용성 관리 콘솔 사이에서 유효성을 검사 합니다. 유효성 검사는 인증서 해지 목록 및 인증서 만료 날짜에 대해 평가 됩니다. *유효성 검사에 실패 하면 관리 콘솔과 센서 간의 통신이 중단 되 고 콘솔에 유효성 검사 오류가 나타납니다*. 이 옵션은 설치 후 기본적으로 사용 하도록 설정 됩니다.

타사 전달 규칙의 유효성은 검사 되지 않습니다. 예제는 SYSLOG, Splunk 또는 ServiceNow로 전송 되는 경고 정보입니다. Active Directory와 통신 합니다.

#### <a name="ssl-certificates"></a>SSL 인증서

IoT 센서 및 온-프레미스 관리 콘솔의 Defender는 SSL을 사용 하 고 다음 기능에는 TLS 인증서를 사용 합니다. 

 - 사용자와 어플라이언스의 웹 콘솔 간에 보안 통신을 설정 합니다. 
 
 - 센서 및 온-프레미스 관리 콘솔에서 REST API에 대 한 통신을 보호 합니다.
 
 - 센서와 온-프레미스 관리 콘솔 간의 통신을 보호 합니다. 

설치가 완료 되 면 어플라이언스는 웹 콘솔에 대 한 사전 액세스를 허용 하는 로컬 자체 서명 된 인증서를 생성 합니다. 명령줄 도구를 사용 하 여 엔터프라이즈 SSL 및 TLS 인증서를 설치할 수 있습니다 [`cyberx-xsense-certificate-import`](#cli-commands) . 

 > [!NOTE]
 > 기기가 세션의 클라이언트 및 개시자 인 통합 및 전달 규칙의 경우 특정 인증서가 사용 되 고 시스템 인증서와 관련 되지 않습니다.  
 >
 >이러한 경우 인증서는 일반적으로 서버에서 수신 되거나, 통합을 설정 하기 위해 특정 인증서가 제공 되는 비대칭 암호화를 사용 합니다. 

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
  > 자체 서명 된 인증서를 사용 하지 않는 것이 좋습니다. 이 유형의 연결은 안전 하지 않으며 테스트 환경에만 사용 해야 합니다. 에서 인증서 소유자의 유효성을 검사할 수 없고 시스템의 보안을 유지할 수 없으므로 프로덕션 네트워크에는 자체 서명 된 인증서를 사용 하면 안 됩니다.

### <a name="supported-ssl-certificates"></a>지원 되는 SSL 인증서 

지원되는 매개 변수는 다음과 같습니다. 

**인증서 CRT**

- 도메인 이름에 대 한 기본 인증서 파일

- 서명 알고리즘 = SHA256RSA
- 서명 해시 알고리즘 = SHA256
- 유효 기간 = 유효한 지난 날짜
- Valid To = 유효한 미래 날짜
- 공개 키 = RSA 2048 비트 (최소) 또는 4096 비트
- CRL 배포 지점 = crl 파일의 URL
- Subject CN = URL은 와일드 카드 인증서 일 수 있습니다. 예를 들면, 수신부 <span> . com 또는 *. contoso. <span> c
- Subject (C) ountry = defined (예: US)
- 주체 (OU) Org Unit = defined; 예: Contoso Labs
- Subject (O) rganization = defined; 예: Contoso Inc

**키 파일**

- CSR을 만들 때 생성 된 키 파일입니다.

- RSA 2048 비트 (최소) 또는 4096 비트

 > [!Note]
 > 키 길이 4096bits 사용:
 > - 각 연결이 시작 될 때 SSL 핸드셰이크가 느려집니다.  
 > - 핸드셰이크 중 CPU 사용량이 증가 합니다. 

**인증서 체인**

- CA에서 제공 하는 중간 인증서 파일 (있는 경우)입니다.

- 서버 인증서를 발급 한 CA 인증서가 먼저 파일에 있고 그 뒤에 루트까지 있는 인증서가 있어야 합니다. 
- 체인에는 모음 특성을 포함할 수 있습니다.

**암호**

- 하나의 키가 지원 됩니다.

- 인증서를 가져올 때 설정 합니다.

다른 매개 변수를 사용 하는 인증서는 작동할 수 있지만 Microsoft는 지원 하지 않습니다.

#### <a name="encryption-key-artifacts"></a>암호화 키 아티팩트

**. pem: 인증서 컨테이너 파일**

PEM (Privacy Enhanced Mail) 파일은 전자 메일을 보호 하는 데 사용 되는 일반 파일 형식입니다. 오늘날 PEM 파일은 인증서와 함께 사용 되며 x509 ASN1 키를 사용 합니다.  

컨테이너 파일은 공용 인증서만 포함할 수 있는 컨테이너 형식인 Rfc 1421에서 1424로 정의 됩니다. 예를 들어 Apache 설치, CA 인증서, 파일, SSL 또는 인증서가 있습니다. 여기에는 공개 키, 개인 키 및 루트 인증서를 포함 한 전체 인증서 체인이 포함 될 수 있습니다.  

또한 CSR을 PEM으로 변환할 수 있는 PKCS10 형식으로 인코딩할 수 있습니다.

**. cert. .cer. crt: 인증서 컨테이너 파일**

`.pem` `.der` 확장명이 다른 이거나 형식이 지정 된 파일입니다. 파일은 Windows 탐색기에서 인증서로 인식 됩니다. `.pem`   Windows 탐색기에서 파일을 인식할 수 없습니다.

**. 키: 개인 키 파일**

키 파일은 PEM 파일과 동일한 형식 이지만 확장명이 다릅니다. 

#### <a name="additional-commonly-available-key-artifacts"></a>일반적으로 사용 가능한 추가 키 아티팩트

**csr-인증서 서명 요청** 입니다.  

이 파일은 인증 기관에 전송 하는 데 사용 됩니다. 실제 형식은 RFC 2986에 정의 된 PKCS10, 요청 된 인증서의 일부 또는 모든 주요 세부 정보를 포함할 수 있습니다. 예를 들어 제목, 조직 및 상태입니다. CA가 서명 하는 인증서의 공개 키 이며 반환 되는 인증서를 받습니다.  

반환 된 인증서는 공개 키를 포함 하지만 개인 키가 포함 되지 않은 공용 인증서입니다. 

.pa. p. p. **p12 – password 컨테이너** 

원래 PKCS (Public-Key 암호화 표준)에서 RSA에 의해 정의 된 12 변형은 원래 Microsoft에서 향상 되었으며 나중에 RFC 7292로 제출 되었습니다.  

이 컨테이너 형식에는 공용 및 개인 인증서 쌍을 모두 포함 하는 암호가 필요 합니다. `.pem`   파일과 달리이 컨테이너는 완전히 암호화 됩니다.  

OpenSSL를 사용 하 여 파일을 `.pem`   공개 키와 개인 키가 모두 포함 된 파일로 전환할 수 있습니다. `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**der – 이진 인코딩 PEM**.

이진수에서 ASN 구문을 인코딩하는 방법은 `.pem`   Base64 인코딩 파일인 파일을 통하는 것입니다. `.der` 

OpenSSL은 이러한 파일을로 변환할 수 `.pem` 있습니다.  `openssl x509 -inform der -in to-convert.der -out converted.pem`  

Windows에서 이러한 파일을 인증서 파일로 인식 합니다. 기본적으로 Windows는 `.der` 다른 확장명을 사용 하 여 인증서를 형식이 지정 된 파일로 내보냅니다.

**. crl-인증서 해지 목록**.  

인증서가 만료 되기 전에 인증서의 권한을 해제 하는 방법으로 인증 기관에서 인증서를 생성 합니다. 

#### <a name="cli-commands"></a>CLI 명령

`cyberx-xsense-certificate-import`CLI 명령을 사용 하 여 인증서를 가져옵니다. 이 도구를 사용 하려면 WinSCP 또는 Wget과 같은 도구를 사용 하 여 인증서 파일을 장치에 업로드 해야 합니다.

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

### <a name="use-openssl-to-manage-certificates"></a>OpenSSL를 사용 하 여 인증서 관리

다음 명령을 사용 하 여 인증서를 관리 합니다.

| Description | CLI 명령 |
|--|--|
| 새 개인 키 및 인증서 서명 요청 생성 | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| 자체 서명된 인증서 생성 | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| 기존 개인 키에 대 한 CSR (인증서 서명 요청)을 생성 합니다. | `openssl req -out CSR.csr -key privateKey.key -new` |
| 기존 인증서를 기반으로 인증서 서명 요청 생성 | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| 개인 키에서 암호 제거 | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

인증서, CSR 또는 개인 키 내의 정보를 확인 해야 하는 경우 다음 명령을 사용 합니다.

| Description | CLI 명령 |
|--|--|
| CSR (인증서 서명 요청) 확인 | `openssl req -text -noout -verify -in CSR.csr` |
| 개인 키 확인 | `openssl rsa -in privateKey.key -check` |
| 인증서 확인 | `openssl x509 -in certificate.crt -text -noout`  |

개인 키가 인증서와 일치 하지 않거나 사이트에 설치한 인증서를 신뢰할 수 없다는 오류가 표시 되 면 다음 명령을 사용 하 여 오류를 해결 합니다.

| Description | CLI 명령 |
|--|--|
| 공개 키의 MD5 해시를 확인 하 여 CSR 또는 개인 키에 있는 항목과 일치 하는지 확인 합니다. | 1(sp1). `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

인증서와 키를 다른 형식으로 변환 하 여 특정 유형의 서버 또는 소프트웨어와 호환 되도록 하려면 다음 명령을 사용 합니다.

| Description | CLI 명령 |
|--|--|
| DER 파일 (.crt. .cer)을 PEM으로 변환  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| PEM 파일을 DER로 변환 | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| 개인 키와 인증서를 포함 하는 PKCS # 12 파일 (.pfx. p12)을 PEM으로 변환 합니다. | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />`-nocerts`을 (를) 추가 하 여 개인 키만 출력 하거나 `-nokeys` 를 추가 하 여 인증서만 출력할 수 있습니다. |
| PEM 인증서 파일 및 개인 키를 PKCS # 12 (.pfx. p12)로 변환 합니다. | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

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

1. [Azure 포털](https://portal.azure.com/)로 이동합니다.

1. IoT 용 Defender로 이동 합니다.

1. **업데이트** 페이지로 이동 합니다.

1. 온-프레미스 관리 콘솔 섹션에서 버전을 선택 합니다.

1. **다운로드** 를 선택하고 파일을 저장합니다.

1. 온-프레미스 관리 콘솔에 로그인 하 고 측면 메뉴에서 **시스템 설정** 을 선택 합니다.

1. **버전 업데이트** 창에서 **업데이트** 를 선택 합니다.

1. IoT 용 Defender **업데이트** 페이지에서 다운로드 한 파일을 선택 합니다.

## <a name="mail-server-settings"></a>메일 서버 설정

온-프레미스 관리 콘솔에 대 한 SMTP 메일 서버 설정을 정의 합니다.

다음을 정의 합니다.

1. 관리 자격 증명을 사용 하 여 온-프레미스 관리를 위해 CLI에 로그인 합니다.
1. ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Enter 키를 선택합니다. 다음 프롬프트가 표시 됩니다.
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. SMTP 서버 이름 및 발신자를 입력 하 고 enter 키를 선택 합니다.

## <a name="see-also"></a>참고 항목

[관리 콘솔에서 센서 관리](how-to-manage-sensors-from-the-on-premises-management-console.md)

[개별 센서 관리](how-to-manage-individual-sensors.md)
