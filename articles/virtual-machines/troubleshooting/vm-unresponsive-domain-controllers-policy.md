---
title: 기본 도메인 컨트롤러 정책을 적용하는 동안 VM이 응답하지 않음
titlesuffix: Azure Virtual Machines
description: 이 문서에서는 기본 도메인 컨트롤러 정책이 Azure VM의 부팅을 방지 하는 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 53e1daca47a2917a19cbc30db5348e4fcc06b325
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90039157"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>기본 도메인 컨트롤러 정책을 적용하는 동안 VM이 응답하지 않음

이 문서에서는 기본 도메인 컨트롤러 정책에서 Azure VM (가상 컴퓨터)의 부팅을 방지 하는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptom"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용 하 여 VM의 스크린샷을 볼 때 메시지 **기본 도메인 컨트롤러 정책**으로 부팅 하는 동안 OS가 응답 하지 않는 것으로 표시 되는 것을 볼 수 있습니다.

  ![그림 1은 ' 기본 도메인 컨트롤러 정책 ' 메시지와 함께 중지 된 OS를 표시 합니다.](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>원인

이 문제는 기본 도메인 컨트롤러 정책에 대 한 최근 변경 내용으로 인해 발생할 수 있습니다. 그렇지 않으면 메모리 덤프 파일 분석을 수행 하 여 근본 원인을 확인 해야 합니다.

## <a name="solution"></a>솔루션

최근에 기본 도메인 컨트롤러 정책을 변경한 경우 해당 변경 내용을 취소 하 여 문제를 해결할 수 있습니다. 문제의 원인이 무엇 인지 확실 하지 않은 경우 메모리 덤프를 수집 하 고 지원 티켓을 제출 합니다.

### <a name="collect-the-memory-dump-file"></a>메모리 덤프 파일 수집

이 문제를 해결 하려면 먼저 크래시에 대 한 메모리 덤프 파일을 수집 하 고 메모리 덤프 파일을 사용 하 여 지원에 문의 해야 합니다. 덤프 파일을 수집 하려면 다음 단계를 수행 합니다.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>새 복구 VM에 OS 디스크를 연결 합니다.

1. [VM 복구 명령](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)의 1 ~ 3단계를 사용하여 복구 VM을 준비합니다.

1. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>덤프 파일을 찾고 지원 티켓을 제출 합니다.

1. 복구 VM에서 연결 된 OS 디스크의 Windows 폴더로 이동 합니다. 연결된 OS 디스크에 할당된 드라이브 문자가 `F`인 경우 `F:\Windows`로 이동해야 합니다.

1. Memory.dmp 파일을 찾은 다음 메모리 덤프 파일을 사용 하 여 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 합니다.

1. Memory.dmp 파일을 찾는 데 문제가 있는 경우 대신 [직렬 콘솔에서 비 마스크 인터럽트 (NMI) 호출](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) 을 사용 하는 것이 좋습니다. 이 가이드에 따라 [NMI 호출을 사용 하 여 크래시 덤프 파일을 생성](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump)합니다.
