---
title: Azure 가상 머신에 TmaxSoft 오픈프레임 설치
description: Azure 가상 머신(VM)에서 TmaxSoft OpenFrame 환경을 사용하여 IBM z/OS 메인프레임 워크로드를 다시 호스팅합니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72436056"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Azure에 TmaxSoft 오픈프레임 설치

개발, 데모, 테스트 또는 프로덕션 워크로드에 적합한 Azure에서 OpenFrame 환경을 설정하는 방법에 대해 알아봅니다. 이 자습서에서는 각 단계를 안내합니다.

OpenFrame에는 Azure에서 메인프레임 에뮬레이션 환경을 만드는 여러 구성 요소가 포함되어 있습니다. 예를 들어, OpenFrame 온라인 서비스는 IBM 고객 정보 제어 시스템(CICS)과 같은 메인프레임 미들웨어를 TJES 구성 요소로 대체하며 IBM 메인프레임의 작업 엔트리 하위 시스템(JES)을 대체합니다.

OpenFrame은 오라클 데이터베이스, 마이크로소프트 SQL 서버, IBM Db2 및 MySQL을 포함한 모든 관계형 데이터베이스와 함께 작동합니다. 오픈 프레임의이 설치는 TmaxSoft 티베로 관계형 데이터베이스를 사용합니다. 오픈 프레임과 티베로 모두 리눅스 운영 체제에서 실행됩니다. 이 자습서는 CentOS 7.3을 설치합니다. OpenFrame 응용 프로그램 서버와 Tibero 데이터베이스는 하나의 가상 머신(VM)에 설치됩니다.

이 자습서에서는 OpenFrame 제품군 구성 요소의 설치를 단계화합니다. 일부는 별도로 설치해야 합니다.

메인 오픈프레임 구성 요소:

- 필요한 설치 패키지.
- 티베로 데이터베이스.
- 오픈 데이터베이스 연결 (ODBC)는 티베로 데이터베이스와 통신하기 위해 OpenFrame의 응용 프로그램에서 사용됩니다.
- 오픈 프레임 베이스, 전체 시스템을 관리하는 미들웨어.
- 오픈 프레임 배치, 메인 프레임의 배치 시스템을 대체하는 솔루션입니다.
- TACF는 시스템 및 리소스에 대한 사용자 액세스를 제어하는 서비스 모듈입니다.
- ProSort, 일괄 처리 트랜잭션에 대 한 정렬 도구입니다.
- OFCOBOL, 메인 프레임의 코볼 프로그램을 해석 하는 컴파일러.
- OFASM은 메인프레임의 어셈블러 프로그램을 해석하는 컴파일러입니다.
- 오픈 프레임 서버 유형 C (OSC), 메인 프레임의 미들웨어와 IBM CICS를 대체 하는 솔루션.
- 자바 엔터프라이즈 에디션 6인증을 받은 웹 애플리케이션 서버인 JAVA 엔터프라이즈 사용자 솔루션(JEUS)입니다.
- OFGW, 3270 수신기를 제공하는 오픈 프레임 게이트웨이 구성 요소입니다.
- OFManager, 웹 환경에서 OpenFrame의 운영 및 관리 기능을 제공하는 솔루션입니다.

기타 필수 오픈프레임 구성 요소:

- OSI, 메인 프레임 미들웨어 및 IMS DC를 대체하는 솔루션.
- TJES, 메인 프레임의 JES 환경을 제공하는 솔루션입니다.
- OFTSAM, (V)SAM 파일을 개방형 시스템에서 사용할 수 있게 하는 솔루션입니다.
- OFHiDB, 메인 프레임의 IMS DB를 대체하는 솔루션.
- OFPLI, 메인프레임의 PL/I 프로그램을 해석하는 컴파일러입니다.
- PROTRIEVE, 메인 프레임 언어 CA-Easytrieve를 실행하는 솔루션.
- OFMiner는 메인프레임 자산을 분석한 다음 Azure로 마이그레이션하는 솔루션입니다.

## <a name="architecture"></a>Architecture

다음 그림은 이 자습서에 설치된 OpenFrame 7.0 아키텍처 구성 요소에 대한 개요를 제공합니다.

![오픈프레임 구성 요소](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure 시스템 요구 사항

다음 표에는 Azure에서 설치하기 위한 요구 사항이 나열되어 있습니다.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>요구 사항</th><th>설명</th></tr>
</thead>
<tbody>
<tr><td>Azure에서 지원되는 Linux 배포판
</td>
<td>
리눅스 x86 2.6 (32 비트, 64 비트)<br/>
레드 햇 7.x<br/>
센트로스 7.x<br/>
</td>
</tr>
<tr><td>하드웨어
</td>
<td>코어: 2 (최소)<br/>
메모리: 4GB(최소)<br/>
스왑 공간: 최소 1GB<br/>
하드 디스크: 100GB(최소)<br/>
</td>
</tr>
<tr><td>Windows 사용자를 위한 옵션 소프트웨어
</td>
<td>PuTTY: 이 가이드에서 VM 기능을 구성하는 데 사용됩니다.<br/>
WinSCP: 사용할 수 있는 인기 있는 SFTP 클라이언트 및 FTP 클라이언트<br/>
윈도우용 이클립스: TmaxSoft가 지원하는 개발 플랫폼<br/>
(현재 Microsoft 비주얼 스튜디오는 지원되지 않음)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>사전 요구 사항

필요한 모든 소프트웨어를 조립하고 모든 수동 프로세스를 완료하는 데 며칠을 할애할 계획입니다.

시작하기 전에 다음을 수행하십시오.

- TmaxSoft에서 오픈 프레임 설치 미디어를 가져옵니다. 기존 TmaxSoft 고객인 경우 TmaxSoft 담당자에게 라이센스가 부여된 사본을 문의하십시오. 그렇지 않으면 [TmaxSoft에서](https://www.tmaxsoft.com/contact/)평가판을 요청하십시오.

- 로 전자 메일을 보내 OpenFrame 설명서를 요청합니다. <support@tmaxsoft.com>

- 아직 Azure 구독이 없는 경우 Azure 구독을 가져옵니다. 시작하기 전에 [무료 계정을](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 만들 수도 있습니다.

- (선택 사항) Azure VM에 대한 액세스를 조직의 허용된 사용자로 제한하는 사이트 간 VPN 터널 또는 점프박스를 설정합니다. 이 단계는 필수는 아니지만 모범 사례입니다.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>오픈 프레임 및 티베로에 대 한 Azure에 VM 설정

다양한 배포 패턴을 사용하여 OpenFrame 환경을 설정할 수 있지만 다음 절차에서는 OpenFrame 응용 프로그램 서버와 Tibero 데이터베이스를 하나의 VM에 배포하는 방법을 보여 주십니까? 대규모 환경과 상당한 워크로드의 경우 성능을 높이기 위해 자체 VM에 데이터베이스를 별도로 배포하는 것이 가장 좋습니다.

**VM을 만들려면**

1. Azure 포털로 <https://portal.azure.com> 이동하여 계정에 로그인합니다.

2. **가상 머신**을 클릭합니다.

    ![Azure 포털의 리소스 목록](media/vm-01.png)

3. **추가**를 클릭합니다.

    ![Azure 포털에 옵션 추가](media/vm-02.png)

4. **운영 체제**의 오른쪽에서 더 **를 클릭합니다.**

     ![Azure 포털에서 추가 옵션](media/vm-03.png)

5. **CentOS 기반 7.3을** 클릭하여 이 워크스루를 정확하게 따르거나 지원되는 다른 Linux 배포판을 선택할 수 있습니다.

     ![Azure 포털의 운영 체제 옵션](media/vm-04.png)

6. **기본** 설정에서 **이름,** **사용자 이름,** **인증 유형,** 구독(종량제는 AWS 결제 스타일) 및 **리소스 그룹(기존** 설정 사용 또는 TmaxSoft 그룹 만들기)을 입력합니다. **Subscription**

7. **완료되면(인증 유형에**대한 공개/개인 키 쌍 포함) **제출을**클릭합니다.

> [!NOTE]
> **인증 유형에**SSH 공개 키를 사용하는 경우 다음 섹션의 단계를 참조하여 공개/개인 키 쌍을 생성한 다음 여기에서 단계를 다시 시작합니다.

### <a name="generate-a-publicprivate-key-pair"></a>공개/비공개 키 쌍 생성

Windows 운영 체제를 사용하는 경우 공개/개인 키 쌍을 생성하려면 PuTTYgen이 필요합니다.

공개 키는 자유롭게 공유할 수 있지만 개인 키는 완전히 비밀로 유지되어야 하며 다른 당사자와 공유해서는 안 됩니다. 키를 생성한 후 **SSH 공개 키를** 구성에 붙여넣고 실제로 Linux VM에 업로드해야 합니다. 사용자 계정의 \~\_홈 디렉터리의 /.ssh 디렉터리 내에 인증된 키 내에 저장됩니다. 그런 다음 Linux VM은 SSH 클라이언트에서 연결된 **SSH 개인 키를** 제공하면 연결을 인식하고 유효성을 검사할 수 있습니다(우리의 경우 PuTTY).

새로운 개인에게 VM에 액세스 권한을 부여할 때: 

- 각 새로운 개인은 PuTTYgen을 사용하여 자신의 공개 / 개인 키를 생성합니다.
- 개인은 자신의 개인 키를 별도로 저장하고 공개 키 정보를 VM 관리자에게 보냅니다.
- 관리자는 \~공개 키의 내용을 /.ssh/권한 있는\_키 파일에 붙여넣습니다.
- 새로운 개인은 PuTTY를 통해 연결됩니다.

**공개/개인 키 쌍을 생성하려면**

1.  에서 <https://www.putty.org/> PuTTYgen을 다운로드하고 모든 기본 설정을 사용하여 설치합니다.

2.  PuTTYgen을 열려면 C:\\프로그램 파일\\PuTTY에서 PuTTY 설치 디렉토리를 찾습니다.

    ![PuTTY 인터페이스](media/puttygen-01.png)

3.  **생성**을

    ![PuTTY 키 생성기 대화 상자](media/puttygen-02.png)

4.  생성 후 공개 키와 개인 키를 모두 저장합니다. **가상 시스템 \> 기본** 창 만들기 창의 **SSH 공개 키** 섹션에 공개 키의 내용을 붙여넣습니다(이전 섹션의 6단계 및 7단계에 표시됨).

    ![PuTTY 키 생성기 대화 상자](media/puttygen-03.png)

### <a name="configure-vm-features"></a>VM 기능 구성

1. Azure 포털에서 크기 블레이드 **선택에서** 원하는 Linux 컴퓨터 하드웨어 설정을 선택합니다. 이 예제 설치와 같이 Tibero 및 OpenFrame을 모두 설치하기 위한 *최소* 요구 사항은 2개의 CPU와 4GB RAM입니다.

    ![가상 컴퓨터 만들기 - 기본 사항](media/create-vm-01.png)

2. **3 설정을** 클릭하고 기본 설정을 사용하여 선택적 피처를 구성합니다.
3. 결제 세부정보를 검토합니다.

    ![가상 컴퓨터 만들기 - 구매](media/create-vm-02.png)

4. 선택 사항을 제출합니다. Azure에서 VM을 배포하기 시작합니다. 이 프로세스는 일반적으로 몇 분 정도 걸립니다.

5. VM이 배포되면 대시보드가 표시되어 구성 중에 선택한 모든 설정이 표시됩니다. **공용 IP 주소를**기록합니다.

    ![Azure 대시보드의 최대 값](media/create-vm-03.png)

6. PuTTY를 엽니다.

7. **호스트 이름의**경우 사용자 이름과 복사한 공용 IP 주소를 입력합니다. 예를 들어 **사용자\@이름 publicip.**

    ![PuTTY 구성 대화 상자](media/putty-01.png)

8. **범주** 상자에서 **연결 \> SSH \> Auth를**클릭합니다. **개인 키** 파일에 대한 경로를 제공합니다.

    ![PuTTY 구성 대화 상자](media/putty-02.png)

9. PuTTY 창을 실행하려면 **열기를** 클릭합니다. 성공하면 Azure에서 실행되는 새 CentOS VM에 연결됩니다.

10. 루트 사용자로 로그온하려면 **sudo bash를**입력합니다.

    ![명령 창에서 루트 사용자 로그온](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>환경 및 패키지 설정

VM이 만들어지고 로그온되었으므로 몇 가지 설치 단계를 수행하고 필요한 사전 설치 패키지를 설치해야 합니다.

1. vi를 사용하여 호스트 파일()을`vi /etc/hosts`편집하여 **demo의** 이름을 로컬 IP 주소에 매핑합니다. IP가 192.168.96.148 ofdemo라고 가정하면 변경 전입니다.

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     변경 후입니다.

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

6. 필수 패키지 받기: 서버가 인터넷에 연결되어 있는지 확인하고 다음 패키지를 다운로드한 다음 설치하십시오.

     - 도스2유니스
     - glibc
     - glibc.i686 glibc.x86\_64
     - 리바이오 주
     - Ncurses

          > [!NOTE]
          > ncurses 패키지를 설치한 후 다음과 같은 기호 링크를 만듭니다.
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - 리바이오-데벨.x86\_64
     - 스트레이스
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

## <a name="install-the-tibero-database"></a>티베로 데이터베이스 설치

Tibero는 Azure의 OpenFrame 환경에서 몇 가지 주요 기능을 제공합니다.

- 티베로는 다양한 시스템 기능에 대한 OpenFrame 내부 데이터 저장소로 사용됩니다.
- KSDS, RRDS 및 ESDS를 포함한 VSAM 파일은 내부적으로 데이터 저장을 위해 티베로 데이터베이스를 사용합니다.
- TACF 데이터 리포지토리는 티베로에 저장됩니다.
- OpenFrame 카탈로그 정보는 티베로에 저장됩니다.
- Tibero 데이터베이스는 IBM Db2를 대체하여 애플리케이션 데이터를 저장하는 데 사용할 수 있습니다.

**티베로를 설치하려면**

1. Tibero 바이너리 설치 관리자 파일이 있는지 확인하고 버전 번호를 검토합니다.
2. 티베로 소프트웨어를 티베로 사용자 계정(oframe)에 복사합니다. 예를 들어:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. vi ()에서\_`vi .bash_profile`.bash 프로필을 열고 다음을 붙여 넣습니다.

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. bash 프로필을 실행하려면 명령 프롬프트 유형에서 다음을 수행합니다.

    ```
    source .bash_profile
    ```

5. 팁 파일(Tibero의 구성 파일)을 생성한 다음 vi에서 엽니다. 예를 들어:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. TB \$\_HOME/클라이언트/구성/tbdsn.tbr을 수정하고 그림과 같이 로컬 호스트 대신 127.0.0.1을 넣습니다.

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. 데이터베이스를 만듭니다. 다음 출력이 표시됩니다.

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

8. 티베로를 재활용하려면 먼저 명령을 `tbdown` 사용하여 종료합니다. 예를 들어:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. 이제 를 사용하여 `tbboot`티베로를 부팅합니다. 예를 들어:

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

12. 티베로를 부팅하고 티베로 프로세스가 실행되고 있는지 확인합니다.

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

출력:

![티베로 출력](media/tibero-01.png)

## <a name="install-odbc"></a>ODBC 설치

OpenFrame의 응용 프로그램은 오픈 소스 unixODBC 프로젝트에서 제공하는 ODBC API를 사용하여 티베로 데이터베이스와 통신합니다.

ODBC를 설치하려면 다음을 수행하십시오.

1. unixODBC-2.3.4.tar.gz 설치 프로그램 파일이 있는지 확인하거나 `wget unixODBC-2.3.4.tar.gz` 명령을 사용합니다. 예를 들어:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. 바이너리의 압축을 풀고 있습니다. 예를 들어:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. unixODBC-2.3.4 디렉토리로 이동하여 검사 기계 정보를 사용하여 Makefile을 생성합니다. 예를 들어:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     기본적으로 unixODBC는 /usr /local에 설치되므로 `--prefix` 위치를 변경하는 값을 전달합니다. 마찬가지로 구성 파일은 기본적으로 /etc에 `--sysconfdir` 설치되므로 원하는 위치의 값을 전달합니다.

4. 메이크 파일 실행:`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. 컴파일 후 프로그램 디렉터리에서 실행 파일을 복사합니다. 예를 들어:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. vi를 사용하여 bash 프로파일()을`vi ~/.bash_profile`편집하고 다음을 추가합니다.

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. ODBC를 적용합니다. 그에 따라 다음 파일을 편집합니다. 예를 들어:

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

8. 기호 링크를 만들고 Tibero 데이터베이스 연결의 유효성을 검사합니다.

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

다음 출력이 표시됩니다.

![SQL에 연결된 ODBC 출력 표시](media/odbc-01.png)

## <a name="install-openframe-base"></a>오픈프레임 베이스 설치

기본 응용 프로그램 서버는 OpenFrame이 트랜잭션 처리 서버 프로세스를 포함하여 Azure에서 시스템을 관리하는 데 사용하는 개별 서비스 앞에 설치됩니다.

**오픈프레임 베이스를 설치하려면**

1. Tibero 설치가 성공했는지 확인한 다음 다음\_OpenFrame\_\_Base7 0 Linux\_x86\_64.bin 설치 프로그램 파일 및 base.properties 구성 파일이 있는지 확인합니다.

2. 다음 티베로 관련 정보로 bash 프로필을 업데이트합니다.

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

3. bash 프로필을 실행합니다.`[oframe7@ofdemo ~]$ . .bash_profile`
4. Tibero 프로세스가 실행되고 있는지 확인합니다. 예를 들어:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![기본](media/base-01.png)

     > [!IMPORTANT]
     > 설치 하기 전에 티베로를 시작 해야 합니다.

5. [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) 라이센스를 생성하고 적절한 폴더에 오픈 프레임 베이스, 배치, TACF, OSC 라이센스를 넣어 :

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. OpenFrame 베이스 바이너리 및 base.properties 파일을 다운로드합니다.

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

7. base.properties 파일을 사용하여 설치 관리자를 실행합니다. 예를 들어:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    완료되면 설치 완료 메시지가 재생됩니다.

8. 명령을 사용하여 OpenFrame 베이스 디렉터리 구조를 확인합니다. `ls -ltr` 예를 들어:

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

9. 오픈프레임 베이스 시작:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot 명령 출력](media/base-02.png)

10. si의 tmadmin 명령을 사용하여 프로세스 상태가 준비되었는지 확인합니다. RDY는 각 프로세스의 **상태** 열에 표시됩니다.

     ![tmadmin 명령 출력](media/base-03.png)

11. 오픈프레임 베이스 종료:

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

## <a name="install-openframe-batch"></a>오픈프레임 배치 설치

OpenFrame 일괄 처리는 메인프레임 일괄 처리 환경을 시뮬레이션하고 Azure에서 일괄 처리 작업을 실행하는 데 사용되는 여러 구성 요소로 구성됩니다.

**일괄 처리를 설치하려면**

1. 기본 설치가 성공했는지 확인한 다음 OpenFrame\_\_Batch7 0\_Fix2\_MVS\_Linux\_x86\_64.bin 설치 프로그램 파일 및 batch.properties 구성 파일이 있는지 확인합니다.

2. 명령 프롬프트에서 `vi batch.properties` vi를 사용하여 batch.properties 파일을 편집하려면 입력합니다.

3. 다음과 같이 매개 변수를 수정합니다.

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

4. 일괄 처리 설치 관리자를 실행하려면 명령 프롬프트 유형에서 다음을 수행합니다.

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. 설치가 완료되면 명령 프롬프트를 입력하여 `tmboot` 설치된 OpenFrame 제품군을 시작합니다.

    ![tmboot 출력](media/tmboot-01.png)

6. OpenFrame 프로세스를 확인하려면 명령 프롬프트를 입력합니다. `tmadmin`

    ![Tmax 관리자 화면](media/tmadmin-01.png)

7. 다음 명령을 실행합니다.

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. `tmdown` 명령을 사용하여 일괄 처리를 시작하고 종료합니다.

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

TACF 관리자는 RACF 보안을 통해 시스템 및 리소스에 대한 사용자 액세스를 제어하는 OpenFrame 서비스 모듈입니다.

**TACF를 설치하려면**

1. \_OpenFrame\_Tacf7 0\_Fix2\_\_Linux x86\_64.bin 설치 프로그램 파일 및 tacf.properties 구성 파일이 있는지 확인합니다.
2. Batch 설치가 성공했는지 확인한 다음 vi를 사용하여 tacf.properties 파일()을`vi tacf.properties`엽니다.
3. TACF 매개 변수 수정:

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

6. 명령 프롬프트에서 `tmboot` OpenFrame을 다시 시작하려면 입력합니다. 출력은 다음과 같습니다.

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

7. 명령에서 프로세스 상태를 사용할 `tmadmin` 준비가 `si` 되었는지 확인합니다. 예를 들어:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     **상태** 열에 RDY가 나타납니다.

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

9. 명령을 사용하여 서버를 `tmdown` 종료합니다. 출력은 다음과 같습니다.

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

## <a name="install-prosort"></a>프로소더리 설치

ProSort는 데이터 정렬을 위한 일괄 처리 트랜잭션에 사용되는 유틸리티입니다.

**ProSort를 설치하려면**

1. 배치 설치가 성공했는지 확인한 다음 **prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz** 설치 프로그램 파일이 있는지 확인합니다.

2. 속성 파일을 사용하여 설치 관리자를 실행합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. 프로세스 디렉토리를 홈 위치로 이동합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. 라이센스 하위 디렉터리를 만들고 라이센스 파일을 복사합니다. 예를 들어:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. vi ()에서`vi .bash_profile`bash.profile을 열고 다음과 같이 업데이트하십시오.

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

6. bash 프로필을 실행하려면 명령 프롬프트에서 다음을 입력합니다.`. .bash_profile`

7. 구성 파일을 만듭니다. 예를 들어:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. 기호 링크를 만듭니다. 예를 들어:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. 명령을 실행하여 ProSort 설치를 `prosort -h` 확인합니다. 예를 들어:

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

## <a name="install-ofcobol"></a>OF코볼 설치

OFCOBOL는 메인프레임의 코볼 프로그램을 해석하는 오픈프레임 컴파일러입니다. 

**OFCOBOL을 설치하려면**

1. Batch/Online 설치가 성공했는지 확인한 다음 OpenFrame\_\_COBOL3\_0\_\_40\_Linux x86 64.bin 설치 프로그램 파일이 있는지 확인합니다.

2. OFCOBOL 설치 프로그램을 실행하려면 명령 프롬프트에서 다음을 입력합니다.

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. 라이선스 계약을 읽고 Enter를 눌러 계속합니다.

4. 라이선스 계약에 동의합니다. 설치가 완료되면 다음과 같은 것이 나타납니다.

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

5. vi ()에서`vi .bash_profile`bash 프로필을 열고 OFCOBOL 변수로 업데이트되었는지 확인합니다.
6. bash 프로필을 실행합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
      source ~/.bash_profile
     ```

7. OFCOBOL 라이센스를 설치된 폴더에 복사합니다. 예를 들어:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. OpenFrame tjclrun.conf 구성 파일로 이동하여 vi에서 엽니다. 예를 들어:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   변경 전의 SYSLIB 섹션은 다음과 같습니다.
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   변경 후의 SYSLIB 섹션은 다음과 같습니다.
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. 오픈 프레임\_코볼\_InstallLog.log 파일을 vi에서 검토하고 오류가 없는지 확인합니다. 예를 들어:
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
10. `ofcob --version` 명령을 사용하고 버전 번호를 검토하여 설치를 확인합니다. 예를 들어:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. 명령을 사용하여 OpenFrame을 재부팅합니다. `tmdown/tmboot`

## <a name="install-ofasm"></a>OFASM 설치

OFASM은 메인프레임의 어셈블러 프로그램을 해석하는 OpenFrame 컴파일러입니다.

**OFASM설치**

1. Batch/Online 설치가 성공했는지 확인한 다음 OpenFrame\_\_ASM3\_\_0 Linux\_x86 64.bin 설치 프로그램 파일이 있는지 확인합니다.

2. 설치 관리자를 실행합니다. 예를 들어:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. 라이선스 계약을 읽고 Enter를 눌러 계속합니다.
4. 라이선스 계약에 동의합니다.
5. BASH 프로필이 OFASM 변수로 업데이트되었는지 확인합니다. 예를 들어:

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

6. OpenFrame tjclrun.conf 구성 파일을 vi에서 열고 다음과 같이 편집합니다.

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     변경 *전의* [SYSLIB] 섹션은 다음과 같습니다.

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     변경 *후* [SYSLIB] 섹션은 다음과 같습니다.

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. OpenFrame\_ASM\_InstallLog.log 파일을 vi에서 열고 오류가 없는지 확인합니다. 예를 들어:

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

8. 다음 명령 중 하나를 실행하여 OpenFrame을 재부팅하십시오.

     ```
     tmdown / tmboot
     ```

     —또는—

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>OSC 설치

OSC는 고속 OLTP 트랜잭션 및 기타 관리 기능을 지원하는 IBM CICS와 유사한 OpenFrame 환경입니다.

**OSC를 설치하려면**

1. 기본 설치가 성공했는지 확인한\_다음 OpenFrame OSC7\_0\_\_\_Fix2\_Linux x86 64.bin 설치 프로그램 파일 및 osc.properties 구성 파일이 있는지 확인합니다.
2. osc.properties 파일에서 다음 매개 변수를 편집합니다.
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. 그림과 같이 속성 파일을 사용하여 설치 관리자를 실행합니다.

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     작업이 완료되면 "설치 완료" 메시지가 표시됩니다.

4. BASH 프로필이 OSC 변수로 업데이트되었는지 확인합니다.
5. 오픈 프레임\_OSC7\_\_0\_Fix2 InstallLog.log 파일을 검토합니다. 다음과 비슷합니다.

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. vi를 사용하여 ofsys.seq 구성 파일을 엽니다. 예를 들어:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. \#BASE 및 \#BATCH 섹션에서 그림과 같이 매개변수를 편집합니다.

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

8. 라이센스 파일을 복사합니다. 예를 들어:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. OSC를 시작하고 종료하려면 명령 프롬프트를 입력하여 CICS 영역 공유 메모리를 `osctdlinit OSCOIVP1` 초기화합니다.

10. OSC를 부팅하려면 실행합니다. `oscboot` 출력은 다음과 같습니다.

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

11. 프로세스 상태가 준비되었는지 확인하려면 si의 `tmadmin` 명령을 사용합니다. 모든 프로세스는 **상태** 열에 RDY를 표시해야 합니다.

    ![RDY를 표시하는 프로세스](media/tmadmin-02.png)

12. 명령을 사용하여 OSC를 종료합니다. `oscdown`

## <a name="install-jeus"></a>JEUS 설치

JEUS(Java 엔터프라이즈 사용자 솔루션)는 OpenFrame 웹 응용 프로그램 서버의 프레젠테이션 계층을 제공합니다.

JEUS를 설치하기 전에 JEUS를 설치하는 데 필요한 라이브러리 및 명령줄 도구를 제공하는 아파치 개미 패키지를 설치합니다.

**아파치 개미를 설치하려면**

1. 명령을 사용하여 개미 바이너리를 다운로드합니다. `wget` 예를 들어:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. 유틸리티를 `tar` 사용하여 바이너리 파일을 추출하고 적절한 위치로 이동합니다. 예를 들어:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. 효율성을 위해 기호 링크를 만듭니다.

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. bash 프로필을 vi ()에서`vi .bash_profile`열고 다음 변수로 업데이트합니다.

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  수정된 환경 변수를 적용합니다. 예를 들어:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**JEUS를 설치하려면**

1. 유틸리티를 사용하여 설치 `tar` 관리자를 확장합니다. 예를 들어:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. **jeus** 폴더 ()를`mkdir jeus7`만들고 바이너리 압축을 해제합니다.
3. **설치** 디렉터리로 변경하거나 사용자 환경에 JEUS 매개 변수를 사용합니다. 예를 들어:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. 빌드를 수행하기 전에 실행합니다. `ant clean-all` 출력은 다음과 같습니다.

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

5.  도메인 구성-template.properties 파일을 백업합니다. 예를 들어:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. vi에서 도메인 구성-template.properties 파일을 엽니다.

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. `jeus.password=jeusadmin nodename=Tmaxsoft`를 `jeus.password=tmax1234 nodename=ofdemo`로 변경

8. `ant install` 명령을 실행하여 JEUS를 빌드합니다.
9.  그림과 같이\_JEUS 변수로 .bash 프로필 파일을 업데이트합니다.

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. bash 프로필을 실행합니다. 예를 들어:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *선택 사항입니다*. JEUS 구성 요소를 쉽게 종료하고 부팅할 수 있는 별칭 만들기:

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

13. 구문을 사용하여 웹 로그온으로 확인합니다.

     ```
     http://<IP>:<port>/webadmin/login
     ```

     예를 들어 <http://192.168.92.133:9736/webadmin/login.> 로그온 화면이 나타납니다.
    
     ![JEUS 웹관리자 로그온 화면](media/jeus-01.png)

     > [!NOTE]
     > 포트 보안에 문제가 있는 경우 포트 9736을 열거나 방화벽()을`systemctl stop firewall`사용하지 않도록 설정합니다.

14. server1의 호스트 이름을 변경하려면 **편집에 & 잠금을**클릭한 다음 **server1을**클릭합니다. 서버 창에서 호스트 이름을 다음과 같이 변경합니다.

    1.  **노드 이름을** **ofdemo로**변경합니다.
    2.  창 의 오른쪽에 **있는 확인을** 클릭합니다.
    3.  창 왼쪽 하단에 **변경 내용 적용을** 클릭하고 설명을 보려면 *호스트 이름 변경 을*입력합니다.

    ![제우스 웹관리자 화면](media/jeus-02.png)

15. 확인 화면에서 구성이 성공했는지 확인합니다.

    ![jeus_domain 서버 화면](media/jeus-03.png)

16. 다음 명령을 사용하여 관리되는 서버 프로세스 "server1"을 시작합니다.

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>OFGW 설치

OFGW는 3270 터미널 에뮬레이터와 OSI 베이스 간의 통신을 지원하고 터미널 에뮬레이터와 OSI 사이의 세션을 관리하는 OpenFrame 게이트웨이입니다.

**OFGW를 설치하려면**

1. JEUS가 성공적으로 설치되었는지 확인한 다음 OFGW7\_\_0\_1 Generic.bin 설치 프로그램 파일이 있는지 확인합니다.
2. 설치 관리자를 실행합니다. 예를 들어:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. 해당 프롬프트에 대해 다음 위치를 사용합니다.
     -   제우스 홈 디렉토리
     -   제우스 도메인 이름
     -   JEUS 서버 이름
     -   티베로 드라이버
     -   티맥스 노드 ID 오브데모

4. 나머지 기본값을 수락한 다음 Enter를 눌러 설치 프로그램을 종료합니다.

5. OFGW의 URL이 예상대로 작동하는지 확인합니다.

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     다음 화면이 나타납니다.

    ![오픈프레임 웹터미널](media/ofgw-01.png)

## <a name="install-ofmanager"></a>OFManager 설치

OFManager는 웹 환경에서 OpenFrame에 대한 운영 및 관리 기능을 제공합니다.

**OFManager를 설치하려면**

1. OFManager7\_Generic.bin 설치 관리자 파일이 있는지 확인합니다.
2. 설치 관리자를 실행합니다. 예를 들어:

     ```
     OFManager7_Generic.bin
     ```

3.  Enter를 눌러 계속한 다음 사용권 계약에 동의합니다.
4.  설치 폴더를 선택합니다.
5.  기본값을 그대로 적용합니다.
6.  데이터베이스로 티베로를 선택합니다.
7.  Enter를 눌러 설치 프로그램을 종료합니다.
8.  OFManager의 URL이 예상대로 작동하는지 확인합니다.

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

시작 화면이 나타납니다.

![Tmax 오픈프레임 매니저 로그온 화면](media/ofmanager-01.png)

OpenFrame 구성 요소의 설치가 완료됩니다.

## <a name="next-steps"></a>다음 단계

메인프레임 마이그레이션을 고려중인 경우 확장 파트너 에코시스템을 통해 도움을 드립니다. 파트너 솔루션 선택과 관련한 상세 지침은 [Platform Modernization Alliance](https://datamigration.microsoft.com/)를 참조하세요.

-   [Azure 시작](https://docs.microsoft.com/azure/)
-   [Host Integration Server(HIS) 설명서](https://docs.microsoft.com/host-integration-server/)
-   [Azure 가상 데이터 센터 리프트 앤 시프트 가이드](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
