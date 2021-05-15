---
title: Azure NetApp Files에 대한 Azure Application Consistent Snapshot 도구를 사용하여 삭제 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구의 삭제 명령을 실행하는 방법에 대한 지침을 제공합니다.
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
ms.openlocfilehash: 1f2c767d45bb08e25a057c7db1f380ceb250f607
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864910"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Azure Application Consistent Snapshot 도구로 삭제(미리 보기)

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구의 삭제 명령을 실행하는 방법에 대한 지침을 제공합니다.

## <a name="introduction"></a>소개

`azacsnap -c delete` 명령을 사용하여 볼륨 스냅샷과 데이터베이스 카탈로그 항목을 삭제할 수 있습니다.

> [!IMPORTANT]
> 스냅샷 복제와의 간섭이 있을 수 있으므로 이 명령을 실행하기 전 10분 이내에 만들어진 스냅샷은 삭제하면 안 됩니다.

## <a name="command-options"></a>명령 옵션

`-c delete` 명령에는 다음과 옵션이 있습니다.

- `--dbsid <SID>`와 `--hanabackupid <HANA backup id>` 옵션과 함께 사용하는 경우, `--delete hana`은 기준과 일치하는 SAP HANA 백업 카탈로그에서 항목을 삭제합니다.

- `--snapshot <snapshot name>` 옵션과 함께 사용하는 경우, `--delete storage`은 백 엔드 스토리지 시스템에서 스냅샷을 삭제합니다.

- `--dbsid <SID>`와 `--hanabackupid <HANA backup id>` 옵션과 함께 사용하는 경우, `--delete sync`은 `<HANA backup id>`에 대한 백업 카탈로그에서 스토리지 스냅샷 이름을 가져온 다음, 백업 카탈로그의 항목 _및_ 명명된 스냅샷이 포함된 볼륨의 스냅샷을 삭제합니다.

- `--snapshot <snapshot name>`과 함께 사용하는 경우, `--delete sync`은 `<snapshot name>`에 대한 백업 카탈로그에 있는 항목을 확인하고, SAP HANA 백업 ID를 가져오며, 백업 카탈로그의 항목 및 명명된 스냅샷이 포함된 볼륨에서 스냅샷을 삭제합니다.

- `[--force]`(선택 사항)*주의해서 사용합니다*.  이 작업은 확인 메시지를 표시하지 않고 강제로 삭제합니다.

- `[--configfile <config filename>]`은 사용자 지정 구성 파일 이름에 허용하는 선택적 매개 변수입니다.

### <a name="delete-a-snapshot-using-sync-option"></a>`sync` 옵션을 사용하여 스냅샷을 제거하기

```bash
azacsnap -c delete --delete sync --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> SAP HANA 백업 ID 157979797979에 대한 백업 카탈로그에 있는 항목을 확인하고, 스토리지 스냅샷 이름을 가져오며, 백업 카탈로그의 항목 및 명명된 스냅샷이 포함된 볼륨에서 스냅샷을 삭제합니다.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> hana_hourly.2020-01-22_2358 스냅샷에 대한 백업 카탈로그에 있는 항목을 확인하고, SAP HANA 백업 ID를 가져오며, 백업 카탈로그의 항목 및 명명된 스냅샷이 포함된 볼륨에서 스냅샷을 삭제합니다.

### <a name="delete-a-snapshot-using-hana-option"></a>`hana` 옵션을 사용하여 스냅샷을 제거하기

```bash
azacsnap -c delete --delete hana --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> SID H80의 백업 카탈로그에서 SAP HANA 백업 ID 157979797979를 삭제합니다.

### <a name="delete-a-snapshot-using-storage-option"></a>`storage` 옵션을 사용하여 스냅샷을 제거하기

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> hana_hourly.2020-01-22_2358 스냅샷이 포함된 볼륨에서 스냅샷을 삭제합니다.

**`--delete storage` 옵션을 사용하여 출력**

사용자는 삭제를 확인하라는 요청을 받습니다.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

선택적 `--force` 매개 변수를 다음과 같이 사용하여 사용자 확인을 무시할 수 있습니다.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>다음 단계

- [스냅샷 상세 정보 가져오기](azacsnap-cmd-ref-details.md)
- [백업 만들기](azacsnap-cmd-ref-backup.md)
