---
title: Azure VM의 Windows 다시 부팅 루프 | Microsoft Docs
description: Windows 다시 부팅 루프 문제를 해결하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 032bc1b9c4b1b0e3bf8040ed52bf4db65ba7b6c7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085595"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Azure VM의 Windows 다시 부팅 루프
이 문서에서는 Microsoft Azure의 Windows VM(Virtual Machine)에서 발생할 수 있는 다시 부팅 루프에 대해 설명합니다.

## <a name="symptom"></a>증상

[부트 진단](./boot-diagnostics.md)을 사용하여 VM에 대한 스크린샷을 가져오면 가상 머신에서 부팅하고 있지만 부팅 프로세스가 중단되었다가 다시 시작된다는 것을 알 수 있습니다.

![시작 화면 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>원인

다시 부팅 루프가 발생하는 원인은 다음과 같습니다.

### <a name="cause-1"></a>원인 1

플래그가 '위험'으로 지정되고 시작할 수 없는 타사 서비스가 있습니다. 이로 인해 운영 체제가 다시 부팅됩니다.

### <a name="cause-2"></a>원인 2

운영 체제가 일부 변경되었습니다. 일반적으로 이러한 변경은 업데이트 설치, 애플리케이션 설치 또는 새 정책과 관련이 있습니다. 자세한 내용을 알아보려면 다음 로그를 확인해야 할 수 있습니다.

- 이벤트 로그
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>원인 3

파일 시스템이 손상되면 이 문제가 발생할 수 있습니다. 그러나 운영 체제가 손상되는 변경을 진단하고 식별하는 것이 어렵습니다.

## <a name="solution"></a>해결 방법

이 문제를 해결하려면 [OS 디스크를 백업](../windows/snapshot-copy-managed-disk.md)하고, [OS 디스크를 복구 VM에 연결](../windows/troubleshoot-recovery-disks-portal.md)한 다음, 이에 따라 솔루션 옵션을 따르거나 솔루션을 하나씩 시도합니다.

### <a name="solution-for-cause-1"></a>원인 1의 해결 방법

1. OS 디스크가 작업 VM에 연결되면 [디스크 관리] 콘솔에서 디스크의 플래그가 **온라인**으로 설정되어 있는지 확인하고, **\Windows** 폴더가 있는 파티션의 드라이브 문자를 적어 둡니다.

2. 디스크가 **오프라인**으로 설정되어 있으면 해당 디스크를 **온라인**으로 설정합니다.

3. 변경할 때 롤백해야 하는 경우 **\Windows\System32\config** 폴더의 복사본을 만듭니다.

4. 복구 VM에서 Windows 레지스트리 편집기(regedit)를 엽니다.

5. **HKEY_LOCAL_MACHINE** 키를 선택한 다음, 메뉴에서 **파일** > **하이브 로드**를 차례로 선택합니다.

6. **\Windows\System32\config** 폴더에서 SYSTEM 파일을 찾습니다.

7. **열기**를 선택하고, 이름에 대해 **BROKENSYSTEM**을 입력하고, **HKEY_LOCAL_MACHINE** 키를 펼치면 **BROKENSYSTEM**이라는 추가 키가 표시됩니다.

8. 컴퓨터가 부팅되는 ControlSet을 확인합니다. 다음 레지스트리 키에 키 번호가 표시됩니다.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. 다음 레지스트리 키를 통해 VM 에이전트 서비스의 중요도를 확인합니다.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. 레지스트리 키 값이 **2**로 설정되어 있지 않으면 다음 완화로 이동합니다.

11. 레지스트리 키 값이 **2**로 설정되어 있으면 값을 **2**에서 **1**로 변경합니다.

12. 다음 키 중 하나가 있고 값이 **2** 또는 **3**이면 해당 값을 **1**로 변경합니다.

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. **BROKENSYSTEM** 키를 선택한 다음, 메뉴에서 **파일** > **하이브 로드**를 차례로 선택합니다.

14. 문제 해결을 위한 VM에서 OS 디스크를 분리합니다.

15. 문제 해결을 위한 VM에서 디스크를 제거하고, Azure에서 해당 디스크를 해제할 때까지 약 2분 정도 기다립니다.

16. [OS 디스크에서 새 VM를 만듭니다](../windows/create-vm-specialized.md).

17. 문제가 해결되면 [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/)(WaAppAgent.exe)를 다시 설치해야 할 수도 있습니다.

### <a name="solution-for-cause-2"></a>원인 2의 해결 방법

VM을 마지막으로 알려진 성공한 구성으로 복원하고, [마지막으로 성공한 구성으로 Azure Windows VM을 시작하는 방법](https://support.microsoft.com/help/4016731/)의 단계를 따릅니다.

### <a name="solution-for-cause-3"></a>원인 3의 해결 방법

1. 디스크가 문제 해결을 위한 VM에 연결되면 [디스크 관리] 콘솔에서 디스크의 플래그가 **온라인**으로 지정되어 있는지 확인합니다.

2. 변경할 때 롤백해야 하는 경우 **\Windows\System32\config** 폴더의 복사본을 만듭니다.

3. **\Windows\System32\config\regback** 폴더의 파일을 복사하고, **\Windows\System32\config** 폴더의 파일을 바꿉니다.

4. 문제 해결을 위한 VM에서 디스크를 제거하고, Azure에서 해당 디스크를 해제할 때까지 약 2분 정도 기다립니다.

5. [OS 디스크에서 새 VM를 만듭니다](../windows/create-vm-specialized.md).

>[!NOTE]
>다음 절차는 마지막 리소스로만 사용해야 합니다. regback에서 복원하면 머신에 대한 액세스가 복원될 수 있지만, 하이브의 타임스탬프와 현재 날짜 사이에 손실된 레지스트리 데이터가 있으므로 OS가 안정적이라고 간주되지 않습니다. 새 VM을 빌드하고 데이터를 마이그레이션할 계획을 세워야 합니다.
