---
title: "Azure Linux 가상 컴퓨터에 Oracle ASM 설정 | Microsoft Docs"
description: "Azure 환경에서 Oracle ASM을 빠르게 준비하여 실행합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/2/2017
ms.author: v-shiuma
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 8290885cd4f3d2faede2900971cd7a5b5a59f5bc
ms.contentlocale: ko-kr
ms.lasthandoff: 07/08/2017

---

# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Azure Linux 가상 컴퓨터에 Oracle ASM 설정  

이 문서에서는 Azure에서 Oracle Linux VM(가상 컴퓨터)에 Oracle ASM(Oracle 자동 저장소 관리)을 설치하고 설정하는 방법을 알아봅니다.

시작하기 전에 Azure CLI가 설치되어 있는지 확인합니다. 자세한 내용은 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prepare-the-environment"></a>환경 준비

### <a name="sign-in-to-azure"></a>Azure에 로그인 

Azure CLI에서 Azure 구독에 로그인하려면 [az login](/cli/azure/#login) 명령을 사용합니다. 그런 다음, 화면에 나타나는 지침에 따릅니다.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 만들려면 [az group create](/cli/azure/group#create) 명령을 사용합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 westus 위치에 myResourceGroup이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-a-vm"></a>VM 만들기

Oracle ASM을 함께 사용하도록 VM을 만들려면 다음 단계를 완료합니다.

1. 가상 컴퓨터를 만들려면 [az vm create](/cli/azure/vm#create) 명령을 사용합니다. 

   다음 예제에서는 myVM이라는 VM을 만듭니다. 또한 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  

   ```azurecli
   az vm create --resource-group myResourceGroup --name myVM --image Oracle:Oracle-Database-Ee:12.1.0.2:latest --size Standard_DS2_v2 --generate-ssh-keys
   ```

   VM을 만든 후 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. `publicIpAddress`에 대한 값을 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "westus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

2. Oracle ASM 구성에 사용할 디스크를 추가합니다.

   ```azurecli
   az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
   az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
   az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
   az vm disk attach -g myResourceGroup --vm-name myVM  --disk myDataDisk4 --new --size-gb 50
   ```

### <a name="connect-to-the-vm"></a>VM에 연결

VM으로 SSH 세션을 만들려면 다음 명령을 사용합니다. 해당 IP 주소를 VM의 `publicIpAddress` 값으로 바꿉니다.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Oracle ASM 설치

Oracle ASM을 설치하려면 다음 단계를 완료합니다. 

Oracle ASM 설치에 대한 자세한 내용은 [Oracle Linux 6에 대한 Oracle ASMLib 다운로드](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)를 참조하세요.  

1. `yum list`를 실행합니다.

   ```bash
   $ sudo su -
   # yum list
   ```
   처음 실행하는 경우 `yum list`를 로드하는 데 몇 분 정도 걸릴 수 있습니다.

2. 다음과 같은 추가 명령을 실행합니다.

   ```bash
   # yum list | grep oracleasm
   # yum -y install kmod-oracleasm.x86_64
   # yum -y install oracleasm-support.x86_64
   # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
   # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
   # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Oracle ASM이 설치되어 있는지 확인합니다.

   ```bash
   # rpm -qa |grep oracleasm
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
   ```

4. 사용자 및 그룹을 추가합니다.

   ```bash
   # groupadd -g 54345 asmadmin
   # groupadd -g 54346 asmdba
   # groupadd -g 54347 asmoper
   # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
   # usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. 사용자 및 그룹을 확인합니다.

   ```bash
   # id grid
   uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
   ```

6. 폴더를 만들고 소유자를 변경합니다.

   ```bash
   # mkdir /u01/app/grid
   # chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Oracle ASM 설정

이 자습서에서는 기본 사용자가 *grid*이며, 기본 그룹은 *asmadmin*입니다. *oracle* 사용자가 asmadmin 그룹의 구성원인지 확인합니다. Oracle ASM 설치를 설정하려면 다음 단계를 완료합니다.

1. Oracle ASM 라이브러리 드라이버를 설정합니다.

   ```bash
   # /usr/sbin/oracleasm configure -i

   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. 디스크 구성을 확인합니다.
   ```bash
   # cat /proc/partitions
   ```

3. 디스크를 포맷합니다.

   ```bash
   # fdisk /dev/sdc
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. /dev/sdd, /dev/sde, and /dev/sdf에 대해 앞 단계를 반복합니다.

5. 디스크 구성을 확인합니다.

   ```bash
   # cat /proc/partitions
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11        0    1048575 sr0
   ```

6. Oracle ASM 서비스 상태를 확인합니다.

   ```bash
   # service oracleasm status
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   ```

7. Oracle ASM 서비스를 시작합니다.

   ```bash
   # service oracleasm start
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

8. Oracle ASM 디스크를 만듭니다.

   ```bash
   # service oracleasm createdisk ASMSP /dev/sdc1
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

   # service oracleasm createdisk DATA /dev/sdd1
   Marking disk "DATA" as an ASM disk:                        [  OK  ]

   # service oracleasm createdisk DATA1 /dev/sde1
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]

   # service oracleasm createdisk FRA /dev/sdf1
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

9. Oracle ASM 디스크를 나열합니다.

   ```bash
   # service oracleasm listdisks
   ASMSP
   DATA
   DATA1
   FRA
   ```

10. 루트, oracle 및 그리드 사용자에 대한 암호를 변경합니다. 이 암호는 나중에 설치하는 동안 사용합니다.

    ```bash
    # passwd oracle
    # passwd grid
    # passwd root
    ```

11. 폴더 사용 권한을 변경합니다.

    ```bash
    # chmod -R 775 /opt
    # chown grid:oinstall /opt
    # chown oracle:oinstall /dev/sdc1
    # chown oracle:oinstall /dev/sdd1
    # chown oracle:oinstall /dev/sde1
    # chown oracle:oinstall /dev/sdf1
    # chmod 600 /dev/sdc1
    # chmod 600 /dev/sdd1
    # chmod 600 /dev/sde1
    # chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Oracle Grid Infrastructure 다운로드 및 준비

Oracle Grid Infrastructure 소프트웨어를 다운로드 및 준비하려면 다음 단계를 완료합니다.

1. [Oracle ASM 다운로드 페이지](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html)에서 Oracle Grid Infrastructure를 다운로드합니다. 

   다운로드 제목 **Linux x86-64용 Oracle Database 12c 릴리스 1 Grid Infrastructure(12.1.0.2.0)**에서 두 개의 .zip 파일을 다운로드합니다.

2. 클라이언트 컴퓨터에 .zip 파일을 다운로드한 후 SCP(보안 복사 프로토콜)를 사용하여 파일을 VM에 복사합니다.

   ```bash
   scp *.zip <publicIpAddress>:<folder>
   ```

3. .zip 파일을 /opt 폴더로 이동합니다. 그런 다음 파일의 소유자를 변경합니다.

   ```bash
   # mv <folder>/*.zip /opt
   # cd /opt
   # chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   # chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. 파일의 압축을 풉니다. (Linux 압축 풀기 도구가 설치되어 있지 않은 경우 이를 설치합니다.)
   
   ```bash
   # yum install unzip
   # unzip linuxamd64_12102_grid_1of2.zip
   # unzip linuxamd64_12102_grid_2of2.zip
   ```

5. 사용 권한을 변경합니다.
   
   ```bash
   # chown -R grid:oinstall /opt/grid
   ```

6. 방화벽을 해제합니다.
   
   ```bash
   # service iptables status
   # service iptables stop
   ```

7. 사용 가능한 스왑 공간을 확인합니다. Grid를 설치하려면 적어도 6.8GB의 스왑 공간이 필요합니다. Azure Linux VM에서는 기본적으로 스왑이 사용하도록 설정되어 있지 않고 구성되어 있지 않습니다.

스왑 공간이 항상 임시 디스크에 만들어지도록 waagent를 사용하여 스왑 공간을 구성하는 것이 좋습니다. 자세한 내용은 [Linux Azure 가상 컴퓨터에 스왑 파일을 추가하는 방법](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)을 참조합니다.

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>x11을 실행하는 클라이언트 및 VM을 준비합니다(Windows 클라이언트에만 해당).
선택적 단계입니다. Linux 클라이언트를 사용하거나 x11을 이미 설정한 경우 이 단계를 건너뛸 수 있습니다.

1. Windows 컴퓨터에 [PuTTY를 다운로드](http://www.putty.org/)하고 [Xming](https://xming.en.softonic.com/)을 다운로드합니다.

2. PuTTY를 PuTTY 폴더(예: C:\Program Files\PuTTY)에 설치한 후 puttygen.exe(PuTTY 키 생성기)를 실행합니다.

3. PuTTY 키 생성기에서,
   
   1. **생성** 단추를 선택하여 키를 생성합니다.
   2. 키의 콘텐츠를 복사(Ctrl + C)합니다.
   3. **개인 키 저장** 단추를 선택합니다.
   4. 표시되는 경고를 무시하고 **확인**을 선택합니다.

   ![PuTTY 키 생성기의 스크린샷](./media/oracle-asm/puttykeygen.png)

4. VM에서 다음 명령을 실행합니다.

   ```bash
   # sudo su - grid
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. authorized_keys라는 파일을 만듭니다. 키의 콘텐츠를 이 파일에 붙여넣은 다음 파일을 저장합니다.

   > [!NOTE]
   > 키에는 문자열 `ssh-rsa`가 포함되어야 합니다. 또한 키의 콘텐츠는 한 줄 텍스트여야 합니다.
   >  

6. PuTTY를 시작합니다. **카테고리** 창에서 **연결** > **SSH** > **인증**으로 이동합니다. **인증에 대한 개인 키 파일** 상자에서 이전에 생성한 키를 찾아봅니다.

   ![SSH 인증 옵션의 스크린샷](./media/oracle-asm/setprivatekey.png)

7. **카테고리** 창에서 **연결** > **SSH** > **X11**로 이동합니다. **X11 전달을 사용하도록 설정** 확인란을 선택합니다.

   ![SSH X11 전달 옵션의 스크린샷](./media/oracle-asm/enablex11.png)

8. **카테고리** 창에서 **세션**으로 이동합니다. 호스트 정보를 입력한 다음 **열기**를 선택합니다.

   ![PuTTY 세션 옵션의 스크린샷](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Oracle Grid Infrastructure 설치

Grid Infrastructure를 설치하려면 다음 단계를 완료합니다.

1. 그리드로 로그인합니다. (로그인할 때 암호 입력 화면이 나타나지 않아야 합니다.) 

   > [!NOTE]
   > 설치를 시작하기 전에 Xming이 실행되고 있는지 확인합니다.

   ```bash
   $ cd /opt/grid
   $ ./runInstaller
   ```

   Oracle Grid Infrastructure 12c 릴리스 1 설치 관리자가 열립니다. (설치 관리자를 시작하는 데 몇 분 정도 걸릴 수 있습니다.)

2. **설치 옵션 선택** 페이지에서 **독립 실행형 서버에 대한 Oracle Grid Infrastructure 설치 및 구성**을 선택합니다.

   ![설치 관리자 설치 옵션 선택 페이지의 스크린샷](./media/oracle-asm/install01.png)

3. **제품 언어 선택** 페이지에서 **영어** 또는 원하는 언어를 선택합니다.

   ![설치 관리자 제품 언어 선택 페이지의 스크린샷](./media/oracle-asm/install02.png)

4. **ASM 디스크 그룹 만들기** 페이지에서,
   - 디스크 그룹에 사용할 이름을 입력합니다.
   - **중복**에서 **외부**를 선택합니다.
   - **할당 단위 크기**에서 **4**를 선택합니다.
   - **디스크 추가**에서 **ORCLASMSP**를 선택합니다.

   ![설치 관리자 ASM 디스크 그룹 만들기 페이지의 스크린샷](./media/oracle-asm/install03.png)

5. **ASM 암호 지정** 페이지에서 **이러한 계정에 대해 동일한 암호 사용** 옵션을 선택하고 암호를 입력합니다.

   ![설치 관리자 ASM 암호 지정 페이지의 스크린샷](./media/oracle-asm/install04.png)

6. (선택 사항) **관리 옵션 지정** 페이지에서 **EM(Enterprise Manager) 클라우드 컨트롤에 등록** 확인란을 선택합니다.

   ![설치 관리자 관리 옵션 지정 페이지의 스크린샷](./media/oracle-asm/install05.png)

7. **권한있는 운영 체제 그룹** 페이지에서 기본 설정을 사용합니다.

   ![설치 관리자 권한 있는 운영 체제 그룹 페이지의 스크린샷](./media/oracle-asm/install06.png)

8. **설치 위치 지정** 페이지에서 기본 설정을 사용합니다.

   ![설치 관리자 설치 위치 지정 페이지의 스크린샷](./media/oracle-asm/install07.png)

9. **인벤토리 만들기** 페이지에서 폴더 위치를 입력하거나 찾아봅니다.

   ![설치 관리자 인벤토리 만들기 페이지의 스크린샷](./media/oracle-asm/install08.png)

10. **루트 스크립트 실행 구성** 페이지에서 **구성 스크립트 자동으로 실행** 확인란을 선택합니다. 그런 다음 **"root" 사용자 자격 증명 사용** 옵션을 선택하고 루트 사용자 암호를 입력합니다.

    ![설치 관리자 루트 스크립트 실행 구성 페이지의 스크린샷](./media/oracle-asm/install09.png)

11. **필수 구성 요소 검사 수행** 페이지에서 **수정 및 다시 확인**을 선택합니다.

    ![설치 관리자 필수 구성 요소 검사 수행 페이지의 스크린샷](./media/oracle-asm/install10.png)

12. **수정 스크립트** 대화 상자에서 **확인**을 선택합니다.

    ![수정 스크립트 대화 상자의 스크린샷](./media/oracle-asm/install11.png)

13. **요약** 페이지에서 선택한 설정을 검토한 다음 **설치**를 선택합니다.

    ![설치 관리자 요약 페이지의 스크린샷](./media/oracle-asm/install12.png)

14. 경고 대화 상자가 나타납니다. **예**를 선택하여 계속합니다.

    ![경고 대화 상자의 스크린샷](./media/oracle-asm/install14.png)

15. **마침** 페이지에서 **닫기**를 선택하여 설치를 완료합니다.

    ![설치 관리자 마침 페이지의 스크린샷](./media/oracle-asm/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>Oracle ASM 설치 설정

Oracle ASM 설치를 설정하려면 다음 단계를 완료합니다.

1. X11 세션에서 그리드로 로그인합니다.

   ```bash
   $ cd /u01/app/grid/product/12.1.0/grid/bin
   $ ./asmca
   ```

   Oracle ASM 구성 도우미가 열립니다.

2. **ASM 구성: 디스크 그룹** 대화 상자에서 **만들기** 단추를 선택하고 **고급 옵션 표시**를 선택합니다.

   ![ASM 구성: 디스크 그룹 대화 상자의 스크린샷](./media/oracle-asm/asm01.png)

3. **디스크 그룹 만들기** 대화 상자에서 다음을 수행합니다.

   - 디스크 그룹 이름을 입력합니다.
   - **멤버 디스크 선택**에서 **ORCL_DATA** 및 **ORCL_DATA1**을 선택합니다.
   - **할당 단위 크기**에서 **4**를 선택합니다.

   ![디스크 그룹 만들기 대화 상자의 스크린샷](./media/oracle-asm/asm02.png)

4. **ASM 구성: 디스크 그룹** 대화 상자에서 **만들기** 단추를 선택하고 **고급 옵션 표시**를 선택합니다.

   ![ASM 구성: 디스크 그룹 대화 상자의 스크린샷](./media/oracle-asm/asm03.png)

5. **디스크 그룹 만들기** 대화 상자에서 다음을 수행합니다.

   - 디스크 그룹 이름을 입력합니다.
   - **중복**에서 **외부**를 선택합니다.
   - **멤버 디스크 선택**에서 **ORCL_FRA**를 선택합니다.
   - **할당 단위 크기**에서 **4**를 선택합니다.

   ![디스크 그룹 만들기 대화 상자의 스크린샷](./media/oracle-asm/asm04.png)

6. **끝내기**를 선택하여 ASM 구성 도우미를 닫습니다.

   ![끝내기 단추가 있는 ASM 구성: 디스크 그룹 대화 상자의 스크린샷](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>데이터베이스 만들기

Oracle 소프트웨어는 이미 Azure Marketplace 이미지에 설치되어 있습니다. 데이터베이스를 설치하려면 다음 단계를 완료합니다.

1. 사용자를 Oracle superuser로 전환하고, 다음을 로깅하기 위해 수신기를 초기화합니다.

   ```bash
   $ su - oracle
   Password:
   $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   $ ./dbca
    ```
   데이터베이스 구성 도우미가 열립니다.

2. **데이터베이스 작업** 페이지에서 **데이터베이스 만들기**를 선택합니다.

   ![데이터베이스 작업 페이지의 스크린샷](./media/oracle-asm/createdb01.png)
  
3. **생성 모드** 페이지에서,

   - 데이터베이스에 사용할 이름을 입력합니다.
   - **저장소 형식**의 경우 **ASM(자동 저장소 관리)**을 선택합니다.
   - **데이터베이스 파일 위치**의 경우 사용할 폴더를 찾아봅니다.
   - **빠른 복구 영역**의 경우 사용할 폴더를 찾아봅니다.

   ![생성 모드 페이지의 스크린샷](./media/oracle-asm/createdb02.png)

4. **요약** 페이지에서 선택한 설정을 검토한 다음 **마침**을 선택하여 데이터베이스를 만듭니다.

   ![요약 페이지의 스크린샷](./media/oracle-asm/createdb03.png)

5. (선택 사항) **마침** 페이지에서 암호를 변경하려면 **암호 관리**를 선택합니다.

   ![마침 페이지의 스크린샷](./media/oracle-asm/createdb04.png)


## <a name="delete-the-vm"></a>VM 삭제

더 이상 VM이 필요하지 않은 경우 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[자습서: 고가용성 가상 컴퓨터 만들기](../../linux/create-cli-complete.md)

[VM 배포 Azure CLI 샘플 탐색](../../linux/cli-samples.md)

