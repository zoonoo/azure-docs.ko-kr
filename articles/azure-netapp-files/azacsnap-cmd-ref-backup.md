---
title: Azure NetApp Files에 대 한 Azure 애플리케이션 일치 스냅숏 도구를 사용 하 여 백업 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구의 백업 명령을 실행 하는 방법에 대 한 지침을 제공 합니다.
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
ms.openlocfilehash: 1051859d514c77bad1aa5f14becc2218a923df44
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632992"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>Azure 애플리케이션 일치 스냅숏 도구를 사용 하 여 백업 (미리 보기)

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구의 백업 명령을 실행 하는 방법에 대 한 지침을 제공 합니다.

## <a name="introduction"></a>소개

저장소 스냅숏 기반 백업은 명령을 사용 하 여 실행 됩니다 `azacsnap -c backup` .  이 명령은 데이터 볼륨에서 데이터베이스 일관성 저장소 스냅숏의 오케스트레이션을 수행 하 고 다른 볼륨에는 데이터베이스 일관성 설정 없이 저장소 스냅숏을 수행 합니다.  

데이터 볼륨의 경우 `azacsnap` 저장소 스냅숏에 대 한 데이터베이스를 준비 합니다. 그러면 구성 된 모든 볼륨에 대 한 저장소 스냅숏이 생성 됩니다. 마지막으로, 데이터베이스에 스냅숏이 완료 되었음을 알려 주어 야 합니다.  또한 스냅숏 백업 작업 (예: 백업 카탈로그 SAP HANA)을 기록 하는 모든 데이터베이스 항목을 관리 합니다.

## <a name="command-options"></a>명령 옵션

이 `-c backup` 명령은 다음 인수를 사용 합니다.

- `--volume=`스냅숏에 대 한 볼륨의 유형입니다 .이 매개 변수는 또는을 포함할 수 있습니다. `data``other`
  - `data` 구성 파일의 dataVolume stanza 내에서 볼륨을 스냅숏 합니다.
  - `other` 구성 파일의 otherVolume stanza 내에서 볼륨을 스냅숏 합니다.
  
  > [!NOTE]
  > 상주 볼륨으로 부팅 볼륨을 사용 하 여 별도의 구성 파일을 만들면 `boot` 스냅숏을 완전히 다른 일정 (예: 매일)으로 수행할 수 있습니다.

- `--prefix=` 스냅숏 이름에 대 한 고객 스냅숏 접두사입니다. 이 매개 변수는 두 가지 용도로 사용 됩니다. 먼저 스냅숏의 그룹화에 고유한 이름을 지정 합니다. 그런 다음 `--retention` 지정 된에 대해 유지 되는 저장소 스냅숏의 수를 확인 합니다 `--prefix` .

    > [!IMPORTANT]
    > 알파 숫자 ("a-z, a-z, 0-9"), 밑줄 ("_") 및 대시 ("-") 문자만 사용할 수 있습니다.

- `--retention` 유지 하도록 정의 된의 스냅숏 수입니다 `--prefix` . 이를 위해 새 스냅숏이 생성 된 후에는 추가 스냅숏이 제거 됩니다 `--prefix` .

- `--trim` SAP HANA v2 이상에서 사용할 수 있습니다 .이 옵션은 백업 카탈로그와 디스크 카탈로그 및 로그 백업을 유지 관리 합니다. 백업 카탈로그에 유지할 항목 수는 위의 옵션에 따라 결정 되며, `--retention` 백업 카탈로그에서 정의 된 접두사 ()의 이전 항목과 `--prefix` 관련 된 물리적 로그 백업을 삭제 합니다. 또한 가장 오래 된 로그 백업 항목 보다 오래 된 로그 백업 항목을 모두 삭제 합니다. 이 작업을 통해 로그 백업에서 사용 가능한 모든 디스크 공간을 사용 하지 않도록 방지할 수 있습니다.

  > [!NOTE]
  > 다음 예제 명령은 9 개의 저장소 스냅숏을 유지 하 고 보존 중인 9 개의 저장소 스냅숏에 맞게 백업 카탈로그가 지속적으로 트리밍 되는지 확인 합니다.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` SAP HANA와 통신 하는 데 사용 되는 암호화 방법을 정의 하는 선택적 매개 변수입니다 ( `openssl` 또는) `commoncrypto` . 정의 된 경우 `azacsnap -c backup` 이 명령은 동일한 디렉터리에서 두 개의 파일을 찾으려고 합니다. 이러한 파일은 해당 SID 이후에 이름이 지정 되어야 합니다. [SAP HANA와 통신 하려면 SSL 사용](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)을 참조 하세요. 다음 예제에서는 접두사를 사용 하 `hana` 여 형식 스냅숏을 사용 하 `hana_TEST` 고 `9` SSL ()을 사용 하 여 SAP HANA와 통신 하는 것을 유지 합니다 `openssl` .

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` 는 사용자 지정 구성 파일 이름을 허용 하는 선택적 매개 변수입니다.

## <a name="snapshot-backups-are-fast"></a>스냅숏 백업 속도 빠름

스냅숏 백업의 지속 시간은 볼륨 크기와는 독립적 이며, 10gb 볼륨과 동일한 예상 시간 내에 10TB 볼륨이 맞춰집니다.  

전반적인 실행 시간에 영향을 주는 주요 요소는 스냅숏의 볼륨 수와 `--retention` 매개 변수의 변경 내용 (감소 하는 경우 초과 스냅숏이 제거 될 때 실행 시간을 증가 시킬 수 있음)입니다.

위의 예제 구성 ( **Azure Large Instance** 의 경우)에서는 두 볼륨에 대 한 스냅숏이 완료 되는 데 5 초 미만 걸렸습니다. **Azure NetApp Files** 의 경우 두 볼륨의 스냅숏은 60 초 정도 걸립니다.

> [!NOTE]
> 이전에 `--retention` `azacsnap` 를 실행 하는 경우 (예:에서로)에서가 크게 `--retention 50` 줄어들면 `--retention 5` `azacsnap` 추가 스냅숏을 제거 하기 위해 소요 되는 시간이 늘어납니다.

## <a name="example-with-data-parameter"></a>`data`매개 변수가 있는 예제

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

명령은 콘솔에 출력 되지 않지만 로그 파일, 결과 파일 및에 기록 `/var/log/messages` 됩니다.

*로그 파일* 은 명령 이름 +-c 옵션 + 구성 파일 이름으로 구성 됩니다. 기본적으로에 대 한 로그 파일 이름은 `-c backup` 기본 구성 파일 이름으로 실행 `azacsnap-backup-azacsnap.log` 됩니다.

*결과* 파일은 로그 파일과 동일한 기본 이름을 사용 합니다 `.result` . 예를 `azacsnap-backup-azacsnap.result` 들어 다음 출력을 포함 하는 접미사를 사용 합니다.

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

파일에는 `/var/log/messages` 파일과 동일한 출력이 포함 되어 있습니다 `.result` . 다음 예제 (실행 루트)를 참조 하세요.

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>`other`매개 변수가 있는 예제

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

명령은 콘솔에 출력 되지 않지만 로그 파일에만 기록 됩니다.  결과 파일 또는 _에 쓰지 않습니다_ `/var/log/messages` .

*로그 파일* 은 명령 이름 +-c 옵션 + 구성 파일 이름으로 구성 됩니다. 기본적으로에 대 한 로그 파일 이름은 `-c backup` 기본 구성 파일 이름으로 실행 `azacsnap-backup-azacsnap.log` 됩니다.

## <a name="example-with-other-parameter-to-backup-host-os"></a>`other`매개 변수가 있는 예제 (호스트 OS를 백업 하려면)

> [!NOTE]
> 부팅 볼륨만 포함 하는 다른 구성 파일 ()을 사용 합니다 `--configfile bootVol.json` .

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

명령은 콘솔에 출력 되지 않지만 로그 파일에만 기록 됩니다.  결과 파일 또는 _에 쓰지 않습니다_ `/var/log/messages` .

이 예제의 *로그 파일* 이름은 `azacsnap-backup-bootVol.log` 입니다.

> [!NOTE]
> 로그 파일 이름은 "(명령 이름-( `-c` 옵션)-(구성 파일 이름)"으로 구성 됩니다.  예를 들어 `-c backup` 옵션을 로그 파일 이름과 함께 사용 하는 경우 `h80.json` 로그 파일이 호출 됩니다 `azacsnap-backup-h80.log` .  또는 옵션을 동일한 구성 파일에 사용 하는 경우 `-c test` 로그 파일이 호출 됩니다 `azacsnap-test-h80.log` .

- HANA Large Instance 유형: `TYPEI` 또는 `TYPEII` HANA Large instance 단위에 종속 된 두 개의 유효한 값이 있습니다.
- 사용 가능한 Sku를 확인 하려면 [HANA Large Instances에 대해 사용 가능한 sku](/azure/virtual-machines/workloads/sap/hana-available-skus) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [스냅숏 정보 가져오기](azacsnap-cmd-ref-details.md)
- [스냅샷 삭제](azacsnap-cmd-ref-delete.md)
