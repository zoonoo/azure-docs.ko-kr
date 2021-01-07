---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f002fec531a0355e803a1545990fc0b13b535742
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582175"
---
장치 문제가 발생 하는 경우 시스템 로그에서 지원 패키지를 만들 수 있습니다. Microsoft 지원이 패키지를 사용 하 여 문제를 해결 합니다. 지원 패키지를 만들려면 다음 단계를 따르세요.

1. [장치의 PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.
2. 명령을 사용 `Get-HcsNodeSupportPackage` 하 여 지원 패키지를 만듭니다. Cmdlet을 사용 하는 방법은 다음과 같습니다.

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

    Cmdlet은 장치에서 로그를 수집 하 고 해당 로그를 지정 된 네트워크 또는 로컬 공유에 복사 합니다.

    사용 되는 매개 변수는 다음과 같습니다.

    - `-Path` -지원 패키지를 복사할 네트워크 또는 로컬 경로를 지정 합니다. (필수)
    - `-Credential` -보호 된 경로에 액세스 하기 위한 자격 증명을 지정 합니다.
    - `-Zip` -Zip 파일을 생성 하도록 지정 합니다.
    - `-Include` -지원 패키지에 포함할 구성 요소를 포함 하도록 지정 합니다. 지정 하지 않으면 `Default` 이 가정 됩니다.
    - `-IncludeArchived` -지원 패키지에 보관 된 로그를 포함 하도록 지정 합니다.
    - `-IncludePeriodicStats` -지원 패키지에 정기 상태 로그를 포함 하도록 지정 합니다.

    
