---
title: RMAN 및 Azure Storage를 사용 하 여 Azure Linux VM에서 Oracle Database 19c 데이터베이스 백업
description: Azure 클라우드 저장소에 Oracle Database 19c 데이터베이스를 백업 하는 방법에 대해 알아봅니다.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: a6ce5446bd6470ef7a829925646d486801b28ebc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670013"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Azure Storage를 사용 하 여 Azure Linux VM에서 Oracle Database 19c 데이터베이스 백업 및 복구

이 문서에서는 Azure VM에서 실행 되는 Oracle 데이터베이스를 백업 및 복원 하기 위한 미디어로 Azure Storage를 사용 하는 방법을 보여 줍니다. SMB 프로토콜을 사용 하 여 VM에 탑재 된 Azure File storage에 Oracle RMAN을 사용 하 여 데이터베이스를 백업 합니다. 백업 미디어에 Azure storage를 사용 하는 것은 매우 비용 효율적이 고 성능이 우수 합니다. 그러나 매우 큰 데이터베이스의 경우 Azure Backup는 더 나은 솔루션을 제공 합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- 백업 및 복구 프로세스를 수행 하려면 먼저 Oracle Database 19c의 설치 된 인스턴스를 포함 하는 Linux VM을 만들어야 합니다. 현재 VM을 만드는 데 사용 되는 Marketplace 이미지는  **oracle: oracle-database-19-3: oracle-0904: 최신** 입니다. [Oracle 데이터베이스 만들기 빠른](./oracle-database-quick-create.md) 시작의 단계에 따라이 자습서를 완료 하는 oracle 데이터베이스를 만듭니다.

## <a name="prepare-the-database-environment"></a>데이터베이스 환경 준비

1. VM으로 SSH(Secure Shell) 세션을 만들려면 다음 명령을 사용합니다. IP 주소 및 호스트 이름 조합을 VM에 대한 `publicIpAddress` 값으로 바꿉니다.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. ***루트*** 사용자로 전환 합니다.
 
   ```bash
   sudo su -
   ```
    
3. ***/Etc/sudoers*** 파일에 oracle 사용자를 추가 합니다.

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. 이 단계에서는 *vmoracle19c* 이라는 VM에서 실행 되는 Oracle 인스턴스 (테스트)가 있다고 가정 합니다.

   사용자를 *oracle* 사용자로 전환 합니다.

   ```bash
   sudo su - oracle
   ```
    
5. 연결하기 전에 환경 변수 ORACLE_SID를 설정해야 합니다.
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   또한 다음 명령을 사용하여 향후 로그인에 사용할 `oracle` 사용자 `.bashrc` 파일에 ORACLE_SID 변수를 추가해야 합니다.

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Oracle 수신기가 아직 실행 되 고 있지 않으면 시작 합니다.
    
   ```bash
   $ lsnrctl start
   ```

    출력은 다음 예제와 유사하게 표시됩니다.

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

7.  FRA (빠른 복구 영역) 위치를 만듭니다.

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  데이터베이스에 연결 합니다.

    ```bash
    sqlplus / as sysdba
    ```

9.  아직 실행 중이 아닌 경우 데이터베이스를 시작 합니다.

    ```bash
    SQL> startup
    ```

10. 빠른 복구 영역에 대 한 데이터베이스 환경 변수 설정:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. 온라인 백업을 사용 하도록 설정 하려면 데이터베이스가 보관 로그 모드에 있는지 확인 합니다.
    먼저 로그 보관 상태를 확인 합니다.

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    NOARCHIVELOG 모드의 경우 sqlplus에서 다음 명령을 실행 합니다.

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. 백업 및 복원 작업을 테스트 하는 테이블을 만듭니다.

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Azure Files에 백업

Azure Files에 백업 하려면 다음 단계를 완료 합니다.

1. Azure File Storage을 설정 합니다.
1. VM에 Azure Storage 파일 공유를 탑재 합니다.
1. 데이터베이스를 백업합니다.
1. 데이터베이스를 복원 및 복구 합니다.

### <a name="set-up-azure-file-storage"></a>Azure File Storage 설정

이 단계에서는 Oracle Recovery Manager (RMAN)를 사용 하 여 Azure File storage에 Oracle 데이터베이스를 백업 합니다. Azure 파일 공유는 클라우드에 상주 하는 완전히 관리 되는 파일 공유입니다. 서버 메시지 블록 (SMB) 프로토콜 또는 NFS (네트워크 파일 시스템) 프로토콜을 사용 하 여 액세스할 수 있습니다. 이 단계에서는 SMB 프로토콜을 사용 하 여 VM에 탑재 하는 파일 공유를 만드는 방법을 설명 합니다. NFS를 사용 하 여 탑재 하는 방법에 대 한 자세한 내용은 [nfs 3.0 프로토콜을 사용 하 여 Blob Storage 탑재](../../../storage/blobs/network-file-system-protocol-support-how-to.md)를 참조 하세요.

Azure Files를 탑재 하는 경우 `cache=none` 파일 공유 데이터의 캐싱을 사용 하지 않도록 설정 하는 데를 사용 합니다. 또한 공유에 생성 된 파일을 oracle 사용자가 소유 하 고 있는지 확인 하기 위해 `uid=oracle` 및 옵션도 설정 합니다 `gid=oinstall` . 

# <a name="portal"></a>[포털](#tab/azure-portal)

먼저 저장소 계정을 설정 합니다.

1. Azure Portal에서 File Storage 구성

    Azure Portal에서 ***+ 리소스 만들기** _를 선택 하 고을 검색 한 후 _ *_저장소 계정_* 을 선택 합니다.*
    
    ![리소스를 만들고 저장소 계정을 선택할 수 있는 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/storage-1.png)
    
2. 저장소 계정 만들기 페이지에서 기존 리소스 그룹 ***rg-oracle** _를 선택 하 고, 저장소 계정의 이름을 _*_Oracbkup1_*_ 으로, 계정 종류에 대해 _*_저장소 V2 (일반 용도 V2)_*_ 를 선택 합니다. 복제를 _*_LRS (로컬 중복 저장소)_*_ 로 변경 하 고 성능을 _ *_표준_* *로 설정 합니다. 위치가 리소스 그룹의 다른 모든 리소스와 동일한 지역으로 설정 되어 있는지 확인 합니다. 
    
    ![기존 리소스 그룹을 선택할 수 있는 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. ***고급** _ 탭을 클릭 하 고 Azure Files에서 _*_큼 파일 공유_*_ 를 _ *_사용_* *으로 설정 합니다. 검토 + 만들기를 클릭 한 다음 만들기를 클릭 합니다.
    
    ![대량 파일 공유를 사용 하도록 설정할 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. 저장소 계정이 만들어지면 리소스로 이동 하 여 ***파일 공유*** 를 선택 합니다.
    
    ![파일 공유를 선택할 수 있는 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/file-storage-3.png)
    
5. ***+ 파일 공유** _를 클릭 하 고 _*_새 파일 공유_*_ 블레이드 이름 파일 공유 _*_orabkup1_*_ 을 클릭 합니다. _*_할당량_*_ 을 _*_10240_*_ GiB로 설정 하 고 계층으로 _*_최적화 된 트랜잭션을_*_ 확인 합니다. 할당량은 파일 공유를 확장할 수 있는 상한을 반영 합니다. 표준 저장소를 사용 하는 것 처럼 리소스가 PAYG 되어 프로 비전 되지 않으므로 10 TiB로 설정 해도 사용 하는 것 보다 비용이 발생 하지 않습니다. 백업 전략에 더 많은 저장소가 필요한 경우 모든 백업을 보관할 적절 한 수준으로 할당량을 설정 해야 합니다.   새 파일 공유 블레이드를 완료 했으면 _ *_만들기_* *를 클릭 합니다.
    
    ![새 파일 공유를 추가할 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. 만든 경우 파일 공유 설정 페이지에서 ***orabkup1*** 을 클릭 합니다. 
    ***연결** _ 탭을 클릭 하 여 연결 블레이드를 열고 _ *_Linux_** 탭을 클릭 합니다. 제공 된 명령을 복사 하 여 SMB 프로토콜을 사용 하 여 파일 공유를 탑재 합니다. 
    
    ![저장소 계정 추가 페이지](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

저장소 계정 및 파일 공유를 설정 하려면 Azure CLI에서 다음 명령을 실행 합니다.

1. VM과 동일한 리소스 그룹 및 위치에 저장소 계정을 만듭니다.
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. 할당량 10 TiB의 저장소 계정에서 파일 공유를 만듭니다.

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. VM에 파일 공유를 탑재할 때 필요한 저장소 계정 기본 키 (key1)를 검색 합니다.

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>VM에 Azure Storage 파일 공유 탑재

1. 탑재 지점을 만듭니다.

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. 자격 증명 설정:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   `<Your Storage Account Key1>`다음 명령을 실행 하기 전에 먼저 검색 된 저장소 계정 키를 대체 합니다.
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. 자격 증명 파일에 대 한 사용 권한 변경:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. /Etc/fstab에 탑재를 추가 합니다.

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. SMB 프로토콜을 사용 하 여 Azure Files 저장소를 탑재 하는 명령을 실행 합니다.

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   다음과 유사한 오류가 표시 되는 경우

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   그런 다음, CIFS 패키지가 Linux 호스트에 설치 되지 않을 수 있습니다. 
   
   CI가 설치 되어 있는지 확인 하려면 다음 명령을 실행 합니다.

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   명령이 출력을 반환 하지 않으면 다음 명령을 사용 하 여 CIFS 패키지를 설치 합니다.

   ```bash 
   sudo yum install cifs-utils
   ```

   이제 위의 탑재 명령을 다시 실행 하 Azure Files 저장소를 탑재 합니다.

6. 다음 명령을 사용 하 여 파일 공유가 제대로 탑재 되었는지 확인 합니다.

   ```bash
   df -h
   ```

   출력은 다음과 비슷한 모양입니다.

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>데이터베이스 백업

이 섹션에서는 RMAN (Oracle Recovery Manager)을 사용 하 여 데이터베이스의 전체 백업을 수행 하 고 로그를 보관 하 고 이전에 탑재 된 Azure 파일 공유에 백업 세트를 백업으로 작성 합니다. 

1. Azure Files 탑재 지점에 백업할 RMAN을 구성 합니다.

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Azure 표준 파일 공유의 최대 파일 크기는 1 TiB 이므로 RMAN 백업 부분의 크기를 1 TiB로 제한 합니다. 프리미엄 파일 공유의 최대 파일 크기 제한은 TiB 4입니다. 자세한 내용은 [Azure Files 확장성 및 성능 목표](../../../storage/files/storage-files-scale-targets.md)를 참조 하세요.)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. 구성 변경 세부 정보를 확인 합니다.

    ```bash
    RMAN> show all;
    ```

4. 이제 백업을 실행 합니다. 다음 명령을 사용 하 여 보관 파일을 포함 하는 전체 데이터베이스 백업을 압축 된 형식으로 backupset.   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

이제 Oracle RMAN을 사용 하 여 데이터베이스를 온라인으로 백업 했으며,이 백업은 Azure File storage에 있습니다. 이 방법은 Azure File storage에 백업을 저장 하는 동안 RMAN의 기능을 활용 하 여 다른 Vm에서 액세스할 수 있는 기능을 활용 하 여 데이터베이스를 복제 해야 하는 경우에 유용 합니다.  
    
데이터베이스 백업에 RMAN 및 Azure File storage를 사용 하는 경우 많은 이점이 있습니다. 백업 및 복원 시간은 데이터베이스 크기에 연결 되므로 매우 큰 데이터베이스의 경우 이러한 작업에 상당한 시간이 걸릴 수 있습니다.  응용 프로그램 일치 VM 백업을 Azure Backup 사용 하는 대체 백업 메커니즘은 스냅숏 기술을 사용 하 여 백업을 수행 합니다 .이 경우 데이터베이스 크기에 관계 없이 백업 속도가 매우 빠릅니다. Recovery Services 자격 증명 모음과 통합 하면 다른 Vm 및 Azure 지역에서 액세스할 수 있는 Azure 클라우드 저장소의 Oracle Database 백업에 대 한 안전한 저장소를 제공 합니다. 

### <a name="restore-and-recover-the-database"></a>데이터베이스 복원 및 복구

1.  Oracle 인스턴스를 종료 합니다.

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  데이터 파일 및 백업을 제거 합니다.

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. 다음 명령은 RMAN을 사용 하 여 누락 된 데이터 파일를 복원 하 고 데이터베이스를 복구 합니다.

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. 데이터베이스 콘텐츠가 완전히 복구 되었는지 확인 합니다.

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


이제 Azure Linux VM에서 Oracle Database 19c 데이터베이스의 백업 및 복구가 완료 되었습니다.

## <a name="delete-the-vm"></a>VM 삭제

더 이상 VM이 필요하지 않은 경우 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>다음 단계

[자습서: 고가용성 VM 만들기](../../linux/create-cli-complete.md)

[VM 배포 Azure CLI 샘플 탐색](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)