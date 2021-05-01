---
title: Azure Virtual Machines에 TmaxSoft OpenFrame 설치
description: 개발, 데모, 테스트 또는 프로덕션 워크로드에 적합한 Azure에서 OpenFrame 환경을 설정하는 방법을 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 2ca5f0311102c27c1233098a18ff16701b99ea2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950793"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Azure에 TmaxSoft OpenFrame 설치

개발, 데모, 테스트 또는 프로덕션 워크로드에 적합한 Azure에서 OpenFrame 환경을 설정하는 방법을 알아봅니다. 이 자습서에서는 각 단계를 안내합니다.

OpenFrame에는 Azure에서 메인프레임 에뮬레이션 환경을 만드는 여러 구성 요소가 포함됩니다. 예를 들어 OpenFrame 온라인 서비스는 IBM CICS(고객 정보 제어 시스템) 같은 메인프레임 미들웨어를 대체하고, OpenFrame Batch는 TJES 구성 요소를 이용해 IBM 메인프레임의 JES(작업 항목 하위 시스템)를 대체합니다.

OpenFrame은 Oracle Database, Microsoft SQL Server, IBM Db2 및 MySQL을 포함한 모든 관계형 데이터베이스에서 작동합니다. 이 OpenFrame 설치는 TmaxSoft Tibero 관계형 데이터베이스를 사용합니다. OpenFrame과 Tibero는 모두 Linux 운영 체제에서 실행됩니다. 다른 지원되는 Linux 배포를 사용할 수 있지만 이 자습서에서는 CentOS 7.3을 설치합니다. OpenFrame 애플리케이션 서버와 Tibero 데이터베이스는 하나의 VM(가상 머신)에 설치됩니다.

이 자습서에서는 OpenFrame 도구 모음 구성 요소를 설치하는 단계를 안내합니다. 일부는 별도로 설치해야 합니다.

주 OpenFrame 구성 요소는 다음과 같습니다.

- 필수 설치 패키지.
- Tibero 데이터베이스.
- ODBC(Open Database Connectivity)는 OpenFrame의 애플리케이션에서 Tibero 데이터베이스와 통신하는 데 사용됩니다.
- OpenFrame Base는 전체 시스템을 관리하는 미들웨어입니다.
- OpenFrame Batch는 메인프레임 일괄 처리 시스템을 대체하는 솔루션입니다.
- TACF는 시스템 및 리소스의 사용자 액세스를 제어하는 서비스 모듈입니다.
- ProSort는 일괄 처리 트랜잭션용 정렬 도구입니다.
- OFCOBOL은 메인프레임의 COBOL 프로그램을 해석하는 컴파일러입니다.
- OFASM은 메인프레임의 어셈블러 프로그램을 해석하는 컴파일러입니다.
- OSC(OpenFrame 서버 유형 C)는 메인프레임 미들웨어 및 IBM CICS를 대체하는 솔루션입니다.
- JEUS(Java Enterprise User Solution)는 Java Enterprise Edition 6용으로 인증된 웹 애플리케이션 서버입니다.
- OFGW(OpenFrame 게이트웨이 구성 요소)는 3270 수신기를 제공합니다.
- OFManager는 웹 환경에서 OpenFrame의 작업 및 관리 기능을 제공하는 솔루션입니다.

기타 필수 OpenFrame 구성 요소는 다음과 같습니다.

- OSI는 메인프레임 미들웨어와 IMS DC를 대체하는 솔루션입니다.
- TJES는 메인프레임의 JES 환경을 제공하는 솔루션입니다.
- OFTSAM은 (V)SAM 파일을 오픈 시스템에서 사용할 수 있도록 하는 솔루션입니다.
- OFHiDB는 메인프레임의 IMS DB를 대체하는 솔루션입니다.
- OFPLI는 메인프레임의 PL/I 프로그램을 해석하는 컴파일러입니다.
- PROTRIEVE는 메인프레임 언어 CA-Easytrieve를 실행하는 솔루션입니다.
- OFMiner는 메인프레임 자산을 분석한 후 Azure로 마이그레이션하는 솔루션입니다.

## <a name="architecture"></a>Architecture

다음 그림은 이 자습서에 설치된 OpenFrame 7.0 아키텍처 구성 요소에 대한 개요를 설명합니다.

![OpenFrame 구성 요소](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure 시스템 요구 사항

다음 표에는 Azure 설치의 요구 사항이 정리되어 있습니다.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>요구 사항</th><th>Description</th></tr>
</thead>
<tbody>
<tr><td>Azure에서 지원되는 Linux 배포
</td>
<td>
Linux x86 2.6(32비트, 64비트)<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>하드웨어
</td>
<td>코어: 2(최소)<br/>
메모리: 4GB(최소)<br/>
스왑 공간: 1GB(최소)<br/>
하드 디스크: 100GB(최소)<br/>
</td>
</tr>
<tr><td>Windows 사용자를 위한 선택적 소프트웨어
</td>
<td>PuTTY: 이 가이드에서 VM 기능을 구성하는 데 사용<br/>
WinSCP: 유명한 SFTP 클라이언트 및 FTP 클라이언트<br/>
Windows용 Eclipse: TmaxSoft에서 지원하는 개발 플랫폼<br/>
(Microsoft Visual Studio는 현재 지원되지 않음)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>필수 구성 요소

필요한 모든 소프트웨어를 조합하고 모든 수동 프로세스를 완료하는 데 며칠 정도 계획을 짭니다.

시작하기 전에 다음을 수행해야 합니다.

- TmaxSoft에서 OpenFrame 설치 미디어를 가져옵니다. 기존 TmaxSoft 고객인 경우 사용이 허가된 복사본에 대해 TmaxSoft 담당자에게 문의하세요. 또는 [Tmaxsoft](https://www.tmaxsoft.com/contact/)에 평가판을 요청합니다.

- <support@tmaxsoft.com>에 메일을 보내 OpenFrame 설명서를 요청합니다.

- 아직 없는 경우 Azure 구독을 신청합니다. 시작하기 전에 [체험 계정을](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 만들 수도 있습니다.

- 선택 사항입니다. Azure VM에 대한 액세스를 조직 내 허용된 사용자로 제한하는 사이트 간 VPN 터널 또는 jumpbox를 설정합니다. 이 단계는 필수는 아니지만 모범 사례입니다.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>OpenFrame 및 Tibero에 대해 Azure에서 VM 설정

다양한 배포 패턴을 사용하여 OpenFrame 환경을 설정할 수 있지만 다음 프로시저에서는 한 VM에 OpenFrame 애플리케이션 서버와 Tibero 데이터베이스를 배포하는 방법을 설명합니다. 대규모 환경 및 대규모 워크로드의 경우 성능 향상을 위해 데이터베이스를 자체 VM에 개별적으로 배포하는 것이 모범 사례입니다.

**VM을 생성하려면**

1. <https://portal.azure.com>에서 Azure Portal로 이동하고, 계정에 로그인합니다.

2. **가상 머신** 을 클릭합니다.

    ![Azure Portal의 리소스 목록](media/vm-01.png)

3. **추가** 를 클릭합니다.

    ![Azure Portal에서 옵션 추가](media/vm-02.png)

4. **운영 체제** 의 오른쪽에서 **자세히** 를 클릭합니다.

     ![Azure Portal의 추가 옵션](media/vm-03.png)

5. **CentOS-based 7.3** 을 클릭하여 이 연습을 정확하게 수행하거나 지원되는 다른 Linux 배포를 선택할 수 있습니다.

     ![Azure Portal의 운영 체제 옵션](media/vm-04.png)

6. **기본** 설정에서 **이름**, **사용자 이름**, **인증 유형**, **구독**(종량제는 AWS 형식의 요금제입니다) 및 **리소스 그룹**(기존 항목 사용 또는 TmaxSoft 그룹 만들기)을 입력합니다.

7. **인증 유형** 에 대한 공개/프라이빗 키 쌍을 포함하여 작업이 완료되면 **제출** 을 클릭합니다.

> [!NOTE]
> **인증 유형** 에 SSH 공개 키를 사용하는 경우 다음 섹션의 단계를 참조하여 공개/프라이빗 키 쌍을 생성한 후 여기서부터 단계를 계속합니다.

### <a name="generate-a-publicprivate-key-pair"></a>공개/프라이빗 키 쌍을 생성합니다.

Windows 운영 체제를 사용하는 경우 공개/프라이빗 키 쌍을 생성하려면 PuTTYgen가 필요합니다.

공개 키는 자유롭게 공유할 수 있지만 프라이빗 키는 완전히 비밀로 유지되어야 하며 다른 당사자와 공유해서는 안 됩니다. 키를 생성한 후에는 **SSH 공개 키** 를 구성에 붙여넣어 실제로 Linux VM에 업로드해야 합니다. 이는 사용자 계정의 홈 디렉터리에 있는/.ssh 디렉터리 내에서 권한 있는 \_키 안에\~ 저장됩니다. 그런 다음 Linux VM은 연결된 **SSH 프라이빗 키** 를 SSH 클라이언트(이 경우 PuTTY)에 제공하여 연결을 인식하고 유효성을 검사할 수 있습니다.

새 개인에게 VM 액세스 권한을 부여하는 경우. 

- 각각의 새 개인은 PuTTYgen을 사용하여 자신의 공개/프라이빗 키를 생성합니다.
- 개인은 개별적으로 프라이빗 키를 저장하고 VM의 관리자에게 공개 키 정보를 보냅니다.
- 관리자는 공개 키의 콘텐츠를 \~/.ssh/authorized\_keys 파일에 붙여넣습니다.
- 새 개인은 PuTTY를 통해 연결됩니다.

**공개/프라이빗 키 쌍을 생성하려면**

1.  PuTTYgen을 <https://www.putty.org/>에서 다운로드하고 모든 기본 설정을 사용하여 설치합니다.

2.  PuTTYgen을 열려면 PuTTY 설치 디렉터리를 C:\\Program Files\\PuTTY에 위치시킵니다.

    ![PuTTY 인터페이스](media/puttygen-01.png)

3.  **생성** 을 클릭합니다.

    ![PuTTY 키 생성기 대화 상자를 보여 주고 생성 단추를 강조 표시 하는 스크린샷.](media/puttygen-02.png)

4.  생성 후 공개 키와 프라이빗 키를 모두 저장합니다. **가상 머신 만들기 \> 기본 사항** 창의 **SSH 공개 키** 섹션(이전 섹션의 6단계 및 7단계에 표시됨)에 공개 키의 내용을 붙여넣습니다.

    ![PuTTY 키 생성기 대화 상자](media/puttygen-03.png)

### <a name="configure-vm-features"></a>VM 기능 구성

1. Azure Portal의 **크기 선택** 블레이드에서 원하는 Linux 머신 하드웨어 설정을 선택합니다. Tibero 및 OpenFrame을 모두 설치하기 위한 *최소* 요구 사항은 예제 설치에 표기된 것과 같이 CPU 2개, 4GB RAM입니다.

    ![가상 머신 만들기 - 기본 사항](media/create-vm-01.png)

2. **3개의 설정** 을 클릭하고 기본 설정을 사용하여 선택적 기능을 구성합니다.
3. 지불 세부 정보를 검토합니다.

    ![가상 머신 만들기 - 구매](media/create-vm-02.png)

4. 선택 항목을 제출합니다. Azure에서 VM 배포를 시작합니다. 이 프로세스에는 일반적으로 몇 분 정도 걸립니다.

5. VM을 배포하면 구성 중에 선택한 모든 설정이 표시되는 대시보드가 표시됩니다. **공용 IP 주소** 를 기록해둡니다.

    ![Azure 대시보드의 tmax](media/create-vm-03.png)

6. PuTTY를 엽니다.

7. **호스트 이름** 에 사용자 이름과 복사한 공용 IP 주소를 입력합니다. 예: **username\@publicip**.

    ![PuTTY 구성 대화 상자를 표시하고 호스트 이름(또는 IP 주소) 필드를 강조 표시하는 스크린샷.](media/putty-01.png)

8. **범주** 상자에서 **연결 \> SSH \> 인증** 을 클릭합니다. **프라이빗 키** 파일의 경로를 제공합니다.

    ![PuTTY 구성 대화 상자](media/putty-02.png)

9. **열기** 를 클릭하여 PuTTY 창을 시작합니다. 성공하면 Azure에서 실행되는 새 CentOS VM에 연결됩니다.

10. 루트 사용자로 로그온 하려면 **sudo bash** 를 입력합니다.

    ![명령 창의 루트 사용자 로그온](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>환경 및 패키지 설정

이제 VM을 만들고 로그온을 했으므로 몇 가지 설정 단계를 수행하고 필수 사전 설치 패키지를 설치해야 합니다.

1. VI를 사용해 호스트 파일(`vi /etc/hosts`)을 편집하여 **ofdemo** 이름을 로컬 IP 주소에 매핑합니다. IP를 192.168.96.148 ofdemo로 가정하면 이 과정은 변경되기 전에 수행됩니다.

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

2. 그룹 및 사용자 만들기.

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. 사용자 oframe7의 암호를 변경합니다.

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. /etc/sysctl.conf에서 커널 매개 변수를 업데이트합니다.

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. 다시 부팅하지 않고 커널 매개 변수를 동적으로 새로 고칩니다.

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. 필요한 패키지 가져오기: 서버가 인터넷에 연결되어 있는지 확인하고 다음 패키지를 다운로드한 후 설치합니다.

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > ncurses 패키지를 설치한 후 다음의 기호화된 링크를 만듭니다.
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. Java RPM 설치의 경우 다음을 수행합니다.

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

## <a name="install-the-tibero-database"></a>Tibero 데이터베이스를 설치합니다

Tibero는 Azure의 OpenFrame 환경에서 몇 가지 주요 기능을 제공합니다.

- Tibero는 다양한 시스템 함수에 대한 OpenFrame 내부 데이터 저장소로 사용됩니다.
- KSDS, RRDS 및 ESDS를 비롯한 VSAM 파일은 데이터 스토리지에 대해 내부적으로 Tibero 데이터베이스를 사용합니다.
- TACF 데이터 리포지토리는 Tibero에 저장됩니다.
- OpenFrame 카탈로그 정보는 Tibero에 저장됩니다.
- Tibero 데이터베이스는 IBM Db2를 대체하여 애플리케이션 데이터를 저장할 수 있습니다.

**Tibero를 설치하려면**

1. Tibero 바이너리 설치 프로그램 파일이 있는지 확인하고 버전 번호를 검토합니다.
2. Tibero 소프트웨어를 Tibero 사용자 계정(oframe)에 복사합니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. VI(`vi .bash_profile`)에서 .bash\_ 프로필을 열고 다음 내용을 붙여넣습니다.

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Bash 프로필을 실행하려면 명령 프롬프트에서 다음 내용을 입력합니다.

    ```
    source .bash_profile
    ```

5. 팁 파일(Tibero의 구성 파일)을 생성한 다음 VI에서 엽니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. \$TB\_HOME/client/config/tbdsn.tbr을 수정해서 아래와 같이 로컬 호스트 대신 127.0.0.1을 추가합니다.

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

8. Tibero를 재활용하려면 먼저 `tbdown` 명령을 사용하여 종료합니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. 이제 `tbboot`를 사용하여 Tibero를 부팅합니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. 테이블스페이스를 만들려면 SYS 사용자(sys/tmax)를 사용하여 데이터베이스에 액세스한 다음 기본 볼륨 및 TACF에 필요한 테이블스페이스를 만듭니다.

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. 이제 다음 SQL 명령을 입력합니다.

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Tibero를 부팅하고 Tibero 프로세스가 실행 중인지 확인합니다.

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

출력:

![Tibero 출력](media/tibero-01.png)

## <a name="install-odbc"></a>ODBC 설치

OpenFrame의 애플리케이션은 오픈 소스 unixODBC 프로젝트에서 제공하는 ODBC API를 사용해 Tibero 데이터베이스와 통신합니다.

ODBC를 설치하려면 다음 절차를 따릅니다.

1. unixODBC-2.3.4.tar.gz 설치 프로그램 파일이 있는지 확인하거나 `wget unixODBC-2.3.4.tar.gz` 명령을 사용합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. 바이너리 파일의 압축을 풉니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. unixODBC-2.3.4 디렉터리로 이동하고 머신 정보 확인을 사용하여 메이크파일을 생성합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     기본적으로 unixODBC는 /usr /local에 설치되므로 `--prefix`이 값을 전달하여 위치를 변경합니다. 마찬가지로 구성 파일은 기본적으로 /etc에 설치되므로 `--sysconfdir`이 원하는 위치의 값을 전달합니다.

4. 메이크파일 실행: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. 컴파일한 후 프로그램 디렉터리에서 실행 파일을 복사합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. VI를 사용하여 bash 프로필(`vi ~/.bash_profile`)을 편집하고 다음을 추가합니다.

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. ODBC를 적용합니다. 다음 파일을 적절하게 편집합니다. 예를 들면 다음과 같습니다.

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

8. 바로 가기 링크를 만들고 Tibero 데이터베이스 연결의 유효성을 검사합니다.

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

다음 출력이 표시됩니다.

![SQL에 연결된 ODBC 출력](media/odbc-01.png)

## <a name="install-openframe-base"></a>OpenFrame Base 설치

Base 애플리케이션 서버는 트랜잭션 처리 서버 프로세스를 비롯하여 OpenFrame이 Azure에서 시스템을 관리하는 데 사용하는 개별 서비스보다 먼저 설치됩니다.

**OpenFrame Base를 설치하려면**

1. Tibero 설치가 성공했는지 확인하고 다음 OpenFrame\_Base7\_0\_Linux\_x86\_64.bin 설치 프로그램 파일 및 기본 속성 구성 파일이 있는지 확인합니다.

2. 다음 Tibero 관련 정보를 이용해 bash 프로필을 업데이트합니다.

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

3. Bash 프로필을 실행합니다. `[oframe7@ofdemo ~]$ . .bash_profile`
4. Tibero 프로세스가 실행되고 있는지 확인합니다. 예를 들면 다음과 같습니다.

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![기준](media/base-01.png)

     > [!IMPORTANT]
     > 설치하기 전에 Tibero를 가동했는지 확인합니다.

5. [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do)에서 라이선스를 생성하고 해당 폴더에 OpenFrame Base, Batch, TACF, OSC 라이선스를 입력합니다.

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. OpenFrame Base 바이너리 및 base.properties 파일을 다운로드합니다.

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

7. base.properties 파일을 사용하여 설치 프로그램을 실행합니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    완료되면 설치 완료 메시지가 표시됩니다.

8. `ls -ltr` 명령을 사용해 OpenFrame Base 디렉터리 구조를 확인합니다. 예를 들면 다음과 같습니다.

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

9. OpenFrame Base 시작:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot 명령 출력](media/base-02.png)

10. SI에서 tmadmin 명령을 사용해 프로세스 상태가 준비되었는지 확인합니다. RDY는 각 프로세스에 대한 **상태** 열에 표시됩니다.

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

## <a name="install-openframe-batch"></a>OpenFrame Batch 설치

OpenFrame Batch는 메인프레임 일괄 처리 환경을 시뮬레이트하는 여러 구성 요소로 구성되며 Azure에서 일괄 처리 작업을 실행하는 데 사용됩니다.

**Batch를 설치하려면**

1. 기본 설치가 성공했는지 확인하고 OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_64.bin 설치 프로그램 파일 및 batch.properties 구성 파일이 있는지 확인합니다.

2. 명령 프롬프트에서 `vi batch.properties`를 입력해 VI를 사용하는 batch.properties 파일을 편집합니다.

3. 다음과 같이 매개 변수를 편집합니다.

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

4. 일괄 처리 설치 프로그램을 실행하려면 명령 프롬프트에서 다음을 입력합니다.

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. 설치가 완료되면 명령 프롬프트에서 `tmboot`를 입력해 설치된 OpenFrame 도구 모음을 실행합니다.

    ![tmboot 출력](media/tmboot-01.png)

6. 명령 프롬프트에서 `tmadmin`를 입력해 OpenFrame 프로세스를 확인합니다.

    ![Tmax 관리자 화면](media/tmadmin-01.png)

7. 다음 명령을 실행합니다.

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. `tmdown` 명령을 사용하여 Batch를 시작하고 종료합니다.

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

TACF Manager는 RACF 보안을 통해 시스템 및 리소스에 대한 사용자 액세스를 제어하는 OpenFrame 서비스 모듈입니다.

**TACF를 설치하려면**

1. OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_64.bin 설치 프로그램 파일 및 tacf.properties 구성 파일이 있는지 확인합니다.
2. Batch 설치가 성공했는지 확인한 다음 VI를 사용해 tacf.propertie파일(`vi tacf.properties`)을 엽니다.
3. TACF 매개 변수를 수정합니다.

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. TACF 설치 프로그램을 완료한 후 TACF 환경 변수를 적용합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     source \~/.bash\_profile
     ```

5. TACF 설치 프로그램을 실행합니다. 명령 프롬프트에 다음을 입력합니다.

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

6. 명령 프롬프트에서 `tmboot`를 입력해 OpenFrame을 다시 시작합니다. 출력은 다음과 같습니다.

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

7. `si` 명령에서 `tmadmin`를 사용해 프로세스 상태가 준비되었는지 확인합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     **상태** 열에 RDY가 다음과 같이 표시됩니다.

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

9. `tmdown` 명령을 사용하여 서버를 종료합니다. 출력은 다음과 같습니다.

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

ProSort는 일괄 처리 트랜잭션에서 데이터를 정렬하는 유틸리티입니다.

**ProSort를 설치하려면**

1. Batch 설치가 성공했는지 확인한 다음 **prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz** 설치 프로그램 파일이 있는지 확인합니다.

2. 속성 파일을 사용해 설치 프로그램을 실행합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. prosort 디렉터리를 홈 위치로 이동합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. 라이선스 하위 디렉터리를 만들고 여기에 라이선스 파일을 복사합니다. 예를 들면 다음과 같습니다.

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. VI(`vi .bash_profile`)에서 bash.profile을 열고 다음과 같이 업데이트합니다.

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

6. Bash 프로필을 실행하려면 명령 프롬프트에서 `. .bash_profile`을 입력합니다.

7. 구성 파일을 생성합니다. 예를 들면 다음과 같습니다.

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. 바로 가기 링크를 만듭니다. 예를 들면 다음과 같습니다.

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. `prosort -h` 명령을 실행해 ProSort 설치를 확인합니다. 예를 들면 다음과 같습니다.

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

OFCOBOL은 메인프레임의 COBOL 프로그램을 해석하는 OpenFrame 컴파일러입니다. 

**OFCOBOL을 설치하려면**

1. Batch/Online 설치가 성공했는지 확인한 후 OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin 설치 프로그램 파일이 있는지 확인합니다.

2. OFCOBOL 설치 프로그램을 실행하려면 명령 프롬프트에 다음을 입력합니다.

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. 라이선스 계약을 읽고 Enter 키를 눌러 계속 진행합니다.

4. 라이선스 계약에 동의합니다. 설치가 완료되면 다음 내용이 표시됩니다.

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

5. VI(`vi .bash_profile`)에서 Bash 프로필을 열고 이 OFCOBOL 변수로 업데이트가 되었는지 확인합니다.
6. Bash 프로필을 실행합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
      source ~/.bash_profile
     ```

7. OFCOBOL 라이선스를 설치된 폴더에 복사합니다. 예를 들면 다음과 같습니다.
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. OpenFrame tjclrun.conf 구성 파일로 이동하여 VI에서 엽니다. 예를 들면 다음과 같습니다.
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
9. VI의 OpenFrame\_COBOL\_InstallLog.log 파일을 검토하고 오류가 없는지 확인합니다. 예를 들면 다음과 같습니다.
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
10. `ofcob --version` 명령을 사용해 버전 번호를 검토하여 설치를 확인합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. `tmdown/tmboot` 명령을 사용해 OpenFrame을 다시 부팅합니다.

## <a name="install-ofasm"></a>OFASM 설치

OFASM은 메인프레임의 어셈블러 프로그램을 해석하는 OpenFrame 컴파일러입니다.

**OFASM을 설치하려면**

1. Batch/Online 설치에 성공했는지 확인한 후 OpenFrame\_ASM3\_0\_Linux\_x86\_64.bin 설치 프로그램 파일이 있는지 확인합니다.

2. 설치 프로그램을 실행합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. 라이선스 계약을 읽고 Enter 키를 눌러 계속 진행합니다.
4. 라이선스 계약에 동의합니다.
5. Bash 프로필이 OFASM 변수로 업데이트되었는지 확인합니다. 예를 들면 다음과 같습니다.

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

6. VI에서 OpenFrame tjclrun.conf 구성 파일을 열고 다음과 같이 편집합니다.

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     변경 *전* 의 [SYSLIB] 섹션은 다음과 같습니다.

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     변경 *후* 의 [SYSLIB] 섹션은 다음과 같습니다.

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. VI에서 OpenFrame\_ASM\_InstallLog.log 파일을 열고 오류가 없는지 확인합니다. 예를 들면 다음과 같습니다.

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

8. 다음 명령 중 하나를 실행하여 OpenFrame을 다시 부팅합니다.

     ```
     tmdown / tmboot
     ```

     또는

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>OSC 설치

OSC는 고속 OLTP 트랜잭션과 기타 관리 기능을 지원하는 IBM CICS와 유사한 OpenFrame 환경입니다.

**OSC를 설치하려면**

1. 기본 설치에 성공했는지 확인한 후 OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_64.bin 설치 프로그램 파일 및 osc.properties 구성 파일이 있는지 확인합니다.
2. osc.properties 파일에서 다음 매개 변수를 편집합니다.
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. 다음과 같이 속성 파일을 사용하여 설치 프로그램을 실행합니다.

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     완료되면 “설치 완료” 메시지가 표시됩니다.

4. Bash 프로필이 OSC 변수로 업데이트되었는지 확인합니다.
5. OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log 파일을 검토합니다. 다음과 같이 표시됩니다.

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. VI를 사용해 ofsys.seq 구성 파일을 엽니다. 예를 들면 다음과 같습니다.

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. \#BASE 및 \#BATCH 섹션에 표시된 대로 매개 변수를 편집합니다.

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

8. 라이선스 파일을 복사합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. OSC를 켜고 끄려면 명령 프롬프트에서 `osctdlinit OSCOIVP1`를 입력해 CICS 지역 공유 메모리를 초기화합니다.

10. `oscboot`을 실행하여 OSC를 부팅합니다. 출력은 다음과 같습니다.

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

11. 프로세스 상태가 준비되었는지 확인하려면 SI에서 `tmadmin` 명령을 사용합니다. 모든 프로세스는 **상태** 열에 RDY를 표시해야 합니다.

    ![RDY를 표시하는 프로세스](media/tmadmin-02.png)

12. `oscdown` 명령으로 OSC를 종료합니다.

## <a name="install-jeus"></a>JEUS 설치

JEUS(Java Enterprise User Solution)는 OpenFrame 웹 애플리케이션 서버의 표시 계층을 제공합니다.

JEUS를 설치하기 전에 JEUS을 설치하는 데 필요한 라이브러리와 명령줄 도구를 제공 하는 Apache Ant 패키지를 설치합니다.

**Apache Ant를 설치하려면**

1. `wget` 명령을 사용해 Ant 바이너리를 다운로드합니다. 예를 들면 다음과 같습니다.

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. `tar` 유틸리티를 사용해 이진 파일을 추출하고 적절한 위치로 이동합니다. 예를 들면 다음과 같습니다.

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. 효율성을 위해 바로 가기 링크를 만듭니다.

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. VI(`vi .bash_profile`)에서 Bash 프로필을 열고 다음 변수를 사용하여 업데이트합니다.

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  수정된 환경 변수를 적용합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**JEUS를 설치하려면**

1. `tar` 유틸리티를 사용해서 설치 프로그램을 확장합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. **Jeus** 폴더(`mkdir jeus7`)를 만들고 바이너리 압축을 풉니다.
3. **설정** 디렉터리로 변경하거나 사용자 환경에 JEUS 매개 변수를 사용합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. 빌드를 수행하기 전에 `ant clean-all`를 실행합니다. 출력은 다음과 같습니다.

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

5.  domain-config-template.properties 파일의 백업을 만듭니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. VI에서 domain-config-template.properties 파일을 엽니다.

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. `jeus.password=jeusadmin nodename=Tmaxsoft`를 `jeus.password=tmax1234 nodename=ofdemo`로 변경

8. `ant install` 명령을 실행해서 JEUS를 빌드합니다.
9.  다음과 같이 JEUS 변수를 사용하여 .bash\_ 프로필 파일을 업데이트합니다.

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Bash 프로필을 실행합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *선택 사항입니다*. JEUS 구성 요소를 쉽게 종료하고 부팅하기 위한 별칭을 만듭니다.

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. 설치를 확인하려면 다음과 같이 도메인 관리자 서버를 시작합니다.

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. 다음 구문을 사용하여 웹 로그온으로 확인합니다.

     ```
     http://<IP>:<port>/webadmin/login
     ```

     예를 들어 <http://192.168.92.133:9736/webadmin/login.> 로그온 화면이 표시됩니다.
    
     ![JEUS WebAdmin 로그온 화면](media/jeus-01.png)

     > [!NOTE]
     > 포트 보안 관련 문제가 발생하는 경우 포트 9736을 열거나 방화벽 (`systemctl stop firewall`)을 해제합니다.

14. server1에 대한 호스트 이름을 변경하려면 **잠금 & 편집** 을 클릭한 다음 **server1** 을 클릭합니다. 서버 창에서 다음과 같이 호스트 이름을 변경합니다.

    1.  **Nodename** 을 **ofdemo** 로 변경합니다.
    2.  창 오른쪽에서 **확인** 을 클릭합니다.
    3.  창의 왼쪽 아래에 있는 **변경 내용 적용** 을 클릭하고 설명에 *호스트 이름 변경* 을 입력합니다.

    ![JEUS WebAdmin 화면](media/jeus-02.png)

15. 확인 화면에서 구성이 성공했는지 확인합니다.

    ![jeus_domain Server screen](media/jeus-03.png)

16. 다음 명령을 사용하여 관리되는 서버 프로세스 “server1”을 시작합니다.

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>OFGW 설치

OFGW는 3270 터미널 에뮬레이터와 OSI 기반 간의 통신을 지원하고 터미널 에뮬레이터와 OSI 간의 세션을 관리하는 OpenFrame 게이트웨이입니다.

**OFGW를 설치하려면**

1. JEUS가 성공적으로 설치되었는지 확인한 다음 OFGW7\_0\_1\_Generic.bin 설치 프로그램 파일이 있는지 확인합니다.
2. 설치 프로그램을 실행합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. 해당 프롬프트에 다음 위치를 사용합니다.
     -   JEUS 홈 디렉터리
     -   JEUS 도메인 이름
     -   JEUS 서버 이름
     -   Tibero Driver
     -   Tmax 노드 ID ofdemo

4. 나머지 기본값을 적용한 다음 Enter 키를 눌러 설치 프로그램을 종료합니다.

5. OFGW의 URL이 예상대로 작동하는지 확인합니다.

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     다음 화면이 나타납니다.

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>OFManager 설치

OFManager는 웹 환경에서 OpenFrame의 작업 및 관리 기능을 제공합니다.

**OFManager를 설치하려면**

1. OFManager7\_Generic.bin 설치 프로그램 파일이 있는지 확인합니다.
2. 설치 프로그램을 실행합니다. 예를 들면 다음과 같습니다.

     ```
     OFManager7_Generic.bin
     ```

3.  Enter 키를 눌러 계속 진행하고 라이선스 계약에 동의합니다.
4.  설치 폴더를 선택합니다.
5.  기본값을 그대로 적용합니다.
6.  데이터베이스로 Tibero를 선택합니다.
7.  Enter 키를 눌러 설치 프로그램을 종료합니다.
8.  OFManager의 URL이 정상적으로 작동하는지 확인합니다.

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

시작 화면이 표시됩니다.

![Tmax OpenFrame Manager 로그온 화면](media/ofmanager-01.png)

그러면 OpenFrame 구성 요소의 설치가 완료됩니다.

## <a name="next-steps"></a>다음 단계

메인프레임 마이그레이션을 고려하는 경우 확장된 파트너 에코시스템이 도움이 될 수 있습니다. 파트너 솔루션 선택과 관련한 상세 지침은 [Platform Modernization Alliance](https://datamigration.microsoft.com/)를 참조하세요.

-   [Azure 시작](../../../../index.yml)
-   [Host Integration Server(HIS) 설명서](/host-integration-server/)
-   [Azure Virtual Data Center 리프트 앤 시프트 가이드](/archive/blogs/azurecat/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide)
