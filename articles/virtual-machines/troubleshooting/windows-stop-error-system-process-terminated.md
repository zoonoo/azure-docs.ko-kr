---
title: Windows 중지 오류 상태 시스템 프로세스가 종료 되었습니다.
description: 이 문서에서는 운영 체제에서 중지 오류 0xC000021A 발생 하는 문제를 해결 하는 단계를 제공 합니다 .이 문제는 Azure 가상 컴퓨터의 부팅을 방지 합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: b13b61aff819271ed1722572f251f9a6d14b17ab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977000"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Windows 중지 오류-0xC000021A 상태 시스템 프로세스가 종료 되었습니다.

이 문서에서는 OS (운영 체제)에서 중지 오류 0xC000021A 발생 하는 문제를 해결 하는 단계를 제공 합니다 .이 문제는 Azure VM (가상 머신)이 부팅 되지 않도록 합니다.

## <a name="symptom"></a>증상

[부팅 진단을](./boot-diagnostics.md) 사용 하 여 VM의 스크린샷을 볼 때 부팅 하는 동안 OS에 오류가 발생 하는 메시지가 스크린샷에 표시 되 고 다음과 같은 메시지가 표시 됩니다.

**PC에서 문제가 발생 하 여 컴퓨터를 다시 시작 해야 합니다. 일부 오류 정보를 수집 하는 중 이므로 다시 시작할 수 있습니다. (# #% 완료) 자세히 알아보려면 나중에이 오류에 대해 온라인으로 검색할 수 있습니다. 0xC000021a**.

  ![그림 1에는 "PC에서 문제가 발생 하 여 다시 시작 해야 하는 메시지와 함께 #0xC000021A 오류 코드가 표시 됩니다. 일부 오류 정보를 수집 하 고 다시 시작할 수 있습니다. "](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>원인

오류 0xC000021A는 **STATUS_SYSTEM_PROCESS_TERMINATED**을 의미 합니다.

이 오류는 WinLogon (winlogon.exe) 또는 클라이언트 서버 Run-Time 하위 시스템 (csrss.exe)과 같은 중요 한 프로세스가 실패할 때 발생 합니다. 커널이 이러한 서비스 중 하나라도 중지 된 것을 감지 하면 **STOP 0xC000021A** 오류가 발생 합니다. 이 오류는 다음과 같은 여러 원인이 있을 수 있습니다.

- 일치 하지 않는 시스템 파일을 설치 했습니다.
- 서비스 팩 또는 KB 업데이트 설치에 실패 했습니다.
- 하드 디스크를 복원 하는 데 사용 되는 백업 프로그램에서 사용 중일 수 있는 파일을 제대로 복원 하지 못했습니다.
- 호환 되지 않는 타사 프로그램이 설치 되었습니다.

## <a name="solution"></a>솔루션

### <a name="collect-the-memory-dump-file"></a>메모리 덤프 파일 수집

이 문제를 해결 하려면 크래시 덤프를 분석 해야 합니다. 크래시에 대 한 메모리 덤프 파일을 수집 하 고 지원 서비스에 문의 하십시오. 덤프 파일을 수집 하려면 다음 단계를 수행 합니다.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>새 복구 VM에 OS 디스크를 연결 합니다.

1.  [VM 복구 명령](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)의 1 ~ 3단계를 사용하여 복구 VM을 준비합니다.
2.  **원격 데스크톱 연결**를 사용 하 여 복구 VM에 연결 합니다.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>덤프 파일을 찾고 지원 티켓을 제출 합니다.

1.  복구 VM에서 연결 된 OS 디스크의 windows 폴더로 이동 합니다. 연결 된 OS 디스크에 할당 된 드라이버 문자가 F 인 경우 F:\Windows.로 이동 합니다.
2.  Memory.dmp 파일을 찾은 다음 메모리 덤프 파일을 사용 하 여 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 합니다.
3.  Memory.dmp 파일을 찾는 데 문제가 있는 경우 대신 [직렬 콘솔에서 비 마스크 인터럽트 (NMI) 호출](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 을 사용 하는 것이 좋습니다. 가이드에 따라 [여기에서 NMI 호출을 사용하여 크래시 덤프 파일을 생성](/windows/client-management/generate-kernel-or-complete-crash-dump)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 추가 문제 해결 정보는 [일반적인 부팅 오류 문제 해결](./boot-error-troubleshoot.md) 또는 [OS 디스크를 복구 VM에 연결 하 여 Windows VM 문제를 해결 하는 방법](./troubleshoot-recovery-disks-windows.md)을 참조 하세요. 또한 [부팅 진단을 사용 하 여 가상 컴퓨터의 문제를 해결 하는 방법을](./boot-diagnostics.md)숙지 해야 합니다.
- Resource Manager를 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요.
- VM에 연결할 수 없는 경우 [AZURE vm에 대 한 RDP 연결 문제 해결](./troubleshoot-rdp-connection.md)을 참조 하세요.