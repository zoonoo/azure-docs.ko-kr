---
title: Azure NetApp Files에 대 한 Azure 애플리케이션 일치 스냅숏 도구 문제 해결 Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구를 사용 하기 위한 문제 해결 콘텐츠를 제공 합니다.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 903cb3323b9441ec8bb382054f065760875e3e89
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632835"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>일관 된 스냅숏 도구 Azure 애플리케이션 문제 해결 (미리 보기)

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구를 사용 하기 위한 문제 해결 콘텐츠를 제공 합니다.

명령을 실행 하는 동안 발생할 수 있는 일반적인 문제는 다음과 같습니다. 설명 된 해결 지침에 따라 문제를 해결 합니다. 여전히 문제가 발생 하는 경우 Azure Portal에서 서비스 요청을 열고 Microsoft 지원 응답할 SAP HANA Large Instance 큐에 요청을 할당 합니다.

## <a name="log-files"></a>로그 파일

AzAcSnap를 사용 하 여 오류를 디버깅 하는 데 가장 적합 한 정보는 로그 파일입니다.  

### <a name="log-file-location"></a>로그 파일 위치

로그 파일은 `logPath` AzAcSnap 구성 파일의 매개 변수에 따라 구성 된 디렉터리에 저장 됩니다.  기본 구성 파일 이름은이 `azacsnap.json` 고의 기본값입니다 `logPath` `"./logs"` . 즉, 로그 파일은 `./logs` 명령이 실행 되는 위치를 기준으로 디렉터리에 기록 됩니다 `azacsnap` .  `logPath`절대 위치 (예:)를 만들면 `/home/azacsnap/logs` `azacsnap` `/home/azacsnap/logs` 명령이 실행 된 위치에 관계 없이 로그를로 출력 합니다 `azacsnap` .

### <a name="log-file-naming"></a>로그 파일 이름 지정

로그 파일 이름은 응용 프로그램 이름 (예:) `azacsnap` , 사용 된 명령 옵션 (예:, 등) `-c` `backup` `test` `details` 및 구성 파일 이름 (예: 기본값 = `azacsnap.json` )을 기반으로 합니다.  따라서 명령을 사용 하는 경우 `-c backup` 기본적으로 로그 파일 이름은 `azacsnap-backup-azacsnap.log` 이며로 구성 된 디렉터리에 기록 됩니다 `logPath` .  

이 명명 규칙은 데이터베이스당 여러 구성 파일을 허용 하도록 설정 되었으며, 연결 된 로그 파일을 쉽게 찾을 수 있도록 합니다.  따라서 구성 파일 이름이 인 경우 `SID.json` 옵션을 사용할 때의 결과 파일 이름은이 됩니다 `azacsnap -c backup --configfile SID.json` `azacsnap-backup-SID.log` .

### <a name="result-file-and-syslog"></a>결과 파일 및 syslog

`-c backup`명령 옵션의 경우 AzAcSnap `*.result` 명령을 사용 하 여 파일 및 시스템 로그 ()에 기록 `/var/log/messages` `logger` 합니다.  `*.result`파일 이름은 [로그 파일과](#log-file-naming) 동일한 기본 이름을 가지 며 [로그 파일과 동일한 위치로](#log-file-location)이동 합니다.  다음 예제에 따라 간단한 단일 줄 출력 파일입니다.

파일의 예제 출력 `*.result` 입니다.
```output
Database # 1 (PR1) : completed ok
```

파일의 예제 출력 `/var/log/messages` 입니다.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-sap-hana"></a>SAP HANA와의 통신 실패

로 테스트를 실행 하 여 SAP HANA 통신의 유효성을 검사 하는 경우 `azacsnap -c test --test hana` 다음 오류를 제공 합니다.

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**해결 방법:**

1. 각 HANA 인스턴스에 대 한 구성 파일 (예:)을 확인 `azacsnap.json` 하 SAP HANA 데이터베이스 값이 올바른지 확인 합니다.
1. 아래 명령을 실행 하 여 `hdbsql` 명령이 경로에 있는지 확인 하 고 SAP HANA 서버에 연결할 수 있는지 확인 하십시오. 다음 예제에서는 명령 및 해당 출력의 올바른 실행을 보여 줍니다.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    이 예에서는 `hdbsql` 사용자에 게 명령이 없습니다 `$PATH` .

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    이 예제에서는 `hdbsql` 명령이 사용자의에 일시적으로 추가 `$PATH` 되지만 실행 시 명령으로 연결 키가 올바르게 설정 되지 않은 것을 볼 `hdbuserstore Set` 수 있습니다 (자세한 내용은 시작 가이드 참조).

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > 사용자에 게 영구적으로 추가 하려면 `$PATH` 사용자의 파일을 업데이트 합니다. `$HOME/.profile`

## <a name="the-hdbuserstore-location"></a>`hdbuserstore`위치

SAP HANA와의 통신을 설정할 때 `hdbuserstore` 프로그램을 사용 하 여 보안 통신 설정을 만듭니다.  `hdbuserstore`프로그램은 일반적으로 또는 아래에 있습니다 `/usr/sap/<SID>/SYS/exe/hdb/` `/usr/sap/hdbclient` .  일반적으로 설치 관리자는 사용자의에 올바른 위치를 추가 `azacsnap` `$PATH` 합니다.

## <a name="failed-test-with-storage"></a>저장소로 실패 한 테스트

명령이 `azacsnap -c test --test storage` 성공적으로 완료 되지 않았습니다.

### <a name="azure-large-instance"></a>Azure Large Instance

다음 예제는 `azacsnap` Azure Large Instance의 SAP HANA에서 실행 되는 것입니다.

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**해결 방법:** 위의 오류는 일반적으로 Azure Large Instance 저장소 사용자가 기본 저장소에 액세스할 수 없을 때 표시 됩니다. 제공 된 저장소 사용자를 사용 하 여 저장소에 대 한 액세스의 유효성을 검사 하려면 `ssh` 명령을 실행 하 여 저장소 플랫폼과의 통신 유효성을 검사 합니다.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

예상 출력이 있는 예제:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>' 172.18.18.11 (172.18.18.11) ' 호스트의 신뢰성을 설정할 수 없습니다.

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**해결 방법:** 예를 선택 하지 마십시오. 저장소 IP 주소가 올바른지 확인 하세요. 문제가 여전히 발생 하는 경우 Microsoft 운영 팀에서 저장소 IP 주소를 확인 합니다.

### <a name="azure-netapp-files"></a>Azure NetApp Files

다음 예는 `azacsnap` Azure NetApp Files을 사용 하 여 VM에서 실행 되는 것입니다.

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**해결 방법:**

1. 구성 파일에 설정 된 대로 서비스 사용자 파일이 있는지 확인 `azureauth.json` `azacsnap.json` 합니다.
1. 로그 파일 (예:)을 확인 `logs/azacsnap-test-azacsnap.log` 하 여 서비스 주체 ()에 올바른 콘텐츠가 있는지 확인 합니다 `azureauth.json` .  로그의 예는 다음과 같습니다.

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. 로그 파일 (예:)을 확인 `logs/azacsnap-test-azacsnap.log` 하 여 서비스 사용자 ( `azureauth.json` )가 만료 되었는지 확인 합니다. 로그의 예는 다음과 같습니다.

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>다음 단계

- [팁](azacsnap-tips.md)
