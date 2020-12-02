---
title: Linux 가상 머신에서 전체 OS 디스크 문제
description: Linux 가상 머신에서 전체 OS 디스크를 사용 하 여 문제를 해결 하는 방법
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523534"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Linux 가상 머신에서 전체 OS 디스크 문제

Linux VM (가상 머신)의 OS 디스크가 꽉 차면 VM의 적절 한 작동에 문제가 발생할 수 있습니다.

## <a name="symptom"></a>증상

새 파일을 만들려고 하면 다음과 같은 메시지가 표시 됩니다.

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

그러면 여러 디먼 부팅 세션 중에 임시 파일을 만들 수 없다는 것을 의미 합니다.

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>원인

이 오류 메시지가 발생할 수 있는 몇 가지 이유는 다음과 같습니다.

1. 디스크가 꽉 찼을 수 있습니다.
1. 파일 시스템의 Inode가 부족 한 것 같습니다.
1. 파일을 숨기는 기존 디렉터리를 통해 데이터 디스크를 탑재할 수 있습니다.
1. 프로세스에서 열고 삭제 한 파일입니다.

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. 복구 VM을 만들고 액세스합니다.
1. 디스크의 공간을 확보합니다.
1. VM을 다시 빌드합니다.

> [!NOTE]
> 이 오류가 발생할 경우 게스트 OS가 작동하지 않습니다. 이 문제를 해결하려면 오프라인 모드에서 이 문제를 해결하세요.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md)의 1 ~ 3단계를 사용하여 복구 VM을 준비합니다.
1. SSH를 사용 하 여 복구 VM에 연결 합니다.

### <a name="free-up-space-on-the-disk"></a>디스크 공간 확보

이 문제를 해결하려면

- 아직 최대 크기인 1TB가 아닌 경우 디스크 크기를 1TB까지 조정합니다.
- 디스크 공간을 늘리십시오.

1. 디스크가 꽉 찼는지 확인합니다. 디스크 크기가 1tb 미만이 면 [Azure CLI를 사용 하 여](../linux/expand-disks.md)최대 1tb까지 확장 합니다.
1. 디스크가 이미 1tb 인 경우에는 디스크 공간을 확보 해야 합니다.
1. 크기 조정 및 정리가 완료 되 면 VM 다시 빌드를 진행 합니다.

### <a name="rebuild-the-vm"></a>VM 다시 빌드

[VM 복구 명령의 5단계](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)를 사용하여 VM을 다시 빌드합니다.
