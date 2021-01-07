---
title: Azure NetApp Files에 대 한 Azure 애플리케이션 일치 스냅숏 도구 테스트 Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구의 테스트 명령을 실행 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632821"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>테스트 Azure 애플리케이션 일관 된 스냅숏 도구 (미리 보기)

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구의 테스트 명령을 실행 하는 방법을 설명 합니다.

## <a name="introduction"></a>소개

구성에 대 한 테스트는 명령을 사용 하 여 수행 됩니다 `azacsnap -c test` .

## <a name="command-options"></a>명령 옵션

명령에는 `-c test` 다음과 같은 옵션이 있습니다.

- `--test hana`  SAP HANA 인스턴스에 대 한 연결을 테스트 합니다.

- `--test storage` 구성 된 모든 볼륨에서 임시 저장소 스냅숏을 만든 다음 제거 하 여 기본 저장소 인터페이스와의 통신 `data` 을 테스트 합니다. 

- `--test all` 는와 테스트를 차례로 수행 `hana` `storage` 합니다.

- `[--configfile <config filename>]` 는 사용자 지정 구성 파일 이름을 허용 하는 선택적 매개 변수입니다.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>SAP HANA 연결 확인 `azacsnap -c test --test hana`

이 명령은 구성 파일에 있는 모든 HANA 인스턴스에 대 한 HANA 연결을 확인 합니다. HDBuserstore를 사용 하 여 SYSTEMDB에 연결 하 고 SID 정보를 페치합니다.

SSL의 경우이 명령은 다음과 같은 선택적 인수를 사용할 수 있습니다.

- `--ssl=` 데이터베이스와의 암호화 된 연결을 강제 적용 하 고 또는와 SAP HANA 통신 하는 데 사용 되는 암호화 방법을 정의 합니다 `openssl` `commoncrypto` . 정의 된 경우이 명령은 동일한 디렉터리에서 두 개의 파일을 찾으려고 합니다. 이러한 파일은 해당 SID 이후에 이름이 지정 되어야 합니다. [SAP HANA와 통신 하려면 SSL 사용](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)을 참조 하세요.

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>`azacsnap -c test --test hana`명령의 출력

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>저장소에 대 한 연결 확인 `azacsnap -c test --test storage`

`azacsnap`명령은 각 SAP HANA 인스턴스의 볼륨에 대 한 올바른 액세스 권한이 있는지 확인 하기 위해 구성 된 모든 데이터 볼륨에 대 한 스냅숏을 만듭니다. 각 파일 시스템에 대 한 스냅숏 액세스를 확인 하기 위해 각 데이터 볼륨에 대해 임시 스냅숏이 생성 된 후 제거 됩니다.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>`azacsnap -c test --test storage`명령의 출력

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
> Azure Large Instance의 경우 `azacsnap -c test --test storage` 명령은 저장소 생성과 HLI SKU를 추정 합니다.  이 정보에 따라 ' 부팅 ' 스냅숏을 구성 하는 방법에 대 한 지침을 제공 합니다 (출력으로 시작 하는 줄 참조 `Action:` ).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 일치 스냅숏 도구를 사용 하 여 스냅숏 백업](azacsnap-cmd-ref-backup.md)
