---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123195"
---
Azure 파일 동기화 에이전트는 새 기능을 추가하고 문제를 해결하기 위해 주기적으로 업데이트됩니다. 사용할 수 있을 때 Microsoft 업데이트에서 Azure 파일 동기화 에이전트에 대한 업데이트를 가져오도록 구성하는 것이 좋습니다.

#### <a name="major-vs-minor-agent-versions"></a>주 및 부 에이전트 버전 비교
* 주 에이전트 버전에는 새로운 기능이 포함되는 경우가 많으며, 버전 번호의 첫 번째 부분에 증가하는 숫자가 있습니다. 예: \*2.\*.\*\*
* 부 에이전트 버전은 "패치"라고도 하며, 주 버전보다 더 자주 릴리스됩니다. 버그가 수정되고 기능이 향상되는 경우가 많지만, 새로운 기능은 없습니다. 예: \*\*.3.\*\*

#### <a name="upgrade-paths"></a>업그레이드 경로
Azure 파일 동기화 에이전트 업데이트를 설치하는 데 승인된 네 가지 테스트 방법이 있습니다. 
1. **(기본 설정) 에이전트 업데이트를 자동으로 다운로드하여 설치하도록 Microsoft 업데이트를 구성합니다.**  
    항상 모든 Azure 파일 동기화 업데이트를 수행하여 서버 에이전트에 대한 최신 수정 프로그램에 액세스할 수 있도록 하는 것이 좋습니다. Microsoft 업데이트는 업데이트를 자동으로 다운로드하고 설치하여 이 프로세스를 원활하게 합니다.
2. **AfsUpdater.exe를 사용하여 에이전트 업데이트를 다운로드하고 설치합니다.**  
    AfsUpdater.exe는 에이전트 설치 디렉터리에 있습니다. 실행 파일을 두 번 클릭하여 에이전트 업데이트를 다운로드하고 설치합니다. 
3. **Microsoft 업데이트 패치 파일 또는 .msp 실행 파일을 사용하여 기존 Azure 파일 동기화 에이전트를 패치합니다. 최신 Azure 파일 동기화 업데이트 패키지는 [Microsoft 업데이트 카탈로그에서](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)다운로드할 수 있습니다.**  
    .msp 실행 파일을 실행하면 Microsoft 업데이트에서 자동으로 사용하는 방식과 동일하게 Azure 파일 동기화 설치가 이전 업그레이드 경로에서 업그레이드됩니다. Microsoft 업데이트 패치를 적용하면 Azure 파일 동기화 설치의 현재 위치 업그레이드가 수행됩니다.
4. **[Microsoft 다운로드 센터에서](https://go.microsoft.com/fwlink/?linkid=858257)최신 Azure 파일 동기화 에이전트 설치 관리자를 다운로드합니다.**  
    기존 Azure 파일 동기화 에이전트 설치를 업그레이드하려면 이전 버전을 제거한 다음, 다운로드한 설치 관리자에서 최신 버전을 설치합니다. Azure 파일 동기화 설치 관리자는 서버 등록, 동기화 그룹 및 다른 모든 설정을 관리합니다.

#### <a name="automatic-agent-lifecycle-management"></a>자동 에이전트 수명 주기 관리
에이전트 버전 6을 사용하면 파일 동기화 팀에서 에이전트 자동 업그레이드 기능을 도입했습니다. 두 가지 모드 중 하나를 선택하고 서버에서 업그레이드를 시도할 유지 관리 기간을 지정할 수 있습니다. 이 기능은 에이전트가 만료되는 것을 방지하는 가드레일을 제공하거나 번거로움 없이 최신 설정을 유지하도록 하여 에이전트 수명 주기 관리를 지원하도록 설계되었습니다.
1. **기본 설정은** 에이전트가 만료되지 않도록 시도합니다. 에이전트가 게시된 만료날짜로부터 21일 이내에 에이전트는 자체 업그레이드를 시도합니다. 만료 21일 전과 선택한 유지 관리 기간 내에 일주일에 한 번 업그레이드를 시작합니다. **이 옵션은 정기적인 Microsoft 업데이트 패치를 사용할 필요가 없습니다.**
1. 선택적으로 새 에이전트 버전을 사용할 수 있게 되는 즉시 에이전트가 자동으로 업그레이드되도록 선택할 수 있습니다(현재 클러스터된 서버에는 적용되지 않음). 이 업데이트는 선택한 유지 관리 기간 동안 발생하며 서버가 일반적으로 사용할 수 있게 되는 즉시 새로운 기능과 개선 사항의 이점을 활용할 수 있습니다. 이 설정은 서버에 주요 에이전트 버전과 정기적인 업데이트 패치를 제공하는 권장되는 걱정 없는 설정입니다. 출시된 모든 에이전트는 GA 품질입니다. 이 옵션을 선택하면 Microsoft에서 최신 에이전트 버전을 플라이트합니다. 클러스터된 서버는 제외됩니다. 비행이 완료되면 에이전트는 aka.ms/AFS/agent [Microsoft 다운로드 센터에서도](https://go.microsoft.com/fwlink/?linkid=858257) 사용할 수 있습니다.

 ##### <a name="changing-the-auto-upgrade-setting"></a>자동 업그레이드 설정 변경

다음 지침은 설치 프로그램을 완료한 후 변경해야 하는 경우 설정을 변경하는 방법을 설명합니다.

PowerShell 콘솔을 열고 동기화 에이전트를 설치한 디렉터리로 이동한 다음 서버 cmdlet을 가져옵니다. 기본적으로 다음과 같이 보입니다.
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

실행하여 `Get-StorageSyncAgentAutoUpdatePolicy` 현재 정책 설정을 확인하고 변경할지 여부를 결정할 수 있습니다.

현재 정책 설정을 지연된 업데이트 트랙으로 변경하려면 다음을 사용할 수 있습니다.
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

현재 정책 설정을 즉시 업데이트 트랙으로 변경하려면 다음을 사용할 수 있습니다.
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>에이전트 수명 주기 및 변경 관리 보장
Azure File Sync는 새로운 기능과 향상된 기능을 지속적으로 도입하는 클라우드 서비스입니다. 즉, 특정 Azure 파일 동기화 에이전트 버전은 제한된 시간 동안만 지원될 수 있습니다. 배포를 용이하게 하기 위해 다음 규칙은 변경 관리 프로세스에서 에이전트 업데이트/업그레이드를 수용할 수 있는 충분한 시간과 알림을 보장합니다.

- 주 에이전트 버전은 최초 릴리스 날짜로부터 최소 6개월 동안 지원됩니다.
- 주 에이전트 버전의 지원 사이에는 3개월 이상 겹치도록 보장합니다. 
- 만료되기 최소 3개월 전에 만료될 에이전트를 사용하여 등록된 서버에 경고가 발급됩니다. 등록된 서버가 Storage Sync Service의 등록된 서버 섹션 아래에서 이전 버전의 에이전트를 사용하고 있는지 확인할 수 있습니다.
- 부 에이전트 버전의 수명은 연결된 주 버전에 따라 결정됩니다. 예를 들어 에이전트 버전 3.0이 릴리스되면 에이전트 버전 2.\*와 함께 모두 만료되도록 설정됩니다.

> [!Note]
> 만료 경고가 있는 에이전트 버전을 설치하면 경고가 표시되지만 성공합니다. 만료된 에이전트 버전을 설치하거나 연결하려고 하면 지원되지 않고 차단됩니다.
