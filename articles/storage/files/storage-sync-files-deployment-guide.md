---
title: Azure 파일 동기화 배포 | Microsoft Docs
description: Azure 파일 동기화를 배포하는 방법을 처음부터 끝까지 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f2c4e762ebf10a5ca2120c13a52750a7781d60b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268068"
---
# <a name="deploy-azure-file-sync"></a>Azure 파일 동기화 배포
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에 설명된 단계를 완료하기 전에 [Azure Files 배포에 대한 계획](storage-files-planning.md) 및 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)을 읽어보는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항
* Azure 파일 동기화를 배포하려는 동일한 리전의 Azure 파일 공유입니다. 자세한 내용은 다음을 참조하십시오.
    - [지역 가용성](storage-sync-files-planning.md#azure-file-sync-region-availability)에서 Azure 파일 동기화를 참조하세요.
    - [파일 공유 만들기](storage-how-to-create-file-share.md)에서 파일 공유를 만드는 방법에 대한 단계별 설명을 참조하세요.
* Azure 파일 동기화와 동기화할 Windows Server 또는 Windows Server 클러스터의 지원되는 인스턴스를 하나 이상 포함합니다. 지원되는 버전의 Windows Server에 대한 자세한 내용은 [Windows Server와의 상호 운용성을](storage-sync-files-planning.md#windows-file-server-considerations)참조하십시오.
* Az PowerShell 모듈은 PowerShell 5.1 또는 PowerShell 6+와 함께 사용할 수 있습니다. Windows 가 아닌 시스템을 포함하여 지원되는 모든 시스템에서 Azure File Sync용 Az PowerShell 모듈을 사용할 수 있지만, 서버 등록 cmdlet은 등록중인 Windows Server 인스턴스에서 항상 실행되어야 합니다(직접 또는 PowerShell을 통해 수행할 수 있음). 리모트)를 제공합니다. Windows Server 2012 R2에서 PowerShell 5.1 이상을 실행 중인지 확인할 수 있습니다. \* **$PSVersionTable** 개체의 **PSVersion** 속성 값을 보면 다음과 같은

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Windows Server 2012 R2를 처음 설치하면 대부분이 그렇듯이, PSVersion 값이 5.1.\*보다 낮으면 [WMF(Windows Management Framework) 5.1](https://www.microsoft.com/download/details.aspx?id=54616)을 다운로드한 후 설치하여 쉽게 업그레이드할 수 있습니다. Windows Server 2012 R2용을 다운로드하고 설치하는 적절한 패키지는 **Win8.1AndW2K12R2-KB-x64.msu\*\*\*\*\*\*\*입니다.** 

    PowerShell 6+는 지원되는 모든 시스템에서 사용할 수 있으며 [GitHub 페이지를](https://github.com/PowerShell/PowerShell#get-powershell)통해 다운로드할 수 있습니다. 

    > [!Important]  
    > PowerShell에서 직접 등록하는 대신 서버 등록 UI를 사용하려는 경우 PowerShell 5.1을 사용해야 합니다.

* PowerShell 5.1을 사용하도록 선택한 경우 .NET 4.7.2 이상이 설치되어 있는지 확인합니다. 시스템에 대한 [.NET Framework 버전 및 종속성에](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) 대해 자세히 알아보십시오.

    > [!Important]  
    > Windows Server Core에 .NET 4.7.2+를 설치하는 경우 `quiet` 및 `norestart` 플래그를 설치하거나 설치가 실패합니다. 예를 들어 .NET 4.8을 설치하는 경우 명령은 다음과 같습니다.
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* 다음과 같은 지침에 따라 설치할 수 있는 Az PowerShell 모듈: [Azure PowerShell 을 설치하고 구성합니다.](https://docs.microsoft.com/powershell/azure/install-Az-ps)
     
    > [!Note]  
    > 이제 Az.StorageSync 모듈을 설치하면 Az PowerShell 모듈이 자동으로 설치됩니다.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Azure 파일 동기화에 사용할 Windows Server 준비
장애 조치(failover) 클러스터의 각 서버 노드를 포함하여 Azure 파일 동기화에 사용할 각 서버에 대해 **Internet Explorer 보안 강화 구성**을 사용하지 않도록 설정합니다. 초기 서버 등록에만 필요합니다. 서버가 등록된 후에 사용하도록 다시 설정할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
> [!Note]  
> Windows 서버 코어에 Azure 파일 동기화를 배포하는 경우 이 단계를 건너뛸 수 있습니다.

1. [서버 관리자]를 엽니다.
2. **로컬 서버**를 클릭합니다.  
    ![서버 관리자 UI 왼쪽에 있는 "로컬 서버"](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. **속성** 하위 창에서 **IE 보안 강화 구성** 링크를 선택합니다.  
    ![서버 관리자 UI의 "IE 보안 강화 구성" 창](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. **Internet Explorer 보안 강화 구성** 대화 상자에서 **관리자**와 **사용자**에 대해 **사용 안 함**을 선택합니다.  
    ![“해제"가 선택된 Internet Explorer 보안 강화 구성 팝업 창](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Internet Explorer 보안 강화 구성을 해제하려면 관리자 권한 PowerShell 세션에서 다음 명령을 실행합니다.

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

---

## <a name="deploy-the-storage-sync-service"></a>스토리지 동기화 서비스 배포 
Azure 파일 동기화 배포에서 가장 먼저 할 일은 선택한 그룹의 리소스 그룹에 **스토리지 동기화 서비스** 리소스를 배치하는 것입니다. 되도록이면 최소한으로 프로비전하는 것이 좋습니다. 서버와 이 리소스 사이에 트러스트 관계를 만들 것이므로 서버를 한 스토리지 동기화 서비스에만 등록할 수 있습니다. 결과적으로, 서버 그룹을 분리하는 데 필요한 만큼 스토리지 동기화 서비스를 배포하는 것이 좋습니다. 서로 다른 스토리지 동기화 서비스의 서버를 서로 동기화할 수 없다는 점을 기억해야 합니다.

> [!Note]
> 저장소 동기화 서비스는 배포된 구독 및 리소스 그룹에서 액세스 권한을 상속합니다. 누가 액세스 권한을 갖고 있는지 신중하게 확인하는 것이 좋습니다. 쓰기 액세스 권한이 있는 엔터티는 등록된 서버에서 이 스토리지 동기화 서비스로 새 파일 집합의 동기화를 시작하고, 파일에 액세스할 수 있는 Azure Storage로 데이터가 흐르도록 만들 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
저장소 동기화 서비스를 배포하려면 [Azure 포털로](https://portal.azure.com/)이동하여 *리소스 만들기를 클릭한* 다음 Azure 파일 동기화를 검색합니다. 검색 결과에서 **Azure 파일 동기화**를 선택한 다음 **만들기를** 선택하여 저장소 동기화 배포 탭을 엽니다. **Create**

열리는 창에 다음 정보를 입력합니다.

- **이름**: 스토리지 동기화 서비스의 고유 이름(구독별)입니다.
- **구독**: 스토리지 동기화 서비스를 만들 구독입니다. 조직의 구성 전략에 따라 하나 이상의 구독에 대한 액세스 권한이 있을 수 있습니다. Azure 구독은 각 클라우드 서비스(예: Azure Files)에 대한 비용을 청구하는 가장 기본적인 컨테이너입니다.
- **리소스 그룹**: 리소스 그룹은 스토리지 계정 또는 스토리지 동기화 서비스와 같은 Azure 리소스의 논리적 그룹입니다. 새 리소스 그룹을 만들거나 Azure File Sync에 대한 기존 리소스 그룹을 사용할 수 있습니다. 리소스 그룹을 컨테이너로 사용하여 특정 프로젝트에 대한 HR 리소스 또는 리소스 그룹화와 같이 조직에 대한 리소스를 논리적으로 격리하는 것이 좋습니다.
- **위치**: Azure 파일 동기화를 배포하려는 지역입니다. 지원되는 리전만 이 목록에서 사용할 수 있습니다.

작업이 끝나면 **만들기**를 선택하여 스토리지 동기화 서비스를 배포합니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
`<Az_Region>`을 `<RG_Name>`대체하고 `<my_storage_sync_service>` 고유한 값으로 사용한 다음 다음 명령을 사용하여 저장소 동기화 서비스를 만들고 배포합니다.

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Azure 파일 동기화 에이전트 설치
Azure 파일 동기화 에이전트는 Windows Server가 Azure 파일 공유와 동기화되도록 하는 다운로드 가능 패키지입니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
[Microsoft 다운로드 센터](https://go.microsoft.com/fwlink/?linkid=858257)에서 에이전트를 다운로드할 수 있습니다. 다운로드가 완료되면 MSI 패키지를 두 번 클릭하여 Azure 파일 동기화 에이전트 설치를 시작합니다.

> [!Important]  
> 장애 조치(Failover) 클러스터에서 Azure 파일 동기화를 사용하려는 경우 Azure 파일 동기화 에이전트를 클러스터의 모든 노드에 설치해야 합니다. Azure 파일 동기화를 사용하여 작동하려면 클러스터의 각 노드를 등록해야 합니다.

다음을 수행하는 것이 좋습니다.
- 문제 해결 및 서버 유지 관리를 간소화하려면 기본 설치 경로(C:\Program Files\Azure\StorageSyncAgent)를 유지하세요.
- Azure 파일 동기화를 최신 상태로 유지하도록 Microsoft 업데이트를 사용하도록 설정하세요. 기능 업데이트 및 핫픽스를 비롯한 Azure 파일 동기화 에이전트에 대한 모든 업데이트는 Microsoft 업데이트에서 수행됩니다. Azure 파일 동기화에 최신 업데이트를 설치하는 것이 좋습니다. 자세한 내용은 [Azure 파일 동기화 업데이트 정책을](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)참조하십시오.

Azure 파일 동기화 에이전트 설치를 마치면 서버 등록 UI가 자동으로 열립니다. 등록하려면 스토리지 동기화 서비스가 있어야 합니다. 스토리지 동기화 서비스를 만드는 방법은 다음 섹션을 참조하세요.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
다음 PowerShell 코드를 실행하여 사용하는 OS에 맞는 Azure 파일 동기화 에이전트 버전을 다운로드하고 시스템에 설치합니다.

> [!Important]  
> 장애 조치(Failover) 클러스터에서 Azure 파일 동기화를 사용하려는 경우 Azure 파일 동기화 에이전트를 클러스터의 모든 노드에 설치해야 합니다. Azure 파일 동기화에서 작동하도록 클러스터의 각 노드를 등록해야 합니다.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>스토리지 동기화 서비스에 Windows Server 등록
스토리지 동기화 서비스에 Windows Server를 등록하면 서버(또는 클러스터)와 스토리지 동기화 서비스 간에 트러스트 관계가 설정됩니다. 서버는 한 스토리지 동기화 서비스에만 등록할 수 있으며 동일한 스토리지 동기화 서비스에 연결된 다른 서버 및 Azure 파일 공유와 동기화할 수 있습니다.

> [!Note]
> 서버 등록 시 Azure 자격 증명을 사용하여 스토리지 동기화 서비스와 Windows Server 사이에 트러스트 관계가 생성되지만, 그 이후 서버는 고유의 ID를 만들어서 사용하며 이 ID는 서버의 등록 상태가 유지되고 현재 공유 액세스 서명 토큰(Storage SAS)이 유효한 동안에는 유효성을 유지합니다. 서버가 등록되지 않으면 새 SAS 토큰을 발급할 수 없으며, 따라서 Azure 파일 공유에 액세스하는 서버 기능이 제거되고 모든 동기화가 중지됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure 파일 동기화 에이전트 설치 후 서버 등록 UI가 자동으로 열립니다. 그렇지 않은 경우 파일 위치(C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe)에서 수동으로 열 수 있습니다. 서버 등록 UI가 열리면 **로그인**을 선택하여 시작합니다.

로그인한 후에 다음 정보를 묻는 메시지가 나타납니다.

![서버 등록 UI 스크린샷](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure 구독**: 스토리지 동기화 서비스가 포함된 구독입니다([스토리지 동기화 서비스 배포](#deploy-the-storage-sync-service) 참조). 
- **리소스 그룹**: 스토리지 동기화 서비스를 포함하는 리소스 그룹입니다.
- **스토리지 동기화 서비스**: 등록하려는 스토리지 동기화 서비스의 이름입니다.

적절한 정보를 선택한 후 **등록**을 선택하여 서버 등록을 완료합니다. 등록 프로세스의 일부로 추가 로그인을 요구하는 메시지가 표시됩니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>동기화 그룹 및 클라우드 엔드포인트 만들기
동기화 그룹은 파일 집합에 대한 동기화 토폴로지를 정의합니다. 동기화 그룹 내 엔드포인트는 서로 동기화된 상태를 유지합니다. 동기화 그룹은 Azure 파일 공유를 나타내는 하나의 클라우드 엔드포인트와 하나 이상의 서버 엔드포인트를 포함해야 합니다. 서버 끝점은 등록된 서버의 경로를 나타냅니다. 한 서버가 여러 동기화 그룹에서 서버 엔드포인트를 가질 수 있습니다. 원하는 동기화 토폴로지를 적절하게 설명하는 데 필요한 만큼 동기화 그룹을 만들 수 있습니다.

클라우드 엔드포인트는 Azure 파일 공유에 대한 포인터입니다. 모든 서버 엔드포인트는 클라우드 엔드포인트와 동기화되어 클라우드 엔드포인트를 허브로 만듭니다. Azure 파일 공유에 대한 스토리지 계정은 스토리지 동기화 서비스와 동일한 영역에 있어야 합니다. Azure 파일 공유 전체가 동기화되지만, 한 가지 예외가 있습니다. NTFS 볼륨의 숨겨진 "시스템 볼륨 정보" 폴더와 비슷한 특수 폴더가 프로비전됩니다. 이 디렉터리를 ".SystemShareInformation"이라고 부릅니다. 이 디렉터리에는 다른 엔드포인트와 동기화되지 않는 중요한 동기화 메타데이터가 포함되어 있습니다. 이 디렉터리를 사용하거나 삭제하지 마세요!

> [!Important]  
> 동기화 그룹의 클라우드 엔드포인트 또는 서버 엔드포인트를 변경할 수 있고, 파일이 동기화 그룹의 다른 엔드포인트와 동기화되도록 할 수 있습니다. 클라우드 엔드포인트(Azure 파일 공유)를 직접 변경하는 경우 변경 사항은 먼저 Azure 파일 동기화 변경 내용 검색 작업으로 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩만 시작됩니다. 자세한 내용은 [Azure Files 질문과 대답](storage-files-faq.md#afs-change-detection)을 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)
동기화 그룹을 만들려면 [Azure 포털에서](https://portal.azure.com/)저장소 동기화 서비스로 이동한 다음 **+ 동기화 그룹을**선택합니다.

![Azure Portal에서 새 동기화 그룹 만들기](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

열리는 창에서 다음 정보를 입력하여 클라우드 엔드포인트가 있는 동기화 그룹을 만듭니다.

- **동기화 그룹 이름**: 만들 동기화 그룹의 이름입니다. 이 이름은 스토리지 동기화 서비스 내에서 고유해야 하지만 사용자에게 논리적인 어떤 이름도 될 수 있습니다.
- **구독**: [스토리지 동기화 서비스 배포](#deploy-the-storage-sync-service)에서 스토리지 동기화 서비스를 배포한 구독입니다.
- **스토리지 계정**: **스토리지 계정 선택**을 선택하면 동기화할 Azure 파일 공유가 있는 스토리지 계정을 선택할 수 있는 또 다른 창이 나타납니다.
- **Azure 파일 공유**: 동기화할 Azure 파일 공유의 이름입니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
동기화 그룹을 만들려면 다음 PowerShell을 실행합니다. `<my-sync-group>`을 원하는 동기화 그룹 이름으로 바꿉니다.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

동기화 그룹이 만들어지면 클라우드 엔드포인트를 만들 수 있습니다. `<my-storage-account>` 및 `<my-file-share>`를 예상 값으로 바꿉니다.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>서버 엔드포인트 만들기
서버 엔드포인트는 서버 볼륨의 폴더와 같이 등록된 서버의 특정 위치를 나타냅니다. 서버 엔드포인트는 등록된 서버(탑재된 공유가 아닌)의 경로여야 하며, 클라우드 계층화를 사용하려면 이 경로가 비 시스템 볼륨에 있어야 합니다. NAS(Network Attached Storage)는 지원되지 않습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
서버 끝점을 추가하려면 새로 만든 동기화 그룹으로 이동한 다음 **서버 끝점 추가를**선택합니다.

![동기화 그룹 창에서 새 서버 엔드포인트 추가](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

서버 **끝점 추가** 창에서 다음 정보를 입력하여 서버 끝점을 만듭니다.

- **등록된 서버**: 서버 끝점을 만들 서버 또는 클러스터의 이름입니다.
- **경로**: 동기화 그룹의 일부로 동기화할 Windows Server 경로입니다.
- **클라우드 계층화**: 클라우드 계층화를 사용하거나 사용하지 않도록 설정할 스위치입니다. 클라우드 계층화를 사용하면 드물게 사용하거나 액세스하는 파일은 Azure 파일로 계층화할 수 있습니다.
- **볼륨 여유 공간:** 서버 끝점이 있는 볼륨에 예약할 여유 공간의 양입니다. 예를 들어 단일 서버 엔드포인트가 있는 볼륨에서 사용 가능한 볼륨 공간을 50%로 설정하는 경우 데이터 양의 절반 정도가 Azure Files로 계층화됩니다. 클라우드 계층화를 사용하도록 설정할지 여부에 관계없이, Azure 파일 공유는 항상 동기화 그룹에 데이터의 전체 복사본을 유지합니다.

서버 끝점을 추가하려면 **을 선택합니다.** 이제 Azure 파일 공유 및 Windows Server에서 파일이 동기화 상태로 유지됩니다. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
다음 PowerShell 명령을 실행하여 서버 엔드포인트를 만들고, `<your-server-endpoint-path>` 및 `<your-volume-free-space>`를 원하는 값으로 바꿉니다.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>방화벽 및 가상 네트워크 설정 구성

### <a name="portal"></a>포털
방화벽 및 가상 네트워크 설정에서 작동하도록 Azure File 동기화를 구성하려면 다음을 수행합니다.

1. Azure 포털에서 보안하려는 저장소 계정으로 이동합니다.
1. 왼쪽 메뉴에서 방화벽 및 가상 네트워크 단추를 **선택합니다.**
1. 에서 액세스 **허용 아래에서** **선택한 네트워크를** 선택합니다.
1. 서버 IP 또는 가상 네트워크가 해당 섹션 아래에 나열되어 있는지 확인합니다.
1. 신뢰할 **수 있는 Microsoft 서비스가 이 저장소 계정에 액세스할 수 있도록 허용하는** 것이 선택되어 있는지 확인합니다.
1. 설정을 저장하려면 **저장을** 선택합니다.

![Azure File 동기화로 작동하도록 방화벽 및 가상 네트워크 설정 구성](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Azure 파일 동기화를 사용한 온보딩
전체 파일 충실도와 ACL(액세스 제어 목록)을 유지하면서 가동 중지 시간 없이 Azure 파일 동기화에 처음 온보딩하는 권장 단계는 다음과 같습니다.
 
1. 스토리지 동기화 서비스를 배포합니다.
2. 동기화 그룹을 만듭니다.
3. 전체 데이터 집합이 있는 서버에 Azure 파일 동기화 에이전트를 설치합니다.
4. 해당 서버를 등록하고 공유에 서버 엔드포인트를 만듭니다. 
5. 전체 업로드를 Azure 파일 공유(클라우드 엔드포인트)에 동기화합니다.  
6. 초기 업로드가 완료된 후 나머지 서버에 각각 Azure 파일 동기화 에이전트를 설치합니다.
7. 나머지 서버에 각각 새 파일 공유를 만듭니다.
8. 원하는 경우 클라우드 계층화 정책을 사용하여 새 파일 공유에 서버 엔드포인트를 만듭니다. 이 단계를 수행하려면 초기 설정에 사용할 수 있는 추가 스토리지가 필요합니다.
9. Azure File Sync 에이전트가 실제 데이터 전송 없이 전체 네임스페이스를 신속하게 복원할 수 있도록 합니다. 전체 네임스페이스가 동기화된 후 동기화 엔진은 서버 엔드포인트에 대한 클라우드 계층화 정책에 따라 로컬 디스크 공간을 채웁니다. 
10. 동기화를 완료하고 필요에 따라 토폴로지를 테스트합니다. 
11. 사용자와 애플리케이션을 이 새로운 공유로 리디렉션합니다.
12. 필요에 따라 서버에서 중복 공유를 삭제할 수 있습니다.
 
초기 온보딩을 위한 추가 스토리지가 없으며 기존 공유에 연결하려는 경우 Azure 파일 공유의 데이터를 사전 시드할 수 있습니다. 이 접근 방법은 가동 중지 시간이 허용되고 초기 온보딩 프로세스 중에 서버 공유의 데이터가 변경되지 않는 것이 확실한 경우에만 사용하는 것이 좋습니다. 
 
1. 온보딩 프로세스 중에 서버의 데이터를 변경할 수 없는지 확인합니다.
2. 사전 시드 Azure 파일은 SMB를 통해 데이터 전송 도구를 사용하여 서버 데이터와 공유합니다(예: Robocopy, 직접 SMB 사본). AzCopy는 SMB를 통해 데이터를 업로드하지 않으므로 사전 시드에 사용할 수 없습니다.
3. 원하는 서버 엔드포인트에서 기존 공유를 가리켜 Azure 파일 동기화 토폴로지를 만듭니다.
4. 모든 엔드포인트에서 동기화가 조정 프로세스를 완료하도록 합니다. 
5. 조정이 완료되면 변경 내용을 위해 공유를 열 수 있습니다.
 
현재, 사전 시드 접근 방식에는 몇 가지 제한사항이 있습니다. 
- 파일의 전체 충실도가 유지되지 않습니다. 예를 들어 파일의 ACL과 타임스탬프가 유실됩니다.
- 동기화 토폴로지가 완전히 작동되어 실행하기 전의 서버 데이터 변경으로 인해 서버 엔드포인트에서 충돌이 발생할 수 있습니다.  
- 클라우드 끝점을 만든 후 Azure File Sync는 초기 동기화를 시작하기 전에 클라우드에서 파일을 검색하는 프로세스를 실행합니다. 이 프로세스를 완료하는 데 걸린 시간은 네트워크 속도, 사용 가능한 대역폭, 파일 및 폴더 수와 같은 다양한 요인에 따라 달라집니다. 미리 보기 릴리스의 대략적인 추정에서 검색 프로세스는 약 10개 파일/초의 속도로 실행됩니다. 따라서 클라우드에 데이터를 사전 시드할 경우 사전 시드가 빠르게 실행되더라도 시스템이 정상적으로 실행되기까지 오랜 시간이 걸릴 수 있습니다.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>이전 버전 및 VSS(볼륨 섀도우 복사 서비스)를 통한 셀프 서비스 복원

> [!IMPORTANT]
> 다음 정보는 저장소 동기화 에이전트의 버전 9(또는 그 이상)에서만 사용할 수 있습니다. 9보다 작은 버전에는 StorageSyncSelfService cmdlet이 없습니다.

이전 버전은 볼륨의 서버 측 VSS 스냅숏을 사용하여 SMB 클라이언트에 복원 가능한 버전의 파일을 표시할 수 있는 Windows 기능입니다.
이렇게 하면 일반적으로 셀프 서비스 복원이라고 하는 강력한 시나리오를 IT 관리자의 복원에 따라다볼 수 있는 대신 정보 작업자에게 직접 사용할 수 있습니다.

VSS 스냅숏 및 이전 버전은 Azure 파일 동기화와 독립적으로 작동합니다. 그러나 클라우드 계층화는 호환 되는 모드로 설정 해야 합니다. 많은 Azure 파일 동기화 서버 끝점이 동일한 볼륨에 존재할 수 있습니다. 클라우드 계층을 계획하거나 사용하는 서버 끝점이 하나도 있는 볼륨당 다음 PowerShell 호출을 만들어야 합니다.

```powershell
Import-Module ‘<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll’
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS 스냅샷은 전체 볼륨에서 생성됩니다. 기본적으로 지정된 볼륨에 대해 최대 64개의 스냅숏이 존재할 수 있으며 스냅숏을 저장할 수 있는 충분한 공간이 부여됩니다. VSS는 이 문제를 자동으로 처리합니다. 기본 스냅숏 일정은 월요일부터 금요일까지 하루에 두 개의 스냅숏을 생성합니다. 해당 일정은 Windows 예약 작업을 통해 구성할 수 있습니다. 위의 PowerShell cmdlet은 다음 두 가지 작업을 수행합니다.
1. 지정된 볼륨에서 Azure File Syncs 클라우드 계층을 이전 버전과 호환되도록 구성하고 서버의 클라우드로 계층화된 경우에도 이전 버전에서 파일을 복원할 수 있도록 보장합니다. 
2. 기본 VSS 일정을 활성화합니다. 그런 다음 나중에 수정하도록 결정할 수 있습니다. 

> [!Note]  
> 유의해야 할 두 가지 중요한 사항은 다음과 같습니다.
>- -Force 매개 변수를 사용하고 VSS가 현재 활성화되어 있으면 현재 VSS 스냅숏 일정을 덮어쓰고 기본 일정으로 바꿉니다. cmdlet을 실행하기 전에 사용자 지정 구성을 저장해야 합니다.
> - 클러스터 노드에서 이 cmdlet을 사용하는 경우 클러스터의 다른 모든 노드에서도 이 cmdlet을 실행해야 합니다. 

셀프 서비스 복원 호환성이 활성화되어 있는지 확인하려면 다음 cmdlet을 실행할 수 있습니다.

```powershell
    Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

서버의 모든 볼륨과 각 볼륨에 대한 클라우드 계층화 호환 일수가 나열됩니다. 이 숫자는 볼륨당 가능한 최대 스냅숏과 기본 스냅숏 일정에 따라 자동으로 계산됩니다. 따라서 기본적으로 정보 작업자에게 제공되는 모든 이전 버전을 사용하여 복원할 수 있습니다. 더 많은 스냅샷을 찍기 위해 기본 일정을 변경하는 경우에도 마찬가지입니다.
그러나 호환되는 일 값보다 오래된 볼륨에서 사용 가능한 스냅숏을 만드는 방식으로 일정을 변경하면 사용자는 이 이전 스냅숏(이전 버전)을 사용하여 복원할 수 없습니다.

> [!Note]
> 셀프 서비스 복원을 사용하도록 설정하면 Azure 저장소 사용량 및 청구에 영향을 미칠 수 있습니다. 이 영향은 현재 서버에서 계층화된 파일로 제한됩니다. 이 기능을 사용하면 이전 버전(VSS 스냅숏) 항목을 통해 참조할 수 있는 클라우드에서 사용할 수 있는 파일 버전이 있는지 확인합니다.
>
> 이 기능을 사용하지 않도록 설정하면 호환되는 일 기간이 경과할 때까지 Azure 저장소 사용량이 서서히 감소합니다. 속도를 높일 수 있는 방법은 없습니다. 

볼륨당 VSS 스냅숏의 기본 최대 수(64)와 이를 수취하는 기본 일정은 볼륨에 저장할 수 있는 VSS 스냅숏 수에 따라 정보 작업자가 복원할 수 있는 이전 버전의 최대 45일이 생성됩니다.

경우 최대입니다. 볼륨당 64 VSS 스냅숏이 올바른 설정이 아니므로 [레지스트리 키를 통해 해당 값을 변경할](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies)수 있습니다.
새 제한이 적용되면 cmdlet을 다시 실행하여 이전에 활성화된 모든 볼륨에서 이전 버전 호환성을 활성화해야 하며 -Force 플래그를 사용하여 볼륨당 VSS 스냅숏의 새 최대 수를 고려해야 합니다. 이렇게 하면 새로 계산된 호환 일 수가 생성됩니다. 이 변경 사항은 새로 계층화된 파일에만 적용되며 사용자가 만든 VSS 일정에 대한 사용자 지정을 덮어씁니다.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>DFS 복제(DFS-R) 배포를 Azure 파일 동기화로 마이그레이션
DFS-R 배포를 Azure 파일 동기화로 마이그레이션하려면

1. 바꾸려는 DFS-R 토폴로지를 나타내는 동기화 그룹을 만듭니다.
2. 마이그레이션할 DFS-R 토폴로지의 전체 데이터 집합이 있는 서버에서 시작합니다. 해당 서버에 Azure 파일 동기화를 설치합니다.
3. 해당 서버를 등록하고 마이그레이션할 첫 번째 서버에 대한 서버 엔드포인트를 만듭니다. 클라우드 계층화를 활성화하지 마세요.
4. Azure 파일 공유(클라우드 엔드포인트)에 대한 모든 데이터 동기화를 수행합니다.
5. 나머지 DFS-R 서버에 각각 Azure 파일 동기화 에이전트를 설치하고 등록합니다.
6. DFS-R을 사용하지 않도록 설정합니다. 
7. 각 DFS-R 서버에 서버 엔드포인트를 만듭니다. 클라우드 계층화를 활성화하지 마세요.
8. 동기화를 완료하고 필요에 따라 토폴로지를 테스트합니다.
9. DFS-R을 사용 중지합니다.
10. 이제 클라우드 계층화를 원하는 모든 서버 엔드포인트에서 사용할 수 있습니다.

자세한 내용은 [분산 파일 시스템(DFS)과의 Azure 파일 동기화 interop](storage-sync-files-planning.md#distributed-file-system-dfs)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 서버 엔드포인트 추가 또는 제거](storage-sync-files-server-endpoint.md)
- [Azure 파일 동기화로 서버 등록 또는 등록 취소](storage-sync-files-server-registration.md)
- [Azure 파일 동기화 모니터링](storage-sync-files-monitoring.md)
