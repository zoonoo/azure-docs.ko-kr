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
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424229"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows 중지 오류-0x0000007E 시스템 스레드 예외가 처리 되지 않음

이 문서에서는 게스트 OS에 문제가 발생 하 여 Azure VM을 다시 시작 하려는 문제를 해결 하는 단계를 제공 합니다. 메시지에 "시스템 스레드 예외가 처리 되지 않았습니다." 라는 메시지가 나타납니다.

## <a name="symptoms"></a>증상

[부팅 진단을](./boot-diagnostics.md) 사용 하 여 VM의 스크린샷을 볼 때 중지 코드 **시스템 스레드 예외 처리 안 함** 또는 오류 코드 **0x0000007e** 을 사용 하 여 다시 시작 해야 하는 창이 표시 됩니다.

![스크린샷에는 "PC가 문제가 발생 하 여 다시 시작 해야 합니다." 라는 메시지와 함께 부팅 하는 동안 발생 한 Windows가 표시 됩니다. 다시 시작 합니다. " 중지 코드: "시스템 스레드 예외가 처리 되지 않음"](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>원인

메모리 덤프 파일이 분석 될 때까지 원인을 확인할 수 없습니다. 메모리 덤프 파일을 계속 수집 합니다.

## <a name="solution"></a>해결 방법

### <a name="collect-the-memory-dump-file"></a>메모리 덤프 파일 수집

이 문제를 해결 하려면 먼저 작동 중단에 대 한 메모리 덤프 파일을 수집한 다음 메모리 덤프 파일을 사용 하 여 지원에 문의 해야 합니다. 덤프 파일을 수집 하려면 다음 단계를 수행 합니다.

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>새 복구 VM에 OS 디스크를 연결 합니다.

1. [Vm 복구 명령의 1-3 단계](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 를 수행 하 여 복구 vm을 준비 합니다.
2. 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>덤프 파일을 찾고 지원 티켓을 제출 합니다.

1. 복구 VM에서 연결 된 OS 디스크의 Windows 폴더로 이동 합니다. 연결 된 OS 디스크에 할당 된 드라이버 문자에 *F* 레이블이 지정 된 경우으로 이동 해야 `F:\Windows` 합니다.
2. **Memory.dmp** 파일을 찾은 다음 메모리 덤프 파일을 사용 하 여 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 합니다.
3. **Memory.dmp** 파일을 찾는 데 문제가 있는 경우에는이 가이드의 지침에 따라 [NMI를 사용 하지 않는 인터럽트 (NMI) 호출을 사용 하 여 크래시 덤프 파일을 생성](/windows/client-management/generate-kernel-or-complete-crash-dump)합니다.

NMI 호출에 대 한 자세한 내용은 [직렬 콘솔 사용자 가이드의 마스크 불가능 인터럽트 (NMI) 호출](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 가상 머신 부팅 오류 문제 해결](./boot-error-troubleshoot.md)