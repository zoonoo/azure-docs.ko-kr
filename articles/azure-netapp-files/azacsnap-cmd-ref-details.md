---
title: Azure NetApp Files에 대한 Azure Application Consistent Snapshot 세부 정보 가져오기 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구의 자세히 보기 명령을 실행하는 방법에 대한 지침을 제공합니다.
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
ms.openlocfilehash: 1d6a5488ee761acec57e6a1030bcb17872b09258
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865301"
---
# <a name="obtain-details-using-azure-application-consistent-snapshot-tool-preview"></a>Azure Application Consistent Snapshot 도구로 세부 정보 가져오기(미리 보기)

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구의 세부 정보 가져오기 명령을 실행하는 방법에 대한 지침을 제공합니다.

## <a name="introduction"></a>소개

`azacsnap -c details`명령은 구성 파일의 모든 볼륨에 저장된 스냅샷(볼륨 이름, 스냅샷 이름, 만든 시간, 설명, 스냅샷 크기)에 대한 세부 정보를 나열합니다.  추가 분석을 위해 콘솔 출력을 스프레드시트에 붙여 넣을 수 있습니다.

백 엔드 시스템에 저장된 정보로 인해 **Azure의 많은 인스턴스와** **Azure NetApp Files** 에서 실행할 때 명령의 출력이 약간 다릅니다.

## <a name="command-options"></a>명령 옵션

`-c details` 명령에는 다음과 옵션이 있습니다.

- `--details snapshots`은 구성된 각 볼륨의 스냅샷에 대한 기본 세부 정보 목록을 제공합니다. 이 명령은 주 서버 또는 재해 복구 위치의 서버에서 실행할 수 있습니다. 이 명령은 스냅샷이 포함된 각 볼륨별로 다음과 같이 세분화된 정보를 제공합니다.
  - 볼륨의 총 스냅샷 크기
  - 해당 볼륨의 각 스냅샷에는 다음 세부 정보가 포함됩니다.
  - 스냅샷 이름
  - 만든 시간
  - 스냅샷 크기
  - 스냅샷 빈도

- `--details replication`은 프로덕션 사이트에서 재해 복구 사이트로의 복제 상태에 대한 기본 세부 정보를 제공합니다. 이 명령은 모니터링을 통해 복제가 수행되도록 하는 출력을 제공합니다.  복제되는 항목의 크기를 표시합니다. 또한 복제가 너무 오래 걸리거나 연결이 다운될 경우에 대한 지침을 제공합니다.

- `[--configfile <config filename>]`은 사용자 지정 구성 파일 이름을 허용하는 선택적 매개 변수입니다.

### <a name="output-of-the-azacsnap--c-details---details-snapshots-command"></a>`azacsnap -c details --details snapshots` 명령의 출력을 확인합니다

아래 예제는 **Azure Large Instance** 에서 실행되었으며, 간단히 하기 위해 출력이 잘렸습니다.  또한 일부 줄이 출력에 맞게 래핑되는 것을 알 수 있습니다.

```bash
azacsnap -c details --details snapshots
```

```output
List snapshot details called with snapshotFilter ''
#, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
#1, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, hana_hourly.2020-06-17T113043.1586971Z, "Wed Jun 17 11:31:14 2020", "HANA Backup ID: 1592393444174, 702.6MB
#2, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2200.5, "Thu Jul 02 22:01:37 2020", "Backup ID: 1593727205201, 342.3MB
#3, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T220201.5332158Z, "Thu Jul 02 22:03:34 2020", "HANA Backup ID: 1593727322533|azacsnap version: 5.0 Preview (20200617.75879)", 3.27MB
#4, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2205.4, "Thu Jul 02 22:06:36 2020", "Backup ID: 1593727504776, 3.14MB
#5, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T220702.3283669Z, "Thu Jul 02 22:08:37 2020", "HANA Backup ID: 1593727623339|azacsnap version: 5.0 Preview (20200617.75879)", 3.50MB
#6, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2210.3, "Thu Jul 02 22:11:37 2020", "Backup ID: 1593727805216, 2.85MB
#7, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T221201.7013700Z, "Thu Jul 02 22:13:36 2020", "HANA Backup ID: 1593727922724|azacsnap version: 5.0 Preview (20200617.75879)", 3.34MB
#8, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2215.2, "Thu Jul 02 22:16:36 2020", "Backup ID: 1593728104772, 2.73MB
#9, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T221702.2535255Z, "Thu Jul 02 22:18:35 2020", "HANA Backup ID: 1593728223274|azacsnap version: 5.0 Preview (20200617.75879)", 3.39MB
#10, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2220.1, "Thu Jul 02 22:21:37 2020", "Backup ID: 1593728405346, 3.29MB
#11, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T222201.4988618Z, "Thu Jul 02 22:23:36 2020", "HANA Backup ID: 1593728522505|azacsnap version: 5.0 Preview (20200617.75879)", 3.68MB
#12, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2225.0, "Thu Jul 02 22:26:37 2020", "Backup ID: 1593728705321, 2.80MB
#13, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T222702.0521995Z, "Thu Jul 02 22:28:37 2020", "HANA Backup ID: 1593728823058|azacsnap version: 5.0 Preview (20200617.75879)", 1.04MB
, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, , , Size used by Snapshots, 1.05GB
#1, hana_shared_h31_azsollabbl20a31_t210_vol, hana_hourly.2020-06-17T113043.1586971Z, "Wed Jun 17 11:31:12 2020", "HANA Backup ID: 1592393444174, 2.55GB
#2, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2200.5, "Thu Jul 02 22:01:37 2020", "Backup ID: 1593727205201, 4.30MB
#3, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T220201.5332158Z, "Thu Jul 02 22:03:36 2020", "HANA Backup ID: 1593727322533|azacsnap version: 5.0 Preview (20200617.75879)", 8.04MB
#4, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2205.4, "Thu Jul 02 22:06:37 2020", "Backup ID: 1593727504776, 8.12MB
#5, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T220702.3283669Z, "Thu Jul 02 22:08:35 2020", "HANA Backup ID: 1593727623339|azacsnap version: 5.0 Preview (20200617.75879)", 4.28MB
#6, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2210.3, "Thu Jul 02 22:11:37 2020", "Backup ID: 1593727805216, 4.33MB
#7, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T221201.7013700Z, "Thu Jul 02 22:13:34 2020", "HANA Backup ID: 1593727922724|azacsnap version: 5.0 Preview (20200617.75879)", 4.31MB
#8, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2215.2, "Thu Jul 02 22:16:37 2020", "Backup ID: 1593728104772, 4.30MB
#9, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T221702.2535255Z, "Thu Jul 02 22:18:37 2020", "HANA Backup ID: 1593728223274|azacsnap version: 5.0 Preview (20200617.75879)", 7.84MB
#10, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2220.1, "Thu Jul 02 22:21:37 2020", "Backup ID: 1593728405346, 8.09MB
#11, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T222201.4988618Z, "Thu Jul 02 22:23:34 2020", "HANA Backup ID: 1593728522505|azacsnap version: 5.0 Preview (20200617.75879)", 4.34MB
#12, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2225.0, "Thu Jul 02 22:26:37 2020", "Backup ID: 1593728705321, 4.31MB
#13, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T222702.0521995Z, "Thu Jul 02 22:28:35 2020", "HANA Backup ID: 1593728823058|azacsnap version: 5.0 Preview (20200617.75879)", 4.31MB
, hana_shared_h31_azsollabbl20a31_t210_vol, , , Size used by Snapshots, 2.62GB
#1, hana_log_backups_h31_azsollabbl20a31_t210_vol, azacsnap_vesangam_other_test.2020-06-17T113215.4462696Z, "Wed Jun 17 11:32:43 2020", "HANA Log-Backups, 156KB
#2, hana_log_backups_h31_azsollabbl20a31_t210_vol, azacsnap_vesangam_other_test2.2020-06-17T114205.1041364Z, "Wed Jun 17 11:42:35 2020", "HANA Log-Backups, 1.34MB
, hana_log_backups_h31_azsollabbl20a31_t210_vol, , , Size used by Snapshots, 1.49MB
```

> [!NOTE]
> 이 예제에서는 최신 버전을 사용하여 실행되는 스냅샷(5.0) 및 이전 버전(v4.3)을 사용하여 실행되는 스냅샷에 대한 출력을 보여줍니다.

다음 예제에서는 **Azure NetApp Files** 에 대한 출력을 포함하고, 정보가 서비스에 의해 노출되지 않기 때문에 이 명령을 사용하여 축소된 정보를 확인합니다.

```bash
azacsnap -c details --details snapshots
```

```output
List snapshot details called with snapshotFilter ''
#, Volume, SnapshotName
#1, HANADATA_P, snapmirror.dd59bda4-d507-11ea-99fc-00a098f31b77_2155201518.2021-03-22_020000
#2, HANADATA_P, quarter_hourly__2021-03-22T020002-3678211Z
#3, HANADATA_P, quarter_hourly__2021-03-22T014502-0716526Z
#4, HANADATA_P, quarter_hourly__2021-03-22T013002-4080651Z
#5, HANADATA_P, quarter_hourly__2021-03-22T011502-8376045Z
#6, HANADATA_P, quarter_hourly__2021-03-22T010002-8810203Z
#7, HANADATA_P, quarter_hourly__2021-03-22T004502-5983306Z
#8, HANADATA_P, quarter_hourly__2021-03-22T003002-1168834Z
#9, HANADATA_P, quarter_hourly__2021-03-22T001501-9781108Z
#10, HANADATA_P, quarter_hourly__2021-03-22T000002-0865483Z
#1, HANASHARED_P, quarter_hourly__2021-03-22T020002-3678211Z
#2, HANASHARED_P, quarter_hourly__2021-03-22T014502-0716526Z
#3, HANASHARED_P, quarter_hourly__2021-03-22T013002-4080651Z
#4, HANASHARED_P, quarter_hourly__2021-03-22T011502-8376045Z
#5, HANASHARED_P, quarter_hourly__2021-03-22T010002-8810203Z
#6, HANASHARED_P, quarter_hourly__2021-03-22T004502-5983306Z
#7, HANASHARED_P, quarter_hourly__2021-03-22T003002-1168834Z
#8, HANASHARED_P, quarter_hourly__2021-03-22T001501-9781108Z
#9, HANASHARED_P, quarter_hourly__2021-03-22T000002-0865483Z
#10, HANASHARED_P, quarter_hourly__2021-03-21T234502-3935816Z
#1, HANALOGBACKUP_P, logs_5mins__2021-03-22T021002-5462356Z
#2, HANALOGBACKUP_P, logs_5mins__2021-03-22T020502-2390356Z
#3, HANALOGBACKUP_P, logs_5mins__2021-03-22T015502-3928726Z
#4, HANALOGBACKUP_P, logs_5mins__2021-03-22T015001-9228768Z
#5, HANALOGBACKUP_P, logs_5mins__2021-03-22T014002-5554548Z
#6, HANALOGBACKUP_P, logs_5mins__2021-03-22T013502-1781392Z
#7, HANALOGBACKUP_P, logs_5mins__2021-03-22T012502-6686004Z
#8, HANALOGBACKUP_P, logs_5mins__2021-03-22T012002-7348198Z
#9, HANALOGBACKUP_P, logs_5mins__2021-03-22T011002-2234413Z
```

### <a name="output-of-the-azacsnap--c-details---details-replication-command"></a>`azacsnap -c details --details replication` 명령의 출력

이 명령은 기본 사이트에서 DR 위치로 스토리지 복제 상태를 확인하고 **DR 사이트 서버** 에서 실행되어야 *합니다*. 이 명령은 구성 파일의 볼륨 **만** 검사합니다.

> [!NOTE]
> 이 옵션은 HLI(**Azure Large Instance**) 시스템에서만 사용할 수 있습니다.

```bash
azacsnap -c details --details replication
```

```output
Getting replication details for HLI systems
Volume, Link status, Current Replication Activity, Latest Snapshot Replicated, Size of Latest Snapshot Replicated, Current Lag Time between snapshots (HH:MM:SS)
hana_data_h80_mnt00001_t250_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036590.2020-11-02_031000, 7.53MB, 00h 06m 02s
hana_shared_h80_t250_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036594.2020-11-02_031000, 1.64MB, 00h 06m 04s
hana_log_backups_h80_t250_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036635.2020-11-02_031500, 2.32KB, 00h 01m 04s
```

다음 예제에서는 복제 상태가 손상되었으며 이 시나리오에서는 DR 사이트에서 DR을 활성화하면 불완전한 데이터가 생성될 수 있습니다.

> [!NOTE]
> 다음 예제에서는 두 볼륨의 복제를 해제하는 방법에 대한 경고를 확인합니다.

```bash
azacsnap -c details --details replication
```

```output
Getting replication details for HLI systems
Volume, Link status, Current Replication Activity, Latest Snapshot Replicated, Size of Latest Snapshot Replicated, Current Lag Time between snapshots (HH:MM:SS)
hana_data_h80_sapprdhdb80_mnt00001_t020_xdp, Broken-Off - Contact Microsoft Operations immediately!, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036523.2020-03-12_052000, 2.47MB, 99h 99m 99s  WARNING: replication has not occurred for more than 30 minutes!
hana_log_backups_h80_sapprdhdb80_t020_xdp, Broken-Off - Contact Microsoft Operations immediately!, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036539.2020-03-12_052400, 196.7KB, 99h 99m 99s  WARNING: replication has not occurred for more than 30 minutes!
hana_shared_h80_sapprdhdb80_t020_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036524.2020-04-02_020000, 2.94MB, 00h 04m 55s
```

이 예제에서는 기본 사이트와 DR 사이트 간의 성공적인 복제가 있으므로 이러한 시스템이 DR 시나리오를 지원할 수 있습니다.

```bash
azacsnap -c details --details replication
```

```output
Getting replication details for HLI systems
Volume, Link status, Current Replication Activity, Latest Snapshot Replicated, Size of Latest Snapshot Replicated, Current Lag Time between snapshots (HH:MM:SS)
hana_data_h80_sapprdhdb80_mnt00001_t020_xdp, Active, Idle, snapmirror.21215d07-2653-11e8-8e4c-00a098af659c_2157387233.2019-04-09_055000, 106.8MB, 00h 09m 45s
hana_log_backups_h80_sapprdhdb80_t020_xdp, Active, Idle, snapmirror.21215d07-2653-11e8-8e4c-00a098af659c_2157387278.2019-04-09_055700, 75.57MB, 00h 02m 45s
hana_shared_h80_sapprdhdb80_t020_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036524.2020-04-02_020000, 2.94MB, 00h 04m 55s
```

## <a name="next-steps"></a>다음 단계

- [백업 만들기](azacsnap-cmd-ref-backup.md)
- [스냅샷 삭제](azacsnap-cmd-ref-delete.md)
