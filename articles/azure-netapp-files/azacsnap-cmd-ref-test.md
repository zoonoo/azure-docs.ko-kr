---
title: Azure NetApp Files에 대한 Azure Application Consistent Snapshot Tool 테스트 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Application Consistent Snapshot Tool의 테스트 명령을 실행하는 방법을 설명합니다.
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
ms.openlocfilehash: 86a670602f448f59decbc64d8a12278697c43596
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107929852"
---
# <a name="test-azure-application-consistent-snapshot-tool"></a>Azure Application Consistent Snapshot Tool 테스트

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot Tool의 테스트 명령을 실행하는 방법을 설명합니다.

## <a name="introduction"></a>소개

구성 테스트는 `azacsnap -c test` 명령을 사용하여 수행됩니다.

## <a name="command-options"></a>명령 옵션

`-c test` 명령에는 다음과 같은 옵션이 있습니다.

- `--test hana`는 SAP HANA 인스턴스에 대한 연결을 테스트합니다.

- `--test storage`는 구성된 모든 `data` 볼륨에서 임시 스토리지 스냅샷을 만든 다음 이를 제거하여 기본 스토리지 인터페이스와의 통신을 테스트합니다. 

- `--test all`은 `hana` 및 `storage` 테스트 모두를 순차적으로 수행합니다.

- `[--configfile <config filename>]`은 사용자 지정 구성 파일 이름을 허용하는 선택적 매개 변수입니다.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>SAP HANA와의 연결 확인 `azacsnap -c test --test hana`

이 명령은 구성 파일에 있는 모든 HANA 인스턴스에 대한 HANA 연결을 확인합니다. HDBuserstore를 사용하여 SYSTEMDB에 연결하고 SID 정보를 페치합니다.

SSL의 경우 이 명령은 다음과 같은 선택적 인수를 사용할 수 있습니다.

- `--ssl=`은 데이터베이스와의 암호화된 연결을 강제 실행하고 SAP HANA와 통신하는 데 사용되는 암호화 메서드(`openssl` 또는 `commoncrypto`)를 정의합니다. 정의된 경우 이 명령은 동일한 디렉터리에서 두 개의 파일을 찾습니다. 이러한 파일은 해당 SID 뒤에 이름이 지정되어야 합니다. [SAP HANA와의 통신을 위한 SSL 사용](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)을 참조하세요.

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>`azacsnap -c test --test hana` 명령의 출력

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>스토리지와의 연결 확인 `azacsnap -c test --test storage`

`azacsnap` 명령은 각 SAP HANA 인스턴스의 볼륨에 대한 올바른 액세스 권한이 있는지 확인하도록 구성된 모든 데이터 볼륨에 대한 스냅샷을 생성합니다. 각 파일 시스템에 대한 스냅샷 액세스를 확인하기 위해 각 데이터 볼륨에 대해 임시 스냅샷이 생성된 후 제거됩니다.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>`azacsnap -c test --test storage` 명령의 출력

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> Azure Large Instance의 경우 `azacsnap -c test --test storage` 명령은 스토리지 생성 및 HLI SKU를 추정합니다.  그런 다음 이 정보를 기반으로 '부팅' 스냅샷 구성에 대한 참고 자료를 제공합니다(`Action:` 출력으로 시작하는 줄 참조).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>다음 단계

- [Azure Application Consistent Snapshot 도구를 사용한 스냅샷 백업](azacsnap-cmd-ref-backup.md)
