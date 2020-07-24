---
title: Azure Virtual Machines에 TmaxSoft OpenFrame 설치
description: Azure Virtual Machines (Vm)에서 TmaxSoft OpenFrame 환경을 사용 하 여 IBM z/OS 메인프레임 워크 로드를 Rehost.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 41b31d5c4a01183d4620dcd6ec6f4729f078d382
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082395"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Azure에 TmaxSoft OpenFrame 설치

Azure에서 OpenFrame 환경을 설정 하는 방법에 대 한 자세한 내용은 개발, 데모, 테스트 또는 프로덕션 워크 로드에 적합 합니다. 이 자습서에서는 각 단계를 안내 합니다.

OpenFrame에는 Azure에서 메인프레임 에뮬레이션 환경을 만드는 여러 구성 요소가 포함 됩니다. 예를 들어 OpenFrame 온라인 서비스 IBM CICS (Customer Information Control System) 및 OpenFrame Batch와 같은 메인프레임 미들웨어를 해당 TJES 구성 요소와 함께 교체 하 고 IBM 메인프레임의 JES (Job Entry Subsystem)를 대체 합니다.

OpenFrame은 Oracle Database, Microsoft SQL Server, IBM Db2 및 MySQL을 비롯 한 모든 관계형 데이터베이스에서 작동 합니다. 이 OpenFrame 설치는 TmaxSoft Tibero 관계형 데이터베이스를 사용 합니다. OpenFrame과 Tibero는 모두 Linux 운영 체제에서 실행 됩니다. 다른 지원 되는 Linux 배포를 사용할 수 있지만이 자습서에서는 CentOS 7.3을 설치 합니다. OpenFrame 응용 프로그램 서버와 Tibero 데이터베이스는 하나의 VM (가상 컴퓨터)에 설치 됩니다.

이 자습서에서는 OpenFrame suite 구성 요소를 설치 하는 과정을 안내 합니다. 일부는 별도로 설치 해야 합니다.

기본 OpenFrame 구성 요소:

- 필수 설치 패키지.
- Tibero 데이터베이스.
- ODBC (Open Database Connectivity)는 OpenFrame의 응용 프로그램에서 Tibero 데이터베이스와 통신 하는 데 사용 됩니다.
- 전체 시스템을 관리 하는 미들웨어 인 OpenFrame 기반입니다.
- OpenFrame Batch-메인프레임 일괄 처리 시스템을 대체 하는 솔루션입니다.
- 시스템 및 리소스에 대 한 사용자 액세스를 제어 하는 서비스 모듈인 TACF.
- ProSort-일괄 처리 트랜잭션에 대 한 정렬 도구입니다.
- OFCOBOL은 메인프레임의 COBOL 프로그램을 해석 하는 컴파일러입니다.
- OFASM. 메인프레임의 어셈블러 프로그램을 해석 하는 컴파일러입니다.
- 메인프레임 미들웨어 및 IBM CICS를 대체 하는 솔루션인, OpenFrame 서버 유형 C (.OSC).
- Java Enterprise Edition 6 용으로 인증 된 웹 응용 프로그램 서버인 java Enterprise 사용자 솔루션 (JEUS)
- OFGW-3270 수신기를 제공 하는 OpenFrame 게이트웨이 구성 요소입니다.
- OFManager-웹 환경에서 OpenFrame의 작업 및 관리 기능을 제공 하는 솔루션입니다.

기타 필수 OpenFrame 구성 요소:

- 메인프레임 미들웨어와 IMS DC를 대체 하는 솔루션인 OSI.
- TJES, 메인프레임의 JES 환경을 제공 하는 솔루션입니다.
- OFTSAM (V) SAM 파일을 오픈 시스템에서 사용할 수 있도록 하는 솔루션입니다.
- OFHiDB, 메인프레임의 IMS DB를 대체 하는 솔루션입니다.
- 이제 메인프레임의 PL/I 프로그램을 해석 하는 컴파일러입니다.
- PROTRIEVE는 메인프레임 언어 CA-Easytrieve를 실행 하는 솔루션입니다.
- OFMiner은 메인프레임 자산을 분석 한 후 Azure로 마이그레이션하는 솔루션입니다.

## <a name="architecture"></a>Architecture

다음 그림은이 자습서에 설치 된 OpenFrame 7.0 아키텍처 구성 요소에 대 한 개요를 제공 합니다.

![OpenFrame 구성 요소](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure 시스템 요구 사항

다음 표에는 Azure의 설치에 대 한 요구 사항이 정리 되어 있습니다.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>요구 사항</th><th>Description</th></tr>
</thead>
<tbody>
<tr><td>Azure에서 지원 되는 Linux 배포판
</td>
<td>
Linux x86 2.6 (32 비트, 64 비트)<br/>
Red Hat 7.x<br/>
CentOS 4.x<br/>
</td>
</tr>
<tr><td>하드웨어
</td>
<td>코어: 2 (최소)<br/>
메모리: 4gb (최소)<br/>
스왑 공간: 1gb (최소)<br/>
하드 디스크: 100 GB (최소)<br/>
</td>
</tr>
<tr><td>Windows 사용자를 위한 선택적 소프트웨어
</td>
<td>PuTTY:이 가이드에서 VM 기능을 구성 하는 데 사용 됩니다.<br/>
WinSCP: 사용할 수 있는 인기 있는 SFTP 클라이언트 및 FTP 클라이언트<br/>
Windows 용 Eclipse: TmaxSoft에서 지원 되는 개발 플랫폼<br/>
(Microsoft Visual Studio는 현재 지원 되지 않음)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>필수 조건

필요한 모든 소프트웨어를 조합 하 고 모든 수동 프로세스를 완료 하는 데 며칠의 비용을 계획 합니다.

시작 하기 전에 다음을 수행 합니다.

- TmaxSoft에서 OpenFrame 설치 미디어를 가져옵니다. 기존 TmaxSoft 고객 인 경우 사용이 허가 된 복사본에 대해 TmaxSoft 담당자에 게 문의 하세요. 그렇지 않으면 [Tmaxsoft](https://www.tmaxsoft.com/contact/)에서 평가판 버전을 요청 합니다.

- 에 전자 메일을 보내 OpenFrame 설명서를 요청 <support@tmaxsoft.com> 합니다.

- 아직 없는 경우 Azure 구독을 가져옵니다. 시작 하기 전에 [무료 계정을](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 만들 수도 있습니다.

- (선택 사항) Azure VM에 대 한 액세스를 조직 내 허용 된 사용자로 제한 하는 사이트 간 VPN 터널 또는 jumpbox를 설정 합니다. 이 단계는 필요 하지 않지만 모범 사례입니다.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>OpenFrame 및 Tibero에 대해 Azure에서 VM 설정

다양 한 배포 패턴을 사용 하 여 OpenFrame 환경을 설정할 수 있지만 다음 절차에서는 한 VM에 OpenFrame 응용 프로그램 서버와 Tibero 데이터베이스를 배포 하는 방법을 보여 줍니다. 대규모 환경 및 대규모 워크 로드의 경우 성능을 향상 시키기 위해 데이터베이스를 자체 VM에 개별적으로 배포 하는 것이 가장 좋습니다.

**VM을 만들려면**

1. 에서 Azure Portal로 이동 하 여 <https://portal.azure.com> 계정에 로그인 합니다.

2. **가상 머신**을 클릭합니다.

    ![Azure Portal의 리소스 목록](media/vm-01.png)

3. **추가**를 클릭합니다.

    ![Azure Portal에서 옵션 추가](media/vm-02.png)

4. **운영 체제**의 오른쪽에서 **자세히**를 클릭 합니다.

     ![Azure Portal의 추가 옵션](media/vm-03.png)

5. **CentOS 기반 7.3** 을 클릭 하 여이 연습을 정확 하 게 수행 하거나 지원 되는 다른 Linux 배포를 선택할 수 있습니다.

     ![Azure Portal의 운영 체제 옵션](media/vm-04.png)

6. **기본** 설정에서 **이름**, **사용자 이름**, **인증 유형**, **구독** (지불의 AWS 스타일) 및 **리소스 그룹** (기존 항목 사용 또는 tmaxsoft 그룹 만들기)을 입력 합니다.

7. **인증 유형에**대 한 공개/개인 키 쌍을 포함 하 여 완료 되 면 **제출**을 클릭 합니다.

> [!NOTE]
> **인증 유형에**SSH 공개 키를 사용 하는 경우 다음 섹션의 단계를 참조 하 여 공개/개인 키 쌍을 생성 한 후 여기에서 단계를 다시 시작 합니다.

### <a name="generate-a-publicprivate-key-pair"></a>공개/개인 키 쌍을 생성 합니다.

Windows 운영 체제를 사용 하는 경우 공개/개인 키 쌍을 생성 하려면 PuTTYgen가 필요 합니다.

공개 키는 자유롭게 공유할 수 있지만 개인 키는 완전히 비밀로 유지 되어야 하며 다른 당사자와 공유 해서는 안 됩니다. 키를 생성 한 후에는 **SSH 공개 키** 를 구성에 붙여넣어 (실제로 Linux VM에 업로드 해야 합니다.) 이 파일은 \_ \~ 사용자 계정의 홈 디렉터리에 있는/sats 디렉터리 내에서 권한 있는 키 안에 저장 됩니다. 그런 다음 Linux VM은 연결 된 **ssh 개인 키** 를 ssh 클라이언트 (이 경우 PuTTY)에 제공 하 여 연결을 인식 하 고 유효성을 검사할 수 있습니다.

새 개인에 게 VM에 대 한 액세스 권한을 부여 하는 경우: 

- 각각의 새 개인은 PuTTYgen를 사용 하 여 자신의 공개/개인 키를 생성 합니다.
- 개인은 개별적으로 개인 키를 저장 하 고 VM의 관리자에 게 공개 키 정보를 보냅니다.
- 관리자는 공개 키의 내용을 \~ /.ssh/authorized \_ keys 파일에 붙여넣습니다.
- 새 개인은 PuTTY을 통해 연결 됩니다.

**공개/개인 키 쌍을 생성 하려면**

1.  에서 PuTTYgen를 다운로드 하 <https://www.putty.org/> 고 모든 기본 설정을 사용 하 여 설치 합니다.

2.  PuTTYgen를 열려면 C: Program Files PuTTY에서 PuTTY 설치 디렉터리를 찾습니다 \\ \\ .

    ![PuTTY 인터페이스](media/puttygen-01.png)

3.  **생성**을 클릭합니다.

    ![PuTTY 키 생성기 대화 상자](media/puttygen-02.png)

4.  생성 후 공개 키와 개인 키를 모두 저장 합니다. **가상 머신 \> 기본 사항 만들기** 창의 **SSH 공개 키** 섹션 (이전 섹션의 6 단계 및 7 단계에 표시 됨)에 공개 키의 내용을 붙여넣습니다.

    ![PuTTY 키 생성기 대화 상자](media/puttygen-03.png)

### <a name="configure-vm-features"></a>VM 기능 구성

1. Azure Portal의 **크기 선택** 블레이드에서 원하는 Linux 컴퓨터 하드웨어 설정을 선택 합니다. Tibero 및 OpenFrame을 모두 설치 하기 위한 *최소* 요구 사항은이 예제 설치에 표시 된 것과 같이 cpu 2 개, 4gb RAM입니다.

    ![가상 컴퓨터 만들기-기본 사항](media/create-vm-01.png)

2. **3 개의 설정** 을 클릭 하 고 기본 설정을 사용 하 여 선택적 기능을 구성 합니다.
3. 지불 세부 정보를 검토 합니다.

    ![가상 머신 만들기-구매](media/create-vm-02.png)

4. 선택 항목을 제출 합니다. Azure에서 VM 배포를 시작 합니다. 이 프로세스에는 일반적으로 몇 분 정도 걸립니다.

5. VM을 배포 하면 구성 중에 선택한 모든 설정이 표시 되는 대시보드가 표시 됩니다. **공용 IP 주소**를 기록해 둡니다.

    ![Azure 대시보드의 tmax](media/create-vm-03.png)

6. PuTTY를 엽니다.

7. **호스트 이름**에 사용자 이름 및 복사한 공용 IP 주소를 입력 합니다. 예: **사용자 이름 \@ publicip**.

    ![PuTTY 구성 대화 상자](media/putty-01.png)

8. **범주** 상자에서 **연결 \> SSH \> 인증**을 클릭 합니다. **개인 키** 파일에 대 한 경로를 제공 합니다.

    ![PuTTY 구성 대화 상자](media/putty-02.png)

9. **열기** 를 클릭 하 여 PuTTY 창을 시작 합니다. 성공 하면 Azure에서 실행 되는 새 CentOS VM에 연결 됩니다.

10. 루트 사용자로 로그온 하려면 **sudo bash**를 입력 합니다.

    ![명령 창의 루트 사용자 로그온](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>환경 및 패키지 설정

이제 VM을 만들고 로그온 했으므로 몇 가지 설정 단계를 수행 하 고 필수 사전 설치 패키지를 설치 해야 합니다.

1. Vi를 사용 하 여 호스트 파일 ()을 편집 하 여 **ofdemo** 이름을 로컬 IP 주소에 매핑합니다 `vi /etc/hosts` . IP가 192.168.96.148 ofdemo 인 것으로 가정 하면이는 변경 되기 전에 수행 됩니다.

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     변경 후에는 다음과 같습니다.

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. 그룹 및 사용자 만들기:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. 사용자 oframe7 암호를 변경 합니다.

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. /Etc/sysctl.conf에서 커널 매개 변수를 업데이트 합니다.

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. 다시 부팅 하지 않고 커널 매개 변수를 동적으로 새로 고칩니다.

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. 필요한 패키지 가져오기: 서버가 인터넷에 연결 되어 있는지 확인 하 고 다음 패키지를 다운로드 한 후 설치 합니다.

     - dos2unix
     - glibc
     - i686 glibc. x86 \_ 64
     - libaio
     - ncurses

          > [!NOTE]
          > Ncurses 패키지를 설치한 후 다음 기호화 된 링크를 만듭니다.
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c + +
     - libaio-devel. x86 \_ 64
     - strace
     - ltrace
     - gdb

7. Java RPM 설치의 경우 다음을 수행 합니다.

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Tibero 데이터베이스 설치

Tibero는 Azure의 OpenFrame 환경에서 몇 가지 주요 기능을 제공 합니다.

- Tibero는 다양 한 시스템 함수에 대 한 OpenFrame 내부 데이터 저장소로 사용 됩니다.
- KSDS, RRDS 및 ESDS를 비롯 한 VSAM 파일은 데이터 저장소에 대해 내부적으로 Tibero 데이터베이스를 사용 합니다.
- TACF 데이터 리포지토리는 Tibero에 저장 됩니다.
- OpenFrame 카탈로그 정보는 Tibero에 저장 됩니다.
- Tibero 데이터베이스는 IBM Db2를 대체 하 여 응용 프로그램 데이터를 저장 하는 데 사용할 수 있습니다.

**Tibero를 설치 하려면**

1. Tibero 이진 설치 관리자 파일이 있는지 확인 하 고 버전 번호를 검토 합니다.
2. Tibero 소프트웨어를 Tibero 사용자 계정 (oframe)에 복사 합니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. \_Vi ()에서 bash 프로필 `vi .bash_profile` 을 열고 다음을 붙여넣습니다.

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Bash 프로필을 실행 하려면 명령 프롬프트에서 다음을 입력 합니다.

    ```
    source .bash_profile
    ```

5. 팁 파일 (Tibero의 구성 파일)을 생성 한 다음 vi에서 엽니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. \$다음과 같이 TB \_ HOME/client/config/tbdsn를 수정 하 고 대신 127.0.0.1을 추가 합니다.

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. 데이터베이스를 만듭니다. 다음과 같은 출력이 표시됩니다.

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Tibero를 재활용 하려면 먼저 명령을 사용 하 여 종료 `tbdown` 합니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. 이제를 사용 하 여 Tibero `tbboot` 를 부팅 합니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. 테이블 스페이스를 만들려면 SYS user (sys/tmax)를 사용 하 여 데이터베이스에 액세스 한 다음 기본 볼륨 및 TACF에 필요한 테이블 스페이스를 만듭니다.

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. 이제 다음 SQL 명령을 입력 합니다.

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Tibero를 부팅 하 고 Tibero 프로세스가 실행 중인지 확인 합니다.

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

출력:

![Tibero 출력](media/tibero-01.png)

## <a name="install-odbc"></a>ODBC 설치

OpenFrame의 응용 프로그램은 오픈 소스 전체 Xodbc 프로젝트에서 제공 하는 ODBC API를 사용 하 여 Tibero 데이터베이스와 통신 합니다.

ODBC를 설치 하려면:

1. UnixODBC-2.3.4 release.tar.gz installer 파일이 있는지 확인 하거나 `wget unixODBC-2.3.4.tar.gz` 명령을 사용 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. 이진 파일의 압축을 풉니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. 2.3.4 디렉터리로 이동 하 고 컴퓨터 정보 확인을 사용 하 여 메이크파일을 생성 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     기본적으로는/local에 `--prefix` 값을 전달 하 여 위치를 변경 하는 값을 전달 합니다. 마찬가지로 구성 파일은 기본적으로/등으로 설치 되므로 `--sysconfdir` 원하는 위치의 값을 전달 합니다.

4. 메이크파일 실행:`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. 컴파일한 후 프로그램 디렉터리에서 실행 파일을 복사 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Vi를 사용 하 여 bash 프로필 ()을 편집 하 `vi ~/.bash_profile` 고 다음을 추가 합니다.

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. ODBC를 적용 합니다. 다음 파일을 적절 하 게 편집 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. 기호화 된 링크를 만들고 Tibero 데이터베이스 연결의 유효성을 검사 합니다.

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

다음 출력이 표시됩니다.

![SQL에 연결 된 ODBC 출력](media/odbc-01.png)

## <a name="install-openframe-base"></a>OpenFrame 기반 설치

기본 응용 프로그램 서버는 OpenFrame에서 트랜잭션 처리 서버 프로세스를 비롯 하 여 Azure에서 시스템을 관리 하는 데 사용 하는 개별 서비스 보다 먼저 설치 됩니다.

**OpenFrame Base를 설치 하려면**

1. Tibero 설치에 성공 했는지 확인 하 고 다음 OpenFrame \_ Base7 \_ 0 \_ Linux \_ x86 \_ 64. bin 설치 관리자 파일 및 기본 속성 구성 파일이 있는지 확인 합니다.

2. 다음 Tibero 관련 정보를 사용 하 여 bash 프로필을 업데이트 합니다.

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Bash 프로필을 실행 합니다.`[oframe7@ofdemo ~]$ . .bash_profile`
4. Tibero 프로세스가 실행 되 고 있는지 확인 합니다. 예를 들면 다음과 같습니다.

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![기준](media/base-01.png)

     > [!IMPORTANT]
     > 설치 하기 전에 Tibero를 시작 해야 합니다.

5. [Technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) 에서 라이선스를 생성 하 고 해당 폴더에 Openframe Base, BATCH, TACF, .osc 라이선스를 넣습니다.

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. OpenFrame 기본 이진 및 기본. 속성 파일을 다운로드 합니다.

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. 기본. 속성 파일을 사용 하 여 설치 관리자를 실행 합니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    완료 되 면 설치 완료 메시지가 꽉 재생 됩니다.

8. 명령을 사용 하 여 OpenFrame 기본 디렉터리 구조를 확인 합니다 `ls -ltr` . 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. OpenFrame 기반 시작:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot 명령 출력](media/base-02.png)

10. Si에서 tmadmin 명령을 사용 하 여 프로세스 상태가 준비 됨 인지 확인 합니다. RDY는 각 프로세스에 대 한 **상태** 열에 표시 됩니다.

     ![tmadmin 명령 출력](media/base-03.png)

11. OpenFrame Base 종료:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>OpenFrame 배치 설치

OpenFrame 일괄 처리는 메인프레임 일괄 처리 환경을 시뮬레이트하는 여러 구성 요소로 구성 되며 Azure에서 batch 작업을 실행 하는 데 사용 됩니다.

**일괄 처리를 설치 하려면**

1. 기본 설치에 성공 했는지 확인 하 고 OpenFrame \_ Batch7 \_ 0 \_ Fix2 \_ mvs \_ Linux \_ x86 \_ 64. bin 설치 관리자 파일 및 batch 구성 파일이 있는지 확인 합니다.

2. 명령 프롬프트에서를 입력 `vi batch.properties` 하 여 vi를 사용 하는 batch. 속성 파일을 편집 합니다.

3. 다음과 같이 매개 변수를 수정 합니다.

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Batch 설치 관리자를 실행 하려면 명령 프롬프트에서 다음을 입력 합니다.

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. 설치가 완료 되 면 명령 프롬프트에서를 입력 하 여 설치 된 OpenFrame 제품군을 시작 합니다 `tmboot` .

    ![tmboot 출력](media/tmboot-01.png)

6. `tmadmin`명령 프롬프트에서를 입력 하 여 OpenFrame 프로세스를 확인 합니다.

    ![Tmax 관리 화면](media/tmadmin-01.png)

7. 다음 명령을 실행합니다.

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. 명령을 사용 `tmdown` 하 여 일괄 처리를 시작 하 고 종료 합니다.

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>TACF 설치

TACF Manager는 RACF security를 통해 시스템 및 리소스에 대 한 사용자 액세스를 제어 하는 OpenFrame 서비스 모듈입니다.

**TACF를 설치 하려면**

1. OpenFrame \_ Tacf7 \_ 0 \_ Fix2 \_ Linux \_ x86 \_ 64. bin 설치 관리자 파일 및 tacf 구성 파일이 있는지 확인 합니다.
2. 일괄 설치가 성공 했는지 확인 한 다음 vi를 사용 하 여 tacf 파일 ()을 엽니다 `vi tacf.properties` .
3. TACF 매개 변수를 수정 합니다.

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. TACF 설치 관리자를 완료 한 후 TACF 환경 변수를 적용 합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     source \~/.bash\_profile
     ```

5. TACF 설치 관리자를 실행 합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     출력은 다음과 같습니다.

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. 명령 프롬프트에서 `tmboot` 를 입력 하 여 OpenFrame을 다시 시작 합니다. 출력은 다음과 같습니다.

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. 명령에서를 사용 하 여 프로세스 상태를 준비 했는지 확인 `tmadmin` `si` 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     **상태** 열에 rdy가 표시 됩니다.

    ![상태 열의 RDY](media/tmboot-02.png)

8. 다음 명령을 실행합니다.

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. 명령을 사용 하 여 서버를 종료 `tmdown` 합니다. 출력은 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>ProSort 설치

ProSort는 일괄 처리 트랜잭션에서 데이터를 정렬 하는 데 사용 되는 유틸리티입니다.

**ProSort를 설치 하려면**

1. 일괄 설치에 성공 했는지 확인 한 다음 **prosort-bin-prosort \_ 2sp3-linux64-2123-opt. release.tar.gz** 설치 관리자 파일이 있는지 확인 합니다.

2. 속성 파일을 사용 하 여 설치 관리자를 실행 합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Prosort 디렉터리를 홈 위치로 이동 합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. 라이선스 하위 디렉터리를 만들고 여기에 라이선스 파일을 복사 합니다. 예를 들면 다음과 같습니다.

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Vi ()에서 bash 프로필을 열고 다음과 `vi .bash_profile` 같이 업데이트 합니다.

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Bash 프로필을 실행 하려면 명령 프롬프트에서 다음을 입력 합니다.`. .bash_profile`

7. 구성 파일을 만듭니다. 예를 들면 다음과 같습니다.

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. 기호화 된 링크를 만듭니다. 예를 들면 다음과 같습니다.

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. 명령을 실행 하 여 ProSort 설치를 확인 합니다 `prosort -h` . 예를 들면 다음과 같습니다.

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>OFCOBOL 설치

OFCOBOL은 메인프레임의 COBOL 프로그램을 해석 하는 OpenFrame 컴파일러입니다. 

**OFCOBOL을 설치 하려면**

1. 일괄 처리/온라인 설치에 성공 했는지 확인 한 후 OpenFrame \_ COBOL3 \_ 0 \_ 40 \_ Linux \_ x86 \_ 64. m 설치 관리자 파일이 있는지 확인 합니다.

2. OFCOBOL 설치 관리자를 실행 하려면 명령 프롬프트에서 다음을 입력 합니다.

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. 사용권 계약을 읽고 Enter 키를 눌러 계속 합니다.

4. 라이선스 계약에 동의합니다. 설치가 완료 되 면 다음이 표시 됩니다.

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Vi ()에서 bash 프로필을 열고 `vi .bash_profile` 이 OFCOBOL 변수로 업데이트 되었는지 확인 합니다.
6. Bash 프로필을 실행 합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
      source ~/.bash_profile
     ```

7. OFCOBOL 라이선스를 설치 된 폴더에 복사 합니다. 예를 들면 다음과 같습니다.
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. OpenFrame tjclrun 구성 파일로 이동 하 여 vi에서 엽니다. 예를 들면 다음과 같습니다.
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   변경 전 SYSLIB 섹션은 다음과 같습니다.
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   변경 후 SYSLIB 섹션은 다음과 같습니다.
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Vi의 OpenFrame \_ COBOL \_ installlog 파일을 검토 하 고 오류가 없는지 확인 합니다. 예를 들면 다음과 같습니다.
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. 명령을 사용 하 `ofcob --version` 고 버전 번호를 검토 하 여 설치를 확인 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. 명령을 사용 하 여 OpenFrame을 다시 부팅 `tmdown/tmboot` 합니다.

## <a name="install-ofasm"></a>OFASM 설치

OFASM은 메인프레임의 어셈블러 프로그램을 해석 하는 OpenFrame 컴파일러입니다.

**OFASM을 설치 하려면**

1. 일괄 처리/온라인 설치에 성공 했는지 확인 한 후 OpenFrame \_ ASM3 \_ 0 \_ Linux \_ x86 \_ 64. bin 설치 관리자 파일이 있는지 확인 합니다.

2. 설치 관리자를 실행 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. 사용권 계약을 읽고 Enter 키를 눌러 계속 합니다.
4. 라이선스 계약에 동의합니다.
5. Bash 프로필이 OFASM 변수로 업데이트 되는지 확인 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Vi에서 OpenFrame tjclrun 구성 파일을 열고 다음과 같이 편집 합니다.

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     변경 *전의* [SYSLIB] 섹션은 다음과 같습니다.

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     다음은 변경 된 *후* 의 [SYSLIB] 섹션입니다.

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Vi에서 OpenFrame \_ ASM \_ installlog 파일을 열고 오류가 없는지 확인 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. 다음 명령 중 하나를 실행 하 여 OpenFrame을 다시 부팅 합니다.

     ```
     tmdown / tmboot
     ```

     또는

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>.OSC 설치

.OSC는 고속 OLTP 트랜잭션과 기타 관리 기능을 지 원하는 IBM CICS와 유사한 OpenFrame 환경입니다.

**.OSC를 설치 하려면**

1. 기본 설치에 성공 했는지 확인 한 후 OpenFrame \_ OSC7 \_ 0 \_ Fix2 \_ Linux \_ x86 \_ 64. bin 설치 관리자 파일 및 .osc 구성 파일이 있는지 확인 합니다.
2. .Osc 속성 파일에서 다음 매개 변수를 편집 합니다.
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. 다음과 같이 속성 파일을 사용 하 여 설치 관리자를 실행 합니다.

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     완료 되 면 "설치 완료" 메시지가 표시 됩니다.

4. Bash 프로필이 .OSC 변수로 업데이트 되는지 확인 합니다.
5. OpenFrame \_ OSC7 \_ 0 \_ Fix2 \_ installlog .log 파일을 검토 합니다. 다음과 같이 표시됩니다.

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Vi를 사용 하 여 ofsys. seq 구성 파일을 엽니다. 예를 들면 다음과 같습니다.

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. \#기본 및 \# 배치 섹션에서 표시 된 대로 매개 변수를 편집 합니다.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. 라이선스 파일을 복사 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. .OSC를 시작 하 고 종료 하려면 명령 프롬프트에서를 입력 하 여 CICS 지역 공유 메모리를 초기화 합니다 `osctdlinit OSCOIVP1` .

10. `oscboot`을 실행 하 여 .osc를 부팅 합니다. 출력은 다음과 같습니다.

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. 프로세스 상태가 준비 인지 확인 하려면 `tmadmin` si에서 명령을 사용 합니다. 모든 프로세스는 **상태** 열에 rdy를 표시 해야 합니다.

    ![RDY를 표시 하는 프로세스](media/tmadmin-02.png)

12. 명령을 사용 하 여 .OSC를 종료 `oscdown` 합니다.

## <a name="install-jeus"></a>JEUS 설치

JEUS (Java Enterprise 사용자 솔루션) OpenFrame 웹 응용 프로그램 서버의 프레젠테이션 계층을 제공 합니다.

JEUS를 설치 하기 전에 JEUS을 설치 하는 데 필요한 라이브러리와 명령줄 도구를 제공 하는 Apache Ant 패키지를 설치 합니다.

**Apache Ant를 설치 하려면**

1. 명령을 사용 하 여 Ant binary `wget` 를 다운로드 합니다. 예를 들면 다음과 같습니다.

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. 유틸리티를 사용 `tar` 하 여 이진 파일을 추출 하 고 적절 한 위치로 이동 합니다. 예를 들면 다음과 같습니다.

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. 효율성을 위해 기호화 된 링크를 만듭니다.

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Vi ()에서 bash 프로필을 열고 `vi .bash_profile` 다음 변수를 사용 하 여 업데이트 합니다.

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  수정 된 환경 변수를 적용 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**JEUS를 설치 하려면**

1. 유틸리티를 사용 하 여 설치 관리자를 확장 합니다 `tar` . 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. **Jeus** 폴더 ()를 만들고 `mkdir jeus7` 이진 파일의 압축을 풉니다.
3. **설치** 디렉터리로 변경 하거나 사용자 환경에 JEUS 매개 변수를 사용 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. `ant clean-all`빌드를 수행 하기 전에를 실행 합니다. 출력은 다음과 같습니다.

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  도메인 구성-템플릿. 속성 파일의 백업을 만듭니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Vi에서 도메인 구성-템플릿. 속성 파일을 엽니다.

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. `jeus.password=jeusadmin nodename=Tmaxsoft`를 `jeus.password=tmax1234 nodename=ofdemo`로 변경

8. 명령을 실행 `ant install` 하 여 JEUS를 빌드합니다.
9.  \_다음과 같이 JEUS 변수를 사용 하 여 bash 프로필 파일을 업데이트 합니다.

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Bash 프로필을 실행 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *선택 사항입니다*. JEUS 구성 요소를 쉽게 종료 하 고 부팅 하기 위한 별칭을 만듭니다.

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. 설치를 확인 하려면 다음과 같이 도메인 관리 서버를 시작 합니다.

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. 구문을 사용 하 여 웹 로그온으로 확인 합니다.

     ```
     http://<IP>:<port>/webadmin/login
     ```

     예를 들어 <http://192.168.92.133:9736/webadmin/login.> 로그온 화면이 표시 됩니다.
    
     ![JEUS WebAdmin 로그온 화면](media/jeus-01.png)

     > [!NOTE]
     > 포트 보안 관련 문제가 발생 하는 경우 포트 9736을 열거나 방화벽을 사용 하지 않도록 설정 `systemctl stop firewall` 합니다 ().

14. Server1에 대 한 호스트 이름을 변경 하려면 **& 편집**을 클릭 한 다음 **server1**을 클릭 합니다. 서버 창에서 다음과 같이 호스트 이름을 변경 합니다.

    1.  **Nodename** 을 **ofdemo**로 변경 합니다.
    2.  창의 오른쪽에서 **확인을** 클릭 합니다.
    3.  창의 왼쪽 아래에 있는 **변경 내용 적용** 을 클릭 하 고 설명에 *호스트 이름 변경*을 입력 합니다.

    ![JEUS WebAdmin 화면](media/jeus-02.png)

15. 확인 화면에서 구성이 성공적인 지 확인 합니다.

    ![jeus_domain 서버 화면](media/jeus-03.png)

16. 다음 명령을 사용 하 여 "server1" 관리 되는 서버 프로세스를 시작 합니다.

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>OFGW 설치

OFGW는 3270 터미널 에뮬레이터와 OSI 기반 간의 통신을 지원 하 고 터미널 에뮬레이터와 OSI 간의 세션을 관리 하는 OpenFrame 게이트웨이입니다.

**OFGW를 설치 하려면**

1. JEUS이 성공적으로 설치 되었는지 확인 한 다음 OFGW7 \_ 0 \_ 1 \_ 일반. bin 설치 관리자 파일이 있는지 확인 합니다.
2. 설치 관리자를 실행 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. 해당 프롬프트에 다음 위치를 사용 합니다.
     -   JEUS 홈 디렉터리
     -   JEUS 도메인 이름
     -   JEUS 서버 이름
     -   Tibero 드라이버
     -   Tmax 노드 ID ofdemo

4. 나머지 기본값을 적용 한 다음 Enter 키를 눌러 설치 관리자를 종료 합니다.

5. OFGW에 대 한 URL이 예상 대로 작동 하는지 확인 합니다.

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     다음 화면이 나타납니다.

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>OFManager 설치

OFManager는 웹 환경에서 OpenFrame의 작업 및 관리 기능을 제공 합니다.

**OFManager를 설치 하려면**

1. OFManager7 \_ 설치 관리자 파일이 있는지 확인 합니다.
2. 설치 관리자를 실행 합니다. 예를 들면 다음과 같습니다.

     ```
     OFManager7_Generic.bin
     ```

3.  Enter 키를 눌러 계속 하 고 사용권 계약에 동의 합니다.
4.  설치 폴더를 선택 합니다.
5.  기본값을 그대로 적용합니다.
6.  데이터베이스로 Tibero를 선택 합니다.
7.  Enter 키를 눌러 설치 관리자를 종료 합니다.
8.  OFManager의 URL이 정상적으로 작동 하는지 확인 합니다.

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

시작 화면이 나타납니다.

![Tmax OpenFrame Manager 로그온 화면](media/ofmanager-01.png)

그러면 OpenFrame 구성 요소의 설치가 완료 됩니다.

## <a name="next-steps"></a>다음 단계

메인프레임 마이그레이션을 고려 하는 경우 확장 된 파트너 에코 시스템을 통해 도움을 얻을 수 있습니다. 파트너 솔루션 선택과 관련한 상세 지침은 [Platform Modernization Alliance](https://datamigration.microsoft.com/)를 참조하세요.

-   [Azure 시작](../../../../index.yml)
-   [Host Integration Server(HIS) 설명서](/host-integration-server/)
-   [Azure 가상 데이터 센터 리프트 및 이동 가이드](/archive/blogs/azurecat/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide)
