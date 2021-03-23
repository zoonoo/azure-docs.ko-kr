---
title: Azure NetApp Files에 대 한 Azure 애플리케이션 일치 스냅숏 도구를 설치 합니다. Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구를 설치 하는 방법에 대 한 지침을 제공 합니다.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 458f4d3f29cb08a94095167ed45133f5cd70f5f4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869194"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>Azure 애플리케이션 일치 스냅숏 도구 설치 (미리 보기)

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구를 설치 하는 방법에 대 한 지침을 제공 합니다.

## <a name="introduction"></a>소개

다운로드 가능한 자동 설치 관리자는 루트 사용자 권한 (예: azacsnap)을 사용 하 여 스냅숏 도구를 쉽게 설정 하 고 실행할 수 있도록 설계 되었습니다. 설치 관리자가 사용자를 설정 하 고 스냅숏 도구를 users `$HOME/bin` 하위 디렉터리 (기본값 =)에 넣습니다 `/home/azacsnap/bin` .
자동 설치 관리자는 설치를 수행 하는 사용자의 구성 (예: root)을 기반으로 하 여 모든 파일에 대 한 올바른 설정 및 경로를 결정 하려고 합니다. 필수 조건 단계 (저장소 및 SAP HANA와의 통신 사용)가 루트로 실행 된 경우에는 설치 시 개인 키와 `hdbuserstore` 백업 사용자의 위치를 복사 합니다. 그러나 저장소 백 엔드와의 통신을 사용 하도록 설정 하 고 설치 후에 지식 있는 관리자가 수동으로 SAP HANA 하는 단계를 수행할 수 있습니다.

## <a name="prerequisites-for-installation"></a>설치를 위한 필수 구성 요소

지침에 따라 스냅숏 및 재해 복구 명령을 설정 하 고 실행 합니다. 스냅숏 도구를 설치 하 고 사용 하기 전에 다음 단계를 루트로 완료 하는 것이 좋습니다.

1. **OS 패치 됨**: [Azure에서 SAP HANA (Large Instances)를 설치 하 고 구성 하는 방법](../virtual-machines/workloads/sap/hana-installation.md#operating-system)의 패치 및 SMT 설치를 참조 하세요.
1. **시간 동기화가 설정 됩니다**. 고객은 NTP 호환 시간 서버를 제공 하 고이에 따라 OS를 구성 해야 합니다.
1. **Hana가 설치** 됨: hana [데이터베이스의 SAP NetWeaver 설치](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database)에 있는 hana 설치 지침을 참조 하세요.
1. **[저장소와의 통신 사용](#enable-communication-with-storage)** (자세한 내용은 별도 섹션 참조): 고객이 개인/공개 키 쌍을 사용 하 여 SSH를 설정 하 고 저장소 백 엔드에서 설치를 위해 스냅숏 도구를 Microsoft Operations로 실행 하도록 계획 된 각 노드에 대 한 공개 키를 제공 해야 합니다.
   1. **Azure NetApp Files (자세한 내용은 별도 섹션 참조)**: 고객이 서비스 주체 인증 파일을 생성 해야 합니다.
   1. **Azure 대량 인스턴스의 경우 (자세한 내용은 별도 섹션 참조)**: 고객이 개인/공개 키 쌍을 사용 하 여 SSH를 설정 하 고 저장소 백 엔드에서 설치를 위해 스냅숏 도구를 Microsoft 작업에 실행 하도록 계획 된 각 노드에 대 한 공개 키를 제공 해야 합니다.

      SSH를 사용 하 여 노드 중 하나 (예:)에 연결 하 여이를 테스트 `ssh -l <Storage UserName> <Storage IP Address>` 합니다.
      `exit`저장소 프롬프트의 로그 아웃을 입력 합니다.

      Microsoft operations은 프로 비전 시 저장소 사용자 및 저장소 IP를 제공 합니다.
  
1. **[SAP HANA와의 통신 사용](#enable-communication-with-sap-hana)** (자세한 내용은 별도 섹션 참조): 고객이 스냅숏을 수행 하는 데 필요한 권한을 가진 적절 한 SAP HANA 사용자를 설정 해야 합니다.
   1. 이 설정은의 텍스트를 사용 하 여 명령줄에서 다음과 같이 테스트할 수 있습니다. `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - 위의 예제는 SAP HANA에 대 한 비 SSL 통신에 대 한 것입니다.

## <a name="enable-communication-with-storage"></a>저장소와의 통신 사용

이 섹션에서는 저장소와의 통신을 사용 하도록 설정 하는 방법을 설명 합니다.

### <a name="azure-netapp-files"></a>Azure NetApp Files

RBAC 서비스 주체 만들기

1. Azure Cloud Shell 세션 내에서 기본적으로 서비스 사용자와 연결 하려는 구독에 로그온 했는지 확인 합니다.

    ```azurecli-interactive
    az account show
    ```

1. 구독이 올바르지 않으면 다음을 사용 합니다.

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. 다음 예제에 따라 Azure CLI를 사용 하 여 서비스 주체를 만듭니다.

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. 그러면 다음 예제와 유사한 출력이 생성 됩니다.

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > 이 명령은 구독 수준에서 서비스 주체에 RBAC 참가자 역할을 자동으로 할당 하므로 테스트에서 리소스를 만들 특정 리소스 그룹으로 범위를 좁힐 수 있습니다.

1. 출력 콘텐츠를 잘라내어 `azureauth.json` 명령과 동일한 시스템에 저장 된 파일에 붙여 넣고 `azacsnap` 적절 한 시스템 권한으로 파일을 보호 합니다.

### <a name="azure-large-instance"></a>Azure Large Instance

저장소 백 엔드와의 통신은 암호화 된 SSH 채널을 통해 실행 됩니다. 다음 예제 단계는이 통신에 대해 SSH를 설정 하는 방법에 대 한 지침을 제공 하는 것입니다.

1. 파일 수정 `/etc/ssh/ssh_config`

    줄이 추가 된 다음 출력을 참조 하세요 `MACs hmac-sha1` .

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. 개인/공개 키 쌍 만들기

    다음 예제 명령을 사용 하 여 키 쌍을 생성 하는 경우 키를 생성할 때 암호를 입력 하지 마세요.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Microsoft 작업에 공개 키 보내기

    `cat /root/.ssh/id_rsa.pub`스냅숏 도구가 저장소 하위 시스템과 통신할 수 있도록 Microsoft 작업에 명령 출력 (예: 아래 예)을 보냅니다.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>SAP HANA와 통신 사용

스냅숏 도구는 SAP HANA와 통신 하며 데이터베이스 저장 지점을 시작 하 고 해제할 수 있는 적절 한 권한이 있는 사용자가 필요 합니다. 다음 예에서는 SAP HANA v2 사용자를 설정 하 고 SAP HANA 데이터베이스와 통신 하는 방법을 보여 줍니다 `hdbuserstore` .

다음 예제 명령은 SAP HANA 2의 SYSTEMDB에서 사용자 (AZACSNAP)를 설정 합니다.
다음과 같이 적절 하 게 IP 주소, 사용자 이름 및 암호를 변경 합니다.

1. SYSTEMDB에 연결 하 여 사용자를 만듭니다.

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. 사용자 만들기

    이 예에서는 SYSTEMDB에 AZACSNAP 사용자를 만듭니다.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. 사용자 권한 부여

    이 예에서는 AZACSNAP 사용자에 대 한 사용 권한을 설정 하 여 데이터베이스 일관성 저장소 스냅숏을 수행할 수 있도록 합니다.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *선택 사항* -사용자의 암호가 만료 되지 않도록 방지

    > [!NOTE]
    > 이렇게 변경 하려면 회사 정책을 확인 하세요.

   이 예에서는 AZACSNAP 사용자에 대 한 암호 만료를 사용 하지 않도록 설정 합니다 .이 변경 없이 사용자의 암호는 스냅숏이 올바르게 수행 되지 않도록 만료 됩니다.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. SAP HANA 보안 사용자 저장소 설정 (암호 변경)이 예제에서는 `hdbuserstore` Linux 셸에서 명령을 사용 하 여 SAP HANA 보안 사용자 저장소를 설정 합니다.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. SAP HANA 보안 사용자 저장소에서 보안 사용자 저장소가 올바르게 설정 되어 있는지 확인 하려면 명령을 사용 하 여 `hdbuserstore` 다음 예제와 유사한 출력을 나열 합니다. 사용에 대 한 자세한 내용은 `hdbuserstore` SAP 웹 사이트에서 확인할 수 있습니다.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>SAP HANA와 통신 하는 데 SSL 사용

이 `azacsnap` 도구는 SAP HANA의 `hdbsql` 명령을 활용 하 여 SAP HANA와 통신 합니다. 여기에는 SAP HANA와의 통신을 암호화할 때 SSL 옵션을 사용 하는 것이 포함 됩니다. `azacsnap` 에서는 `hdbsql` 다음과 같이 명령의 SSL 옵션을 사용 합니다.

다음은 옵션을 사용할 때 항상 사용 됩니다 `azacsnap --ssl` .

- `-e` -TLS 암호화 Tls/SSL 암호화를 사용 하도록 설정 합니다. 서버에서 사용 가능한 가장 높은를 선택 합니다.
- `-ssltrustcert` -서버 인증서의 유효성을 검사할지 여부를 지정 합니다.
- `-sslhostnameincert "*"` -서버 id를 확인 하는 데 사용 되는 호스트 이름을 지정 합니다. `"*"`을 호스트 이름으로 지정 하면 서버의 호스트 이름에 대 한 유효성이 검사 되지 않습니다.

SSL 통신에는 키 저장소 및 신뢰 저장소 파일도 필요 합니다.  이러한 파일은 Linux 설치의 기본 위치에 저장 될 수 있지만, 다양 한 SAP HANA 시스템에 올바른 키 자료가 사용 되는지 확인 하려면 (즉, 각 SAP HANA 시스템에 서로 다른 키 저장소 및 신뢰 저장소 파일이 사용 되는 경우) `azacsnap` `securityPath` 구성 파일에 지정 된 위치에 키 저장소 및 신뢰 저장소 파일이 저장 될 것으로 예상 합니다 `azacsnap` .

#### <a name="key-store-files"></a>키 저장소 파일

- 동일한 키 자료로 여러 Sid를 사용 하는 경우에는 구성 파일에 정의 된 대로 securityPath 위치에 대 한 링크를 만드는 것이 더 쉽습니다 `azacsnap` .  SSL을 사용 하는 모든 SID에 대해 이러한 값이 존재 하는지 확인 합니다.
  - Openssl의 경우:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - Commoncrypto의 경우:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- SID 당 다른 키 자료를 사용 하 여 여러 Sid를 사용 하는 경우 파일을 Sid 구성 파일에 정의 된 securityPath 위치로 복사 하거나 이동 하 고 이름을 바꿉니다 `azacsnap` .
  - Openssl의 경우:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - Commoncrypto의 경우:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

가 `azacsnap` 호출 되 면 `hdbsql` `-sslkeystore=<securityPath>/<SID>_keystore` 명령줄에 추가 됩니다.

#### <a name="trust-store-files"></a>저장소 파일 신뢰

- 동일한 키 자료로 여러 Sid를 사용 하는 경우 구성 파일에 정의 된 대로 securityPath 위치에 하드 링크를 만듭니다 `azacsnap` .  SSL을 사용 하는 모든 SID에 대해 이러한 값이 존재 하는지 확인 합니다.
  - Openssl의 경우:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - Commoncrypto의 경우:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- SID 당 다른 키 자료를 사용 하 여 여러 Sid를 사용 하는 경우 파일을 Sid 구성 파일에 정의 된 securityPath 위치로 복사 하거나 이동 하 고 이름을 바꿉니다 `azacsnap` .
  - Openssl의 경우:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - Commoncrypto의 경우:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> `<SID>`파일 이름의 구성 요소는 모두 대문자 (예:, 등)의 SAP HANA 시스템 식별자 여야 합니다 `H80` `PR1` .

가 `azacsnap` 호출 되 면 `hdbsql` `-ssltruststore=<securityPath>/<SID>_truststore` 명령줄에 추가 됩니다.

따라서 `azacsnap -c test --test hana --ssl openssl` `SID` 가 `H80` 구성 파일에 있는 경우에는 다음과 같이 연결을 실행 합니다 `hdbsql` .

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> 문자는 명령줄 `\` 에서 전달 되는 여러 매개 변수의 명확성을 높이기 위해 줄 바꿈이 됩니다.

## <a name="installing-the-snapshot-tools"></a>스냅숏 도구 설치

다운로드 가능한 자동 설치 관리자는 루트 사용자 권한 (예: azacsnap)을 사용 하 여 스냅숏 도구를 쉽게 설정 하 고 실행할 수 있도록 설계 되었습니다. 설치 관리자가 사용자를 설정 하 고 스냅숏 도구를 users `$HOME/bin` 하위 디렉터리 (기본값 =)에 넣습니다 `/home/azacsnap/bin` .

자동 설치 관리자는 설치를 수행 하는 사용자의 구성 (예: root)을 기반으로 하 여 모든 파일에 대 한 올바른 설정 및 경로를 결정 하려고 합니다. 이전 설치 단계 (저장소 및 SAP HANA와의 통신 사용)가 루트로 실행 된 경우에는 설치 시 개인 키와를 `hdbuserstore` 백업 사용자의 위치에 복사 합니다. 그러나 저장소 백 엔드와의 통신을 사용 하도록 설정 하 고 설치 후에 지식 있는 관리자가 수동으로 SAP HANA 하는 단계를 수행할 수 있습니다.

> [!NOTE]
> Azure 대량 인스턴스 설치에 대 한 이전 SAP HANA의 경우 사전 설치 된 스냅숏 도구의 디렉터리는 `/hana/shared/<SID>/exe/linuxx86_64/hdb` 입니다.

필수 구성 요소 [단계](#prerequisites-for-installation) 를 완료 하 고 나면 다음과 같이 자동 설치 관리자를 사용 하 여 스냅숏 도구를 설치할 수 있습니다.

1. 다운로드 한 자동 설치 관리자를 대상 시스템에 복사 합니다.
1. 자동 설치 관리자를 사용자로 실행 합니다 `root` . 다음 예제를 참조 하세요. 필요한 경우 명령을 사용 하 여 파일 실행 파일을 만듭니다 `chmod +x *.run` .

인수 없이 자체 설치 관리자 명령을 실행 하면 설치 관리자를 사용 하 여 다음과 같이 스냅숏 도구를 설치 하는 데 대 한 도움말이 표시 됩니다.

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> 셀프 설치 관리자에는 사용자 만들기 및 설정을 수행 하지 않고 번들에서 스냅숏 도구를 추출 (-X) 할 수 있는 옵션이 있습니다. 이를 통해 숙련 된 관리자는 수동으로 설치 단계를 완료 하거나 기존 설치를 업그레이드 하는 명령을 복사할 수 있습니다.

### <a name="easy-installation-of-snapshot-tools-default"></a>간편한 스냅숏 도구 설치 (기본값)

설치 관리자는 Azure에서 SAP HANA에 대 한 스냅숏 도구를 신속 하 게 설치할 수 있도록 설계 되었습니다. 기본적으로 설치 관리자가-I 옵션을 사용 하 여 실행 되는 경우 다음 단계를 수행 합니다.

1. 스냅숏 사용자 ' azacsnap ', 홈 디렉터리를 만들고 그룹 멤버 자격을 설정 합니다.
1. Azacsnap 사용자의 로그인을 구성 `~/.profile` 합니다.
1. Azacsnap에 추가할 디렉터리의 파일 시스템을 검색 합니다 `$PATH` .이는 일반적으로 및와 같은 SAP HANA 도구의 경로 `hdbsql` `hdbuserstore` 입니다.
1. Azacsnap의에 추가할 디렉터리의 파일 시스템을 검색 `$LD_LIBRARY_PATH` 합니다. 많은 명령에서 올바르게 실행 하기 위해 라이브러리 경로를 설정 해야 합니다. 이렇게 하면 설치 된 사용자에 대해 구성 됩니다.
1. "루트" 사용자 (설치를 실행 하는 사용자)에서 azacsnap의 백 엔드 저장소에 대 한 SSH 키를 복사 합니다. 여기서는 "루트" 사용자가 저장소에 대 한 연결을 이미 구성 했다고 가정 합니다.
    - "[저장소와 통신 사용](#enable-communication-with-storage)" 섹션을 참조 하세요.
1. 대상 사용자 azacsnap에 대 한 SAP HANA 연결 보안 사용자 저장소를 복사 합니다. 여기서는 "루트" 사용자가 보안 사용자 저장소를 이미 구성 했다고 가정 합니다. "SAP HANA 통신 사용" 섹션을 참조 하세요.
1. 스냅숏 도구는로 추출 됩니다 `/home/azacsnap/bin/` .
1. 의 명령에는 `/home/azacsnap/bin/` 사용 권한 집합 (소유권 및 실행 가능 비트 등)이 있습니다.

다음 예에서는 기본 설치 옵션을 사용 하 여 실행 하는 경우 설치 관리자의 올바른 출력을 보여 줍니다.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>스냅숏 도구 제거

기본 설정을 사용 하 여 스냅숏 도구가 설치 된 경우에는 설치 된 사용자만 제거 하면 됩니다 (기본값 = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>스냅숏 도구의 수동 설치

도구를 수동으로 설치 해야 하는 경우도 있지만이 프로세스를 용이 하 게 하려면 설치 관리자의 기본 옵션을 사용 하는 것이 좋습니다.

문자로 시작 하는 각 줄은 `#` 문자 다음에 나오는 예제 명령을 루트 사용자가 실행 하는 방법을 보여 줍니다. `\`줄의 끝에 있는는 shell 명령의 표준 줄 연속 문자입니다.

루트 수퍼유저는 다음과 같이 수동 설치를 수행할 수 있습니다.

1. "Sapsys" 그룹 ID를 가져옵니다 .이 경우 그룹 ID는 1010입니다.

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. 1 단계의 그룹 ID를 사용 하 여 스냅숏 사용자 ' azacsnap ', 홈 디렉터리를 만들고 그룹 멤버 자격을 설정 합니다.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. 사용자 azacsnap의 로그인이 있는지 확인 `.profile` 합니다.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Azacsnap의 $PATH에 추가할 디렉터리의 파일 시스템을 검색 합니다. 일반적으로 및와 같은 SAP HANA 도구에 대 한 `hdbsql` 경로 `hdbuserstore` 입니다.

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. 업데이트 된 경로를 사용자의 프로필에 추가 합니다.

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Azacsnap의 $LD _LIBRARY_PATH에 추가할 디렉터리의 파일 시스템을 검색 합니다.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. 업데이트 된 라이브러리 경로를 사용자의 프로필에 추가 합니다.

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. Azure의 많은 인스턴스
    1. "루트" 사용자 (설치를 실행 하는 사용자)에서 azacsnap의 백 엔드 저장소에 대 한 SSH 키를 복사 합니다. 여기서는 "루트" 사용자가 저장소에 대 한 연결을 이미 구성 했다고 가정 합니다.
       > "[저장소와 통신 사용](#enable-communication-with-storage)" 섹션을 참조 하세요.

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. SSH 파일에 대해 사용자 권한 설정

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. Azure NetApp Files에서
    1. `DOTNET_BUNDLE_EXTRACT_BASE_DIR`.Net Core 단일 파일 추출 지침에 따라 사용자 경로를 구성 합니다.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. 대상 사용자 azacsnap에 대 한 SAP HANA 연결 보안 사용자 저장소를 복사 합니다. 여기서는 "루트" 사용자가 보안 사용자 저장소를 이미 구성 했다고 가정 합니다.
    > "[SAP HANA 통신 사용](#enable-communication-with-sap-hana)" 섹션을 참조 하세요.

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. 파일에 대 한 사용자 권한 설정 `hdbuserstore`

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. /Home/azacsnap/bin/.에 스냅숏 도구 추출

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. 명령 실행 파일 만들기

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. 사용자의 홈 디렉터리에 대 한 올바른 소유권 사용 권한이 설정 되어 있는지 확인 합니다.

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>스냅숏 도구 설정 완료

설치 관리자는 스냅숏 도구 설치가 완료 된 후 완료 해야 하는 단계를 제공 합니다.
스냅숏 도구를 구성 하 고 테스트 하려면 다음 단계를 수행 합니다.  성공적으로 테스트 한 후에는 첫 번째 데이터베이스 일치 저장소 스냅숏을 수행 합니다.

다음 출력에서는 기본 설치 옵션을 사용 하 여 설치 관리자를 실행 한 후 완료 해야 하는 단계를 보여 줍니다.

1. 스냅숏 사용자 계정으로 변경
    1. `su - azacsnap`
1. HANA 보안 사용자 저장소 설정
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. 명령 위치로 변경
   1. `cd /home/azacsnap/bin/`
1. 고객 세부 정보 파일 구성
   1. `azacsnap -c configure –-configuration new`
1. 저장소에 대 한 연결 테스트 ...
   1. `azacsnap -c test –-test storage`
1. HANA에 대 한 연결 테스트 ...
    1. SSL 없음
       1. `azacsnap -c test –-test hana`
    1. SSL을 사용 하는 경우 올바른 SSL 옵션을 선택 해야 합니다.
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. 첫 번째 스냅숏 백업 실행
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

설치 하기 전에 "[SAP HANA 통신 사용](#enable-communication-with-sap-hana)"을 수행 하지 않은 경우 2 단계가 필요 합니다.

> [!NOTE]
> 테스트 명령이 올바르게 실행 되어야 합니다. 그렇지 않으면 명령이 실패할 수 있습니다.

## <a name="configuring-the-database"></a>데이터베이스 구성

이 섹션에서는 데이터 베이스를 구성 하는 방법을 설명 합니다.

### <a name="sap-hana-configuration"></a>SAP HANA 구성

로그 백업 및 카탈로그에 대 한 보호를 보장 하기 위해 SAP HANA에 적용 되는 몇 가지 권장 변경 사항이 있습니다. 기본적으로 및는 `basepath_logbackup` `basepath_catalogbackup` 해당 파일을 디렉터리에 출력 `$(DIR_INSTANCE)/backup/log` 하며,이 경로가 스냅숏으로 구성 된 볼륨에 있을 가능성이 거의 `azacsnap` 없습니다. 이러한 파일은 저장소 스냅숏으로 보호 되지 않습니다.

다음 `hdbsql` 명령 예제는에 의해 스냅숏이 될 수 있는 저장소 볼륨의 위치로 로그 및 카탈로그 경로를 설정 하는 방법을 설명 하기 위한 것 `azacsnap` 입니다. 명령줄에서 로컬 SAP HANA 구성과 일치 하는 값을 확인 해야 합니다.

### <a name="configure-log-backup-location"></a>로그 백업 위치 구성

이 예제에서는 매개 변수를 변경 합니다 `basepath_logbackup` .

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>카탈로그 백업 위치 구성

이 예제에서는 매개 변수를 변경 합니다 `basepath_catalogbackup` . 먼저 `basepath_catalogbackup` 디렉터리와 동일한 소유권이 있는 경로를 만들지 않는 경우 파일 시스템에 경로가 있는지 확인 합니다.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

경로를 만들어야 하는 경우 다음 예에서는 경로를 만들고 올바른 소유권 및 사용 권한을 설정 합니다. 이러한 명령은 루트로 실행 해야 합니다.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

다음 예에서는 SAP HANA 설정을 변경 합니다.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>로그 및 카탈로그 백업 위치 확인

위에서 변경한 후 다음 명령을 사용 하 여 설정이 올바른지 확인 합니다.
이 예제에서 위의 지침에 따라 설정 된 설정은 시스템 설정으로 표시 됩니다.

> 또한이 쿼리는 비교를 위한 기본 설정을 반환 합니다.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>로그 백업 제한 시간 구성

로그 백업을 수행 하는 SAP HANA에 대 한 기본 설정은 900 초 (15 분)입니다. 이 값을 300 초 (5 분)로 줄이는 것이 좋습니다.  그런 다음 구성 파일의 다른 볼륨 섹션에 log_backups 볼륨을 추가 하 여 정기적인 백업 (예: 10 분 마다)을 실행할 수 있습니다.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>로그 백업 제한 시간 확인

로그 백업 제한 시간을 변경한 후 다음과 같이 설정 되었는지 확인 합니다.
이 예에서는 설정 된 설정이 시스템 설정으로 표시 되지만이 쿼리는 비교를 위한 기본 설정도 반환 합니다.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 일치 스냅숏 도구 구성](azacsnap-cmd-ref-configure.md)
