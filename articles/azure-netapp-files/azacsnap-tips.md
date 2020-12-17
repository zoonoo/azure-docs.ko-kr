---
title: Azure NetApp Files에 대 한 Azure 애플리케이션 일치 스냅숏 도구를 사용 하기 위한 팁과 요령 Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구를 사용 하기 위한 팁과 요령을 제공 합니다.
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
ms.openlocfilehash: d73bfd19a4135d09e9e19fcbcfedd50dbc1f7067
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632808"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>Azure 애플리케이션 일치 스냅숏 도구를 사용 하기 위한 팁과 요령 (미리 보기)

이 문서에서는 AzAcSnap를 사용할 때 도움이 될 수 있는 팁과 요령을 제공 합니다.

## <a name="limit-service-principal-permissions"></a>서비스 사용자 권한 제한

AzAcSnap 서비스 주체의 범위를 제한 해야 할 수도 있습니다.  Azure 리소스의 세분화 된 액세스 관리에 대 한 자세한 내용은 [AZURE RBAC 설명서](https://docs.microsoft.com/azure/role-based-access-control/) 를 검토 하세요.  

다음은 AzAcSnap를 작동 하는 데 필요한 최소한의 작업을 수행 하는 역할 정의의 예입니다.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

## <a name="take-snapshots-manually"></a>수동으로 스냅숏 만들기

백업 명령 ()을 실행 하기 전에 `azacsnap -c backup` 테스트 명령을 실행 하 여 구성을 확인 하 고 성공적으로 실행 되었는지 확인 합니다.  이러한 테스트의 올바른 실행은 `azacsnap` 설치 된 SAP HANA 데이터베이스와 **Azure Large Instance** 또는 **Azure NetApp Files** 시스템에서 SAP HANA의 기본 저장소 시스템과 통신할 수 있습니다.

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

그런 다음, 수동 데이터베이스 스냅숏 백업을 수행 하려면 다음 명령을 실행 합니다.

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>자동 스냅숏 백업 설정

`cron`시스템에서 실행 되는 명령을 자동화 하는 데 사용 하는 Unix/Linux 시스템에서 일반적으로 사용 됩니다. 스냅숏 도구의 표준 방법은 사용자의를 설정 하는 것입니다 `crontab` .

`crontab`사용자가 `azacsnap` 스냅숏을 자동화 하는 예는 다음과 같습니다.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

위의 crontab에 대 한 설명입니다.

- `MAILTO=""`: 빈 값을 사용 하 여 cron가 로컬 사용자의 메일 파일에 나올 가능성이 있으므로 crontab 항목을 실행할 때 자동으로 사용자에 게 전자 메일을 시도 하지 않도록 합니다.
- Crontab 항목에 대 한 간단한 타이밍 버전은 별도의 설명이 필요 없습니다.
  - `@monthly` = 한 달에 한 번 실행 합니다. 즉, "0 0 1 * *"입니다.
  - `@weekly`  = 매주 한 번 실행 됩니다. 즉, "0 0 * * 0"입니다.
  - `@daily`   = 하루에 한 번 실행 합니다. 즉, "0 0 * * *"입니다.
  - `@hourly`  = 한 시간에 한 번 실행 합니다. 즉, "0 * * * *"입니다.
- 처음 다섯 개의 열을 사용 하 여 시간을 지정 합니다. 아래 열 예제를 참조 하십시오.
  - `0,15,30,45`: 15 분 마다
  - `0-23`: 매 시간
  - `*` : 매일
  - `*` : 매월
  - `*` : 매일 요일
- 괄호 "()" 안에 포함 된 실행 명령줄
  - `. /home/azacsnap/.profile` = $PATH 등 환경을 설정 하기 위해 사용자의 프로필을 가져옵니다.
  - `cd /home/azacsnap/bin` = 구성 파일이 있는 "/home/azacsnap/bin" 위치로 실행 디렉터리를 변경 합니다.
  - `azacsnap -c .....` = 모든 옵션을 포함 하 여 실행할 전체 azacsnap 명령입니다.

Cron 및 crontab 파일의 형식에 대 한 자세한 설명은 다음과 같습니다. <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> 사용자는 스냅숏이 성공적으로 생성 되 고 있는지 확인 하기 위해 cron 작업을 모니터링 해야 합니다.

## <a name="monitor-the-snapshots"></a>스냅숏 모니터링

정상적인 시스템이 되도록 하려면 다음 조건을 모니터링 해야 합니다.

1. 사용 가능한 디스크 공간 이전 디스크 블록을 스냅숏에 유지 하기 때문에 스냅숏은 디스크 공간을 느리게 소비 합니다.
    1. 디스크 공간 관리를 자동화 하는 데 도움이 되도록 `--retention` 및 옵션을 사용 `--trim` 하 여 이전 스냅숏 및 데이터베이스 로그 파일을 자동으로 정리 합니다.
1. 스냅숏 도구가 성공적으로 실행 되었습니다.
    1. 파일에서 `*.result` 최신 실행의 성공 또는 실패를 확인 합니다 `azacsnap` .
    1. `/var/log/messages`명령에서 출력을 확인 `azacsnap` 합니다.
1. 다른 시스템에 주기적으로 복원 하 여 스냅숏의 일관성을 유지 합니다.

> [!NOTE]
> 스냅숏 정보를 나열 하려면 명령을 실행 `azacsnap -c details` 합니다.

## <a name="delete-a-snapshot"></a>스냅숏 삭제

스냅숏을 삭제 하려면 명령을 실행 `azacsnap -c delete` 합니다. OS 수준에서 스냅숏을 삭제 하는 것은 불가능 합니다. `azacsnap -c delete`저장소 스냅숏을 삭제 하려면 올바른 명령 ()을 사용 해야 합니다.

> [!IMPORTANT]
> 스냅숏을 삭제 하는 경우 유의 합니다. 삭제 한 후에는 삭제 된 스냅숏을 복구할 수 **없습니다** .

## <a name="restore-a-snapshot"></a>스냅샷 복원

저장소 볼륨 스냅숏은 새 볼륨 ()으로 복원할 수 있습니다 `-c restore --restore snaptovol` .  Azure 대규모 인스턴스의 경우 볼륨을 스냅숏 ()으로 되돌릴 수 있습니다 `-c restore --restore revertvolume` .

> [!NOTE]
> 제공 된 데이터베이스 복구 명령이 **없습니다** .

스냅숏은 SAP HANA 데이터 영역에 다시 복사할 수 있지만 복사본이 생성 될 때 () SAP HANA 실행 되 고 있지 않아야 합니다 `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` .

Azure Large Instance의 경우 사용 가능한 기존 스냅숏에서 원하는 스냅숏을 복원 하는 서비스 요청을 열어 Microsoft 운영 팀에 문의할 수 있습니다. Azure Portal에서 서비스 요청을 열 수 있습니다. <https://portal.azure.com.>

재해 복구 장애 조치 (failover)를 수행 하기로 결정 한 경우 `azacsnap -c restore --restore revertvolume` DR 사이트의 명령이 SAP HANA 복구를 위해 최신 ( `/hana/data` 및 `/hana/logbackups` ) 볼륨 스냅숏을 자동으로 사용할 수 있게 됩니다. 프로덕션 및 DR 사이트 간 복제가 중단 되는 경우이 명령을 주의 해 서 사용 합니다.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>' 부팅 ' 볼륨에 대해서만 스냅숏 설정

> [!IMPORTANT]
> 이 작업은 Azure Large 인스턴스에만 적용 됩니다.

일부 경우에는 SAP HANA를 보호 하 고 ' boot ' 볼륨 스냅숏을 구성할 수 있는 도구가 이미 있습니다.  이 경우 작업이 간소화 되 고 다음 단계를 수행 해야 합니다.

1. 설치를 위한 필수 구성 요소 1-4 단계를 완료 합니다.
1. 저장소와의 통신을 사용 하도록 설정 합니다.
1. 설치 관리자 실행을 다운로드 하 여 스냅숏 도구를 설치 합니다.
1. 스냅숏 도구의 설치를 완료 합니다.
1. 다음과 같이 새 구성 파일을 만듭니다. 부팅 볼륨 세부 정보는 OtherVolume stanza ( <span style="color:red">빨간색</span>의 사용자 항목)에 있어야 합니다.
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. 구성 파일을 확인 하 고 다음 예제를 참조 하십시오.

    `cat`명령을 사용 하 여 구성 파일의 내용을 표시 합니다.

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0 Preview",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume": "t210_sles_boot_azsollabbl20a31_vol"
                  }
                ]
              }
            ],
            "anfStorage": []
          }
        }
      ]
    }
    ```

1. 부팅 볼륨 백업 테스트

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. 나열 된 확인란을 선택 `--snapshotfilter` 하 여 반환 되는 스냅숏 목록을 제한 하는 옵션을 추가 합니다.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    명령 출력:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. 자동 스냅숏 백업을 설정 합니다.

> [!NOTE]
> SAP HANA와의 통신 설정은 필요 하지 않습니다.

## <a name="restore-a-boot-snapshot"></a>' 부팅 ' 스냅숏 복원

> [!IMPORTANT]
> 이 작업은 Azure Large Instance 모드에 대 한 작업입니다.
> 서버가 스냅숏이 만들어진 시점으로 복원 됩니다.

' Boot ' 스냅숏은 다음과 같이 복구할 수 있습니다.

1. 고객은 서버를 종료 해야 합니다.
1. 서버가 종료 된 후 고객은 복원할 컴퓨터 ID와 스냅숏이 포함 된 서비스 요청을 열어야 합니다.
    > 고객은 Azure Portal에서 서비스 요청을 열 수 있습니다. <https://portal.azure.com.>
1. Microsoft는 지정 된 컴퓨터 ID와 스냅숏을 사용 하 여 운영 체제 LUN을 복원한 다음 서버를 부팅 합니다.
1. 그러면 고객은 서버가 부팅 되 고 정상 상태 인지 확인 해야 합니다.

복원 후 수행할 추가 단계가 없습니다.

## <a name="key-facts-to-know-about-snapshots"></a>스냅숏에 대해 알아야 할 주요 사항

저장소 볼륨 스냅숏의 주요 특성:

- **스냅숏 위치**: 스냅숏은 볼륨 내의 가상 디렉터리 ()에서 찾을 수 있습니다 `.snapshot` .  **Azure Large Instance** 에 대 한 다음 예제를 참조 하세요.
  - 데이터 `/hana/data/<SID>/mnt00001/.snapshot`
  - 공유할 `/hana/shared/<SID>/.snapshot`
  - 로그온 `/hana/logbackups/<SID>/.snapshot`
  - 부팅: HLI의 부팅 스냅숏은 OS 수준에서 **표시 되지** 않지만을 사용 하 여 나열할 수 있습니다 `azacsnap -c details` .

  > [!NOTE]
  >  `.snapshot` 는 스냅숏에 대 한 읽기 전용 액세스를 제공 하는 읽기 전용 숨겨진 *가상* 폴더입니다.

- **최대 스냅숏:** 하드웨어는 볼륨당 최대 250 개의 스냅숏을 유지할 수 있습니다. Snapshot 명령은 명령줄의 보존 집합에 따라 접두사에 대 한 최대 스냅숏 수를 유지 하 고 가장 오래 된 스냅숏이 보존할 최대 개수를 초과 하는 경우이를 삭제 합니다.
- **스냅숏 이름:** 스냅숏 이름에는 고객에 의해 제공 된 접두사 레이블이 포함 됩니다.
- **스냅숏의 크기:** 데이터베이스 수준의 크기/변경 내용에 따라 달라 집니다.
- **로그 파일 위치:** 명령으로 생성 되는 로그 파일은 JSON 구성 파일에 정의 된 폴더에 출력 됩니다 .이 파일은 기본적으로 명령이 실행 되는 하위 폴더입니다 (예: `./logs` ).

## <a name="next-steps"></a>다음 단계

- [문제 해결](azacsnap-troubleshoot.md)
