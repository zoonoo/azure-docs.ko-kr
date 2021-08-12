---
title: Azure NetApp Files에 대한 Azure Application Consistent Snapshot 도구를 사용하여 복원 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구의 복원 명령을 실행하는 방법에 대한 지침을 제공합니다.
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
ms.topic: reference
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: df292fc59d46d59c94f43dae0b1827c068b3e057
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107929870"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool"></a>Azure Application Consistent Snapshot 도구를 사용하여 복원

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구의 복원 명령을 실행하는 방법에 대한 지침을 제공합니다.

## <a name="introduction"></a>소개

스냅숏에서 볼륨 복원을 수행하는 작업은 `azacsnap -c restore` 명령을 사용하여 수행됩니다.

> [!IMPORTANT]
> 이 명령은 데이터베이스 복구를 수행하지 않고 아래의 각 옵션에 대해 설명한 대로 볼륨 복원만 수행합니다.

## <a name="command-options"></a>명령 옵션

`-c restore` 명령에는 다음과 옵션이 있습니다.

- `--restore snaptovol`은 대상 볼륨의 최신 스냅샷을 기반으로 새 볼륨을 만듭니다. 이 명령은 새 볼륨을 만들기 위한 기준으로 최신 볼륨 스냅숏을 사용하여 구성된 대상 볼륨을 기반으로 새 "복제된" 볼륨을 만듭니다.  이 명령을 사용해도 기본 스토리지에서 보조 스토리지로의 스토리지 복제가 중지되지는 않습니다. 대신 사용 가능한 최신 스냅숏의 복제본이 DR 사이트에서 생성되고 복제된 볼륨의 권장 파일 시스템 탑재 지점이 제시됩니다. 이 명령은 **DR 지역의** Azure Large Instance 시스템(즉, 대상 장애 조치 시스템)에서 실행해야 합니다.

- `--restore revertvolume`은 가장 최근의 스냅샷을 기반으로 대상 볼륨을 이전 상태로 되돌립니다.  이 명령은 연결된 DR 지역에 대한 DR 장애 조치의 일부로 사용합니다. 이 명령을 사용하면 기본 사이트에서 보조 사이트로의 스토리지 복제를 **중지** 하고 대상 DR 볼륨을 해당 DR 볼륨의 사용 가능한 최신 스냅샷과 되돌린 DR 볼륨의 filesystem 탑재 지점으로 되돌립니다. 이 명령은 **DR 지역의** Azure Large Instance 시스템(즉, 대상 장애 조치 시스템)에서 실행해야 합니다.
    > [!NOTE]
    > 하위 명령(`--restore revertvolume`)은 Azure Large Instance에만 사용할 수 있으며 Azure NetApp Files에 사용할 수 없습니다.
- `--dbsid <SAP HANA SID>`는 볼륨 복원 명령을 적용할 구성 파일에서 선택하는 SAP HANA SID입니다.

- `[--configfile <config filename>]`은 사용자 지정 구성 파일 이름을 허용하는 선택적 매개 변수입니다.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>테스트 DR 장애 조치 `azacsnap -c restore --restore snaptovol` 수행

이 명령은 "전체" DR 장애 조치 명령(`--restore restorevolume`)과 유사하지만 기본 사이트와 재해 복구 사이트 간의 복제를 중단하는 대신 복제 볼륨이 재해 복구 볼륨에서 만들어지므로 DR 사이트에서 최신 스냅숏이 복원됩니다. 사용자는 그런 다음 이 복제된 볼륨을 사용하여 기본 사이트와 재해 복구 사이트 간의 복제 계약을 위반하는 HANA 환경의 전체 장애 조치를 실행하지 않고도 재해 복구를 테스트할 수 있습니다.

- 이러한 방식으로 여러 다른 복원 지점을 테스트하여 각각 자체 복원 지점을 사용할 수 있습니다.
- 복제본은 명령이 실행될 때의 타임 스탬프에 의해 지정되고, 실행 시 사용할 수 있는 가장 최근 데이터 및 기타 스냅숏을 나타냅니다.

> [!IMPORTANT]
> 이 작업은 Azure Large Instance에만 적용됩니다.
>
> - 이 명령을 실행하면 4주 후에 복제본을 삭제하기 전에 연락을 위해 작업에 연락처 이메일을 사용해야 합니다.
> - 이 명령을 실행하면 테스트가 완료될 때 Microsoft 작업에서 삭제해야 하는 새 복제본이 생성됩니다.
> - 생성된 모든 복제 볼륨은 4주 후 자동으로 삭제됩니다.

구성 파일(예: `DR.json`)에는 프로덕션 볼륨이 아닌 DR 볼륨만 포함되어야 합니다. 그렇지 않으면 프로덕션 볼륨에 복제본이 생성될 수 있습니다.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>`azacsnap -c restore --restore snaptovol` 명령의 출력(단일 노드 시나리오의 경우)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --dbsid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> "볼륨당 탑재 지점 표시 출력"은 다양한 시나리오에서 서로 다르게 작용합니다.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>전체 DR 장애 조치 `azacsnap -c restore --restore revertvolume` 수행

이 명령은 기본 사이트에서 보조 사이트로의 스토리지 복제를 **중지** 하고 DR 볼륨에서 최신 스냅샷을 복원하며 DR 볼륨에 대한 탑재 지점을 제공합니다.

DR 볼륨만 포함된 구성 파일(예: `DR.json`)을 사용하여 DR 서버에서 이 명령을 실행해야 합니다.

`azacsnap -c restore --restore revertvolume` 명령을 실행하여 DR 사이트로의 장애 조치를 수행합니다.  이 명령을 사용하려면 매개 변수로 SID를 추가해야 합니다. DR 사이트에서 복구해야 하는 HANA 인스턴스의 SID입니다.

> [!IMPORTANT]
> DR 연습 또는 테스트를 수행할 계획인 경우에만 이 명령을 실행합니다. 이 명령을 사용하면 복제가 중단됩니다. 복제를 다시 사용하도록 설정하려면 Microsoft Operations에 문의해야 합니다.

높은 수준에서 DR 장애 조치를 실행하는 단계는 다음과 같습니다.

- **기본** 사이트에서 HANA 인스턴스를 종료해야 합니다. 이 작업은 데이터 불일치를 방지하기 위해 실제로 DR 사이트로 장애 조치를 수행하는 경우에만 필요합니다.
- 프로덕션 SID에 대한 DR 노드의 HANA 인스턴스를 종료합니다.
- 복구하려는 SID를 사용하여 DR 노드에서 명령 `azacsnap -c restore --restore revertvolume`을 실행합니다.
  - 해당 명령은 기본 사이트에서 DR 사이트로의 스토리지 복제 링크를 중단합니다.
  - 이 명령을 사용하면 "data" 및 "other" 볼륨을 구성된 대로 복원합니다.  일반적으로 이 작업은 `/hana/data` 및 `/hana/logbackups` 파일 시스템 볼륨에 대한 작업입니다.  `/hana/shared` 파일 시스템은 복구되지 않고 DR 위치에서 SID에 대한 기존 `/hana/shared`를 사용합니다.
  - `/hana/data` 및 `/hana/logbackups` 볼륨 탑재 – `/etc/fstab` 파일에 추가되는지 확인합니다.
- HANA SYSTEMDB 스냅샷을 복원합니다. HANA studio는 스냅샷 명령 `azacsnap -c restore --restore revertvolume` 실행의 일부로 복원된 스토리지 스냅샷에서 사용 가능한 최신 HANA 스냅샷만을 표시합니다.
- 테넌트 데이터베이스를 복구합니다.
- 프로덕션 SID에 대한 DR 사이트에서 HANA 인스턴스를 시작합니다(예: 이 경우 H80).
- 데이터베이스 테스트를 수행합니다.

## <a name="next-steps"></a>다음 단계

- [백업 만들기](azacsnap-cmd-ref-backup.md)
- [스냅샷 정보 가져오기](azacsnap-cmd-ref-details.md)
