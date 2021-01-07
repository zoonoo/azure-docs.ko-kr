---
title: Windows 중지 오류-0x0000007E 시스템 스레드 예외가 처리 되지 않음
description: 이 문서에서는 게스트 OS에 문제가 발생 하 여 Azure VM을 다시 시작 하려는 문제를 해결 하는 단계를 제공 합니다. 메시지에 "시스템 스레드 예외가 처리 되지 않았습니다." 라는 메시지가 나타납니다.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: 1ce594d9e3ffddf781c61717ae4534f0c7bd40f8
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681893"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows 중지 오류-0x0000007E 시스템 스레드 예외가 처리 되지 않음

이 문서에서는 게스트 운영 체제 (게스트 OS)에서 문제가 발생 하 여 Azure VM (가상 컴퓨터)을 다시 시작 하려고 하는 문제를 해결 하는 단계를 제공 합니다. "시스템 스레드 예외가 처리 되지 않았습니다." 라는 오류 메시지가 표시 됩니다.

## <a name="symptoms"></a>증상

[부팅 진단을](./boot-diagnostics.md) 사용 하 여 VM 출력의 스크린샷을 볼 때 "시스템 스레드 예외가 처리 되지 않음" 중지 코드 또는 "0x0000007e" 오류 코드를 사용 하 여 Windows를 다시 시작 해야 함을 알 수 있습니다.

!["PC를 부팅 하는 동안 문제가 발생 하 여 다시 시작 해야 하는 Windows의 중단을 보여 주는 스크린샷 다시 시작 합니다. " 오류 메시지와 "시스템 스레드 예외가 처리 되지 않음" 중지 코드](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>원인

메모리 덤프 파일이 분석 될 때까지 원인을 확인할 수 없습니다. 메모리 덤프 파일을 계속 수집 합니다.

## <a name="solution"></a>솔루션

이 문제를 해결 하려면 먼저 작동 중단에 대 한 메모리 덤프 파일을 수집 하 고 파일을 Microsoft 지원으로 전송 해야 합니다. 덤프 파일을 수집 하려면 다음 두 섹션의 지침을 따르세요.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>새 복구 VM에 OS 디스크를 연결 합니다.

1. 복구 VM을 준비 하려면 [vm 복구 명령의](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)1-3 단계를 수행 합니다.
1. 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>덤프 파일을 찾고 지원 티켓을 제출 합니다.

1. 복구 VM에서 연결 된 OS 디스크의 Windows 폴더로 이동 합니다. 예를 들어 연결 된 OS 디스크에 할당 된 드라이브 문자에 *F* 라는 레이블이 지정 된 경우로 이동 `F:\Windows` 합니다.
1. *Memory.dmp* 파일을 찾은 다음 연결 된 메모리 덤프 파일을 사용 하 여 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 합니다.
1. *Memory.dmp* 파일을 찾는 데 문제가 있는 경우이 가이드의 지침에 따라 [NMI (비 마스크 인터럽트) 호출을 사용 하 여 크래시 덤프 파일을 생성](/windows/client-management/generate-kernel-or-complete-crash-dump)합니다.

NMI 호출에 대 한 자세한 내용은 [Azure 직렬 콘솔에서 nmi 호출](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 사용자 가이드를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 가상 머신 부팅 오류 문제 해결](./boot-error-troubleshoot.md)
