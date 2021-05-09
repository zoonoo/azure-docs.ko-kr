---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67182662"
---
디바이스 문제가 발생하는 경우 시스템 로그에서 지원 패키지를 만들 수 있습니다. Microsoft 지원에서는 이 패키지를 사용하여 문제를 해결합니다. 다음 단계에 따라 지원 패키지를 만듭니다.

1. [디바이스의 PowerShell 인터페이스에 연결합니다](#connect-to-the-powershell-interface).
2. `Get-HcsNodeSupportPackage` 명령을 사용하여 지원 패키지를 만듭니다. Cmdlet의 사용법은 다음과 같습니다.

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Cmdlet은 디바이스에서 로그를 수집하고 해당 로그를 지정된 네트워크 또는 로컬 공유에 복사합니다.

    사용되는 매개 변수는 다음과 같습니다.

    - `-Path` - 지원 패키지를 복사할 네트워크 또는 로컬 경로를 지정합니다. (필수)
    - `-Credential` - 보호된 경로에 액세스하기 위한 자격 증명을 지정합니다.
    - `-Zip` - zip 파일을 생성하도록 지정합니다.
    - `-Include` - 지원 패키지에 포함할 구성 요소를 포함하도록 지정합니다. 지정되지 않으면 `Default`가 가정됩니다.
    - `-IncludeArchived` - 지원 패키지에 보관된 로그를 포함하도록 지정합니다.
    - `-IncludePeriodicStats` - 지원 패키지에 정기 상태 로그를 포함하도록 지정합니다.

    
