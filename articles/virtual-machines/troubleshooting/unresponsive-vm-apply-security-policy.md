---
title: 시스템에 보안 정책을 적용 하는 동안 Azure VM이 응답 하지 않습니다.
description: 이 문서에서는 Azure VM의 시스템에 보안 정책을 적용 하는 동안 VM이 응답 하지 않을 때 로드 화면이 중단 되는 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 6b50bffd1a44c0cf53f15650f5ff4d938f45df4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84908064"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>시스템에 보안 정책을 적용 하는 동안 Azure VM이 응답 하지 않습니다.

이 문서에서는 OS가 중단 되 고 Azure VM에 보안 정책을 적용 하는 동안 응답 하지 않는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptoms"></a>증상

[부팅 진단을](boot-diagnostics.md) 사용 하 여 VM의 스크린샷을 볼 때 메시지를 사용 하 여 부팅 하는 동안 OS가 중지 된 것이 표시 됩니다.

> ' 시스템에 보안 정책을 적용 하는 중 '.

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Windows Server 2012 R2 시작 화면의 스크린샷이 중지 되었습니다.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="Windows Server 2012 R2 시작 화면의 스크린샷이 중지 되었습니다.":::

## <a name="cause"></a>원인

이 문제의 가능한 원인은 다양 한 있습니다. 메모리 덤프 분석이 수행 될 때까지 소스를 알 수 없습니다.

## <a name="resolution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. [복구 VM 만들기 및 액세스](#create-and-access-a-repair-vm)
2. [직렬 콘솔 및 메모리 덤프 수집 사용](#enable-serial-console-and-memory-dump-collection)
3. [VM 다시 빌드](#rebuild-the-vm)
4. [메모리 덤프 파일 수집](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령의 1~3단계](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)를 사용하여 복구 VM을 준비합니다.
2. 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

### <a name="enable-serial-console-and-memory-dump-collection"></a>직렬 콘솔 및 메모리 덤프 수집 사용

메모리 덤프 수집 및 직렬 콘솔을 사용하려면 다음 스크립트를 실행합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다(관리자 권한으로 실행).
2. BCD 저장소 데이터를 나열 하 고 다음 단계에서 사용할 부팅 로더 식별자를 확인 합니다.

     1. 1 세대 VM의 경우 다음 명령을 입력 하 고 나열 된 식별자를 확인 합니다.

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        명령에서를 \<BOOT PARTITON> 부팅 폴더를 포함 하는 연결 된 디스크의 파티션 문자로 바꿉니다.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="Windows Server 2012 R2 시작 화면의 스크린샷이 중지 되었습니다." /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        손상된 OS 디스크를 언로드합니다.

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>VM 다시 빌드

[VM 복구 명령의 5단계](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)를 사용하여 VM을 다시 조합합니다.

### <a name="collect-the-memory-dump-file"></a>메모리 덤프 파일 수집

이 문제를 해결 하려면 먼저 크래시에 대 한 메모리 덤프 파일을 수집 하 고 메모리 덤프 파일을 사용 하 여 지원에 문의 해야 합니다. 덤프 파일을 수집 하려면 다음 단계를 수행 합니다.

1. 새 복구 VM에 OS 디스크를 연결 합니다.

    - [Vm 복구 명령의 1-3 단계](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 를 사용 하 여 새 복구 vm을 준비 합니다.
    - 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

2. 덤프 파일을 찾아서 지원 티켓을 제출합니다.

    - 복구 VM에서 연결 된 OS 디스크의 windows 폴더로 이동 합니다. 연결된 OS 디스크에 할당된 드라이브 문자가 `F`인 경우 `F:\Windows`로 이동해야 합니다.
    - Memory.dmp 파일을 찾은 다음 메모리 덤프 파일을 사용 하 여 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 합니다.
    - Memory.dmp 파일을 찾는 데 문제가 있는 경우 대신 [직렬 콘솔에서 비 마스크 인터럽트 (NMI) 호출](serial-console-windows.md#use-the-serial-console-for-nmi-calls) 을 사용 하는 것이 좋습니다. 이 가이드에 따라 [NMI 호출을 사용 하 여 크래시 덤프 파일을 생성할](/windows/client-management/generate-kernel-or-complete-crash-dump)수 있습니다.

## <a name="next-steps"></a>다음 단계

로컬 사용자 및 그룹 정책을 적용 하는 동안 문제가 발생 하는 경우 [그룹 정책 로컬 사용자 및 그룹 정책을 적용할 때 VM이 응답](unresponsive-vm-apply-group-policy.md) 하지 않음을 참조 하세요.