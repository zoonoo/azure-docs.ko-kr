---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182662"
---
모든 장치 문제가 있는 경우에 시스템 로그에서 지원 패키지를 만들 수 있습니다. Microsoft 지원에서 문제를 해결 하려면이 패키지를 사용 합니다. 지원 패키지를 만들려면 다음이 단계를 수행 합니다.

1. [장치의 PowerShell 인터페이스를 연결할](#connect-to-the-powershell-interface)합니다.
2. 사용 된 `Get-HcsNodeSupportPackage` 지원 패키지 만들기 명령을 합니다. Cmdlet의 사용법은 다음과 같습니다.

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

    Cmdlet은 장치에서 로그를 수집 하 고 지정 된 네트워크 또는 로컬 공유에 이러한 로그를 복사 합니다.

    사용 된 매개 변수는 다음과 같습니다.

    - `-Path` -네트워크 또는 지원 패키지를 복사 하려면 로컬 경로 지정 합니다. (필수)
    - `-Credential` -보호 된 경로 액세스할 자격 증명을 지정 합니다.
    - `-Zip` -Zip 파일을 생성 하도록 지정 합니다.
    - `-Include` -지원 패키지에 포함 될 구성 요소를 포함 하도록 지정 합니다. 지정 하지 않으면 `Default` 것으로 간주 됩니다.
    - `-IncludeArchived` -지원 패키지에 보관 된 로그를 포함 하도록 지정 합니다.
    - `-IncludePeriodicStats` -Stat 정기적인 로그 지원 패키지에 포함 하도록 지정 합니다.

    
