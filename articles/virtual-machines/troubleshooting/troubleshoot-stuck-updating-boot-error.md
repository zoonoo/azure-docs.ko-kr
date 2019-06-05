---
title: Azure VM 시작이 Windows 업데이트에서 중단되는 경우 | Microsoft Docs
description: Azure VM 시작이 Windows 업데이트에서 중단되는 경우 문제 해결 방법을 알아봅니다.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: cff1577eacd0af86d3ad1c99e1eb2164b64318c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443775"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Azure VM 시작이 Windows 업데이트에서 중단되는 경우

이 문서에서는 VM(Virtual Machine)이 시작 중에 Windows 업데이트 단계에서 중단되는 경우 문제를 해결하도록 도와 줍니다. 

> [!NOTE] 
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 리소스 관리자 배포 모델 사용에 대해 설명합니다. 클래식 배포 모델을 사용하는 대신 새 배포에 이 모델을 사용하는 것이 좋습니다.

## <a name="symptom"></a>증상

 Windows VM이 시작되지 않습니다. [부트 진단](../troubleshooting/boot-diagnostics.md) 창의 스크린샷을 확인하면 업데이트 프로세스에서 시작이 중단되는 것을 알 수 있습니다. 다음은 수신할 수 있는 메시지의 예입니다.

- Windows 설치 중 ##% PC를 끄지 마십시오. 이 과정은 PC가 여러 번 다시 시작하므로 어느 정도 시간이 걸립니다.
- 이 작업이 완료될 때까지 PC를 켜진 상태로 유지하십시오. 업데이트 설치 중 # / #... 
- 업데이트를 완료할 수 없습니다. 변경 실행 취소 중입니다. 컴퓨터를 끄지 마십시오.
- Windows 업데이트 구성 실패. 변경을 되돌리는 중. 컴퓨터를 끄지 마십시오.
- 업데이트 작업 적용 중 오류 < 오류 코드 > ##### / ##### (\Regist...)
- 업데이트 작업 적용 중 치명적 오류 < 오류 코드 > ##### / ##### ($$...)


## <a name="solution"></a>해결 방법

설치 또는 롤백하는 업데이트 수에 따라 업데이트 프로세스에 어느 정도 시간이 걸릴 수 있습니다. 이 상태에서 8 시간 동안 VM을 그대로 유지하십시오. 그 기간이 지난 후에도 VM이 여전히 이 상태에 있는 경우 Azure Portal에서 VM을 다시 시작하고 정상으로 시작할 수 있는지 확인하십시오. 이 단계도 소용이 없으면 다음 솔루션을 시도합니다.

### <a name="remove-the-update-that-causes-the-problem"></a>문제의 원인이 된 업데이트를 제거합니다.

1. 영향을 받는 VM의 OS 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](../windows/snapshot-copy-managed-disk.md)을 참조하세요. 
2. [복구 VM에 OS 디스크를 연결합니다](troubleshoot-recovery-disks-portal-windows.md).
3. OS 디스크가 복구 VM에 연결되면 **diskmgmt.msc**를 실행하여 디스크 관리를 열고 연결된 디스크가 **ONLINE** 상태인지 확인합니다. \Windows 폴더를 유지하는 연결된 OS 디스크에 할당된 드라이브 문자를 기록해 둡니다. 디스크 암호화된 경우 이 문서의 다음 단계를 진행하기 전에 디스크를 암호 해독합니다.

4. 관리자 권한 명령 프롬프트 인스턴스를 엽니다(관리자 권한으로 실행). 다음 명령을 실행하여 연결된 OS 디스크에 있는 업데이트 패키지의 목록을 가져옵니다.

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    예를 들어 연결된 OS 디스크가 드라이브 F인 경우 다음 명령을 실행합니다.

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. C:\temp\Patch_level.txt 파일을 연 다음, 아래쪽에서 위쪽으로 읽습니다. **설치 보류 중** 또는 **제거 보류 중** 상태에 있는 업데이트를 찾습니다.  다음은 업데이트 상태의 샘플입니다.

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. 문제의 원인이 된 업데이트를 제거합니다.
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    예제: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > 패키지의 크기에 따라 DISM 도구가 제거를 처리하려면 어느 정도 시간이 걸립니다. 일반적으로 이 프로세스는 16 분 이내에 완료됩니다.

7. [OS 디스크를 분리하고 VM을 다시 만듭니다](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). 그런 다음, 문제가 해결되었는지 확인합니다.
