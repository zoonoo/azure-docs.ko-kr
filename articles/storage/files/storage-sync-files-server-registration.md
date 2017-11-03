---
title: "Azure 파일 동기화(미리 보기)에서 서버 등록/등록 취소 | Microsoft Docs"
description: "Azure 파일 동기화 저장소 동기화 서비스에서 Windows Server를 등록 및 등록 취소하는 방법을 알아봅니다."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 13a75d5cafd94435346660614721399f2d77919b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2017
---
# <a name="registerunregister-a-server-with-azure-file-sync-preview"></a>Azure 파일 동기화(미리 보기)에서 서버 등록/등록 취소
Azure File Sync(미리 보기)를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure 파일에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. 이 작업은 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환하여 수행합니다. Windows Server에서 사용할 수 있는 아무 프로토콜이나 사용하여 데이터를 로컬로(SMB, NFS 및 FTPS 포함) 액세스할 수 있으며 세계 전역에 걸쳐 필요한 만큼 캐시를 보유할 수 있습니다.

다음 문서에서는 저장소 동기화 서비스에서 서버를 등록 및 등록 취소하는 방법을 보여 줍니다. 서버가 서비스 해제되거나 동기화 그룹에 새 서버 끝점이 필요한 경우 이 기능이 유용할 수 있습니다. 종단 간 Azure 파일 동기화를 배포하는 방법에 대한 자세한 내용은 [Azure 파일 동기화(미리 보기)를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
저장소 동기화 서비스에 Windows Server를 등록하려면 먼저 필요한 필수 구성 요소를 갖춘 Windows Server를 준비해야 합니다.

* 저장소 동기화 서비스가 배포되었는지 확인합니다. 저장소 동기화 서비스를 배포하는 방법에 대한 자세한 내용은 [Azure 파일 동기화(미리 보기)를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.
* 서버가 인터넷에 연결되어 있고 Azure가 액세스 가능한지 확인합니다.
* 서버 관리자 UI를 사용하여 관리자에 대한 IE 고급 보안 구성을 사용하지 않도록 설정합니다.
    
    ![IE 고급 보안 구성이 강조 표시된 서버 관리자 UI](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* AzureRM PowerShell 모듈이 서버에 설치되어 있는지 확인합니다. 서버가 장애 조치(failover) 클러스터의 멤버인 경우 클러스터의 모든 노드에 AzureRM 모듈이 필요합니다. AzureRM 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)을 참조하세요.

    > [!Note]  
    > 최신 버전의 AzureRM PowerShell 모듈을 사용하여 서버를 등록/등록 취소하는 것이 좋습니다. AzureRM 패키지가 이전에 이 서버에 설치되었고 이 서버의 PowerShell 버전이 5.* 이상인 경우 `Update-Module` cmdlet을 사용하여 이 패키지를 업데이트할 수 있습니다. 

## <a name="register-a-server-with-storage-sync-service"></a>저장소 동기화 서비스에 서버 등록
Azure 파일 동기화의 *동기화 그룹*에서 Windows Server를 *서버 끝점*으로 사용하려면 먼저 해당 서버를 *저장소 동기화 서비스*에 등록해야 합니다. 서버는 한 번에 하나의 *저장소 동기화 서비스*에만 등록할 수 있습니다.

### <a name="install-the-azure-file-sync-agent"></a>Azure 파일 동기화 에이전트 설치
1. [Azure 파일 동기화 에이전트를 다운로드](https://go.microsoft.com/fwlink/?linkid=858257)합니다.
2. Azure 파일 동기화 에이전트 설치 관리자를 시작합니다.
    
    ![Azure 파일 동기화 에이전트 설치 관리자의 첫 번째 창](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Microsoft 업데이트를 사용하여 Azure 파일 동기화 에이전트에 대한 업데이트를 사용하도록 설정해야 합니다. 서버 패키지에 대한 중요한 보안 수정과 향상된 기능은 Microsoft 업데이트를 통해 제공됩니다.

    ![Azure 파일 동기화 에이전트 설치 관리자의 [Microsoft 업데이트] 창에서 Microsoft 업데이트가 사용하도록 설정되어 있는지 확인합니다.](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. 이전에 서버가 등록되지 않은 경우에는 설치를 완료한 후 바로 서버 등록 UI가 표시됩니다.

> [!Important]  
> 서버가 장애 조치(failover) 클러스터의 멤버인 경우 Azure 파일 동기화 에이전트를 클러스터의 모든 노드에 설치해야 합니다.

### <a name="register-the-server-using-the-server-registration-ui"></a>서버 등록 UI를 사용하여 서버 등록

> [!Important]  
> 클라우드 솔루션 공급자 구독은 서버 등록 UI를 사용할 수 없습니다. 대신 PowerShell을 사용합니다(이 섹션 아래).

1. Azure File Sync 에이전트 설치를 완료한 후 바로 서버 등록 UI가 시작되지 않은 경우에는 `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`를 실행하여 수동으로 해당 UI를 시작할 수 있습니다.
2. *로그인*을 클릭하여 Azure 구독에 액세스합니다. 

    ![서버 등록 UI 대화 상자 열기](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. 대화 상자에서 올바른 구독, 리소스 그룹 및 저장소 동기화 서비스를 선택합니다.

    ![저장소 동기화 서비스 정보](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. 미리 보기에서는 프로세스를 완료하려면 한 번 더 로그인해야 합니다. 

    ![로그인 대화 상자](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> 서버가 장애 조치(failover) 클러스터의 멤버인 경우 각 서버는 서버 등록을 실행해야 합니다. Azure Portal에서 등록된 서버로 표시되면 Azure 파일 동기화는 자동으로 각 노드를 동일한 장애 조치(failover) 클러스터의 멤버로 인식하고 적절하게 그룹화합니다.

### <a name="register-the-server-with-powershell"></a>PowerShell로 서버 등록
PowerShell을 통해 서버 등록을 수행할 수도 있습니다. 클라우드 솔루션 공급자 구독에서 지원되는 유일한 서버 등록 방법입니다.

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

## <a name="unregister-the-server-with-storage-sync-service"></a>저장소 동기화 서비스에서 서버 등록 취소
저장소 동기화 서비스에서 서버 등록을 취소하려면 여러 단계를 수행해야 합니다. 서버를 제대로 등록 취소하는 방법을 살펴보겠습니다.

### <a name="optional-recall-all-tiered-data"></a>(선택 사항) 모든 계층화된 데이터 기억
서버 끝점에 대해 사용하도록 설정된 경우 클라우드 계층화 기능이 파일을 Azure 파일 공유로 *계층화*합니다. 이렇게 하면 온-프레미스 파일 공유가 데이터 집합의 전체 복사본이 아닌 캐시로 사용될 수 있으므로 파일 서버의 공간이 효율적으로 사용됩니다. 그러나 계층화된 파일이 아직도 서버에서 로컬로 존재하는 서버 끝점이 제거되면 해당 파일에 액세스할 수 없습니다. 따라서 파일에 계속 액세스해야 하는 경우 등록 취소를 계속하기 전에 Azure Files에서 모든 계층화된 파일을 기억해야 합니다. 

이 작업에는 다음과 같이 PowerShell cmdlet을 사용할 수 있습니다.

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> 서버를 호스트하는 로컬 볼륨에 모든 계층화된 데이터를 기억할 충분한 사용 가능한 공간이 없으면 `Invoke-StorageSyncFileRecall` cmdlet이 실패합니다.  

### <a name="remove-the-server-from-all-sync-groups"></a>모든 동기화 그룹에서 서버 제거
저장소 동기화 서비스에서 서버의 등록을 취소하기 전에 해당 서버에 대한 모든 서버 끝점을 제거해야 합니다. 이 작업은 Portal을 통해 수행할 수 있습니다.

1. 서버가 등록되어 있는 저장소 동기화 서비스로 이동합니다.
2. 저장소 동기화 서비스의 각 동기화 그룹에서 이 서버에 대한 모든 서버 끝점을 제거합니다. 이 작업을 수행하려면 [동기화 그룹] 창에서 관련 서버 끝점을 마우스 오른쪽 단추로 클릭합니다.

    ![동기화 그룹에서 서버 끝점 제거](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

간단한 PowerShell 스크립트를 사용하여 이 작업을 수행할 수도 있습니다.

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

### <a name="unregister-the-server"></a>서버 등록 취소
이제 모든 데이터가 기억되었고 서버가 모든 동기화 그룹에서 제거되었으므로 서버 등록을 취소할 수 있습니다. 

1. Azure Portal에서 저장소 동기화 서비스의 “등록된 서버” 섹션을 탐색합니다.
2. 등록 취소할 서버를 마우스 오른쪽 단추로 클릭하고 “서버 등록 취소”를 클릭합니다.

    ![서버 등록 취소](media/storage-sync-files-server-registration/unregister-server-1.png)