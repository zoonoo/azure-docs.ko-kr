---
title: Azure Files에서 DFS-N 사용 방법
description: Azure Files와 일반적인 DFS-N 사용 사례
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0676639523a0b1ebd23ff0e5082e6cccbd641f4a
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117292"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>Azure Files DFS 네임스페이스를 사용하는 방법
일반적으로 DFS 네임스페이스 또는 DFS-N이라고 하는 [분산 파일 시스템 네임스페이스](/windows-server/storage/dfs-namespaces/dfs-overview)는 프로덕션 환경에서 SMB 파일 공유의 배포 및 유지 관리를 간소화하는 데 널리 사용되는 Windows Server 서버 역할입니다. DFS 네임 스페이스는 스토리지 네임스페이스 가상화 기술입니다. 즉, 파일 공유의 UNC 경로와 실제 파일 공유 간의 간접 참조 계층을 제공할 수 있음을 의미합니다. DFS 네임 스페이스는 SMB 파일 공유를 사용하여 작동합니다. 이러한 파일 공유와 관계없이 호스트됩니다. 또한 온-프레미스 Windows 파일 서버에서 호스트되는 SMB 공유와 함께 사용할 수 있습니다. 이 파일 공유는 Azure 파일 동기화, Azure 파일 직접 공유, Azure NetApp Files 또는 기타 타사 제품에서 호스트되는 SMB 파일 공유 및 다른 클라우드에서 호스트되는 파일 공유의 경우에도 마찬가지입니다. 

기본적으로 DFS 네임 스페이스는 사용자에게 친숙한 UNC 경로(예: `\\contoso\shares\ProjectX`과 같은 및 `\\Server01-Prod\ProjectX` 또는 `\\storageaccount.file.core.windows.net\projectx`과 같은 SMB 공유의 기본 UNC 경로)와 매핑을 제공합니다. 최종 사용자가 파일 공유로 이동하려는 경우 사용자에게 친숙한 UNC 경로를 입력하지만, 해당 SMB 클라이언트는 매핑의 기본 SMB 경로에 액세스합니다. 이 기본 개념을 확장하여 `\\MyServer\ProjectX`과 같은 기존 파일 서버 이름을 사용할 수도 있습니다. 이 기능을 사용하여 다음과 같은 시나리오를 달성할 수 있습니다.

- 논리적 데이터 집합에 대한 마이그레이션 증명 이름을 제공합니다. 이 예제에서는 `\\OldServer\Engineering`에 매핑되는 `\\contoso\shares\Engineering` 같은 매핑이 있습니다. Azure Files로의 마이그레이션을 완료하는 경우 사용자에게 친숙한 UNC 경로가 `\\storageaccount.file.core.windows.net\engineering`을 가리키도록 매핑을 변경할 수 있습니다. 최종 사용자가 사용자에게 친숙한 UNC 경로에 액세스하면 Azure 파일 공유 경로로 원활하게 리디렉션됩니다.

- Azure 파일 동기화 등을 통해 서로 다른 물리적 사이트의 여러 서버에 배포되는 논리적 데이터 집합에 대한 일반 이름을 설정합니다. 이 예에서는 `\\contoso\shares\FileSyncExample`과 같은 이름이 `\\FileSyncServer1\ExampleShare`, `\\FileSyncServer2\DifferentShareName`, `\\FileSyncServer3\ExampleShare`와 같은 여러 UNC 경로에 매핑됩니다. 사용자에게 친숙한 UNC에 액세스하면 가능한 UNC 경로 목록이 제공되며, Windows Server Active Directory(AD) 사이트 정의에 따라 가장 가까이 있는 경로를 선택합니다.

- 크기, IO 또는 기타 비율 크기 조정 임계값에 걸쳐 데이터의 논리적 집합을 확장합니다. 이는 사용자 디렉터리를 처리하는 경우 일반적입니다. 여기서 모든 사용자는 공유에 고유한 폴더를 가져오거나 스크래치 공유를 사용하여 사용자가 임시 데이터 요구 사항을 처리할 수 있는 임의 공간을 확보합니다. DFS 네임스페이스를 사용하여 여러 폴더를 결합된 네임스페이스에 연결합니다. 예를 들어,`\\contoso\shares\UserShares\user1`은 `\\storageaccount.file.core.windows.net\user1`에 매핑되고, `\\contoso\shares\UserShares\user2`은 `\\storageaccount.file.core.windows.net\user2`에 매핑합니다.  

다음 비디오 개요에서 Azure Files 배포를 사용하여 DFS 네임스페이스를 사용하는 방법의 예제를 볼 수 있습니다.

[![Azure Files를 사용하여 DFS-N을 설정하는 방법에 대한 데모-클릭하여 재생합니다!](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> DFS 네임스페이스를 설정하는 방법을 보려면 비디오에서 10:10으로 건너뛰십시오.

DFS 네임 스페이스가 이미 있는 경우 Azure Files 및 파일 동기화와 함께 사용하는 데 특별한 단계가 필요하지 않습니다. 온-프레미스에서 Azure 파일 공유에 액세스하는 경우 일반적인 네트워킹 고려 사항이 적용됩니다. 자세한 정보는 [Azure Files 네트워킹 고려 사항](./storage-files-networking-overview.md)을 참조하세요.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="namespace-types"></a>네임스페이스 형식
DFS 네임스페이스는 다음과 같은 두 가지 주요 네임스페이스 형식을 제공합니다.

- **도메인 기반 네임 스페이스**: WINDOWS Server AD 도메인의 일부로 호스트되는 네임스페이스입니다. 도메인이 `contoso.com`인 경우 AD의 일부로 호스트되는 네임스페이스에는 도메인의 이름을 포함하는 UNC 경로(예: 도메인이 `\\contoso.com\shares\myshare`)가 있습니다. 도메인 기반 네임스페이스는 AD를 통해 더 큰 규모 제한과 기본 제공 중복성을 지원합니다. 도메인 기반 네임스페이스는 장애 조치(failover) 클러스터에서 클러스터된 리소스가 될 수 없습니다. 
- **독립 실행형 네임 스페이스**: 개별 서버에서 호스트되는 네임스페이스이며 WINDOWS Server AD의 일부로 호스팅되지 않습니다. 독립 실행형 서버 이름이 `MyStandaloneServer`로 지정된 경우 독립 실행형 네임스페이스에는 독립 실행형 서버의 이름(예: `\\MyStandaloneServer\shares\myshare`)을 기반으로 이름이 지정됩니다. 독립 실행형 네임스페이스는 도메인 기반 네임스페이스 보다 낮은 규모의 대상을 지원하지만 장애 조치(failover) 클러스터에서 클러스터된 리소스로 호스팅될 수 있습니다.

## <a name="requirements"></a>요구 사항
Azure Files 및 파일 동기화에서 DFS 네임스페이스를 사용하려면 다음 리소스가 있어야 합니다.

- Active Directory 도메인. 이는 온-프레미스, Azure VM (가상 머신) 또는 다른 클라우드에서 호스트 될 수도 있습니다.
- 네임스페이스를 호스트할 수 있는 Windows Server입니다. DFS 네임스페이스에 대한 일반적인 패턴 배포 패턴은 Active Directory 도메인 컨트롤러를 사용하여 네임스페이스를 호스트 하는 것이지만, DFS 네임스페이스 서버 역할이 설치된 모든 서버에서 네임스페이스를 설정할 수 있습니다. DFS 네임스페이스는 지원되는 모든 Windows Server 버전에서 사용할 수 있습니다.
- 도메인에 가입된 환경에서 호스트되는 SMB 파일 공유입니다. 예를 들어 도메인에 가입된 스토리지 계정 내에서 호스트되는 Azure 파일 공유 또는 Azure 파일 동기화를 사용하여 도메인에 가입된 Windows 파일 서버에서 호스트되는 파일 공유입니다. 스토리지 계정에 대한 도메인 가입에 대한 자세한 내용은 [ID 기반 인증](storage-files-active-directory-overview.md)을 참조하세요. Windows 파일 서버는 Azure 파일 동기화 사용 여부에 관계없이 동일한 방식으로 도메인에 가입됩니다.
- DFS 네임스페이스와 함께 사용하려는 SMB 파일 공유는 온-프레미스 네트워크에서 연결할 수 있어야 합니다. 이는 주로 Azure 파일 공유의 경우에는 중요하지만, Azure 또는 다른 클라우드에서 호스트되는 모든 파일 공유에 기술적으로 적용됩니다. 네트워킹에 대한 자세한 내용은 [직접 액세스를 위한 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조하세요.

## <a name="install-the-dfs-namespaces-server-role"></a>DFS 네임스페이스 서버 역할 설치
DFS 네임스페이스를 이미 사용하고 있거나 도메인 컨트롤러에서 DFS 네임스페이스를 설정하려는 경우 이러한 단계를 안전하게 건너뛸 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
DFS 네임 스페이스 서버 역할을 설치하려면 서버에서 서버 관리자를 엽니다. **관리** 를 선택한 다음, **역할 및 기능 추가** 를 선택합니다. 결과 마법사는 DFS 네임스페이스를 실행하고 관리하는 데 필요한 Windows 구성 요소를 설치하는 과정을 안내합니다. 

설치 마법사의 **설치 유형** 섹션에서 **역할 기반 또는 기능 기반 설치** 라디오 단추를 선택하고 **다음** 을 선택합니다. **서버 선택** 섹션에서 DFS 네임스페이스 서버 역할을 설치할 원하는 서버를 선택하고 **다음** 을 선택합니다. 

**서버 역할** 섹션의 역할 목록에서 **DFS 네임스페이스** 역할을 선택하고 확인합니다. **파일 및 스토리지 서비스** > **파일 및 ISCSI 서비스** 에서 찾을 수 있습니다. DFS 네임스페이스 서버 역할을 선택하는 경우 필수 지원 서버 역할이나 아직 설치하지 않은 기능을 추가할 수도 있습니다.

![**DFS 네임스페이스** 역할이 선택된** 역할 및 기능 추가** 마법사의 스크린샷.](./media/files-manage-namespaces/dfs-namespaces-install.png)

**DFS 네임스페이스** 역할을 확인한 후에는 모든 후속 화면에서 **다음** 을 선택하고 마법사에서 단추를 사용하도록 설정하는 즉시 **설치** 를 선택할 수 있습니다. 설치가 완료되면 네임스페이스를 구성할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
관리자 권한 PowerShell 세션에서(또는 PowerShell 원격을 사용하여) 다음 명령을 실행합니다.

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>루트 통합으로 기존 서버 이름 사용
DFS 네임 스페이스의 중요한 용도는 파일 공유의 물리적 레이아웃을 리팩터링 하기 위해 기존 서버 이름을 가져오는 것입니다. 예를 들어 현대화 마이그레이션을 수행하는 동안 여러 이전 파일 서버에서 파일 공유를 단일 파일 서버에 함께 통합할 수 있습니다. 일반적으로 최종 사용자 숙련도 및 문서 연결은 단일 호스트에서 서로 다른 파일 서버의 파일 공유를 통합하는 기능을 제한하지만, DFS 네임스페이스 루트 통합 기능을 사용하면 단일 서버를 여러 서버 이름으로 분할하고 적절한 공유 이름으로 라우팅할 수 있습니다.

다양한 데이터 센터 마이그레이션 시나리오에 유용하지만, 다음과 같은 이유로 루트 통합은 클라우드 네이티브 Azure 파일 공유를 채택하는 데 특히 유용합니다.

- Azure 파일 공유를 사용하면 기존 온-프레미스 서버 이름을 유지할 수 없습니다.
- Azure 파일 공유는 스토리지 계정의 정규화된 도메인 이름(FQDN)을 통해 액세스해야 합니다. 예를 들어 스토리지 계정 `storageaccount`에서 `share`이라 불리는 Azure 파일 공유는 항상 `\\storageaccount.file.core.windows.net\share` UNC 경로를 통해 액세스됩니다. 이는 짧은 이름을 필요로 하는 최종 사용자에게 혼란스러울 수 있습니다(예: `\\MyServer\share`) 또는 조직의 도메인 이름의 하위 도메인 이름(예: `\\MyServer.contoso.com\share`).

루트 통합은 독립 실행형 네임스페이스에만 사용할 수 있습니다. 파일 공유에 대한 기존 도메인 기반 네임스페이스가 이미 있는 경우 루트 통합 네임스페이스를 만들 필요가 없습니다.

### <a name="enabling-root-consolidation"></a>루트 통합 사용
관리자 권한 PowerShell 세션(또는 PowerShell 원격을 사용)에서 다음 레지스트리 키를 설정하여 루트 통합을 사용하도록 설정할 수 있습니다.

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>기존 파일 서버 이름에 대한 DNS 항목 만들기
DFS 네임스페이스가 기존 파일 서버 이름에 응답하도록 하려면 DFS 네임스페이스 서버 이름을 가리키는 기존 파일 서버에 대한 별칭(CNAME) 레코드를 만듭니다. DNS 레코드를 업데이트하는 정확한 절차는 조직에서 사용 중인 서버 및 조직에서 사용자 지정 도구를 사용하여 DNS 관리를 자동화하는 경우에 따라 달라질 수 있습니다. 다음 단계는 Windows Server에 포함되어 있으며 Windows AD에서 자동으로 사용되는 DNS 서버에 대해 표시됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
Windows DNS 서버에서 DNS 관리 콘솔을 엽니다. 이는 **시작** 단추를 선택하고 **DNS** 를 입력하여 찾을 수 있습니다. 도메인에 대한 전방 조회 영역으로 이동합니다. 예를 들어 도메인이 `contoso.com`인 경우 관리 콘솔의 **전방 조회 영역** >  **`contoso.com`** 에서 전방 조회 영역을 찾을 수 있습니다. 이 대화에 표시되는 정확한 계층 구조는 네트워크에 대한 DNS 구성에 따라 달라집니다.

전방 조회 영역을 마우스 오른쪽 단추로 클릭하고 **새 별칭(CNAME)** 을 선택합니다. 결과 대화에서 바꿀 파일 서버의 짧은 이름을 입력합니다. 정규화된 도메인 이름은 **정규화된 도메인 이름** 이라는 텍스트 상자에 자동으로 채워집니다. **대상 호스트에 대한 FQDN(정규화된 도메인 이름)** 이라는 레이블이 지정된 텍스트 상자에 설정한 DFSN 서버의 이름을 입력합니다. **찾아보기** 단추를 사용하여 원하는 경우 서버를 선택하는데 도움을 줍니다. **확인** 을 선택하여 서버에 대한 CNAME 레코드를 만듭니다.

![CNAME DNS 항목에 대한 **새 리소스 레코드**를 보여주는 스크린샷.](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Windows DNS 서버에서 PowerShell 세션을 열거나 PowerShell 원격을 사용하여 사용자 환경에 대한 관련 값을 사용하여 `$oldServer` 및 `$dfsnServer`를 채운 다음 명령을 실행합니다(`$domain`은 도메인 이름으로 자동 채우기거나 수동으로 입력할 수도 있습니다).

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>네임스페이스 만들기
DFS 네임스페이스에 대한 기본 관리 단위는 네임 스페이스입니다. 네임스페이스 루트 또는 이름은 네임스페이스의 시작 지점입니다. 예를 들어 UNC 경로 `\\contoso.com\Public\`에서 네임 스페이스 루트는 `Public`입니다. 

DFS 네임 스페이스를 사용하여 루트 통합이 있는 기존 서버 이름을 사용하는 경우 네임스페이스 이름 앞에 `#` 문자를 추가할 서버 이름의 이름이어야 합니다. 예를 들어 `MyServer`이라는 기존 서버를 사용하려는 경우 `#MyServer`라는 DFS-N 네임 스페이스를 만듭니다. 아래의 PowerShell 섹션은 `#` 앞에 추가하지만 DFS 관리 콘솔을 통해 만드는 경우에는 필요에 따라 앞에 추가해야 합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
새 네임스페이스를 만들려면 **DFS 관리** 콘솔을 엽니다. 이는 **시작** 단추를 선택하고 **DNS 관리** 를 입력하여 찾을 수 있습니다. 결과 관리 콘솔에는 각각 DFS 네임 스페이스 및 DFS 복제 (DFS-R)를 참조하는 두 개의 섹션 **네임 스페이스** 와 **복제가** 있습니다. Azure 파일 동기화는 복제를 원하는 경우에는 DFS-R 대신 사용할 수 있는 최신 복제 및 동기화 메커니즘을 제공합니다.

**네임스페이스** 섹션을 선택하고 **새 네임스페이스** 단추를 선택합니다(**네임스페이스** 섹션을 마우스 오른쪽 단추로 클릭할 수도 있음). 결과 **새 네임스페이스 마법사** 는 네임스페이스를 만드는 과정을 안내합니다. 

마법사의 첫 번째 섹션에서는 네임스페이스를 호스트할 DFS 네임스페이스 서버를 선택해야 합니다. 여러 서버에서 네임스페이스를 호스트할 수 있지만 한 번에 하나의 서버를 사용하여 DFS 네임스페이스를 설정해야 합니다. 원하는 DFS 네임스페이스 서버의 이름을 입력하고 **다음** 을 선택합니다. **네임스페이스 이름 및 설정** 섹션에서 원하는 네임스페이스 이름을 입력하고 **다음** 을 선택할 수 있습니다. 

**네임스페이스 형식** 섹션에서 **도메인 기반 네임스페이스** 와 **독립 실행형 네임스페이스** 중 하나를 선택할 수 있습니다. DFS 네임스페이스를 사용하여 기존 파일 서버/NAS 디바이스 이름을 유지하려는 경우 독립 실행형 네임스페이스 옵션을 선택해야 합니다. 다른 모든 시나리오의 경우에는 도메인 기반 네임스페이스를 선택해야 합니다. 네임스페이스 유형을 선택하는 방법에 대한 자세한 내용은 위의 [네임스페이스 유형](#namespace-types)을 참조하세요.

![**새 네임 스페이스 마법사**에서 도메인 기반 네임스페이스와 독립 실행형 네임스페이스를 선택하는 스크린샷.](./media/files-manage-namespaces/dfs-namespace-type.png)

환경에 대해 원하는 네임스페이스 유형을 선택하고 **다음** 을 선택합니다. 그러면 마법사가 만들 네임스페이스를 요약합니다. **만들기** 를 선택하여 네임 스페이스를 만들고 대화가 완료되면 **닫습니다**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
DFS 네임 스페이스 서버의 PowerShell 세션에서 다음 PowerShell 명령을 실행하고, `$namespace`, `$type`, 및 `$takeOverName`을 사용자 환경에 대한 관련 값으로 채웁니다.

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>폴더 및 폴더 대상 구성
네임스페이스를 유용하게 만들려면 폴더 및 폴더 대상이 있어야 합니다. 각 폴더에는 해당 콘텐츠를 호스트하는 SMB 파일 공유에 대한 포인터인 하나 이상의 폴더 대상이 있을 수 있습니다. 사용자가 폴더 대상을 사용하여 폴더를 검색하면 클라이언트 컴퓨터에서 폴더 대상 중 하나로 클라이언트 컴퓨터를 투명하게 리디렉션하는 조회를 수신합니다. 폴더 대상이 없는 폴더를 사용하여 네임스페이스에 구조 및 계층을 추가할 수도 있습니다.

DFS 네임스페이스 폴더를 파일 공유와 유사한 것으로 간주할 수 있습니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
DFS 관리 콘솔에서 방금 만든 네임 스페이스를 선택하고 **새 폴더** 를 선택합니다. 결과 **새 폴더** 대화를 사용하여 폴더와 해당 대상을 모두 만들 수 있습니다.

![**새 폴더** 대화의 스크린샷.](./media/files-manage-namespaces/dfs-folder-targets.png)

**이름** 이라는 텍스트 상자는 폴더 이름을 제공합니다. **추가...** 를 선택하여 이 폴더의 폴더 대상을 추가합니다. 결과 **폴더 대상 추가** 대화는 원하는 폴더에 대한 UNC 경로를 제공할 수 있는 **폴더 대상 경로** 라는 텍스트 상자를 제공합니다. **폴더 대상 추가** 대화에서 **확인을** 선택합니다. Azure 파일 공유에 UNC 경로를 추가하는 경우 서버 `storageaccount.file.core.windows.net`에 연결할 수 없다는 메시지가 표시될 수 있습니다. 이는 예상된 것입니다. **예** 를 선택하여 계속합니다. 마지막으로 **새 폴더** 대화에서 **확인** 을 선택하여 폴더 및 폴더 대상을 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

이제 네임스페이스, 폴더 및 폴더 대상을 만들었으므로 DFS 네임스페이스를 통해 파일 공유를 탑재할 수 있습니다. 도메인 기반 네임스페이스를 사용하는 경우 공유에 대한 전체 경로는 `\\<domain-name>\<namespace>\<share>`이어야 합니다. 독립 실행형 네임스페이스를 사용하는 경우 공유에 대한 전체 경로는 `\\<DFS-server>\<namespace>\<share>`이어야 합니다. 루트 통합이 포함된 독립 실행형 네임스페이스를 사용하는 경우 `\\<old-server>\<share>`과 같은 이전 서버 이름을 통해 직접 액세스할 수 있습니다.

## <a name="see-also"></a>추가 정보
- Azure 파일 공유 배포: [Azure Files 배포를 계획](storage-files-planning.md) 및 [파일 공유를 만드는 방법](storage-how-to-create-file-share.md).
- 파일 공유 액세스 구성: [ID 기반 인증](storage-files-active-directory-overview.md) 및 [직접 액세스를 위한 네트워킹 고려 사항](storage-files-networking-overview.md).
- [Windows Server 분산 파일 시스템 네임스페이스](/windows-server/storage/dfs-namespaces/dfs-overview)