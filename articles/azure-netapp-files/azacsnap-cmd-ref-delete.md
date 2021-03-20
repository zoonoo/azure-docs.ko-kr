---
title: Azure NetApp Files에 대 한 Azure 애플리케이션 일치 스냅숏 도구를 사용 하 여 삭제 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구의 delete 명령을 실행 하는 방법에 대 한 지침을 제공 합니다.
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
ms.openlocfilehash: 0e2e4beebedb93524da43c5a3fad750b0295f5cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632908"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Azure 애플리케이션 일치 스냅숏 도구를 사용 하 여 삭제 (미리 보기)

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구의 delete 명령을 실행 하는 방법에 대 한 지침을 제공 합니다.

## <a name="introduction"></a>소개

명령을 사용 하 여 볼륨 스냅숏과 데이터베이스 카탈로그 항목을 삭제할 수 있습니다 `azacsnap -c delete` .

> [!IMPORTANT]
> 스냅숏 복제와의 간섭을 받을 수 있으므로이 명령을 실행 하기 전에 10 분 이내에 만들어진 스냅숏은 삭제 하면 안 됩니다.

## <a name="command-options"></a>명령 옵션

명령에는 `-c delete` 다음과 같은 옵션이 있습니다.

- `--delete hana` 옵션과 함께 사용 하는 경우에는 `--hanasid <SID>` `--hanabackupid <HANA backup id>` 조건과 일치 하는 SAP HANA 백업 카탈로그에서 항목을 삭제 합니다.

- `--delete storage` 옵션과 함께 사용 하는 경우 `--snapshot <snapshot name>` 백 엔드 저장소 시스템에서 스냅숏을 삭제 합니다.

- `--delete sync`옵션과 함께 사용 `--hanasid <SID>` 하 고의 `--hanabackupid <HANA backup id>` 백업 카탈로그에서 저장소 스냅숏 이름을 가져온 `<HANA backup id>` 다음 백업 카탈로그의 항목과 명명 된 스냅숏이 포함 된 볼륨의 스냅숏을  모두 삭제 합니다.

- `--delete sync`에서 사용 하는 경우에 `--snapshot <snapshot name>` 대 한 백업 카탈로그에 있는 항목을 확인 하 `<snapshot name>` 고, SAP HANA 백업 ID를 가져오고, 명명 된 스냅숏이 포함 된  볼륨에서 백업 카탈로그의 항목과 스냅숏을 모두 삭제 합니다.

- `[--force]` 필드 *주의 해 서 사용* 합니다.  이 작업은 확인 메시지를 표시 하지 않고 강제로 삭제 합니다.

- `[--configfile <config filename>]` 는 사용자 지정 구성 파일 이름을 허용 하는 선택적 매개 변수입니다.

### <a name="delete-a-snapshot-using-sync-option"></a>' 옵션 '을 사용 하 여 스냅숏 삭제 `sync`

```bash
azacsnap -c delete --delete sync --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> 백업 카탈로그에서 SAP HANA 백업 ID 157979797979에 대 한 항목을 확인 하 고, 저장소 스냅숏 이름을 가져오고, 명명 된 스냅숏이 포함 된 모든 볼륨의 스냅숏 및 백업 카탈로그의 항목을 모두 삭제 합니다.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Hana_hourly .2020-01 -22 _2358 이라는 스냅숏에 대 한 백업 카탈로그의 모든 항목을 확인 하 고, SAP HANA 백업 ID를 가져오고, 명명 된 스냅숏이 포함 된 볼륨에서 백업 카탈로그의 항목과 스냅숏을 모두 삭제 합니다.

### <a name="delete-a-snapshot-using-hana-option"></a>' 옵션 '을 사용 하 여 스냅숏 삭제 `hana`

```bash
azacsnap -c delete --delete hana --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> SID H80 백업 카탈로그에서 SAP HANA 백업 ID 157979797979를 삭제 합니다.

### <a name="delete-a-snapshot-using-storage-option"></a>' 옵션 '을 사용 하 여 스냅숏 삭제 `storage`

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Hana_hourly .2020-01 -22 _2358 이라는 스냅숏이 포함 된 볼륨에서 스냅숏을 삭제 합니다.

**옵션을 사용 하 여 출력 `--delete storage`**

사용자에 게 삭제를 확인 하 라는 메시지가 표시 됩니다.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

선택적 매개 변수를 다음과 같이 사용 하 여 사용자 확인을 방지할 수 있습니다 `--force` .

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>다음 단계

- [스냅숏 정보 가져오기](azacsnap-cmd-ref-details.md)
- [백업 만들기](azacsnap-cmd-ref-backup.md)
