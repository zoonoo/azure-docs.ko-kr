---
title: Azure NetApp Files에 대한 Azure Application Consistent Snapshot 도구 구성하기 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구의 구성 명령을 실행하는 방법에 대한 지침을 제공합니다.
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
ms.openlocfilehash: f12e9ab2ab0faf77520805e3be645a14dbd38885
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107929996"
---
# <a name="configure-azure-application-consistent-snapshot-tool"></a>Azure 애플리케이션 일치 스냅샷 도구 구성

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구의 구성 명령을 실행하는 방법에 대한 지침을 제공합니다.

## <a name="introduction"></a>소개

`azacsnap -c configure` 명령을 사용하여 구성 파일을 만들거나 편집할 수 있습니다.

## <a name="command-options"></a>명령 옵션

`-c configure` 명령 옵션은 다음과 같습니다

- `--configuration new`로 새 구성 파일을 만듭니다.

- `--configuration edit`로 기존 구성 파일을 편집합니다.

- `[--configfile <config filename>]`은 사용자 지정 구성 파일 이름을 허용하는 선택적 매개 변수입니다.

## <a name="configuration-file-for-snapshot-tools"></a>스냅샷 도구 구성 파일

구성 파일은 `azacsnap -c configure --configuration new`을 실행하여 만들 수 있습니다.  기본적으로 구성 파일 이름은 `azacsnap.json`입니다.  사용자 지정 이름 파일은 `--configfile=` 매개 변수(예: `--configfile=<customname>.json`)와 함께 사용할 수 있습니다. 다음의 예제는 Azure 대규모 인스턴스 구성에 대한 것입니다:

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>필요한 값의 세부 정보

다음 섹션에서는 구성 파일에 필요한 다양한 값에 대한 자세한 지침을 제공합니다.

### <a name="sap-hana-values"></a>SAP HANA 값

구성에 *데이터베이스* 를 추가할 때 다음 값이 필요합니다:

- **HANA 서버 주소** = SAP HANA 서버 호스트 이름 또는 IP 주소입니다.
- **HANA SID** = SAP HANA 시스템 ID입니다.
- **HANA 인스턴스 번호** = SAP HANA 인스턴스 번호입니다.
- **HANA HDB 사용자 저장소 키** = 데이터베이스 백업을 실행할 수 있는 권한으로 구성된 SAP HANA 사용자입니다.

- 단일 노드: 노드의 IP 및 호스트 이름
- STONITH가 있는 HSR: 노드의 IP 및 호스트 이름
- 스케일 아웃(N+N, N+M): 현재 마스터 노드 IP 및 호스트 이름
- STONITH가 없는 HSR: 노드의 IP 및 호스트 이름
- 단일 노드의 다중 SID: 해당 SID를 호스팅하는 노드의 호스트 이름 및 IP

### <a name="azure-large-instance-hli-storage-values"></a>HLI (Azure 대규모 인스턴스) 스토리지 값

*HLI 스토리지* 를 데이터베이스 섹션에 추가할 때 다음 값이 필요합니다:

- **스토리지 사용자 이름** = 이 값은 스토리지에 대한 SSH 연결을 설정하는데 사용되는 사용자 이름입니다.
- **스토리지 IP 주소** = 스토리지 시스템의 주소입니다.
- **스토리지 볼륨 이름** = 스냅샷에 대한 볼륨 이름입니다.  이 값은 여러 방법으로 결정할 수 있습니다. 아마 가장 간단한 방법은 다음의 셸 명령을 시도하는 것입니다:

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>ANF(Azure NetApp Files) 스토리지 값

*ANF 스토리지* 를 데이터베이스 섹션에 추가할 때 다음 값이 필요합니다:

- **서비스 사용자 인증 파일 이름** = Azure NetApp Files 스토리지와의 통신을 구성할 때 Cloud Shell에서 생성되는 `authfile.json` 파일입니다.
- **전체 ANF 스토리지 볼륨 리소스 ID** = 스냅샷이 되는 볼륨의 전체 리소스 ID입니다.  Azure Portal –> ANF –> 볼륨 –> 설정/속성 –> 리소스 ID에서 검색할 수 있습니다

## <a name="configuration-file-overview-azacsnapjson"></a>구성 파일 개요 (`azacsnap.json`)

다음 예제에서는 `azacsnap.json`은 하나의 SID로 구성됩니다.

매개 변수 값은 고객의 특정 SAP HANA 환경으로 설정해야 합니다.
**Azure 대규모 인스턴스** 시스템의 경우 이 정보는 온보딩/인계 호출 중에 Microsoft Service Management에서 제공하며 인계 중에 제공되는 Excel 파일에서 사용할 수 있습니다. 이 정보를 다시 제공해야 하는 경우 서비스 요청을 엽니다.

다음은 예제일 뿐이며 모든 값을 적절하게 업데이트합니다.

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
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

> [!NOTE]
> DR 사이트에서 백업을 실행하는 DR 시나리오의 경우 DR 사이트의 DR 구성 파일(예: `DR.json`)에 구성된 HANA 서버 이름이 프로덕션 서버 이름과 동일해야 합니다.

> [!NOTE]
> Azure Large Instance의 경우 스토리지 IP 주소는 서버 풀과 동일한 서브넷에 있어야합니다. 예를 들어 이 경우 서버 풀 서브넷은 172입니다. 18. 18 .0/24 및 할당된 스토리지 IP는 172.18.18.11입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Application Consistent Snapshot Tool 테스트](azacsnap-cmd-ref-test.md)
