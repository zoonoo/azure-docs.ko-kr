---
title: 로컬 세션 관리자를 기다리는 동안 VM이 응답 하지 않습니다.
description: 이 문서에서는 Azure VM을 부팅 하는 동안 로컬 세션 관리자가 처리를 마칠 때까지 대기 하는 게스트 OS의 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: f2f0177b5fe8bb97773d297319f6c9196d8178d2
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536237"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>로컬 세션 관리자를 기다리는 동안 VM이 응답 하지 않습니다.

이 문서에서는 Azure VM (가상 머신)을 부팅 하는 동안 로컬 세션 관리자가 처리를 마칠 때까지 대기 하는 게스트 OS의 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptoms"></a>증상

[부팅 진단을](./boot-diagnostics.md) 사용 하 여 VM의 스크린샷을 볼 때 스크린 샷에서는 " *로컬 세션 관리자를 기다리는* 중입니다." 라는 메시지가 표시 되는 것을 볼 수 있습니다.

![스크린샷은 Windows Server 2012 r 2의 "로컬 세션 관리자를 기다리는 중" 메시지와 함께 중단 된 게스트 OS를 보여 줍니다.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>원인

가상 컴퓨터에서 로컬 세션 관리자를 대기 하는 데 여러 가지 이유가 있을 수 있습니다. 로컬 세션 관리자의 대기 시간이 지속 되는 경우 분석을 위해 메모리 덤프를 수집 해야 합니다.

## <a name="solution"></a>해결 방법

경우에 따라 프로세스가 완료 되는 데 충분 한 시간 동안 대기 하면 문제가 해결 됩니다. VM이 응답 하지 않으며 대기 화면에서 한 시간 이상 지속 되는 경우 메모리 덤프를 수집 하 고 Microsoft 지원에 문의 해야 합니다.

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
> [Azure 가상 머신 부팅 오류 문제 해결](boot-error-troubleshoot.md)