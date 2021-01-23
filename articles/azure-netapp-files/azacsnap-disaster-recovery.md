---
title: Azure NetApp Files에 대 한 Azure 애플리케이션 일치 스냅숏 도구를 사용 하 여 재해 복구 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구를 사용 하는 경우 재해 복구를 수행 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 70e1823b30814d7dc29fef69215fcb53a2a2ab96
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730873"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Azure 애플리케이션 일치 스냅숏 도구를 사용 하 여 재해 복구 (미리 보기)

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구를 사용 하는 경우 재해 복구를 수행 하는 방법을 설명 합니다.

> [!IMPORTANT]
> 이 작업은 **Azure Large 인스턴스에만** 적용 됩니다.

## <a name="introduction"></a>소개

Azure 대규모 인스턴스 플랫폼에는 저장소 볼륨 스냅숏을 복제할 수 있는 재해 복구 사이트가 구성 될 수도 있습니다.  이러한 설정으로 스냅숏이 올바르게 구성 된 경우이 사이트에서 재해 복구를 수행할 수 있습니다.  이 문서는이 설치에 대 한 재해 복구를 수행 하는 방법에 대 한 지침을 제공 합니다.

## <a name="prerequisites-for-disaster-recovery-setup"></a>재해 복구 설치를 위한 필수 구성 요소

재해 복구 장애 조치 (failover)를 계획 하려면 먼저 다음 필수 구성 요소를 충족 해야 합니다.

- Dr 사이트에서 프로 비전 된 DR 노드가 있습니다. DR에 대 한 두 가지 옵션이 있습니다. 하나는 정상적인 DR 이며 다른 하나는 다목적 DR입니다.
- 저장소 복제가 작동 하 고 있습니다. Microsoft 운영 팀은 DR 프로 비전 시 저장소 복제 설정을 자동으로 수행 합니다. DR 사이트에서 명령을 사용 하 여 저장소 복제를 모니터링할 수 있습니다 `azacsnap -c details --details replication` .
- 기본 위치에서 저장소 스냅숏을 설정 하 고 구성 했습니다.
- 주 인스턴스와 동일한 SID를 가진 주 복제본에 대해 DR 사이트에 HANA 인스턴스가 설치 되어 있어야 합니다.
- [Azure의 고가용성 및 재해 복구 SAP HANA(대규모 인스턴스)](../virtual-machines/workloads/sap/hana-failover-procedure.md) 에 설명 된 DR 장애 조치 (Failover) 절차를 읽고 이해 합니다.
- DR 위치에서 저장소 스냅숏을 설정 하 고 구성 했습니다.
- Dr `DR.json` 저장소 볼륨 및 dr 서버에 관련 된 정보를 사용 하 여 구성 파일 (예:)을 만들었습니다.
- DR 사이트에서 다음 단계를 완료 했습니다.
  - 저장소와의 통신을 사용 하도록 설정 합니다.
  - SAP HANA와의 통신을 사용 하도록 설정 합니다.

## <a name="set-up-disaster-recovery"></a>재해 복구 설정

Microsoft는 DR 복구에 대 한 저장소 수준 복제를 지원 합니다. DR을 설정 하는 방법에는 두 가지가 있습니다.

하나는 **정상적인** 것이 고 다른 하나는 **다목적** 입니다. **일반** dr의 경우 장애 조치 (failover)를 위한 DR 위치에 전용 인스턴스가 있습니다. **다용도** dr 시나리오에서는 DR 사이트의 hana 대량 인스턴스 단위에서 실행 되는 다른 QA 또는 개발 HANA 인스턴스가 있습니다. 그러나 대기 상태 이며 해당 HANA 대량 인스턴스 단위로 장애 조치 (failover) 하려는 HANA 인스턴스와 동일한 SID를 가진 미리 설치 된 HANA 인스턴스도 설치 했습니다. Microsoft 운영은 온 보 딩 시에 SRF (서비스 요청 폼)에 제공 된 입력을 기반으로 저장소 복제를 포함 하 여 환경을 설정 합니다.

> [!IMPORTANT]
> DR 설정에 대해 모든 필수 구성 요소를 충족 하는지 확인 합니다.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>기본 사이트에서 DR 사이트로 데이터 복제 모니터링

Microsoft 운영 팀은 기본 사이트에서 DR 사이트로 DR 링크를 이미 관리 하 고 모니터링 합니다.
Snapshot 명령을 사용 하 여 주 서버에서 DR 서버로의 데이터 복제를 모니터링할 수 있습니다 `azacsnap -c details --details replication` .

## <a name="perform-a-failover-to-dr-site"></a>DR 사이트로 장애 조치 (failover) 수행

DR 사이트 ()에서 장애 조치 (failover `azacsnap -c restore --restore revertvolume` ) 명령을 실행 합니다.

> [!IMPORTANT]
> `azacsnap -c restore --restore revertvolume`명령은 프로덕션 사이트에서 DR 사이트로의 저장소 복제를 중단 합니다. 복제를 다시 설정 하려면 Microsoft 작업에 연결 해야 합니다. 복제를 다시 사용 하도록 설정 하면이 SID에 대 한 DR 저장소의 모든 데이터가 초기화 됩니다. 장애 조치 (failover)를 수행 하는 명령을 사용 하면 가장 최근에 복제 된 저장소 스냅숏을 사용할 수 있습니다. 이전 스냅숏으로 복원 해야 하는 경우 작업을 통해 DR 사이트에서 복원 된 이전 스냅숏을 제공할 수 있도록 지원 요청을 엽니다.

높은 수준에서 DR 장애 조치 (failover)를 위해 수행 해야 하는 단계는 다음과 같습니다.

- **기본** 사이트에서 HANA 인스턴스를 종료 해야 합니다. 이 작업은 DR 사이트로의 장애 조치 (failover)를 수행 하 여 데이터 불일치가 발생 하지 않는 경우에만 필요 합니다.
- 프로덕션 SID에 대 한 DR 노드의 HANA 인스턴스를 종료 합니다.
- `azacsnap -c restore --restore revertvolume`복구할 SID를 사용 하 여 DR 노드에서 명령을 실행 합니다.
  - 명령은 저장소 복제 링크를 기본에서 DR 사이트로 중단 합니다.
  - 이 명령은/data 및/logbackups 볼륨만 복원 하 고/공유 볼륨은 복구 되지 않으며, 대신 DR 위치에서 SID에 대 한 기존/공유를 사용 합니다.
  - /Data 및/logbackups 볼륨 탑재 – fstab 파일에 추가 해야 합니다.
- HANA SYSTEMDB 스냅숏을 복원 합니다. HANA studio는 명령 실행의 일부로 복원 된 저장소 스냅숏에서 사용 가능한 최신 HANA 스냅숏으로 표시 합니다 `azacsnap -c restore --restore revertvolume` .
- 테 넌 트 데이터베이스를 복구 합니다.
- 프로덕션 SID에 대 한 DR 사이트에서 HANA 인스턴스를 시작 합니다 (예:이 경우 H80).
- 테스트를 수행 합니다.

### <a name="example-performing-disaster-recovery"></a>재해 복구를 수행 하는 예제

이 하위 섹션에서는 재해 복구 사이트로 장애 조치 (failover) 하는 자세한 단계에 대해 설명 합니다.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>1 단계: DR 노드의 볼륨 세부 정보 가져오기

`df –h`장애 조치 (failover) 후에 참조할 파일 시스템 및 연결 된 볼륨을 나열 하는 명령을 실행 합니다.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>2 단계: 기본 사이트에서 HANA 종료

프로덕션 작업의 전체 장애 조치 (failover)를 수행 하는 경우 기본 프로덕션 사이트에 연결할 수 있는 경우 장애 조치 (failover) 되는 SAP HANA 인스턴스를 DR으로 종료 합니다.

예를 들어 root로 로그인 한 경우 다음 예에서는 SAP HANA를 종료 하는 방법을 보여 줍니다.  <sid>을 SAP HANA SID로 바꿉니다.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>3 단계: DR 사이트에서 HANA 종료

볼륨을 복원 하기 전에 DR 사이트에서 SAP HANA를 종료 하는 것이 중요 합니다.

예를 들어 root로 로그인 한 경우 다음 예에서는 SAP HANA를 종료 하는 방법을 보여 줍니다.  <sid>을 SAP HANA SID로 바꿉니다.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> 볼륨을 복원 하기 전에 DR 사이트의 HANA 인스턴스가 오프 라인 상태 인지 확인 합니다.

#### <a name="step-4-restore-the-volumes"></a>4 단계: 볼륨 복원

```bash
azacsnap -c restore --restore revertvolume --hanasid H80
```

**_DR 장애 조치 (failover) 명령의 출력_** 입니다.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --hanasid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> 콘솔 표시의 끝에 있는 단계를 수행 하 여 DR 장애 조치 (failover)에 대 한 저장소 준비를 완료 해야 합니다.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>5 단계: 불필요 한 파일 시스템 분리

명령을 실행 `umount` 하 여 필요 하지 않은 파일 시스템/볼륨을 분리 합니다.

```bash
umount <Mount point>
```

데이터 및 로그 백업 탑재 지점이를 분리 합니다. 스케일 아웃 시나리오에서는 여러 데이터를 탑재 해야 할 수 있습니다.

#### <a name="step-6-configure-the-mount-points"></a>6 단계: 탑재 위치 구성

파일을 수정 `/etc/fstab` 하 여 주 sid에 대 한 데이터 및 로그 백업 항목을 주석으로 처리 하 고 (이 예에서는 SID = H80) 기본 사이트 DR 볼륨에서 만든 새 탑재 지점 항목을 추가 합니다. 새 탑재 지점 항목이 명령 출력에 제공 됩니다.

- 다음 문자를 사용 하 여 DR 사이트에서 실행 중인 기존 탑재 위치를 주석으로 처리 합니다 `#` .

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- 다음 줄을에 추가 합니다. `/etc/fstab`
  > 이는 명령과 동일한 출력 이어야 합니다.

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>7 단계: 복구 볼륨 탑재

명령을 실행 `mount –a` 하 여 모든 탑재 위치를 탑재 합니다.

```bash
mount -a
```

이제를 실행 하면 `df –h` 볼륨이 탑재 된 것을 볼 수 있습니다 `*_dp` .

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>8 단계: SYSTEMDB 복구

HANA Studio에서 SYSTEMDB 인스턴스를 마우스 오른쪽 단추로 클릭 하 고 "백업 및 복구"를 선택한 다음 "시스템 데이터베이스 복구"를 선택 합니다.

스냅숏에서 데이터베이스를 복구 하는 방법, 특히 SYSTEMDB를 참조 하십시오.

#### <a name="step-9-recover-the-tenant-database"></a>9 단계: 테 넌 트 데이터베이스 복구

HANA Studio에서 SYSTEMDB 인스턴스를 마우스 오른쪽 단추로 클릭 하 고 "백업 및 복구"를 선택한 다음 "테 넌 트 데이터베이스 복구"를 선택 합니다.

스냅숏에서 데이터베이스를 복구 하려면 특히 테 넌 트 데이터베이스 가이드를 참조 하세요.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>`azacsnap -c backup`DR 사이트에서 실행

DR 사이트에서 스냅숏 기반 백업을 실행 하는 경우 DR 사이트의 구성 파일에 구성 된 HANA 서버 이름이 `azacsnap` 프로덕션 서버 이름과 동일 해야 합니다.

> [!IMPORTANT]
> 를 실행 하면 `azacsnap -c backup` DR 사이트에서 저장소 스냅숏이 생성 될 수 있으며, 이러한 스냅숏은 자동으로 다른 사이트에 복제 되지 않습니다.  Microsoft 작업을 사용 하 여 원본 프로덕션 사이트로 파일이 나 데이터를 다시 반환 하는 것을 더 잘 이해할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [스냅숏 정보 가져오기](azacsnap-cmd-ref-details.md)
- [백업 만들기](azacsnap-cmd-ref-backup.md)
