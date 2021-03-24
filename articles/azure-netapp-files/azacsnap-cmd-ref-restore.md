---
title: Azure NetApp Files에 대 한 Azure 애플리케이션 일치 스냅숏 도구를 사용 하 여 복원 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구의 복원 명령을 실행 하는 방법에 대 한 지침을 제공 합니다.
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
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 793b4da8fcf46ba4d5618f8ada86f9c3c8026ffd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865267"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Azure 애플리케이션 일치 스냅숏 도구를 사용 하 여 복원 (미리 보기)

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구의 복원 명령을 실행 하는 방법에 대 한 지침을 제공 합니다.

## <a name="introduction"></a>소개

스냅숏에서 볼륨 복원을 수행 하는 작업은 명령을 사용 하 여 수행 됩니다 `azacsnap -c restore` .

> [!IMPORTANT]
> 이는 데이터베이스 복구를 수행 하지 않고 아래의 각 옵션에 대해 설명한 대로 볼륨 복원만 수행 합니다.

## <a name="command-options"></a>명령 옵션

명령에는 `-c restore` 다음과 같은 옵션이 있습니다.

- `--restore snaptovol` 대상 볼륨의 최신 스냅숏을 기반으로 새 볼륨을 만듭니다. 이 명령은 새 볼륨을 만들기 위한 기준으로 최신 볼륨 스냅숏을 사용 하 여 구성 된 대상 볼륨을 기반으로 새 "복제 된" 볼륨을 만듭니다.  이 명령은 기본에서 보조로의 저장소 복제를 중단 하지 않습니다. 대신 사용 가능한 최신 스냅숏의 복제본이 DR 사이트에서 생성 되 고 복제 된 볼륨의 권장 파일 시스템 탑재 지점이이 표시 됩니다. 이 명령은 **DR 지역의** Azure Large Instance 시스템 (즉, 대상 장애 조치 (failover) 시스템)에서 실행 해야 합니다.

- `--restore revertvolume` 가장 최근의 스냅숏을 기반으로 대상 볼륨을 이전 상태로 되돌립니다.  이 명령을 연결 된 DR 지역으로 DR 장애 조치 (Failover)의 일부로 사용 합니다. 이 명령은 기본 사이트에서 보조 사이트로의 저장소 복제를 **중지** 하 고, 되돌려 진 dr 볼륨에 대해 권장 되는 filesystem 탑재 지점이와 함께 대상 dr 볼륨을 dr 볼륨에서 사용 가능한 최신 스냅숏으로 되돌립니다. 이 명령은 **DR 지역의** Azure Large Instance 시스템 (즉, 대상 장애 조치 (failover) 시스템)에서 실행 해야 합니다.
    > [!NOTE]
    > 하위 명령 ( `--restore revertvolume` )은 Azure Large 인스턴스에만 사용할 수 있으며 Azure NetApp Files 사용할 수 없습니다.
- `--dbsid <SAP HANA SID>` 볼륨 복원 명령을 적용할 구성 파일에서 선택 하는 SAP HANA SID입니다.

- `[--configfile <config filename>]` 는 사용자 지정 구성 파일 이름을 허용 하는 선택적 매개 변수입니다.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>테스트 DR 장애 조치 (failover) 수행 `azacsnap -c restore --restore snaptovol`

이 명령은 "full" DR 장애 조치 (Failover) 명령 ()과 유사 `--restore restorevolume` 하지만 기본 사이트와 재해 복구 사이트 간의 복제를 중단 하는 대신, 복제 볼륨이 재해 복구 볼륨에서 만들어지므로 DR 사이트에서 최신 스냅숏이 복원 될 수 있습니다. 그런 다음이 복제 된 볼륨을 사용 하 여 기본 사이트와 재해 복구 사이트 간의 복제 계약을 위반 하는 HANA 환경의 전체 장애 조치 (failover)를 실행 하지 않고도 재해 복구를 테스트할 수 있습니다.

- 이러한 방식으로 여러 다른 복원 지점을 테스트 하 여 각각 자체 복원 지점을 사용할 수 있습니다.
- 복제본은 명령이 실행 될 때의 타임 스탬프에 의해 지정 되 고, 실행 시 사용할 수 있는 가장 최근 데이터 및 기타 스냅숏을 나타냅니다.

> [!IMPORTANT]
> 이 작업은 Azure Large 인스턴스에만 적용 됩니다.
>
> - 이 명령을 실행 하면 4 주 후에 복제본을 삭제 하기 전에 liaise 작업에 대 한 연락처 전자 메일을 사용 해야 합니다.
> - 이 명령을 실행 하면 테스트가 완료 될 때 Microsoft 작업에서 삭제 해야 하는 새 복제본이 생성 됩니다.
> - 생성 된 모든 복제 볼륨은 4 주 후 자동으로 삭제 됩니다.

구성 파일 (예:)에는 `DR.json` 프로덕션 볼륨이 아닌 DR 볼륨만 포함 되어야 합니다. 그렇지 않으면 프로덕션 볼륨에 복제본이 생성 될 수 있습니다.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>`azacsnap -c restore --restore snaptovol`명령의 출력 (Single-Node 시나리오의 경우)

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
> "볼륨당 탑재 위치 표시" 출력은 다양 한 시나리오에서 서로 다릅니다.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>전체 DR 장애 조치 (failover) 수행 `azacsnap -c restore --restore revertvolume`

이 명령은 기본 사이트에서 보조 사이트로의 저장소 복제를 **중지** 하 고 dr 볼륨에서 최신 스냅숏을 복원 하 고 dr 볼륨에 대 한 탑재 지점이을 제공 합니다.

DR 볼륨만 포함 된 구성 파일 (예:)을 사용 하 여 DR 서버에서이 명령을 실행 해야 합니다 `DR.json` .

명령을 실행 하 여 DR 사이트로의 장애 조치 (failover)를 수행 `azacsnap -c restore --restore revertvolume` 합니다.  이 명령을 사용 하려면 매개 변수로 SID를 추가 해야 합니다. DR 사이트에서 복구 해야 하는 HANA 인스턴스의 SID입니다.

> [!IMPORTANT]
> DR 연습 또는 테스트를 수행할 계획인 경우에만이 명령을 실행 합니다. 이 명령은 복제를 중단 합니다. 복제를 다시 사용 하도록 설정 하려면 Microsoft 작업에 문의 해야 합니다.

높은 수준에서 DR 장애 조치 (failover)를 실행 하는 단계는 다음과 같습니다.

- **기본** 사이트에서 HANA 인스턴스를 종료 해야 합니다. 이 작업은 데이터 불일치를 방지 하기 위해 DR 사이트로의 장애 조치 (failover)를 수행 하는 경우에만 필요 합니다.
- 프로덕션 SID에 대 한 DR 노드의 HANA 인스턴스를 종료 합니다.
- 복구할 SID를 사용 하 여 DR 노드에서 명령을 실행 합니다 `azacsnap -c restore --restore revertvolume` .
  - 명령은 저장소 복제 링크를 기본에서 DR 사이트로 중단 합니다.
  - 이 명령은 "data" 및 "other" 볼륨을 구성 된 대로 복원 합니다.  일반적으로이 작업은 및 파일 시스템 볼륨에 대 한 `/hana/data` 것 `/hana/logbackups` 입니다.  `/hana/shared`파일 시스템은 복구 되지 않고 `/hana/shared` DR 위치에서 SID에 대 한 기존를 사용 합니다.
  - `/hana/data`및 볼륨 탑재 `/hana/logbackups` – 파일에 추가 되는지 확인 합니다. `/etc/fstab`
- HANA SYSTEMDB 스냅숏을 복원 합니다. HANA studio는 스냅숏 명령 실행의 일부로 복원 된 저장소 스냅숏에서 사용 가능한 최신 HANA 스냅숏으로 표시 합니다 `azacsnap -c restore --restore revertvolume` .
- 테 넌 트 데이터베이스를 복구 합니다.
- 프로덕션 SID에 대 한 DR 사이트에서 HANA 인스턴스를 시작 합니다 (예:이 경우 H80).
- 데이터베이스 테스트를 수행 합니다.

## <a name="next-steps"></a>다음 단계

- [백업 만들기](azacsnap-cmd-ref-backup.md)
- [스냅숏 정보 가져오기](azacsnap-cmd-ref-details.md)
