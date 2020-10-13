---
title: Azure Data Box를 사용 하 여 파일 Acl, 특성 및 타임 스탬프 유지
description: Azure Data Box SMB를 통해 데이터를 복사 하는 동안 유지 되는 Acl, 타임 스탬프 및 특성입니다. Windows 및 Linux 데이터 복사 도구를 사용 하 여 메타 데이터를 복사 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: alkohli
ms.openlocfilehash: 74b8bfcd8cfedaa7c5e24b6c29d9229a4db5828a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450723"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Azure Data Box를 사용 하 여 파일 Acl, 특성 및 타임 스탬프 유지

Azure Data Box를 사용 하면 Azure에 데이터를 보낼 때 Acl (액세스 제어 목록), 타임 스탬프 및 파일 특성을 유지할 수 있습니다. 이 문서에서는 SMB (서버 메시지 블록)를 통해 Data Box로 데이터를 복사 하 여 Azure Files에 업로드할 때 전송할 수 있는 메타 데이터에 대해 설명 합니다. Windows 및 Linux 데이터 복사 도구를 사용 하 여 메타 데이터를 복사 하기 위한 특정 단계가 제공 됩니다.

이 문서에서는 전송 된 Acl, 타임 스탬프 및 파일 특성을 통칭 하 여 *메타 데이터로*참조 합니다.

## <a name="transferred-metadata"></a>전송 된 메타 데이터

다음 메타 데이터는 Data Box의 데이터가 Azure Files에 업로드 될 때 전송 됩니다.

#### <a name="timestamps"></a>타임스탬프

전송 되는 타임 스탬프는 다음과 같습니다.
- CreationTime
- LastWriteTime

다음 타임 스탬프가 전송 되지 않습니다.
- LastAccessTime
  
#### <a name="file-attributes"></a>파일 특성

다른 설명이 없는 한 파일 및 디렉터리의 파일 특성은 모두 전송 됩니다.

전송 되는 파일 특성은 다음과 같습니다.
- FILE_ATTRIBUTE_READONLY (파일만)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (디렉터리만)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (파일만)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

다음 파일 특성은 전송 되지 않습니다.
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
디렉터리에 대 한 읽기 전용 특성은 전송 되지 않습니다.

#### <a name="acls"></a>ACL

SMB를 통해 Data Box에 복사 하는 디렉터리 및 파일에 대 한 모든 Acl이 복사 되 고 전송 됩니다. 전송에는 Dacl (임의 Acl) 및 Sacl (시스템 Acl)이 모두 포함 됩니다. Linux의 경우 Windows NT Acl만 전송 됩니다.

Acl은 NTS (네트워크 파일 시스템)를 통해 데이터를 복사 하는 동안 이나 데이터 복사 서비스를 사용 하 여 데이터를 전송 하는 동안 전송 되지 않습니다. 데이터 복사 서비스는 공유에서 직접 데이터를 읽고 Acl을 읽을 수 없습니다.

데이터 복사 도구에서 Acl을 복사 하지 않는 경우에도 디렉터리와 파일에 대 한 기본 Acl은 Azure Files 전송 됩니다. 기본 Acl에는 기본 제공 관리자 계정, 시스템 계정 및 Data Box 데이터를 탑재 하 고 복사 하는 데 사용 된 SMB 공유 사용자 계정에 대 한 권한이 있습니다.

Acl에는 Acl, Owner, Group, SACL 속성이 있는 보안 설명자가 포함 됩니다.

Acl 전송은 기본적으로 사용 하도록 설정 되어 있습니다. Data Box의 로컬 웹 UI에서이 설정을 사용 하지 않도록 설정할 수 있습니다. 자세한 내용은 [로컬 웹 UI를 사용 하 여 Data Box 및 Data Box Heavy 관리를](./data-box-local-web-ui-admin.md)참조 하세요.

> [!NOTE]
> 조건부 ACE (액세스 제어 항목) 문자열을 포함 하는 Acl이 있는 파일은 복사 되지 않습니다. 이것은 알려진 문제입니다. 이 문제를 해결 하려면 공유를 탑재 한 다음 Acl 복사를 지 원하는 복사 도구를 사용 하 여 이러한 파일을 Azure Files 공유에 수동으로 복사 합니다.

## <a name="copying-data-and-metadata"></a>데이터 및 메타 데이터 복사

데이터에 대 한 Acl, 타임 스탬프 및 특성을 전송 하려면 다음 절차를 사용 하 여 데이터를 Data Box에 복사 합니다. 

### <a name="windows-data-copy-tool"></a>Windows 데이터 복사 도구

SMB를 통해 Data Box 데이터를 복사 하려면와 같은 SMB 호환 파일 복사 도구를 사용 `robocopy` 합니다. 다음 샘플 명령은 데이터와 함께 메타 데이터를 전송 하는 모든 파일 및 디렉터리를 복사 합니다.

또는 옵션을 사용 하는 경우 `/copyall` `/dcopy:DAT` 필수 Backup 연산자 권한이 사용 되지 않도록 설정 되어 있는지 확인 합니다. 자세한 내용은 [로컬 웹 UI를 사용 하 여 Data Box 및 Data Box Heavy 관리를](./data-box-local-web-ui-admin.md)참조 하세요. 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

where

|옵션 |설명 |
|------------------- | ----- |
|`/copyall` |모든 특성을 복사 합니다.|
|`/e`      |빈 디렉터리를 포함 하 여 하위 디렉터리를 복사 합니다.         |
|`/dcopy:DAT`  |데이터, 특성 및 타임 스탬프를 복사 합니다. 참고: 디렉터리에서 전송 하려면/dcopy: DAT 옵션을 사용 해야 합니다. `CreationTime` |
|`/r:3`    |실패 한 복사본에 대해 3 번 다시 시도를 지정 합니다.         |
|`/w:60`   |재시도 사이의 대기 시간을 60 초로 지정 합니다.         |
|`/is`     |동일한 파일을 포함합니다.         |
|`/nfl`    |는 파일 이름을 기록 하지 않습니다.         |
|`/ndl`    |는 디렉터리 이름을 기록 하지 않습니다.        |
|`/np`     |복사 작업의 진행률을 표시 하지 않습니다.         |
|`/MT:32 or 64`  |32 또는 64 스레드와 함께 다중 스레딩을 사용 합니다.           |
|`/fft`    |모든 파일 시스템에 대 한 타임 스탬프 세분성을 줄입니다.        |
|`/log+:<LogFile>`  |기존 로그 파일에 출력을 추가합니다.|

이러한 매개 변수에 대 한 자세한 내용은 `robocopy` [자습서: SMB를 통해 Azure Data Box에 데이터 복사](./data-box-deploy-copy-data.md) 를 참조 하세요.

### <a name="linux-data-copy-tool"></a>Linux 데이터 복사 도구

Linux에서 메타 데이터 전송은 2 단계 프로세스로 진행 됩니다. 먼저 메타 데이터를 복사 하지 않는와 같은 도구를 사용 하 여 원본 데이터를 복사 `rsync` 합니다. 데이터를 복사한 후 또는와 같은 도구를 사용 하 여 메타 데이터를 복사할 수 있습니다 `smbcacls` `cifsacl` . 

다음 샘플 명령은를 사용 하 여 데이터를 복사 하는 첫 번째 단계를 수행 합니다 `rsync` . 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>다음 단계

- [SMB를 통해 Azure Data Box에 데이터 복사](./data-box-deploy-copy-data.md)