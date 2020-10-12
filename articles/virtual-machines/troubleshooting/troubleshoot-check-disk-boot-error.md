---
title: Azure VM을 부팅할 때 파일 시스템 확인 중 | Microsoft Docs
description: 부팅 시 VM이 파일 시스템 확인 중을 표시하는 문제를 해결하는 방법 알아보기 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: f80fbd803cbe4ae5c4ac381c8cdb2f72d0ede316
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86132933"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Azure VM을 부팅할 때 Windows에서 "파일 시스템 확인 중"을 표시합니다.

이 문서에서는 Microsoft Azure에서 Windows VM(가상 머신)을 부팅할 때 발생할 수 있는 "파일 시스템 확인 중" 오류에 대해 설명합니다.


## <a name="symptom"></a>증상 

Windows VM이 시작되지 않습니다. [부트 진단](boot-diagnostics.md)에서 부팅 스크린샷을 확인하면 다음 메시지 중 하나를 나타내면서 디스크 검사 프로세스(chkdsk.exe)가 실행 중인 것을 알 수 있습니다.

- 드라이브 (C:) 검사 및 복구 중
- C:에서 파일 시스템 확인 중

## <a name="cause"></a>원인

파일 시스템에 NTFS 오류가 있으면 Windows는 다음에 다시 시작할 때 디스크 일관성을 검사하고 복구합니다. 일반적으로 이 문제는 VM이 예기치 않게 다시 시작되거나 VM 종료 프로세스가 갑자기 중단된 경우에 발생합니다.

## <a name="solution"></a>솔루션 

Windows는 디스크 검사 프로세스가 완료된 후 정상적으로 부팅됩니다. VM의 디스크 검사 프로세스가 중단되면 오프라인에서 VM에 대해 디스크 검사를 실행합니다.
1. 영향을 받는 VM의 OS 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](../windows/snapshot-copy-managed-disk.md)을 참조하세요.
2. [OS 디스크를 복구 VM에 연결](troubleshoot-recovery-disks-portal-windows.md)합니다.  
3. 복구 VM에서 연결된 OS 디스크에 대해 디스크 검사를 실행합니다. 다음 샘플에서는 연결된 OS 디스크의 드라이브 문자가 E:입니다. 

    ```console
    chkdsk E: /f
    ```

4. 디스크 검사가 완료되면 복구 VM에서 디스크를 분리했다가 영향을 받는 VM에 OS 디스크로서 다시 연결합니다. 자세한 내용은 [OS 디스크를 복구 VM에 연결함으로써 Windows VM 문제 해결](troubleshoot-recovery-disks-portal-windows.md)을 참조하세요.
